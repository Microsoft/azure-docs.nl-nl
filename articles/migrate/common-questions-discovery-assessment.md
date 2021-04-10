---
title: Vragen over detectie, evaluatie en afhankelijkheids analyse in Azure Migrate
description: Krijg antwoorden op veelgestelde vragen over detectie, evaluatie en afhankelijkheids analyse in Azure Migrate.
author: rashijoshi
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 629459d22b18b326307b45bb512d16622808b533
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562627"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Detectie, beoordeling en afhankelijkheids analyse-Veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over detectie, evaluatie en afhankelijkheids analyse in Azure Migrate. Als u andere vragen hebt, raadpleegt u deze bronnen:

- [Algemene vragen](resources-faq.md) over Azure migrate
- Vragen over het [Azure migrate apparaat](common-questions-appliance.md)
- Vragen over [server migratie](common-questions-server-migration.md)
- Beantwoorde vragen in het [Azure migrate-forum](https://aka.ms/AzureMigrateForum) ontvangen


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Welke geografische gebieden worden ondersteund voor detectie en evaluatie met Azure Migrate?

Bekijk de ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).


## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Hoeveel servers kan ik vinden met een apparaat?

U kunt tot 10.000 servers in de VMware-omgeving, tot 5.000 servers van de Hyper-V-omgeving en Maxi maal 1000 fysieke servers detecteren door één apparaat te gebruiken. Als u meer servers hebt, leest u over [het schalen van een Hyper-V-evaluatie](scale-hyper-v-assessment.md), [het schalen van een VMware-evaluatie](scale-vmware-assessment.md)of [het schalen van een fysieke server beoordeling](scale-physical-assessment.md).

## <a name="how-do-i-choose-the-assessment-type"></a>Hoe kies ik het evaluatietype?

- Gebruik **Azure VM-evaluaties** als u servers wilt beoordelen van uw on-premises [VMware](how-to-set-up-appliance-vmware.md) -en [Hyper-V-](how-to-set-up-appliance-hyper-v.md) omgeving en [fysieke servers](how-to-set-up-appliance-physical.md) voor migratie naar virtuele Azure-machines. [Meer informatie](concepts-assessment-calculation.md)

- Gebruik het beoordelings type **Azure SQL** wanneer u uw on-premises SQL Server wilt beoordelen vanuit uw VMware-omgeving voor migratie naar Azure SQL database of Azure SQL Managed instance. [Meer informatie](concepts-assessment-calculation.md)

- Gebruik de evaluaties van de **Azure VMware-oplossing (AVS)** als u uw on-premises [virtuele VMware-machines](how-to-set-up-appliance-vmware.md) wilt controleren op migratie naar [Azure VMware-oplossing (AVS)](../azure-vmware/introduction.md) met dit beoordelings type. [Meer informatie](concepts-azure-vmware-solution-assessment-calculation.md)

- U kunt een algemene groep met VMware-machines alleen gebruiken om beide typen evaluaties uit te voeren. Let op: als u AVS-evaluaties in Azure Migrate voor het eerst uitvoert, is het raadzaam om een nieuwe groep VMware-machines te maken.
 

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Waarom ontbreken prestatie gegevens voor sommige/alle servers in mijn Azure VM-en/of AVS-evaluatie rapport?

Voor de evaluatie op basis van prestaties geeft de export rapport uitvoer ' PercentageOfCoresUtilizedMissing ' of ' PercentageOfMemoryUtilizedMissing ' als het Azure Migrate apparaat geen prestatie gegevens kan verzamelen voor de on-premises servers. Controleer het volgende:

- Als de servers zijn ingeschakeld voor de duur waarvoor u de evaluatie maakt
- Als er alleen geheugen items ontbreken en u probeert servers in de Hyper-V-omgeving te beoordelen. In dit scenario schakelt u dynamisch geheugen op de servers in en herberekenen de evaluatie om de meest recente wijzigingen weer te geven. Het apparaat kan alleen geheugen gebruiks waarden voor servers in de Hyper-V-omgeving verzamelen wanneer dynamisch geheugen is ingeschakeld op de server.

