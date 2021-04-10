---
title: Back-ups en herstel na nood gevallen voor IaaS-schijven op virtuele Azure-machines
description: In dit artikel wordt uitgelegd hoe u een back-up en herstel na nood gevallen kunt plannen voor virtuele IaaS-machines en-schijven in Azure. In dit document worden zowel beheerde als onbeheerde schijven beschreven.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e5ae08c23748e55a8c3b75eb8fb9c112684f022e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102507903"
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Back-ups en herstel na nood gevallen voor Azure IaaS-schijven

In dit artikel wordt uitgelegd hoe u back-ups en herstel na noodgevallen van virtuele IaaS-machines en schijven in Azure kunt plannen. In dit document worden zowel beheerde als onbeheerde schijven beschreven.

Eerst beschrijven we de ingebouwde mogelijkheden voor fouttolerantie in het Azure-platform die helpen beschermen tegen lokale storingen. Vervolgens bespreken we de noodscenario's die niet volledig worden gedekt door de ingebouwde mogelijkheden. Er worden ook verschillende voorbeelden van workloadscenario's weergegeven waarin verschillende overwegingen voor back-ups en herstel na noodgevallen van toepassing kunnen zijn. Vervolgens worden mogelijke oplossingen voor herstel na noodgevallen van IaaS-schijven gecontroleerd.

## <a name="introduction"></a>Inleiding

Het Azure-platform gebruikt verschillende methoden voor redundantie en fouttolerantie om klanten te beschermen tegen lokale hardwarestoringen. Lokale storingen kunnen problemen omvatten met een Azure Storage-servermachine die een deel van de gegevens voor een virtuele schijf opslaat of storingen van een SSD of HDD op die server. Dergelijke storingen van afgeschermde hardwareonderdelen kunnen optreden tijdens normale bewerkingen.

Het Azure-platform is ontworpen om bestand te zijn tegen dit soort storingen. Grote rampen kunnen leiden tot storingen of het niet toegankelijk zijn van verschillende opslagservers of zelfs een heel datacentrum. Hoewel uw virtuele machines en schijven normaal gesproken worden beschermd tegen lokale storingen, zijn er aanvullende stappen nodig om uw workload te beschermen tegen onherstelbare storingen in de hele regio, zoals een grote ramp, die uw virtuele machine en schijven kan treffen.

Naast de mogelijkheid van platformstoringen, kunnen er ook problemen optreden met een klanttoepassing of klantgegevens. Een nieuwe versie van uw toepassing kan bijvoorbeeld per ongeluk een wijziging aanbrengen in de gegevens waardoor deze beschadigd raken. In dat geval wilt u mogelijk de toepassing en de gegevens terugzetten naar een eerdere versie die de laatst bekende juiste status bevat. Hiervoor moeten reguliere back-ups worden gemaakt.

Voor regionaal herstel na noodgevallen moet u een back-up maken van uw IaaS VM-schijven naar een andere regio.

Voordat we gaan kijken naar back-ups en opties voor herstel na noodgevallen gaan we enkele methoden bekijken die beschikbaar zijn voor het afhandelen van lokale storingen.

### <a name="azure-iaas-resiliency"></a>Tolerantie van Azure IaaS

*Tolerantie* verwijst naar de tolerantie voor normale storingen die zich voor kunnen doen in hardwareonderdelen. Tolerantie is het vermogen om storingen te herstellen en te kunnen blijven functioneren. Het gaat niet om het vermijden van storingen, maar om het reageren op storingen op een manier waarmee downtime of gegevensverlies wordt voorkomen. Het doel van flexibiliteit is om de toepassing na een storing weer volledig te laten functioneren. Virtuele machines en schijven van Azure zijn ontworpen om bestand te zijn tegen veelvoorkomende hardwarefouten. Laten we eens kijken hoe het Azure IaaS-platform deze tolerantie biedt.

Een virtuele machine bestaat uit twee hoofdonderdelen: een rekenserver en de permanente schijven. Beide zijn van invloed op de fouttolerantie van een virtuele machine.

Als de Azure Compute-hostserver die uw virtuele machine bevat een hardwarestoring heeft, wat zelden voorkomt, is Azure ontworpen om de VM automatisch te herstellen op een andere server. Als dit scenario zich voordoet, wordt de computer opnieuw opgestart en komt de virtuele machine na enige tijd terug. Azure detecteert dergelijke hardwarestoringen automatisch en voert herstelbewerkingen uit om ervoor te zorgen dat de virtuele machine van de klant zo snel mogelijk beschikbaar is.

