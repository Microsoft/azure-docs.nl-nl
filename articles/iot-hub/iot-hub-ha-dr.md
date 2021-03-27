---
title: Azure IoT Hub hoge Beschik baarheid en herstel na nood geval | Microsoft Docs
description: Beschrijft de functies van Azure en IoT Hub die u helpen bij het bouwen van Maxi maal beschik bare Azure IoT-oplossingen met mogelijkheden voor herstel na nood gevallen.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: philmea
ms.openlocfilehash: 9d2ffac813456398c02066c978c37bdb09501aeb
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628982"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Hoge beschikbaarheid en herstel na noodgevallen van IoT Hub

Als eerste stap voor het implementeren van een robuuste IoT-oplossing, moeten architecten, ontwikkel aars en zakelijke eigen aren de doel stellingen van de uptime definiëren voor de oplossingen die ze bouwen. Deze doel stellingen kunnen voornamelijk worden gedefinieerd op basis van specifieke zakelijke doel stellingen voor elk scenario. In deze context wordt in het artikel [technische richt lijnen voor Azure-bedrijfs continuïteit](/azure/architecture/resiliency/) beschreven, waarmee u kunt denken over bedrijfs continuïteit en herstel na nood gevallen. Het document [nood herstel en hoge Beschik baarheid voor Azure-toepassingen](/azure/architecture/reliability/disaster-recovery) biedt architectuur richtlijnen voor strategieën voor Azure-toepassingen om hoge beschik BAARHEID (ha) en herstel na nood gevallen (Dr) te kunnen uitvoeren.

In dit artikel worden de HA-en DR-functies beschreven die specifiek door de IoT Hub-service worden aangeboden. De algemene gebieden die in dit artikel worden besproken, zijn:

- Binnenste regio HA
- DR-regio tussen regio's
- Kruis regio HA bereiken

Afhankelijk van de doel stellingen van uw IoT-oplossingen, moet u bepalen welke van de onderstaande opties het beste aansluiten bij uw zakelijke doel stellingen. Het opnemen van een van deze HA/DR-alternatieven in uw IoT-oplossing vereist een zorgvuldige evaluatie van de wissel werking tussen de volgende zaken:

- Niveau van tolerantie dat u nodig hebt 
- Complexiteit van implementatie en onderhoud
- KPV-impact

## <a name="intra-region-ha"></a>Binnenste regio HA

