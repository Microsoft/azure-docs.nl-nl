---
title: Problemen met de beschikbaarheids tests van Azure-toepassing Insights oplossen
description: Problemen met webtests oplossen in Azure-toepassing Insights. Ontvang een waarschuwing wanneer een website niet meer beschikbaar is of traag reageert.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 04/28/2020
ms.reviewer: sdash
ms.openlocfilehash: 0ac8dd189bee1c1d4f5a7a4d0f7de68b085fbc56
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015329"
---
# <a name="troubleshooting"></a>Problemen oplossen

Dit artikel helpt u bij het oplossen van veelvoorkomende problemen die zich kunnen voordoen bij het gebruik van beschikbaarheids bewaking.

## <a name="ssltls-errors"></a>SSL/TLS-fouten

|Symptoom/fout bericht| Mogelijke oorzaken|
|--------|------|
|Kan geen beveiligd SSL/TLS-kanaal maken  | SSL-versie. Alleen TLS 1,0, 1,1 en 1,2 worden ondersteund. **SSLv3 wordt niet ondersteund.**
|TLSv 1.2-record laag: waarschuwing (niveau: onherstelbaar, beschrijving: ongeldige record-MAC)| Zie stack Exchange-thread voor [meer informatie](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake).
|Een URL die mislukt is, is een CDN (Content Delivery Network) | Dit kan worden veroorzaakt door een onjuiste configuratie in uw CDN |  

### <a name="possible-workaround"></a>Mogelijke tijdelijke oplossing

* Als de Url's die het probleem ondervinden, altijd tot afhankelijke resources zijn, is het raadzaam om **afhankelijke aanvragen** voor de webtest uit te scha kelen.

## <a name="test-fails-only-from-certain-locations"></a>Test mislukt alleen op bepaalde locaties

|Symptoom/fout bericht| Mogelijke oorzaken|
|----|---------|
|Een verbindings poging is mislukt omdat de verbonden partij na een bepaalde tijd niet goed heeft gereageerd  | Test agents op bepaalde locaties worden geblokkeerd door een firewall.|
|    |Het opnieuw routeren van bepaalde IP-adressen vindt plaats via (load balancers, geo Traffic managers, Azure Express route). 
|    |Als u Azure ExpressRoute gebruikt, zijn er scenario's waarin pakketten kunnen worden verwijderd in gevallen waarin [asymmetrische route ring plaatsvindt](../../expressroute/expressroute-asymmetric-routing.md).|

## <a name="test-failure-with-a-protocol-violation-error"></a>Fout in de test fout met een schending van het Protocol

|Symptoom/fout bericht| Mogelijke oorzaken| Mogelijke oplossingen |
|----|---------|-----|
|De server heeft een schending van het protocol doorgevoerd. Sectie = ResponseHeader Details = CR moet worden gevolgd door LF | Dit gebeurt wanneer er een onjuiste koptekst wordt gedetecteerd. Het is in het bijzonder dat sommige headers geen gebruikmaken van CRLF om het einde van de regel aan te geven, wat in strijd is met de HTTP-specificatie. Application Insights dwingt deze HTTP-specificatie af en mislukt de antwoorden met ongeldige headers.| a. Neem contact op met de website van de host provider/CDN-provider om de defecte servers te herstellen. <br> b. Als de mislukte aanvragen resources zijn (bijvoorbeeld stijl bestanden, afbeeldingen, scripts), kunt u overwegen het parseren van afhankelijke aanvragen uit te scha kelen. Als u dit doet, verliest u de mogelijkheid om de beschik baarheid van die bestanden te bewaken).