- Als alle prestatie meter items ontbreken, moet u ervoor zorgen dat uitgaande verbindingen op poort 443 (HTTPS) zijn toegestaan.

    > [!Note]
    > Als een van de prestatie meter items ontbreekt, Azure Migrate: Server analyse terugvallen op de toegewezen kernen/geheugen on-premises en raadt de VM-grootte dienovereenkomstig aan.


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Waarom ontbreken prestatie gegevens voor sommige/alle SQL-exemplaren/data bases in mijn Azure SQL-evaluatie?

Controleer het volgende om zeker te weten dat de prestatiegegevens zijn verzameld:

- Als de SQL-servers zijn ingeschakeld voor de duur waarvoor u de evaluatie maakt
- Als de verbindings status van de SQL-Agent in Azure Migrate verbonden is en de laatste heartbeat controleren 
- Als Azure Migrate verbindings status voor alle SQL-exemplaren ' connected ' is op de Blade gedetecteerde SQL-instantie
- Als alle prestatiemeteritems ontbreken, moet u ervoor zorgen dat uitgaande verbindingen op poort 443 (HTTPS) zijn toegestaan

Als een van de prestatie meter items ontbreekt, raadt Azure SQL assessment de kleinste Azure SQL-configuratie voor die instantie/data base aan.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Waarom is de betrouwbaarheidsclassificatie van mijn evaluatie laag?