De IoT Hub-service voorziet in binnen regio HA door redundantie in bijna alle lagen van de service te implementeren. De [Sla die wordt gepubliceerd door de IOT hub-service](https://azure.microsoft.com/support/legal/sla/iot-hub) wordt bereikt door gebruik te maken van deze redundantie. De ontwikkel aars van een IoT-oplossing hoeven verder niets te doen om te profiteren van deze HA-functies. Hoewel IoT Hub een redelijk hoge uptime biedt, kunnen tijdelijke fouten nog steeds worden verwacht als bij een gedistribueerd computing platform. Als u zojuist aan de slag gaat met het migreren van uw oplossingen naar de Cloud vanuit een on-premises oplossing, moet u de nadruk op de gemiddelde tijd van de tijd tussen storingen bepalen. Met andere woorden, tijdelijke fouten worden als normaal beschouwd tijdens het werken met de cloud in de mix. Het juiste [beleid voor opnieuw proberen](iot-hub-reliability-features-in-sdks.md) moet zijn ingebouwd in de onderdelen die communiceren met een Cloud toepassing om te kunnen omgaan met tijdelijke fouten.

> [!NOTE]
> Sommige Azure-Services bieden ook extra Beschik baarheid in een regio door te integreren met [Beschikbaarheidszones (AZs)](../availability-zones/az-overview.md). AZs worden momenteel niet ondersteund door de IoT Hub-service.

## <a name="cross-region-dr"></a>DR-regio tussen regio's

Er kunnen zeldzame situaties optreden wanneer een Data Center uitvallende storingen veroorzaakt door stroom storingen of andere storingen met betrekking tot fysieke activa. Dergelijke gebeurtenissen zijn zeldzame gevallen waarin de functie voor de intra regio HA die hierboven wordt beschreven wellicht niet altijd meer helpt. IoT Hub biedt meerdere oplossingen voor het herstellen van dergelijke uitgebreide storingen. 

De herstel opties die beschikbaar zijn voor klanten in een dergelijke situatie, zijn door [micro soft geïnitieerde failover](#microsoft-initiated-failover) en [hand matige failover](#manual-failover). Het belangrijkste verschil tussen de twee is dat micro soft de eerste initieert en de gebruiker initieert de laatste. Daarnaast biedt hand matige failover een lagere herstel tijd (RTO) in vergelijking met de optie voor door micro soft geïnitieerde failover. De specifieke Rto's die bij elke optie worden aangeboden, worden besproken in de volgende secties. Wanneer een van deze opties voor het uitvoeren van een failover van een IoT-hub vanuit de primaire regio wordt uitgevoerd, wordt de hub volledig functioneel in de bijbehorende [Azure geo-gekoppelde regio](../best-practices-availability-paired-regions.md).

Beide failover-opties bieden de volgende herstel punt doelstellingen (Rpo's):

| Gegevenstype | Herstel punt doelstellingen (RPO) |
| --- | --- |
| Id-REGI ster |0-5 minuten gegevens verlies |
| Dubbele gegevens van apparaat |0-5 minuten gegevens verlies |
| Cloud-naar-apparaat-berichten<sup>1</sup> |0-5 minuten gegevens verlies |
| Taken van bovenliggend<sup>1</sup> en apparaat |0-5 minuten gegevens verlies |
| Apparaat-naar-cloud-berichten |Alle ongelezen berichten gaan verloren |
| Bewakings berichten voor bewerkingen |Alle ongelezen berichten gaan verloren |
| Feedback berichten van Cloud naar apparaat |Alle ongelezen berichten gaan verloren |

<sup>1</sup> Cloud-naar-apparaat-berichten en bovenliggende taken worden niet hersteld als onderdeel van een hand matige failover.

Zodra de failoverbewerking voor de IoT-hub is voltooid, worden alle bewerkingen van het apparaat en de back-end-toepassingen verwacht te blijven werken zonder dat hiervoor hand matige interventie nodig is. Dit betekent dat uw apparaat-naar-Cloud-berichten moeten blijven werken en dat het hele REGI ster van het apparaat intact is. Gebeurtenissen die via Event Grid worden gegenereerd, kunnen worden gebruikt via dezelfde abonnement (en) die eerder zijn geconfigureerd, mits deze Event Grid abonnementen nog steeds beschikbaar zijn. Er is geen verdere verwerking vereist voor aangepaste eind punten.

> [!CAUTION]
> - De Event hub-compatibele naam en het eind punt van het eind punt van de IoT Hub ingebouwde gebeurtenissen na een failover wijzigen. Bij het ontvangen van telemetriegegevens van het ingebouwde eind punt met behulp van de Event hub-client of de Event processor host, moet u [de Connection String IOT hub gebruiken](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) om de verbinding tot stand te brengen. Zo zorgt u ervoor dat uw back-end-toepassingen blijven werken zonder dat er hand matige interventie post-failover is vereist. Als u de Event hub-compatibele naam en het eind punt in uw toepassing rechtstreeks gebruikt, moet u na [de failover het nieuwe event hub-compatibele eind punt ophalen om de](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) bewerkingen voort te zetten. 
>
> - Als u Azure Functions of Azure Stream Analytics gebruikt om verbinding te maken met het ingebouwde eind punt van gebeurtenissen, moet u mogelijk **opnieuw opstarten**. Dit komt doordat tijdens de voorafgaande failover geen geldige offset meer geldig zijn.
>
> - Bij het door sturen naar de opslag wordt u aangeraden de blobs of bestanden weer te geven en vervolgens te herhalen, om ervoor te zorgen dat alle blobs of bestanden worden gelezen zonder dat er veronderstellingen worden gemaakt van de partitie. Het partitie bereik kan mogelijk worden gewijzigd tijdens een door micro soft geïnitieerde failover of hand matige failover. U kunt de [List blobs API](/rest/api/storageservices/list-blobs) gebruiken om de lijst met blobs of de [lijst ADLS Gen2-API](/rest/api/storageservices/datalakestoragegen2/filesystem/listpaths) voor de lijst met bestanden op te sommen. Zie [Azure Storage als een eind punt van een route ring](iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint)voor meer informatie.

## <a name="microsoft-initiated-failover"></a>Door micro soft geïnitieerde failover

Door micro soft geïnitieerde failover wordt door micro soft in zeldzame gevallen uitgevoerd om alle IoT-hubs te failoveren vanuit een betrokken regio naar de overeenkomstige geografische paar regio. Dit proces is een standaard optie (geen manier om gebruikers te kiezen) en vereist geen tussen komst van de gebruiker. Micro soft behoudt zich het recht voor om te bepalen wanneer deze optie wordt uitgeoefend. Dit mechanisme heeft geen toestemming van een gebruiker voor het uitvoeren van een failover van de gebruiker. Failover die door micro soft is gestart, heeft een beoogde herstel tijd (RTO) van 2-26 uur. 

De grote RTO is omdat micro soft de failover-bewerking moet uitvoeren namens alle betrokken klanten in die regio. Als u een minder kritieke IoT-oplossing gebruikt die zich ongeveer een dag bevindt, is het raadzaam dat u een afhankelijkheid neemt van deze optie om te voldoen aan de algemene doel stellingen voor herstel na nood gevallen voor uw IoT-oplossing. De totale tijd dat runtime-bewerkingen volledig operationeel worden zodra dit proces wordt geactiveerd, wordt beschreven in de sectie ' te herstellen tijdstip '.

## <a name="manual-failover"></a>Handmatige failover

Als de doel stellingen van uw bedrijf niet voldoen aan de RTO die door micro soft is gestart, kunt u een hand matige failover uitvoeren om het failoverproces zelf te activeren. De RTO die deze optie gebruikt, kan een wille keurige periode van tien minuten tot enkele uren zijn. De RTO is momenteel een functie van het aantal apparaten dat is geregistreerd voor de IoT hub-instantie waarvoor failover is uitgevoerd. U kunt verwachten dat de RTO voor een hub die ongeveer 100.000 apparaten host, zich in de indicatieve van 15 minuten bevindt. De totale tijd dat runtime-bewerkingen volledig operationeel worden zodra dit proces wordt geactiveerd, wordt beschreven in de sectie ' te herstellen tijdstip '.

De optie hand matige failover is altijd beschikbaar voor gebruik, ongeacht of er sprake is van uitval tijd van de primaire regio. Daarom kan deze optie mogelijk worden gebruikt voor het uitvoeren van geplande failovers. Een voor beeld van het gebruik van geplande failovers is het uitvoeren van periodieke failover-oefeningen. Een waarschuwing dat een geplande failover-bewerking resulteert in een downtime voor de hub gedurende de periode die is gedefinieerd door de RTO voor deze optie, en resulteert ook in een gegevens verlies zoals gedefinieerd in de bovenstaande tabel RPO. U kunt overwegen om een test IoT hub-exemplaar in te stellen om de optie voor de geplande failover regel matig uit te voeren, zodat u uw end-to-end-oplossingen optimaal kunt benutten wanneer een echte nood situatie optreedt.

De hand matige failover is zonder extra kosten beschikbaar voor IoT-hubs die zijn gemaakt na 18 mei 2017

Zie [zelf studie: hand matige failover uitvoeren voor een IOT-hub](tutorial-manual-failover.md) voor stapsgewijze instructies.

### <a name="running-test-drills"></a>Test oefeningen uitvoeren

Test oefeningen mogen niet worden uitgevoerd op IoT-hubs die worden gebruikt in uw productie omgevingen.

### <a name="dont-use-manual-failover-to-migrate-iot-hub-to-a-different-region"></a>Geen hand matige failover gebruiken om IoT hub te migreren naar een andere regio

Hand matige failover mag *niet* worden gebruikt als mechanisme voor het permanent migreren van uw hub tussen de met Azure geografische gekoppelde regio's. Dit verhoogt de latentie voor de bewerkingen die worden uitgevoerd op de IoT-hub van apparaten die zich in de oude primaire regio bevinden.

## <a name="failback"></a>Failback

Het is niet mogelijk om een back-up te maken van de oude primaire regio door de failover-actie nog een keer te activeren. Als de oorspronkelijke failoverbewerking werd uitgevoerd om een uitgebreide onderbreking in de oorspronkelijke primaire regio te herstellen, wordt aangeraden om de hub terug te zetten naar de oorspronkelijke locatie wanneer de locatie is hersteld van de onderbrekings situatie.

> [!IMPORTANT]
> - Gebruikers mogen alleen twee geslaagde failovers en twee geslaagde failback-bewerkingen per dag uitvoeren.
>
> - Terug naar back-failover/failback-bewerkingen zijn niet toegestaan. U moet één uur wachten tussen deze bewerkingen.

## <a name="time-to-recover"></a>Tijd om te herstellen

Hoewel de FQDN (en dus de connection string) van de IoT hub-instantie dezelfde post-failover blijft, verandert het onderliggende IP-adres. Daarom kan de algemene tijd voor het uitvoeren van runtime-bewerkingen ten opzichte van uw IoT hub-exemplaar volledig operationeel zijn nadat het failover-proces is geactiveerd, met behulp van de volgende functie.

Tijd voor herstel = RTO [10 min-2 uur voor hand matige failover | 2-26 uur voor door micro soft geïnitieerde failover] + DNS-doorgifte vertraging + tijd die door de client toepassing wordt gebruikt om alle in cache opgeslagen IoT Hub IP-adressen te vernieuwen.

> [!IMPORTANT]
> De IoT-Sdk's slaan het IP-adres van de IoT-hub niet op in de cache. We raden aan dat gebruikers code die de Sdk's heeft, de IP-adres van de IoT-hub niet in de cache opslaat.

## <a name="achieve-cross-region-ha"></a>Kruis regio HA behalen

Als de doel stellingen van uw bedrijf niet voldoen aan de RTO die door micro soft geïnitieerde failover of hand matige failover-opties bieden, kunt u overwegen een mechanisme voor automatische failover voor meerdere regio's te implementeren.
Een volledige behandeling van implementatie topologieën in IoT-oplossingen valt buiten het bereik van dit artikel. In het artikel wordt het *regionale* implementatie model voor failover beschreven voor hoge Beschik baarheid en herstel na nood gevallen.

In een regionaal failovercluster wordt de back-end van de oplossing voornamelijk op één datacenter locatie uitgevoerd. Een secundaire IoT-hub en back-end worden geïmplementeerd op een andere locatie van het Data Center. Als de IoT-hub in de primaire regio een storing ondervindt of de netwerk verbinding van het apparaat naar de primaire regio wordt onderbroken, gebruiken apparaten een secundair service-eind punt. U kunt de beschik baarheid van de oplossing verbeteren door een failover-model voor meerdere regio's te implementeren in plaats van binnen één regio te blijven. 

Op een hoog niveau moet u de volgende stappen uitvoeren om een regionaal failover-model te implementeren met IoT Hub:

* **Een secundaire IOT hub-en apparaat-routerings logica**: als de service in de primaire regio wordt onderbroken, moeten apparaten verbinding maken met uw secundaire regio. Gezien de status bewuste aard van de meeste betrokken services, is het gebruikelijk dat beheerders van de oplossing het failoverproces tussen regio's kunnen activeren. De beste manier om het nieuwe eind punt te communiceren met apparaten, met behoud van de controle over het proces, is ervoor te zorgen dat ze regel matig een *Concierge* -service controleren op het huidige actieve eind punt. De concierge-service kan een webtoepassing zijn die wordt gerepliceerd en bereikbaar is met behulp van technieken voor het omleiden van DNS (bijvoorbeeld met behulp van [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)).

   > [!NOTE]
   > De IoT hub-service is geen ondersteund eindpunt type in azure Traffic Manager. De aanbeveling is de voorgestelde Concierge-service te integreren met Azure Traffic Manager door deze de Endpoint Health probe-API te implementeren.

* **Identiteits register replicatie**: het kan alleen worden gebruikt als de secundaire IOT-hub alle apparaat-id's bevat waarmee verbinding kan worden gemaakt met de oplossing. In de oplossing moeten geo-gerepliceerde back-ups van apparaat-id's worden bewaard en geüpload naar de secundaire IoT-hub voordat het actieve eind punt voor de apparaten wordt overgeschakeld. De functie voor het exporteren van de apparaat-id van IoT Hub is nuttig in deze context. Zie [IOT hub ontwikkelaars handleiding-identiteits register](iot-hub-devguide-identity-registry.md)voor meer informatie.

* **Samen voegen van logica**: wanneer de primaire regio weer beschikbaar is, moeten alle status en gegevens die zijn gemaakt op de secundaire site terug worden gemigreerd naar de primaire regio. Deze status en gegevens zijn voornamelijk gerelateerd aan de apparaat-id's en meta gegevens van toepassingen, die moeten worden samengevoegd met de primaire IoT-hub en andere toepassingsspecifieke winkels in de primaire regio. 

Als u deze stap wilt vereenvoudigen, moet u idempotent-bewerkingen gebruiken. Met idempotent bewerkingen worden de neven effecten van de uiteindelijke consistente distributie van gebeurtenissen en van duplicaten of aflevering van gebeurtenissen geminimaliseerd. Daarnaast moet de toepassings logica worden ontworpen om mogelijke inconsistenties of een verouderde status te verdragen. Deze situatie kan zich voordoen als gevolg van de extra tijd die het systeem nodig heeft om op te lossen op basis van de herstel punt doelstellingen (RPO).

## <a name="choose-the-right-hadr-option"></a>Kies de optie juiste HA/DR

Hier volgt een samen vatting van de HA/DR-opties die in dit artikel worden gepresenteerd en die als referentie kader kunnen worden gebruikt om de juiste optie te kiezen die geschikt is voor uw oplossing.

| HA/DR-optie | RTO | RPO | Is hand matige interventie vereist? | Implementatie complexiteit | Extra kosten impact|
| --- | --- | --- | --- | --- | --- |
| Door micro soft geïnitieerde failover |2-26 uur|Bovenstaande RPO-tabel verwijzen|No|Geen|Geen|
| Handmatige failover |10 min-2 uur|Bovenstaande RPO-tabel verwijzen|Yes|Zeer laag. U hoeft alleen deze bewerking vanuit de portal te activeren.|Geen|
| Kruis regio HA |< 1 minuut|Is afhankelijk van de replicatie frequentie van uw aangepaste HA-oplossing|No|Hoog|> 1x de kosten van 1 IoT hub|

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure IoT Hub?](about-iot-hub.md)
* [Aan de slag met IoT hubs (Quick Start)](quickstart-send-telemetry-dotnet.md)
* [Zelfstudie: Handmatige failover uitvoeren voor een IoT-hub](tutorial-manual-failover.md)
