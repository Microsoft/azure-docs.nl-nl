---
title: Hand leiding voor Azure Relay Hybride verbindingen Protocol | Microsoft Docs
description: In dit artikel wordt de interactie aan de client zijde beschreven met de Hybride verbindingen relay voor het verbinden van clients in de rollen listener en Sender.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8a812aa401077b81934d89ada99cf1dc312d8dbc
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862323"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure Relay Hybride verbindingen-Protocol

Azure Relay is een van de belangrijkste mogelijkheden van pijlers van het Azure Service Bus platform. De nieuwe _hybride verbindingen_ mogelijkheid van relay is een veilige, open-protocol ontwikkeling op basis van http en websockets. Het vervangt de voormalige, even _BizTalk Services_ -functie die is gebouwd op basis van een eigen protocol Foundation. De integratie van Hybride verbindingen in Azure-app Services blijft functioneren als-is.

Hybride verbindingen maakt bidirectionele, binaire stroom communicatie en eenvoudige data gram stroom tussen twee toepassingen in een netwerk mogelijk. Een of beide partijen kunnen zich achter Nat's of firewalls bevinden.

In dit artikel wordt de interactie aan de client zijde beschreven met de Hybride verbindingen relay voor het verbinden van clients in de rollen listener en Sender. Ook wordt beschreven hoe listeners nieuwe verbindingen en aanvragen accepteren.

## <a name="interaction-model"></a>Interactie model

De Hybride verbindingen relay verbindt twee partijen door een rendez-punt op te geven in de Azure-Cloud die partijen kunnen ontdekken en er verbinding mee maken vanuit hun eigen perspectief van het netwerk. Het rendez-punt wordt ' hybride verbinding ' genoemd in deze en andere documentatie in de Api's, en ook in de Azure Portal. Voor de rest van dit artikel wordt het eind punt van de Hybride verbindingen service als ' service ' genoemd.

Met deze service kunnen WebSocket verbindingen en HTTP (S)-aanvragen en-antwoorden worden doorgestuurd.

Het interactie model is gebaseerd op de nomenclatuur die is vastgesteld door veel andere netwerk-Api's. Er is een listener die de gereedheid voor het afhandelen van binnenkomende verbindingen aangeeft en deze vervolgens accepteert wanneer ze binnenkomen. Aan de andere kant maakt een client verbinding met de listener, waarbij wordt verwacht dat deze verbinding moet worden geaccepteerd om een bidirectionele communicatie traject tot stand te brengen. ' Connect, ' Listen ' en ' Accept ' zijn dezelfde voor waarden die u vindt in de meeste socket-Api's.

Elk doorgestuurd communicatie model heeft een van beide partijen uitgaande verbindingen met een service-eind punt maken. Dit maakt de ' listener ' ook een ' client ' in colloquial-gebruik en kan ook andere terminologie overladen veroorzaken. De exacte terminologie die daarom wordt gebruikt voor Hybride verbindingen is als volgt:

De Program ma's aan beide zijden van een verbinding worden ' clients ' genoemd, omdat ze clients zijn voor de service. De client die wacht op en accepteert verbindingen is de ' listener ' of wordt aangeduid als de ' listener-rol '. De client die een nieuwe verbinding via de service initieert naar een listener, wordt de ' afzender ' genoemd, of is de rol ' afzender '.

### <a name="listener-interactions"></a>Listener-interacties

De listener heeft vijf interactie met de service. alle bedradings gegevens worden verderop in dit artikel beschreven in het gedeelte referentie.

De berichten Luis teren, accepteren en aanvragen worden ontvangen van de service. De vernieuwings-en ping-bewerkingen worden verzonden door de listener.

#### <a name="listen-message"></a>Luister bericht

Er wordt een uitgaande WebSocket-verbinding gemaakt om de gereedheid aan te geven voor de service die een listener gereed is om verbindingen te accepteren. De verbindings-Handshake bevat de naam van een hybride verbinding die is geconfigureerd voor de relay-naam ruimte en een beveiligings token dat de ' Luis teren ' recht op die naam heeft.

Wanneer de WebSocket door de service wordt geaccepteerd, wordt de registratie voltooid en wordt de ingestelde WebSocket actief gehouden als ' besturings kanaal ' voor het inschakelen van alle volgende interacties. Met de service kunnen Maxi maal 25 gelijktijdige listeners voor één hybride verbinding worden gemaakt. Het quotum voor AppHooks moet worden bepaald.

Voor Hybride verbindingen, als er twee of meer actieve listeners zijn, worden binnenkomende verbindingen in wille keurige volg orde verdeeld. Er is een poging tot eerlijke distributie met de beste poging.

#### <a name="accept-message"></a>Bericht accepteren

Wanneer een afzender een nieuwe verbinding met de service opent, kiest en ontvangt de service een van de actieve listeners op de hybride verbinding. Deze melding wordt verzonden naar de listener via het open besturings kanaal als een JSON-bericht. Het bericht bevat de URL van het WebSocket-eind punt waarmee de listener verbinding moet maken om de verbinding te accepteren.

