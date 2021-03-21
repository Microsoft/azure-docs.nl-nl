---
title: Door klant beheerde sleutels of BYOK REST API gebruiken
description: In deze zelfstudie gebruikt u door klant beheerde sleutels of neemt u uw eigen sleutel (BYOK) mee met een Azure Media Services-opslagaccount.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c8a5b682e2ac4879d2181bdb069cf554bad512d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498282"
---
# <a name="tutorial-use-customer-managed-keys-or-byok-with-media-services-rest-api"></a>Zelfstudie: Door klant beheerde sleutels of BYOK gebruiken met Media Services REST API

Met de 2020-05-01 API kun u een door de klant beheerde RSA-sleutel gebruiken met een Azure Media Services-account dat een met het systeem beheerde identiteit heeft. Deze zelfstudie omvat een Postman-verzameling en -omgeving voor het verzenden van REST-aanvragen naar Azure-services. De gebruikte services zijn:

- Azure Active Directory (Azure AD)-toepassingsregistratie voor Postman
- Microsoft Graph API
- Azure Storage
- Azure Key Vault
- Azure Media Services

In deze zelfstudie leert u Postman te gebruiken voor het volgende:

> [!div class="checklist"]
> - Tokens ophalen voor gebruik met Azure-services.
> - Een resourcegroep en een opslagaccount maken.
> - Een Media Services-account maken met een in het systeem beheerde identiteit.
> - Een sleutelkluis maken voor het opslaan van een door de klant beheerde RSA-sleutel.
> - Het Media Services-account bijwerken om de RSA-sleutel te gebruiken met het opslagaccount.
> - Variabelen gebruiken in Postman.