> [!NOTE]
> De URL mislukt mogelijk niet bij browsers met een beperkte validatie van HTTP-headers. Zie dit blogbericht voor een gedetailleerde uitleg van dit probleem: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Veelgestelde vragen over het oplossen van problemen

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>De site ziet er goed uit, maar ik constateer testfouten Waarom Application Insights waarschuwing?

   * Is het **parseren van afhankelijke aanvragen** ingeschakeld voor uw test? Dit resulteert in een strikte controle op resources zoals scripts, installatie kopieën, enzovoort. Deze typen fouten zijn mogelijk niet merkbaar in een browser. Controleer alle afbeeldingen, scripts, stijlmodellen en andere bestanden geladen door de pagina. Als er een fout optreedt, wordt de test gerapporteerd als mislukt, zelfs als de HTML-hoofd pagina zonder problemen wordt geladen. Als u de test voor dergelijke bron fouten wilt desensitize, schakelt u het parseren van afhankelijke aanvragen uit de test configuratie uit.

   * Als u de conflicteert van ruis van tijdelijke netwerk problemen etc. wilt verminderen, moet u ervoor zorgen dat de configuratie voor test fouten opnieuw proberen is ingeschakeld. U kunt ook testen vanaf meer locaties en de drempel waarde voor waarschuwings regels dienovereenkomstig beheren om te voor komen dat er locatie-specifieke problemen optreden die onnodige waarschuwingen veroorzaken.

   * Klik op een van de rode punten van de beschikbaarheids ervaring of een beschikbaarheids fout in de zoek Verkenner om de details te bekijken van de reden waarom de fout is gerapporteerd. Het test resultaat, samen met de gecorreleerde telemetrie aan de server zijde (indien ingeschakeld), helpt te begrijpen waarom de test is mislukt. Veelvoorkomende oorzaken van tijdelijke problemen zijn netwerk-of verbindings problemen.

   * Is de test time-out opgegaan? Tests worden na twee minuten afgebroken. Als uw ping-of multi-step-test langer dan twee minuten duurt, zullen we dit melden als een fout. U kunt de test opsplitsen in meerdere records die in korte duur kunnen worden voltooid.

   * Zijn alle locaties van het rapport mislukt of slechts een deel ervan? Als er slechts enkele gerapporteerde fouten zijn, kan dit worden veroorzaakt door netwerk-en CDN-problemen. Klik opnieuw op de rode punten om te begrijpen waarom de locatie fouten heeft gerapporteerd.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Ik heb geen e-mail bericht ontvangen toen de waarschuwing werd geactiveerd of is opgelost of beide?

Controleer de configuratie van de klassieke waarschuwingen om te bevestigen dat uw e-mail adres direct wordt weer gegeven of dat een distributie lijst die u op, is geconfigureerd voor het ontvangen van meldingen. Als dat het geval is, controleert u de configuratie van de distributie lijst om te bevestigen dat het externe e-mail berichten kan ontvangen. Controleer ook of de e-mail beheerder mogelijk beleids regels heeft geconfigureerd die dit probleem kunnen veroorzaken.

### <a name="i-did-not-receive-the-webhook-notification"></a>Ik heb de webhook-melding niet ontvangen?

Controleer of de toepassing die de webhook-melding ontvangt, beschikbaar is en de webhook-aanvragen verwerkt. Zie [voor](../platform/alerts-log-webhook.md) meer informatie.

### <a name="i-am-getting--403-forbidden-errors-what-does-this-mean"></a>Ik ontvang 403 verboden fouten, wat betekent dit?

