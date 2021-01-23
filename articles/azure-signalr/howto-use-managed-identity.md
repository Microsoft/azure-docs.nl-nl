---
title: Beheerde identiteiten in de Azure signalerings service
description: Meer informatie over hoe beheerde identiteiten werken in de Azure signalerings service en hoe u een beheerde identiteit gebruikt in serverloze scenario's.
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: dee15977318eda7bcd0b1950286bb33f621221dd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731581"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Beheerde identiteiten voor de Azure signalerings service

In dit artikel wordt beschreven hoe u een beheerde identiteit voor de Azure signalerings service maakt en hoe u deze kunt gebruiken in serverloze scenario's.

> [!Important] 
> De Azure signalerings service kan slechts één beheerde identiteit ondersteunen. Dit betekent dat u een door het systeem toegewezen identiteit of een door de gebruiker toegewezen identiteit kunt toevoegen. 

## <a name="add-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen

Als u een beheerde identiteit wilt instellen in de Azure Portal, maakt u eerst een service-exemplaar van Azure signalering en schakelt u de functie in.

1. Maak een exemplaar van de Azure Signalr-service in de portal zoals u dat gewend bent. Blader ernaar in de portal.

2. Selecteer **identiteit**.

4. Schakel op het tabblad **systeem toegewezen** de optie **status** in **op aan**. Selecteer **Opslaan**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="Een door het systeem toegewezen identiteit toevoegen aan de portal":::

## <a name="add-a-user-assigned-identity"></a>Een door de gebruiker toegewezen identiteit toevoegen

Voor het maken van een exemplaar van de Azure signalerings service met een door de gebruiker toegewezen identiteit moet u de identiteit maken en vervolgens de resource-id toevoegen aan uw service.

