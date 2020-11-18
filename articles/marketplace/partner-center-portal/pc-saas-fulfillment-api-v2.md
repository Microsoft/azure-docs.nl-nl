---
title: SaaS-fulfillment-Api's v2 in micro soft Commercial Marketplace
description: Meer informatie over het maken en beheren van een SaaS-aanbieding op Microsoft AppSource en Azure Marketplace met behulp van de fulfillment Api's versie 2.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: c2679be2ca1db9017cbc37219402fa4e1c0666a5
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874420"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>SaaS-fulfillment-Api's versie 2 in de commerciële Marketplace

In dit artikel vindt u meer informatie over de Api's waarmee partners hun SaaS-aanbiedingen kunnen verkopen in Microsoft AppSource en Azure Marketplace. Er is een uitgever vereist voor het implementeren van integratie met deze Api's voor het publiceren van een transactable SaaS-aanbieding in het partner centrum.

## <a name="managing-the-saas-subscription-life-cycle"></a>De levens cyclus van het SaaS-abonnement beheren

De commerciële Marketplace beheert de volledige levens cyclus van een SaaS-abonnement na aankoop door de eind klant.  Het maakt gebruik van de landings pagina, de uitvoering van Api's, Operations Api's en de webhook als mechanisme om de daad werkelijke activering van SaaS-abonnementen en het gebruik, de updates en de annulering van het abonnement te best uren.  De factuur van de eind klant is gebaseerd op de status van de SaaS-abonnement die micro soft onderhoudt. 

### <a name="states-of-a-saas-subscription"></a>Statussen van een SaaS-abonnement

De statussen van een SaaS-abonnement en de toepasselijke acties worden weer gegeven.

