---
title: Een SCIM-eind punt bouwen voor het inrichten van gebruikers voor apps vanuit Azure AD
description: Met System for Cross-domain Identity Management (SCIM) wordt het automatisch inrichten van gebruikers gestandaardiseerd. Leer hoe u een SCIM-eind punt ontwikkelt, uw SCIM-API integreert met Azure Active Directory en beginnen met het automatiseren van het inrichten van gebruikers en groepen in uw Cloud toepassingen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ae36af981b113d44ac1b8fd45a1d084760b0294
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900161"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Zelf studie: een voor beeld van een SCIM-eind punt ontwikkelen

Niemand wil zelf een nieuw eind punt bouwen, dus we hebben een [verwijzings code](https://aka.ms/scimreferencecode) gemaakt om aan de slag te gaan met [scim](https://aka.ms/scimoverview). In deze zelf studie wordt beschreven hoe u de SCIM-referentie code implementeert in Azure en deze test met behulp van Postman of door te integreren met de Azure AD SCIM-client. U kunt uw SCIM-eind punt in slechts vijf minuten actief maken zonder code. Deze zelf studie is bedoeld voor ontwikkel aars die aan de slag willen gaan met SCIM of anderen die geïnteresseerd zijn in het testen van een SICM-eind punt. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De referentie code downloaden
> * Uw SCIM-eind punt implementeren in azure
> * Uw SCIM-eind punt testen

De eindpunt mogelijkheden, inclusief:

|Eindpunt|Beschrijving|
|---|---|
|`/User`|RUWE bewerkingen uitvoeren voor een gebruikers resource: **maken**, **bijwerken**, **verwijderen**, **ophalen**, **weer geven**, **filteren**|
|`/Group`|RUWE bewerkingen uitvoeren op een groeps resource: **maken**, **bijwerken**, **verwijderen**, **ophalen**, **weer geven**, **filteren**|
|`/Schemas`|Een of meer ondersteunde schema's ophalen.<br/><br/>De set kenmerken van een resource die wordt ondersteund door elke service provider kan variëren, bijvoorbeeld: de service provider A ondersteunt ' naam ', ' titel ' en ' e-mail berichten ' terwijl service provider B ' naam ', ' titel ' en ' phoneNumbers ' ondersteunt voor gebruikers.|
|`/ResourceTypes`|Ondersteunde resource typen ophalen.<br/><br/>Het aantal en de typen resources die worden ondersteund door elke service provider kan variëren, bijvoorbeeld service provider A ondersteunt gebruikers terwijl service provider B gebruikers en groepen ondersteunt.|
|`/ServiceProviderConfig`|De SCIM-configuratie van de service provider ophalen<br/><br/>De SCIM-functies die worden ondersteund door elke service provider kunnen variëren, bijvoorbeeld service provider A ondersteunt patch bewerkingen terwijl service provider B patch bewerkingen en schema detectie ondersteunt.|

## <a name="download-the-reference-code"></a>De referentie code downloaden

De [referentie code](https://github.com/AzureAD/SCIMReferenceCode) die moet worden gedownload, omvat de volgende projecten:

- **Microsoft.SystemForCrossDomainIdentityManagement**, de .net core MVC Web API om een scim-API te bouwen en in te richten
- **Micro soft. scim. WebHostSample**, een werkend voor beeld van een scim-eind punt

De projecten bevatten de volgende mappen en bestanden:

|Bestand/map|Beschrijving|
|-|-|
|Map met **schema's**| De modellen voor de **gebruikers** -en **groeps** resources samen met enkele abstracte klassen, zoals geschematiseerde voor gedeelde functionaliteit.<br/><br/> Een map **Attributes** die de klassedefinities bevat voor complexe kenmerken van **gebruikers** en **groepen** , zoals adressen.|
|Map **service** | Bevat logica voor acties die betrekking hebben op de manier waarop resources worden opgevraagd en bijgewerkt.<br/><br/> De referentie code bevat services voor het retour neren van gebruikers en groepen.<br/><br/>De map **controllers** bevat de verschillende scim-eind punten. Bron controllers bevatten HTTP-woorden om ruwe bewerkingen uit te voeren op de resource (**Get**, **post**, **put**, **patch**, **Delete**). Controllers zijn afhankelijk van services om de acties uit te voeren.|
|Map **protocol**|Bevat logica voor acties die betrekking hebben op de manier waarop bronnen worden geretourneerd volgens de SCIM RFC, zoals:<br/><ul><li>Het retour neren van meerdere resources als een lijst.</li><li>Alleen specifieke resources worden geretourneerd op basis van een filter.</li><li>Een query omzetten in een lijst met gekoppelde lijsten met één filter.</li><li>Een PATCH-aanvraag omzetten in een bewerking met kenmerken die betrekking hebben op het pad naar de waarde.</li><li>Het definiëren van het type bewerking dat kan worden gebruikt om wijzigingen toe te passen op Resource-objecten.</li></ul>|
|`Microsoft.SystemForCrossDomainIdentityManagement`| Voorbeeld bron code.|
|`Microsoft.SCIM.WebHostSample`| Voorbeeld implementatie van de SCIM-bibliotheek.|
|*.gitignore*|Definieer wat u wilt negeren tijdens het door voeren.|
|*CHANGELOG.md*|Lijst met wijzigingen in het voor beeld.|
|*CONTRIBUTING.md*|Richt lijnen voor het bijdragen aan het voor beeld.|
|*README.md*|Dit **Leesmij** -bestand.|
|*HOUDERS*|De licentie voor het voor beeld.|

> [!NOTE]
> Deze code is bedoeld om te helpen bij het bouwen van een SCIM-eind punt en wordt **als zodanig** gegeven. De opgenomen referenties hebben geen garantie voor actieve maintainence of ondersteuning.
>
> Op dit project is de [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Microsoft Open Source-gedragscode) van toepassing. Omdat de [bijdragen](https://github.com/AzureAD/SCIMReferenceCode/wiki/Contributing-Overview) van de Community welkom zijn bij het bouwen en onderhouden van de opslag plaats, en als andere open-source bijdragen, gaat u akkoord met een CLA (Contributor-licentie overeenkomst). Deze overeenkomst verklaart dat u beschikt over de rechten voor het gebruik van uw bijdrage, Zie [micro soft open source](https://cla.opensource.microsoft.com)voor meer informatie.
>
> Raadpleeg de [Veelgestelde vragen over de gedragscode](https://opensource.microsoft.com/codeofconduct/faq/) voor meer informatie of neem contact op met [opencode@microsoft.com](mailto:opencode@microsoft.com) als u meer vragen of opmerkingen hebt.

###  <a name="use-multiple-environments"></a>Meerdere omgevingen gebruiken

De opgenomen SCIM-code maakt gebruik van een ASP.NET Core omgeving voor het beheren van de autorisatie voor gebruik in ontwikkeling en na de implementatie raadpleegt [u meerdere omgevingen gebruiken in ASP.net core](https://docs.microsoft.com/aspnet/core/fundamentals/environments?view=aspnetcore-3.1).

```csharp
private readonly IWebHostEnvironment _env;
...

public void ConfigureServices(IServiceCollection services)
{
    if (_env.IsDevelopment())
    {
        ...
    }
    else
    {
        ...
    }
```

## <a name="deploy-your-scim-endpoint-in-azure"></a>Uw SCIM-eind punt implementeren in azure

De stappen die hier worden beschreven, implementeren het SCIM-eind punt voor een service met behulp van [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) en [Azure-app Services](https://docs.microsoft.com/azure/app-service/). De SCIM-referentie code kan ook lokaal worden uitgevoerd, worden gehost door een on-premises server of worden geïmplementeerd in een andere externe service. 

### <a name="open-solution-and-deploy-to-azure-app-service"></a>Oplossing openen en implementeren in Azure App Service

1. Ga naar de [referentie code](https://github.com/AzureAD/SCIMReferenceCode) van github en selecteer **klonen of downloaden**.

1. Kies of u wilt **openen op het bureau blad**, of kopieer de koppeling, open **Visual Studio** en selecteer **klonen of uitchecken code** om de gekopieerde koppeling in te voeren en een lokale kopie te maken.

1. Zorg er in **Visual Studio** voor dat u zich aanmeldt bij het account dat toegang heeft tot uw hosting-resources.

1. Open in **Solution Explorer** *micro soft. scim. SLN* en klik met de rechter muisknop op het bestand *micro soft. scim. WebHostSample* . Selecteer **Publiceren**.

    ![Cloud publicatie](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Als u deze oplossing lokaal wilt uitvoeren, dubbelklikt u op het project en selecteert u **IIS Express** om het project te openen als een webpagina met een lokale host-URL.

1. Selecteer **profiel maken** en controleer of **app service** en **nieuwe maken** zijn geselecteerd.

    ![Cloud publicatie 2](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Door loop de opties van het dialoog venster en wijzig de naam van de app in een door u gemaakte keuze. Deze naam wordt gebruikt in zowel de app als de SCIM-eind punt-URL.

    ![Cloud publicatie 3](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Selecteer de resource groep die u wilt gebruiken en kies **publiceren**.

1. Navigeer naar de toepassing in **Azure-app Services**-  >  **configuratie** en selecteer **nieuwe toepassings instelling** om de instelling *Token__TokenIssuer* met de waarde toe te voegen `https://sts.windows.net/<tenant_id>/` . Vervang door `<tenant_id>` uw Azure AD-tenant_id en als u het scim-eind punt met behulp van [postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)wilt testen, voegt u ook een *ASPNETCORE_ENVIRONMENT* instelling toe met de waarde `Development` . 

   ![appservice-instellingen](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Bij het testen van uw eind punt met een bedrijfs toepassing in de Azure Portal kiest u ervoor om de omgeving te behouden `Development` en het token op te geven dat is gegenereerd op basis van het `/scim/token` eind punt voor het testen of wijzigen van de omgeving `Production` , en laat u het veld token leeg in de bedrijfs toepassing in de [Azure Portal](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client). 

Dat is alles. Uw SCIM-eind punt wordt nu gepubliceerd en u kunt de Azure App Service URL gebruiken om het SCIM-eind punt te testen.

## <a name="test-your-scim-endpoint"></a>Uw SCIM-eind punt testen

Voor de aanvragen van een SCIM-eind punt zijn autorisatie vereist en de SCIM-standaard laat meerdere opties voor verificatie en autorisatie, zoals cookies, basis verificatie, TLS-client verificatie of een van de methoden die worden vermeld in [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Vermijd onveilige methoden, zoals gebruikers naam en wacht woord, voor een veiligere methode zoals OAuth. Azure AD biedt ondersteuning voor langlopende Bearer-tokens (voor galerie-en niet-galerie toepassingen) en de OAuth-autorisatie toekenning (voor toepassingen die zijn gepubliceerd in de app-galerie).

> [!NOTE]
> De autorisatie methoden in de opslag plaats zijn alleen bedoeld voor test doeleinden. Wanneer u integreert met Azure AD, kunt u de autorisatie richtlijnen bekijken, het [inrichten plannen voor een scim-eind punt](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#authorization-for-provisioning-connectors-in-the-application-gallery). 

De ontwikkel omgeving maakt functies die onveilig zijn voor productie, zoals referentie code, om het gedrag van de validatie van het beveiligings token te beheren. De token validatie code is geconfigureerd voor het gebruik van een zelf-ondertekend beveiligings token en de handtekening sleutel wordt opgeslagen in het configuratie bestand. Zie de para meter **token: IssuerSigningKey** in de *appsettings.Development.jsvoor* het bestand.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Door een **Get** -aanvraag naar het `/scim/token` eind punt te verzenden, wordt een token uitgegeven met de geconfigureerde sleutel en kan worden gebruikt als Bearer-token voor de volgende autorisatie.

De standaard code voor token validatie is geconfigureerd voor het gebruik van een token dat is uitgegeven door Azure Active Directory en vereist dat de verlenende Tenant wordt geconfigureerd met de para meter **token: TokenIssuer** in de *appsettings.jsvoor* het bestand.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Postman gebruiken om eind punten te testen

Nadat het SCIM-eind punt is geïmplementeerd, kunt u testen om er zeker van te zijn dat het SCIM RFC-compatibel is. Dit voor beeld bevat een reeks tests in **postman** voor het valideren van ruwe bewerkingen op gebruikers en groepen, filters, updates voor groepslid maatschap en het uitschakelen van gebruikers.

De eind punten bevinden zich in de `{host}/scim/` map en kunnen worden gebruikt met behulp van standaard HTTP-aanvragen. `/scim/`Zie *ControllerConstant.cs* in **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**-  >  **controllers** om de route te wijzigen.

> [!NOTE]
> U kunt alleen HTTP-eind punten gebruiken voor lokale tests als de Azure AD-inrichtings service ondersteuning vereist voor HTTPS voor uw endpoint.

#### <a name="open-postman-and-run-tests"></a>Open postman en voer tests uit

1. Down load [berichtman](https://www.getpostman.com/downloads/) en start de toepassing.
1. Kopieer de koppeling [https://aka.ms/ProvisioningPostman](https://aka.ms/ProvisioningPostman) en plak deze in het bericht om de test verzameling te importeren.

    ![Postman-verzameling](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Maak een test omgeving met de volgende variabelen:

   |Omgeving|Variabele|Waarde|
   |-|-|-|
   |Project lokaal uitvoeren met IIS Express|||
   ||**Server**|`localhost`|
   ||**Poort**|`:44359`*(Vergeet niet de **:**)*|
   ||**Inschakelen**|`scim`|
   |Project lokaal uitvoeren met Kestrel|||
   ||**Server**|`localhost`|
   ||**Poort**|`:5001`*(Vergeet niet de **:**)*|
   ||**Inschakelen**|`scim`|
   |Het eind punt hosten in azure|||
   ||**Server**|*(Voer uw SCIM-URL in)*|
   ||**Poort**|*(leeg laten)*|
   ||**Inschakelen**|`scim`|

1. Gebruik de **Get-sleutel** van de Postman-verzameling om een **Get** -aanvraag naar het token-eind punt te verzenden en een beveiligings token op te halen dat in de **token** variabele voor volgende aanvragen moet worden opgeslagen. 

   ![Get-sleutel van postman](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Als u een SCIM-eind punt wilt beveiligen, hebt u een beveiligings token nodig voordat u verbinding kunt maken. in de zelf studie wordt het `{host}/scim/token` eind punt gebruikt voor het genereren van een zelfondertekend token.

Dat is alles. U kunt nu de **postman** -verzameling uitvoeren om de functionaliteit van het scim-eind punt te testen.

## <a name="next-steps"></a>Volgende stappen

Zie de [scim-client implementatie](http://www.simplecloud.info/#Implementations2)voor het ontwikkelen van een scim-compatibel gebruikers-en groeps eindpunt met interoperabiliteit voor een client.

> [!div class="nextstepaction"]
> [Zelf studie: inrichten opstellen en plannen voor een scim-eind punt](use-scim-to-provision-users-and-groups.md) 
>  [Zelf studie: inrichten configureren voor een galerie-app](configure-automatic-user-provisioning-portal.md)