1. Maak een door de gebruiker toegewezen beheerde identiteits bron op basis van [deze instructies](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Maak een exemplaar van de Azure Signalr-service in de portal zoals u dat gewend bent. Blader ernaar in de portal.

3. Selecteer **identiteit**.

4. Selecteer **toevoegen** op het tabblad door de **gebruiker toegewezen** .

5. Zoek de identiteit die u eerder hebt gemaakt en selecteert deze. Selecteer **Toevoegen**.

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="Een door de gebruiker toegewezen identiteit toevoegen aan de portal":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>Een beheerde identiteit gebruiken in serverloze scenario's

De Azure signalerings service is een volledig beheerde service. u kunt dus geen beheerde identiteit gebruiken om tokens hand matig op te halen. In plaats daarvan gebruikt de Azure signalerings service de beheerde identiteit die u hebt ingesteld om een toegangs token op te halen. De service stelt vervolgens het toegangs token in op een `Authorization` header in een upstream-aanvraag in serverloze scenario's.

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>Beheerde identiteits verificatie inschakelen in de upstream-instellingen

1. Een door het systeem toegewezen identiteit of door de gebruiker toegewezen identiteit toevoegen.

2. Voeg één upstream-instelling toe en klik op een wille keurige asterisk om naar een gedetailleerde pagina te gaan, zoals hieronder wordt weer gegeven.
    :::image type="content" source="media/signalr-howto-use-managed-identity/pre-msi-settings.png" alt-text="pre-MSI-instelling":::
    
    :::image type="content" source="media/signalr-howto-use-managed-identity/msi-settings.png" alt-text="MSI-instelling":::

3. In de instellingen voor beheerde identiteits verificatie voor **resource** kunt u de doel resource opgeven. De resource wordt een `aud` claim in het verkregen toegangs token, dat kan worden gebruikt als onderdeel van validatie in de upstream-eind punten. De bron kan een van de volgende zijn:
    - Leeg
    - Toepassings-ID (client) van de Service-Principal
    - De URI van de toepassings-ID van de Service-Principal
    - [Resource-ID van een Azure-service](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > Als u een toegangs token door uzelf in uw service valideert, kunt u een van de resource-indelingen kiezen. Zorg ervoor dat de **bron** waarde in **verificatie** -instellingen en de validatie consistent zijn. Als u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruikt voor een gegevenslaag, moet u de resource gebruiken die door de service provider wordt aangevraagd.

### <a name="validate-access-tokens"></a>Toegangs tokens valideren

Het token in de `Authorization` header is een [toegangs token van micro soft Identity platform](../active-directory/develop/access-tokens.md#validating-tokens).

Als u toegangs tokens wilt valideren, moet uw app ook de doel groep en de handtekening tokens valideren. Deze moeten worden gevalideerd aan de hand van de waarden in het detectie document voor OpenID Connect. Zie bijvoorbeeld de [Tenant-onafhankelijke versie van het document](https://login.microsoftonline.com/common/.well-known/openid-configuration).

De Azure Active Directory (Azure AD) middleware heeft ingebouwde mogelijkheden voor het valideren van toegangs tokens. U kunt door onze voor [beelden](../active-directory/develop/sample-v2-code.md) bladeren om er een te vinden in de taal van uw keuze.

We bieden bibliotheken en code voorbeelden die laten zien hoe u de token validatie kunt afhandelen. Er zijn ook verschillende open source-partner bibliotheken beschikbaar voor de validatie van de JSON Web Token (JWT). Er is ten minste één optie voor bijna elk platform en de taal. Zie [verificatie bibliotheken voor micro soft-identiteits platform](../active-directory/develop/reference-v2-libraries.md)voor meer informatie over Azure AD-verificatie bibliotheken en code voorbeelden.

#### <a name="authentication-in-function-app"></a>Verificatie in functie-app

Het instellen van de validatie van toegangs tokens in functie-app is eenvoudig en efficiënt zonder code werkt.

1. Schakel op de pagina **verificatie/autorisatie** het **app service verificatie** in **op** aan.

2. Selecteer **Aanmelden met Azure Active Directory** in **actie die moet worden uitgevoerd wanneer de aanvraag niet is geverifieerd**.

3. Klik in de verificatie provider in **Azure Active Directory**

4. Op de pagina Nieuw. Selecteer **Express** en **Maak nieuwe AD-App** en klik vervolgens op **OK** :::image type="content" source="media/signalr-howto-use-managed-identity/function-aad.png" alt-text="functie Aad":::

5. Navigeer naar de signalerings service en volg de [stappen](howto-use-managed-identity.md#add-a-system-assigned-identity) om een door het systeem toegewezen identiteit toe te voegen of door de gebruiker toegewezen identiteit.

6. Ga in de seingevings service naar **upstream-instellingen** en kies **beheerde identiteit gebruiken** en **Selecteer bestaande toepassingen**. Selecteer de toepassing die u eerder hebt gemaakt.

Na deze instellingen worden aanvragen zonder toegangs token in de header door de functie-app geweigerd.

## <a name="use-a-managed-identity-for-key-vault-reference"></a>Een beheerde identiteit gebruiken voor Key Vault referentie

De signaal service heeft toegang tot Key Vault om geheim te krijgen met behulp van de beheerde identiteit.

1. Voeg een door het systeem toegewezen identiteit of door de gebruiker toegewezen identiteit voor de Azure signalerings service toe.

2. Verleen geheim Lees machtiging voor de beheerde identiteit in het toegangs beleid in de Key Vault. Zie [toegangs beleid voor Key Vault toewijzen met behulp van de Azure Portal](../key-vault/general/assign-access-policy-portal.md)

Deze functie kan momenteel worden gebruikt in de volgende scenario's:

- [Referentie geheim in upstream-URL-patroon](./concept-upstream.md#key-vault-secret-reference-in-url-template-settings)


## <a name="next-steps"></a>Volgende stappen

- [Ontwikkeling en configuratie van Azure Functions met Azure SignalR Service](signalr-concept-serverless-development-config.md)