Als u nog geen Azure-abonnement hebt, [maakt u een gratis proefaccount](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

1. Registreer een service-principal met de juiste machtigingen.
1. Installeer [Postman](https://www.postman.com).
1. Download de Postman-verzameling voor deze zelfstudie op [Azure-voorbeelden: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Registreer een service-principal met de benodigde machtigingen

1. [Een service-principal maken](../../active-directory/develop/howto-create-service-principal-portal.md).
1. Ga naar [Optie 2: Een nieuw toepassingsgeheim maken](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) om het geheim van de service-principal te verkrijgen.

   > [!IMPORTANT]
   >Kopieer het geheim en sla het op voor later gebruik. U hebt geen toegang tot het geheim nadat u de geheime pagina in de portal verlaat.

1. Wijs machtigingen toe aan de service-principal, zoals wordt weergegeven in de volgende schermopname:

   :::image type="complex" source="./media/tutorial-byok/service-principal-permissions-1.png" alt-text="Schermopname met de machtigingen die nodig zijn voor de service-principal.":::
   De machtigingen worden vermeld per service, machtigingsnaam, type en vervolgens beschrijving. Azure Key Vault: gebruikersimitatie, gedelegeerde en volledige toegang tot Azure Key Vault. Azure Service Management: gebruikersimitatie, gedelegeerd, toegang tot Azure Service Management als organisatiegebruiker. Azure Storage: gebruikersimitatie, gedelegeerd toegang Azure Storage. Media-services: gebruikersimitatie, gedelegeerd, toegang tot media-services. Microsoft Graph: user.read, gedelegeerd, aanmelden en lezen gebruikersprofiel.
   :::image-end:::

### <a name="install-postman"></a>Postman installeren

Als u Postman nog niet hebt geïnstalleerd voor gebruik met Azure, kunt u het downloaden op [postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>De verzameling downloaden en importeren

Download de Postman-verzameling voor deze zelfstudie op [Azure-voorbeelden: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

## <a name="install-the-postman-collection-and-environment"></a>Installeer de Postman-verzameling en -omgeving

1. Voer Postman uit.
1. Selecteer **Importeren**.
1. Selecteer **Bestanden uploaden**.
1. Ga naar de locatie waar u de verzameling- en omgevingsbestanden hebt opgeslagen.
1. Selecteer de verzameling- en omgevingsbestanden.
1. Selecteer **Openen**. Er wordt een waarschuwing weergegeven met de melding dat de bestanden niet worden geïmporteerd als een API, maar als verzamelingen. Deze waarschuwing is in orde. Dit is wat u wilt.

De verzameling wordt nu als BYOK in uw verzamelingen weergegeven. Ook worden de omgevingsvariabelen weergegeven in uw omgevingen.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>De REST API-aanvragen in de verzameling begrijpen

De verzameling biedt de volgende REST API-aanvragen.

> [!NOTE]
>
>- De aanvragen moeten in de aangegeven volgorde worden verzonden.
>- De meeste aanvragen hebben testscripts waarmee dynamisch globale variabelen worden gemaakt voor de volgende aanvraag in de reeks.
>- U hoeft niet handmatig globale variabelen te maken.

In Postman worden deze variabelen weergegeven tussen haken. Bijvoorbeeld `{{bearerToken}}`.

1. Een Azure AD-token verkrijgen: De test stelt de globale variabele **bearerToken** in.
2. Een Microsoft Graph-token verkrijgen: De test stelt de globale variabele **graphToken** in.
3. Details van service-principal verkrijgen: De test stelt de globale variabele **servicePrincipalObjectId** in.
4. Een opslagaccount maken: De test stelt de globale variabele **storageAccountId** in.
5. Een Media Services-account maken met een in het systeem beheerde identiteit: De test stelt de globale variabele **principalId** in.
6. Een sleutelkluis maken om toegang te verlenen aan de service-principal: De test stelt de globale variabele **keyVaultId** in.
7. Een Key Vault-token verkrijgen: De test stelt de globale variabele **keyVaultToken** in.
8. Maak de RSA-sleutel in de sleutelkluis: De test stelt de globale variabele **keyId** in.
9. Het Media Services-account bijwerken om de sleutel te gebruiken met het opslagaccount: Er is geen testscript voor deze aanvraag.

## <a name="define-environment-variables"></a>Omgevingsvariabelen definiëren

1. Selecteer de vervolgkeuzelijst van de omgeving om over te schakelen naar de omgeving die u hebt gedownload.
1. Stel de omgevingsvariabelen in Postman in. Ze worden ook gebruikt als variabelen tussen haken. Bijvoorbeeld `{{tenantId}}`.

    - **tenantId**: Uw tenant-id.
    - **servicePrincipalId**: De id van de service-principal die u hebt ingesteld met uw favoriete methode, zoals portal of CLI.
    - **servicePrincipalSecret**: Het geheim dat is gemaakt voor de service-principal.
    - **subscription**: Uw abonnement-id.
    - **storageName**: De naam die u uw opslag wilt geven.
    - **accountName**: De Media Services-accountnaam die u wilt gebruiken.
    - **keyVaultName**: De sleutelkluisnaam die u wilt gebruiken.
    - **resourceLocation**: De locatie **CentralUs** of waar u uw resources wilt plaatsen. Deze verzameling is alleen getest met **CentralUs**.
    - **resourceGroup**: De naam van de resourcegroep.

    De volgende variabelen zijn standaard voor het werken met Azure-resources. U hoeft deze dus niet te wijzigen.

    - **armResource**: `https://management.core.windows.net`
    - **graphResource**: `https://graph.windows.net/`
    - **keyVaultResource**: `https://vault.azure.net`
    - **armEndpoint**: `management.azure.com`
    - **graphEndpoint**: `graph.windows.net`
    - **aadEndpoint**: `login.microsoftonline.com`
    - **keyVaultDomainSuffix**: `vault.azure.net`

## <a name="send-the-requests"></a>De aanvragen verzenden

Nadat u de omgevingsvariabelen hebt gedefinieerd, kunt u de aanvragen per keer in de vorige volgorde uitvoeren. U kunt ook de runner van Postman gebruiken om de verzameling uit te voeren.

## <a name="change-the-key"></a>De sleutel wijzigen

Media Services detecteert automatisch wanneer de sleutel wordt gewijzigd. Maak nog een sleutelversie voor dezelfde sleutel om dit proces te testen. De sleutel moet binnen 15 minuten worden gedetecteerd in Media Services.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u hebt gemaakt, verder niet meer gaat gebruiken, en *u niet meer gefactureerd wilt worden voor deze resources*, verwijdert u ze.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over:
> [!div class="nextstepaction"]
> [Extern bestand coderen op basis van URL, en de video streamen met REST](stream-files-tutorial-with-rest.md)