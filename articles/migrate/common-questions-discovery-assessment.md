---
title: Vragen over detectie, evaluatie en afhankelijkheids analyse in Azure Migrate
description: Krijg antwoorden op veelgestelde vragen over detectie, evaluatie en afhankelijkheids analyse in Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 4531d68c2fbd0698c33d70a75bb82ac9c7f52f49
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752240"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Detectie, beoordeling en afhankelijkheids analyse-Veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over detectie, evaluatie en afhankelijkheids analyse in Azure Migrate. Als u andere vragen hebt, raadpleegt u deze bronnen:

- [Algemene vragen](resources-faq.md) over Azure migrate
- Vragen over het [Azure migrate apparaat](common-questions-appliance.md)
- Vragen over [server migratie](common-questions-server-migration.md)
- Beantwoorde vragen in het [Azure migrate-forum](https://aka.ms/AzureMigrateForum) ontvangen


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Welke geografische gebieden worden ondersteund voor detectie en evaluatie met Azure Migrate?

Bekijk de ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Hoeveel Vm's kan ik vinden met een apparaat?

U kunt Maxi maal 10.000 VMware-Vm's, Maxi maal 5.000 virtuele Hyper-V-machines en Maxi maal 1000 fysieke servers, detecteren door één apparaat te gebruiken. Als u meer computers hebt, leest u over [het schalen van een Hyper-V-beoordeling](scale-hyper-v-assessment.md), [het schalen van een VMware-evaluatie](scale-vmware-assessment.md)of [het schalen van een fysieke server beoordeling](scale-physical-assessment.md).

## <a name="how-do-i-choose-the-assessment-type"></a>Hoe kies ik het evaluatietype?

- Gebruik **Azure VM-evaluaties** als u uw on-premises virtuele [VMware-machines](how-to-set-up-appliance-vmware.md), [virtuele Hyper-V-](how-to-set-up-appliance-hyper-v.md)machines en [fysieke servers](how-to-set-up-appliance-physical.md) wilt controleren op migratie naar Azure-vm's. [Meer informatie](concepts-assessment-calculation.md)

- Gebruik de evaluaties van de **Azure VMware-oplossing (AVS)** als u uw on-premises [virtuele VMware-machines](how-to-set-up-appliance-vmware.md) wilt controleren op migratie naar [Azure VMware-oplossing (AVS)](../azure-vmware/introduction.md) met dit beoordelings type. [Meer informatie](concepts-azure-vmware-solution-assessment-calculation.md)

- U kunt een algemene groep met VMware-machines alleen gebruiken om beide typen evaluaties uit te voeren. Let op: als u AVS-evaluaties in Azure Migrate voor het eerst uitvoert, is het raadzaam om een nieuwe groep VMware-machines te maken.
 

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>Waarom ontbreken er prestatiegegevens voor sommige/alle VM's in mijn evaluatierapport?

Voor evaluaties Op basis van prestaties staat in de export van het evaluatierapport PercentageOfCoresUtilizedMissing of PercentageOfMemoryUtilizedMissing, wanneer er geen prestatiegegevens voor de on-premises VM's kunnen worden verzameld op het Azure Migrate-apparaat. Controleer het volgende:

- Of de VM's zijn ingeschakeld gedurende de periode waarvoor u de evaluatie maakt
- Als er alleen geheugenitems ontbreken en u virtuele Hyper-V-machines probeert te evalueren, controleert u of er dynamisch geheugen is ingeschakeld op deze virtuele machines. Er is momenteel een bekend probleem als gevolg waarvan het Azure Migrate-apparaat geen geheugengebruik kan verzamelen voor dergelijke VM's.
- Als alle prestatie meter items ontbreken, moet u ervoor zorgen dat uitgaande verbindingen op poort 443 (HTTPS) zijn toegestaan.

Opmerking: als een van de prestatiemeteritems ontbreekt, gebeurt het volgende in Azure Migrate: Server-evaluatie valt terug op de toegewezen on-premises kernen/geheugen en raadt een relevante VM-grootte aan.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Waarom is de betrouwbaarheidsclassificatie van mijn evaluatie laag?

De betrouwbaarheidsclassificatie wordt berekend voor evaluaties Op basis van prestaties, op basis van het percentage [beschikbare gegevenspunten](./concepts-assessment-calculation.md#ratings) dat nodig is om de evaluatie te berekenen. Hieronder ziet u de redenen waarom een evaluatie een lage betrouwbaarheidsclassificatie kan krijgen:

- U hebt uw omgeving niet geprofileerd gedurende de periode waarvoor u de evaluatie maakt. Als u bijvoorbeeld een evaluatie maakt waarbij de duur van de prestaties is ingesteld op één week, moet u na het starten van de detectie minstens een week wachten voordat alle gegevenspunten zijn verzameld. Als u niet kunt wachten op de duur, wijzigt u de duur van de prestaties in een kortere periode en berekent u de evaluatie opnieuw.
 
- Met server evaluatie kunnen de prestatie gegevens voor sommige of alle virtuele machines in de evaluatie periode niet worden verzameld. Controleer of de virtuele machines zijn ingeschakeld voor de duur van de evaluatie en of uitgaande verbindingen op poort 443 zijn toegestaan. Als dynamisch geheugen is ingeschakeld voor virtuele Hyper-VM's, ontbreken er geheugenitems, wat tot een lage betrouwbaarheidsclassificatie leidt. Bereken de evaluatie opnieuw om de meest recente wijzigingen in de betrouwbaarheidsclassificatie weer te geven. 

- Er zijn enkele VM’s gemaakt nadat detectie in Server-evaluatie al was gestart. Als u bijvoorbeeld een evaluatie maakt voor de prestatiegeschiedenis van de laatste maand, maar er een week geleden enkele VM's in de omgeving zijn gemaakt. In dit geval zijn er voor de hele periode geen prestatiegegevens van de nieuwe VM’s beschikbaar, waardoor de betrouwbaarheidsclassificatie laag is.

[Meer informatie](./concepts-assessment-calculation.md#confidence-ratings-performance-based) over betrouwbaarheidsclassificaties.

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Ik zie sommige groepen niet bij het maken van een evaluatie van een Azure VMware-oplossing (AVS)

- Een AVS-evaluatie kan worden uitgevoerd voor groepen die alleen VMware-machines hebben. Verwijder alle niet-VMware-machines uit de groep als u van plan bent om een AVS-evaluatie uit te voeren.
- Als u AVS-evaluaties in Azure Migrate de eerste keer uitvoert, is het raadzaam om een nieuwe groep VMware-machines te maken.

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>Ik zie geen enkele VM-typen in Azure Government

VM-typen die voor evaluatie en migratie worden ondersteund, zijn afhankelijk van de beschik baarheid op Azure Government locatie. U kunt VM-typen [controleren en vergelijken](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) in azure Government.

## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>De grootte van mijn VM is gewijzigd. Kan ik een evaluatie opnieuw uitvoeren?

Het Azure Migrate apparaat verzamelt voortdurend informatie over de on-premises omgeving.  Een evaluatie is een tijdgebonden moment opname van on-premises Vm's. Als u de instellingen van een virtuele machine die u wilt beoordelen, wijzigt, gebruikt u de optie opnieuw berekenen om de evaluatie bij te werken met de meest recente wijzigingen.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Hoe kan ik virtuele machines detecteren in een multi tenant-omgeving?

- **VMware**: als een omgeving wordt gedeeld door tenants en u niet wilt dat de vm's van een Tenant in een ander abonnement van een Tenant worden gedetecteerd, moet u VMware vCenter Server referenties maken die alleen toegang hebben tot de virtuele machines die u wilt detecteren. Gebruik vervolgens deze referenties wanneer u de detectie start in het Azure Migrate apparaat.
- **Hyper-v**: detectie maakt gebruik van referenties voor hyper-v-hosts. Als Vm's dezelfde Hyper-V-host delen, is er momenteel geen enkele manier om de detectie te scheiden.  

## <a name="do-i-need-vcenter-server"></a>Heb ik vCenter Server nodig?

Ja, Azure Migrate vereist vCenter Server in een VMware-omgeving om detectie uit te voeren. Azure Migrate biedt geen ondersteuning voor detectie van ESXi-hosts die niet worden beheerd door vCenter Server.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Wat zijn de opties voor het aanpassen van de grootte van een Azure VM-evaluatie?

Bij een on-premises grootte wordt Azure Migrate geen rekening gehouden met de prestatie gegevens van de virtuele machine voor evaluatie. Azure Migrate evalueert VM-grootten op basis van de on-premises configuratie. De grootte van de prestaties is gebaseerd op de gebruiks gegevens.

Bijvoorbeeld, als een on-premises VM vier kernen en 8 GB geheugen heeft met een CPU-gebruik van 50% en 50% geheugen gebruik:
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

Met behulp van afhankelijkheids visualisatie kunt u groepen Vm's bepalen die met een grotere betrouw baarheid moeten worden gemigreerd. Afhankelijkheids visualisatie: meerdere computer afhankelijkheden controleren voordat u een evaluatie uitvoert. Zo kunt u er zeker van zijn dat er niets achter komt, waardoor onverwachte storingen worden voor komen wanneer u naar Azure migreert. Azure Migrate maakt gebruik van de Servicetoewijzing-oplossing in Azure Monitor om afhankelijkheidsvisualisatie mogelijk te maken. [Meer informatie](concepts-dependency-visualization.md).

> [!NOTE]
> Afhankelijkheids analyse op basis van een agent is niet beschikbaar in Azure Government. U kunt afhankelijkheids analyse zonder agent gebruiken

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Wat is het verschil tussen op een agent en zonder agents?

De verschillen tussen visualisatie zonder agents en visualisaties op basis van agents worden in de tabel samenvatten.

**Vereiste** | **Zonder agent** | **Op basis van een agent**
--- | --- | ---
Ondersteuning | Deze optie is momenteel in Preview en is alleen beschikbaar voor virtuele VMware-machines. [Bekijk](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) ondersteunde besturings systemen. | In algemene Beschik baarheid (GA).
Agent | U hoeft geen agents te installeren op computers die u wilt cross-checken. | Agents die moeten worden geïnstalleerd op elke on-premises computer die u wilt analyseren: [micro soft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md)en de [dependency agent](../azure-monitor/platform/agents-overview.md#dependency-agent). 
Vereisten | [Bekijk](concepts-dependency-visualization.md#agentless-analysis) de vereisten en implementatie behoeften. | [Bekijk](concepts-dependency-visualization.md#agent-based-analysis) de vereisten en implementatie behoeften.
Log Analytics | Niet vereist. | Azure Migrate gebruikt de [servicetoewijzing](../azure-monitor/insights/service-map.md) oplossing in [Azure monitor logboeken](../azure-monitor/log-query/log-query-overview.md) voor de visualisatie van afhankelijkheden. [Meer informatie](concepts-dependency-visualization.md#agent-based-analysis).
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

- [Micro soft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md)
- [Agent voor afhankelijkheden](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Als u machines hebt die geen verbinding met internet hebben, kunt u de Log Analytics-gateway op de computers downloaden en installeren.

U hebt deze agents alleen nodig als u visualisatie van afhankelijkheden op basis van een agent gebruikt.

## <a name="can-i-use-an-existing-workspace"></a>Kan ik een bestaande werk ruimte gebruiken?

Ja, voor visualisatie op basis van een agent kunt u een bestaande werk ruimte koppelen aan het migratie project en deze gebruiken voor de visualisatie van afhankelijkheden. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Kan ik het rapport over de visualisatie van afhankelijkheden exporteren?

Nee, het visualisatie rapport van de afhankelijkheid in de op een agent gebaseerde visualisatie kan niet worden geëxporteerd. Azure Migrate maakt echter gebruik van Servicetoewijzing, en u kunt de [Servicetoewijzing rest API](/rest/api/servicemap/machines/listconnections) gebruiken om de afhankelijkheden in JSON-indeling op te halen.

## <a name="can-i-automate-agent-installation"></a>Kan ik de agent installatie automatiseren?

Voor visualisatie op basis van een agent:

- Gebruik een [script om de afhankelijkheids agent te installeren](../azure-monitor/insights/vminsights-enable-hybrid.md#dependency-agent).
- Gebruik voor MMA [de opdracht regel of de automatisering](../azure-monitor/platform/log-analytics-agent.md#installation-options)of gebruik een [script](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Naast scripts kunt u implementatie hulpprogramma's zoals micro soft endpoint Configuration Manager en [Intigua](https://www.intigua.com/intigua-for-azure-migration) gebruiken om de agents te implementeren.

## <a name="what-operating-systems-does-mma-support"></a>Welke besturings systemen ondersteunt MMA?

- Bekijk de lijst met [Windows-besturings systemen die MMA ondersteunt](../azure-monitor/platform/log-analytics-agent.md#installation-options).
- Bekijk de lijst met [Linux-besturings systemen die MMA ondersteunt](../azure-monitor/platform/log-analytics-agent.md#installation-options).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Kan ik afhankelijkheden langer dan een uur visualiseren?

Voor visualisaties op basis van een agent kunt u afhankelijkheden tot een uur visualiseren. U kunt één maand teruggaan naar een specifieke datum in de geschiedenis, maar de maximale duur voor de visualisatie is één uur. U kunt bijvoorbeeld de tijds duur in de afhankelijkheids toewijzing gebruiken om afhankelijkheden voor gisteren weer te geven, maar u kunt alleen afhankelijkheden weer geven voor een venster van één uur. U kunt echter Azure Monitor-Logboeken gebruiken om een langere duur van [afhankelijkheids gegevens](./how-to-create-group-machine-dependencies.md) op te vragen.

Voor visualisatie zonder agent kunt u de afhankelijkheids toewijzing van één server weer geven vanaf een duur van slechts één uur tot 30 dagen.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Kan ik afhankelijkheden voor groepen van meer dan tien Vm's visualiseren?

U kunt [afhankelijkheden](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) voor groepen met Maxi maal tien vm's visualiseren. Als u een groep hebt met meer dan tien Vm's, raden we u aan de groep te splitsen in kleinere groepen en vervolgens de afhankelijkheden te visualiseren.

## <a name="next-steps"></a>Volgende stappen

Lees het [Azure migrate overzicht](migrate-services-overview.md).