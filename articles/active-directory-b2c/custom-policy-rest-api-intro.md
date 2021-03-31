---
title: REST API claim uitwisselingen in B2C aangepast beleid
titleSuffix: Azure AD B2C
description: Een inleiding tot het maken van een Azure AD B2C gebruikers traject dat communiceert met de REST-services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc2b72779460c2b7e3999204ace50ca57388b9a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89594183"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>REST API claim uitwisselingen integreren in uw aangepaste beleid voor Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Het Framework voor identiteits ervaring, dat Azure Active Directory B2C (Azure AD B2C), kan worden geïntegreerd met behulp van de REST Api's binnen een gebruikers traject. In dit artikel wordt beschreven hoe u een gebruikers traject maakt die samenwerkt met een REST-service met behulp van een aanstaand [technisch profiel](restful-technical-profile.md).

Met Azure AD B2C kunt u uw eigen bedrijfs logica toevoegen aan een gebruikers reis door uw eigen REST-service aan te roepen. Het Framework voor identiteits ervaring kan gegevens verzenden naar en ontvangen van uw REST-service om claims uit te wisselen. U kunt bijvoorbeeld:

- **Gebruikers invoer gegevens valideren**. U kunt bijvoorbeeld controleren of het e-mail adres dat is opgegeven door de gebruiker bestaat in de data base van uw klant en als dat niet het geval is, een fout melding geven.
- **Claims verwerken**. Als een gebruiker zijn of haar voor naam in alle kleine letters of in alle letters typt, kan de REST API de naam met alleen de eerste letter indelen en retour neren naar Azure AD B2C.
- **Verrijkende gebruikers gegevens door verder te integreren met zakelijke line-of-business-toepassingen**. De REST-service kan het e-mail adres van de gebruiker ontvangen, query's uitvoeren op de data base van de klant en het loyaliteits nummer van de gebruiker voor Azure AD B2C retour neren. Vervolgens kunnen retour claims worden opgeslagen in het Azure AD-account van de gebruiker, geëvalueerd in de volgende Orchestration-stappen of opgenomen in het toegangs token.
- **Aangepaste bedrijfs logica uitvoeren**. U kunt push meldingen verzenden, zakelijke data bases bijwerken, een gebruikers migratie proces uitvoeren, machtigingen beheren, data bases controleren en andere werk stromen uitvoeren.

