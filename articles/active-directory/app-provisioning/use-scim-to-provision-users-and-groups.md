---
title: 'Zelfstudie: een SCIM-eindpunt ontwikkelen voor het inrichten van gebruikers voor apps vanuit Azure AD'
description: Met System for Cross-domain Identity Management (SCIM) wordt het automatisch inrichten van gebruikers gestandaardiseerd. In deze zelfstudie leert u hoe u een SCIM-eindpunt ontwikkelt, uw SCIM-API integreert met Azure Active Directory en begint met het automatiseren van het inrichten van gebruikers en groepen in uw Cloud-toepassingen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6250aef5ec69c65dcdfe457a56d4e077b31af4d1
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051665"
---
# <a name="tutorial-develop-and-plan-provisioning-for-a-scim-endpoint"></a>Zelf studie: inrichten opstellen en plannen voor een SCIM-eind punt

Als ontwikkelaar van een toepassing kunt u de System for Cross-Domain Identity Management (SCIM)-gebruikersbeheer-API gebruiken om het automatisch inrichten van gebruikers en groepen tussen uw toepassing en Azure AD mogelijk te maken. In dit artikel wordt beschreven hoe u een SCIM-eindpunt bouwt en integreert met de Azure AD-inrichtingsservice. De SCIM-specificatie biedt een gemeenschappelijk schema voor het inrichten van gebruikers. Bij gebruik in combinatie met federatiestandaarden zoals SAML of OpenID Connect, biedt SCIM beheerders een end-to-end, op standaarden gebaseerde oplossing voor toegangsbeheer.

SCIM is een gestandaardiseerde definitie van twee eind punten: een `/Users` eind punt en een `/Groups` eind punt. Er worden algemene REST-werkwoorden gebruikt om objecten te maken, bij te werken en te verwijderen, en een vooraf gedefinieerd schema voor algemene kenmerken zoals groepsnaam, gebruikersnaam, voornaam, achternaam en e-mailadres. Apps die een SCIM 2.0-REST API bieden, kunnen de nadelen van het werken met een eigen API voor gebruikersbeheer verminderen of elimineren. Elke compatibele SCIM-client weet bijvoorbeeld hoe u een HTTP POST van een JSON-object naar het `/Users` eind punt kunt maken om een nieuwe gebruikers vermelding te maken. Apps die voldoen aan de SCIM-standaard kunnen direct profiteren van bestaande clients, hulpprogramma's en code, in plaats van dat ze een iets andere API voor dezelfde basisacties moeten gebruiken. 

![Inrichten vanuit Azure AD naar een app met SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Met het standaard gebruikersobjectschema en de REST-beheer-API’s die zijn gedefinieerd in SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) kunnen id-providers en apps gemakkelijker met elkaar worden geïntegreerd. Toepassingsontwikkelaars die een SCIM-eindpunt bouwen, kunnen deze integreren met elke SCIM-compatibele client zonder aanpassingen te doen.