De URL kan en moet rechtstreeks worden gebruikt door de listener zonder extra werk.
De versleutelde informatie is alleen geldig voor een korte periode, al naar gelang de verzender bereid is te wachten tot de verbinding is ingesteld op end-to-end. Het maximum aantal moet worden verondersteld, is 30 seconden. De URL kan alleen worden gebruikt voor een geslaagde verbindings poging. Zodra de WebSocket-verbinding met de URL van rendez-vous tot stand is gebracht, wordt alle verdere activiteiten op deze WebSocket door gegeven van en naar de afzender. Dit gebeurt zonder tussen komst of interpretatie van de service.

### <a name="request-message"></a>Aanvraag bericht

Naast WebSocket-verbindingen kan de listener ook HTTP-aanvraag frames van een afzender ontvangen, als deze mogelijkheid expliciet is ingeschakeld voor de hybride verbinding.

Listeners die aan Hybride verbindingen zijn gekoppeld met HTTP-ondersteuning, moeten de penbeweging afhandelen `request` . Een listener die geen afhandelt `request` en daarom herhaalde time-outfouten veroorzaakt tijdens de verbinding, wordt mogelijk in de toekomst geblokkeerd door de service.

Meta gegevens van HTTP-frame header worden omgezet in JSON voor eenvoudiger verwerking door het listener-Framework, omdat het parseren van de HTTP-header bibliotheken rarer dan JSON-parsers is. HTTP-meta gegevens die alleen relevant zijn voor de relatie tussen de afzender en de relay HTTP-gateway, inclusief verificatie gegevens, worden niet doorgestuurd. HTTP-aanvraag teksten worden transparant overgebracht als binaire WebSocket-frames.

De listener kan reageren op HTTP-aanvragen met een equivalente respons-beweging.

De aanvraag/antwoord stroom maakt standaard gebruik van het besturings kanaal, maar kan indien nodig worden bijgewerkt naar een andere rendez-WebSocket. DISTINCT-WebSocket-verbindingen verbeteren de door Voer voor elke client conversatie, maar ze belasten de listener met meer verbindingen die moeten worden verwerkt, hetgeen mogelijk niet geschikt is voor licht gewicht clients.

