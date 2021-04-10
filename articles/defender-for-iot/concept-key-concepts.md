---
title: Belangrijkste voor delen
description: Meer informatie over Basic Defender voor IoT-concepten.
ms.date: 12/13/2020
ms.topic: article
ms.openlocfilehash: ca1e5a4d8554b208f5275fd0e7519f2db3fafc08
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784744"
---
# <a name="basic-concepts"></a>Basisbegrippen 

In dit artikel worden de belangrijkste voor delen van Azure Defender voor IoT beschreven.

## <a name="rapid-non-invasive-deployment-and-passive-monitoring"></a>Snelle, niet-invasieve implementatie en passieve bewaking

Defender voor IoT Sens oren maakt verbinding met switch-poorten (mirror) en netwerk kranen en beginnen onmiddellijk met het verzamelen van ICS-netwerk verkeer via passieve bewaking (zonder agent). Diepe pakket inspectie (DPI) wordt gebruikt voor het dissect van verkeer van zowel het seriële als het Ethernet-beheer netwerk apparatuur. Defender voor IoT heeft geen invloed op de netwerken, omdat deze niet worden geplaatst in het gegevenspad en niet actief worden gescand op apparaten. 

Voor het leveren van directe moment opnamen van gedetailleerde Windows-apparaatgegevens, kan Defender voor IoT-sensor worden geconfigureerd voor een extra passieve bewaking met een optioneel actief onderdeel. Dit onderdeel maakt gebruik van veilige, door de leverancier goedgekeurde opdrachten om Windows-apparaten te doorzoeken op apparaatgegevens, zo vaak of zo vaak als u wilt.

## <a name="embedded-knowledge-of-ics-protocols-devices-and-applications"></a>Inge sloten kennis van ICS-protocollen,-apparaten en-toepassingen

Alleen DPI is voldoende voor het identificeren van protocol afwijkingen en het identificeren van het apparaat op een gedetailleerd niveau. De Defender voor IoT-sensor behandelt een aantal van de grootste en meest complexe omgevingen. Meer dan 1.300-netwerken zijn op de datum geanalyseerd, in alle industriële sectoren.

## <a name="analytics-and-self-learning-engines"></a>Analyse en self-learning motoren

Engines identificeren beveiligings problemen via voortdurende bewaking en vijf analyse-engines met zelf studie om te voor komen dat hand tekeningen worden bijgewerkt of regels worden gedefinieerd. De engines gebruiken ICS-specifieke gedrags analyse en data Science om voortdurend het netwerk verkeer voor afwijkingen te analyseren. De vijf motoren zijn:

- **Detectie van protocol schendingen**: Hiermee wordt het gebruik van pakket structuren en veld waarden aangegeven die in strijd zijn met de specificaties van het ICS-protocol.

- **Detectie van beleids overtreding**: identificeert beleids schendingen, zoals onbevoegd gebruik van functie codes, toegang tot specifieke objecten of wijzigingen in de apparaatconfiguratie.

- **Industriële detectie van malware**: identificeert gedrag dat aangeeft dat er sprake is van bekende schadelijke software, zoals Conficker, Black Energy, WannaCry en NotPetya.

- **Anomalie detectie**: detecteert ongebruikelijke M2M-communicatie (machine-to-machine) en gedrag. Door ICS-netwerken te model leren als deterministische reeksen van statussen en overgangen, gebruikt de engine een gepatenteerde techniek met de naam IFSM (Industrial eindig State Modeling). De oplossing vereist een kortere leer periode dan de generieke mathematische benaderingen of analyses, die oorspronkelijk voor het eerst zijn ontwikkeld in plaats van de oorspronkelijke voor waarde. Er worden ook afwijkingen sneller gedetecteerd, met minimale valse positieven.

- **Detectie van operationele incidenten**: identificeert operationele problemen, zoals onregelmatige connectiviteit, waarmee vroegtijdige symptomen van een defecte apparatuur kunnen worden aangegeven.