![Levens cyclus van een SaaS-abonnement in Marketplace](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Gekocht maar nog niet geactiveerd (*PendingFulfillmentStart*)

Nadat een eind klant (of CSP) een SaaS-aanbieding heeft gekocht op Marketplace, moet de uitgever op de hoogte worden gesteld van de aankoop zodat een nieuw SaaS-account wordt gemaakt en geconfigureerd voor de eind klant aan de kant van de uitgever.

Voor het maken van accounts gebeurt het volgende:

1. De klant moet op de knop **configureren** klikken die beschikbaar is voor een SaaS-aanbieding na een succes volle aankoop in Microsoft AppSource of Azure Portal. Of in het e-mail bericht dat de klant kort na de aankoop zal ontvangen.
2. Vervolgens waarschuwt micro soft de partner over de aankoop door te openen op het tabblad nieuwe browser de URL van de landings pagina met de para meter token (het aankoop identificatie token voor commerciële markt plaatsen).

Een voor beeld van een dergelijke aanroep is `https://contoso.com/signup?token=<blob>` , terwijl de URL van de landings pagina voor deze SaaS-aanbieding in het partner centrum is geconfigureerd als `https://contoso.com/signup` . Dit token biedt de uitgever een ID die de SaaS-aankoop en de klant op unieke wijze identificeert.

>[!NOTE]
>De uitgever krijgt geen melding van de SaaS-aankoop totdat de klant het configuratie proces van micro soft initieert.

De URL van de landings pagina moet 24 uur duren en u kunt op elk moment nieuwe oproepen van micro soft ontvangen. Als de landings pagina niet beschikbaar is, kunnen klanten zich niet aanmelden voor de SaaS-service en deze gebruiken.

Vervolgens moet het *token* worden teruggestuurd naar micro soft via de uitgever door de [API voor SaaS-conflicten](#resolve-a-purchased-subscription)aan te roepen als de waarde van de `x-ms-marketplace-token header` para meter header.  Als gevolg van de API-aanroep omzetten, wordt het token uitgewisseld voor de details van de SaaS-aankoop, zoals de unieke ID van de aankoop, de gekochte aanbiedings-ID, het gekochte abonnement-id, enzovoort.

Op de landings pagina moet de klant zijn aangemeld bij het nieuwe of bestaande SaaS-account via Azure Active Directory (SSO) voor eenmalige aanmelding (Azure AD).

De uitgever moet SSO-aanmelding implementeren om de gebruikers ervaring te bieden die micro soft voor deze stroom vereist.  Zorg ervoor dat u de Azure AD-toepassing met meerdere tenants gebruikt, zowel werk-als school accounts of persoonlijke micro soft-accounts wilt toestaan bij het configureren van de SSO.  Deze vereiste is alleen van toepassing op de landings pagina en voor gebruikers die worden omgeleid naar de SaaS-service wanneer ze al zijn aangemeld met micro soft-referenties. Het is niet van toepassing op alle aanmeldingen bij de SaaS-service.

> [!NOTE]
>Als een SSO-aanmelding vereist dat een beheerder machtigingen voor een app toewijst, moet de beschrijving van de aanbieding in het partner centrum vermelden dat toegang op beheerders niveau is vereist. Dit is om te voldoen aan het [beleid voor commerciële Marketplace-certificering](/legal/marketplace/certification-policies#10003-authentication-options).

Zodra u bent aangemeld, moet de klant de SaaS-configuratie aan de kant van de uitgever volt ooien. De uitgever moet vervolgens de [API Activate](#activate-a-subscription) van het abonnement aanroepen om een signaal naar Marketplace te verzenden dat het inrichten van het SaaS-account is voltooid.
Hiermee wordt de facturerings cyclus van de klant gestart. Als het activeren van de API-aanroep van het abonnement is mislukt, wordt de klant niet gefactureerd voor de aankoop.


![API-aanroepen voor een inrichtings scenario](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Actief (geabonneerd)

Deze status is de stabiele status van een ingerichte SaaS-abonnement. Zodra de [API](#activate-a-subscription) -aanroep voor het activeren van een abonnement op micro soft wordt verwerkt, wordt het SaaS-abonnement gemarkeerd als geabonneerd. De SaaS-service is nu klaar om te worden gebruikt door de klant op de kant van de uitgever en de klant wordt gefactureerd.

Wanneer het SaaS-abonnement al actief is en de klant kiest voor het starten van SaaS-ervaring vanuit het Azure Portal-of M365 **-beheer centrum** , wordt de URL van de **landings pagina** opnieuw aangeroepen door micro soft met de *token* parameter, net als in de stroom activeren.  De uitgever moet onderscheid maken tussen nieuwe aankopen en het beheer van bestaande SaaS-accounts en de URL-aanroep van deze landings pagina afhandelen dienovereenkomstig.

#### <a name="being-updated-subscribed"></a>Worden bijgewerkt (geabonneerd)

Deze actie houdt in dat een update van een bestaand actief SaaS-abonnement wordt verwerkt door zowel micro soft als de uitgever. Een dergelijke update kan worden gestart door:

- de klant van de commerciële Marketplace.
- de CSP van de commerciële Marketplace.
- de klant van de SaaS-site van de uitgever (is niet van toepassing op de CSP die aankopen heeft gedaan).

Er zijn twee soorten updates beschikbaar voor een SaaS-abonnement:

- Plan een update uit wanneer de klant een ander abonnement kiest.
- Hoeveelheid bijwerken wanneer de klant het aantal aangeschafte seats voor het abonnement wijzigt

Alleen een actief abonnement kan worden bijgewerkt. Terwijl het abonnement wordt bijgewerkt, blijft de status actief aan de kant van micro soft.

##### <a name="update-initiated-from-the-marketplace"></a>Update geïnitieerd vanuit Marketplace

In deze stroom wijzigt de klant het abonnement of de hoeveelheid stoelen van het Azure Portal of het M365-beheer centrum.  

1. Zodra een update is ingevoerd, roept micro soft de webhook-URL van de uitgever aan, die is geconfigureerd in het veld voor de **verbindings webhook** in het partner centrum, met een geschikte waarde voor *actie* en andere relevante para meters.  
1. De uitgever moet de vereiste wijzigingen aanbrengen in de SaaS-service en micro soft waarschuwen wanneer de wijziging is voltooid door de [update status van de bewerkings-API aan](#update-the-status-of-an-operation)te roepen.
1. Als de patch wordt verzonden met de status mislukt, wordt het update proces niet voltooid aan de kant van micro soft.  Het SaaS-abonnement blijft bestaan met het bestaande abonnement en de hoeveelheid stoelen.

> [!NOTE]
> De uitgever moet een PATCH aanroepen om [de status van de bewerkings-API](#update-the-status-of-an-operation) met een fout-en geslaagde reactie *binnen een periode van 10 seconden* na ontvangst van de webhook-melding bij te werken. Als de PATCH van de bewerkings status niet binnen tien seconden wordt ontvangen, wordt het wijzigings plan *automatisch als geslaagd gerepareerd*. 

De volg orde van de API-aanroepen voor een door Marketplace geïnitieerd update scenario wordt hieronder weer gegeven.

![API-aanroepen voor een door Marketplace geïnitieerde update](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>Update gestart vanuit de Publisher

In deze stroom wijzigt de klant het abonnement of het aantal stoelen dat is gekocht van de SaaS-service zelf. 

1. De uitgevers code moet de [API change plan](#change-the-plan-on-the-subscription) aanroepen en/of de API van de [hoeveelheid wijzigen](#change-the-quantity-of-seats-on-the-saas-subscription) voordat de aangevraagde wijziging aan de kant van de uitgever wordt aangebracht. 

1. Micro soft past de wijziging toe op het abonnement en waarschuwt de uitgever via de **verbindings-webhook** om dezelfde wijziging toe te passen.  

1. Alleen dan moet de uitgever de vereiste wijziging aanbrengen in het SaaS-abonnement en micro soft op de hoogte stellen wanneer de wijziging plaatsvindt door de [update status van de bewerkings-API aan](#update-the-status-of-an-operation)te roepen.

De volg orde van de API-aanroepen voor het door de uitgever geïnitieerde update scenario.

![API-aanroepen voor een door de uitgever geïnitieerde update](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Onderbroken (*onderbroken*)

Deze status geeft aan dat de betaling van een klant voor de SaaS-service niet is ontvangen. De uitgever ontvangt een melding van deze wijziging in de status van SaaS-abonnementen door micro soft. De melding wordt uitgevoerd via een aanroep van webhook waarvoor de *actie* parameter is ingesteld op *opgeschort*.

De uitgever kan wellicht geen wijzigingen aanbrengen in de SaaS-service aan de kant van de uitgever. We raden aan dat deze informatie door de uitgever beschikbaar wordt gesteld aan de opgeschorte klant en de toegang van klanten tot de SaaS-service blokkeert.  Er is een kans dat de betaling nooit wordt ontvangen.

Micro soft geeft de klant een respijt periode van 30 dagen voordat het abonnement automatisch wordt geannuleerd. Wanneer een abonnement de status onderbroken heeft:

* De SaaS-account moet door de ISV worden bewaard in een herstel bare status. Volledige functionaliteit kan worden hersteld zonder verlies van gegevens of instellingen.
* U ontvangt een aanvraag voor het opnieuw invoeren van dit abonnement als de betaling is ontvangen tijdens de respijt periode of een aanvraag voor het ongedaan maken van de inrichting aan het einde van de respijt periode, zowel via het webhook-mechanisme.

De status van het abonnement wordt gewijzigd in opgeschort op de micro soft-website voordat de uitgever actie onderneemt. Alleen actieve abonnementen kunnen worden opgeschort.

#### <a name="reinstated-suspended"></a>Opnieuw ingesteld (*onderbroken*)

Het abonnement wordt opnieuw ingesteld.

Deze actie geeft aan dat het betalings instrument van de klant opnieuw geldig is en dat er een betaling wordt gedaan voor het SaaS-abonnement.  Het abonnement wordt opnieuw ingesteld. In dat geval: 

1. Micro soft roept webhook aan met een *actie* parameter ingesteld op de waarde voor het opnieuw *invoeren* .  
1. De uitgever zorgt ervoor dat dit abonnement weer volledig operationeel is aan de kant van de uitgever.
1. De uitgever roept de [API-bewerkings-api's](#update-the-status-of-an-operation) met de status geslaagd aan.  
1. Vervolgens is het opnieuw invoeren geslaagd en wordt de klant opnieuw gefactureerd voor het SaaS-abonnement. 
1. Als de patch wordt verzonden met de status mislukt, wordt het herstel proces niet voltooid aan de kant van micro soft. Het abonnement blijft opgeschort.

Als de patch wordt verzonden met de status mislukt, wordt het herstel proces niet voltooid aan de kant van micro soft.  Het abonnement blijft opgeschort.

Alleen een opgeschort abonnement kan worden hersteld.  Terwijl een SaaS-abonnement wordt hersteld, blijft de status opgeschort.  Zodra deze bewerking is voltooid, wordt de status van het abonnement actief.

#### <a name="renewed-subscribed"></a>Vernieuwd (*geabonneerd*)

Aan het einde van de abonnements termijn (na een maand of een jaar) wordt het SaaS-abonnement automatisch vernieuwd door micro soft.  De standaard instelling voor automatisch vernieuwen is *waar* voor alle SaaS-abonnementen. Actieve SaaS-abonnementen worden nog steeds vernieuwd met gewone uitgebracht. Micro soft waarschuwt de uitgever niet wanneer een abonnement wordt vernieuwd. Een klant kan automatische verlenging voor een SaaS-abonnement uitschakelen via de M365-beheer portal of via Azure Portal.  In dit geval wordt het SaaS-abonnement automatisch geannuleerd aan het einde van de huidige facturerings periode.  Klanten kunnen het SaaS-abonnement ook op elk moment annuleren.

Alleen actieve abonnementen worden automatisch verlengd.  Abonnementen blijven actief tijdens het vernieuwings proces en als de automatische verlenging slaagt.  Na de verlenging worden de begin-en eind datum van de abonnements termijn bijgewerkt naar de datums van de nieuwe periode.

Als een automatische vernieuwing mislukt vanwege een probleem met de betaling, wordt het abonnement onderbroken.  Er wordt een melding van de uitgever weer gegeven.

#### <a name="canceled-unsubscribed"></a>Geannuleerd (*afgemeld*) 

Abonnementen bereiken deze status als reactie op een expliciete klant-of CSP-actie door de annulering van een abonnement van de Publisher-site, het Azure Portal of het M365-beheer centrum.  Een abonnement kan ook impliciet worden geannuleerd als gevolg van een niet-betaalde contributie, na een opgeschorte status van 30 dagen.

Bij het ontvangen van een aanroep van de webhook van een annulering moet de klant gegevens voor herstel op aanvraag ten minste zeven dagen worden bewaard. Alleen vervolgens kunnen klant gegevens worden verwijderd.

Een SaaS-abonnement kan op elk gewenst moment in de levens cyclus worden geannuleerd. Wanneer een abonnement is geannuleerd, kan het niet opnieuw worden geactiveerd.

## <a name="api-reference"></a>API-verwijzing

In deze sectie worden de SaaS-abonnementen en Operations Api's gedocumenteerd.

**Abonnement-api's** moeten worden gebruikt voor het afhandelen van de levens cyclus van het SaaS-abonnement van aankoop tot annulering.

**Operations api's** moeten worden gebruikt voor het volgende:

* de verwerkte webhook-aanroepen verifiëren en bevestigen
* een lijst met apps ophalen die wachten op bevestiging door de uitgever

### <a name="enforcing-tls-12-note"></a>Opmerking over het afdwingen van TLS 1,2

De versie van de TLS-versie 1,2 wordt binnenkort afgedwongen als de minimale versie voor HTTPS-communicatie. Zorg ervoor dat u deze TLS-versie in uw code gebruikt.  TLS-versie 1,0 en 1,1 zal binnenkort worden afgeschaft.

### <a name="subscription-apis"></a>Abonnements-Api's

#### <a name="resolve-a-purchased-subscription"></a>Een gekocht abonnement oplossen

Met het eind punt voor omzetten kan de uitgever het aankoop identificatie token van de Marketplace uitwisselen (aangeduid als *token* in [ingekocht maar nog niet geactiveerd](#purchased-but-not-yet-activated-pendingfulfillmentstart)) naar een permanent aangeschafte SaaS-abonnement-id en de bijbehorende gegevens.

Wanneer een klant wordt omgeleid naar de URL van de landings pagina van de partner, wordt het identificatie token van de klant door gegeven als *token* parameter in deze URL-aanroep. De partner wordt verwacht dit token te gebruiken en een aanvraag te doen om het te verhelpen. Het antwoord op de API oplossen bevat de SaaS-abonnements-ID en andere gegevens om de aankoop uniek te identificeren. Het *token* dat is opgenomen in de URL-oproep van de landings pagina is doorgaans 24 uur geldig. Als het *token* dat u ontvangt al is verlopen, raden we u aan de volgende richt lijnen aan te bieden aan de eind klant:

"We kunnen deze aankoop niet identificeren, open dit SaaS-abonnement opnieuw in Azure Portal of in het M365-beheer centrum en klik opnieuw op de knop account configureren of account beheren."

Bij het aanroepen van Resolve API worden de abonnements gegevens en de status van SaaS-abonnementen in alle ondersteunde statussen geretourneerd.

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Plaatsen`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Gebruik 2018-08-31.   |

*Aanvraag headers:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID. Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers. |
|  `x-ms-correlationid` |  Een unieke teken reeks waarde voor de bewerking op de client. Deze para meter verbindt alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde. Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers.  |
|  `authorization`     |  Een uniek toegangs token dat de uitgever identificeert die deze API aanroept. De indeling is `"Bearer <accessaccess_token>"` wanneer de token waarde wordt opgehaald door de uitgever, zoals wordt uitgelegd in [een Token ophalen op basis van de Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post). |
|  `x-ms-marketplace-token`  | De para meter voor het kopen van id- *tokens* voor Marketplace voor het oplossen.  Het token wordt door gegeven in de URL-oproep van de landings pagina wanneer de klant wordt omgeleid naar de website van de SaaS-partner (bijvoorbeeld: `https://contoso.com/signup?token=<token><authorization_token>` ). <br> <br>  *Opmerking:* De *token* waarde die wordt gecodeerd, maakt deel uit van de URL van de landings pagina en moet dus worden gedecodeerd voordat deze als para meter wordt gebruikt in deze API-aanroep.  <br> <br> Voor beeld van een gecodeerde teken reeks in de URL ziet er als volgt uit: `contoso.com/signup?token=ab%2Bcd%2Fef` , waarbij token is `ab%2Bcd%2Fef` .  Hetzelfde token is gedecodeerd: `Ab+cd/ef` |
| | |

*Antwoord codes:*

Code: 200 retourneert unieke SaaS-abonnements-id's op basis van de voor `x-ms-marketplace-token` waarde.

Voor beeld van antwoord tekst:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Code: 400 ongeldige aanvraag. `x-ms-marketplace-token` ontbreekt, is onjuist of ongeldig of verlopen.

Code: 403 verboden. Het autorisatie token is ongeldig, verlopen of niet meegeleverd.  De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-app-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) .

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

#### <a name="activate-a-subscription"></a>Een abonnement activeren

Zodra het SaaS-account is geconfigureerd voor een eind klant, moet de uitgever de API voor het activeren van abonnementen aan micro soft zijde aanroepen.  De klant wordt niet gefactureerd tenzij deze API-aanroep is geslaagd.

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Plaatsen`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Gebruik 2018-08-31.   |
| `subscriptionId` | Een unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token van de Marketplace met behulp van de [API voor omzetten](#resolve-a-purchased-subscription).
 |

*Aanvraag headers:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers. |
| `x-ms-correlationid` |  Een unieke teken reeks waarde voor de bewerking op de client.  Deze teken reeks correleert alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers. |
| `authorization`      |  Een uniek toegangs token dat de uitgever identificeert die deze API aanroept. De indeling is `"Bearer <access_token>"` wanneer de token waarde wordt opgehaald door de uitgever, zoals wordt uitgelegd in [een Token ophalen op basis van de Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Voor beeld van Payload aanvragen:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Antwoord codes:*

Code: 200 het abonnement is gemarkeerd als geabonneerd op micro soft-kant.

Er is geen antwoord tekst voor deze aanroep.

Code: 400 ongeldige aanvraag: de validatie is mislukt.

* `planId` bestaat niet in de aanvraag lading.
* `planId` in de aanvraag lading komt niet overeen met de waarde die is gekocht.
* `quantity` in de aanvraag lading komt niet overeen met het aangeschafte item
* Het SaaS-abonnement is geabonneerd of onderbroken.

Code: 403 verboden. Het verificatie token is ongeldig, verlopen of niet meegeleverd. De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-app-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) .

Code: 404 niet gevonden. Het SaaS-abonnement is afgemeld.

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

#### <a name="get-list-of-all-subscriptions"></a>Lijst met alle abonnementen ophalen

Hiermee haalt u een lijst met alle aangeschafte SaaS-abonnementen op voor alle aanbiedingen die zijn gepubliceerd door de uitgever op Marketplace.  SaaS-abonnementen in alle mogelijke statussen worden geretourneerd. Niet-geabonneerde SaaS-abonnementen worden ook geretourneerd, omdat deze informatie niet wordt verwijderd aan micro soft.

Deze API retourneert gepagineerde resultaten. Pagina grootte is 100.

##### <a name="gethttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Ophalen`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Gebruik 2018-08-31.  |
| `continuationToken`  | Optionele parameter. Als u de eerste pagina met resultaten wilt ophalen, laat u leeg.  Gebruik de waarde die is geretourneerd in `@nextLink` de para meter om de volgende pagina op te halen. |

*Aanvraag headers:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID. Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers. |
| `x-ms-correlationid` |  Een unieke teken reeks waarde voor de bewerking op de client.  Deze para meter verbindt alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers. |
| `authorization`      |  Een uniek toegangs token dat de uitgever identificeert die deze API aanroept.  De indeling is `"Bearer <access_token>"` wanneer de token waarde wordt opgehaald door de uitgever, zoals wordt uitgelegd in [een Token ophalen op basis van de Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Antwoord codes:*

Code: 200 retourneert de lijst met alle bestaande abonnementen voor alle aanbiedingen van deze uitgever, op basis van het autorisatie token van de uitgever.

*Voor beeld van antwoord tekst:*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Als er geen aangeschafte SaaS-abonnementen voor deze uitgever worden gevonden, wordt er een lege antwoord tekst geretourneerd.

Code: 403 verboden. Het autorisatie token is niet beschikbaar, ongeldig of verlopen.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) . 

Code: 500 interne server fout. Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

#### <a name="get-subscription"></a>Abonnement ophalen

Haalt een opgegeven ingekochte SaaS-abonnement op voor een SaaS-aanbieding die is gepubliceerd op Marketplace door de uitgever. Gebruik deze aanroep om alle beschik bare informatie voor een specifiek SaaS-abonnement op te halen in plaats van de API aan te roepen voor het ophalen van een lijst met alle abonnementen.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Toevoegen `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Gebruik 2018-08-31. |
| `subscriptionId`     |  Een unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token van de Marketplace met behulp van de API voor omzetten. |

*Aanvraag headers:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID. Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers. |
|  `x-ms-correlationid` |  Een unieke teken reeks waarde voor de bewerking op de client.  Deze para meter verbindt alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers. |
|  `authorization`     | Een uniek toegangs token dat de uitgever identificeert die deze API aanroept. De indeling is `"Bearer <access_token>"` wanneer de token waarde wordt opgehaald door de uitgever, zoals wordt uitgelegd in [een Token ophalen op basis van de Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Antwoord codes:*

Code: 200 retourneert Details voor een SaaS-abonnement op basis van de voor `subscriptionId` waarde.

*Voor beeld van antwoord tekst:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

Code: 403 verboden. Het verificatie token is ongeldig, verlopen en niet gegeven. De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-app-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) . 

Code: 404 niet gevonden.  SaaS-abonnement met de opgegeven `subscriptionId` kan niet worden gevonden.

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

#### <a name="list-available-plans"></a>Beschik bare abonnementen weer geven

Haalt alle abonnementen op voor een SaaS-aanbieding die wordt geïdentificeerd door de `subscriptionId` van een specifieke aankoop van deze aanbieding.  Gebruik deze aanroep om een lijst op te halen met alle persoonlijke en open bare abonnementen die de ontvanger van een SaaS-abonnement kan bijwerken voor het abonnement.  De plannen die worden geretourneerd, zijn beschikbaar in dezelfde geografie als het al gekochte abonnement.

Deze aanroep retourneert een lijst met abonnementen die beschikbaar zijn voor die klant, naast het abonnement dat al is gekocht.  De lijst kan worden weer gegeven aan een eind klant op de Publisher-site.  Een eind klant kan het abonnement wijzigen in een van de plannen in de geretourneerde lijst.  Het is niet mogelijk om het plan te wijzigen in een item dat niet wordt weer gegeven in de lijst.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Toevoegen `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Gebruik 2018-08-31.  |
|  `subscriptionId`    |  Een unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token van de Marketplace met behulp van de API voor omzetten. |

*Aanvraag headers:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers. |
|  `x-ms-correlationid`  |  Een unieke teken reeks waarde voor de bewerking op de client.  Deze para meter verbindt alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers. |
|  `authorization`     |  Een uniek toegangs token dat de uitgever identificeert die deze API aanroept.  De indeling is `"Bearer <access_token>"` wanneer de token waarde wordt opgehaald door de uitgever, zoals wordt uitgelegd in [een Token ophalen op basis van de Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Antwoord codes:*

Code: 200 retourneert een lijst van alle beschik bare abonnementen voor een bestaand SaaS-abonnement, met inbegrip van de versie die al is gekocht.

Voor beeld van antwoord tekst:

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

Als `subscriptionId` niet wordt gevonden, wordt de tekst van een leeg antwoord geretourneerd.

Code: 403 verboden. Het autorisatie token is ongeldig, verlopen of niet meegeleverd.  De aanvraag probeert mogelijk toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-app-ID dan die waarmee het autorisatie token is gemaakt.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) . 

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

#### <a name="change-the-plan-on-the-subscription"></a>Wijzig het abonnement

Het bestaande abonnement dat is gekocht voor een SaaS-abonnement bijwerken naar een nieuw abonnement (openbaar of privé).  De uitgever moet deze API aanroepen wanneer een plan wordt gewijzigd op de Publisher-kant voor een SaaS-abonnement dat is gekocht in Marketplace.

Deze API kan alleen worden aangeroepen voor actieve abonnementen.  Elk plan kan worden gewijzigd in elk ander bestaand plan (openbaar of privé), maar niet op zichzelf.  Voor privé abonnementen moet de Tenant van de klant worden gedefinieerd als onderdeel van de doel groep van het plan in het partner centrum.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Verzenden `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Gebruik 2018-08-31.  |
| `subscriptionId`     | Een unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token van de Marketplace met behulp van de API voor omzetten. |

*Aanvraag headers:*
 
|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID. Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers.  |
|  `x-ms-correlationid`  | Een unieke teken reeks waarde voor de bewerking op de client.  Deze para meter verbindt alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers.  |
|  `authorization`     |  Een uniek toegangs token dat de uitgever identificeert die deze API aanroept.  De indeling is `"Bearer <access_token>"` wanneer de token waarde wordt opgehaald door de uitgever, zoals wordt uitgelegd in [een Token ophalen op basis van de Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Voor beeld van Payload aanvragen:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*Antwoord codes:*

Code: 202 de aanvraag voor het wijzigings plan is asynchroon geaccepteerd en afgehandeld.  De partner moet de URL van de **bewerkings locatie** naar verwachting controleren om te bepalen of de aanvraag voor het wijzigings plan is geslaagd of mislukt.  Polling moet elke seconde worden uitgevoerd tot de uiteindelijke status mislukt, geslaagd of conflict is ontvangen voor de bewerking.  De uiteindelijke bewerkings status moet snel worden geretourneerd, maar kan in sommige gevallen enkele minuten duren.

De partner krijgt ook webhook-melding wanneer de actie gereed is om te worden voltooid op de Marketplace-zijde.  En alleen dan moet de uitgever het plan wijzigen aan de kant van de uitgever.

*Antwoord headers:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  URL voor het ophalen van de status van de bewerking.  Bijvoorbeeld `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Code: 400 ongeldige aanvraag: validatie mislukt.

* Het nieuwe abonnement bestaat niet of is niet beschikbaar voor dit specifieke SaaS-abonnement.
* Er wordt geprobeerd om te wijzigen in hetzelfde abonnement.
* De status van het SaaS-abonnement is niet geabonneerd.
* De update bewerking voor een SaaS-abonnement is niet opgenomen in `allowedCustomerOperations` .

Code: 403 verboden. Het autorisatie token is ongeldig, verlopen of niet meegeleverd.  De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-app-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) .

Code: 404 niet gevonden.  Het SaaS-abonnement met `subscriptionId` kan niet worden gevonden.

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

>[!NOTE]
>Het plan of aantal stoelen kan tegelijk worden gewijzigd, niet beide.

>[!Note]
>Deze API kan alleen worden aangeroepen nadat expliciete goed keuring van de eind klant is opgehaald voor de wijziging.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Het aantal stoelen in het SaaS-abonnement wijzigen

Update (verg Roten of verkleinen) van het aantal seats dat is gekocht voor een SaaS-abonnement.  De uitgever moet deze API aanroepen wanneer het aantal seats wordt gewijzigd van de uitgever van een SaaS-abonnement dat is gemaakt in Marketplace.

Aantal seats mag niet meer zijn dan is toegestaan in het huidige plan.  In dit geval moet het plan worden gewijzigd voordat een hoeveelheid wordt gewijzigd.

##### <a name="patchhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Gebruik 2018-08-31.  |
|  `subscriptionId`     | Een unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token van de Marketplace met behulp van de API voor omzetten.  |

*Aanvraag headers:*
 
|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers.  |
|  `x-ms-correlationid`  | Een unieke teken reeks waarde voor de bewerking op de client.  Deze para meter verbindt alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers.  |
|  `authorization`     | Een uniek toegangs token dat de uitgever identificeert die deze API aanroept.  De indeling is `"Bearer <access_token>"` wanneer de token waarde wordt opgehaald door de uitgever, zoals wordt uitgelegd in [een Token ophalen op basis van de Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Voor beeld van Payload aanvragen:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*Antwoord codes:*

Code: 202 de aanvraag voor het wijzigen van de hoeveelheid is asynchroon geaccepteerd en afgehandeld. De partner moet de URL van de **bewerkings locatie** naar verwachting controleren om te bepalen of de aanvraag voor wijzigings hoeveelheid is geslaagd of mislukt.  Polling moet elke seconde worden uitgevoerd tot de uiteindelijke status mislukt, geslaagd of conflict is ontvangen voor de bewerking.  De uiteindelijke bewerkings status moet snel worden geretourneerd, maar kan in sommige gevallen enkele minuten in beslag nemen.

De partner krijgt ook webhook-melding wanneer de actie gereed is om te worden voltooid op de Marketplace-zijde.  En alleen dan moet de uitgever de hoeveelheid wijzigen aan de kant van de uitgever.

*Antwoord headers:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Koppeling naar een resource om de status van de bewerking op te halen.  Bijvoorbeeld `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Code: 400 ongeldige aanvraag: validatie mislukt.

* De nieuwe hoeveelheid is groter of lager dan de limiet van het huidige abonnement.
* De nieuwe hoeveelheid ontbreekt.
* Er wordt geprobeerd om te wijzigen in hetzelfde aantal.
* De status van het SaaS-abonnement is niet geabonneerd.
* De update bewerking voor een SaaS-abonnement is niet opgenomen in `allowedCustomerOperations` .

Code: 403 verboden.  Het autorisatie token is ongeldig, verlopen of niet meegeleverd.  De aanvraag probeert toegang te krijgen tot een abonnement dat geen deel uitmaakt van de huidige uitgever.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) . 

Code: 404 niet gevonden.  Het SaaS-abonnement met `subscriptionId` kan niet worden gevonden.

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

>[!Note]
>Alleen een plan of hoeveelheid kan in één keer worden gewijzigd, niet beide.

>[!Note]
>Deze API kan alleen worden aangeroepen nadat expliciete goed keuring van de eind klant is opgehaald voor de wijziging.

#### <a name="cancel-a-subscription"></a>Een abonnement annuleren

Een opgegeven SaaS-abonnement opzeggen.  De uitgever hoeft deze API niet te gebruiken en wij raden aan dat de klanten naar Marketplace worden geleid om SaaS-abonnementen te annuleren.

Als de uitgever besluit de annulering te implementeren van het SaaS-abonnement dat is gekocht in Marketplace op de kant van de uitgever, moet hij deze API aanroepen.  Nadat deze aanroep is voltooid, wordt de status van het abonnement *afgemeld* bij micro soft.

Als een abonnement wordt geannuleerd binnen de volgende respijt perioden, wordt de klant niet in rekening gebracht:

* 24 uur voor een maandelijks abonnement na activering.
* 14 dagen voor een jaar abonnement na activering.

De klant wordt gefactureerd als een abonnement wordt geannuleerd na de bovenstaande respijt periode.  Zodra de annulering is geslaagd, verliest de klant onmiddellijk de toegang tot het SaaS-abonnement aan micro soft.

##### <a name="deletehttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Verwijderen`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Gebruik 2018-08-31.  |
|  `subscriptionId`     | Een unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token van de Marketplace met behulp van de API voor omzetten.  |

*Aanvraag headers:*
 
|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers.  |
|  `x-ms-correlationid`  | Een unieke teken reeks waarde voor de bewerking op de client.  Deze para meter verbindt alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers.  |
|  `authorization`     |  Een uniek toegangs token dat de uitgever identificeert die deze API aanroept.  De indeling is `"Bearer <access_token>"` wanneer de token waarde wordt opgehaald door de uitgever, zoals wordt uitgelegd in [een Token ophalen op basis van de Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Antwoord codes:*

Code: 202 de aanvraag voor het afmelden is asynchroon geaccepteerd en afgehandeld.  De partner moet de URL van de **bewerkings locatie** naar verwachting controleren om te bepalen of deze aanvraag is geslaagd of mislukt.  Polling moet elke seconde worden uitgevoerd tot de uiteindelijke status mislukt, geslaagd of conflict is ontvangen voor de bewerking.  De uiteindelijke bewerkings status moet snel worden geretourneerd, maar kan in sommige gevallen enkele minuten in beslag nemen.

De partner haalt ook webhook-melding op wanneer de actie is voltooid aan de kant van de Marketplace.  En alleen dan moet de uitgever het abonnement op de uitgever annuleren.

*Antwoord headers:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Koppeling naar een resource om de status van de bewerking op te halen.  Bijvoorbeeld `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Code: 400 ongeldige aanvraag.  Verwijderen is niet in `allowedCustomerOperations` lijst voor dit SaaS-abonnement.

Code: 403 verboden.  Het verificatie token is ongeldig, verlopen of niet beschikbaar. De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-app-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) .

Code: 404 niet gevonden.  Het SaaS-abonnement met `subscriptionId` kan niet worden gevonden.

Code: 500 interne server fout. Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

### <a name="operations-apis"></a>Operations-Api's

#### <a name="list-outstanding-operations"></a>Openstaande bewerkingen weer geven 

Haal een lijst op met de bewerkingen die in behandeling zijn voor het opgegeven SaaS-abonnement.  Geretourneerde bewerkingen moeten worden bevestigd door de uitgever door de [API voor de bewerkings patch](#update-the-status-of-an-operation)aan te roepen.

Op dit moment worden alleen de **bewerkingen** voor deze API-aanroep geretourneerd als reactie.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Toevoegen `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Gebruik 2018-08-31.         |
|    `subscriptionId` | Een unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token van de Marketplace met behulp van de API voor omzetten.  |

*Aanvraag headers:*
 
|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers.  |
|  `x-ms-correlationid` |  Een unieke teken reeks waarde voor de bewerking op de client.  Deze para meter verbindt alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers.  |
|  `authorization`     |  De indeling is `"Bearer <access_token>"` wanneer de token waarde wordt opgehaald door de uitgever, zoals wordt uitgelegd in [een Token ophalen op basis van de Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Antwoord codes:*

Code: 200 retourneert wachtend op opnieuw invoeren voor het opgegeven SaaS-abonnement.

*Voor beeld van een nettolading van antwoorden:*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

Retourneert een lege JSON als er geen bewerkingen voor opnieuw invoeren in behandeling zijn.

Code: 400 ongeldige aanvraag: validatie mislukt.

Code: 403 verboden. Het autorisatie token is ongeldig, verlopen of niet meegeleverd.  De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-app-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) . 

Code: 404 niet gevonden.  Het SaaS-abonnement met `subscriptionId` kan niet worden gevonden.

Code: 500 interne server fout. Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

#### <a name="get-operation-status"></a>Bewerkings status ophalen

Hiermee kan de uitgever de status van de opgegeven async-bewerking bijhouden:  **Afmelden**, **ChangePlan** of **ChangeQuantity**.

De `operationId` voor deze API-aanroep kan worden opgehaald uit de waarde die wordt geretourneerd door de **bewerking-locatie**, in afwachting van de API-aanroep in behandeling of de `<id>` parameter waarde ontvangen in een webhook-aanroep.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Toevoegen `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Gebruik 2018-08-31.  |
|  `subscriptionId`    |  Een unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token van de Marketplace met behulp van de API voor omzetten. |
|  `operationId`       |  De unieke id van de bewerking die wordt opgehaald. |

*Aanvraag headers:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers. |
|  `x-ms-correlationid` |  Een unieke teken reeks waarde voor de bewerking op de client.  Deze para meter verbindt alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers.  |
|  `authorization`     |  Een uniek toegangs token dat de uitgever identificeert die deze API aanroept.  De indeling is `"Bearer <access_token>"` wanneer de token waarde wordt opgehaald door de uitgever, zoals wordt uitgelegd in [een Token ophalen op basis van de Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Antwoord codes:*

Code: 200 Hiermee worden details opgehaald voor de opgegeven SaaS-bewerking. 

*Voor beeld van een nettolading van antwoorden:*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeed, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

Code: 403 verboden. Het autorisatie token is ongeldig, verlopen of niet meegeleverd.  De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-app-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) . 

Code: 404 niet gevonden.  

* Kan het abonnement `subscriptionId` niet vinden.
* De bewerking `operationId` kan niet worden gevonden.

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

#### <a name="update-the-status-of-an-operation"></a>De status van een bewerking bijwerken

Werk de status van een bewerking in behandeling bij om aan te geven dat het slagen of mislukken van de bewerking aan de kant van de uitgever is.

De `operationId` voor deze API-aanroep kan worden opgehaald uit de waarde die wordt geretourneerd door de **bewerkings locatie**, in behandeling zijnde Operations API-aanroep of de `<id>` parameter waarde ontvangen in een webhook-aanroep.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Verzenden `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Gebruik 2018-08-31.  |
|   `subscriptionId`   |  Een unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token van de Marketplace met behulp van de API voor omzetten.  |
|   `operationId`      |  De unieke id van de bewerking die wordt voltooid. |

*Aanvraag headers:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers. |
|   `x-ms-correlationid` |  Een unieke teken reeks waarde voor de bewerking op de client.  Deze para meter verbindt alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers. |
|  `authorization`     |  Een uniek toegangs token dat de uitgever identificeert die deze API aanroept.  De indeling is `"Bearer <access_token>"` wanneer de token waarde wordt opgehaald door de uitgever, zoals wordt uitgelegd in [een Token ophalen op basis van de Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Voor beeld van Payload aanvragen:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Antwoord codes:*

Code: 200 een aanroep naar het melden van de voltooiing van een bewerking aan de partner zijde.  Deze reactie kan bijvoorbeeld geven aan de voltooiing van de wijziging van de stoelen of abonnementen aan de kant van de uitgever.

Code: 403 verboden.  Het autorisatie token is niet beschikbaar, ongeldig of verlopen. De aanvraag probeert mogelijk toegang te krijgen tot een abonnement dat geen deel uitmaakt van de huidige uitgever.
Slag.  Het autorisatie token is ongeldig, verlopen of niet meegeleverd.  De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-app-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) .

Code: 404 niet gevonden.

* Kan het abonnement `subscriptionId` niet vinden.
* De bewerking `operationId` kan niet worden gevonden.

Code: 409 conflict.  Er is bijvoorbeeld al aan een nieuwere update voldaan.

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

## <a name="implementing-a-webhook-on-the-saas-service"></a>Een webhook implementeren op de SaaS-service

Bij het maken van een transactable SaaS-aanbieding in het partner centrum biedt de partner de URL van de **verbindings-webhook** die moet worden gebruikt als een http-eind punt.  Deze webhook wordt door micro soft aangeroepen met behulp van de POST HTTP-aanroep om aan de uitgever een melding te sturen van de volgende gebeurtenissen die zich op micro soft zijde voordoen:

* Wanneer het SaaS-abonnement de status geabonneerd heeft:
    * ChangePlan 
    * ChangeQuantity
    * Onderbreken
    * Afmelden
* Wanneer SaaS-abonnement de status opgeschort heeft:
    * Opnieuw invoeren
    * Afmelden

De uitgever moet een webhook implementeren in de SaaS-service om de SaaS-abonnements status consistent te laten zijn met de micro soft-zijde.  De SaaS-service is vereist voor het aanroepen van de API Get Operation om de webhook-aanroep-en payload-gegevens te valideren en te autoriseren voordat u actie onderneemt op basis van de webhook-melding.  De uitgever moet HTTP 200 terugsturen naar micro soft zodra de webhook-aanroep wordt verwerkt.  Met deze waarde wordt bevestigd dat de webhook-aanroep is ontvangen door de uitgever.

>[!Note]
>De webhook-URL-service moet 24x7 actief zijn, en u kunt op elk moment nieuwe oproepen ontvangen van micro soft-tijd.  Micro soft heeft een beleid voor opnieuw proberen voor de webhook-aanroep (500 nieuwe pogingen van meer dan acht uur), maar als de uitgever de aanroep niet accepteert en een antwoord retourneert, zal de bewerking die webhook waarschuwt uiteindelijk mislukken aan de kant van micro soft.

*Voor beelden van payload van webhook:*

```json
// end customer changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

```json
// end customer's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": " 20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress"
}
```

## <a name="development-and-testing"></a>Ontwikkelen en testen

Om het ontwikkel proces te starten, raden we aan om dummy API-antwoorden te maken aan de kant van de uitgever.  Deze antwoorden kunnen worden gebaseerd op voorbeeld reacties die in dit document zijn opgenomen.

Wanneer de uitgever klaar is voor het end-to-end testen:

* Publiceer een SaaS-aanbieding naar een beperkte preview-doel groep en bewaar deze in de preview-fase.
* Deze aanbieding moet een abonnement hebben met een prijs van 0, zodat er geen werkelijke facturerings kosten worden geactiveerd tijdens het testen.  Een andere mogelijkheid is om een prijs die niet gelijk is aan nul in te stellen en alle test aankopen binnen 24 uur te annuleren.
* Zorg ervoor dat alle stromen end-to-end worden aangeroepen, net zoals een klant de aanbieding koopt.
* Als de partner volledige aankoop-en facturerings stroom wil testen, doet u dit met een aanbieding die hoger is dan $0.  De aankoop wordt gefactureerd en er wordt een factuur gegenereerd.

Een aankoop stroom kan worden geactiveerd vanaf de Azure Portal-of Microsoft AppSource-sites, afhankelijk van waar de aanbieding wordt gepubliceerd.

Acties voor het wijzigen van de *planning*, het wijzigen van de *hoeveelheid* en het *Afmelden* van het abonnement worden getest vanuit de uitgever.  Aan de kant van micro soft kan de *afmelding* worden geactiveerd vanuit zowel de Azure portal als het beheer centrum (de portal waar Microsoft AppSource aankopen worden beheerd).  *Hoeveelheid en plan wijzigen* kan alleen worden geactiveerd vanuit het beheer centrum.

## <a name="get-support"></a>Ondersteuning krijgen

Zie [ondersteuning voor het programma voor commerciële Marketplace in het partner centrum](../support.md) voor ondersteunings opties voor Publisher.

## <a name="next-steps"></a>Volgende stappen

Zie de [commerciële Marketplace meter service-api's](marketplace-metering-service-apis.md) voor meer opties voor SaaS-aanbiedingen in de commerciële Marketplace.

Bekijk en gebruik de [SaaS SDK](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK) die is gebouwd op basis van de api's die in dit document worden beschreven.
