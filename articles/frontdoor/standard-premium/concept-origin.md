---
title: Oorsprong en oorspronkelijke groep in azure front deur Standard/Premium
description: In dit artikel wordt beschreven welke oorspronkelijke en oorspronkelijke groep zich in een Azure front-deur configuratie bevinden.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 42a9a0ea23faa481140a46ec52b2214431dd723e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099216"
---
# <a name="origin-and-origin-group-in-azure-front-door-standardpremium-preview"></a>Oorspronkelijke en oorspronkelijke groep in azure front deur Standard/Premium (preview)

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

Dit artikel heeft betrekking op concepten over de werking van de implementatie van uw webtoepassing met Azure front deur Standard/Premium. U leert ook wat een *oorsprong* en de *oorspronkelijke groep* is in de Azure front deur Standard/Premium-configuratie.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="origin"></a>Oorsprong

Azure front deur Standard/Premium Origin verwijst naar de hostnaam of het open bare IP-adres van uw toepassing die uw client aanvragen verzendt. Azure front deur Standard/Premium ondersteunt zowel Azure-als niet-Azure-resources in een originele groep. De toepassing kan ook worden gehost in uw on-premises Data Center of in een andere Cloud provider. De oorsprong mag niet worden verward met de data base-laag of de opslaglaag. Oorsprong moet worden weer gegeven als het open bare eind punt voor de back-end van uw toepassing. Wanneer u een oorsprong toevoegt aan een Azure front deur Standard/Premium-oorsprongs groep, moet u ook de volgende informatie toevoegen:

* **Type oorsprong:** Het type resource dat u wilt toevoegen. De front deur ondersteunt automatische detectie van uw back-end van uw toepassing vanuit App Service, Cloud service of opslag. Als u een andere resource wilt in azure of zelfs een back-end die niet van Azure is, selecteert u **aangepaste host**.

    >[!IMPORTANT]
    >Tijdens de configuratie valideert Api's niet als de oorsprong niet toegankelijk is vanuit de front-deur omgeving. Zorg ervoor dat de front deur uw oorsprong kan bereiken.

* **Hostnaam van abonnement en oorsprong:** Als u geen **aangepaste host** hebt geselecteerd voor het type back-end, selecteert u uw back-end door het juiste abonnement en de bijbehorende back-end-hostnaam te kiezen.

