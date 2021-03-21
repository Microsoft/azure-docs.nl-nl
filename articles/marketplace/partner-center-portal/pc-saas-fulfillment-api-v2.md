---
title: SaaS-fulfillment-Api's v2 in micro soft Commercial Marketplace
description: Meer informatie over het maken en beheren van een SaaS-aanbieding op Microsoft AppSource en Azure Marketplace met behulp van de fulfillment Api's versie 2.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 2acf5178e7d1cfdf907146d733150a48e9696a5e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712348"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>SaaS-fulfillment-Api's versie 2 in de commerciële Marketplace

In dit artikel vindt u meer informatie over de Api's waarmee partners hun software als een service (SaaS) kunnen verkopen in Microsoft AppSource en Azure Marketplace. Er is een uitgever vereist voor het implementeren van integratie met deze Api's voor het publiceren van een transactable SaaS-aanbieding in het partner centrum.

## <a name="managing-the-saas-subscription-life-cycle"></a>De levens cyclus van het SaaS-abonnement beheren

De commerciële Marketplace beheert de volledige levens cyclus van een SaaS-abonnement na de aankoop door de eind gebruiker. Het maakt gebruik van de landings pagina, de fulfillment Api's, de operations-Api's en de webhook als mechanisme om de daad werkelijke activering, het gebruik, de updates en de annulering van het SaaS-abonnement te best uren. De factuur van de eind gebruiker is gebaseerd op de status van de SaaS-abonnement die micro soft onderhoudt. 

### <a name="states-of-a-saas-subscription"></a>Statussen van een SaaS-abonnement

In het volgende diagram ziet u de statussen van een SaaS-abonnement en de toepasselijke acties.