## <a name="network-traffic-analysis-for-risk-and-vulnerability-assessment"></a>Netwerk verkeer analyse voor risico-en beveiligings evaluatie

De unieke in de branche maakt Defender voor IoT gebruik van eigen NTA-algoritmen (Network Traffic Analysis) om alle netwerk-en eindpunt beveiligings lekken te identificeren, zoals:

- Niet-gemachtigde RAS-verbindingen
- Rogue of niet-gedocumenteerde apparaten
- Zwakke verificatie
- Kwets bare apparaten (op basis van niet-gepatchde CVEs)
- Niet-geautoriseerde bruggen tussen subnetten
- Zwakke firewall regels

## <a name="data-mining-for-investigations-forensics-and-threat-hunting"></a>Gegevens analyse voor onderzoek, forensische en Threat-jacht

Het platform biedt een intuïtieve interface voor gegevens analyse voor een nauw keurige zoek opdracht van historisch verkeer over alle relevante dimensies. Voor beelden zijn onder meer tijds periode, IP-adres, MAC-adres en poorten. U kunt ook protocolspecifieke query's maken op basis van functie codes, Protocol services en modules. PCAPs met volledige betrouw baarheid zijn beschikbaar voor verdere analyse van inzoomen.

## <a name="sensor-cloud-management-mode"></a>Modus voor sensor Cloud beheer

De sensor Cloud beheer modus bepaalt waar apparaat, waarschuwing en andere informatie die door de sensor wordt gedetecteerd, worden weer gegeven.

Voor **met Clouds verbonden Sens oren** wordt informatie die de sensor detecteert weer gegeven in de sensor console. Waarschuwings informatie wordt geleverd via een IoT-hub en kan worden gedeeld met andere Azure-Services, zoals Azure Sentinel.

Voor **lokaal verbonden Sens oren** wordt informatie die de sensor detecteert weer gegeven in de sensor console. Detectiegegevens worden ook gedeeld met de on-premises beheerconsole als de sensor hieraan is gekoppeld.

## <a name="air-gapped-networks"></a>Gapped netwerken

Als u in een gapped omgeving werkt, biedt de on-premises beheer console in Defender voor IoT een real-time weer gave van de belangrijkste IoT-en OT-risico indicatoren en waarschuwingen over al uw faciliteiten. Het is nauw geïntegreerd met uw SOC-werk stromen en-runbooks en maakt het eenvoudig om de prioriteit van de oplossings activiteiten en de correlatie van bedreigingen op meerdere locaties te verbeteren.  

