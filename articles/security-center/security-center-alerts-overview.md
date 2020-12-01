---
title: Beveiligings waarschuwingen in Azure Security Center | Microsoft Docs
description: In dit onderwerp wordt uitgelegd welke beveiligings waarschuwingen er zijn en welke verschillende typen beschikbaar zijn in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: memildin
ms.openlocfilehash: c1406e6a1f2f36c5d42544c52ade3f991fe2ba0a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433016"
---
# <a name="security-alerts-in-azure-security-center"></a>Beveiligingswaarschuwingen in Azure Security Center

In Security Center zijn er diverse waarschuwingen voor veel verschillende resource typen. Security Center genereert waarschuwingen voor resources die zijn geïmplementeerd op Azure en ook voor resources die zijn geïmplementeerd op on-premises en hybride Cloud omgevingen.

Beveiligings waarschuwingen worden geactiveerd door geavanceerde detecties en zijn alleen beschikbaar met Azure Defender. Er is een gratis proefversie beschikbaar. U kunt de pagina **prijzen &-instellingen** bijwerken. Meer informatie over [Security Center prijzen](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="respond-to-todays-threats"></a>Reageer op de bedreigingen <a name="respond-threats"> </a> van vandaag

De bedreigingen zijn de afgelopen 20 jaar aanzienlijk veranderd. In het verleden moesten bedrijven meestal zich richten op een website met individuele aanvallers die meestal geïnteresseerd zijn in de weer gave ' wat ze zouden kunnen doen '. De huidige aanvallers zijn veel geavanceerder en georganiseerd. Ze hebben vaak specifieke financiële en strategische doelstellingen. Ze hebben ook meer bronnen beschikbaar, aangezien ze kunnen worden gefinancierd door de status van de Staten of een georganiseerde criminaliteit.

Deze veranderende vaststaande hebben geleid tot een ongekend niveau van professionalisatie in de aanvals classificaties. Ze zijn niet langer geïnteresseerd in het beschadigen van websites. Ze zijn nu geïnteresseerd in het stelen van informatie, financiële accounts en persoonlijke gegevens, die allemaal kunnen worden gebruikt om geld te genereren op de open markt of om gebruik te maken van een bepaalde bedrijfs-, politieke of militaire positie. Nog zorgwekkender dan de aanvallers met financiële oogmerken zijn de aanvallers die netwerken kraken om schade te berokkenen aan de infrastructuur en personen.

Organisaties implementeren als antwoord vaak verschillende afzonderlijke oplossingen die zich richten op het verdedigen van de bedrijfsomtrek of de eindpunten waarbij specifieke aanvalskenmerken in de gaten worden gehouden. Deze oplossingen genereren meestal een groot aantal onbetrouwbare waarschuwingen die een beveiligingsanalist vervolgens moet bekijken en onderzoeken. De meeste organisaties hebben niet de tijd en expertise die vereist zijn om te reageren op deze waarschuwingen - vele worden niet nader onderzocht.  

Daarnaast hebben aanvallers hun methoden ontwikkeld om veel op hand tekeningen gebaseerde verdedigingen te doen en zich aan [te passen aan Cloud omgevingen](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Nieuwe handelswijzen zijn vereist om nieuwe bedreigingen sneller te identificeren, te detecteren en erop te reageren.

## <a name="what-are-security-alerts-and-security-incidents"></a>Wat zijn beveiligings waarschuwingen en beveiligings incidenten? 

**Waarschuwingen** zijn de meldingen die Security Center gegenereerd wanneer er bedreigingen voor uw resources worden gedetecteerd. Security Center prioriteiten en de waarschuwingen weer gegeven, samen met de informatie die u nodig hebt om snel het probleem te onderzoeken. Security Center geeft ook aanbevelingen voor hoe u een aanval kunt oplossen.

**Een beveiligings incident** is een verzameling van gerelateerde waarschuwingen, in plaats van elke waarschuwing afzonderlijk weer te geven. Security Center maakt gebruik van de correlatie van een [Smart-waarschuwing](security-center-alerts-cloud-smart.md) in de cloud om verschillende waarschuwingen en signalen met lage betrouw baarheid te correleren in beveiligings incidenten

Met behulp van incidenten Security Center biedt u één weer gave van een aanvals campagne en alle gerelateerde waarschuwingen. Met deze weer gave kunt u snel inzicht krijgen in welke acties de aanvaller heeft geduurd en welke resources zijn beïnvloed. Zie [Cloud Smart alert correlatie](security-center-alerts-cloud-smart.md)(Engelstalig) voor meer informatie.



## <a name="how-does-security-center-detect-threats"></a>Hoe detecteert Security Center bedreigingen? <a name="detect-threats"> </a>

Beveiligingsonderzoekers van Microsoft zijn voortdurend op zoek naar bedreigingen. Vanwege onze wereld wijde aanwezigheid in de Cloud en on-premises, hebben we toegang tot een expansieve, set telemetrie. Dankzij de uitgebreide en gevarieerde verzameling gegevens sets kunnen wij nieuwe aanvals patronen en trends ontdekken over onze on-premises consumenten-en bedrijfs producten, evenals onze onlineservices. Als gevolg hiervan kan Security Center de detectie-algoritmen snel bijwerken wanneer aanvallers met nieuwe en steeds meer geavanceerde aanvallen komen. Met deze benadering kunt u de snel veranderende bedreigingen bijhouden.

Security Center verzamelt, analyseert en integreert de logboek gegevens van uw Azure-resources en het netwerk om echte bedreigingen te detecteren en fout-positieven te verminderen. Het werkt ook met verbonden partner oplossingen, zoals firewall-en eindpunt beveiligings oplossingen. Security Center analyseert deze gegevens, vaak het correleren van informatie uit meerdere bronnen, om bedreigingen te identificeren.

![Gegevensverzameling en -presentatie in Security Center](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Security Center maakt gebruik van geavanceerde beveiligingsanalyses die veel verder gaan dan op handtekeningen gebaseerde benaderingen. Doorbraken in big data- en [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/)-technologieën worden gebruikt om gebeurtenissen overal in de cloudinfrastructuur te evalueren - waarbij bedreigingen worden gedetecteerd die onmogelijk te identificeren waren geweest met behulp van handmatige benaderingen en het voorspellen van de ontwikkeling van aanvallen. Deze beveiligingsanalyses omvatten:

* **Geïntegreerde bedreigings informatie**: micro soft heeft een enorme hoeveelheid wereld wijde bedreigings informatie. Telemetrie loopt over van meerdere bronnen, zoals Azure, Microsoft 365, micro soft CRM Online, micro soft Dynamics AX, outlook.com, MSN.com, de micro soft Digital misdrijven Unit (DCU) en het micro soft Security Response Center (MSRC). Onderzoekers ontvangen ook informatie over bedreigingen die worden gedeeld door grote Cloud serviceproviders en feeds van andere derden. Azure Security Center kan deze informatie gebruiken om u te waarschuwen over bedreigingen van bekende beveiligingsrisico’s.

* **Gedrags analyse**: gedrags analyse is een techniek die gegevens analyseert en vergelijkt met een verzameling van bekende patronen. Deze patronen zijn echter geen eenvoudige handtekeningen. Ze worden vastgesteld aan de hand van complexe machine learning-algoritmen die worden toegepast op grote gegevenssets. Ze worden ook vastgesteld via de zorgvuldige analyse van schadelijk gedrag door deskundige analisten. Azure Security Center kunt gedrags analyse gebruiken om gemanipuleerde resources te identificeren op basis van de analyse van logboeken van virtuele machines, logboeken voor virtuele netwerk apparaten, infrastructuur logboeken en andere bronnen.

* **Afwijkings detectie**: Azure Security Center gebruikt ook anomalie detectie om bedreigingen te identificeren. In tegens telling tot gedrags analyse (die afhankelijk is van bekende patronen die zijn afgeleid van grote gegevens sets), is afwijkings detectie meer ' persoonlijk ' en wordt er gekeken naar basis lijnen die specifiek zijn voor uw implementaties. Machine learning wordt toegepast om de normale activiteit voor uw implementaties te bepalen en vervolgens worden regels gegenereerd om afwijkende omstandigheden te definiëren die een veiligheidsrisico zouden kunnen vormen.

## <a name="how-are-alerts-classified"></a>Hoe worden waarschuwingen geclassificeerd?

Security Center wijst een Ernst toe aan waarschuwingen, zodat u de volg orde kunt bepalen van elke waarschuwing, zodat u meteen aan de voor waarde wordt gevraagd wanneer een bron is aangetast. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

> [!NOTE]
> Ernst van waarschuwing wordt anders weer gegeven in de portal en de versies van de REST API die vóór datum 01-01-2019. Als u een oudere versie van de API gebruikt, moet u een upgrade uitvoeren voor de consistente ervaring die hieronder wordt beschreven.

- **Hoog:** Er is een hoge waarschijnlijkheid dat uw resource is aangetast. U ziet het meteen. Security Center heeft hoge betrouw baarheid in zowel de schadelijke intentie als de bevindingen die worden gebruikt voor het uitgeven van de waarschuwing. Een waarschuwing waarmee de uitvoering van een bekend schadelijk hulp programma, zoals Mimikatz, wordt gedetecteerd, is een algemeen hulp programma dat wordt gebruikt voor referentie diefstal.
- **Gemiddeld:** Dit is waarschijnlijk een verdachte activiteit kan erop wijzen dat een bron is aangetast.
Het vertrouwen van Security Center in het analyse programma of het zoeken is gemiddeld en het vertrouwen van de schadelijke intentie is gemiddeld voor hoog. Deze worden doorgaans machine learning en detecties op basis van anomalieën. Bijvoorbeeld een aanmeldings poging vanaf een afwijkende locatie.
- **Laag:** Dit kan een goed aardige positieve of een geblokkeerde aanval zijn.
   * Security Center niet zeker weet dat de intentie schadelijk is en de activiteit mogelijk onschuld is. Logboek wissen is bijvoorbeeld een actie die zich kan voordoen wanneer een aanvaller hun tracks probeert te verbergen, maar in veel gevallen is een routine bewerking door beheerders uitgevoerd.
   * Security Center vertelt u doorgaans niet wanneer er aanvallen zijn geblokkeerd, tenzij dit een interessant geval is. 
- **Ter informatie:** Er worden alleen informatieve waarschuwingen weer geven wanneer u inzoomt op een beveiligings incident of als u de REST API met een specifieke waarschuwings-ID gebruikt. Een incident bestaat doorgaans uit een aantal waarschuwingen, wat soms alleen ter informatie zou worden weer gegeven, maar in de context van de andere waarschuwingen kan betrouw bare een nauwere blik zijn. 

## <a name="continuous-monitoring-and-assessments"></a>Doorlopende bewaking en evaluaties

Azure Security Center voor delen van het uitvoeren van beveiligings onderzoek en data Science teams in micro soft die voortdurend controleren op wijzigingen in de beveiliging tegen bedreigingen. Dit omvat de volgende initiatieven:

* **Threat Intelligence-bewaking**: bedreigings informatie omvat mechanismen, indica toren, implicaties en bruikbare adviezen over bestaande of opkomende bedreigingen. Deze informatie wordt gedeeld in de beveiligingscommunity en Microsoft volgt continu feeds met informatie over bedreigingen uit interne en externe bronnen.
* **Signaal deling**: inzichten van beveiligings teams in het brede assortiment van de Cloud en on-premises services van micro soft, servers en client eindpunt apparaten worden gedeeld en geanalyseerd.
* **Microsoft-beveiligingsspecialisten**: continue inzet van teams overal bij Microsoft die op gespecialiseerde beveiligingsgebieden werken, zoals forensisch onderzoek en webaanvaldetectie.
* **Detectieafstemming**: algoritmen worden uitgevoerd op echte gegevenssets van klanten en beveiligingsonderzoekers werken samen met klanten om de resultaten te valideren. Echte en fout-positieven worden gebruikt voor het verfijnen van machine learning-algoritmen.

Deze gecombineerde inspanningen culminate in nieuwe en verbeterde detecties, die u direct kunt voor delen. u hoeft zich geen actie te ondernemen.

## <a name="export-alerts"></a>Waarschuwingen exporteren

U hebt verschillende mogelijkheden voor het weer geven van uw waarschuwingen buiten Security Center, waaronder:

- **Down load het CSV-rapport** in het dash board waarschuwingen en biedt een eenmalige export naar CSV.
- Met **continue export** van prijzen & instellingen kunt u stromen van beveiligings waarschuwingen en aanbevelingen configureren voor log Analytics werk ruimten en Event hubs. [Meer informatie over continue export](continuous-export.md)
- Beveiligings waarschuwingen van Azure Security Center worden door **Azure Sentinel connector** naar Azure Sentinel gestreamd. [Meer informatie over het verbinden van Azure Security Center met Sentinel](../sentinel/connect-azure-security-center.md)


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de verschillende soorten waarschuwingen die beschikbaar zijn in Security Center. Zie voor meer informatie:

- **Beveiligings waarschuwingen in azure-activiteiten logboek** -naast de beschik baarheid van de Azure portal of programmatisch, worden beveiligings waarschuwingen en incidenten gecontroleerd als gebeurtenissen in [Azure-activiteiten logboek](../azure-monitor/platform/activity-log.md#view-the-activity-log). Zie [beveiligings waarschuwingen in azure-activiteiten logboek](https://go.microsoft.com/fwlink/?linkid=2114113) voor meer informatie over het gebeurtenis schema