In het besturings kanaal zijn de aanvraag-en antwoord teksten beperkt tot Maxi maal 64 kB. Meta gegevens van HTTP-header zijn beperkt tot een totaal van 32 kB. Als de aanvraag of het antwoord de drempel waarde overschrijdt, moet de listener een upgrade uitvoeren naar een WebSocket van rendez-out met behulp van een penbewegings equivalent voor het afhandelen van de [acceptatie](#accept-message).

Voor aanvragen bepaalt de service of aanvragen worden gerouteerd via het besturings kanaal. Dit omvat, maar is mogelijk niet beperkt tot gevallen waarin een aanvraag groter is dan 64 kB (kopteksten en hoofd tekst), of als de aanvraag wordt verzonden met [' gesegmenteerde ' overdracht-encoding](https://tools.ietf.org/html/rfc7230#section-4.1) en de service heeft een reden om te voor komen dat de aanvraag groter is dan 64 kB of het lezen van de aanvraag is niet onmiddellijk. Als de service kiest voor het leveren van de aanvraag via rendez-vous, wordt alleen het adres van de rendez door gegeven aan de listener.
De listener moet vervolgens de WebSocket van rendez-to-the-service tot stand brengen en de volledige aanvraag wordt via de WebSocket van het rendez-upvenster geleverd. Het antwoord moet ook de WebSocket rendez gebruiken.

Voor aanvragen die via het besturings kanaal binnenkomen, bepaalt de listener of er moet worden gereageerd via het besturings kanaal of via rendez. De service moet een adres voor rendez-out bevatten waarbij elke aanvraag wordt gerouteerd via het besturings kanaal. Dit adres is alleen geldig voor het uitvoeren van een upgrade van de huidige aanvraag.

Als de listener kiest voor een upgrade, wordt er verbinding gemaakt met en wordt het antwoord via de ingestelde rendez van het programma voor het afleveren van de server.

Zodra de rendez-WebSocket is ingesteld, moet de listener deze behouden voor verdere verwerking van aanvragen en antwoorden van dezelfde client. De-service houdt de WebSocket voor zolang de HTTPS-socket verbinding met de verzender persistent is en routert alle volgende aanvragen van die afzender via de onderhouden WebSocket. Als de listener ervoor kiest om de rendez-WebSocket van de zijkant te verwijderen, wordt de verbinding met de verzender ook ongedaan gemaakt, ongeacht of een volgende aanvraag mogelijk al wordt uitgevoerd.

#### <a name="renew-operation"></a>Bewerking vernieuwen

Het beveiligings token dat moet worden gebruikt voor het registreren van de listener en het onderhouden van het besturings kanaal kan verlopen wanneer de listener actief is. Het token verloop heeft geen invloed op actieve verbindingen, maar dit heeft tot gevolg dat het besturings kanaal wordt verwijderd door de service op of kort na het tijdstip van de verloop datum. De bewerking ' renew ' is een JSON-bericht dat de listener kan verzenden om het token te vervangen dat is gekoppeld aan het besturings kanaal, zodat het besturings kanaal kan worden onderhouden voor langere Peri Oden.

#### <a name="ping-operation"></a>Ping-bewerking

Als het besturings kanaal gedurende een lange periode niet actief blijft, worden tussenliggende verbindingen, zoals load balancers of Nat's, de TCP-verbinding. Met de bewerking ' ping ' wordt voor komen dat er een kleine hoeveelheid gegevens op het kanaal wordt verzonden waarmee iedereen op de netwerk route wordt geherinnert dat de verbinding actief moet zijn en ook als een ' live ' test voor de listener. Als de ping mislukt, moet het besturings kanaal als onbruikbaar worden beschouwd en moet de listener opnieuw verbinding maken.

### <a name="sender-interaction"></a>Interactie van de afzender

De afzender heeft twee interactie met de service: er wordt verbinding gemaakt met een web-socket of aanvragen worden verzonden via HTTPS. Aanvragen kunnen niet via een web-socket worden verzonden vanuit de rol afzender.

#### <a name="connect-operation"></a>Verbindings bewerking

Met de bewerking ' verbinden ' wordt een WebSocket voor de service geopend, waarbij de naam van de hybride verbinding wordt opgegeven en (optioneel, maar standaard vereist) een beveiligings token dat de machtiging ' verzenden ' in de query teken reeks moet worden verleend. De service communiceert vervolgens met de listener volgens de eerder beschreven manier en de listener maakt een rendez-verbinding die is gekoppeld aan deze WebSocket. Nadat de WebSocket is geaccepteerd, zijn alle verdere interacties voor die WebSocket met een verbonden listener.

#### <a name="request-operation"></a>Aanvraag bewerking

Voor Hybride verbindingen waarvoor de functie is ingeschakeld, kan de afzender voornamelijk onbeperkte HTTP-aanvragen verzenden naar listeners.

Met uitzonde ring van het toegangs token voor relay dat is inge sloten in de query teken reeks of in een HTTP-header van de aanvraag, is de relay volledig transparant voor alle HTTP-bewerkingen op het omleidings adres en alle achtervoegsels van het pad naar de relay, waardoor de listener volledig beheer van end-to-end-autorisatie en zelfs HTTP-extensie functies als [CORS](https://www.w3.org/TR/cors/)heeft.

Sender Authorization with het relay-eind punt is standaard ingeschakeld, maar is optioneel. De eigenaar van de hybride verbinding kan ervoor kiezen anonieme afzenders toe te staan. Met de service worden verificatie gegevens als volgt onderschept, geïnspecteerd en gesegmenteerd:

1. Als de query reeks een `sb-hc-token` expressie bevat, wordt de expressie altijd verwijderd uit de query reeks. Deze wordt geëvalueerd als relay-autorisatie is ingeschakeld.
2. Als de aanvraag headers een `ServiceBusAuthorization` kop bevatten, wordt de header-expressie altijd verwijderd uit de verzameling koptekst.
   Deze wordt geëvalueerd als relay-autorisatie is ingeschakeld.
3. Alleen als relay-autorisatie is ingeschakeld en de aanvraag headers een `Authorization` header bevatten en geen van de voor gaande expressies aanwezig is, wordt de header geëvalueerd en verwijderd. Anders wordt de `Authorization` wordt altijd door gegeven als.

Als er geen actieve listener is, retourneert de service de fout code ' ongeldige gateway van 502 '. Als de service niet wordt weer gegeven om de aanvraag af te handelen, retourneert de service na 60 seconden een time-out voor de ' gateway van een 504 '.

### <a name="interaction-summary"></a>Samen vatting van interactie

Het resultaat van dit interactie model is dat de Sender-client uit de handshake komt met een ' schone ' WebSocket, die is verbonden met een listener en die geen verdere voor raden of voor bereiding nodig heeft. Dit model maakt nagenoeg alle bestaande WebSocket-client implementaties eenvoudig voor deel van de Hybride verbindingen-service door een correct geconstrueerde URL op te geven in de WebSocket-client laag.

De WebSocket van de rendez-verbinding die de listener via de acceptatie interactie verkrijgt, wordt ook gereinigd en kan worden overgedragen aan een bestaande WebSocket-Server implementatie met een minimale extra abstractie die onderscheidt tussen de ' Accept '-bewerkingen op de lokale netwerklisteners van het Framework en Hybride verbindingen externe "Accept"-bewerkingen.

Het HTTP-aanvraag/antwoord model geeft de afzender een grotendeels onbeperkt HTTP-protocol surface area met een optionele autorisatie laag. De listener haalt een vooraf geparseerde header van een HTTP-aanvraag op die kan worden omgezet in een downstream HTTP-aanvraag of kan worden verwerkt als is, met binaire frames die HTTP-instanties uitvoeren. Antwoorden gebruiken dezelfde indeling. Interacties met minder dan 64 KB aan aanvraag-en antwoord tekst kunnen worden verwerkt via één web-socket die wordt gedeeld voor alle afzenders. Grotere aanvragen en antwoorden kunnen worden verwerkt met behulp van het model rendez.

## <a name="protocol-reference"></a>Naslag informatie over protocollen

In deze sectie worden de details beschreven van de protocol interacties die eerder zijn beschreven.

Alle WebSocket-verbindingen worden gemaakt op poort 443 als een upgrade van HTTPS 1,1, die meestal wordt afgeleid door een WebSocket-Framework of API. De beschrijving is hier niet van toepassing onafhankelijk, zonder dat een specifiek Framework wordt voorgesteld.

### <a name="listener-protocol"></a>Listener-Protocol

Het listener-protocol bestaat uit twee verbindings bewegingen en drie bericht bewerkingen.

#### <a name="listener-control-channel-connection"></a>Listener-kanaal verbinding

Het besturings kanaal wordt geopend met het maken van een WebSocket-verbinding naar:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

De `namespace-address` is de Fully Qualified Domain name van de Azure relay naam ruimte die als host fungeert voor de hybride verbinding, doorgaans van het formulier `{myname}.servicebus.windows.net` .

U hebt de volgende opties voor de query reeks parameter.

| Parameter        | Vereist | Beschrijving
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Ja      | Voor de listener-functie moet de para meter **SB-HC-Action = listen**
| `{path}`         | Yes      | Het pad naar de URL-code ring van de vooraf geconfigureerde hybride verbinding om deze listener te registreren. Deze expressie wordt toegevoegd aan het gedeelte vast `$hc/` pad.
| `sb-hc-token`    | Yes\*    | De listener moet een geldig, URL-gecodeerd Service Bus token voor gedeelde toegang opgeven voor de naam ruimte of hybride verbinding die het **Geluisterde** recht verleent.
| `sb-hc-id`       | No       | Met deze door de client verstrekte optionele ID kan end-to-end diagnostische tracering worden ingeschakeld.

Als de WebSocket-verbinding mislukt omdat het Hybrid Connection-pad niet is geregistreerd, of een ongeldig of ontbrekend token of een andere fout, wordt de fout feedback weer gegeven met het reguliere HTTP 1,1-status feedback model. De status beschrijving bevat een fout tracering-id die kan worden gecommuniceerd naar ondersteunings personeel van Azure:

| Code | Fout          | Beschrijving
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Niet gevonden      | Het Hybrid Connection-pad is ongeldig of de basis-URL is onjuist gevormd.
| 401  | Niet geautoriseerd   | Het beveiligings token ontbreekt of is onjuist of ongeldig.
| 403  | Verboden      | Het beveiligings token is niet geldig voor dit pad voor deze actie.
| 500  | Interne fout | Er is iets verkeerd gegaan in de service.

Als de WebSocket-verbinding opzettelijk wordt afgesloten door de service nadat deze voor het eerst is ingesteld, wordt de reden hiervoor gecommuniceerd met behulp van de juiste WebSocket protocol-fout code en een beschrijving van het fout bericht dat ook een tracerings-ID bevat. De service sluit het besturings kanaal niet af zonder dat er een fout optreedt. Een schone afsluiting wordt door de client beheerd.

| WS-status | Description
| --------- | -------------------------------------------------------------------------------
| 1001      | Het hybride verbindings traject is verwijderd of uitgeschakeld.
| 1008      | Het beveiligings token is verlopen, daarom wordt het autorisatie beleid geschonden.
| 1011      | Er is iets verkeerd gegaan in de service.

#### <a name="accept-handshake"></a>Handshake accepteren

Het bericht ' Accept ' wordt door de service verzonden naar de listener voor het eerder vastgelegde besturings kanaal als JSON-bericht in een WebSocket-tekst kader. Er is geen antwoord op dit bericht.

Het bericht bevat een JSON-object met de naam ' Accept ', waarmee de volgende eigenschappen op dit moment worden gedefinieerd:

* **adres** : de URL-teken reeks die moet worden gebruikt voor het tot stand brengen van de WebSocket in de service om een binnenkomende verbinding te accepteren.
* **id** : de unieke id voor deze verbinding. Als de ID is opgegeven door de verzender-client, is het de opgegeven waarde voor de afzender, anders is het een door het systeem gegenereerde waarde.
* **connectHeaders** : alle HTTP-headers die zijn verstrekt aan het relay-eind punt door de afzender. Dit omvat ook de headers SEC-WebSocket-protocol en SEC-WebSocket-extensies.

```json
{
    "accept" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?..."
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",
        "connectHeaders" : {
            "Host" : "...",
            "Sec-WebSocket-Protocol" : "...",
            "Sec-WebSocket-Extensions" : "..."
        }
     }
}
```

De adres-URL die in het JSON-bericht wordt gegeven, wordt door de listener gebruikt voor het instellen van de WebSocket voor het accepteren of afwijzen van de afzender-socket.

##### <a name="accepting-the-socket"></a>De socket wordt geaccepteerd

Om te accepteren, brengt de listener een WebSocket-verbinding tot stand met het gegeven adres.

Als het bericht ' Accept ' een `Sec-WebSocket-Protocol` header bevat, wordt verwacht dat de listener alleen de WebSocket accepteert als dat protocol wordt ondersteund. Daarnaast wordt de koptekst ingesteld als de WebSocket is ingesteld.

Hetzelfde geldt voor de `Sec-WebSocket-Extensions` koptekst. Als het Framework een uitbrei ding ondersteunt, moet de header worden ingesteld op het antwoord aan de server zijde van de vereiste `Sec-WebSocket-Extensions` Handshake voor de uitbrei ding.

De URL moet worden gebruikt als-is voor het instellen van de Accept-socket, maar bevat de volgende para meters:

| Parameter      | Vereist | Beschrijving
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ja      | Voor het accepteren van een socket moet de para meter `sb-hc-action=accept`
| `{path}`       | Yes      | (Zie de volgende alinea)
| `sb-hc-id`     | No       | Zie de vorige beschrijving van de **id**.

`{path}` is het URL-gecodeerde pad naar de naam ruimte van de vooraf geconfigureerde hybride verbinding waarop deze listener moet worden geregistreerd. Deze expressie wordt toegevoegd aan het gedeelte vast `$hc/` pad.

De `path` expressie kan worden uitgebreid met een achtervoegsel en een query reeks expressie die na de geregistreerde naam volgt na een gescheiden slash.
Hierdoor kan de Sender-client verzend argumenten door geven aan de geaccepteerde listener wanneer het niet mogelijk is HTTP-headers op te nemen. De verwachting is dat het listener-Framework het vaste pad en de geregistreerde naam van het pad parseert en dat de rest, eventueel zonder query teken reeks argumenten vooraf vastgesteld door `sb-` , beschikbaar is voor de toepassing om te bepalen of de verbinding moet worden geaccepteerd.

Zie de sectie ' Sender Protocol ' voor meer informatie.

Als er een fout optreedt, kan de service als volgt reageren:

| Code | Fout          | Beschrijving
| ---- | -------------- | -----------------------------------
| 403  | Verboden      | De URL is ongeldig.
| 500  | Interne fout | Er is iets verkeerd gegaan in de service

 Nadat de verbinding tot stand is gebracht, wordt de WebSocket door de server uitgeschakeld wanneer de WebSocket van de afzender wordt uitgeschakeld of met de volgende status:

| WS-status | Description                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | De Sender client sluit de verbinding.                                    |
| 1001      | Het hybride verbindings traject is verwijderd of uitgeschakeld.                        |
| 1008      | Het beveiligings token is verlopen, daarom wordt het autorisatie beleid geschonden. |
| 1011      | Er is iets verkeerd gegaan in de service.                                            |

##### <a name="rejecting-the-socket"></a>De socket weigeren

 Voor het afwijzen van de socket na het inspecteren `accept` van het bericht is een vergelijk bare Handshake vereist zodat de status code en beschrijving die de reden voor de weigering door gegeven, naar de afzender kunnen stromen.

 De keuze van het gekozen protocol is door gebruik te maken van een WebSocket-Handshake (die is ontworpen om te eindigen met een gedefinieerde fout status), zodat listener-client implementaties kunnen blijven vertrouwen op een WebSocket-client en geen extra, bare HTTP-client hoeven te gebruiken.

 Om de socket af te wijzen, gebruikt de client de adres-URI uit het `accept` bericht en worden er twee query teken reeks parameters aan toegevoegd:

| Param                   | Vereist | Beschrijving                              |
| ----------------------- | -------- | ---------------------------------------- |
| SB-HC-status code        | Yes      | Numerieke HTTP-status code.                |
| SB-HC-statusDescription | Yes      | Door de mens lees bare reden voor de afwijzing. |

De resulterende URI wordt vervolgens gebruikt om een WebSocket-verbinding tot stand te brengen.

Bij een juiste voltooiing wordt deze Handshake met opzet mislukt met een HTTP-fout code 410, omdat er geen WebSocket is ingesteld. Als er iets fout gaat, beschrijven de volgende codes de fout:

| Code | Fout          | Beschrijving                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Verboden      | De URL is ongeldig.                |
| 500  | Interne fout | Er is iets verkeerd gegaan in de service. |

#### <a name="request-message"></a>Aanvraag bericht

Het `request` bericht wordt door de service naar de listener verzonden via het besturings kanaal. Hetzelfde bericht wordt ook na het tot stand brengen van de WebSocket van rendez verzonden.

De `request` bestaat uit twee delen: een koptekst en binaire hoofd frame (s).
Als er geen hoofd tekst is, worden de hoofd frames wegge laten. De indicator voor of een hoofd tekst aanwezig is, is de Booleaanse `body` eigenschap in het aanvraag bericht.

Voor een aanvraag met een aanvraag tekst kan de structuur er als volgt uitzien:

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : true,
        ...
    }
}
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame -FIN
FEFEFEFEFEFEFEFEFEFEF...
----------------------------------
```

De listener moet de ontvangst van de aanvraag tekst splitsen over meerdere binaire frames (Zie [WebSocket-fragmenten](https://tools.ietf.org/html/rfc6455#section-5.4)) afhandelen.
De aanvraag wordt beëindigd wanneer een binair frame met de set FIN Flags is ontvangen.

Er is slechts één tekst kader voor een aanvraag zonder tekst.

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : false,
        ...
    }
}
----------------------------------
```

De JSON-inhoud voor `request` is als volgt:

* **adres** -URI-teken reeks. Dit is het adres van de rendez dat voor deze aanvraag moet worden gebruikt. Als de binnenkomende aanvraag groter is dan 64 kB, wordt de rest van dit bericht leeg gelaten en moet de client een hand Shake van het rendez-vous initiëren dat overeenkomt met de `accept` onderstaande bewerking. De service plaatst vervolgens de volledige `request` op de tot stand gebrachte WebSocket. Als de reactie naar verwachting groter is dan 64 kB, moet de listener ook een Shake van het rendez-vous initiëren en vervolgens het antwoord over de ingestelde Web-socket overdragen.
* **id** : teken reeks. De unieke id voor deze aanvraag.
* **RequestHeaders** : dit object bevat alle HTTP-headers die door de afzender aan het eind punt zijn door gegeven, met uitzonde ring van autorisatie-informatie zoals [hierboven](#request-operation)beschreven en headers die strikt verband houden met de verbinding met de gateway. In het bijzonder worden alle headers die zijn gedefinieerd of gereserveerd in [RFC7230](https://tools.ietf.org/html/rfc7230), met uitzonde ring van `Via` , verwijderd en niet doorgestuurd:

  * `Connection` (RFC7230, sectie 6,1)
  * `Content-Length`  (RFC7230, sectie 3.3.2)
  * `Host`  (RFC7230, sectie 5,4)
  * `TE`  (RFC7230, sectie 4,3)
  * `Trailer`  (RFC7230, sectie 4,4)
  * `Transfer-Encoding`  (RFC7230, sectie 3.3.1)
  * `Upgrade` (RFC7230, sectie 6,7)
  * `Close`  (RFC7230, sectie 8,1)

* **requestTarget** : String. Deze eigenschap bevat de aanvraag  [doel (RFC7230, sectie 5,3)](https://tools.ietf.org/html/rfc7230#section-5.3) van de aanvraag. Dit omvat het gedeelte met de query teken reeks, dat alle `sb-hc-` voorvaste para meters bevat.
* **methode** -teken reeks. Dit is de methode van de aanvraag, per [RFC7231, sectie 4](https://tools.ietf.org/html/rfc7231#section-4). De `CONNECT` methode mag niet worden gebruikt.
* **hoofd tekst** : Booleaans. Hiermee wordt aangegeven of een of meer binaire hoofdtekst frames volgt.

``` JSON
{
    "request" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?...",
        "id" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "requestTarget" : "/abc/def?myarg=value&otherarg=...",
        "method" : "GET",
        "requestHeaders" : {
            "Host" : "...",
            "Content-Type" : "...",
            "User-Agent" : "..."
        },
        "body" : true
     }
}
```

##### <a name="responding-to-requests"></a>Reageren op aanvragen

De ontvanger moet reageren. Herhaalde fout om te reageren op aanvragen bij het onderhouden van de verbinding kan ertoe leiden dat de listener wordt geblokkeerd.

Antwoorden kunnen in een wille keurige volg orde worden verzonden, maar elke aanvraag moet binnen 60 seconden worden gereageerd of de levering wordt gerapporteerd als mislukt. De deadline van 60 seconden wordt geteld totdat het `response` frame door de service is ontvangen. Een doorlopende reactie met meerdere binaire frames kan langer dan 60 seconden niet actief worden of wordt beëindigd.

Als de aanvraag via het besturings kanaal wordt ontvangen, moet het antwoord worden verzonden op het besturings kanaal van waaruit de aanvraag is ontvangen of moet het worden verzonden via een rendez-kanaal.

Het antwoord is een JSON-object met de naam ' Response '. De regels voor het afhandelen van de inhoud van de hoofd tekst zijn precies hetzelfde als het `request` bericht en op basis van de `body` eigenschap.

* **aanvraag** -waarde – string. Vereist. De `id` eigenschaps waarde van het `request` bericht waarop wordt gereageerd.
* **status** code – number. Vereist. een numerieke HTTP-status code die het resultaat van de melding aangeeft. Alle status codes van [RFC7231, sectie 6](https://tools.ietf.org/html/rfc7231#section-6) , zijn toegestaan, met uitzonde ring van de [502 "Ongeldige gateway"](https://tools.ietf.org/html/rfc7231#section-6.6.3) en [504 "Gateway-time-out"](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **statusDescription** -teken reeks. Beschrijving. HTTP-status-woordgroepen code per [RFC7230, para graaf 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **gegevensservicehost** : http-headers die moeten worden ingesteld in een extern http-antwoord.
  Net als met de `request` , RFC7230 gedefinieerde HEADERS mogen niet worden gebruikt.
* **hoofd tekst** : Booleaans. Geeft aan of er (en) binaire tekst frames moeten worden gevolgd.

``` text
----- Web Socket text frame ----
{
    "response" : {
        "requestId" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "statusCode" : "200",
        "responseHeaders" : {
            "Content-Type" : "application/json",
            "Content-Encoding" : "gzip"
        }
         "body" : true
     }
}
----- Web Socket binary frame -FIN
{ "hey" : "mydata" }
----------------------------------
```

##### <a name="responding-via-rendezvous"></a>Reageert via rendez-vous

Voor antwoorden die 64 kB overschrijdt, moet het antwoord worden verzonden via een rendez van het andere. Als de aanvraag meer dan 64 kB overschrijdt en de `request` enige adres veld bevat, moet u een socket van rendez-vous instellen om de te verkrijgen `request` . Zodra een rendez van de client is ingesteld, moeten reacties op de respectievelijke clients en volgende aanvragen van die respectieve clients worden geleverd via de socket van de rendez-out.

De `address` URL in de `request` moet worden gebruikt als-is voor het tot stand brengen van de disrendez-socket, maar bevat de volgende para meters:

| Parameter      | Vereist | Beschrijving
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ja      | Voor het accepteren van een socket moet de para meter `sb-hc-action=request`

Als er een fout optreedt, kan de service als volgt reageren:

| Code | Fout           | Beschrijving
| ---- | --------------- | -----------------------------------
| 400  | Ongeldige aanvraag | Niet-herkende actie of URL niet geldig.
| 403  | Verboden       | De URL is verlopen.
| 500  | Interne fout  | Er is iets verkeerd gegaan in de service

 Nadat de verbinding tot stand is gebracht, wordt de WebSocket door de server uitgeschakeld wanneer de HTTP-socket van de client wordt uitgeschakeld of met de volgende status:

| WS-status | Description                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | De Sender client sluit de verbinding.                                    |
| 1001      | Het hybride verbindings traject is verwijderd of uitgeschakeld.                        |
| 1008      | Het beveiligings token is verlopen, daarom wordt het autorisatie beleid geschonden. |
| 1011      | Er is iets verkeerd gegaan in de service.                                            |


#### <a name="listener-token-renewal"></a>Listener-token vernieuwing

Wanneer het listener-token bijna is verlopen, kan het worden vervangen door een tekst frame bericht naar de service te verzenden via het ingestelde besturings kanaal. Het bericht bevat een JSON-object met `renewToken` de naam, waarmee de volgende eigenschap op dit moment wordt gedefinieerd:

* **token** : een geldig, URL-gecodeerd service bus token voor gedeelde toegang voor de naam ruimte of hybride verbinding die het **geluisterde** recht verleent.

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Als de token validatie mislukt, wordt de toegang geweigerd en sluit de Cloud service de WebSocket van het besturings kanaal met een fout. Anders is er geen antwoord.

| WS-status | Description                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Het beveiligings token is verlopen, daarom wordt het autorisatie beleid geschonden. |

### <a name="web-socket-connect-protocol"></a>Web socket Connect-protocol

Het Protocol van de afzender is in feite hetzelfde als de manier waarop een listener tot stand is gebracht.
Het doel is de maximale doorzichtigheid voor de end-to-end WebSocket. Het adres waarmee verbinding moet worden gemaakt, is hetzelfde als voor de listener, maar de actie verschilt en het token heeft een andere machtiging nodig:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sb-hc-token=...
```

De _naam ruimte-adres_ is de Fully Qualified Domain name van de Azure relay naam ruimte die als host fungeert voor de hybride verbinding, doorgaans van het formulier `{myname}.servicebus.windows.net` .

De aanvraag kan wille keurige extra HTTP-headers bevatten, inclusief door de toepassing gedefinieerde items. Alle opgegeven headers Flow naar de listener en kunnen worden gevonden op het `connectHeader` object van het **Accept** Control-bericht.

U hebt de volgende opties voor de query teken reeks parameter:

| Param          | Vereist? | Beschrijving
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Ja       | Voor de rol afzender moet de para meter zijn `sb-hc-action=connect` .
| `{path}`       | Yes       | (Zie de volgende alinea)
| `sb-hc-token`  | Yes\*     | De listener moet een geldig, URL-gecodeerd Service Bus token voor gedeelde toegang opgeven voor de naam ruimte of hybride verbinding die het **Verzend** recht verleent.
| `sb-hc-id`     | No        | Een optionele ID waarmee end-to-end diagnostische tracering is ingeschakeld en beschikbaar wordt gesteld aan de listener tijdens het accepteren van de handshake.

 Het `{path}` is het pad naar de URL-code naam ruimte van de vooraf geconfigureerde hybride verbinding waarop u deze listener wilt registreren. De `path` expressie kan worden uitgebreid met een achtervoegsel en een query reeks expressie om verder te communiceren. Als de hybride verbinding onder het pad is geregistreerd `hyco` , `path` kan de expressie worden `hyco/suffix?param=value&...` gevolgd door de query teken reeks parameters die hier zijn gedefinieerd. Een volledige expressie kan er dan als volgt uitzien:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sb-hc-token=...
```

De `path` expressie wordt door gegeven aan de listener in de adres-URI die is opgenomen in het besturings bericht ' Accept '.

Als de WebSocket-verbinding mislukt omdat het Hybrid Connection-pad niet is geregistreerd, een ongeldig of ontbrekend token of een andere fout, wordt de fout feedback weer gegeven met behulp van het normale HTTP 1,1-status feedback model. De status beschrijving bevat een ID voor het bijhouden van fouten die kan worden gecommuniceerd naar ondersteunings personeel van Azure:

| Code | Fout          | Beschrijving
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Niet gevonden      | Het Hybrid Connection-pad is ongeldig of de basis-URL is onjuist gevormd.
| 401  | Niet geautoriseerd   | Het beveiligings token ontbreekt of is onjuist of ongeldig.
| 403  | Verboden      | Het beveiligings token is niet geldig voor dit pad en voor deze actie.
| 500  | Interne fout | Er is iets verkeerd gegaan in de service.

Als de WebSocket-verbinding opzettelijk wordt afgesloten door de service nadat deze voor het eerst is ingesteld, wordt de reden hiervoor gecommuniceerd met behulp van de juiste WebSocket protocol-fout code en een beschrijving van het fout bericht dat ook een tracerings-ID bevat.

| WS-status | Description
| --------- | ------------------------------------------------------------------------------- 
| 1000      | De listener heeft de socket afgesloten.
| 1001      | Het hybride verbindings traject is verwijderd of uitgeschakeld.
| 1008      | Het beveiligings token is verlopen, daarom wordt het autorisatie beleid geschonden.
| 1011      | Er is iets verkeerd gegaan in de service.

### <a name="http-request-protocol"></a>HTTP-aanvraag Protocol

Met het HTTP-aanvraag protocol kunt u wille keurige HTTP-aanvragen, behalve protocol upgrades.
HTTP-aanvragen worden naar het normale runtime adres van de entiteit gewijsd zonder de $hc infix die wordt gebruikt voor de WebSocket-clients van hybride verbindingen.

```
https://{namespace-address}/{path}?sb-hc-token=...
```

De _naam ruimte-adres_ is de Fully Qualified Domain name van de Azure relay naam ruimte die als host fungeert voor de hybride verbinding, doorgaans van het formulier `{myname}.servicebus.windows.net` .

De aanvraag kan wille keurige extra HTTP-headers bevatten, inclusief door de toepassing gedefinieerde items. Alle opgegeven headers, met uitzonde ring van degene die rechtstreeks zijn gedefinieerd in RFC7230 (Zie [aanvraag bericht](#request-message)) stroom naar de listener en kunnen worden gevonden op het `requestHeader` object van het **aanvraag** bericht.

U hebt de volgende opties voor de query teken reeks parameter:

| Param          | Vereist? | Beschrijving
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Ja\*     | De listener moet een geldig, URL-gecodeerd Service Bus token voor gedeelde toegang opgeven voor de naam ruimte of hybride verbinding die het **Verzend** recht verleent.

Het token kan ook worden uitgevoerd in de `ServiceBusAuthorization` of `Authorization` http-header. Het token kan worden wegge laten als de hybride verbinding zo is geconfigureerd dat anonieme aanvragen worden toegestaan.

Omdat de service effectief fungeert als een proxy, zelfs als dit niet zo is als een echte HTTP-proxy, voegt deze een `Via` kop toe of maakt een aantekeningen voor de bestaande `Via` header die voldoet aan [RFC7230, in de sectie 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1).
De service voegt de hostnaam van de relay-naam ruimte toe aan `Via` .

| Code | Bericht  | Beschrijving                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | De aanvraag is verwerkt door ten minste één listener.  |
| 202  | Geaccepteerd | De aanvraag is door ten minste één listener geaccepteerd. |

Als er een fout optreedt, kan de service als volgt reageren. Of het antwoord afkomstig is van de service of van de listener, kan worden geïdentificeerd door de aanwezigheid van de `Via` header. Als de header aanwezig is, is de reactie van de listener.

| Code | Fout           | Beschrijving
| ---- | --------------- |--------- |
| 404  | Niet gevonden       | Het Hybrid Connection-pad is ongeldig of de basis-URL is onjuist gevormd.
| 401  | Niet geautoriseerd    | Het beveiligings token ontbreekt of is onjuist of ongeldig.
| 403  | Verboden       | Het beveiligings token is niet geldig voor dit pad en voor deze actie.
| 500  | Interne fout  | Er is iets verkeerd gegaan in de service.
| 503  | Ongeldige gateway     | De aanvraag kan niet worden doorgestuurd naar een listener.
| 504  | Time-out van Gateway | De aanvraag is doorgestuurd naar een listener, maar de listener heeft geen ontvangst in de vereiste tijd bevestigd.

## <a name="next-steps"></a>Volgende stappen

* [Veelgestelde vragen over Relay](relay-faq.md)
* [Een naamruimte maken](relay-create-namespace-portal.md)
* [Aan de slag met .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Aan de slag met knooppunten](relay-hybrid-connections-node-get-started.md)