Voor het automatiseren van de inrichting van een toepassing moet u een SCIM-eindpunt bouwen en integreren met de Azure AD SCIM-client. Voer de volgende stappen uit om het inrichten van gebruikers en groepen in uw toepassing te starten. 
    
  * **[Stap 1: Ontwerp uw gebruikers- en groepsschema.](#step-1-design-your-user-and-group-schema)** Identificeer de objecten en kenmerken die uw toepassing nodig heeft en bepaal hoe deze worden toegewezen aan het gebruikers- en groepsschema dat wordt ondersteund door de Azure AD SCIM-implementatie.

  * **[Stap 2: Begrijp de werking van de Azure AD SCIM-implementatie.](#step-2-understand-the-azure-ad-scim-implementation)** Leer hoe de Azure AD SCIM-client wordt geïmplemtenteerd en modelleer de verwerking van de aanvragen en antwoorden van uw SCIM-protocol.

  * **[Stap 3: Bouw een SCIM-eind punt.](#step-3-build-a-scim-endpoint)** Een eindpunt moet compatibel zijn met SCIM 2.0 om te kunnen worden geïntegreerd met de Azure AD-inrichtingsservice. Als optie kunt u gebruikmaken van Microsoft Common Language Infrastructure (CLI)-bibliotheken en -codevoorbeelden om uw eindpunt te bouwen. Deze voorbeelden zijn alleen ter referentie en om te testen. We raden af om uw productie-app op basis hiervan te coderen.

  * **[Stap 4: Integreer uw SCIM-eindpunt met de Azure AD SCIM-client.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Als uw organisatie gebruikmaakt van een toepassing van derden die het profiel van SCIM 2.0 dat door Azure AD wordt ondersteund, implementeert, kunt u direct beginnen met het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en groepen.

  * **[Stap 5: Uw toepassing publiceren in de Azure AD-toepassingsgalerie.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Maak het eenvoudig voor klanten om uw toepassing te detecteren en de inrichting eenvoudig te configureren. 

![Stappen voor het integreren van een SCIM-eindpunt met Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Stap 1: Uw gebruikers- en groepsschema ontwerpen

Voor elke toepassing zijn verschillende kenmerken vereist om een gebruiker of groep te maken. Start uw integratie door de objecten (gebruikers, groepen) en kenmerken (naam, beheerder, functie, enz.) te identificeren die nodig zijn voor uw toepassing. De SCIM-standaard biedt een schema voor het beheren van gebruikers en groepen. Het algemene gebruikersschema vereist slechts drie kenmerken: **id** (id van de service provider), **externalId** (id van de client) en **meta** (alleen-lezen metagegevens die worden bijgehouden door de service provider). Alle andere kenmerken zijn optioneel. Naast het algemene gebruikersschema biedt de SCIM-standaard een extensie voor enterprise-gebruikers en een model om het gebruikersschema uit te breiden om te voldoen aan de behoeften van uw toepassing. Als uw toepassing bijvoorbeeld de beheerder van een gebruiker vereist, kunt u het enterprise-gebruikersschema gebruiken om de beheerder van de gebruiker en het algemene schema op te halen voor het e-mailadres van de gebruiker. Volg de onderstaande stappen om uw schema te ontwerpen:
  1. Maak een lijst van de kenmerken die uw toepassing nodig heeft. Het kan handig zijn om uw vereisten onder te verdelen in kenmerken die nodig zijn voor verificatie (bijvoorbeeld aanmeldingsnaam en e-mail adres), kenmerken die nodig zijn voor het beheren van de levenscyclus van de gebruiker (bijvoorbeeld status/actief) en andere kenmerken die nodig zijn om uw specifieke toepassing te laten werken (bijvoorbeeld beheerder, tag).
  2. Controleer of deze kenmerken al zijn gedefinieerd in het algemene gebruikersschema of het enterprise-gebruikersschema. Als u kenmerken nodig hebt die niet worden behandeld in de algemene of enterprise-gebruikersschema's, moet u een extensie definiëren voor het gebruikersschema met de kenmerken die u nodig hebt. In het onderstaande voorbeeld hebben we een extensie toegevoegd aan de gebruiker om het inrichten van een 'tag' voor een gebruiker toe te staan. U kunt het beste beginnen met alleen de algemene en enterprise-gebruikersschema's en deze later verder uitbreiden met aangepaste schema's.  
  3. Wijs de SCIM-kenmerken toe aan de gebruikerskenmerken in Azure AD. Als een van de kenmerken die u hebt gedefinieerd in uw SCIM-eindpunt geen duidelijke vergelijkbare tegenhanger heeft in het Azure AD-gebruikersschema, is de kans groot dat de meeste tenants de gegevens niet in het gebruikersobject opslaan. Bepaal of dit kenmerk optioneel kan zijn voor het maken van een gebruiker. Als het kenmerk essentieel is voor de werking van uw toepassing, kunt u de tenantbeheerder helpen hun schema uit te breiden of een uitbreidingskenmerk te gebruiken zoals hieronder wordt weergegeven voor de eigenschap "Tags".

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabel 1: Overzicht maken van de kenmerken die u nodig hebt 
| Stap 1: Bepaal de kenmerken die uw app nodig heeft| Stap 2: Wijs de app-vereisten toe aan SCIM Standard| Stap 3: Wijs de SCIM-kenmerken toe aan de Azure AD-kenmerken|
|--|--|--|
|loginName|userName|userPrincipalName|
|voornaam|name.givenName|givenName|
|achternaam|name.familyName|Roep|
|workMail|e-mail berichten [type EQ "werk]. waarde|Mail|
|manager|manager|manager|
|tag|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|actief|isSoftDeleted (berekende waarde niet opgeslagen voor gebruiker)|

Het hierboven gedefinieerde schema wordt weergegeven met behulp van de onderstaande JSON-payload. Houd er rekening mee dat naast de vereiste kenmerken voor de toepassing de JSON-weergave de vereiste kenmerken `id`, `externalId`en `meta` bevat.

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
}   
```

### <a name="table-2-default-user-attribute-mapping"></a>Tabel 2: Standaardtoewijzing van gebruikerskenmerk
U kunt de onderstaande tabel gebruiken om te begrijpen hoe de kenmerken die uw toepassing nodig heeft, kunnen worden toegewezen aan een kenmerk in Azure AD en de SCIM RFC. U kunt [aanpassen](customize-application-attributes.md) hoe kenmerken worden toegewezen tussen Azure AD en uw SCIM-eindpunt. Houd er rekening mee dat u niet zowel gebruikers als groepen of alle hieronder weer gegeven kenmerken hoeft te ondersteunen. Ze laten alleen de manier zien waarop kenmerken in Azure AD vaak worden toegewezen aan eigenschappen in het SCIM-protocol. 

| Azure Active Directory-gebruiker | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |actief |
|department|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|employeeId|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |titel |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| mobiel |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Tabel 3: Standaardtoewijzing van groepskenmerk

| Azure Active Directory-groep | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| leden |leden |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

Er zijn verschillende eindpunten gedefinieerd in de SCIM RFC. U kunt beginnen met het /User-eindpunt en vervolgens van daaruit verder gaan. Het /Schemas-eindpunt is handig wanneer u aangepaste kenmerken gebruikt of als uw schema regelmatig wordt gewijzigd. Hiermee kan een client automatisch het meest recente schema ophalen. Het /Bulk-eindpunt is vooral handig bij het ondersteunen van groepen. De volgende tabel beschrijft de verschillende eindpunten die zijn gedefinieerd in de SCIM-standaard.
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabel 4: Bepaal de eind punten die u wilt ontwikkelen
|EINDPUNT:|BESCHRIJVING|
|--|--|
|/User|Hiermee voert u CRUD-bewerkingen op een gebruikersobject uit.|
|/Group|Hiermee voert u CRUD-bewerkingen op een groepsobject uit.|
|/ServiceProviderConfig|Bevat informatie over de functies van de SCIM-standaard die worden ondersteund, bijvoorbeeld de resources die worden ondersteund en de verificatiemethode.|
|/ResourceTypes|Hiermee geeft u de metagegevens van elke resource op|
|/Schemas|De set kenmerken die door elke client en serviceprovider wordt ondersteund, kan variëren. De ene serviceprovider kan `name`, `title` en `emails` gebruiken, terwijl een andere service provider `name`, `title` en `phoneNumbers` gebruikt. Het eindpunt voor schema's maakt het mogelijk om de kenmerken te detecteren die worden ondersteund.|
|/Bulk|Met bulkbewerkingen kunt u met één bewerking verschillende bewerkingen uitvoeren op een grote verzameling Resource-objecten (zoals lidmaatschappen bijwerken voor een grote groep).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Stap 2: Begrijp de werking van de Azure AD SCIM-implementatie.
> [!IMPORTANT]
> Het gedrag van de Azure AD SCIM-implementatie is voor het laatst bijgewerkt op 18 december 2018. Zie [Naleving van het SCIM 2.0 protocol van de Azure AD-inrichtingsservice voor gebruikers](application-provisioning-config-problem-scim-compatibility.md) voor meer informatie over wat er is gewijzigd.

Als u een toepassing bouwt die een SCIM 2.0-gebruikersbeheer-API ondersteunt, wordt in deze sectie in detail beschreven hoe de Azure AD SCIM-client is geïmplementeerd. Ook wordt uitgelegd hoe u de verwerking van SCIM-protocolaanvragen en -antwoorden kunt modelleren. Nadat u uw SCIM-eindpunt hebt geïmplementeerd, kunt u dit testen door de procedure te volgen die in de vorige sectie is beschreven.

Volgens de [SCIM 2.0-protocolspecificatie](http://www.simplecloud.info/#Specification) moet uw toepassing aan de volgende vereisten voldoen:

* Biedt ondersteuning voor het maken van gebruikers en optioneel ook groepen, volgens sectie [3.3 van het SCIM-protocol](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Ondersteunt het wijzigen van gebruikers of groepen met PATCH-aanvragen, volgens sectie [3.5.2 van het SCIM-protocol](https://tools.ietf.org/html/rfc7644#section-3.5.2). Ondersteuning garandeert dat groepen en gebruikers op een efficiënte manier worden ingericht. 
* Biedt ondersteuning voor het ophalen van een bekende resource voor een eerder gemaakte gebruiker of groep, volgens [sectie 3.4.1 van het SCIM-protocol](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Biedt ondersteuning voor het uitvoeren van query's op gebruikers of groepen, volgens sectie [3.4.2 van het SCIM-protocol](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Gebruikers worden standaard opgehaald op basis van `id` en query’s worden uitgevoerd op basis van `username` en de `externalId`. Groepen worden opgehaald door query’s uit te voeren op basis van `displayName`.  
* Biedt ondersteuning voor het uitvoeren van query's op de gebruiker op basis van de id en beheerder, volgens sectie 3.4.2 van het SCIM-protocol.  
* Ondersteunt het opvragen van groepen op basis van id en lid, volgens sectie 3.4.2 van het SCIM-protocol.  
* Biedt ondersteuning voor het filter [excludedAttributes=members](#get-group) wanneer u een query uitvoert op de groepsresource, volgens sectie 3.4.2.5 van het SCIM-protocol.
* Hiermee wordt één Bearer-token geaccepteerd voor verificatie en autorisatie van Azure AD voor uw toepassing.
* Ondersteunt het voorlopig verwijderen van een gebruiker `active=false` en het herstellen van de gebruiker `active=true` (het gebruikersobject moet worden geretourneerd in een aanvraag, ongeacht of de gebruiker wel of niet actief is). De enige keer dat een gebruiker niet wordt geretourneerd, is wanneer deze permanent wordt verwijderd uit de toepassing. 

Volg deze algemene richtlijnen bij het implementeren van een SCIM-eindpunt om te zorgen voor compatibiliteit met Azure AD:

* `id` is een vereiste eigenschap voor alle resources. Elk antwoord dat een resource retourneert, moet ervoor zorgen dat elke resource deze eigenschap bevat, met uitzondering van `ListResponse` met nul leden.
* Het antwoord op een query/filter-aanvraag moet altijd een `ListResponse` zijn.
* Groepen zijn optioneel, maar worden alleen ondersteund als de SCIM-implementatie PATCH-aanvragen ondersteunt.
* Het is niet nodig om de volledige resource op te nemen in het PATCH-antwoord.
* Microsoft Azure AD gebruikt alleen de volgende operators:  
    - `eq`
    - `and`
* Vereist geen hoofdlettergevoelige overeenkomst voor structurele elementen in SCIM, met name PATCH `op`-bewerkingswaarden, zoals gedefinieerd in https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD verzendt de waarden van 'op' als `Add`, `Replace` en `Remove`.
* Microsoft Azure AD doet aanvragen voor het ophalen van een willekeurige gebruiker en groep om ervoor te zorgen dat het eindpunt en de referenties geldig zijn. Het wordt ook uitgevoerd als onderdeel van de werkstroom **Verbinding testen** in de [Azure Portal](https://portal.azure.com). 
* Het kenmerk waarmee de resources kunnen worden opgevraagd, moet worden ingesteld als overeenkomend kenmerk in de toepassing in de [Azure Portal](https://portal.azure.com). Zie voor meer informatie [Het aanpassen van kenmerktoewijzingen voor het inrichten van gebruikers](customize-application-attributes.md)
* Ondersteuning voor HTTPS op uw SCIM-eindpunt

### <a name="user-provisioning-and-deprovisioning"></a>Inrichting en ongedaan maken van inrichting van gebruikers

In de volgende afbeelding ziet u de berichten die Azure Active Directory verzendt naar een SCIM-service om de levenscyclus van een gebruiker te beheren in de identiteitsopslag van uw toepassing.  

![Hiermee wordt de volgorde voor de inrichting en het ongedaan maken van de inrichting voor gebruikers weergegeven](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Volgorde voor inrichting en ongedaan maken van inrichting van gebruikers*

### <a name="group-provisioning-and-deprovisioning"></a>Inrichting en ongedaan maken van inrichting van groepen

De inrichting en het ongedaan maken van de inrichting van groepen is optioneel. In de volgende afbeelding ziet u de berichten die, indien geïmplementeerd en ingeschakeld, door Azure AD worden verzonden naar een SCIM-service om de levenscyclus van een groep te beheren in de identiteitsopslag van uw toepassing.  Deze berichten verschillen op twee manieren van de berichten over gebruikers:

* Bij aanvragen voor het ophalen van groepen wordt het ledenkenmerk uitgesloten van alle resources die worden weergegeven als antwoord op de aanvraag.  
* Aanvragen om te bepalen of een verwijzingskenmerk een bepaalde waarde heeft zijn aanvragen over het ledenkenmerk.  

![Hiermee wordt de volgorde voor de inrichting en het ongedaan maken van de inrichting voor groepen weergegeven](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Volgorde voor inrichting en ongedaan maken van inrichting van groepen*

### <a name="scim-protocol-requests-and-responses"></a>Aanvragen en antwoorden van het SCIM-Protocol
Deze sectie bevat voorbeelden van SCIM-aanvragen die worden verzonden door de Azure AD SCIM-client en voorbeelden van verwachte antwoorden. Voor de beste resultaten moet u de app coderen om deze aanvragen in deze indeling af te handelen en de verwachte antwoorden te verzenden.

> [!IMPORTANT]
> Als u wilt weten hoe en wanneer de Azure AD-inrichtingsservice voor gebruiker de hieronder beschreven bewerkingen uitvoert, raadpleegt u de sectie [Inrichtingscycli: De eerste en incrementele](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in [hoe werkt inrichten](how-provisioning-works.md).

[Gebruikersbewerkingen](#user-operations)
  - [Gebruiker maken](#create-user) ([Aanvraag](#request) / [Antwoord](#response))
  - [Gebruiker ophalen](#get-user) ([Aanvraag](#request-1) / [Antwoord](#response-1))
  - [Gebruiker ophalen op basis van query](#get-user-by-query) ([Aanvraag](#request-2) / [Antwoord](#response-2))
  - [Gebruiker ophalen op basis van query: nul resultaten](#get-user-by-query---zero-results) ([Aanvraag](#request-3) / [Antwoord](#response-3))
  - [Gebruiker bijwerken [eigenschappen met meerdere waarden]](#update-user-multi-valued-properties) ([Aanvraag](#request-4) / [Antwoord](#response-4))
  - [Gebruiker bijwerken [eigenschappen met één waarde]](#update-user-single-valued-properties) ([Aanvraag](#request-5) / [Antwoord](#response-5)) 
  - [Gebruiker uitschakelen](#disable-user) ([Aanvraag](#request-14) / [Antwoord](#response-14))
  - [Gebruiker verwijderen](#delete-user) ([Aanvraag](#request-6) / [Antwoord](#response-6))


[Groepsbewerkingen](#group-operations)
  - [Groep maken](#create-group) ([Aanvraag](#request-7) / [Antwoord](#response-7))
  - [Groep ophalen](#get-group) ( [Aanvraag](#request-8) / [Antwoord](#response-8))
  - [Groep ophalen op basis van displayName](#get-group-by-displayname) ([Aanvraag](#request-9) / [Antwoord](#response-9))
  - [Groep bijwerken [kenmerken voor niet-leden]](#update-group-non-member-attributes) ([Aanvraag](#request-10) / [Antwoord](#response-10))
  - [Groep bijwerken [leden toevoegen]](#update-group-add-members) ([Aanvraag](#request-11) / [Antwoord](#response-11))
  - [Groep bijwerken [leden verwijderen]](#update-group-remove-members) ([Aanvraag](#request-12) / [Antwoord](#response-12))
  - [Groep verwijderen](#delete-group) ([Aanvraag](#request-13) / [Antwoord](#response-13))

### <a name="user-operations"></a>Gebruikersbewerkingen

* Er kunnen query’s worden uitgevoerd op gebruikers op basis van `userName`- of `email[type eq "work"]`-kenmerken.  

#### <a name="create-user"></a>Gebruiker maken

###### <a name="request"></a>Aanvraag

*POST /Users*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Antwoord

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Gebruiker ophalen

###### <a name="request"></a><a name="request-1"></a>Aanvraag
*OPHALEN /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Antwoord (gebruiker gevonden)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Aanvraag
*GET /Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Antwoord (gebruiker niet gevonden. Houd er rekening mee dat de details niet vereist zijn, alleen de status.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Gebruiker ophalen met query

##### <a name="request"></a><a name="request-2"></a>Aanvraag

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Response

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Gebruiker ophalen met query: nul resultaten

##### <a name="request"></a><a name="request-3"></a>Aanvraag

*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response"></a><a name="response-3"></a>Response

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Gebruiker bijwerken [eigenschappen met meerdere waarden]

##### <a name="request"></a><a name="request-4"></a>Aanvraag

*PATCH/users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>Response

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Gebruiker bijwerken [eigenschappen met één waarde]

##### <a name="request"></a><a name="request-5"></a>Aanvraag

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>Response

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>Gebruiker uitschakelen

##### <a name="request"></a><a name="request-14"></a>Aanvraag

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>Response

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Gebruiker verwijderen

##### <a name="request"></a><a name="request-6"></a>Aanvraag

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Response

*HTTP/1.1 204 No Content*

### <a name="group-operations"></a>Groepsbewerkingen

* Groepen moeten altijd worden gemaakt met een lege ledenlijst.
* Er kunnen query’s worden uitgevoerd op groepen met het kenmerk `displayName` .
* Het bijwerken van de PATCH-aanvraag van de groep moet een *HTTP 204 No Content* opleveren in het antwoord. Het is niet aan te raden om een instantie terug te sturen met een lijst van alle leden.
* Het is niet nodig om alle leden van de groep te retourneren.

#### <a name="create-group"></a>Groep maken

##### <a name="request"></a><a name="request-7"></a>Aanvraag

*POST /Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>Response

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Groep ophalen

##### <a name="request"></a><a name="request-8"></a>Aanvraag

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Response
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Groep ophalen met displayName

##### <a name="request"></a><a name="request-9"></a>Aanvraag
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Response

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Groep bijwerken [niet-leden kenmerken]

##### <a name="request"></a><a name="request-10"></a>Aanvraag

*PATCH/groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>Response

*HTTP/1.1 204 No Content*

### <a name="update-group-add-members"></a>Groep bijwerken [Leden toevoegen]

##### <a name="request"></a><a name="request-11"></a>Aanvraag

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>Response

*HTTP/1.1 204 No Content*

#### <a name="update-group-remove-members"></a>Groep bijwerken [Leden verwijderen]

##### <a name="request"></a><a name="request-12"></a>Aanvraag

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>Response

*HTTP/1.1 204 No Content*

#### <a name="delete-group"></a>Groep verwijderen

##### <a name="request"></a><a name="request-13"></a>Aanvraag

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Response

*HTTP/1.1 204 No Content*

### <a name="security-requirements"></a>Beveiligingsvereisten
**TLS Protocol Versions**

De enige acceptabele TLS-protocolversies zijn TLS 1.2 en TLS 1.3. Er zijn geen andere versies van TLS toegestaan. Er is geen SSL-versie toegestaan. 
- RSA-sleutels moeten ten minste 2048 bits zijn.
- ECC-sleutels moeten ten minste 256 bits zijn en worden gegenereerd met behulp van een goedgekeurde elliptische curve


**Sleutellengten**

Alle services moeten X.509-certificaten gebruiken die zijn gegenereerd met cryptografische sleutels van voldoende lengte, wat betekent:

**Coderingssuites**

Alle services moeten worden geconfigureerd voor het gebruik van de volgende coderingssuites in de exacte volgorde die hieronder is opgegeven. Houd er rekening mee dat als u alleen een RSA-certificaat hebt, de installatie van de ECDSA-coderingssuites geen effect heeft. </br>

Minimale staaf voor TLS 1.2-coderingssuites:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>IP-bereiken
De Azure AD-inrichtingsservice werkt momenteel met de IP-bereiken voor AzureActiveDirectory, zoals [hier](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all) wordt weergegeven. U kunt de IP-bereiken onder het label AzureActiveDirectory toevoegen om verkeer van de Azure AD-inrichtingsservice toe te staan in uw toepassing. Houd er rekening mee dat u de lijst met IP-bereiken zorgvuldig moet controleren op berekende adressen. Een adres zoals '40.126.25.32' kan worden weergegeven in de lijst met IP-bereiken als ' 40.126.0.0/18'. U kunt de lijst IP-bereik ook programmatisch ophalen met behulp van de volgende [API](/rest/api/virtualnetwork/servicetags/list).

## <a name="step-3-build-a-scim-endpoint"></a>Stap 3: Een SCIM-eindpunt bouwen

Nu u uw schema hebt ontworpen en de Azure AD SCIM-implementatie begrijpt, kunt u aan de slag met het ontwikkelen van uw SCIM-eindpunt. In plaats van helemaal opnieuw te beginnen en de implementatie volledig zelf te bouwen, kunt u gebruikmaken van de open source SCIM-bibliotheken die zijn gepubliceerd door de SCIM-community.

Zie [een voor beeld scim-eind punt ontwikkelen](use-scim-to-build-users-and-groups-endpoints.md)voor meer informatie over het bouwen van een scim-eind punt met voor beelden.

Het voor beeld van een open source .NET core- [referentie code](https://aka.ms/SCIMReferenceCode) gepubliceerd door het Azure AD-inrichtings team is een van de bronnen waarmee u uw ontwikkeling kunt starten. Nadat u het SCIM-eindpunt hebt gemaakt, moet u het testen. U kunt de verzameling [postman tests ](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) gebruiken die als onderdeel van de referentiecode worden gegeven, of door de voorbeeld aanvragen/antwoorden uit te voeren die [hierboven](#user-operations) worden genoemd.  

   > [!Note]
   > De referentiecode is bedoeld om u te helpen bij het bouwen van uw SCIM-eindpunt en wordt 'AS IS' aangeboden. Bijdragen van de community zijn welkom bij het bouwen en onderhouden van de code.

De oplossing bestaat uit twee projecten _Microsoft.SCIM_ en _Microsoft.SCIM.WebHostSample_.

Het _Microsoft.SCIM_-project is de bibliotheek waarin de onderdelen van de webservice worden gedefinieerd die voldoen aan de SCIM-specificatie. Het declareert de interface _Microsoft.SCIM.IProvider_, aanvragen worden omgezet in aanroepen naar de methoden van de provider, die vervolgens kunnen worden geprogrammeerd om te werken in een identiteitsopslag.

![Uitsplitsing: Een aanvraag die is omgezet in aanroepen naar de methoden van de provider](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

Het _Microsoft.SCIM.WebHostSample_-project is een Visual Studio ASP.NET Core-webtoepassing, op basis van een _leeg_ sjabloon. Hiermee kan de voorbeeldcode worden geïmplementeerd als standalone, gehost in containers of in Internet Information Services. Het implementeert ook de _Microsoft.SCIM.IProvider_-interface waarbij klassen in het geheugen worden bewaard als een voorbeeld van een identiteitsopslag.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Een aangepast SCIM-eindpunt bouwen

De SCIM-service moet een HTTP-adres en serververificatiecertificaat hebben waarvan de basiscertificeringsinstantie een van de volgende namen heeft:

* CNNIC
* Comodo
* Cyber Trust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign
* DST Root CA X3

Het .NET Core SDK bevat een HTTPS-ontwikkelingscertificaat dat kan worden gebruikt tijdens de ontwikkeling. Het certificaat wordt geïnstalleerd als onderdeel van de eerste sessie. Afhankelijk van hoe u de ASP.NET Core-webtoepassing uitvoert, luistert deze mogelijk naar een andere poort:

* Microsoft.SCIM.WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

Gebruik de volgende link voor meer informatie over HTTPS in ASP.NET Core: [HTTPS afdwingen in ASP.NET Core](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Verificatie van eindpunt afhandelen

Aanvragen van Azure Active Directory bevatten een OAuth 2.0 Bearer-token. Alle services die de aanvraag ontvangen, moeten de uitgever verifiëren als Azure Active Directory voor de verwachte Azure Active Directory-tenant.

In het token wordt de uitgever geïdentificeerd aan de hand van een ISS-claim, zoals `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`. In dit voorbeeld is het basisadres van de claimwaarde `https://sts.windows.net`, Azure Active Directory, als de uitgever geïdentificeerd, terwijl het relatief adressegment, _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_, een unieke id is van de Azure Active Directory Tenant waarvoor het token is uitgegeven.

Het doel van het token is de toepassingssjabloon-ID voor de toepassing in de galerie. Elk van de toepassingen die in één tenant zijn geregistreerd, kan dezelfde `iss`-claim ontvangen met SCIM-aanvragen. De toepassingssjabloon-ID voor alle aangepaste apps is _8adf8e6e-67b2-4cf2-A259-e3dc5476c621_. Het token dat is gegenereerd door de Azure AD-inrichtingsservice mag alleen worden gebruikt voor het testen. Het mag niet worden gebruikt in productie-omgevingen.

In de voorbeeldcode worden aanvragen geverifieerd met het pakket Microsoft.AspNetCore.Authentication.JwtBearer package. De volgende code dwingt af dat aanvragen voor een van de service-eindpunten worden geverifieerd met behulp van het Bearer-token dat is uitgegeven door Azure Active Directory voor een opgegeven tenant:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

Er is ook een Bearer-token vereist voor het gebruik van de verstrekte [postman tests](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) en om lokale foutopsporing uit te voeren met localhost. De voorbeeldcode maakt gebruik van ASP.NET Core-omgevingen om de verificatie-opties tijdens de ontwikkelingsfase te wijzigen en het gebruik van een zelfondertekend token in te schakelen.

Gebruik de volgende link voor meer informatie over meerdere omgevingen in ASP.NET Core: [Meerdere omgevingen gebruiken in ASP.NET Core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments) (Engelstalig)

De volgende code dwingt af dat aanvragen voor een van de service-eindpunten worden geverifieerd met behulp van een Bearer-token dat is ondertekend met een aangepaste sleutel:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

Verzend een GET-aanvraag naar de tokencontroller om een geldig Bearer-token op te halen. De methode _GenerateJSONWebToken_ is verantwoordelijk voor het maken van een token dat overeenkomt met de parameters die zijn geconfigureerd voor ontwikkeling:

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Inrichting en het ongedaan maken van de inrichting van gebruikers afhandelen

***Voorbeeld 1. Een query uitvoeren op de service voor een overeenkomende gebruiker**

Azure Active Directory vraagt de service om een gebruiker met een `externalId`-kenmerkwaarde die overeenkomt met de mailnickname-kenmerkwaarde van een gebruiker in Azure AD. De query wordt weer gegeven als een Hypertext Transfer Protocol (HTTP)-aanvraag, zoals dit voorbeeld, waarin jyoung een voorbeeld is van een mailnickname van een gebruiker in Azure Active Directory.

>[!NOTE]
> Dit is een voorbeeld. Niet alle gebruikers hebben een mailnickname-kenmerk en de waarde die een gebruiker heeft, is mogelijk niet uniek in de Directory. Het kenmerk dat wordt gebruikt voor het vergelijken (in dit geval `externalId`) kan ook worden geconfigureerd in de [Azure AD-kenmerktoewijzingen](customize-application-attributes.md).

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

In de voorbeeldcode wordt de aanvraag omgezet in een aanroep naar de QueryAsync-methode van de provider van de service. Dit is de handtekening van deze methode: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

In deze voorbeeldquery voor een gebruiker met een opgegeven waarde voor het kenmerk `externalId` zijn de waarden van de argumenten die zijn doorgegeven aan de methode QueryAsync:

_ parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***Voorbeeld 2. Een gebruiker inrichten**

Als het antwoord op een query van de webservice voor een gebruiker met een `externalId`-kenmerkwaarde die overeenkomt met de mailnickname-kenmerkwaarde van een gebruiker geen gebruikers retourneert, dan vraagt Azure Active Directory de service een gebruiker in te richten die overeenkomt met die in Azure Active Directory.  Hier volgt een voorbeeld van een dergelijke aanvraag: 

```
 POST https://.../scim/Users HTTP/1.1
 Authorization: Bearer ...
 Content-type: application/scim+json
 {
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

In de voorbeeldcode wordt de aanvraag omgezet in een aanroep naar de methode CreateAsync van de provider van de service. Dit is de handtekening van deze methode: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

In een aanvraag om een gebruiker in te richten, is de waarde van het resource-argument een instantie van de klasse Microsoft.SCIM.Core2EnterpriseUser, die is gedefinieerd in de bibliotheek Microsoft.SCIM.Schemas.  Als de aanvraag om de gebruiker in te richten slaagt, wordt de implementatie van de methode verwacht een instantie van de klasse Microsoft.SCIM.Core2EnterpriseUser te retourneren, waarbij de waarde van de eigenschap ID is ingesteld op de unieke id van de nieuwe ingerichte gebruiker.  

_*_Voorbeeld 3. Een query uitvoeren op de huidige status van een gebruiker_*_ 

Als u een gebruiker wilt bijwerken waarvan bekend is dat deze bestaat in een identiteitsopslag die wordt gebruikt door een SCIM, vraagt Azure Active Directory naar de huidige status van die gebruiker bij de service met een aanvraag zoals: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

In de voorbeeldcode wordt de aanvraag omgezet in een aanroep naar de methode RetrieveAsync van de provider van de service. Dit is de handtekening van deze methode: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

In deze voorbeeldaanvraag voor het ophalen van de huidige status van een gebruiker, zijn de waarden van de eigenschappen van het object die zijn opgegeven als de waarde van het argument van de parameters als volgt: 
  
_ Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Voorbeeld 4. Een query uitvoeren op de waarde van een verwijzingskenmerk dat moet worden bijgewerkt** 

Als een verwijzingskenmerk moet worden bijgewerkt, wordt door Azure Active Directory de service gevraagd om te bepalen of de huidige waarde van het referentiekenmerk in de identiteitsopslag die door de service wordt aangestuurd, al overeenkomt met de waarde van dat kenmerk in Azure Active Directory. Voor gebruikers is het enige kenmerk waarvan de huidige waarde op deze manier wordt opgevraagd het beheerderskenmerk. Hier volgt een voorbeeld van een aanvraag om te bepalen of het beheerderskenmerk van een gebruikersobject momenteel een bepaalde waarde heeft: In de voorbeeldcode wordt de aanvraag omgezet in een aanroep naar de QueryAsync-methode van de provider van de service. De waarde van de eigenschappen van het object die wordt opgegeven als de waarde van het argument parameters, is als volgt: 
  
_ parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: 'Id'
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): 'Id'
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Hier kan de waarde van de index x 0 zijn en de waarde van de index y kan 1 zijn, of de waarde van x kan 1 zijn, en de waarde van y kan 0 zijn, afhankelijk van de volgorde van de expressies van de filterqueryparameter.   

***Voorbeeld 5. Aanvraag van Azure AD naar een SCIM-service om een gebruiker bij te werken** 

Hier volgt een voorbeeld van een aanvraag van Azure Active Directory naar een SCIM-service om een gebruiker bij te werken: 

```
  PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
  Content-type: application/scim+json
  {
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

In de voorbeeldcode wordt de aanvraag omgezet in een aanroep naar de methode UpdateAsync van de provider van de service. Dit is de handtekening van deze methode: 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

In het voorbeeld van een aanvraag om een gebruiker bij te werken, heeft het object dat wordt gegeven als de waarde van het argument patch de volgende eigenschapswaarden: 
  
_ ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier:  "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

***Voorbeeld 6. De inrichting van een gebruiker ongedaan maken**

Voor het ongedaan maken van de inrichting van een gebruiker uit een identiteitsopslag voor een SCIM-service, verzendt Azure AD een aanvraag zoals:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

In de voorbeeldcode wordt de aanvraag omgezet in een aanroep naar de methode DeleteAsync van de provider van de service. Dit is de handtekening van deze methode: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

Het object dat als waarde voor het argument resourceIdentifier wordt gegeven, heeft de volgende eigenschapswaarden in het voorbeeld van een aanvraag om de inrichting van een gebruiker ongedaan te maken: 

_ ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Stap 4: Uw SCIM-eindpunt integreren met de Azure AD SCIM-client

Azure AD kan worden geconfigureerd voor het automatisch inrichten van toegewezen gebruikers en groepen voor toepassingen die een specifiek profiel van het [SCIM 2.0-protocol implementeren](https://tools.ietf.org/html/rfc7644). De details van het profiel zijn beschreven in [Stap 2: Begrijp de werking van de Azure AD SCIM-implementatie](#step-2-understand-the-azure-ad-scim-implementation).

Neem contact op met uw toepassingsprovider of de documentatie van uw toepassingsprovider voor instructies over de compatibiliteit met deze vereisten.

> [!IMPORTANT]
> De Azure AD SCIM-implementatie is gebaseerd op de Azure AD-inrichtingsservice voor gebruikers, die is ontworpen om gebruikers voortdurend te synchroniseren tussen Azure AD en de doeltoepassing, en een zeer specifieke set standaardbewerkingen uit te voeren. Het is belangrijk dat u dit gedrag begrijpt om inzicht te krijgen in het gedrag van de Azure AD SCIM-client. Zie voor meer informatie de sectie [Inrichtingscycli: De eerste en incrementele](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in [hoe werkt inrichten](how-provisioning-works.md).

### <a name="getting-started"></a>Aan de slag

Toepassingen die ondersteuning bieden voor het SCIM-profiel dat in dit artikel wordt beschreven, kunnen worden verbonden met Azure Active Directory met behulp van de functie 'niet-galerie toepassing' in de Azure AD-toepassingsgalerie. Zodra de verbinding tot stand is gebracht, voert Azure AD elke 40 minuten een synchronisatie uit waarbij het SCIM-eindpunt van de toepassing wordt opgevraagd voor toegewezen gebruikers en groepen, en worden ze gemaakt of gewijzigd op basis van de toewijzingsdetails.

**Verbinding maken met een toepassing die ondersteuning biedt voor SCIM:**

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com). U kunt toegang krijgen tot een gratis proefversie van Azure Active Directory met P2-licenties door u aan te melden voor het [ontwikkelaarsprogramma](https://developer.microsoft.com/office/dev-program)
2. Selecteer **Enterprise-toepassingen** in het linkerdeelvenster. Er wordt een lijst met alle geconfigureerde apps weergegeven, met inbegrip van apps die zijn toegevoegd vanuit de galerie.
3. Selecteer **+ nieuwe toepassing**  >  **+ Maak uw eigen toepassing**.
4. Voer een naam in voor uw toepassing, kies de optie *een andere toepassing integreren die u niet in de galerie vindt* en selecteer **toevoegen** om een app-object te maken. De nieuwe app wordt toegevoegd aan de lijst met bedrijfstoepassingen en wordt geopend op het scherm voor het beheren van apps.

   ![Scherm opname toont de Azure AD-toepassings galerie ](media/use-scim-to-provision-users-and-groups/scim-figure-2b-1.png)
    *Azure AD-toepassings galerie*

   > [!NOTE]
   > Als u de oude app Gallery-ervaring gebruikt, volgt u de onderstaande scherm gids.
   
   ![Scherm afbeelding toont de Azure AD-galerie met oude apps die de Azure AD-ervaring voor ](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)
    *oude apps Gallery* heeft

5. Selecteer in het scherm voor het beheren van apps **Inrichting** in het linker deelvenster.
6. Selecteer **Automatisch** in het menu **Inrichtingsmodus**.

   ![Voorbeeld: De inrichtingspagina van een app in de Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Inrichting configureren in de Azure Portal*

7. Voer in het veld **Tenant-URL** de URL in van het SCIM-eindpunt van de toepassing. Voorbeeld: `https://api.contoso.com/scim/`
8. Als het SCIM-eindpunt een OAuth Bearer-token van een andere uitgever dan Azure AD vereist, kopieert u het vereiste OAuth Bearer-token naar het optionele veld **Geheime token**. Als dit veld leeg blijft, voegt Azure AD aan elke aanvraag een OAuth Bearer-token toe dat is uitgegeven door Azure AD. Apps die gebruikmaken van Azure AD als id-provider kunnen dit door Azure AD uitgegeven token valideren. 
   > [!NOTE]
   > Het wordt **_niet_* aanbevolen om dit veld leeg te laten en te vertrouwen op een token dat door Azure AD wordt gegenereerd. Deze optie is voornamelijk beschikbaar voor testdoeleinden.
9. Selecteer *Verbinding testen** om Azure Active Directory verbinding te laten maken met het SCIM-eindpunt. Als de poging mislukt, wordt er informatie over de fout weergegeven.  

    > [!NOTE]
    > **Verbinding testen** voert een query uit op het SCIM-eindpunt voor een gebruiker die niet bestaat, met behulp van een willekeurige GUID als de overeenkomende eigenschap die is geselecteerd in de Azure AD-configuratie. Het verwachte juiste antwoord is HTTP 200 OK met een leeg SCIM ListResponse-bericht.

10. Als de pogingen om verbinding te maken met de toepassing slagen, selecteert u **Opslaan** om de beheerdersreferenties op te slaan.
11. In de sectie **Toewijzingen** zijn er twee sets [kenmerktoewijzingen](customize-application-attributes.md): een voor gebruikersobjecten en één voor groepsobjecten. Selecteer ze allebei om de kenmerken te controleren die zijn gesynchroniseerd van Azure Active Directory naar uw app. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om de gebruikers en groepen in uw app te vinden voor updatebewerkingen. Selecteer **Opslaan** om eventuele wijzigingen toe te passen.

    > [!NOTE]
    > U kunt de synchronisatie van groepsobjecten eventueel uitschakelen door de toewijzing van groepen uit te schakelen.

12. Onder **Instellingen** wordt in het veld **Bereik** gedefinieerd welke gebruikers en groepen worden gesynchroniseerd. Selecteer **Alleen toegewezen gebruikers en groepen** (aanbevolen) om gebruikers en groepen te synchroniseren die zijn toegewezen op het tabblad **Gebruikers en groepen**.
13. Nadat de configuratie is voltooid, stelt u de **Inrichtingsstatus** in op **Aan**.
14. Selecteer **Opslaan** om de Azure AD-inrichtingsservice te starten.
15. Als u alleen toegewezen gebruikers en groepen (aanbevolen) wilt synchroniseren, selecteert u het tabblad **Gebruikers en groepen** en wijst u de gebruikers of groepen toe die u wilt synchroniseren.

Zodra de eerste cyclus is gestart, kunt u **Inrichtingslogboeken** selecteren in het linker deelvenster om de voortgang te bewaken, hierin worden alle acties weergegeven die door de inrichtingsservice in uw app worden uitgevoerd. Zie [Rapportage over automatische inrichting van gebruikersaccounts](check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

> [!NOTE]
> De initiële cyclus duurt langer dan toekomstige synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de service wordt uitgevoerd.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Stap 5: Uw toepassing publiceren in de Azure AD-toepassingsgalerie

Als u een toepassing bouwt die wordt gebruikt door meer dan één tenant, kunt u deze beschikbaar stellen in de Azure AD-toepassingsgalerie. Dit maakt het eenvoudig voor organisaties om de toepassing te vinden en inrichtingen te configureren. Het publiceren van uw app in de Azure AD-galerie en het beschikbaar stellen van de inrichting voor anderen is eenvoudig. Bekijk de stappen [hier](../develop/v2-howto-app-gallery-listing.md) Microsoft werkt samen met u om uw toepassing te integreren in onze galerie, uw eindpunt te testen en onboarding-[documentatie](../saas-apps/tutorial-list.md) beschikbaar te stellen voor klanten.

### <a name="gallery-onboarding-checklist"></a>Controlelijst voor onboarding van galerie
Gebruik de onderstaande controlelijst om ervoor te zorgen dat de onboarding van uw toepassing snel verloopt en klanten een prettige implementatie-ervaring hebben. De informatie wordt van u verzameld bij de onboarding in de galerie. 
> [!div class="checklist"]
> * Ondersteuning voor een [SCIM 2.0 ](#step-2-understand-the-azure-ad-scim-implementation) gebruikers- en groepseindpunt (er is slechts één vereist, maar beide worden aanbevolen)
> * Ondersteuning voor ten minste 25 aanvragen per seconde per tenant om ervoor te zorgen dat het inrichten en ongedaan maken van de inrichting van gebruikers en groepen zonder vertraging kan worden uitgevoerd (vereist)
> * Technische hulp en ondersteuning om klanten te begeleiden na onboarding in de galerie (vereist)
> * 3 niet-verlopende test-aanmeldingsgegevens voor uw toepassing (vereist)
> * Ondersteuning voor het verlenen van de OAuth-autorisatiecode of een token met lange levensduur zoals hieronder wordt beschreven (vereist)
> * Technische hulp en ondersteuning om klanten te ondersteunen na onboarding in de galerie (vereist)
> * Ondersteuning voor het bijwerken van meerdere groepslidmaatschappen met één PATCH (aanbevolen) 
> * Openbare documentatie van uw SCIM-eindpunt (aanbevolen) 
> * [Ondersteuning voor schemadetectie](https://tools.ietf.org/html/rfc7643#section-6) (aanbevolen)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorisatie voor het inrichten van connectors in de toepassingsgalerie
De SCIM spec definieert geen SCIM-specifiek schema voor verificatie en autorisatie. Het is gebaseerd op het gebruik van bestaande industriestandaarden. De Azure AD-inrichtingsclient ondersteunt twee autorisatiemethoden voor toepassingen in de galerie. 

|Verificatiemethode|Voordelen|Nadelen|Ondersteuning|
|--|--|--|--|
|Gebruikersnaam en -wachtwoord (niet aanbevolen of niet ondersteund door Azure AD)|Eenvoudig te implementeren|Onveilig: [Your Pa$$word doesn't matter](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Per geval ondersteund voor galerie-apps. Niet ondersteund voor niet-galerie-apps.|
|Bearer-token met lange levensduur|Voor tokens met een lange levensduur hoeft geen gebruiker aanwezig te zijn. Ze kunnen eenvoudig worden gebruikt bij het instellen van de inrichting.|Tokens met een lange levensduur kunnen moeilijk worden gedeeld met een beheerder zonder gebruik te maken van onveilige methoden zoals e-mail. |Ondersteund voor galerie- en niet-galerie-apps. |
|OAuth-autorisatiecode verlenen|Toegangstokens hebben een veel kortere levensduur dan wachtwoorden en beschikken over een mechanisme voor automatisch vernieuwen die de Bearer-tokens met langere levensduur niet hebben.  Er moet een echte gebruiker aanwezig zijn tijdens de eerste autorisatie, wat een bepaald niveau van verantwoordelijkheid toevoegt |Hiervoor moet een gebruiker aanwezig zijn. Als de gebruiker de organisatie verlaat, wordt het token ongeldig en moet de autorisatie opnieuw worden uitgevoerd.|Ondersteund voor galerie-apps, maar niet voor niet-galerie-apps. U kunt echter een toegangstoken in de gebruikersinterface opgeven als geheim token voor testdoeleinden op korte termijn. Ondersteuning voor OAuth-code toekenning op niet-galerie is in onze achterstand en biedt naast ondersteuning voor Configureer bare verificatie/token-Url's in de galerie-app.|
|Referenties voor OAuth-client verlenen|Toegangstokens hebben een veel kortere levensduur dan wachtwoorden en beschikken over een mechanisme voor automatisch vernieuwen die de Bearer-tokens met langere levensduur niet hebben. Zowel bij het verlenen van de autorisatiecode als de clientaanmeldingsgegevens wordt hetzelfde type toegangstoken gebruikt, dus het schakelen tussen deze methoden is transparant voor de API.  Het inrichten kan volledig worden geautomatiseerd en nieuwe tokens kunnen zonder tussenkomst van de gebruiker worden aangevraagd. ||Niet ondersteund voor apps uit de galerie en niet-galerie-apps. Ondersteuning in onze achterstand.|

> [!NOTE]
> Het is niet raadzaam om het tokenveld leeg te laten in de aangepaste app-gebruikersinterface van de Azure AD-inrichtingsconfiguratie. Het gegenereerde token is voornamelijk bedoeld voor testdoeleinden.

**Stroom voor het verlenen van OAuth-autorisatiecode:** De inrichtingsservice ondersteunt het verlenen van de [autorisatiecode](https://tools.ietf.org/html/rfc6749#page-24). Na het verzenden van uw aanvraag voor het publiceren van uw app in de galerie, zal ons team met u samen werken om de volgende informatie te verzamelen:
*  Autorisatie-URL: Een URL van de client om autorisatie te verkrijgen van de resource-eigenaar door middel van omleiding van de gebruikersagent. De gebruiker wordt omgeleid naar deze URL om de toegang te autoriseren. Houd er rekening mee dat deze URL momenteel niet per tenant kan worden geconfigureerd.
*  URL voor tokenuitwisseling: Een URL door de client voor het uitwisselen van een autorisatiemachtiging voor een toegangstoken, meestal met clientverificatie. Houd er rekening mee dat deze URL momenteel niet per tenant kan worden geconfigureerd.
*  Client-id: De autorisatieserver geeft de geregistreerde client een client-id. Dit is een unieke tekenreeks voor de registratiegegevens die door de client worden verstrekt.  De client-id is geen geheim. Het is zichtbaar voor de resource-eigenaar en **mag niet** alleen worden gebruikt voor clientverificatie.  
*  Clientgeheim: Het clientgeheim is een geheim dat door de autorisatieserver wordt gegenereerd. Dit moet een unieke waarde zijn die alleen bekend is bij de autorisatieserver. 

OAuth v1 wordt niet ondersteund vanwege de blootstelling van het clientgeheim. OAuth v2 wordt ondersteund.  

Best practices (aanbevolen maar niet vereist):
* Ondersteuning voor meerdere omleidings-URL's. Beheerders kunnen het inrichten configureren via 'portal.azure.com' en 'aad.portal.azure.com'. Door meerdere omleidings-Url's te ondersteunen zorgt u ervoor dat gebruikers toegang kunnen verlenen vanuit beide portals.
* Ondersteuning van meerdere geheimen voor eenvoudige geheimvernieuwing, zonder downtime. 

Stappen van de stroom voor het verlenen van de OAuth-code:
1. Gebruiker meldt zich aan bij de Azure Portal > Enterprise-toepassingen > Toepassing selecteren > Inrichting > klik autoriseren.
2. Azure Portal stuurt de gebruiker door naar de autorisatie-URL (aanmeldingspagina voor de app van derden).
3. Beheerder verstrekt aanmeldingsgegevens voor de toepassing van derden. 
4. De app van derden stuurt de gebruiker terug naar Azure Portal en verstrekt de toekenningscode 
5. De Azure AD-inrichtingsservice roept de token-URL aan en verstrekt de toekenningscode. De toepassing van derden reageert met het toegangstoken, het vernieuwingstoken en de vervaldatum
6. Wanneer de inrichtingscyclus begint, controleert de service of het huidige toegangstoken geldig is en wisselt deze zo nodig voor een nieuw token. Het toegangstoken wordt verstrekt in elke aanvraag die aan de app wordt gedaan en de geldigheid van de aanvraag wordt voor elke aanvraag gecontroleerd.

> [!NOTE]
> Het is momenteel niet mogelijk om OAuth in te stellen op een niet-galerie toepassing, maar u kunt handmatig een toegangstoken genereren op basis van uw autorisatieserver en deze invoeren in het geheime tokenveld van de niet-galerie toepassing. Hiermee kunt u de compatibiliteit van uw SCIM-server controleren met de Azure AD SCIM-client voordat u de app-galerie uitschakelt, die ondersteuning biedt voor de OAuth-codetoekenning.  

**OAuth Bearer-tokens met lange levensduur:** Als uw toepassing geen ondersteuning biedt voor de overdrachtsstroom van de OAuth-autorisatiecode, kunt u ook een OAuth Bearer-token met lange levensduur genereren die een beheerder vervolgens kan gebruiken om de inrichtingsintegratie in te stellen. Het token moet permanent zijn, anders wordt de inrichtingstaak [in quarantaine geplaatst](application-provisioning-quarantine-status.md) wanneer het token verloopt.

Voor aanvullende verificatie- en autorisatie methoden, kunt u ons benaderen via [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Controlelijst voor het lanceren in de galerie
We raden u aan om uw bestaande documentatie bij te werken en de integratie in uw marketingkanalen te versterken om het bewustzijn en de vraag van onze gezamenlijke integratie te stimuleren.  Hieronder vindt u een aantal controle-activiteiten die u kunt uitvoeren om de lancering te ondersteunen

* **Gereedheid voor verkoop- en klantondersteuning.** Zorg ervoor dat uw verkoop- en ondersteuningsteams op de hoogte zijn van en dat ze kunnen praten over de integratiemogelijkheden. Geef uw verkoop- en ondersteuningsteam een korte beschrijving en voeg de integratie toe aan uw verkoopmaterialen. 
* **Blogbericht en/of persbericht.** Maak een blogbericht of persbericht met een beschrijving van de gezamenlijke integratie, de voordelen en hoe deze kan worden gebruikt. [Voorbeeld: Persbericht over Imprivata en Azure Active Directory](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Sociale media.** Maak gebruik van uw sociale mediakanalen, zoals Twitter, Facebook of LinkedIn, om de integratie bij uw klanten te introduceren. Zorg ervoor dat @AzureAD wordt toegevoegd, zodat we uw bericht kunnen delen. [Voorbeeld: Imprivata Twitter-bericht](https://twitter.com/azuread/status/1123964502909779968)
* **Marketingwebsite.** Maak of werk marketingpagina's bij (bijvoorbeeld integratiepagina, partnerpagina, prijspagina enz.) om de gezamenlijke integratie op te nemen. [Voorbeeld: Pingboard-integratiepagina](https://pingboard.com/org-chart-for), [Smart Sheet-integratiepagina](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [Monday.com-prijspagina](https://monday.com/pricing/) 
* **Technische documentatie.** Maak een Help Center-artikel of technische documentatie over hoe klanten aan de slag kunnen. [Voorbeeld: Envoy + Microsft Azure Active Directory-integratie.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Communicatie met klant.** Breng klanten van de nieuwe integratie op de hoogte via uw klantcommunicatiekanalen (maandelijkse nieuwsbrieven, e-mailcampagnes, opmerkingen bij de release van het product). 

## <a name="related-articles"></a>Verwante artikelen:

* [Een voor beeld van een SCIM-eind punt ontwikkelen](use-scim-to-build-users-and-groups-endpoints.md)
* [Automatisch gebruikers voor SaaS-apps inrichten en de inrichting ongedaan maken](user-provisioning.md)
* [Kenmerktoewijzingen aanpassen voor het inrichten van gebruikers](customize-application-attributes.md)
* [Expressies schrijven voor kenmerktoewijzingen](functions-for-customizing-application-data.md)
* [Bereikfilters voor het inrichten van gebruikers](define-conditional-rules-for-provisioning-user-accounts.md)
* [Meldingen over accountinrichting](user-provisioning.md)
* [Lijst met handleidingen voor het integreren van SaaS-apps](../saas-apps/tutorial-list.md)