Deze fout geeft aan dat u Firewall uitzonderingen moet toevoegen om ervoor te zorgen dat de beschik bare agents uw doel-URL testen. Raadpleeg het [artikel over IP-uitzonde ringen](./ip-addresses.md#availability-tests)voor een volledige lijst met IP-adressen van de agent die u wilt toestaan.

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Onregelmatige testfout met een protocolfout?

De fout 'Schending van protocol... CR moet worden gevolgd door LF' geeft een probleem met de server (of afhankelijkheden) aan. Dit gebeurt wanneer onjuist gevormde headers zijn ingesteld in het antwoord. Dit kan worden veroorzaakt door load balancers of CDN's. Het is in het bijzonder mogelijk dat er geen CRLF wordt gebruikt om het einde van de regel aan te geven, wat in strijd is met de HTTP-specificatie en daarom niet kan worden gevalideerd op het niveau van de .NET-webaanvraag. Inspecteer het antwoord op Spot headers. Dit kan een schending opleveren.

> [!NOTE]
> De URL mislukt mogelijk niet bij browsers met een beperkte validatie van HTTP-headers. Zie dit blogbericht voor een gedetailleerde uitleg van dit probleem: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Ik zie geen verwante telemetrie aan de server zijde voor het vaststellen van test fouten? *

Als u Application Insights hebt ingesteld voor uw app aan serverzijde, kan dit komen doordat er [steekproeven](./sampling.md) worden uitgevoerd. Selecteer een ander beschikbaarheids resultaat.

### <a name="can-i-call-code-from-my-web-test"></a>Kan ik code aanroepen via mijn webtest?

Nee. De stappen van de test moeten zich in het bestand .webtest bevinden. U kunt geen andere webtests aanroepen of lussen gebruiken. Maar er zijn verschillende invoegtoepassingen die nuttig kunnen zijn.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Is er een verschil tussen webtests en beschikbaarheidstests?

De twee voorwaarden kunnen door elkaar worden gebruikt. 'Beschikbaarheidstest' is een algemenere term waar niet alleen webtests met meerdere stappen, maar ook tests met enkele URL-ping onder vallen.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Ik wil graag beschikbaarheidstests gebruiken voor onze interne server die achter een firewall wordt uitgevoerd.

   Er zijn twee mogelijke oplossingen:

   * Configureer uw firewall om binnenkomende aanvragen van de [IP-adressen van onze webtestagents](./ip-addresses.md) toe te staan.
   * Schrijf uw eigen code om uw interne server periodiek te testen. Voer de code uit als achtergrondproces op een testserver achter de firewall. De resultaten van het testproces kunnen worden verzonden naar Application Insights door de API [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) te gebruiken in het SDK-kernpakket. Hiervoor moet uw testserver uitgaande toegang hebben tot het opname-eindpunt van Application Insights, maar dit is een veel kleiner beveiligingsrisico dan wanneer u binnenkomende aanvragen toestaat. De resultaten worden weer gegeven op de Blades voor beschik baarheids webtests, maar de ervaring is enigszins vereenvoudigd, wat beschikbaar is voor testen die zijn gemaakt via de portal. Aangepaste beschikbaarheids tests worden ook weer gegeven als beschikbaarheids resultaten in analyses, zoek acties en metrische gegevens.

### <a name="uploading-a-multi-step-web-test-fails"></a>Het uploaden van een webtest met meerdere stappen mislukt

Dit kan een van de volgende oorzaken hebben:
   * Er is een limiet van 300 K.
   * Lussen worden niet ondersteund.
   * Verwijzingen naar andere webtests worden niet ondersteund.
   * Gegevensbronnen worden niet ondersteund.

### <a name="my-multi-step-test-doesnt-complete"></a>Mijn test met meerdere stappen wordt niet voltooid

Er is een limiet van 100 aanvragen per test. De test wordt ook gestopt als deze langer dan twee minuten wordt uitgevoerd.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Hoe voer ik een test uit met clientcertificaten?

Dit wordt momenteel niet ondersteund.

## <a name="who-receives-the-classic-alert-notifications"></a>Wie ontvangt de (klassieke) waarschuwings meldingen?

Deze sectie is alleen van toepassing op klassieke waarschuwingen en helpt u bij het optimaliseren van uw waarschuwings meldingen om ervoor te zorgen dat alleen de gewenste ontvangers meldingen ontvangen. Raadpleeg het [overzichts artikel over waarschuwingen](../platform/alerts-overview.md)voor meer informatie over het verschil tussen [klassieke waarschuwingen](../platform/alerts-classic.overview.md)en de nieuwe waarschuwingen. Voor het beheren van waarschuwings meldingen in de nieuwe waarschuwings ervaring gebruikt u [actie groepen](../platform/action-groups.md).

* We raden u aan specifieke ontvangers te gebruiken voor klassieke waarschuwings meldingen.

* Voor waarschuwingen over storingen van de X-Y-locaties wordt de optie voor het selectie vakje voor **bulk/groep** , indien ingeschakeld, verzonden naar gebruikers met rollen beheerder/mede beheerder.  In wezen worden _alle_ beheerders van het _abonnement_ meldingen ontvangen.

* Voor waarschuwingen over de metrische gegevens over de beschik baarheid wordt de optie voor **bulk/groep** -selectie in-of uitgeschakeld, verzonden naar gebruikers met de rollen eigenaar, bijdrager of lezer in het abonnement. In feite hebben _alle_ gebruikers met toegang tot het abonnement de Application Insights-resource binnen bereik en ontvangen ze meldingen. 

> [!NOTE]
> Als u momenteel de optie voor het selectie vakje **massaal/groep** gebruikt en deze functie uitschakelt, kunt u de wijziging niet meer ongedaan maken.

Gebruik de nieuwe waarschuwings ervaring/bijna realtime waarschuwingen als u gebruikers op basis van hun rollen wilt waarschuwen. Met [actie groepen](../platform/action-groups.md)kunt u e-mail meldingen configureren voor gebruikers met een van de rollen Inzender/eigenaar/lezer (niet gecombineerd als één optie).

## <a name="next-steps"></a>Volgende stappen

* [Webtest met meerdere stappen](availability-multistep.md)
* [URL-ping-tests](monitor-web-app-availability.md)