![Diagram waarin de levens cyclus van een software als een service-abonnement in de Marketplace wordt weer gegeven.](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Gekocht maar nog niet geactiveerd (*PendingFulfillmentStart*)

Nadat een eind gebruiker (of CSP) een SaaS-aanbieding heeft gekocht in de commerciële Marketplace, moet de uitgever op de hoogte worden gesteld van de aankoop. De uitgever kan vervolgens een nieuw SaaS-account maken en configureren aan de kant van de uitgever voor de eind gebruiker.

Voor het maken van accounts gebeurt het volgende:

1. De klant selecteert de knop **configureren** die beschikbaar is voor een SaaS-aanbieding na een succes volle aankoop in Microsoft AppSource of de Azure Portal. Het is ook mogelijk dat de klant de knop **configureren** in het e-mail bericht gebruikt dat ze kort na de aankoop zullen worden ontvangen.
2. Micro soft stuurt de partner vervolgens op de hoogte van de aankoop door te openen op het tabblad nieuwe browser de URL van de landings pagina met de para meter token (het aankoop identificatie token van de commerciële Marketplace).

Een voor beeld van een dergelijke aanroep is `https://contoso.com/signup?token=<blob>` , terwijl de URL van de landings pagina voor deze SaaS-aanbieding in het partner centrum is geconfigureerd als `https://contoso.com/signup` . Dit token biedt de uitgever een ID die de SaaS-aankoop en de klant op unieke wijze identificeert.

>[!NOTE]
>De uitgever ontvangt geen melding van de SaaS-aankoop totdat de klant het configuratie proces van de micro soft-kant initieert.

De URL van de landings pagina moet elke dag, elke dag, en nu op elk moment nieuwe oproepen van micro soft ontvangen. Als de landings pagina niet beschikbaar is, kunnen klanten zich niet aanmelden voor de SaaS-service en deze gebruiken.

Vervolgens moet de uitgever het *token* terugsturen naar micro soft door de [API voor SaaS-conflicten](#resolve-a-purchased-subscription)aan te roepen en het token in te voeren als de waarde van de `x-ms-marketplace-token header` para meter header. Als gevolg van de API-aanroep omzetten, wordt het token uitgewisseld voor details van de SaaS-aankoop, zoals de unieke ID van de aankoop, de gekochte aanbiedings-ID en de aangeschafte plan-ID.

Op de landings pagina moet de klant zijn aangemeld bij het nieuwe of bestaande SaaS-account via Azure Active Directory (SSO) eenmalige aanmelding (Azure AD).

De uitgever moet SSO implementeren om de gebruikers ervaring te bieden die micro soft voor deze stroom vereist. Zorg ervoor dat u de multi tenant Azure AD-toepassing gebruikt en zowel werk-als school accounts of persoonlijke micro soft-accounts wilt toestaan bij het configureren van SSO. Deze vereiste geldt alleen voor de landings pagina voor gebruikers die worden omgeleid naar de SaaS-service wanneer ze zich al hebben aangemeld met micro soft-referenties. SSO is niet vereist voor alle aanmeldingen bij de SaaS-service.

> [!NOTE]
>Als voor SSO vereist is dat een beheerder machtigingen moet verlenen voor een app, moet de beschrijving van de aanbieding in Partner Center worden vermeld dat toegang op beheerders niveau is vereist. Deze mede deling is om te voldoen aan het [beleid voor commerciële Marketplace-certificering](/legal/marketplace/certification-policies#10003-authentication-options).

Na het aanmelden moet de klant de SaaS-configuratie aan de zijkant van de uitgever volt ooien. Vervolgens moet de uitgever de [API Activate-abonnement](#activate-a-subscription) aanroepen om een signaal naar Azure Marketplace te verzenden dat het inrichten van het SaaS-account is voltooid.
Met deze actie wordt de facturerings cyclus van de klant gestart. Als het activeren van de API-aanroep van het abonnement is mislukt, wordt de klant niet gefactureerd voor de aankoop.


![Diagram met de P I-aanroepen voor een inrichtings scenario.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Actief (*geabonneerd*)

*Actief (geabonneerd)* is de stabiele status van een ingerichte SaaS-abonnement. Nadat de micro soft de API-aanroep voor het activeren van het [abonnement](#activate-a-subscription) heeft verwerkt, wordt het SaaS-abonnement als *geabonneerd* gemarkeerd. De klant kan nu de SaaS-service op de kant van de Publisher gebruiken en wordt gefactureerd.

Wanneer een SaaS-abonnement al actief is, kan de klant de functie **SaaS-ervaring beheren** selecteren in het Azure Portal of Microsoft 365 beheer centrum. Deze actie zorgt er ook voor dat micro soft de URL van de **landings pagina** aanroept met de para meter *token* , zoals in de stroom activeren. De uitgever moet onderscheid maken tussen nieuwe aankopen en het beheer van bestaande SaaS-accounts en de URL-aanroep van deze landings pagina afhandelen dienovereenkomstig.

#### <a name="being-updated-subscribed"></a>Worden bijgewerkt (*geabonneerd*)

Deze actie houdt in dat een update van een bestaand actief SaaS-abonnement wordt verwerkt door zowel micro soft als de uitgever. Een dergelijke update kan worden gestart door:

- De klant van de commerciële Marketplace.
- De CSP van de commerciële Marketplace.
- De klant van de SaaS-site van de uitgever (maar niet voor door de CSP gemaakte aankopen).

Er zijn twee soorten updates beschikbaar voor een SaaS-abonnement:

- Plan een update uit wanneer de klant een ander abonnement kiest.
- Hoeveelheid bijwerken wanneer de klant het aantal aangeschafte seats voor het abonnement wijzigt.

Alleen een actief abonnement kan worden bijgewerkt. Terwijl het abonnement wordt bijgewerkt, blijft de status actief aan de kant van micro soft.

##### <a name="update-initiated-from-the-commercial-marketplace"></a>Update gestart vanuit de commerciële Marketplace

In deze stroom wijzigt de klant het abonnement of de hoeveelheid stoelen van de Azure Portal of Microsoft 365 beheer centrum.

1. Nadat een update is ingevoerd, roept micro soft de webhook-URL van de uitgever aan, die in het veld **verbindings webhook** in het partner centrum is geconfigureerd, met een geschikte waarde voor *actie* en andere relevante para meters. 
1. De uitgever moet de vereiste wijzigingen aanbrengen in de SaaS-service en micro soft waarschuwen wanneer dit is voltooid door de [update status van de bewerkings-API aan](#update-the-status-of-an-operation)te roepen.
1. Als de patch wordt verzonden met de status *mislukt* , wordt het update proces niet voltooid aan de kant van micro soft. Het SaaS-abonnement blijft het bestaande abonnement en de hoeveelheid stoelen.

> [!NOTE]
> De uitgever moet een PATCH aanroepen om [de status van de bewerkings-API](#update-the-status-of-an-operation) met een fout-en geslaagde reactie *binnen een periode van 10 seconden* na ontvangst van de webhook-melding bij te werken. Als de PATCH van de bewerkings status niet binnen tien seconden wordt ontvangen, wordt het wijzigings plan *automatisch als geslaagd gerepareerd*. 

In het volgende diagram ziet u de volg orde van de API-aanroepen voor een update scenario dat wordt geïnitieerd vanuit de commerciële Marketplace.

![Diagram waarin de A P I-aanroepen voor een door Marketplace geïnitieerde update worden weer gegeven.](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>Update gestart vanuit de Publisher

In deze stroom wijzigt de klant het abonnement of het aantal stoelen dat is gekocht van de SaaS-service zelf. 

1. De uitgevers code moet de API voor het [wijzigings plan](#change-the-plan-on-the-subscription) en/of de [API voor wijzigings hoeveelheid](#change-the-quantity-of-seats-on-the-saas-subscription) aanroepen voordat de aangevraagde wijziging aan de kant van de uitgever wordt aangebracht. 

1. Micro soft past de wijziging toe op het abonnement en waarschuwt de uitgever via de **verbindings-webhook** om dezelfde wijziging toe te passen.

1. Alleen dan moet de uitgever de vereiste wijziging aanbrengen in het SaaS-abonnement en micro soft op de hoogte stellen wanneer de wijziging plaatsvindt door de [update status van de bewerkings-API aan](#update-the-status-of-an-operation)te roepen.

In het volgende diagram ziet u de volg orde van de API-aanroepen voor een update scenario dat wordt gestart vanaf de uitgever.

![Diagram met de A P I-aanroepen voor een door de uitgever geïnitieerde update.](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Onderbroken (*onderbroken*)

Deze status geeft aan dat de betaling van een klant voor de SaaS-service niet is ontvangen. De uitgever ontvangt een melding van deze wijziging in de status van de SaaS-abonnementen van micro soft. De melding wordt uitgevoerd via een aanroep van webhook met de *actie* parameter ingesteld op *opgeschort*.

De uitgever kan mogelijk geen wijzigingen aanbrengen in de SaaS-service aan de kant van de uitgever. U wordt aangeraden deze informatie beschikbaar te maken voor de opgeschorte klant en limieten of om de toegang van de klant tot de SaaS-service te blok keren. Er is een kans dat de betaling nooit wordt ontvangen.

Micro soft geeft de klant een respijt periode van 30 dagen voordat het abonnement automatisch wordt geannuleerd. Wanneer een abonnement de status *onderbroken* heeft:

* De partner of ISV moet het SaaS-account in een herstel bare staat houden zodat de volledige functionaliteit kan worden hersteld zonder verlies van gegevens of instellingen.
* De partner of ISV moet een aanvraag verwachten om het abonnement opnieuw in te stellen als de betaling is ontvangen tijdens de respijt periode, of een aanvraag om het abonnement aan het einde van de respijt periode te deactiveren. Beide aanvragen worden verzonden via het mechanisme webhook.

De status van het abonnement wordt gewijzigd in opgeschort op de micro soft-website voordat de uitgever actie onderneemt. Alleen actieve abonnementen kunnen worden opgeschort.

#### <a name="reinstated-suspended"></a>Opnieuw ingesteld (*onderbroken*)

Deze actie geeft aan dat het betalings instrument van de klant opnieuw geldig is, er is een betaling gedaan voor het SaaS-abonnement en het abonnement wordt opnieuw ingesteld. In dat geval: 

1. Micro soft roept webhook aan met een *actie* parameter ingesteld op de waarde voor het opnieuw *invoeren* .
1. De uitgever zorgt ervoor dat het abonnement weer volledig operationeel is aan de kant van de uitgever.
1. De uitgever roept de [API-bewerkings-api's](#update-the-status-of-an-operation) met de status geslaagd aan.
1. Het herstelproces is geslaagd en de klant wordt opnieuw gefactureerd voor het SaaS-abonnement. 

Als de patch wordt verzonden met de status *mislukt* , wordt het herstel proces niet voltooid op de micro soft-kant en blijft het abonnement *onderbroken*.

Alleen een opgeschort abonnement kan worden hersteld. Het opgeschorte SaaS-abonnement blijft in een *onderbroken* status terwijl het wordt hersteld. Nadat deze bewerking is voltooid, wordt de status van het abonnement *actief*.

#### <a name="renewed-subscribed"></a>Vernieuwd (*geabonneerd*)

Het SaaS-abonnement wordt automatisch door micro soft vernieuwd aan het einde van de abonnements periode van een maand of een jaar. De standaard instelling voor automatisch vernieuwen is *waar* voor alle SaaS-abonnementen. Actieve SaaS-abonnementen worden nog steeds vernieuwd met een gewone uitgebracht. Micro soft waarschuwt de uitgever niet wanneer een abonnement wordt vernieuwd. Een klant kan automatische verlenging voor een SaaS-abonnement uitschakelen via de beheer portal van Microsoft 365. In dit geval wordt het SaaS-abonnement automatisch geannuleerd aan het einde van de huidige facturerings periode. Klanten kunnen het SaaS-abonnement ook op elk gewenst moment annuleren.

Alleen actieve abonnementen worden automatisch verlengd. Abonnementen blijven actief tijdens het vernieuwings proces en als de automatische verlenging slaagt. Na de verlenging worden de begin-en eind datum van de abonnements periode bijgewerkt naar de datums van de nieuwe periode.

Als een automatische vernieuwing mislukt vanwege een probleem met de betaling, wordt het abonnement *onderbroken* en wordt de uitgever hiervan op de hoogte gebracht.

#### <a name="canceled-unsubscribed"></a>Geannuleerd (*afgemeld*) 

Abonnementen bereiken deze status als reactie op een expliciete klant-of CSP-actie door de annulering van een abonnement van de Publisher-site, het Azure Portal of het beheer centrum van Microsoft 365. Een abonnement kan ook impliciet worden geannuleerd als gevolg van een niet-betaalde contributie, na 30 dagen na de *onderbroken* status.

Nadat de uitgever een aanroep van een annulerings webhook heeft ontvangen, moeten de klant gegevens voor herstel op aanvraag ten minste zeven dagen worden bewaard. Alleen vervolgens kunnen klant gegevens worden verwijderd.

Een SaaS-abonnement kan op elk gewenst moment in de levens cyclus worden geannuleerd. Nadat een abonnement is geannuleerd, kan het niet opnieuw worden geactiveerd.

## <a name="api-reference"></a>API-verwijzing

In deze sectie worden de SaaS-abonnementen en Operations Api's gedocumenteerd.

**Abonnement-api's** moeten worden gebruikt voor het afhandelen van de levens cyclus van het SaaS-abonnement van aankoop tot annulering.

**Operations api's** moeten worden gebruikt voor het volgende:

* Verifieer en bevestig de verwerkte webhook-aanroepen.
* Een lijst met apps ophalen die in behandeling zijn en die wachten op bevestiging door de uitgever.

> [!NOTE]
> De versie van de TLS-versie 1,2 wordt binnenkort afgedwongen als de minimale versie voor HTTPS-communicatie. Zorg ervoor dat u deze TLS-versie in uw code gebruikt. TLS-versies 1,0 en 1,1 zullen binnenkort worden afgeschaft.

### <a name="subscription-apis"></a>Abonnements-Api's

#### <a name="resolve-a-purchased-subscription"></a>Een gekocht abonnement oplossen

Met het eind punt voor omzetten kan de uitgever het aankoop identificatie token van de commerciële Marketplace (aangeduid als *token* in [ingekocht maar nog niet geactiveerd](#purchased-but-not-yet-activated-pendingfulfillmentstart)) uitwisselen met een permanente aangeschafte SaaS-abonnements-id en de bijbehorende gegevens.

Wanneer een klant wordt omgeleid naar de URL van de landings pagina van de partner, wordt het identificatie token van de klant door gegeven als de *token* parameter in deze URL-aanroep. De partner wordt verwacht dit token te gebruiken en een aanvraag te doen om het te verhelpen. Het antwoord op de API oplossen bevat de SaaS-abonnements-ID en andere gegevens om de aankoop uniek te identificeren. Het *token* dat is opgenomen in de URL-oproep van de landings pagina is doorgaans 24 uur geldig. Als het *token* dat u ontvangt al is verlopen, raden we u aan de volgende richt lijnen aan te bieden aan de eind gebruiker:

"We kunnen deze aankoop niet identificeren. Open dit SaaS-abonnement opnieuw in de Azure Portal of in Microsoft 365 beheer centrum en selecteer opnieuw account configureren of account beheren.

Bij het aanroepen van de API voor omzetten worden de abonnements gegevens en de status van SaaS-abonnementen geretourneerd in alle ondersteunde statussen.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Verzenden `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

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
|  `x-ms-marketplace-token`  | De para meter voor het aankoop-id- *token* dat moet worden omgezet.  Het token wordt door gegeven in de URL-oproep van de landings pagina wanneer de klant wordt omgeleid naar de website van de SaaS-partner (bijvoorbeeld: `https://contoso.com/signup?token=<token><authorization_token>` ). <br> <br>  Houd er rekening mee dat de *token* waarde die wordt gecodeerd, deel uitmaakt van de URL van de landings pagina en moet worden gedecodeerd voordat deze wordt gebruikt als een para meter in deze API-aanroep.  <br> <br> Hier volgt een voor beeld van een gecodeerde teken reeks in de URL: `contoso.com/signup?token=ab%2Bcd%2Fef` , waarbij *token* is `ab%2Bcd%2Fef` .  Hetzelfde token is gedecodeerd: `Ab+cd/ef` |
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

Code: 403 verboden. Het autorisatie token is ongeldig, verlopen of niet ingevoerd.  De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-App-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) .

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

#### <a name="activate-a-subscription"></a>Een abonnement activeren

Nadat het SaaS-account is geconfigureerd voor een eind gebruiker, moet de uitgever de API voor het activeren van abonnementen aan de micro soft-kant aanroepen.  De klant wordt niet gefactureerd tenzij deze API-aanroep is geslaagd.

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Verzenden `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Gebruik 2018-08-31.   |
| `subscriptionId` | De unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token voor commerciële Marketplace met behulp van de [API voor omzetten](#resolve-a-purchased-subscription).
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

Code: 200 het abonnement is gemarkeerd als geabonneerd op de micro soft-zijde.

Er is geen antwoord tekst voor deze aanroep.

Code: 400 ongeldige aanvraag: de validatie is mislukt.

* `planId` bestaat niet in de aanvraag lading.
* `planId` in de aanvraag lading komt niet overeen met het aantal dat is gekocht.
* `quantity` in de aanvraag lading komt niet overeen met de aangeschafte nettolading
* Het SaaS-abonnement is *geabonneerd* of *onderbroken* .

Code: 403 verboden. Het autorisatie token is ongeldig, verlopen of niet ingevoerd. De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-App-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) .

Code: 404 niet gevonden. Het SaaS-abonnement bevindt zich in een niet- *geabonneerde* staat.

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

#### <a name="get-list-of-all-subscriptions"></a>Lijst met alle abonnementen ophalen

Deze API haalt een lijst op met alle aangeschafte SaaS-abonnementen voor alle aanbiedingen die de uitgever publiceert in de commerciële Marketplace.  SaaS-abonnementen in alle mogelijke statussen worden geretourneerd. Niet-geabonneerde SaaS-abonnementen worden ook geretourneerd, omdat deze gegevens niet aan de micro soft-kant worden verwijderd.

De API retourneert gepagineerde resultaten van 100 per pagina.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Toevoegen `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

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

Code: 200 retourneert de lijst met alle bestaande abonnementen voor alle aanbiedingen die zijn gemaakt door deze uitgever, op basis van het autorisatie token van de uitgever.

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

Deze API haalt een opgegeven aangekochte SaaS-abonnement op voor een SaaS-aanbieding die de uitgever publiceert in de commerciële Marketplace. Gebruik deze aanroep om alle beschik bare informatie voor een specifiek SaaS-abonnement op te halen met de bijbehorende ID in plaats van de API aan te roepen die wordt gebruikt voor het ophalen van een lijst met alle abonnementen.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Toevoegen `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Gebruik 2018-08-31. |
| `subscriptionId`     |  De unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token voor commerciële Marketplace met behulp van de API voor omzetten. |

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

Code: 403 verboden. Het autorisatie token is ongeldig, verlopen of niet ingevoerd. De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-App-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) . 

Code: 404 niet gevonden.  SaaS-abonnement met de opgegeven `subscriptionId` kan niet worden gevonden.

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

#### <a name="list-available-plans"></a>Beschik bare abonnementen weer geven

Deze API haalt alle abonnementen op voor een SaaS-aanbieding die wordt geïdentificeerd door de `subscriptionId` van een specifieke aankoop van deze aanbieding.  Gebruik deze aanroep om een lijst op te halen met alle persoonlijke en open bare abonnementen die de ontvanger van een SaaS-abonnement kan bijwerken voor het abonnement.  De plannen die worden geretourneerd, zijn beschikbaar in dezelfde geografie als het al gekochte abonnement.

Deze aanroep retourneert een lijst met abonnementen die beschikbaar zijn voor die klant, naast het abonnement dat al is gekocht.  De lijst kan worden weer gegeven aan een eind gebruiker op de Publisher-site.  Een eind gebruiker kan het abonnement wijzigen in een van de plannen in de geretourneerde lijst.  Het is niet mogelijk om het abonnement te wijzigen in een niet in de lijst.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Toevoegen `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Gebruik 2018-08-31.  |
|  `subscriptionId`    |  De unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token voor commerciële Marketplace met behulp van de API voor omzetten. |

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

Code: 403 verboden. Het autorisatie token is ongeldig, verlopen of niet ingevoerd.  De aanvraag probeert mogelijk toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-App-ID dan die waarmee het autorisatie token is gemaakt.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) . 

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

#### <a name="change-the-plan-on-the-subscription"></a>Wijzig het abonnement

Gebruik deze API om het bestaande abonnement bij te werken dat is gekocht voor een SaaS-abonnement op een nieuw abonnement (openbaar of privé).  De uitgever moet deze API aanroepen wanneer een plan wordt gewijzigd op de Publisher-kant voor een SaaS-abonnement dat is gekocht in de commerciële Marketplace.

Deze API kan alleen worden aangeroepen voor *actieve* abonnementen.  Elk plan kan worden gewijzigd in elk ander bestaand plan (openbaar of privé), maar niet op zichzelf.  Voor privé abonnementen moet de Tenant van de klant worden gedefinieerd als onderdeel van de doel groep van het plan in het partner centrum.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Verzenden `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Gebruik 2018-08-31.  |
| `subscriptionId`     | De unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token voor commerciële Marketplace met behulp van de API voor omzetten. |

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

Code: 202 de aanvraag voor het wijzigings plan is asynchroon geaccepteerd en afgehandeld.  De partner moet de URL van de **bewerkings locatie** naar verwachting controleren om te bepalen of de aanvraag voor het wijzigings plan is geslaagd of mislukt.  Polling moet elke seconde worden uitgevoerd tot de uiteindelijke status *mislukt*, *geslaagd* of *conflict* is ontvangen voor de bewerking.  De uiteindelijke bewerkings status moet snel worden geretourneerd, maar kan in sommige gevallen enkele minuten duren.

De partner ontvangt ook webhook-melding wanneer de actie gereed is om te worden voltooid op de commerciële Marketplace-zijde.  Alleen dan moet de uitgever de planning wijzigen aan de kant van de uitgever.

*Antwoord headers:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  URL voor het ophalen van de status van de bewerking.  Bijvoorbeeld `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Code: 400 ongeldige aanvraag: validatie mislukt.

* Het nieuwe abonnement bestaat niet of is niet beschikbaar voor dit specifieke SaaS-abonnement.
* Het nieuwe abonnement is hetzelfde als het huidige abonnement.
* De status van het SaaS-abonnement is niet *geabonneerd*.
* De update bewerking voor een SaaS-abonnement is niet opgenomen in `allowedCustomerOperations` .

Code: 403 verboden. Het verificatie token is ongeldig, verlopen of niet opgegeven.  De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-App-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) .

Code: 404 niet gevonden.  Het SaaS-abonnement met `subscriptionId` kan niet worden gevonden.

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

>[!NOTE]
>Het plan of aantal stoelen kan tegelijk worden gewijzigd, niet beide.

>[!Note]
>Deze API kan pas worden aangeroepen nadat expliciete goed keuring voor de wijziging van de eind gebruiker is ontvangen.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Het aantal stoelen in het SaaS-abonnement wijzigen

Gebruik deze API voor het bijwerken (verg Roten of verkleinen) van het aantal seats dat is gekocht voor een SaaS-abonnement.  De uitgever moet deze API aanroepen wanneer het aantal seats wordt gewijzigd van de uitgever van een SaaS-abonnement dat is gemaakt in de commerciële Marketplace.

De hoeveelheid stoelen kan niet groter zijn dan de hoeveelheid die is toegestaan in het huidige plan.  In dit geval moet de uitgever het abonnement wijzigen voordat het aantal seats wordt gewijzigd.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Verzenden `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Gebruik 2018-08-31.  |
|  `subscriptionId`     | Een unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token voor commerciële Marketplace met behulp van de API voor omzetten.  |

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

Code: 202 de aanvraag voor het wijzigen van de hoeveelheid is asynchroon geaccepteerd en afgehandeld. De partner moet de URL van de **bewerkings locatie** naar verwachting controleren om te bepalen of de aanvraag voor wijzigings hoeveelheid is geslaagd of mislukt.  Polling moet elke seconde worden uitgevoerd tot de uiteindelijke status *mislukt*, *geslaagd* of *conflict* is ontvangen voor de bewerking.  De uiteindelijke bewerkings status moet snel worden geretourneerd, maar kan in sommige gevallen enkele minuten in beslag nemen.

De partner ontvangt ook webhook-melding wanneer de actie gereed is om te worden voltooid op de commerciële Marketplace-zijde.  Alleen dan moet de uitgever de hoeveelheid wijzigen aan de kant van de uitgever.

*Antwoord headers:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Koppeling naar een resource om de status van de bewerking op te halen.  Bijvoorbeeld `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Code: 400 ongeldige aanvraag: validatie mislukt.

* De nieuwe hoeveelheid is groter of lager dan de limiet van het huidige abonnement.
* De nieuwe hoeveelheid ontbreekt.
* De nieuwe hoeveelheid is hetzelfde als de huidige hoeveelheid.
* De status van het SaaS-abonnement is niet geabonneerd.
* De update bewerking voor een SaaS-abonnement is niet opgenomen in `allowedCustomerOperations` .

Code: 403 verboden.  Het autorisatie token is ongeldig, verlopen of niet ingevoerd.  De aanvraag probeert toegang te krijgen tot een abonnement dat geen deel uitmaakt van de huidige uitgever.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) . 

Code: 404 niet gevonden.  Het SaaS-abonnement met `subscriptionId` kan niet worden gevonden.

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

>[!Note]
>Alleen een plan of hoeveelheid kan in één keer worden gewijzigd, niet beide.

>[!Note]
>Deze API kan pas worden aangeroepen nadat expliciete goed keuring van de eind gebruiker is opgehaald voor de wijziging.

#### <a name="cancel-a-subscription"></a>Een abonnement annuleren

Gebruik deze API om een opgegeven SaaS-abonnement op te zeggen.  De uitgever hoeft deze API niet te gebruiken en wij raden aan dat klanten naar de commerciële Marketplace worden geleid om SaaS-abonnementen te annuleren.

Als de uitgever besluit de annulering te implementeren van een SaaS-abonnement dat is gekocht in de commerciële Marketplace op de zijde van de uitgever, moet deze de API aanroepen.  Nadat deze aanroep is voltooid, wordt de status van het abonnement op de micro soft-website *afgemeld* .

De klant wordt niet gefactureerd als een abonnement wordt geannuleerd binnen de volgende respijt perioden:

* Eenentwintig vier uur voor een maandelijks abonnement na activering.
* Veer tien dagen voor een jaar abonnement na activering.

De klant wordt gefactureerd als een abonnement na de vorige respijt periode wordt geannuleerd.  De klant verliest direct na het annuleren toegang tot het SaaS-abonnement op de micro soft-zijde. 

##### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Verwijderd `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Gebruik 2018-08-31.  |
|  `subscriptionId`     | De unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token voor commerciële Marketplace met behulp van de API voor omzetten.  |

*Aanvraag headers:*
 
|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Een unieke teken reeks waarde voor het bijhouden van de aanvraag van de client, bij voor keur een GUID.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers.  |
|  `x-ms-correlationid`  | Een unieke teken reeks waarde voor de bewerking op de client.  Deze para meter verbindt alle gebeurtenissen van de client bewerking met gebeurtenissen aan de server zijde.  Als deze waarde niet is opgenomen, wordt er een gegenereerd en geleverd in de antwoord headers.  |
|  `authorization`     |  Een uniek toegangs token dat de uitgever identificeert die deze API aanroept.  De indeling is `"Bearer <access_token>"` wanneer de token waarde wordt opgehaald door de uitgever, zoals wordt uitgelegd in [een Token ophalen op basis van de Azure AD-App](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Antwoord codes:*

Code: 202 de aanvraag voor het afmelden is asynchroon geaccepteerd en afgehandeld.  De partner moet de URL van de **bewerkings locatie** naar verwachting controleren om te bepalen of deze aanvraag is geslaagd of mislukt.  Polling moet elke seconde worden uitgevoerd tot de uiteindelijke status *mislukt*, *geslaagd* of *conflict* is ontvangen voor de bewerking.  De uiteindelijke bewerkings status moet snel worden geretourneerd, maar kan in sommige gevallen enkele minuten in beslag nemen.

De partner ontvangt ook webhook-meldingen wanneer de actie is voltooid op de commerciële Marketplace.  Vervolgens moet de uitgever het abonnement op de uitgever annuleren.

*Antwoord headers:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Koppeling naar een resource om de status van de bewerking op te halen.  Bijvoorbeeld `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Code: 400 ongeldige aanvraag.  Verwijderen is niet in `allowedCustomerOperations` lijst voor dit SaaS-abonnement.

Code: 403 verboden.  Het verificatie token is ongeldig, verlopen of niet beschikbaar. De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-App-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) .

Code: 404 niet gevonden.  Het SaaS-abonnement met `subscriptionId` kan niet worden gevonden.

Code: 500 interne server fout. Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

### <a name="operations-apis"></a>Operations-Api's

#### <a name="list-outstanding-operations"></a>Openstaande bewerkingen weer geven 

Haal een lijst op met de bewerkingen die in behandeling zijn voor het opgegeven SaaS-abonnement.  De uitgever moet geretourneerde bewerkingen bevestigen door de [API voor de bewerkings patch](#update-the-status-of-an-operation)aan te roepen.

Op dit moment worden alleen de **bewerkingen** voor deze API-aanroep geretourneerd als reactie.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Toevoegen `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Gebruik 2018-08-31.         |
|    `subscriptionId` | De unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token voor commerciële Marketplace met behulp van de API voor omzetten.  |

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

Code: 403 verboden. Het autorisatie token is ongeldig, verlopen of niet ingevoerd.  De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-App-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) . 

Code: 404 niet gevonden.  Het SaaS-abonnement met `subscriptionId` kan niet worden gevonden.

Code: 500 interne server fout. Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

#### <a name="get-operation-status"></a>Bewerkings status ophalen

Met deze API kan de uitgever de status van de opgegeven async-bewerking bijhouden:  **Afmelden**, **ChangePlan** of **ChangeQuantity**.

De `operationId` voor deze API-aanroep kan worden opgehaald uit de waarde die wordt geretourneerd door de **bewerkings locatie**, de aanroepen van de API Get pending Operations of de `<id>` parameter waarde ontvangen in een webhook-aanroep.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Toevoegen `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Gebruik 2018-08-31.  |
|  `subscriptionId`    |  De unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token voor commerciële Marketplace met behulp van de API voor omzetten. |
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
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

Code: 403 verboden. Het autorisatie token is ongeldig, verlopen of niet ingevoerd.  De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-App-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) . 

Code: 404 niet gevonden.  

* Kan het abonnement `subscriptionId` niet vinden.
* De bewerking `operationId` kan niet worden gevonden.

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

#### <a name="update-the-status-of-an-operation"></a>De status van een bewerking bijwerken

Gebruik deze API om de status van een in behandeling zijnde bewerking bij te werken om aan te geven dat het slagen of mislukken van de bewerking aan de kant van de uitgever is.

De `operationId` voor deze API-aanroep kan worden opgehaald uit de waarde die wordt geretourneerd door de **bewerkings locatie**, de aanroepen van de API Get pending Operations of de `<id>` parameter waarde ontvangen in een webhook-aanroep.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Verzenden `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Query parameters:*

|  Parameter         | Waarde             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Gebruik 2018-08-31.  |
|   `subscriptionId`   |  De unieke id van het aangeschafte SaaS-abonnement.  Deze ID wordt verkregen na het oplossen van het autorisatie token voor commerciële Marketplace met behulp van de API voor omzetten.  |
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

Code: 403
- Slag.  Het autorisatie token is niet beschikbaar, is ongeldig of is verlopen. De aanvraag probeert mogelijk toegang te krijgen tot een abonnement dat geen deel uitmaakt van de huidige uitgever.
- Slag.  Het autorisatie token is ongeldig, verlopen of niet ingevoerd.  De aanvraag probeert toegang te krijgen tot een SaaS-abonnement voor een aanbieding die is gepubliceerd met een andere Azure AD-App-ID dan de versie die is gebruikt om het autorisatie token te maken.

Deze fout is vaak een symptoom van het niet juist uitvoeren van de [SaaS-registratie](pc-saas-registration.md) .

Code: 404 niet gevonden.

* Kan het abonnement `subscriptionId` niet vinden.
* De bewerking `operationId` kan niet worden gevonden.

Code: 409 conflict.  Er is bijvoorbeeld al aan een nieuwere update voldaan.

Code: 500 interne server fout.  Voer de API-aanroep opnieuw uit.  Neem contact op met [micro soft ondersteuning](https://partner.microsoft.com/support/v2/?stage=1)als de fout zich blijft voordoen.

## <a name="implementing-a-webhook-on-the-saas-service"></a>Een webhook implementeren op de SaaS-service

Bij het maken van een transactable SaaS-aanbieding in het partner centrum biedt de partner de URL van de **verbindings-webhook** die moet worden gebruikt als een http-eind punt.  Deze webhook wordt door micro soft aangeroepen met behulp van de POST HTTP-aanroep om de uitgever van de volgende gebeurtenissen op de micro soft-website op de hoogte te stellen:

* Wanneer het SaaS-abonnement de status *geabonneerd* heeft:
    * ChangePlan 
    * ChangeQuantity
    * Onderbreken
    * Afmelden
* Wanneer SaaS-abonnement de status *opgeschort* heeft:
    * Opnieuw invoeren
    * Afmelden

De uitgever moet een webhook implementeren in de SaaS-service om de SaaS-abonnements status consistent te laten zijn met de micro soft-zijde.  De SaaS-service is vereist voor het aanroepen van de API Get Operation om de webhook-aanroep-en payload-gegevens te valideren en te autoriseren voordat u actie onderneemt op basis van de webhook-melding.  De uitgever moet HTTP 200 terugsturen naar micro soft zodra de webhook-aanroep wordt verwerkt.  Met deze waarde wordt bevestigd dat de webhook-aanroep is ontvangen door de uitgever.

>[!Note]
>De webhook-URL-service moet 24x7 actief zijn, en u kunt op elk moment nieuwe oproepen ontvangen van micro soft-tijd.  Micro soft heeft een beleid voor opnieuw proberen voor de webhook-aanroep (500 nieuwe pogingen van meer dan acht uur), maar als de uitgever de aanroep niet accepteert en een antwoord retourneert, zal de bewerking die webhook waarschuwt uiteindelijk mislukken aan de kant van micro soft.

*Voor beelden van payload van webhook:*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
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
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
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

Om het ontwikkel proces te starten, raden we aan om dummy API-antwoorden te maken aan de kant van de uitgever.  Deze antwoorden kunnen worden gebaseerd op voorbeeld reacties die in dit artikel worden vermeld.

Wanneer de uitgever klaar is voor het end-to-end testen:

* Publiceer een SaaS-aanbieding naar een beperkte preview-doel groep en bewaar deze in de preview-fase.
* Stel de prijs van het abonnement in op 0, om te voor komen dat er daad werkelijke facturerings kosten worden geactiveerd tijdens het testen.  Een andere mogelijkheid is om een prijs die niet gelijk is aan nul in te stellen en alle test aankopen binnen 24 uur te annuleren.
* Zorg ervoor dat alle stromen end-to-end worden aangeroepen om een echt klant scenario te simuleren.
* Als de partner volledige aankoop-en facturerings stroom wil testen, doet u dit met een prijs van meer dan $0.  De aankoop wordt gefactureerd en er wordt een factuur gegenereerd.

Een aankoop stroom kan worden geactiveerd vanaf de Azure Portal-of Microsoft AppSource-sites, afhankelijk van waar de aanbieding wordt gepubliceerd.

Acties voor het wijzigen van de *planning*, het wijzigen van de *hoeveelheid* en het *Afmelden* van het abonnement worden getest vanuit de uitgever.  Op de micro soft-website kunt u het *abonnement opzeggen* door de Azure Portal en het beheer centrum (de portal waar Microsoft AppSource aankopen worden beheerd) te activeren.  Het wijzigen van de *hoeveelheid en het abonnement* kan alleen worden geactiveerd vanuit het beheer centrum.

## <a name="get-support"></a>Ondersteuning krijgen

Zie [ondersteuning voor het programma voor commerciële Marketplace in het partner centrum](../support.md) voor ondersteunings opties voor Publisher.

## <a name="next-steps"></a>Volgende stappen

Zie de [commerciële Marketplace meter service-api's](marketplace-metering-service-apis.md) voor meer opties voor SaaS-aanbiedingen in de commerciële Marketplace.

Controleer en gebruik de [clients voor verschillende programmeer talen en voor beelden](https://github.com/microsoft/commercial-marketplace-samples).