De betrouwbaarheidsclassificatie wordt berekend voor evaluaties Op basis van prestaties, op basis van het percentage [beschikbare gegevenspunten](./concepts-assessment-calculation.md#ratings) dat nodig is om de evaluatie te berekenen. Hieronder ziet u de redenen waarom een evaluatie een lage betrouwbaarheidsclassificatie kan krijgen:

- U hebt uw omgeving niet geprofileerd gedurende de periode waarvoor u de evaluatie maakt. Als u bijvoorbeeld een evaluatie maakt waarbij de duur van de prestaties is ingesteld op één week, moet u na het starten van de detectie minstens een week wachten voordat alle gegevenspunten zijn verzameld. Als u niet kunt wachten op de duur, wijzigt u de duur van de prestaties in een kleinere periode en berekent u de evaluatie **opnieuw** .
 
- Bij de evaluatie kunnen de prestatiegegevens voor sommige of alle servers in de evaluatieperiode niet worden verzameld. Voor een hoge betrouwbaarheids classificatie moet u het volgende doen: 
    - Servers zijn ingeschakeld voor de duur van de evaluatie
    - Uitgaande verbindingen op poort 443 zijn toegestaan
    - Voor Hyper-V-servers is dynamisch geheugen ingeschakeld 
    - De verbindings status van agents in Azure Migrate is verbonden en controleer de laatste heartbeat
    - Voor Azure SQL-evaluaties is Azure Migrate verbindings status voor alle SQL-exemplaren verbonden op de Blade gedetecteerde SQL-instantie

    **Bereken de evaluatie opnieuw** om de meest recente wijzigingen in de betrouwbaarheidsclassificatie mee te nemen.

- Voor Azure VM-en AVS-evaluaties zijn er weinig servers gemaakt nadat de detectie is gestart. Als u bijvoorbeeld een evaluatie maakt voor de prestatie geschiedenis van de laatste maand, maar weinig servers in de omgeving is slechts een week geleden gemaakt. In dit geval zijn de prestatie gegevens voor de nieuwe servers niet beschikbaar voor de hele duur en is de betrouwbaarheids classificatie laag. [Meer informatie](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- Voor Azure SQL-evaluaties zijn enkele SQL-exemplaren of -databases gemaakt nadat de detectie is gestart. Als u bijvoorbeeld een evaluatie maakt voor de prestatie geschiedenis van de laatste maand, maar weinig SQL-exemplaren of data bases zijn in de omgeving slechts een week geleden gemaakt. In dit geval zijn de prestatie gegevens voor de nieuwe servers niet beschikbaar voor de hele duur en is de betrouwbaarheids classificatie laag. [Meer informatie](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="i-want-to-try-out-the-new-azure-sql-assessment"></a>Ik wil de nieuwe Azure SQL-evaluatie uitproberen
Detectie en evaluatie van SQL Server instanties en data bases die worden uitgevoerd in uw VMware-omgeving is nu beschikbaar als preview-versie. Ga aan de slag met [deze zelfstudie](tutorial-discover-vmware.md). Als u deze functie in een bestaand project wilt uitproberen, moet u ervoor zorgen dat u de [vereisten](how-to-discover-sql-existing-project.md) in dit artikel hebt voltooid.

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-sql-assessment"></a>Ik zie sommige servers niet bij het maken van een Azure SQL-evaluatie

- Azure SQL-evaluatie kan alleen worden uitgevoerd op servers waarop SQL-exemplaren zijn gedetecteerd. Als u de servers en SQL-exemplaren die u wilt evalueren, niet ziet, wacht u even tot de detectie is voltooid en maakt u vervolgens de evaluatie. 
- Als u een eerder gemaakte groep niet kunt zien tijdens het maken van de evaluatie, moet u een niet-VMware-Server of een andere server zonder SQL-exemplaar uit de groep verwijderen.
- Als u Azure SQL-evaluaties in Azure Migrate de eerste keer uitvoert, is het raadzaam om een nieuwe groep servers te maken.

## <a name="i-want-to-understand-how-was-the-readiness-for-my-instance-computed"></a>Ik wil weten hoe de gereedheid voor mijn exemplaar is berekend?
De gereedheid van uw SQL-exemplaren wordt berekend na een functiecompatibiliteitscontrole, met het beoogde Azure SQL-implementatietype (Azure SQL Database of Azure SQL Managed Instance). [Meer informatie](./concepts-azure-sql-assessment-calculation.md#calculate-readiness)

## <a name="why-is-the-readiness-for-all-my-sql-instances-marked-as-unknown"></a>Waarom is de gereedheid voor alle mijn SQL-instanties gemarkeerd als onbekend?
Als uw detectie recentelijk is gestart en nog steeds wordt uitgevoerd, ziet u mogelijk de gereedheid voor sommige of alle SQL-exemplaren als onbekend. We raden u aan even te wachten totdat de omgeving is geprofileerd op het apparaat, en de evaluatie vervolgens opnieuw te berekenen.
De SQL-detectie wordt elke 24 uur uitgevoerd en u moet mogelijk tot een dag wachten voordat de meest recente configuratie wijzigingen overeenkomen. 

## <a name="why-is-the-readiness-for-some-of-my-sql-instances-marked-as-unknown"></a>Waarom is de gereedheid voor sommige van mijn SQL-instanties gemarkeerd als onbekend?
Dit kan gebeuren als: 
- De detectie wordt nog uitgevoerd. We raden u aan even te wachten totdat de omgeving is geprofileerd op het apparaat, en de evaluatie vervolgens opnieuw te berekenen.
- Er is een aantal detectieproblemen dat u moet oplossen op de blade Fouten en meldingen.

De SQL-detectie wordt elke 24 uur uitgevoerd en u moet mogelijk tot een dag wachten voordat de meest recente configuratie wijzigingen overeenkomen.

## <a name="my-assessment-is-in-outdated-state"></a>Mijn evaluatie heeft de status Verouderd

### <a name="azure-vmavs-assessment"></a>Azure VM/AVS-evaluatie
Als er on-premises wijzigingen zijn aangebracht aan servers die zich in een groep bevinden die is beoordeeld, wordt de evaluatie gemarkeerd als verouderd. Een evaluatie kan worden gemarkeerd als verouderd vanwege een of meer wijzigingen in de volgende eigenschappen:
- Aantal processor kernen
- Toegewezen geheugen
- Opstart type of-firmware
- Naam van besturings systeem, versie en architectuur
- Aantal schijven
- Aantal netwerk adapters
- Wijziging in de schijf grootte (GB toegewezen)
- Update van NIC-eigenschappen. Voor beeld: Mac-adres wijzigingen, IP-adres toevoegen etc.

Voer de evaluatie **opnieuw** uit om de laatste wijzigingen in de evaluatie weer te geven.

### <a name="azure-sql-assessment"></a>Azure SQL-evaluatie
Als er wijzigingen zijn aangebracht in on-premises SQL-exemplaren en -databases in een groep die is geëvalueerd, wordt de evaluatie gemarkeerd als **Verouderd**:
- SQL-exemplaar is toegevoegd aan of verwijderd van een server
- SQL database is toegevoegd aan of verwijderd uit een SQL-exemplaar
- De totale databasegrootte in een SQL-exemplaar is meer dan 20% gewijzigd
- Wijziging in aantal processor kernen en/of toegewezen geheugen

Voer de evaluatie **opnieuw** uit om de laatste wijzigingen in de evaluatie weer te geven.

## <a name="why-was-i-recommended-a-particular-target-deployment-type"></a>Waarom is mij een bepaald type doelimplementatie aanbevolen?
Azure Migrate raadt een specifiek Azure SQL-implementatietype aan dat compatibel is met uw SQL-exemplaar. Door te migreren naar een door Microsoft aanbevolen doel vermindert u de algehele migratie-inspanning. Deze Azure SQL-configuratie (SKU) wordt aanbevolen na overweging van de prestatiekenmerken van uw SQL-exemplaar en de databases die hierop worden beheerd. Als meerdere Azure SQL-configuraties in aanmerking komen, raden we u aan om de configuratie te kiezen die het meest rendabel is. [Meer informatie](./concepts-azure-sql-assessment-calculation.md#calculate-sizing)

## <a name="what-deployment-target-should-i-choose-if-my-sql-instance-is-ready-for-azure-sql-db-and-azure-sql-mi"></a>Welk implementatiedoel moet ik kiezen als mijn SQL-exemplaar gereed is voor Azure SQL DB en Azure SQL MI? 
Als uw exemplaar gereed is voor zowel Azure SQL DB als Azure SQL MI, raden we het type doelimplementatie aan waarvoor de geschatte kosten voor de Azure SQL-configuratie het laagst zijn.

## <a name="why-is-my-instance-marked-as-potentially-ready-for-azure-vm-in-my-azure-sql-assessment"></a>Waarom is mijn instantie gemarkeerd als mogelijk klaar voor Azure VM in mijn Azure SQL-evaluatie?
Dit kan gebeuren wanneer het gekozen type doelimplementatie in de evaluatie-eigenschappen **Aanbevolen** is, en het SQL-exemplaar niet gereed is voor Azure SQL Database en Azure SQL Managed Instance. De gebruiker wordt aanbevolen om een evaluatie in Azure Migrate te maken met het evaluatietype **Azure VM**, om te bepalen of de server waarop het exemplaar wordt uitgevoerd, gereed is voor migratie naar een Azure VM.
De gebruiker wordt aanbevolen een evaluatie te maken in Azure Migrate met een evaluatie type als **Azure VM** om te bepalen of de server waarop het exemplaar wordt uitgevoerd, gereed is om te migreren naar een virtuele machine van Azure:
- Azure VM-evaluaties in Azure Migrate worden momenteel weer gegeven als een Shift-toets en beschouwt niet de specifieke prestatie gegevens voor het uitvoeren van SQL-instanties en data bases op de virtuele machine van Azure. 
- Wanneer u een Azure VM-evaluatie uitvoert op een server, gelden de aanbevolen grootte en kostenramingen voor alle exemplaren die op de server worden uitgevoerd, en kunnen worden gemigreerd naar een Azure VM via het hulpprogramma voor servermigratie. Voordat u migreert, [raadpleegt u de richtlijnen voor prestaties](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) voor SQL Server op virtuele Azure-machines.

## <a name="i-cant-see-some-databases-in-my-assessment-even-though-the-instance-is-part-of-the-assessment"></a>Ik zie sommige databases niet in mijn evaluatie, ongeacht het feit dat het exemplaar wel onderdeel uitmaakt van de evaluatie

De Azure SQL-evaluatie bevat alleen databases die een onlinestatus hebben. Als de database zich in een andere status bevindt, worden de gereedheid, grootte en kostenberekening voor dergelijke databases genegeerd in de evaluatie. Als u dergelijke databases wilt evalueren, wijzigt u de status van de database, en berekent u de evaluatie op een later tijdstip opnieuw.

## <a name="i-want-to-compare-costs-for-running-my-sql-instances-on-azure-vm-vs-azure-sql-databaseazure-sql-managed-instance"></a>Ik wil de kosten voor het uitvoeren van mijn SQL-exemplaren op Azure VM versus Azure SQL Database/Azure SQL Managed instance vergelijken

U kunt een evaluatie met het type **Azure VM** maken in dezelfde groep die is gebruikt voor uw **Azure SQL**-evaluatie. Vervolgens kunt u de twee rapporten naast elkaar leggen en ze vergelijken. Let op: Azure VM-evaluaties in Azure Migrate zijn momenteel gefocust op lift-and-shift, en de specifieke metrische prestatiegegevens voor de uitvoering van SQL-exemplaren en -databases worden niet in overweging genomen op de virtuele Azure-machine. Wanneer u een Azure VM-evaluatie uitvoert op een server, gelden de aanbevolen grootte en kostenramingen voor alle exemplaren die op de server worden uitgevoerd, en kunnen worden gemigreerd naar een Azure VM via het hulpprogramma voor servermigratie. Voordat u migreert, [raadpleegt u de richtlijnen voor prestaties](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) voor SQL Server op virtuele Azure-machines.

## <a name="the-storage-cost-in-my-azure-sql-assessment-is-zero"></a>De opslag kosten in mijn Azure SQL-evaluatie zijn nul
Voor Azure SQL Managed instance zijn er geen opslag kosten toegevoegd voor de eerste 32 GB/exemplaar/maand opslag en worden extra opslag kosten toegevoegd voor opslag in 32 GB. [Meer informatie](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Ik zie sommige groepen niet bij het maken van een evaluatie van een Azure VMware-oplossing (AVS)

- Een AVS-evaluatie kan worden uitgevoerd voor groepen die alleen VMware-machines hebben. Verwijder alle niet-VMware-machines uit de groep als u van plan bent om een AVS-evaluatie uit te voeren.
- Als u AVS-evaluaties in Azure Migrate de eerste keer uitvoert, is het raadzaam om een nieuwe groep VMware-machines te maken.

## <a name="i-cant-see-some-vm-types-and-sizes-in-azure-government"></a>Ik zie enkele VM-typen en-grootten niet in Azure Government

VM-typen en-grootten die worden ondersteund voor evaluatie en migratie, zijn afhankelijk van de beschik baarheid op Azure Government locatie. U kunt VM-typen [controleren en vergelijken](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) in azure Government.

## <a name="the-size-of-my-server-changed-can-i-run-an-assessment-again"></a>De grootte van mijn server is gewijzigd. Kan ik een evaluatie opnieuw uitvoeren?

Het Azure Migrate apparaat verzamelt voortdurend informatie over de on-premises omgeving.  Een evaluatie is een tijdgebonden moment opname van on-premises servers. Als u de instellingen wijzigt op een server die u wilt beoordelen, gebruikt u de optie opnieuw berekenen om de evaluatie bij te werken met de meest recente wijzigingen.

## <a name="how-do-i-discover-servers-in-a-multitenant-environment"></a>Hoe kan ik Detecteer servers in een multi tenant-omgeving?

- **VMware**: als een omgeving wordt gedeeld tussen tenants en u niet wilt dat de servers van een Tenant in een abonnement op een andere Tenant worden gedetecteerd, moet u VMware vCenter Server referenties maken die alleen toegang hebben tot de servers die u wilt detecteren. Gebruik vervolgens deze referenties wanneer u de detectie start in het Azure Migrate apparaat.
- **Hyper-v**: detectie maakt gebruik van referenties voor hyper-v-hosts. Als servers dezelfde Hyper-V-host delen, is er momenteel geen enkele manier om de detectie te scheiden.  

## <a name="do-i-need-vcenter-server"></a>Heb ik vCenter Server nodig?

Ja, Azure Migrate vereist vCenter Server in een VMware-omgeving om detectie uit te voeren. Azure Migrate biedt geen ondersteuning voor detectie van ESXi-hosts die niet worden beheerd door vCenter Server.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Wat zijn de opties voor het aanpassen van de grootte van een Azure VM-evaluatie?

Bij een on-premises grootte wordt Azure Migrate geen rekening gehouden met de prestatie gegevens van de server voor evaluatie. Azure Migrate evalueert VM-grootten op basis van de on-premises configuratie. De grootte van de prestaties is gebaseerd op de gebruiks gegevens.

Bijvoorbeeld, als een on-premises server vier kernen en 8 GB geheugen heeft met een CPU-gebruik van 50% en 50% geheugen gebruik:
- Bij een on-premises grootte wordt een Azure VM-SKU aanbevolen met vier kernen en 8 GB aan geheugen.
- Op basis van de prestaties wordt een VM-SKU aanbevolen met twee kernen en 4 GB geheugen, omdat het gebruiks percentage wordt overwogen.

De schijf grootte is ook afhankelijk van de grootte criteria en het opslag type:
- Als de grootte criteria op basis van prestaties zijn en het opslag type automatisch is, neemt Azure Migrate de IOPS-en doorvoer waarden van de schijf in rekening wanneer het type doel schijf (Standard of Premium) wordt geïdentificeerd.
- Als de grootte criteria op basis van prestaties zijn en het opslag type Premium is, raadt Azure Migrate een Premium-schijf-SKU aan op basis van de grootte van de on-premises schijf. Dezelfde logica wordt toegepast op schijf grootte wanneer de grootte is ingesteld op on-premises en het opslag type Standard of Premium is.

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>Is de prestatie geschiedenis en het gebruik van invloed op de grootte van een Azure VM-evaluatie?

Ja, prestatie geschiedenis en gebruik zijn van invloed op de grootte van een Azure VM-evaluatie.

### <a name="performance-history"></a>Prestatiegeschiedenis

Azure Migrate verzamelt de prestatie geschiedenis van on-premises computers, en gebruikt deze vervolgens om de VM-grootte en het schijf type in azure aan te bevelen:

1. Het apparaat maakt continu gebruik van de on-premises omgeving voor het verzamelen van gegevens in realtime om de 20 seconden.
2. Het apparaat rolt de verzamelde voor beelden van 20 seconden samen en gebruikt deze voor het maken van één gegevens punt om de 15 minuten.
3. Voor het maken van het gegevens punt selecteert het apparaat de piek waarde van alle voor beelden van 20 seconden.
4. Het apparaat verzendt het gegevens punt naar Azure.

### <a name="utilization"></a>Gebruik

Bij het maken van een evaluatie in azure, afhankelijk van de prestatie duur en de waarde voor het percentiel van de prestatie geschiedenis, berekent Azure Migrate de juiste gebruiks waarde en wordt deze gebruikt voor het aanpassen van de grootte.

Als u bijvoorbeeld de duur van de prestaties instelt op één dag en de percentiel waarde op het 95e percentiel, Azure Migrate worden de voorbeeld punten van 15 minuten die zijn verzonden door de collector voor de afgelopen dag in oplopende volg orde gesorteerd. Hiermee wordt de waarde van het 95e percentiel als effectief gebruik gekozen.

Het gebruik van de waarde voor het percentiel van 95e zorgt ervoor dat uitbijters worden genegeerd. Uitbijters kunnen worden opgenomen als uw Azure Migrate gebruikmaakt van het 99e percentiel. Als u het piek gebruik voor de periode wilt kiezen zonder uitbijters te missen, stelt u Azure Migrate in om het 99e percentiel te gebruiken.


## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Wat is het verschil tussen evaluaties op basis van een import bewerking en de detectie bron als apparaat?

Azure VM-evaluaties op basis van import zijn evaluaties die zijn gemaakt met machines die in Azure Migrate worden geïmporteerd met behulp van een CSV-bestand. Er zijn slechts vier velden die verplicht moeten worden geïmporteerd: Server naam, kern geheugens en besturings systeem. Hier volgen enkele dingen die u moet weten: 
 - De gereedheids criteria zijn minder strikt in evaluaties op basis van een import bewerking voor de opstart type-para meter. Als het opstart type niet is opgegeven, wordt ervan uitgegaan dat de computer een BIOS-opstart type heeft en dat de machine niet als **voorwaardelijk** is gemarkeerd. In beoordelingen met detectie bron als apparaat is de gereedheid gemarkeerd als **voorwaardelijk gereed** als het opstart type ontbreekt. Dit verschil in de gereedheids berekening is omdat gebruikers mogelijk niet alle informatie over de computers in de vroege fase van de migratie planning hebben wanneer op import gebaseerde evaluaties worden uitgevoerd. 
 - Bij evaluaties op basis van de prestaties wordt gebruikgemaakt van de gebruiks waarde die door de gebruiker wordt geleverd voor het berekenen van de rechter grootte. Aangezien de gebruiks waarde wordt geleverd door de gebruiker, worden de opties voor de **prestatie geschiedenis** en het **percentiel gebruik** in de eigenschappen van de beoordeling uitgeschakeld. In beoordelingen met detectie bron als apparaat wordt de gekozen percentiel waarde opgehaald uit de prestatie gegevens die zijn verzameld door het apparaat.

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Waarom is het voorgestelde migratie hulpprogramma in op import gebaseerde AVS-evaluatie gemarkeerd als onbekend?

Voor machines die worden geïmporteerd via een CSV-bestand, is het standaard hulp programma voor migratie in een AVS-evaluatie onbekend. Voor VMware-machines is het echter raadzaam de VMware Hybrid Cloud extension (HCX)-oplossing te gebruiken. [Meer informatie](../azure-vmware/tutorial-deploy-vmware-hcx.md).


## <a name="what-is-dependency-visualization"></a>Wat is de visualisatie van afhankelijkheden?

Met behulp van afhankelijkheids visualisatie kunt u groepen servers zo bepalen dat deze met een grotere betrouw baarheid worden gemigreerd. Afhankelijkheids visualisatie: meerdere computer afhankelijkheden controleren voordat u een evaluatie uitvoert. Zo kunt u er zeker van zijn dat er niets achter komt, waardoor onverwachte storingen worden voor komen wanneer u naar Azure migreert. Azure Migrate maakt gebruik van de Servicetoewijzing-oplossing in Azure Monitor om afhankelijkheidsvisualisatie mogelijk te maken. [Meer informatie](concepts-dependency-visualization.md).

> [!NOTE]
> Afhankelijkheids analyse op basis van een agent is niet beschikbaar in Azure Government. U kunt afhankelijkheids analyse zonder agent gebruiken

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Wat is het verschil tussen op een agent en zonder agents?

De verschillen tussen visualisatie zonder agents en visualisaties op basis van agents worden in de tabel samenvatten.

**Vereiste** | **Zonder agent** | **Op basis van een agent**
--- | --- | ---
Ondersteuning | Deze optie is momenteel in Preview en is alleen beschikbaar voor servers in de VMware-omgeving. [Bekijk](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) ondersteunde besturings systemen. | In algemene Beschik baarheid (GA).
Agent | U hoeft geen agents te installeren op computers die u wilt cross-checken. | Agents die moeten worden geïnstalleerd op elke on-premises computer die u wilt analyseren: [micro soft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)en de [dependency agent](../azure-monitor/agents/agents-overview.md#dependency-agent). 
Vereisten | [Bekijk](concepts-dependency-visualization.md#agentless-analysis) de vereisten en implementatie behoeften. | [Bekijk](concepts-dependency-visualization.md#agent-based-analysis) de vereisten en implementatie behoeften.
Log Analytics | Niet vereist. | Azure Migrate gebruikt de [servicetoewijzing](../azure-monitor/vm/service-map.md) oplossing in [Azure monitor logboeken](../azure-monitor/logs/log-query-overview.md) voor de visualisatie van afhankelijkheden. [Meer informatie](concepts-dependency-visualization.md#agent-based-analysis).
Uitleg | Hiermee worden TCP-verbindings gegevens vastgelegd op computers die zijn ingeschakeld voor de visualisatie van afhankelijkheden. Na detectie verzamelt het gegevens met intervallen van vijf minuten. | Servicetoewijzing agents die op een computer zijn geïnstalleerd, verzamelen gegevens over TCP-processen en inkomende/uitgaande verbindingen voor elk proces.
Gegevens | Naam van de bron computer server, proces, toepassings naam.<br/><br/> Naam van de doel computer server, proces, toepassings naam en poort. | Naam van de bron computer server, proces, toepassings naam.<br/><br/> Naam van de doel computer server, proces, toepassings naam en poort.<br/><br/> Het aantal gegevens over verbindingen, latentie en gegevens overdracht wordt verzameld en beschikbaar gesteld voor Log Analytics query's. 
Visualisatie | Afhankelijkheids toewijzing van één server kan worden weer gegeven gedurende een periode van één uur tot 30 dagen. | Afhankelijkheids toewijzing van één server.<br/><br/> De kaart kan alleen over een uur worden weer gegeven.<br/><br/> Afhankelijkheids toewijzing van een groep servers.<br/><br/> Servers in een groep toevoegen aan en verwijderen uit de kaart weergave.
Gegevensexport | Gegevens van de afgelopen 30 dagen kunnen worden gedownload in een CSV-indeling. | Gegevens kunnen worden opgevraagd met Log Analytics.


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>Moet ik het apparaat implementeren voor een afhankelijkheids analyse zonder agent?

Ja, het [Azure migrate apparaat](migrate-appliance.md) moet zijn geïmplementeerd.

## <a name="do-i-pay-for-dependency-visualization"></a>Moet ik betalen voor de visualisatie van de afhankelijkheid?

Nee. Meer informatie over [Azure migrate prijzen](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Wat moet ik installeren voor visualisatie op basis van een agent?

Als u visualisatie op basis van een op agents gebaseerde afhankelijkheid wilt gebruiken, downloadt en installeert u agents op elke on-premises computer die u wilt evalueren:

- [Micro soft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)
- [Agent voor afhankelijkheden](../azure-monitor/agents/agents-overview.md#dependency-agent)
- Als u machines hebt die geen verbinding met internet hebben, kunt u de Log Analytics-gateway op de computers downloaden en installeren.

U hebt deze agents alleen nodig als u visualisatie van afhankelijkheden op basis van een agent gebruikt.

## <a name="can-i-use-an-existing-workspace"></a>Kan ik een bestaande werk ruimte gebruiken?

Ja, voor visualisatie op basis van een agent kunt u een bestaande werk ruimte koppelen aan het migratie project en deze gebruiken voor de visualisatie van afhankelijkheden. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Kan ik het rapport over de visualisatie van afhankelijkheden exporteren?

Nee, het visualisatie rapport van de afhankelijkheid in de op een agent gebaseerde visualisatie kan niet worden geëxporteerd. Azure Migrate maakt echter gebruik van Servicetoewijzing, en u kunt de [Servicetoewijzing rest API](/rest/api/servicemap/machines/listconnections) gebruiken om de afhankelijkheden in JSON-indeling op te halen.

## <a name="can-i-automate-agent-installation"></a>Kan ik de agent installatie automatiseren?

Voor visualisatie op basis van een agent:

- Gebruik een [script om de afhankelijkheids agent te installeren](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent).
- Gebruik voor MMA [de opdracht regel of de automatisering](../azure-monitor/agents/log-analytics-agent.md#installation-options)of gebruik een [script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Naast scripts kunt u implementatie hulpprogramma's zoals micro soft endpoint Configuration Manager en [Intigua](https://www.intigua.com/intigua-for-azure-migration) gebruiken om de agents te implementeren.

## <a name="what-operating-systems-does-mma-support"></a>Welke besturings systemen ondersteunt MMA?

- Bekijk de lijst met [Windows-besturings systemen die MMA ondersteunt](../azure-monitor/agents/log-analytics-agent.md#installation-options).
- Bekijk de lijst met [Linux-besturings systemen die MMA ondersteunt](../azure-monitor/agents/log-analytics-agent.md#installation-options).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Kan ik afhankelijkheden langer dan een uur visualiseren?

Voor visualisaties op basis van een agent kunt u afhankelijkheden tot een uur visualiseren. U kunt één maand teruggaan naar een specifieke datum in de geschiedenis, maar de maximale duur voor de visualisatie is één uur. U kunt bijvoorbeeld de tijds duur in de afhankelijkheids toewijzing gebruiken om afhankelijkheden voor gisteren weer te geven, maar u kunt alleen afhankelijkheden weer geven voor een venster van één uur. U kunt echter Azure Monitor-Logboeken gebruiken om een langere duur van [afhankelijkheids gegevens](./how-to-create-group-machine-dependencies.md) op te vragen.

Voor visualisatie zonder agent kunt u de afhankelijkheids toewijzing van één server weer geven vanaf een duur van slechts één uur tot 30 dagen.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-servers"></a>Kan ik afhankelijkheden voor groepen van meer dan 10 servers visualiseren?

U kunt [afhankelijkheden](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) voor groepen met Maxi maal 10 servers visualiseren. Als u een groep hebt met meer dan 10 servers, raden we u aan de groep te splitsen in kleinere groepen en vervolgens de afhankelijkheden te visualiseren.

## <a name="next-steps"></a>Volgende stappen

Lees het [Azure migrate overzicht](migrate-services-overview.md).