Bij IaaS-schijven is de duurzaamheid van gegevens van essentieel belang voor een permanent opslag platform. Azure-klanten hebben belangrijke zakelijke toepassingen die worden uitgevoerd op IaaS en ze zijn afhankelijk van de persistentie van de gegevens. Azure is ontworpen om bescherming te bieden voor deze IaaS-schijven, met drie redundante kopieën van de gegevens die lokaal zijn opgeslagen. Deze kopieën bieden een hoge duurzaamheid tegen lokale storingen. Als een van de hardwareonderdelen van uw schijf uitvalt, wordt uw virtuele machine niet beïnvloed, omdat er twee extra kopieën zijn om schijfaanvragen te ondersteunen. Het werkt prima, zelfs als twee verschillende hardwareonderdelen van een schijf tegelijkertijd een storing krijgen (wat zelden voorkomt). 

Om ervoor te zorgen dat u altijd drie replica's hebt, start Azure Storage automatisch een nieuwe kopie van de gegevens op de achtergrond als een van de drie kopieën niet meer beschikbaar is. Daarom is het niet nodig om RAID te gebruiken met Azure-schijven voor fouttolerantie. Een eenvoudige RAID 0-configuratie is voldoende om de schijven indien nodig te strippen om grotere volumes te creëren.

