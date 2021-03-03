---
title: Een SCIM-eind punt bouwen om gebruikers in te richten op apps van Azure Active Directory
description: Leer hoe u een SCIM-eind punt ontwikkelt, uw SCIM-API integreert met Azure AD en automatisch gebruikers en groepen inricht in uw Cloud toepassingen met Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: b69e701177c6f017388521ed05c37de1271c7e60
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650155"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Zelf studie: een voor beeld van een SCIM-eind punt ontwikkelen

Niemand wil een volledig nieuw eind punt bouwen, dus hebben we een [verwijzings code](https://aka.ms/scimreferencecode) gemaakt om aan de slag te gaan met het [systeem voor Identity Management (scim) tussen domeinen](https://aka.ms/scimoverview). U kunt uw SCIM-eind punt in slechts vijf minuten actief maken zonder code.

In deze zelf studie wordt beschreven hoe u de SCIM-referentie code implementeert in Azure en deze test met behulp van Postman of door te integreren met de Azure Active Directory (Azure AD) SCIM-client. Deze zelf studie is bedoeld voor ontwikkel aars die aan de slag willen gaan met SCIM, of iemand die geïnteresseerd is in het testen van een SCIM-eind punt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Implementeer uw SCIM-eind punt in Azure.
> * Test uw SCIM-eind punt.

## <a name="deploy-your-scim-endpoint-in-azure"></a>Uw SCIM-eind punt implementeren in azure

Met de stappen implementeert u het SCIM-eind punt voor een service met behulp van [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) en [Azure app service](../../app-service/index.yml). De SCIM-referentie code kan ook lokaal worden uitgevoerd, worden gehost door een on-premises server of worden geïmplementeerd in een andere externe service.

1. Ga naar de [referentie code](https://github.com/AzureAD/SCIMReferenceCode) van github en selecteer **klonen of downloaden**.

1. Selecteer **openen in bureau blad** of kopieer de koppeling, open Visual Studio en selecteer **klonen of uitchecken code** om de gekopieerde koppeling in te voeren en een lokale kopie te maken.

1. Zorg er in Visual Studio voor dat u zich aanmeldt bij het account dat toegang heeft tot uw hosting-resources.

1. Open in Solution Explorer *micro soft. scim. SLN* en klik met de rechter muisknop op het bestand *micro soft. scim. WebHostSample* . Selecteer **Publiceren**.

    ![Scherm opname waarin het voorbeeld bestand wordt weer gegeven.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Als u deze oplossing lokaal wilt uitvoeren, dubbelklikt u op het project en selecteert u **IIS Express** om het project te openen als een webpagina met een lokale host-URL.

1. Selecteer **profiel maken** en zorg ervoor dat **app service** en **nieuwe maken** zijn geselecteerd.

    ![Scherm opname van het venster publiceren.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Door loop de opties van het dialoog venster en wijzig de naam van de app in een door u gemaakte keuze. Deze naam wordt gebruikt in zowel de app als de SCIM-eind punt-URL.

    ![Scherm opname van het maken van een nieuwe app service.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Selecteer de resource groep die u wilt gebruiken en selecteer **publiceren**.

1. Ga naar de toepassing in **Azure app service**  >  **configuratie** en selecteer **nieuwe toepassings instelling** om de instelling *Token__TokenIssuer* met de waarde toe te voegen `https://sts.windows.net/<tenant_id>/` . Vervang door `<tenant_id>` de id van uw Azure AD-Tenant. Als u het SCIM-eind punt wilt testen met behulp van [postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint), voegt u een *ASPNETCORE_ENVIRONMENT* -instelling toe met de waarde `Development` .

   ![Scherm opname van het venster toepassings instellingen.](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Wanneer u uw eind punt met een bedrijfs toepassing in de [Azure Portal](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client)test, hebt u twee opties. U kunt de omgeving in houden `Development` en het test token van het `/scim/token` eind punt opgeven, of u kunt de omgeving wijzigen in `Production` en het veld token leeg laten.

Dat is alles. Uw SCIM-eind punt wordt nu gepubliceerd en u kunt de Azure App Service URL gebruiken om het SCIM-eind punt te testen.

## <a name="test-your-scim-endpoint"></a>Uw SCIM-eind punt testen

Voor aanvragen voor een SCIM-eind punt is autorisatie vereist. De SCIM-standaard heeft meerdere opties voor verificatie en autorisatie, waaronder cookies, basis verificatie, TLS-client verificatie of een van de methoden die worden vermeld in [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Vermijd methoden die niet beveiligd zijn, zoals gebruikers naam en wacht woord, voor een veiligere methode zoals OAuth. Azure AD biedt ondersteuning voor langlopende Bearer-tokens (voor galerie-en niet-galerie toepassingen) en de OAuth-autorisatie toekenning (voor galerie toepassingen).

> [!NOTE]
> De autorisatie methoden in de opslag plaats zijn alleen bedoeld voor test doeleinden. Wanneer u integreert met Azure AD, kunt u de autorisatie richtlijnen bekijken. Zie [inrichting plannen voor een scim-eind punt](use-scim-to-provision-users-and-groups.md).

De ontwikkel omgeving maakt functies mogelijk die onveilig zijn voor productie, zoals referentie code om het gedrag van de validatie van beveiligings tokens te bepalen. De token validatie code gebruikt een zelfondertekend beveiligings token en de handtekening sleutel wordt opgeslagen in het configuratie bestand. Zie de para meter **token: IssuerSigningKey** in de *appsettings.Development.jsvoor* het bestand.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Wanneer u een **Get** -aanvraag naar het `/scim/token` eind punt verzendt, wordt er een token uitgegeven met de geconfigureerde sleutel. Dit token kan worden gebruikt als Bearer-token voor de volgende autorisatie.

De standaard code voor token validatie is geconfigureerd voor het gebruik van een Azure AD-token en vereist dat de uitgevende Tenant wordt geconfigureerd met de para meter **token: TokenIssuer** in de *appsettings.jsvoor* het bestand.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Postman gebruiken om eind punten te testen

Nadat u het SCIM-eind punt hebt geïmplementeerd, kunt u testen om er zeker van te zijn dat het compatibel is met SCIM RFC. Dit voor beeld bevat een reeks tests in postman waarmee ruwe (maken, lezen, bijwerken en verwijderen) bewerkingen worden gevalideerd voor gebruikers en groepen, filters, updates voor groepslid maatschap en gebruikers uitschakelen.

De eind punten bevinden zich in de `{host}/scim/` map en u kunt standaard HTTP-aanvragen gebruiken om ermee te communiceren. `/scim/`Zie *ControllerConstant.cs* in **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**-  >  **controllers** om de route te wijzigen.

> [!NOTE]
> U kunt alleen HTTP-eind punten gebruiken voor lokale tests. De Azure AD-inrichtings service vereist dat uw endpoint HTTPS ondersteunt.

1. Down load [postman](https://www.getpostman.com/downloads/) en start de toepassing.
1. Kopieer en plak deze koppeling in de Postman om de test verzameling te importeren: `https://aka.ms/ProvisioningPostman` .

    ![Scherm opname van het importeren van de test verzameling in postman.](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Maak een test omgeving met deze variabelen:

   |Omgeving|Variabele|Waarde|
   |-|-|-|
   |Voer het project lokaal uit met behulp van IIS Express|||
   ||**Server**|`localhost`|
   ||**Poort**|`:44359`*(Vergeet niet de **`:`** )*|
   ||**Inschakelen**|`scim`|
   |Het project lokaal uitvoeren met behulp van Kestrel|||
   ||**Server**|`localhost`|
   ||**Poort**|`:5001`*(Vergeet niet de **`:`** )*|
   ||**Inschakelen**|`scim`|
   |Het eind punt hosten in azure|||
   ||**Server**|*(Voer uw SCIM-URL in)*|
   ||**Poort**|*(leeg laten)*|
   ||**Inschakelen**|`scim`|

1. Gebruik de **Get-sleutel** van de Postman-verzameling om een **Get** -aanvraag naar het token-eind punt te verzenden en een beveiligings token op te halen dat in de **token** variabele voor volgende aanvragen moet worden opgeslagen.

   ![Scherm opname van de map postman Get-sleutel.](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Als u een SCIM-eind punt beveiligd wilt maken, hebt u een beveiligings token nodig voordat u verbinding maakt. In de zelf studie wordt het `{host}/scim/token` eind punt gebruikt voor het genereren van een zelfondertekend token.

Dat is alles. U kunt nu de **postman** -verzameling uitvoeren om de functionaliteit van het scim-eind punt te testen.

## <a name="next-steps"></a>Volgende stappen

Zie [scim-client implementatie](http://www.simplecloud.info/#Implementations2)als u een scim-compatibel gebruikers-en groeps eindpunt wilt ontwikkelen met interoperabiliteit voor een client.

> [!div class="nextstepaction"]
> [Zelf studie: inrichten opstellen en plannen voor een scim-eind punt](use-scim-to-provision-users-and-groups.md) 
>  [Zelf studie: inrichten configureren voor een galerie-app](configure-automatic-user-provisioning-portal.md)