Defender voor IoT biedt een geconsolideerde weer gave van al uw apparaten. Het bevat ook essentiële informatie over de apparaten, zoals het type (PLC, RTU, DC'S en meer), de fabrikant, het model en het revisie niveau van de firmware, evenals informatie over de waarschuwingen.  

Met Defender voor IoT kunt u een effectief beheer van meerdere implementaties en een uitgebreide, uniforme weer gave van het netwerk. Defender voor IoT optimaliseert de verwerking van waarschuwingen en het beheren van de operationele netwerk beveiliging.

De on-premises beheer console is een webgebaseerd beheer platform waarmee u de activiteiten van globale sensor installaties kunt bewaken en beheren. Naast het beheren van de gegevens die zijn ontvangen van geïmplementeerde Sens oren, integreert de on-premises beheer console naadloos gegevens uit verschillende bedrijfs bronnen: CMDB, DNS, firewalls, Web-Api's en meer.

:::image type="content" source="media/concept-air-gapped-networks/site-management-alert-screen.png" alt-text="On-premises beheer console weer geven.":::

Het is raadzaam dat u vertrouwd bent met de concepten, mogelijkheden en functies die beschikbaar zijn voor Sens oren voordat u met de on-premises beheer console werkt.

## <a name="integrations"></a>Integraties

U kunt de mogelijkheden van Defender voor IoT uitbreiden door zowel apparaat-als waarschuwings gegevens met partner systemen te delen. Integraties helpen ondernemingen eerder gestuurde beveiligings oplossingen te gebruiken om de zicht baarheid en bedreigings informatie van het apparaat aanzienlijk te verbeteren. Dankzij integraties kunnen bedrijven de volledige systeem reacties ook versnellen en risico's sneller beperken. 

Integraties verminderen de complexiteit en elimineren deze en de silo's door ze te integreren in uw bestaande SOC-werk stromen en beveiligings stack. Bijvoorbeeld:

- Siem's zoals IBM QRadar, Splunk, ArcSight, LogRhythm en RSA-netwitness

- Beveiligings-en ticket systemen zoals ServiceNow en IBM-robuust

- Veilige oplossingen voor externe toegang, zoals CyberArk Privilegeed Session Manager (PSM) en BeyondTrust

- Beveiligde NAC-systemen (Network Access Control) zoals Aruba ClearPass en Forescout

- Firewalls zoals Fortinet en Check Point

## <a name="complete-protocol-support"></a>Volledige protocol ondersteuning

Naast ondersteuning voor Inge sloten protocol kunt u IoT-en ICS-apparaten beveiligen die gebruikmaken van eigen en aangepaste protocollen, of protocollen die afwijken van de standaard. Ontwikkel aars kunnen met behulp van de node-SDK (Open Development Environment) Dissector-invoeg toepassingen maken waarmee netwerk verkeer wordt gedecodeerd op basis van gedefinieerde protocollen. Services analyseren het verkeer om te zorgen voor volledige bewaking, waarschuwingen en rapportage. Gebruik de Horizony tot:

- Breid zicht baarheid en beheer uit zonder dat u de nieuwe versies hoeft te upgraden.

- Beveilig eigendoms gegevens door on-site te ontwikkelen als een externe invoeg toepassing.

- Lokalisatie van tekst voor waarschuwingen, gebeurtenissen en protocol parameters.

Daarnaast kunt u eigen protocol waarschuwingen gebruiken om informatie te communiceren:

- Over het detecteren van verkeer op basis van protocollen en onderliggende protocollen in een eigen horizon-invoeg toepassing.

- Over een combi natie van protocol velden van alle protocol lagen. In een omgeving met MODBUS kunt u bijvoorbeeld een waarschuwing genereren wanneer de sensor een schrijf opdracht voor een geheugen registratie op een specifiek IP-adres en Ethernet-bestemming detecteert. Het is ook mogelijk dat u een waarschuwing wilt genereren wanneer een wille keurige toegang tot een specifiek IP-adres wordt uitgevoerd.

Waarschuwingen worden geactiveerd wanneer wordt voldaan aan de voor waarden van de horizon-waarschuwings regel.

Daarnaast kunt u met aangepaste horizon-waarschuwingen uw eigen waarschuwings titels en berichten schrijven. Omgezette protocol velden en-waarden kunnen worden inge sloten in de tekst van het waarschuwings bericht.

Door gebruik te maken van aangepaste waarschuwingen op basis van voor waarden en Messa ging helpt u specifieke netwerk activiteiten te herkennen en uw beveiliging, IT en operationele teams effectief bij te werken.


## <a name="high-availability"></a>Hoge beschikbaarheid

Verhoog de flexibiliteit van uw Defender voor IoT-implementatie door een apparaat voor hoge Beschik baarheid te installeren in de on-premises beheer console. Implementaties met een hoge Beschik baarheid zorgen ervoor dat uw beheerde Sens oren continu rapporteren aan een actieve on-premises beheer console.

Deze implementatie wordt geïmplementeerd met een on-premises beheer console paar dat een primair en secundair apparaat bevat.

## <a name="localization"></a>Lokalisatie

Veel console functies bieden ondersteuning voor een groot aantal talen.

## <a name="next-step"></a>Volgende stap

[Aan de slag met Defender voor IoT](getting-started.md)