Deze architectuur heeft ervoor gezorgd dat Azure consistente duurzaamheid op bedrijfsniveau kan bieden voor IaaS-schijven, met een toonaangevend [jaarlijks storingspercentage](https://en.wikipedia.org/wiki/Annualized_failure_rate) van nul procent.

Lokale hardwarestoringen op de compute-host of het opslagplatform kunnen soms leiden tot de tijdelijke onbeschikbaarheid van de virtuele machine die wordt gedekt door de [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) voor VM-beschikbaarheid. Azure biedt ook een toonaangevende SLA voor afzonderlijke VM-instanties die gebruikmaken van Azure Premium SSD's.

Om toepassingsworkloads te beschermen tegen downtime vanwege de tijdelijke onbeschikbaarheid van een schijf of virtuele machine, kunnen klanten [beschikbaarheidssets gebruiken](./availability.md). Twee of meer virtuele machines in een beschikbaarheidsset bieden redundantie voor de toepassing. Azure maakt deze virtuele machines en schijven vervolgens in afzonderlijke foutdomeinen met verschillende stroom-, netwerk- en serveronderdelen.

Vanwege deze afzonderlijke foutdomeinen hebben lokale hardwarestoringen doorgaans geen invloed op meerdere virtuele machines in de set tegelijkertijd. Afzonderlijke foutdomeinen bieden een hoge beschikbaarheid voor uw toepassing. Het is een goede gewoonte om beschikbaarheidssets te gebruiken wanneer hoge beschikbaarheid is vereist. In de volgende sectie wordt herstel na noodgevallen beschreven.

### <a name="backup-and-disaster-recovery"></a>Back-up en herstel na noodgevallen

Herstel na noodgevallen is het vermogen om zeldzaam voorkomende, maar vaak serieuze incidenten te herstellen. Deze incidenten omvatten niet-tijdelijke, grootschalige storingen, zoals serviceonderbrekingen die van invloed zijn op een hele regio. Herstel na noodgevallen omvat back-up en archivering van gegevens, en kan bestaan uit handmatig ingrijpen, zoals het herstellen van een database uit een back-up.

De ingebouwde beveiliging van het Azure-platform tegen lokale storingen kan de virtuele machines/schijven mogelijk niet volledig beveiligen als een grote ramp grootschalige storingen veroorzaakt. Deze grootschalige storingen omvatten onherstelbare gebeurtenissen, zoals wanneer een datacentrum wordt getroffen door een orkaan, aardbeving of brand, of als er een grootschalige storing optreedt in de hardware-eenheid. Daarnaast kunnen er storingen optreden vanwege problemen met toepassingen of gegevens.

Als u uw IaaS-workloads wilt beveiligen tegen storingen, moet u redundantie plannen en back-ups maken om herstel mogelijk te maken. Voor herstel na noodgevallen moet u een back-up maken op een andere geografische locatie dan die van de primaire site. Deze aanpak zorgt ervoor dat de back-up niet wordt beïnvloed door dezelfde gebeurtenis die de virtuele machine of schijven heeft beïnvloed. Zie [Herstel na noodgevallen voor Azure-toepassingen](/azure/architecture/resiliency/disaster-recovery-azure-applications) voor meer informatie.

Uw overwegingen voor herstel na noodgevallen omvatten mogelijk de volgende aspecten:

- Hoge beschikbaarheid: Het vermogen van de toepassing om in een probleemloze status te kunnen worden uitgevoerd, zonder significante downtime. Met *probleemloze status* wordt bedoeld dat de toepassing responsief is (tijdig reageert) en gebruikers er verbinding mee kunnen maken en interactie mee kunnen aangaan. Bepaalde essentiële toepassingen en databases moeten mogelijk altijd beschikbaar zijn, zelfs als er storingen zijn in het platform. Voor deze workloads moet u mogelijk de redundantie voor de toepassing en de gegevens plannen.

- Duurzaamheid van gegevens: In sommige gevallen is de belangrijkste overweging ervoor te zorgen dat de gegevens behouden blijven als er zich een noodgeval voordoet. Daarom hebt u mogelijk een back-up van uw gegevens op een andere site nodig. Voor dergelijke workloads hebt u mogelijk niet volledige redundantie nodig voor de toepassing, maar alleen een regelmatige back-up van de schijven.

## <a name="backup-and-dr-scenarios"></a>Scenario’s voor back-ups en herstel na noodgevallen

Bekijk enkele typische voorbeelden van de scenario's voor toepassingsworkloads en de overwegingen voor het plannen van herstel na noodgevallen.

### <a name="scenario-1-major-database-solutions"></a>Scenario 1: Oplossingen voor grote databases

Overweeg een productiedatabaseserver, zoals SQL Server of Oracle, die hoge beschikbaarheid kan ondersteunen. Kritieke productietoepassingen en gebruikers zijn afhankelijk van deze database. Het plan voor herstel na noodgevallen voor dit systeem moet mogelijk aan de volgende vereisten voldoen:

- De gegevens moeten worden beveiligd en herstelbaar zijn.
- De server moet beschikbaar zijn voor gebruik.

Het plan voor herstel na noodgevallen vereist mogelijk dat een replica van de database in een andere regio wordt bewaard als back-up. Afhankelijk van de vereisten voor serverbeschikbaarheid en gegevensherstel, kan de oplossing variëren van een active-active of active-passive replicasite tot periodieke offline back-ups van de gegevens. Relationele databases, zoals SQL Server en Oracle, bieden verschillende opties voor replicatie. Gebruik voor SQL Server [SQL Server AlwaysOn-beschikbaarheidsgroepen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) voor maximale beschikbaarheid.

NoSQL-databases, zoals MongoDB, ondersteunen ook [replica's](https://docs.mongodb.com/manual/replication/) voor redundantie. De replica's voor hoge beschikbaarheid worden gebruikt.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scenario 2: Een cluster met redundante virtuele machines

Overweeg een workload die wordt afgehandeld door een cluster met virtuele machines die redundantie en taakverdeling bieden. Een voorbeeld is een Cassandra-cluster dat in een regio is geïmplementeerd. Dit type architectuur biedt al een hoge redundantie binnen die regio. Als u de workload wilt beschermen tegen een storing op regionaal niveau, kunt u overwegen om het cluster over twee regio's te verspreiden of periodieke back-ups naar een andere regio te maken.

### <a name="scenario-3-iaas-application-workload"></a>Scenario 3: Workload van de IaaS-toepassing

Laten we eens kijken naar de workload van de IaaS-toepassing. Deze toepassing kan bijvoorbeeld een typische productieworkload zijn die wordt uitgevoerd op een virtuele machine van Azure. Dit kan een webserver zijn of een bestandsserver met de inhoud en andere bronnen van een site. Het kan ook een aangepaste zakelijke toepassing zijn die wordt uitgevoerd op een virtuele machine die de gegevens, bronnen en toepassingsstatus op de VM-schijven heeft opgeslagen. In dit geval is het belangrijk om ervoor te zorgen dat u regelmatig back-ups maakt. De back-upfrequentie moet gebaseerd zijn op de aard van de VM-workload. Als de toepassing bijvoorbeeld elke dag wordt uitgevoerd en gegevens worden gewijzigd, moet de back-up elk uur worden gemaakt.

Een ander voorbeeld is een rapportserver die gegevens ophaalt uit andere bronnen en geaggregeerde rapporten genereert. Het verlies van deze virtuele machine of schijven kan leiden tot het verlies van de rapporten. Het is echter mogelijk het rapportageproces opnieuw uit te voeren en de uitvoer opnieuw te genereren. In dat geval hebt u geen gegevensverlies, zelfs als de rapportageserver wordt getroffen door een ramp. Als gevolg hiervan is het mogelijk dat u een hoger tolerantieniveau hebt voor het verliezen van een deel van de gegevens op de rapportageserver. In dat geval zijn minder frequente back-ups een optie om de kosten te verlagen.

### <a name="scenario-4-iaas-application-data-issues"></a>Scenario 4: Problemen met gegevens van IaaS-toepassingen

Problemen met gegevens van IaaS-toepassingen zijn een andere mogelijkheid. Overweeg een toepassing die belangrijke commerciële gegevens berekent, bijhoudt en weergeeft, zoals prijsinformatie. Een nieuwe versie van uw toepassing had een softwarefout waardoor de prijzen onjuist werden berekend en de bestaande handelsgegevens van het platform beschadigd raakten. In dit geval kunt u het beste teruggaan naar een eerdere versie van de toepassing en de gegevens. U kunt dit doen door periodieke back-ups van uw systeem te maken.

## <a name="disaster-recovery-solution-azure-backup"></a>Oplossing voor herstel na noodgevallen: Azure Backup 

[Azure Backup](https://azure.microsoft.com/services/backup/) wordt gebruikt voor back-ups en herstel na noodgevallen. Het werkt met [beheerde schijven](managed-disks-overview.md) en onbeheerde schijven. U kunt een back-uptaak maken met op tijd gebaseerde back-ups, eenvoudig VM-herstel en bewaarbeleid voor back-ups.

Als u [Premium SSD's](disks-types.md), [beheerde schijven](managed-disks-overview.md)of andere schijftypen met de optie [lokaal redundante opslag](../storage/common/storage-redundancy.md#locally-redundant-storage) gebruikt, is het vooral belangrijk om periodieke back-ups te maken voor herstel na noodgevallen. Azure Backup slaat de gegevens op in uw Recovery Services-kluis voor langetermijnretentie. Kies de optie [geografisch redundante opslag](../storage/common/storage-redundancy.md#geo-redundant-storage) voor de Recovery Services-kluis voor back-ups. Deze optie zorgt ervoor dat back-ups worden gerepliceerd naar een andere Azure-regio ter bescherming tegen regionale rampen.

Voor onbeheerde IaaS-schijven kunt u het lokaal redundante opslagtype gebruiken. Zorg er wel voor dat Azure Backup is ingeschakeld met de geografisch redundante opslagoptie voor de Recovery Services-kluis.

> [!NOTE]
> Als u de geo [-redundante opslag](../storage/common/storage-redundancy.md#geo-redundant-storage) of [geografisch redundante opslag optie met lees toegang](../storage/common/storage-redundancy.md#read-access-to-data-in-the-secondary-region)  voor uw onbeheerde schijven gebruikt, hebt u nog steeds consistente moment opnamen nodig voor back-up en Dr. Gebruik [Azure Backup](https://azure.microsoft.com/services/backup/) of [consistente momentopnames](#alternative-solution-consistent-snapshots).

 De volgende tabel bevat een overzicht van de oplossingen die beschikbaar zijn voor herstel na noodgevallen.

| Scenario | Automatische replicatie | Oplossing voor herstel na noodgevallen |
| --- | --- | --- |
| Premium SSD-schijven | Lokaal ([lokaal redundante opslag](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Managed Disks | Lokaal ([lokaal redundante opslag](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Onbeheerde lokaal redundante opslagschijven | Lokaal ([lokaal redundante opslag](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Onbeheerde geografisch redundante opslagschijven | Meerdere regio’s ([geografisch redundante opslag](../storage/common/storage-redundancy.md#geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Consistente momentopnames](#alternative-solution-consistent-snapshots) |
| Onbeheerde geografisch redundante opslagschijven met leestoegang | Meerdere regio’s ([geografisch redundante opslag met leestoegang](../storage/common/storage-redundancy.md#read-access-to-data-in-the-secondary-region)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Consistente momentopnames](#alternative-solution-consistent-snapshots) |

Hoge beschikbaarheid wordt het makkelijkst bereikt door beheerde schijven in een beschikbaarheidsset te gebruiken samen met Azure Backup. Als u onbeheerde schijven gebruikt, kunt u nog steeds Azure Backup gebruiken voor herstel na noodgevallen. Als u Azure Backup niet kunt gebruiken, dan zijn [consistente momentopnames](#alternative-solution-consistent-snapshots), zoals verderop beschreven, een alternatieve manier voor het maken van back-ups en herstel na noodgevallen.

Uw keuzes voor hoge beschikbaarheid, back-up en herstel na noodgevallen op voor toepassingen of infrastructuur kunnen als volgt worden weergegeven:

| Niveau |   Hoge beschikbaarheid   | Back-up of herstel na noodgevallen |
| --- | --- | --- |
| Toepassing | SQL Server AlwaysOn | Azure Backup |
| Infrastructuur    | Beschikbaarheidsset  | Geografisch redundante opslag met consistente momentopnames |

### <a name="using-azure-backup"></a>Azure Backup gebruiken 

Met [Azure Backup](../backup/backup-azure-vms-introduction.md) kunt u een back-up maken van uw virtuele machines met Windows of Linux naar de Azure Recovery Services-kluis. Het maken van een back-up en het herstellen van bedrijfskritische gegevens is gecompliceerd door het feit dat er een back-up van bedrijfskritieke gegevens moet worden gemaakt terwijl de toepassingen die de gegevens produceren, worden uitgevoerd. 

Om dit probleem op te lossen, biedt Azure Backup toepassingsconsistente back-ups voor Microsoft-workloads. Het gebruikt de volume-schaduwservice om ervoor te zorgen dat de gegevens correct naar de opslag worden geschreven. Voor Linux-VM’s is de standaardmodus voor back-upconsistentie het gebruik van bestandsconsistente back-ups, omdat Linux niet dezelfde functionaliteit heeft als de volume-schaduwservice in Windows. Zie [Toepassingsconsistente back-up van Azure Linux-VM’s](../backup/backup-azure-linux-app-consistent.md) voor Linux-computers.

![Stroom van Azure Backup][1]

Wanneer er op het geplande tijdstip een back-uptaak wordt gestart met Azure Backup, wordt de back-upextensie die in de virtuele machine is geïnstalleerd, geactiveerd om een momentopname van een bepaald tijdstip te maken. Er wordt een momentopname gemaakt in combinatie met de volume-schaduwservice om een consistente momentopname van de schijven op de virtuele machine te kunnen maken zonder deze af te sluiten. De back-upextensie in de virtuele machine verwijdert alle schrijfbewerkingen voordat een consistente momentopname van alle schijven wordt gemaakt. Na het maken van de momentopname worden de gegevens door Azure Backup overgedragen naar de back-upkluis. Om het back-upproces efficiënter te maken, identificeert en draagt de service alleen de gegevensblokken over die sinds de vorige back-up zijn gewijzigd.

Als u deze wilt herstellen, kunt u de beschikbare back-ups bekijken via Azure Backup en vervolgens een herstel starten. U kunt Azure-back-ups maken en herstellen via de [Azure-portal](https://portal.azure.com/) door [PowerShell](../backup/backup-azure-vms-automation.md) of [Azure CLI](/cli/azure/) te gebruiken.

### <a name="steps-to-enable-a-backup"></a>Stappen voor het inschakelen van een back-up

Gebruik de volgende stappen om back-ups van uw virtuele machines in te schakelen met behulp van de [Azure-portal](https://portal.azure.com/). Er zijn enkele verschillen, afhankelijk van uw exacte scenario. Raadpleeg de documentatie over [Azure Backup](../backup/backup-azure-vms-introduction.md) voor volledige informatie. Azure Backup [ondersteunt ook virtuele machines met beheerde schijven](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Een Recovery Services-kluis voor een VM maken:

    a. Ga in de [Azure Portal](https://portal.azure.com/) naar **Alle resources** en zoek **Recovery Services-kluizen**.

    b. Klik in het menu **Recovery Services-kluizen** op **Toevoegen** en volg de stappen voor het maken van een nieuwe kluis in dezelfde regio als de virtuele machine. Als uw virtuele machine zich bijvoorbeeld in de regio US - west bevindt, kiest u US- west voor de kluis.

1.  Controleer de opslagreplicatie voor de zojuist gemaakte kluis. Open de kluis onder **Recovery Services-kluizen** en ga naar **Eigenschappen** > **Back-upconfiguratie** > **Bijwerken**. Zorg ervoor dat de optie **geografisch redundante opslag** standaard is geselecteerd. Deze optie zorgt ervoor dat uw kluis automatisch wordt gerepliceerd naar een secundair datacentrum. Uw kluis in US - west wordt bijvoorbeeld automatisch gerepliceerd naar US - oost.

1.  Configureer het back-upbeleid en selecteer de virtuele machine in dezelfde gebruikersinterface.

1.  Zorg ervoor dat de back-upagent is geïnstalleerd op de virtuele machine. Als uw virtuele machine is gemaakt met behulp van een Azure Gallery-installatiekopie, is de back-upagent al geïnstalleerd. Anders (als u een aangepaste installatiekopie gebruikt), gebruikt u de instructies om de VM-agent op een virtuele machine te [installeren](../backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  Nadat de vorige stappen zijn voltooid, wordt de back-up met regelmatige intervallen uitgevoerd zoals opgegeven in het back-upbeleid. Indien nodig kunt u de eerste back-up handmatig activeren vanuit het kluisdashboard op de Azure-portal.

Raadpleeg [PowerShell-cmdlets voor back-up van VM’s](../backup/backup-azure-vms-automation.md) voor het automatiseren van Azure Backup met behulp van scripts.

### <a name="steps-for-recovery"></a>Stappen voor herstel

Als u een virtuele machine moet herstellen of herbouwen, kunt u de virtuele machine herstellen vanaf een van de back-upherstelpunten in de kluis. Er zijn verschillende opties voor het uitvoeren van het herstel:

-   U kunt een nieuwe virtuele machine maken als een point-in-time weergave van de back-up van uw virtuele machine.

-   U kunt de schijven herstellen en vervolgens de sjabloon voor de virtuele machine gebruiken om de herstelde virtuele machine aan te passen en te herbouwen.

Zie voor meer informatie de instructies voor het [gebruik van de Azure-portal voor het herstellen van virtuele machines](../backup/backup-azure-arm-restore-vms.md). In dit document worden ook de specifieke stappen beschreven voor het herstellen van back-ups van virtuele machines naar een gekoppeld datacentrum met behulp van uw geografisch redundante back-upkluis als er sprake is van een noodgeval in het primaire datacentrum. In dat geval gebruikt Azure Backup de compute-service van de secundaire regio om de herstelde virtuele machine te maken.

U kunt ook PowerShell gebruiken voor [het maken van een nieuwe virtuele machine op basis van herstelde schijven](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternatieve oplossing: Consistente momentopnames

Als Azure Backup niet kan worden gebruikt, kunt u uw eigen back-upmechanisme implementeren met behulp van momentopnames. Het maken van consistente momentopnames voor alle schijven die worden gebruikt door een virtuele machine en het repliceren van die momentopnames naar een andere regio is gecompliceerd. Daarom beschouwt Azure het gebruik van de back-upservice als een betere optie dan het bouwen van een aangepaste oplossing.

Als u geografisch redundante opslag/geografisch redundante opslag met leestoegang gebruikt voor schijven, worden momentopnames automatisch gerepliceerd naar een secundair datacentrum. Als u lokaal redundante opslag voor schijven gebruikt, moet u de gegevens zelf repliceren. Zie [Back-ups maken van onbeheerde Azure-VM-schijven met incrementele momentopnames](windows/incremental-snapshots.md) voor meer informatie.

Een momentopname is een representatie van een object op een bepaald moment. Een momentopname wordt gefactureerd voor de incrementele grootte van de gegevens die het bevat. Zie [Een blob-momentopname maken](../storage/blobs/snapshots-overview.md) voor meer informatie.

### <a name="create-snapshots-while-the-vm-is-running"></a>Momentopnames maken terwijl de virtuele machine wordt uitgevoerd

Hoewel u op elk gewenst moment een momentopname kunt maken, worden er nog steeds gegevens naar de schijven gestreamd als de virtuele machine wordt uitgevoerd. De momentopnames kunnen gedeeltelijke bewerkingen bevatten die nog werden uitgevoerd. Als er meerdere schijven zijn, kunnen de momentopnames van verschillende schijven op verschillende tijdstippen hebben plaatsgevonden. Deze scenario's kunnen ertoe leiden dat de momentopnames ongecoördineerd zijn. Dit gebrek aan coördinatie is vooral problematisch voor gestripte volumes waarvan de bestanden beschadigd kunnen raken als er wijzigingen zijn aangebracht tijdens het maken van de back-up.

Om deze situatie te voorkomen, moeten de volgende stappen worden geïmplementeerd in het back-upproces:

1.  Alle schijven blokkeren.

1.  Alle in behandeling zijnde schrijfbewerkingen leegmaken.

1.  [Een blobl-momentopname maken](../storage/blobs/snapshots-manage-dotnet.md) voor alle schijven.

Sommige Windows-toepassingen, zoals SQL Server, bieden een gecoördineerd back-upmechanisme via een volume-schaduwservice om toepassingsconsistente back-ups te maken. In Linux kunt u een hulpprogramma zoals *fsfreeze* gebruiken voor het coördineren van de schijven. Dit hulpprogramma biedt bestandsconsistente back-ups, maar geen toepassingsconsistente momentopnames. Dit proces is complex, dus u kunt overwegen [Azure Backup](../backup/backup-azure-vms-introduction.md) of een back-upoplossing van derden te gebruiken die deze procedure al implementeert.

Het vorige proces resulteert in een verzameling gecoördineerde momentopnames voor alle VM-schijven, die een specifieke point-in-time weergave van de virtuele machine vertegenwoordigen. Dit is een herstelpunt voor back-ups van de virtuele machine. U kunt het proces op geplande intervallen herhalen om periodieke back-ups te maken. Zie [De back-ups naar een andere regio kopiëren](#copy-the-snapshots-to-another-region) voor stappen om de momentopnames naar een andere regio te kopiëren voor herstel na noodgevallen.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Momentopnames maken terwijl de virtuele machine offline is

Een andere optie voor het maken van consistente back-ups is het afsluiten van de virtuele machine en blob-momentopnames van elke schijf te maken. Het maken van blob-momentopnames is gemakkelijker dan het coördineren van momentopnames van een actieve virtuele machine, maar het vereist enkele minuten downtime.

1. Sluit de virtuele machine af.

1. Maak een momentopname van elke virtuele harde schijf-blob. Dit neemt slechts een paar seconden in beslag.

    Als u een momentopname wilt maken, kunt u gebruikmaken van [Power shell](/powershell/module/az.storage), de [Azure Storage REST API](/rest/api/storageservices/Snapshot-Blob), [Azure CLI](/cli/azure/), of een van de Azure Storage-clientbibliotheken, zoals [de Storage-clientbibliotheek voor .NET](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

1. Start de virtuele machine om de downtime te beëindigen. Normaal gesproken is het hele proces binnen een paar minuten voltooid.

Dit proces levert een verzameling van consistente momentopnames voor alle schijven, zodat er een back-upherstelpunt voor de virtuele machine wordt geboden.

### <a name="copy-the-snapshots-to-another-region"></a>De momentopnames kopiëren naar een andere regio

Het is mogelijk dat alleen het maken van momentopnames niet genoeg is voor herstel na noodgevallen. U moet de momentopnames ook naar een andere regio repliceren.

Als u geografisch redundante opslag of geografisch redundante opslag met leestoegang voor uw schijven gebruikt, worden de momentopnames automatisch naar de secundaire regio gerepliceerd. Het kan een paar minuten duren voordat de replicatie is uitgevoerd. Als het primaire datacentrum uitvalt voordat de momentopnames zijn gerepliceerd, hebt u geen toegang tot de momentopnames van het secundaire datacentrum. De kans hierop is klein.

> [!NOTE]
> Alleen het hebben van de schijven in een geografisch redundante opslag of een geografisch redundant opslagaccount met leestoegang beschermt de virtuele machine niet tegen rampen. U moet ook gecoördineerde momentopnames maken of Azure Backup gebruiken. Dit is vereist om een virtuele machine naar een consistente staat te herstellen.

Als u lokaal redundante opslag gebruikt, moet u de momentopnames direct na het maken van de momentopnames naar een ander opslagaccount kopiëren. Het kopieerdoel kan een lokaal redundant opslagaccount in een andere regio zijn, waardoor de kopie zich in een externe regio zal bevinden. U kunt de momentopname ook kopiëren naar een geografisch redundant opslagaccount met leestoegang in dezelfde regio. In dit geval wordt de momentopname vertraagd gerepliceerd naar de externe secundaire regio. Uw back-up wordt beschermd tegen rampen op de primaire site nadat het kopiëren en repliceren is voltooid.

Als u uw incrementele momentopnames op efficiënte wijze wilt kopiëren voor herstel na noodgevallen, bekijkt u de instructies in [Back-ups maken van onbeheerde Azure-VM-schijven met incrementele momentopnames](windows/incremental-snapshots.md).

![Back-ups maken van onbeheerde VM-schijven met incrementele momentopnames][2]

### <a name="recovery-from-snapshots"></a>Herstel van momentopnames

Als u een momentopname wilt ophalen, moet u deze kopiëren om een nieuwe blob te maken. Als u de momentopname van het primaire account kopieert, kunt u de momentopname kopiëren naar de basis-blob van de momentopname. Met dit proces wordt de schijf teruggezet naar de momentopname. Dit proces staat bekend als het promoten van de momentopname. Als u de back-up van de momentopname van een secundair account kopieert, moet u deze naar een primair account kopiëren in het geval van een geografisch redundant opslagaccount met leestoegang. U kunt een momentopname kopiëren door [PowerShell](/powershell/module/az.storage) te gebruiken of door het AzCopy-hulpprogramma te gebruiken. Zie [Gegevensoverdracht met het AzCopy-opdrachtregelprogramma](../storage/common/storage-use-azcopy-v10.md) voor meer informatie.

Voor virtuele machines met meerdere schijven moet u alle momentopnames kopiëren die deel uitmaken van hetzelfde gecoördineerde herstelpunt. Nadat u de momentopnames naar schrijfbare VHD-blobs hebt gekopieerd, kunt u de blobs gebruiken om de virtuele machine opnieuw te maken met behulp van de sjabloon voor de virtuele machine.

## <a name="other-options"></a>Andere opties

### <a name="sql-server"></a>SQL Server

Een SQL Server die in een virtuele machine worden uitgevoerd, heeft zijn eigen ingebouwde mogelijkheden om een back-up van uw SQL Server-database te maken naar Azure Blob-opslag of een bestandsshare. Als het opslagaccount een geografisch redundante opslag of geografisch redundante opslag met leestoegang is, hebt u toegang tot de back-ups in het secundaire datacentrum van het opslagaccount in het geval van een ramp, met dezelfde beperkingen als eerder besproken. Zie voor meer informatie [Back-up en herstel voor SQL Server in Azure Virtual Machines](../azure-sql/virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md). [SQL Server AlwaysOn-beschikbaarheidsgroepen](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md) kunnen niet alleen back-ups maken en herstellen, maar ook secundaire replica's van databases onderhouden. Deze mogelijkheid verkort het herstel na noodgevallen aanzienlijk.

## <a name="other-considerations"></a>Andere overwegingen

In dit artikel wordt beschreven hoe u back-ups maakt of momentopnames van uw virtuele machines en hun schijven kunt maken ter ondersteuning van herstel na noodgevallen en hoe u deze back-ups of momentopnames kunt gebruiken om uw gegevens te herstellen. Met het Azure Resource Manager-model gebruiken veel mensen sjablonen om hun virtuele machines en andere infrastructuren in Azure te maken. U kunt een sjabloon gebruiken om een virtuele machine te maken die elke keer dezelfde configuratie heeft. Als u aangepaste installatiekopieën gebruikt voor het maken van uw virtuele machines, moet u er ook voor zorgen dat uw installatiekopieën worden beschermd door een geografisch redundant opslagaccount met leestoegang te gebruiken om ze op te slaan.

Dit betekent dat uw back-upproces een combinatie van twee dingen kan zijn:

- Een back-up maken van de gegevens (schijven).
- Een back-up maken van de configuratie (sjablonen en aangepaste installatiekopieën).

Afhankelijk van de back-upoptie die u kiest, moet u mogelijk de back-up van zowel de gegevens als de configuratie afhandelen, of kan de back-upservice dit allemaal voor u afhandelen.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Bijlage: Meer informatie over de impact van gegevensredundantie

Voor opslagaccounts in Azure zijn er drie soorten gegevensredundantie voor herstel na noodgevallen: lokaal redundant, geografisch redundant of geografisch redundant met leestoegang. 

Lokaal redundante opslag bewaren drie kopieën van de gegevens in hetzelfde datacentrum. Wanneer de virtuele machine de gegevens schrijft, worden alle drie de kopieën bijgewerkt voordat deze worden geretourneerd aan de oproepende functie, zodat u weet dat ze identiek zijn. Uw schijf wordt beschermd tegen lokale storingen, omdat het onwaarschijnlijk is dat alle drie de kopieën op hetzelfde moment worden beïnvloed. In het geval van lokaal redundante opslag is er geen geografische redundantie, waardoor de schijf niet wordt beschermd tegen onherstelbare storingen die van invloed kunnen zijn op een volledig datacentrum of opslageenheid.

Bij geografisch redundante opslag en geografisch redundante opslag met leestoegang worden drie kopieën van uw gegevens bewaard in de primaire regio die door u is geselecteerd. Daarnaast worden er drie extra kopieën van uw gegevens bewaard in een bijbehorende secundaire regio die is ingesteld door Azure. Als u bijvoorbeeld gegevens in US - west opslaat, worden de gegevens gerepliceerd naar US - oost. Het bewaren van kopieën wordt asynchroon uitgevoerd en er is een kleine vertraging tussen updates van de primaire en secundaire sites. Replica's van de schijven op de secundaire site zijn consistent per schijf (met vertraging), maar replica's van meerdere actieve schijven zijn mogelijk niet synchroon met elkaar. Voor consistente replica's op meerdere schijven zijn consistente momentopnames vereist.

Het belangrijkste verschil tussen geografisch redundante opslag en geografisch redundante opslag met leestoegang is dat met geografisch redundante opslag met leestoegang u de secundaire kopie op elk gewenst moment kunt lezen. Als er een probleem is waardoor de gegevens in de primaire regio niet toegankelijk zijn, doet het Azure-team er alles aan om de toegang te herstellen. Als u geografisch redundante opslag met leestoegang hebt ingeschakeld, kunt u de primaire gegevens bekijken in het secundaire datacentrum. Als u dus van plan bent om de replica te lezen terwijl de primaire niet toegankelijk is, moet u geografisch redundante opslag met leestoegang overwegen.

Als het een aanzienlijke storing blijkt te zijn, kan het Azure-team een geo-failover activeren en de primaire DNS-vermeldingen wijzigen om naar de secundaire opslag te verwijzen. Als u op dit moment geografisch redundante opslag of geografisch redundante opslag met leestoegang hebt ingeschakeld, kunt u toegang tot de gegevens krijgen in de regio die vroeger de secundaire was. Met andere woorden, als uw opslagaccount geografisch redundante opslag is en er een probleem is, hebt u alleen toegang tot de secundaire opslag als er een geo-failover is.

Zie voor meer informatie [Wat te doen in het geval van een Azure Storage-storing](../storage/common/storage-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Volgende stappen

Zie [back-ups maken van niet-beheerde virtuele machines van Azure met incrementele moment opnamen](linux/incremental-snapshots.md).

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png