![Diagram van een REST service claim uitwisseling](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

> [!NOTE]
> Als er een langzame of geen reactie van de REST-service naar Azure AD B2C is, is de time-out 30 seconden en is het aantal nieuwe pogingen twee keer (wat betekent dat er drie pogingen in totaal zijn). De instellingen voor de time-out en het aantal pogingen kunnen momenteel niet worden geconfigureerd.

## <a name="calling-a-restful-service"></a>Een REST-service aanroepen

De interactie bevat een claim uitwisseling van informatie tussen de REST API claims en Azure AD B2C. U kunt op de volgende manieren de integratie met de REST-services ontwerpen:

- **Validatie technische profiel**. Het aanroepen van de REST-service vindt plaats binnen een [validatie technische profiel](validation-technical-profile.md) van het opgegeven [zelfondertekende technische profiel](self-asserted-technical-profile.md)of een [besturings element voor controle weergave](display-control-verification.md) van een [Weergave besturings element](display-controls.md). Het validatie-technische profiel valideert de door de gebruiker verschafte gegevens voordat de reis van de gebruiker vooruit gaat. Met het technische profiel voor validatie kunt u het volgende doen:

  - Claims naar uw REST API verzenden.
  - Claims valideren en aangepaste fout berichten genereren die worden weer gegeven aan de gebruiker.
  - Teruggestuurde claims van de REST API naar volgende Orchestration-stappen.

- **Claim uitwisseling**. Een directe claim uitwisseling kan worden geconfigureerd door rechtstreeks vanuit een Orchestration-stap van een [gebruikers traject](userjourneys.md)een rest API technisch profiel aan te roepen. Deze definitie is beperkt tot:

  - Claims naar uw REST API verzenden.
  - Claims valideren en aangepaste fout berichten genereren die worden teruggestuurd naar de toepassing.
  - Teruggestuurde claims van de REST API naar volgende Orchestration-stappen.

U kunt aan elke stap in de gebruikersbeleving die is gedefinieerd door een aangepast beleid, een aanroep van een REST-API toevoegen. U kunt bijvoorbeeld een REST-API aanroepen:

- Tijdens het aanmelden, net voordat Azure AD B2C de referenties valideert.
- Direct na het aanmelden.
- Voordat Azure AD B2C een nieuw account in de Directory maakt.
- Nadat Azure AD B2C een nieuw account in de map hebt gemaakt.
- Voordat Azure AD B2C een toegangs token uitgeeft.

![Validatie van technische profiel verzameling](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Gegevens verzenden

In het [vervolg technische profiel](restful-technical-profile.md) `InputClaims` bevat het element een lijst met claims die naar uw rest-service moeten worden verzonden. U kunt de naam van uw claim toewijzen aan de naam die is gedefinieerd in de REST-service, een standaard waarde instellen en [claim resolvers](claim-resolver-overview.md)gebruiken.

U kunt configureren hoe de invoer claims worden verzonden naar de claim provider voor de REST met behulp van het kenmerk SendClaimsIn. De mogelijke waarden zijn:

- **Hoofd tekst**, verzonden in de tekst van de HTTP POST-aanvraag in JSON-indeling.
- **Formulier**, verzonden in de hoofd tekst van de HTTP POST-aanvraag in een ampersand ' & ' gescheiden sleutel waarde-indeling.
- **Header**, verzonden in de header HTTP GET-aanvraag.
- **Query string, verzonden** in de HTTP GET-aanvraag teken reeks.

Wanneer de optie voor de **hoofd tekst** is geconfigureerd, kunt u met het technische profiel van rest API een complexe JSON-nettolading verzenden naar een eind punt. Zie [een JSON-Payload verzenden](restful-technical-profile.md#send-a-json-payload)voor meer informatie.

## <a name="receiving-data"></a>Gegevens ontvangen

Het `OutputClaims` element van het [onderhouds technische profiel](restful-technical-profile.md) bevat een lijst met claims die zijn geretourneerd door de rest API. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in de REST API. U kunt ook claims toevoegen die niet worden geretourneerd door de REST API ID-provider, zolang u het kenmerk DefaultValue hebt ingesteld.

De uitvoer claims die worden geparseerd door de claim provider voor de REST, verwachten altijd een platte JSON-hoofd tekst te parseren, zoals:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

De uitvoer claims moeten er als volgt uitzien:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Als u een geneste JSON-hoofd tekst wilt parseren, stelt u de ResolveJsonPathsInJsonTokens-meta gegevens in op True. Stel in de uitvoer claim de PartnerClaimType in op het JSON-padcomponent-element dat u wilt uitvoeren.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


De uitvoer claims moeten er als volgt uitzien:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Beveiligingsoverwegingen

U moet uw REST API-eind punt beveiligen zodat alleen geverifieerde clients ermee kunnen communiceren. Het REST API moet een HTTPS-eind punt gebruiken. Stel de AuthenticationType-meta gegevens in op een van de volgende verificatie methoden:

- **Client certificaat** beperkt de toegang door gebruik te maken van verificatie op basis van client certificaten. Alleen services die over de juiste certificaten beschikken, hebben toegang tot uw API. U slaat het client certificaat op in een Azure AD B2C-beleids sleutel. Meer informatie over hoe u [uw rest-service kunt beveiligen met behulp van client certificaten](secure-rest-api.md#https-client-certificate-authentication).
- **Basic** beveiligt de rest API met HTTP Basic-verificatie. Alleen geverifieerde gebruikers, met inbegrip van Azure AD B2C, hebben toegang tot uw API. De gebruikers naam en het wacht woord worden opgeslagen in Azure AD B2C-beleids sleutels. Meer informatie over het [beveiligen van uw rest-Services met behulp van HTTP-basis verificatie](secure-rest-api.md#http-basic-authentication).
- **Bearer** beperkt de toegang met behulp van een OAuth2-toegangs token van de client. Het toegangs token wordt opgeslagen in een Azure AD B2C-beleids sleutel. Meer informatie over hoe u [uw rest-service kunt beveiligen met behulp van Bearer-tokens](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>REST API platform
Uw REST API kan worden gebaseerd op elk platform en worden geschreven in elke programmeer taal, zolang deze veilig is en claims kan verzenden en ontvangen zoals is opgegeven in een onderliggend [technisch profiel](restful-technical-profile.md).

## <a name="localize-the-rest-api"></a>De REST API lokaliseren
In een onderliggend technisch profiel wilt u mogelijk de taal/land instelling van de huidige sessie verzenden en zo nodig een gelokaliseerd fout bericht genereren. Met de [claim resolver](claim-resolver-overview.md)kunt u een contextuele claim verzenden, zoals de gebruikers taal. In het volgende voor beeld ziet u een onderliggend technisch profiel met een demonstratie van dit scenario.

```xml
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Fout berichten verwerken

Uw REST API moet mogelijk een fout bericht retour neren, zoals ' de gebruiker is niet gevonden in het CRM-systeem '. Als er een fout optreedt, moet de REST API een HTTP 409-fout bericht retour neren (status code voor de conflict reactie). Zie het [rest technische profiel](restful-technical-profile.md#returning-validation-error-message)voor meer informatie.

Dit kan alleen worden bereikt door een REST API technisch profiel aan te roepen vanuit een validatie technische profiel. Hiermee kan de gebruiker de gegevens op de pagina corrigeren en de validatie opnieuw uitvoeren bij het verzenden van de pagina.

Een HTTP 409-antwoord is vereist om te voor komen dat volgende validatie technische profielen binnen deze Orchestration-stap worden verwerkt.

Als u rechtstreeks naar een REST API technisch profiel verwijst vanuit een gebruikers traject, wordt de gebruiker omgeleid naar de Relying Party toepassing met het relevante fout bericht.

## <a name="publishing-your-rest-api"></a>Uw REST API publiceren

De aanvraag voor uw REST API-service is afkomstig van Azure AD B2C-servers. De REST API-service moet worden gepubliceerd op een openbaar toegankelijk HTTPS-eind punt. De REST API-aanroepen worden ontvangen van een Azure Data Center-IP-adres.

Ontwerp uw REST API-service en de onderliggende onderdelen (zoals de data base en het bestands systeem) die Maxi maal beschikbaar zijn.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor voor beelden van het gebruik van een reactief technisch profiel:

- [Walkthrough: REST API claims-uitwisselingen integreren in uw Azure AD B2C gebruikers door voeren als validatie van gebruikers invoer](custom-policy-rest-api-claims-validation.md)
- [Walkthrough: REST API claims-uitwisselingen toevoegen aan aangepaste beleids regels in Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Uw REST API Services beveiligen](secure-rest-api.md)
- [Referentie: technisch profiel (REST)](restful-technical-profile.md)
