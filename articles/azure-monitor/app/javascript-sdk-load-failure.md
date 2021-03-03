---
title: Problemen oplossen met SDK-laad fouten voor Java script-webtoepassingen-Azure-toepassing Insights
description: Problemen met het laden van een SDK voor Java script-webtoepassingen oplossen
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6295a56abbf3466c68b968c935936dbc10e22fb5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711413"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>Problemen met SDK-laad fouten voor Java script-web-apps oplossen

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

De SDK-fout uitzondering voor laden wordt gemaakt en gerapporteerd door het Java script-fragment (v3 of hoger) wanneer wordt gedetecteerd dat het SDK-script niet kan worden gedownload of geïnitialiseerd. Simplistically, de client van uw eind gebruikers (browser) kan de Application Insights SDK niet downloaden of initialiseren vanaf de geïdentificeerde hosting pagina en daarom worden er geen telemetriegegevens of gebeurtenissen gerapporteerd.

![Overzicht van mislukte browser Azure Portal](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> Deze uitzonde ring wordt ondersteund voor alle belang rijke browsers die ondersteuning bieden voor de ophaal-API of XMLHttpRequest. Dit geldt niet voor IE 8 en lager, zodat u dit type uitzonde ring niet kunt ontvangen van deze browsers (tenzij uw omgeving een polyvulling bevat).

![Details van browser uitzondering](./media/javascript-sdk-load-failure/exception.png)

De stack details bevatten de basis informatie met de Url's die worden gebruikt door de eind gebruiker.

| Naam                      | Beschrijving                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;CDN- &nbsp; eind punt&gt; | De URL die is gebruikt (en mislukt) voor het downloaden van de SDK.                                                      |
| &lt;Help- &nbsp; koppeling&gt;    | Een URL die verwijst naar documentatie voor probleem oplossing (deze pagina).                                              |
| &lt;Host- &nbsp; URL&gt;     | De volledige URL van de pagina die de eind gebruiker gebruikt.                                                    |
| &lt;Eind punt- &nbsp; URL&gt; | De URL die is gebruikt om de uitzonde ring te rapporteren, deze waarde kan handig zijn om te bepalen of de hosting pagina is geopend vanuit het open bare Internet of een privécloud.

De meest voorkomende oorzaken voor deze uitzonde ring zijn:

- Onregelmatige netwerk verbindings fout.
- Application Insights CDN-onderbreking.
- De SDK kan niet worden geïnitialiseerd na het laden van het script.
- Application Insights java script CDN is geblokkeerd.

[Onregelmatige fout in de netwerk verbinding](#intermittent-network-connectivity-failure) is de meest voorkomende reden voor deze uitzonde ring, met name in scenario's voor mobiele roaming waarbij de gebruikers een netwerk verbinding in een onregelmatige omstandigheden verliezen.

In de volgende secties wordt beschreven hoe u elke mogelijke oorzaak van deze fout kunt oplossen.

> [!NOTE]
> Bij verschillende stappen voor probleem oplossing wordt ervan uitgegaan dat uw toepassing direct controle heeft over het &lt; script/label van het fragment &gt; en de configuratie die wordt geretourneerd als onderdeel van de HTML-hosting-pagina. Als u dat niet doet, zijn de geïdentificeerde stappen niet van toepassing op uw scenario.

## <a name="intermittent-network-connectivity-failure"></a>Onregelmatige netwerk verbindings fout

**Als de gebruiker onregelmatige problemen met de netwerk verbinding ondervindt, zijn er minder mogelijke oplossingen en worden deze over een korte periode in het algemeen opgelost.** Als de gebruiker bijvoorbeeld uw site opnieuw laadt (de pagina wordt vernieuwd), worden de bestanden (uiteindelijk) gedownload en lokaal in de cache geplaatst totdat een bijgewerkte versie wordt uitgebracht.

> [!NOTE]
> Als deze uitzonde ring permanent is en wordt uitgevoerd in veel van uw gebruikers (met een snelle en permanente hoeveelheid van deze uitzonde ring gerapporteerd), samen met een verlaging van de normale client-telemetrie, is de kans op problemen met de netwerk verbinding _niet waarschijnlijk_ de echte oorzaak van het probleem en moet u de diagnose voortzetten met de andere bekende mogelijke problemen.

Met deze situatie die als host fungeert voor de SDK op uw eigen CDN, is het waarschijnlijk niet mogelijk om deze uitzonde ring op te lossen of te verminderen, omdat uw eigen CDN wordt beïnvloed door hetzelfde probleem.

Hetzelfde geldt ook voor het gebruik van de SDK via NPM packages. Vanuit het perspectief van eind gebruikers is het echter wel mogelijk dat uw hele toepassing niet kan worden geladen/geïnitialiseerd (in plaats van _alleen_ de telemetrie-SDK, die ze niet visueel zien), zodat ze uw site waarschijnlijk zullen vernieuwen totdat deze volledig wordt geladen.

U kunt ook proberen om [NPM-pakketten](#use-npm-packages-to-embed-the-application-insight-sdk) te gebruiken om de Application Insights SDK in te sluiten.

Om een onregelmatige fout in de netwerk verbinding te minimaliseren, hebben we Cache-Control headers geïmplementeerd op alle CDN-bestanden, zodat de huidige versie van de SDK door de browser van de eind gebruiker niet meer hoeft te worden gedownload. in de browser wordt de eerder verkregen kopie opnieuw gebruikt (Zie [hoe caching werkt](../../cdn/cdn-how-caching-works.md)). Als de cache controle mislukt of er een nieuwe release is, moet de bijgewerkte versie van de browser van uw eind gebruiker worden gedownload. Het is dus mogelijk dat het achtergrond niveau _' ruis '_ wordt weer gegeven in het scenario voor de controle fout of een tijdelijke piek wanneer een nieuwe release plaatsvindt en algemeen beschikbaar wordt gemaakt (geïmplementeerd in het CDN).
 
## <a name="application-insights-cdn-outage"></a>Application Insights CDN-onderbreking

U kunt controleren of er een Application Insights CDN-onderbreking is door rechtstreeks vanuit de browser toegang te krijgen tot het CDN-eind punt (bijvoorbeeld https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js of https://js.monitor.azure.com/scripts/b/ai.2.min.js) vanaf een andere locatie dan uw eind gebruikers) (ervan uitgaande dat uw organisatie dit domein niet heeft geblokkeerd).

Als u bevestigt dat er zich een storing voordoet, kunt u [een nieuw ondersteunings ticket maken](https://azure.microsoft.com/support/create-ticket/) of de URL die wordt gebruikt voor het downloaden van de SDK proberen te wijzigen.

### <a name="change-the-cdn-endpoint"></a>Het CDN-eind punt wijzigen
  
Wanneer het fragment en de configuratie ervan door uw toepassing worden geretourneerd als onderdeel van elke gegenereerde pagina, kunt u de `src` configuratie van het fragment wijzigen om een andere URL voor de SDK te gebruiken. Door deze methode te gebruiken, kunt u het door CDN geblokkeerde probleem negeren als de nieuwe URL niet moet worden geblokkeerd.

Huidige Application Insights java script SDK CDN-eind punten
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> Het `https://js.monitor.azure.com/` eind punt is een alias waarmee we binnen ongeveer 5 minuten tussen CDN-providers kunnen scha kelen, zonder dat u een configuratie hoeft te wijzigen. Zo kunnen we de gedetecteerde CDN-problemen sneller oplossen als een CDN-provider regionale of wereld wijde problemen heeft zonder dat iedereen hun instellingen hoeft aan te passen.

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>SDK kan niet worden geïnitialiseerd na het laden van het script

Wanneer de SDK niet kan worden geïnitialiseerd, &lt; is het script &gt; verwijderd uit het CDN, maar mislukt tijdens de initialisatie. Dit kan worden veroorzaakt door ontbrekende of ongeldige afhankelijkheden of een vorm van een Java script-uitzonde ring.

De eerste keer dat u controleert of de SDK correct is gedownload, als het script niet is gedownload, is dit scenario __niet__ de oorzaak van de fout uitzondering voor de SDK-belasting.

Snelle controle: het gebruik van een browser die ondersteuning biedt voor ontwikkel hulpprogramma's (F12), Valideer op het tabblad netwerk dat het script dat is gedefinieerd in de ```src``` fragment configuratie is gedownload met de antwoord code 200 (geslaagd) of een 304 (niet gewijzigd). U kunt ook een hulp programma zoals Fiddler gebruiken om het netwerk verkeer te controleren.

De volgende secties bevatten verschillende rapportage opties, maar u wordt aangeraden een ondersteunings ticket te maken of een probleem op GitHub op te lossen.

 Basis regels voor rapportage:

- Als het probleem alleen van invloed is op een klein aantal gebruikers en een specifieke of subset van browser versie (s) (Raadpleeg de details over de gemelde uitzonde ring), is de kans groot dat een eind gebruiker of omgeving alleen een probleem heeft, waardoor uw toepassing waarschijnlijk extra `polyfill` implementaties moet bieden. Hiervoor is [een probleem opgetreden in github](https://github.com/Microsoft/ApplicationInsights-JS/issues).
- Als dit probleem van invloed is op uw hele toepassing en al uw gebruikers worden beïnvloed, is dit waarschijnlijk een probleem met betrekking tot de release en moet u daarom [een nieuw ondersteunings ticket maken](https://azure.microsoft.com/support/create-ticket/).

### <a name="javascript-exceptions"></a>Java script-uitzonde ringen

Eerst kunt u op Java script-uitzonde ringen controleren met behulp van een browser die ontwikkel hulpprogramma's ondersteunt (F12) de pagina laden en controleren of er uitzonde ringen zijn opgetreden.

Als er uitzonde ringen worden gerapporteerd in het SDK-script (bijvoorbeeld ai.2.min.js), kan dit erop wijzen dat de configuratie die in de SDK is door gegeven, onverwachte of ontbrekende vereiste configuratie bevat of dat een beschadigde release is geïmplementeerd in het CDN.

Als u wilt controleren op defecte configuratie, wijzigt u de configuratie die is door gegeven aan het fragment (indien nog niet), zodat het alleen uw instrumentatie sleutel als teken reeks waarde bevat.

> src: " https://js.monitor.azure.com/scripts/b/ai.2.min.js ",<br />
> cfg: {<br />
> instrumentationKey: INSTRUMENTATION_KEY<br />
> }});<br />

Als u deze minimale configuratie gebruikt, wordt er nog [steeds een Java script-](https://azure.microsoft.com/support/create-ticket/) uitzonde ring in het SDK-script weer gegeven. hiervoor moet de defecte build worden teruggedraaid omdat deze waarschijnlijk een probleem met een nieuwe geïmplementeerde versie is.

Als de uitzonde ring verdwijnt, wordt het probleem waarschijnlijk veroorzaakt door een niet-overeenkomend type of onverwachte waarde. Begin met het toevoegen van uw configuratie opties één voor één en test totdat de uitzonde ring opnieuw optreedt. Raadpleeg vervolgens de documentatie voor het item dat het probleem veroorzaakt. Als de documentatie onduidelijk is of als u hulp nodig hebt, kunt u [een probleem in github oplossen](https://github.com/Microsoft/ApplicationInsights-JS/issues).

Als uw configuratie al eerder is geïmplementeerd en werkte, maar u zojuist deze uitzonde ring had gerapporteerd, is het mogelijk dat er een probleem is met een nieuwe geïmplementeerde versie. Controleer of dit van invloed is op een klein aantal gebruikers/browsers en of u een [probleem op github](https://github.com/Microsoft/ApplicationInsights-JS/issues) of  [een nieuw ondersteunings ticket wilt maken](https://azure.microsoft.com/support/create-ticket/).

### <a name="enable-console-debugging"></a>Fout opsporing via console inschakelen

Ervan uitgaande dat er geen uitzonde ringen worden gegenereerd de volgende stap is het inschakelen van fout opsporing via de console door de `loggingLevelConsole` instelling toe te voegen aan de configuratie. Hierdoor worden alle initialisatie fouten en waarschuwingen naar de browser console verzonden (normaal gesp roken beschikbaar via de hulpprogram ma's voor ontwikkel aars (F12)). Eventuele gerapporteerde fouten moeten zichzelf verklaren en als u meer hulp nodig hebt, kunt u [een probleem op github](https://github.com/Microsoft/ApplicationInsights-JS/issues).

> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> loggingLevelConsole: 2<br />
> }});<br />

> [!NOTE]
> Tijdens de initialisatie voert de SDK enkele basis controles uit op bekende primaire afhankelijkheden. Als deze niet worden verschaft door de huidige runtime, worden de fouten gerapporteerd als waarschuwings berichten naar de-console, maar alleen als de waarde `loggingLevelConsole` groter is dan nul.

Als deze nog steeds niet kan worden geïnitialiseerd, schakelt u de ```enableDebug``` configuratie-instelling in. Dit zorgt ervoor dat alle interne fouten worden gegenereerd als een uitzonde ring (waardoor telemetrie verloren gaat). Aangezien dit een ontwikkelaar is, is het waarschijnlijk dat er ruis ontstaat met uitzonde ringen die worden gegenereerd als onderdeel van een aantal interne controles. Daarom moet u elke uitzonde ring bekijken om te bepalen welk probleem ervoor zorgt dat de SDK mislukt. Gebruik de niet-minified versie van het script (Let op de uitbrei ding onder het '. js ' en niet op ' .min.js ') anders worden de uitzonde ringen onleesbaar.

> [!WARNING]
> Dit is een instelling die alleen voor ontwikkel aars is en die nooit moet worden ingeschakeld in een volledige productie omgeving omdat telemetrie verloren gaat.

> src: " https://js.monitor.azure.com/scripts/b/ai.2.min.js ",<br />
> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> enableDebug: True<br />
> }});<br />

Als er nog steeds geen inzichten zijn, dient u [een probleem in github op te lossen](https://github.com/Microsoft/ApplicationInsights-JS/issues) met de details en een voorbeeld site als u er een hebt. Neem de details van de browser versie, het besturings systeem en het JS-Framework op om het probleem te identificeren.

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>De Application Insights java script CDN is geblokkeerd

Het CDN dat wordt geblokkeerd, is mogelijk wanneer een Application Insights java script SDK CDN-eind punt is gerapporteerd en/of is geïdentificeerd als onveilig. Als dit gebeurt, wordt het eind punt openbaar geblokkeerd en gebruiken de consumenten van deze lijsten alle toegang te blok keren.

Om het probleem op te lossen, moet de eigenaar van het CDN-eind punt samen werken met de entiteit voor de blok vermelding die het eind punt als onveilig heeft gemarkeerd, zodat deze kan worden verwijderd uit de relevante lijst.

Controleer of het CDN-eind punt is geïdentificeerd als onveilig.
- [Google-transparantie rapport](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

Afhankelijk van de frequentie waarmee de toepassing, firewall of omgeving de lokale exemplaren van deze lijsten bijwerken, kan het enige tijd duren en/of hand matige tussen komst van de eind gebruikers of de IT-afdeling van het bedrijf verplichten om een update af te dwingen of expliciet de CDN-eind punten toe te staan om het probleem op te lossen.

Als het CDN-eind punt wordt aangeduid als onveilig, [maakt u een ondersteunings ticket](https://azure.microsoft.com/support/create-ticket/) om ervoor te zorgen dat het probleem zo snel mogelijk wordt opgelost.

Als u dit probleem *mogelijk* sneller wilt overs Laan, kunt u [het SDK CDN-eind punt wijzigen](#change-the-cdn-endpoint).

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>Application Insights java script CDN is geblokkeerd (door de gebruiker geblokkeerd door de browser; geïnstalleerde blok kering; persoonlijke firewall)

Controleren of uw eind gebruikers over het volgende beschikken:

- Er is een browserinvoegtoepassing (meestal een vorm van AD, malware of pop-upblokkering) geïnstalleerd.
- Het Application Insights CDN-eind punten geblokkeerd (of niet toegestaan) in hun browser of proxy.
- Er is een firewall regel geconfigureerd die ervoor zorgt dat het CDN-domein voor de SDK wordt geblokkeerd (of dat de DNS-vermelding niet kan worden omgezet).

Als ze een van deze opties hebben geconfigureerd, moet u ermee werken (of de documentatie opgeven) om de CDN-eind punten toe te staan.

Het is mogelijk dat de invoeg toepassing die ze hebben geïnstalleerd, gebruikmaakt van de open bare blokkerings lijst. Als dat niet het geval is, is het waarschijnlijk een andere hand matig geconfigureerde oplossing of wordt er een privé domein blokkerings lijst gebruikt.

#### <a name="add-exceptions-for-cdn-endpoints"></a>Uitzonde ringen voor CDN-eind punten toevoegen

Werk samen met uw eind gebruikers of zorg ervoor dat er in de documentatie wordt vermeld dat er scripts van de Application Insights CDN-eind punten moeten worden gedownload door ze op te nemen in de uitzonderings lijst van de browser of door de firewall regel (afhankelijk van de omgeving van de eind gebruiker).

Hier volgt een voor beeld van het configureren van [Chrome om toegang tot websites toe te staan of te blok keren.](https://support.google.com/chrome/a/answer/7532419?hl=en)

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>Application Insights CDN is geblokkeerd (door de bedrijfs firewall)

Als uw eind gebruikers zich in een bedrijfs netwerk bevinden, is het waarschijnlijk de firewall oplossing en heeft de IT-afdeling een vorm van Internet filtering geïmplementeerd. In dat geval moet u ermee samen werken om de benodigde regels voor uw eind gebruikers toe te staan.

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>Uitzonde ringen voor CDN-eind punten voor bedrijven toevoegen

  Dit is vergelijkbaar met het [toevoegen van uitzonde ringen voor eind gebruikers](#add-exceptions-for-cdn-endpoints), maar u moet samen werken met de IT-afdeling van het bedrijf om de Application Insights CDN-eind punten zo te configureren dat ze worden gedownload door ze op te nemen (of te verwijderen) in een domein blok vermelding of services voor het toestaan van vermeldingen.

  > [!WARNING]
  > Als uw zakelijke gebruiker een [privécloud](https://azure.microsoft.com/overview/what-is-a-private-cloud/) gebruikt en er geen vorm van uitzonde ring kan worden geboden om hun interne gebruikers te voorzien van open bare toegang voor de CDN-eind punten, moet u de [Application Insights NPM-pakketten](https://www.npmjs.com/package/applicationinsights) gebruiken of de Application Insights SDK op uw eigen CDN hosten.  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>Aanvullende probleem oplossing voor geblokkeerde CDN

> [!NOTE]
> Als uw gebruikers een [privécloud](https://azure.microsoft.com/overview/what-is-a-private-cloud/) gebruiken en ze geen toegang hebben tot het open bare Internet, moet u de SDK hosten op uw eigen CDN of gebruikmaken van NPM-pakketten.

#### <a name="host-the-sdk-on-your-own-cdn"></a>De SDK op uw eigen CDN hosten

 In plaats van uw eind gebruikers te downloaden van de Application Insights SDK vanuit het open bare CDN, kunt u de Application Insights SDK hosten vanuit uw eigen CDN-eind punt. Het is raadzaam een specifieke versie (AI. 2. #. # .min.js) te gebruiken, zodat het gemakkelijker is om te identificeren welke versie u gebruikt. Werk het ook regel matig bij met de huidige versie (ai.2.min.js), zodat u kunt gebruikmaken van eventuele oplossingen voor fouten en nieuwe functies die beschikbaar worden.

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>NPM-pakketten gebruiken om de Application Insight-SDK in te sluiten

In plaats van het fragment en open bare CDN-eind punten te gebruiken, kunt u de [NPM-pakketten](https://www.npmjs.com/package/applicationinsights) gebruiken voor het opnemen van de SDK als onderdeel van uw eigen Java script-bestanden. De SDK wordt gewoon een ander pakket binnen uw eigen scripts.

> [!NOTE]
> Het wordt aanbevolen dat wanneer u NPM-pakketten gebruikt, ook een vorm van [Java script-bundel](https://www.bing.com/search?q=javascript+bundler) moet gebruiken om te helpen bij het splitsen van code en minificatie.

Net als bij het fragment is het ook mogelijk dat uw eigen scripts (met of zonder de SDK NPM-pakketten) kunnen worden beïnvloed door de problemen die hier worden vermeld. Dit kan, afhankelijk van uw toepassing, uw gebruikers en uw Framework, overwegen iets te implementeren dat vergelijkbaar is met de logica in het fragment om deze problemen te detecteren en te rapporteren.


## <a name="next-steps"></a>Volgende stappen 
- [Meer informatie over het indienen van een probleem bij GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [Gebruik van webpagina's bewaken](javascript.md)