* **Oorsprong van host-header:** De waarde van de host-header die wordt verzonden naar de back-end voor elke aanvraag. Zie [Origin host header](#hostheader)(Engelstalig) voor meer informatie.

* **Prioriteit:** Wijs prioriteiten toe aan uw andere oorsprong wanneer u een primaire oorsprong voor al het verkeer wilt gebruiken. Geef ook back-ups op als de primaire of de back-oorsprong niet beschikbaar zijn. Zie [Priority](#priority)voor meer informatie.

* **Gewicht:** Wijs gewichten toe aan uw andere oorsprong om verkeer te verdelen over een reeks oorsprong, hetzij gelijkmatig als op basis van gewichts coëfficiënten. Zie [gewichten](#weighted)voor meer informatie.

### <a name="origin-host-header"></a><a name = "hostheader"></a>Header van oorsprong van host

Aanvragen die worden doorgestuurd door de standaard/Premium van de Azure-voor deur, bevatten een host-header veld dat door de oorsprong wordt gebruikt om de doel resource op te halen. De waarde voor dit veld is doorgaans afkomstig van de oorsprongs-URI die de host-header en-poort heeft.

Een aanvraag voor heeft bijvoorbeeld `www.contoso.com` de hostheader `www.contoso.com` . Als u Azure Portal gebruikt om uw oorsprong te configureren, is de standaard waarde voor dit veld de hostnaam van de back-end. Als uw oorsprong `contoso-westus.azurewebsites.net` in de Azure Portal, is de automatisch gevulde waarde voor de oorsprong van de host-header `contoso-westus.azurewebsites.net` . Als u echter Azure Resource Manager sjablonen of een andere methode gebruikt zonder dit veld expliciet in te stellen, stuurt front deur de binnenkomende hostnaam als de waarde voor de host-header. Als de aanvraag is ingediend voor `www.contoso.com` en uw oorsprong `contoso-westus.azurewebsites.net` een leeg header-veld heeft, wordt de host-header door de voor deur ingesteld als `www.contoso.com` .

Voor de meeste app-back-ends (Azure Web Apps, Blob Storage en Cloud Services) moet de host-header overeenkomen met het domein van de back-end. De frontend-host die naar uw back-end stuurt, gebruikt echter een andere hostnaam, zoals `www.contoso.net` .

Als uw oorsprong de host-header moet overeenkomen met de hostnaam van de back-end, moet u ervoor zorgen dat de back-end-host-header de hostnaam van de back-end bevat.

#### <a name="configuring-the-origin-host-header-for-the-origin"></a>De oorsprong van de host-header voor de oorsprong configureren

Het veld **oorsprong van host-header** configureren voor een oorsprong in de sectie oorspronkelijke groep:

1. Open de resource van de voor deur en selecteer de oorspronkelijke groep waarvan de oorsprong moet worden geconfigureerd.

2. Voeg een oorsprong toe als u dit nog niet hebt gedaan, of bewerk een bestaande naam.

3. Stel het veld oorsprong van host-header in op een aangepaste waarde of laat dit leeg. De hostnaam voor de binnenkomende aanvraag wordt gebruikt als de waarde voor de host-header.

## <a name="origin-group"></a>Oorspronkelijke groep

Een oorspronkelijke groep in azure front deur Standard/Premium verwijst naar de set oorsprong die soortgelijk verkeer voor hun toepassing ontvangt. Met andere woorden, het is een logische groepering van uw toepassings instanties over de hele wereld die hetzelfde verkeer ontvangen en reageert met een verwacht gedrag. Deze oorsprongen kunnen worden geïmplementeerd in verschillende regio's of binnen dezelfde regio. Alle oorsprongen kunnen in de actieve/actieve implementatie modus zijn of wat is gedefinieerd als actieve/passieve configuratie.

Een originele groep definieert hoe de oorsprong moet worden geëvalueerd via status controles. Ook wordt gedefinieerd hoe taak verdeling tussen deze items wordt uitgevoerd.

### <a name="health-probes"></a>Statuscontroles

Azure front deur Standard/Premium verzendt periodieke HTTP/HTTPS-test aanvragen naar elk van uw geconfigureerde oorsprong. Test aanvragen bepalen de nabijheid en status van elke oorsprong voor taak verdeling van uw aanvragen voor eind gebruikers. De status test instellingen voor een originele groep bepalen hoe we de status van app-back-ends controleren. De volgende instellingen zijn beschikbaar voor de configuratie van de taak verdeling:

* **Pad**: de URL die wordt gebruikt voor het testen van aanvragen voor alle oorsprongen in de oorspronkelijke groep. Als een van uw oorsprongen bijvoorbeeld is `contoso-westus.azurewebsites.net` en het pad wordt ingesteld op/probe/test.aspx, wordt er een voor beeld van een front-deur-omgeving, ervan uitgaande dat het protocol http is, worden aanvragen voor status test verzonden naar `http://contoso-westus.azurewebsites.net/probe/test.aspx` .

* **Protocol**: definieert of de Health probe-aanvragen van de voor deur naar uw oorsprong moeten worden verzonden met het HTTP-of HTTPS-protocol.

* **Methode**: de HTTP-methode die moet worden gebruikt voor het verzenden van status controles. De opties zijn GET of HEAD (standaard).
    > [!NOTE]
    > Voor een lagere belasting en kosten op uw back-end wordt aanbevolen om hoofd aanvragen voor status tests te gebruiken.

* **Interval (seconden)**: definieert de frequentie van status controles aan uw oorsprong of de intervallen waarin elk van de voor deur omgevingen een sonde verzendt.

    >[!NOTE]
    >Voor snellere failovers stelt u het interval in op een lagere waarde. Hoe lager de waarde, hoe hoger het volume van de status test uw back-ends worden ontvangen. Als het interval bijvoorbeeld is ingesteld op 30 seconden met zeggen, 100 front deur Pop's wereld wijd, ontvangt elke back-end ongeveer 200 test aanvragen per minuut.

Zie [status tests](concept-health-probes.md)voor meer informatie.

### <a name="load-balancing-settings"></a>Instellingen voor taak verdeling

Met de instellingen voor taak verdeling voor de oorspronkelijke groep definieert u hoe de status tests worden geëvalueerd. Deze instellingen bepalen of de oorsprong in orde of beschadigd is. Ze controleren ook hoe de taak verdeling van verkeer tussen verschillende oorsprongen in de oorspronkelijke groep verloopt. De volgende instellingen zijn beschikbaar voor de configuratie van de taak verdeling:

* **Steekproef grootte:** Hiermee wordt aangegeven hoeveel steek proeven van Health tests moeten worden genomen om de status van de oorsprong te evalueren.

* **Geslaagde sample grootte:** Hiermee definieert u de voorbeeld grootte zoals eerder vermeld, het aantal geslaagde steek proeven dat nodig is om de oorsprong in orde aan te roepen. Stel dat het interval voor de status test voor de voor deur 30 seconden is, de voorbeeld grootte 5 is en een geslaagde voorbeeld grootte is ingesteld op 3. Telkens wanneer we de status controles voor uw oorsprong evalueren, kijken we naar de laatste vijf voor beelden over 150 seconden (5 x 30). Ten minste drie geslaagde tests zijn vereist om de oorsprong als in orde te declareren.

* **Latentie gevoeligheid (extra latentie):** Hiermee wordt bepaald of u de aanvraag wilt verzenden naar de oorsprong binnen het gevoeligheids bereik van de latentie meting of door sturen van de aanvraag naar de dichtstbijzijnde back-end.

Zie voor meer informatie [op minste latentie gebaseerde routerings methode](#latency).

## <a name="routing-methods"></a>Routeringsmethoden

Azure front deur Standard/Premium ondersteunt verschillende soorten verkeers routerings methoden om te bepalen hoe u uw HTTP/HTTPS-verkeer wilt door sturen naar verschillende service-eind punten. Wanneer uw client aanvragen de voor deur bereiken, wordt de geconfigureerde routerings methode toegepast om ervoor te zorgen dat de aanvragen worden doorgestuurd naar het beste back-end-exemplaar. 

Er zijn vier routerings methoden voor verkeer beschikbaar in azure front deur Standard/Premium:

* **[Latentie](#latency):** Met de latentie gebaseerde route ring zorgt u ervoor dat aanvragen worden verzonden naar de laagste latentie back-end die acceptabel is binnen een gevoeligheids bereik. In principe worden uw gebruikers aanvragen verzonden naar de ' dichtstbijgelegen ' set back-ends met betrekking tot netwerk latentie.
* **[Prioriteit](#priority):** U kunt prioriteiten toewijzen aan uw back-ends wanneer u een primaire back-end wilt configureren om al het verkeer te onderhouden. De secundaire back-end kan een back-up zijn voor het geval de primaire back-end niet beschikbaar is.
* **[Gewogen](#weighted):** U kunt gewichten toewijzen aan uw back-ends wanneer u verkeer wilt distribueren over een set back-ends. Of u gelijkmatig wilt verdelen of op basis van de gewichts coëfficiënten.

Alle standaard configuraties van de Azure front-deur zijn onder andere het controleren van de status van back-end en automatische directe, globale failover. Zie [back-end controleren](concept-health-probes.md)voor meer informatie. De voor deur kan worden gebruikt op basis van één routerings methode. Maar afhankelijk van de behoeften van uw toepassing, kunt u ook meerdere routerings methoden combi neren om een optimale routerings topologie te bouwen.

### <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Laagste verkeer op basis van latentie-route ring

Het implementeren van back-ends op twee of meer locaties over de hele wereld kan de reactie snelheid van uw toepassingen verbeteren door verkeer naar de doel locatie te sturen die het dichtst bij uw eind gebruikers ligt. De standaard methode voor het routeren van verkeer voor de configuratie van de voor deur stuurt aanvragen van uw eind gebruikers door naar de dichtstbijzijnde back-end van de front-deur omgeving die de aanvraag heeft ontvangen. In combi natie met de anycast-architectuur van de voor deur van Azure, zorgt deze benadering ervoor dat elk van uw eind gebruikers de maximale prestaties op basis van hun locatie kan verkrijgen.

De ' dichtstbijgelegen ' back-end is niet noodzakelijkerwijs het dichtst bij de geografische afstand gemeten. In plaats daarvan bepaalt de voor deur de dichtstbijzijnde back-end door netwerk latentie te meten.

Hieronder ziet u de algehele beslissings stroom:

| Beschik bare back-ends | Prioriteit | Latentie signaal (op basis van de status test) | Gewicht |
|-------------| ----------- | ----------- | ----------- |
| Selecteer eerst alle backends die zijn ingeschakeld en in orde zijn geretourneerd (200 OK) voor de status test. Als er zes back-ends A, B, C, D, E en F zijn, en deze C zijn beschadigd en E is uitgeschakeld. De lijst met beschik bare back-ends is A, B, D en F.  | Vervolgens worden de back-end met de hoogste prioriteit geselecteerd voor de beschik bare items. Als back-end A, B en D prioriteit 1 hebben en backend F de prioriteit 2 heeft. Vervolgens worden de geselecteerde back-ends A, B en D.| Selecteer de back-ends met het latentie bereik (minste latentie & latentie gevoeligheid in MS opgegeven). Als back-end A 15 MS is, is B 30 MS en D 60 MS weg van de front-deur omgeving waar de aangevraagde aanvraag en latentie gevoeligheid 30 MS zijn, en de laagste latentie groep bestaat uit de back-end A en B, omdat D groter is dan 30 MS van de dichtstbijzijnde back-end. | Ten slotte wordt de voor deur round robin het verkeer tussen de uiteindelijke geselecteerde pool met back-ends in de verhouding van de opgegeven gewichten. Als back-end A een gewicht van 5 heeft en backend B een gewicht van 8 heeft, wordt het verkeer gedistribueerd in de verhouding 5:8 tussen back-ends A en B. |

>[!NOTE]
> De eigenschap voor de latentie gevoeligheid wordt standaard ingesteld op 0 MS, dat wil zeggen, de aanvraag altijd door sturen naar de snelste beschik bare back-end.

### <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Op prioriteit gebaseerd verkeer: route ring

Een organisatie wil vaak hoge Beschik baarheid bieden voor hun services door meer dan één back-upservice te implementeren, in het geval de primaire versie één keer wordt uitgeschakeld. In de hele branche wordt deze topologie ook wel actief/stand-by of actieve/passieve implementatie topologie genoemd. Met de routerings methode ' Priority ' kunnen Azure-klanten het failover-patroon eenvoudig implementeren.

De standaard-deur bevat een lijst met gelijke prioriteiten voor de back-end. Standaard verzendt de voor deur alleen verkeer naar de back-ends met de hoogste prioriteit (laagste waarde voor de prioriteit), de primaire set back-ends. Als de primaire back-end niet beschikbaar is, stuurt front deur het verkeer naar de secundaire set back-ends (tweede laagste waarde voor prioriteit). Als zowel de primaire als de secundaire back-end niet beschikbaar zijn, gaat het verkeer naar de derde, enzovoort. De beschik baarheid van de back-end is gebaseerd op de geconfigureerde status (ingeschakeld of uitgeschakeld) en de voortdurende status van de back-end, zoals bepaald door de status controles.

#### <a name="configuring-priority-for-backends"></a>De prioriteit voor back-ends configureren

Elke back-end in uw back-end-groep van de front-deur configuratie heeft een eigenschap met de naam priority. Dit kan een getal tussen 1 en 5 zijn. Met de voor deur van Azure kunt u de back-end-prioriteit expliciet configureren met behulp van deze eigenschap voor elke back-end. Deze eigenschap is een waarde tussen 1 en 5. Lagere waarden vertegenwoordigen een hogere prioriteit. Back-ends kunnen prioriteits waarden delen.

### <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Gewogen verkeer-routerings methode
Met de methode gewogen verkeers routering kunt u verkeer gelijkmatig distribueren of een vooraf gedefinieerde wegings factor gebruiken.

In de routerings methode gewogen verkeer wijst u een gewicht toe aan elke back-end in de front-deur configuratie van de back-endserver. Het gewicht is een geheel getal tussen 1 en 1000. Voor deze para meter wordt het standaard gewicht van ' 50 ' gebruikt.

Met de lijst met beschik bare back-ends die een acceptabele latentie gevoeligheid hebben, wordt het verkeer gedistribueerd met een Round Robin-mechanisme met de verhouding van de opgegeven gewichten. Als de latentie gevoeligheid wordt ingesteld op 0 milliseconden, wordt deze eigenschap pas van kracht als er twee back-ends zijn met dezelfde netwerk latentie. 

De methode weighted maakt enkele handige scenario's mogelijk:

* **Geleidelijke upgrade** van de toepassing: geeft een percentage van het verkeer dat naar een nieuwe back-end kan worden gerouteerd en verhoogt het verkeer gedurende een periode geleidelijk om dit gemiddeld met andere back-ends te brengen.
* **Toepassings migratie naar Azure**: Maak een back-end-groep met zowel Azure als externe back-endservers. Pas het gewicht van de back-ends aan om de voor keur te geven aan de nieuwe back-end. U kunt dit geleidelijk instellen, te beginnen met het uitschakelen van de nieuwe back-ups en vervolgens de laagste gewichten toewijzen, waardoor deze langzaam toeneemt op het niveau van het verkeer. Vervolgens worden de minder voorkeurs back-ends uitgeschakeld en verwijderd uit de groep.  
* **Cloud-bursting voor extra capaciteit**: Vouw snel een on-premises implementatie uit in de Cloud door deze achter de deur te plaatsen. Wanneer u extra capaciteit nodig hebt in de Cloud, kunt u meer back-endservers toevoegen of inschakelen en opgeven welk deel van het verkeer naar elke back-end gaat.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een voor deur standaard/Premium](create-front-door-portal.md)
