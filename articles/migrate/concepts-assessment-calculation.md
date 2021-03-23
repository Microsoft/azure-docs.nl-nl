---
title: Azure VM-evaluaties in Azure Migrate
description: Meer informatie over evaluaties in Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 7d756b53247206ab4dd4f955c954e6bd105afa1d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778488"
---
# <a name="assessment-overview-migrate-to-azure-vms"></a>Overzicht van evaluatie (migreren naar Azure-VM's)

In dit artikel vindt u een overzicht van de evaluaties in de [Azure migrate: Server detectie en evaluatie](migrate-services-overview.md) programma. Het hulp programma kan on-premises servers in virtuele VMware-en Hyper-V-omgevingen en fysieke servers beoordelen voor migratie naar Azure.

## <a name="whats-an-assessment"></a>Wat is een evaluatie?

Een evaluatie met het hulp programma detectie en evaluatie meet de gereedheid en schat het effect van het migreren van on-premises servers naar Azure.

> [!NOTE]
> Bekijk in Azure Government de [ondersteunde doel](migrate-support-matrix.md#supported-geographies-azure-government) locaties voor de evaluatie. Houd er rekening mee dat de aanbevelingen voor de VM-grootte in evaluaties de VM-serie specifiek voor overheids Cloud regio's gebruiken. Meer [informatie](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) over VM-typen.

## <a name="types-of-assessments"></a>Typen evaluaties

Er zijn drie soorten evaluaties die u kunt maken met behulp van Azure Migrate: detectie en evaluatie.

***Beoordelings type** | **Details**
--- | --- 
**Azure VM** | Evaluaties om uw on-premises servers te migreren naar virtuele Azure-machine. U kunt uw on-premises servers in [VMware](how-to-set-up-appliance-vmware.md) en [Hyper-V-](how-to-set-up-appliance-hyper-v.md) omgeving, en [fysieke servers](how-to-set-up-appliance-physical.md) voor migratie naar virtuele Azure-machines evalueren met dit beoordelings type.
**Azure SQL** | Beoordelingen voor het migreren van uw on-premises SQL-servers vanuit uw VMware-omgeving naar Azure SQL Database of Azure SQL Managed instance.
**Azure VMware Solution (AVS)** | Evaluaties om uw on-premises servers te migreren naar [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). U kunt uw on-premises [VMware-VM’s](how-to-set-up-appliance-vmware.md) evalueren voor migratie naar Azure VMware Solution (AVS) met dit evaluatietype. [Meer informatie](concepts-azure-vmware-solution-assessment-calculation.md)

Evaluaties die u met Azure Migrate maakt, zijn een tijdgebonden moment opname van gegevens. Een Azure VM-evaluatie biedt twee opties voor het instellen van de grootte:

**Evaluatietype** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties die aanbevelingen doen op basis van verzamelde prestatiegegevens | De aanbeveling van de VM-grootte is gebaseerd op de CPU-en RAM-gebruiks gegevens.<br/><br/> De aanbeveling van het schijf type is gebaseerd op de invoer/uitvoer-bewerkingen per seconde (IOPS) en de door Voer van de on-premises schijven. Schijf typen zijn Azure-Standard-HDD, Azure Standard-SSD en Azure Premium-schijven.
**As-is on-premises** | Beoordelingen die geen prestatie gegevens gebruiken om aanbevelingen te doen | De aanbeveling van de VM-grootte is gebaseerd op de grootte van de on-premises server.<br/><br> Het aanbevolen schijf type is gebaseerd op het geselecteerde opslag type voor de evaluatie.

## <a name="how-do-i-run-an-assessment"></a>Hoe kan ik een evaluatie uit te voeren?

Er zijn een aantal manieren om een evaluatie uit te voeren.

- Evalueer servers met behulp van server meta gegevens die zijn verzameld door een licht gewicht Azure Migrate apparaat. Het apparaat detecteert on-premises servers. Vervolgens worden de meta gegevens en prestatie gegevens van de server naar Azure Migrate verzonden.
- Evalueer servers met behulp van server meta gegevens die worden geïmporteerd in een CSV-indeling (door lijst scheidings tekens gescheiden waarden).

## <a name="how-do-i-assess-with-the-appliance"></a>Hoe kan ik evalueren met het apparaat?

Als u een Azure Migrate apparaat implementeert om on-premises servers te detecteren, voert u de volgende stappen uit:

1. Stel Azure en uw on-premises omgeving in om met Azure Migrate te werken.
1. Voor uw eerste evaluatie maakt u een Azure-project en voegt u het hulp programma voor detectie en evaluatie toe.
1. Een licht gewicht Azure Migrate implementeren. Het apparaat detecteert voortdurend on-premises servers en verzendt meta gegevens en prestatie gegevens van de server naar Azure Migrate. Implementeer het apparaat als een virtuele machine of een fysieke server. U hoeft niets te installeren op servers die u wilt beoordelen.

Nadat het apparaat server detectie heeft gestart, kunt u de servers die u wilt beoordelen in een groep verzamelen en een evaluatie uitvoeren voor de groep met een evaluatie type van **Azure VM**.

Volg de zelf studies voor [VMware](./tutorial-discover-vmware.md)-, [Hyper-V-](./tutorial-discover-hyper-v.md)of [fysieke servers](./tutorial-discover-physical.md) om deze stappen uit te proberen.

## <a name="how-do-i-assess-with-imported-data"></a>Hoe kan ik evalueren met geïmporteerde gegevens?

Als u servers met behulp van een CSV-bestand wilt beoordelen, hebt u geen apparaat nodig. Voer in plaats daarvan de volgende stappen uit:

1. Azure instellen voor gebruik met Azure Migrate
1. Voor uw eerste evaluatie maakt u een Azure-project en voegt u het hulp programma voor detectie en evaluatie toe.
1. Een CSV-sjabloon downloaden en er Server gegevens aan toevoegen.
1. Importeer de sjabloon in Azure Migrate
1. Ontdek servers die zijn toegevoegd met de import, verzamel ze in een groep en voer een evaluatie uit voor de groep met een evaluatie type van **Azure VM**.

## <a name="what-data-does-the-appliance-collect"></a>Welke gegevens worden door het apparaat verzameld?

Als u het Azure Migrate-apparaat gebruikt voor evaluatie, kunt u meer informatie vinden over de meta gegevens en prestatie gegevens die worden verzameld voor [VMware](migrate-appliance.md#collected-data---vmware) en [Hyper-V](migrate-appliance.md#collected-data---hyper-v).

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hoe worden prestatie gegevens berekend op het apparaat?

Als u het apparaat voor detectie gebruikt, worden de prestatie gegevens voor de reken instellingen verzameld met de volgende stappen:

1. Het apparaat verzamelt een real-time voor beeld van een punt.

    - **VMware-vm's**: een voor beeld van een punt wordt elke 20 seconden verzameld.
    - **Virtuele Hyper-V-machines**: een voor beeld van een punt wordt elke 30 seconden verzameld.
    - **Fysieke servers**: een voor beeld van een punt wordt elke vijf minuten verzameld.

1. Het apparaat combineert de voorbeeld punten voor het maken van één gegevens punt om de 10 minuten voor VMware-en Hyper-V-servers en om de 5 minuten voor fysieke servers. Het apparaat selecteert de piek waarden van alle voor beelden om het gegevens punt te maken. Vervolgens wordt het gegevens punt naar Azure verzonden.
1. In de evaluatie worden alle gegevens punten van 10 minuten voor de afgelopen maand opgeslagen.
1. Wanneer u een evaluatie maakt, identificeert de evaluatie het juiste gegevens punt dat moet worden gebruikt voor supportte. Identificatie is gebaseerd op de percentiel waarden voor de *prestatie geschiedenis* en het *percentiel gebruik*.

    - Als de prestatie geschiedenis bijvoorbeeld één week is en het percentiel gebruik het 95e percentiel is, sorteert de evaluatie de 10-minuten steekproef punten voor de afgelopen week. Ze worden in oplopende volg orde gesorteerd en de 95e percentiel waarde voor supportte wordt gekozen.
    - De 95e percentiel waarde zorgt ervoor dat u alle uitbijters negeert, die mogelijk worden opgenomen als u het 99e percentiel hebt gekozen.
    - Als u het piek gebruik voor de periode wilt kiezen en u geen uitbijters wilt missen, selecteert u het 99e percentiel voor percentiel gebruik.

1. Deze waarde wordt vermenigvuldigd met de comfort factor om de effectief prestatie gebruiks gegevens te verkrijgen voor de volgende meet waarden die het apparaat verzamelt:

    - CPU-gebruik
    - RAM-gebruik
    - Schijf-IOPS (lezen en schrijven)
    - Schijf doorvoer (lezen en schrijven)
    - Netwerk doorvoer (in-en uitgaand)

## <a name="how-are-azure-vm-assessments-calculated"></a>Hoe worden Azure VM-evaluaties berekend?

De evaluatie maakt gebruik van de meta gegevens en prestaties van de on-premises servers om evaluaties te berekenen. Als u het Azure Migrate apparaat implementeert, gebruikt de evaluatie de gegevens die door het apparaat worden verzameld. Maar als u een evaluatie uitvoert die is geïmporteerd met behulp van een CSV-bestand, geeft u de meta gegevens voor de berekening op.

In deze drie fasen worden berekeningen uitgevoerd:

1. **Azure-gereedheid berekenen**: Bepaal of servers geschikt zijn voor migratie naar Azure.
1. **Aanbevelingen voor de grootte berekenen**: schatting van de berekenings-, opslag-en netwerk grootte.
1. **Bereken de maandelijkse kosten**: Bereken de geschatte maandelijkse reken-en opslag kosten voor het uitvoeren van de servers in azure na de migratie.

Berekeningen worden in de voor gaande volg orde beschreven. Een server server wordt alleen naar een latere fase verplaatst als deze de voor gaande wordt door gegeven. Als een server bijvoorbeeld uitvalt op de Azure Readiness-fase, wordt deze gemarkeerd als niet geschikt voor Azure. De berekening van de grootte en kosten voor die server wordt niet uitgevoerd.

## <a name="whats-in-an-azure-vm-assessment"></a>Wat is een Azure VM-evaluatie?

Dit is what's opgenomen in een Azure VM-evaluatie:

**Eigenschap** | **Details**
--- | ---
**Doellocatie** | De locatie waarnaar u wilt migreren. De evaluatie ondersteunt momenteel deze Azure-doel regio's:<br/><br/> Australië-oost, Australië-zuidoost, Brazilië-zuid, Canada-centraal, Canada-oost, Centraal-India, centraal VS, China-oost, China-noord, Azië-oost, VS-Oost, VS-Oost 2, Duitsland-centraal, Duitsland-noordoost, Japan-Oost, Japan-West, Korea-centraal, Korea-Zuid, Noord-Centraal VS, Europa-noord, Zuid-Centraal VS, Zuidoost-Azië, India-zuid, UK-zuid, UK-west, US Gov-Arizona, US gov-Texas US gov-Virginia , West-Centraal VS, Europa-west, West-India, VS-West en VS-West 2.
**Doel opslag schijf (in grootte)** | Het type schijf dat moet worden gebruikt voor opslag in Azure. <br/><br/> Geef de doel opslag schijf op als Premium beheerd, Standard-SSD beheerd of Standard-HDD beheerd.
**Doel opslag schijf (grootte op basis van prestaties)** | Hiermee geeft u het type doel opslag schijf op als automatische, door een Standard-HDD beheerd of door Standard-SSD beheerd beheer.<br/><br/> **Automatisch**: de aanbevolen schijf is gebaseerd op de prestatie gegevens van de schijven, wat de IOPS en door Voer is.<br/><br/>**Premium of Standard**: de evaluatie beveelt een schijf-SKU aan binnen het geselecteerde opslag type.<br/><br/> Als u een VM met één exemplaar van 99,9% wilt maken, kunt u overwegen om Premium-beheerde schijven te gebruiken. Dit gebruik zorgt ervoor dat alle schijven in de evaluatie worden aanbevolen als Premium-beheerde schijven.<br/><br/> Azure Migrate ondersteunt alleen beheerde schijven voor migratie beoordeling.
**Azure Reserved VM Instances** | Hiermee worden [gereserveerde instanties](https://azure.microsoft.com/pricing/reserved-vm-instances/) opgegeven, zodat rekening wordt gehouden met kosten ramingen in de evaluatie.<br/><br/> Wanneer u gereserveerde instanties selecteert, wordt de korting (%) de eigenschappen van de uptime van de VM zijn niet van toepassing.<br/><br/> Azure Migrate ondersteunt momenteel alleen Azure Reserved VM Instances voor aanbiedingen met betalen per gebruik.
**Criteria voor het aanpassen van de grootte** | Wordt gebruikt voor het optimaliseren van de Azure-VM.<br/><br/> Gebruiken als is formaat of op basis van de prestaties.
**Prestatiegeschiedenis** | Wordt gebruikt met een grootte op basis van prestaties. Met de prestatie geschiedenis wordt de duur opgegeven die wordt gebruikt wanneer prestatie gegevens worden geëvalueerd.
**Percentiel gebruik** | Wordt gebruikt met een grootte op basis van prestaties. Percentiel gebruik geeft de percentiel waarde van het voor beeld van de prestaties die wordt gebruikt voor supportte.
**VM-reeks** | De Azure-VM-reeks die u wilt overwegen voor supportte. Als u bijvoorbeeld geen productieomgeving hebt die naar de A-serie van virtuele machines in Azure gaat, kunt u de A-serie uitsluiten van de reeks.
**Comfortfactor** | De buffer die wordt gebruikt tijdens de evaluatie. Deze wordt toegepast op de CPU-, RAM-, schijf-en netwerk gegevens voor Vm's. IT-accounts voor problemen zoals seizoen gebruik, korte prestatie geschiedenis en waarschijnlijk toename van toekomstig gebruik.<br/><br/> Zo resulteert een virtuele machine met 10 kern met 20% gebruik doorgaans in een virtuele machine met twee kernen. Met een comfort factor van 2,0 is het resultaat een virtuele machine met vier kernen.
**Aanbieding** | De [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) waarin u bent Inge schreven. De evaluatie maakt een schatting van de kosten voor die aanbieding.
**Valuta** | De facturerings valuta voor uw account.
**Korting (%)** | Alle abonnements kortingen die u boven op de Azure-aanbieding ontvangt. De standaardinstelling is 0%.
**VM tijd actief** | De duur in dagen per maand en uur per dag voor virtuele Azure-machines die niet continu worden uitgevoerd. Kosten ramingen zijn gebaseerd op die duur.<br/><br/> De standaard waarden zijn 31 dagen per maand en 24 uur per dag.
**Azure Hybrid Benefit** | Hiermee geeft u op of u Software Assurance hebt en in aanmerking komt voor [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Als de instelling de standaard waarde Ja heeft, worden de Azure-prijzen voor andere besturings systemen dan Windows in aanmerking genomen voor Windows-Vm's.
**EA-abonnement** | Hiermee geeft u op dat een Enterprise Overeenkomst (EA)-abonnement wordt gebruikt voor de schatting van de kosten. Houdt rekening met de korting die van toepassing is op het abonnement. <br/><br/> Wijzig de instellingen voor gereserveerde instanties, discount (%) en VM-uptime-eigenschappen met de standaard instellingen.


[Bekijk de aanbevolen procedures voor het](best-practices-assessment.md) maken van een evaluatie met Azure Migrate.

## <a name="calculate-readiness"></a>Gereedheid berekenen

Niet alle servers zijn geschikt om te worden uitgevoerd in Azure. Een Azure VM-evaluatie evalueert alle on-premises servers en wijst deze toe aan een gereedheids categorie.

- **Gereed voor Azure**: de server kan zonder wijzigingen worden gemigreerd naar Azure. Het wordt gestart in azure met volledige ondersteuning voor Azure.
- **Voorwaardelijk gereed voor Azure**: de server wordt mogelijk gestart in azure, maar heeft mogelijk geen volledige ondersteuning voor Azure. Azure biedt bijvoorbeeld geen ondersteuning voor een server waarop een oude versie van Windows Server wordt uitgevoerd. U moet voorzichtig zijn voordat u deze servers naar Azure migreert. Volg de richt lijnen voor herstel van de evaluatie om problemen met de gereedheids oplossing op te lossen.
- **Niet gereed voor Azure**: de server start niet in Azure. Als de schijf van een on-premises server bijvoorbeeld meer dan 64 TB opslaat, kan Azure de server niet hosten. Volg de richt lijnen voor herstel om het probleem op te lossen voordat de migratie wordt uitgevoerd.
- **Gereedheid onbekend**: Azure migrate kunt de gereedheid van de server niet bepalen vanwege onvoldoende meta gegevens.

Voor het berekenen van de gereedheid controleert de evaluatie de server eigenschappen en de instellingen van het besturings systeem in de volgende tabellen.

### <a name="server-properties"></a>Servereigenschappen

Voor een Azure VM-evaluatie controleert de evaluatie de volgende eigenschappen van een on-premises virtuele machine om te bepalen of deze kan worden uitgevoerd op virtuele Azure-machines.

Eigenschap | Details | Status van Azure-gereedheid
--- | --- | ---
**Opstarttype** | Azure ondersteunt Vm's met een opstart type BIOS, niet voor UEFI. | Voorwaardelijk gereed als het opstart type UEFI is
**Kernen** | Elke-server mag niet meer dan 128 kernen hebben. Dit is het maximum aantal dat door een Azure-VM wordt ondersteund.<br/><br/> Als er een prestatie geschiedenis beschikbaar is, worden de gebruikte kernen Azure Migrate beschouwd als vergelijking. Als de evaluatie-instellingen een comfort factor opgeven, wordt het aantal gebruikte kernen vermenigvuldigd met de comfort factor.<br/><br/> Als er geen prestatie geschiedenis is, gebruikt Azure Migrate de toegewezen kernen om de comfort factor toe te passen. | Gereed als het aantal kern geheugens binnen de limiet ligt
**RAM** | Elke server mag Maxi maal 3.892 GB RAM-geheugen hebben. Dit is de maximale grootte van een Azure M-serie Standard_M128m &nbsp; <sup>2</sup> VM ondersteunt. [Meer informatie](../virtual-machines/sizes.md).<br/><br/> Als de prestatie geschiedenis beschikbaar is, Azure Migrate beschouwt het gebruikte RAM-geheugen voor vergelijking. Als er een comfort factor is opgegeven, wordt het gebruikte RAM vermenigvuldigd met de comfort factor.<br/><br/> Als er geen geschiedenis is, wordt het toegewezen RAM-geheugen gebruikt voor het Toep assen van een comfort factor.<br/><br/> | Gereed als de hoeveelheid RAM-geheugen binnen de limiet ligt
**Opslagschijf** | De toegewezen grootte van een schijf mag niet groter zijn dan 32 TB. Hoewel Azure ondersteuning biedt voor 64-TB schijven met Azure Ultra-SSD schijven, controleert de evaluatie momenteel op 32 TB als de schijf grootte, omdat deze nog geen ondersteuning biedt voor Ultra-SSD. <br/><br/> Het aantal schijven dat is gekoppeld aan de server, met inbegrip van de besturingssysteem schijf, moet 65 of minder zijn. | Gereed als de schijf grootte en het-nummer binnen de limieten vallen
**Netwerken** | Aan een server mogen niet meer dan 32 netwerk interfaces (Nic's) zijn gekoppeld. | Gereed als het aantal Nic's binnen de limiet ligt

### <a name="guest-operating-system"></a>Gastbesturingssysteem

Voor een evaluatie van de Azure-VM, samen met het controleren van VM-eigenschappen, kijkt de evaluatie op het gast besturingssysteem van een server om te bepalen of het op Azure kan worden uitgevoerd.

> [!NOTE]
> Voor het afhandelen van de gast analyse voor VMware-Vm's gebruikt de evaluatie het besturings systeem dat is opgegeven voor de virtuele machine in vCenter Server. VCenter Server biedt echter niet de kernel-versie voor Linux VM-besturings systemen. Als u de versie wilt detecteren, moet u [toepassings detectie](./how-to-discover-applications.md)instellen. Het apparaat detecteert vervolgens versie-informatie met behulp van de gast referenties die u opgeeft bij het instellen van app-detectie.


De evaluatie maakt gebruik van de volgende logica om de Azure-gereedheid op basis van het besturings systeem te identificeren:

**Besturingssysteem** | **Details** | **Status van Azure-gereedheid**
--- | --- | ---
Windows Server 2016 en alle SP's | Azure biedt volledige ondersteuning. | Gereed voor Azure.
Windows Server 2012 R2 en alle SP's | Azure biedt volledige ondersteuning. | Gereed voor Azure.
Windows Server 2012 en alle SP's | Azure biedt volledige ondersteuning. | Gereed voor Azure.
Windows Server 2008 R2 met alle SPs | Azure biedt volledige ondersteuning.| Gereed voor Azure.
Windows Server 2008 (32-bits en 64-bits) | Azure biedt volledige ondersteuning. | Gereed voor Azure.
Windows Server 2003 en Windows Server 2003 R2 | Deze besturings systemen hebben hun end-of-support-datums door lopen en hebben een [aangepaste ondersteunings overeenkomst (CSA)](/troubleshoot/azure/virtual-machines/server-software-support) nodig voor ondersteuning in Azure. | Voorwaardelijk gereed voor Azure. Overweeg het besturings systeem te upgraden voordat u naar Azure migreert.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3,1 en MS-DOS | Deze besturings systemen hebben hun end-of-support-datums door gegeven. De server kan worden gestart in azure, maar Azure biedt geen ondersteuning voor het besturings systeem. | Voorwaardelijk gereed voor Azure. Het is raadzaam om het besturings systeem bij te werken voordat u naar Azure migreert.
Windows 7, Windows 8 en Windows 10 | Azure biedt alleen ondersteuning voor een [Visual Studio-abonnement.](../virtual-machines/windows/client-images.md) | Voorwaardelijk gereed voor Azure.
Windows 10 Pro | Azure biedt ondersteuning met [multitenant-hostingrechten.](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md) | Voorwaardelijk gereed voor Azure.
Windows Vista en Windows XP Professional | Deze besturings systemen hebben hun end-of-support-datums door gegeven. De server kan worden gestart in azure, maar Azure biedt geen ondersteuning voor het besturings systeem. | Voorwaardelijk gereed voor Azure. Het is raadzaam om het besturings systeem bij te werken voordat u naar Azure migreert.
Linux | Zie de [Linux-besturings systemen](../virtual-machines/linux/endorsed-distros.md) die door Azure worden goedgekeurd. Andere Linux-besturings systemen kunnen worden gestart in Azure. Het is echter raadzaam dat u het besturings systeem bijwerkt naar een officiële versie voordat u naar Azure migreert. | Gereed voor Azure als de versie goedgekeurd is.<br/><br/>Voorwaardelijk gereed als de versie niet wordt goedgekeurd.
Andere besturings systemen, zoals Oracle Solaris, Apple macOS en FreeBSD | Deze besturingssystemen worden niet door Azure goedgekeurd. De server kan worden gestart in azure, maar Azure biedt geen ondersteuning voor het besturings systeem. | Voorwaardelijk gereed voor Azure. U wordt aangeraden een ondersteund besturings systeem te installeren voordat u naar Azure migreert.  
Besturingssysteem dat is opgegeven als **Anders** in vCenter Server | Azure Migrate kan het besturings systeem in dit geval niet identificeren. | Gereedheid onbekend. Zorg ervoor dat Azure ondersteuning biedt voor het besturings systeem dat wordt uitgevoerd in de virtuele machine.
32-bits besturingssystemen | De server kan worden gestart in azure, maar Azure biedt mogelijk geen volledige ondersteuning. | Voorwaardelijk gereed voor Azure. U kunt een upgrade uitvoeren naar een 64-bits besturings systeem voordat u naar Azure migreert.

## <a name="calculating-sizing"></a>Grootte berekenen

Nadat de server is gemarkeerd als gereed voor Azure, maakt de evaluatie aanbeveling in de Azure VM-evaluatie. Deze aanbevelingen identificeren de Azure VM en schijf-SKU. Het aanpassen van de grootte is afhankelijk van het feit of u gebruikmaakt van de on-premises grootte of op basis van de prestaties.

### <a name="calculate-sizing-as-is-on-premises"></a>Grootte berekenen (as-on-premises)

 Als u de on-premises grootte op locatie gebruikt, wordt de prestatie geschiedenis van de Vm's en schijven in de Azure VM-evaluatie niet door de evaluatie beschouwd.

- **Berekenings grootte**: de evaluatie wijst een Azure VM-SKU toe op basis van de grootte die lokaal is toegewezen.
- **Grootte van opslag en schijf**: de evaluatie bekijkt het opslag type dat is opgegeven in de eigenschappen van de evaluatie en raadt het juiste schijf type aan. Mogelijke opslag typen zijn Standard-HDD, Standard-SSD en Premium. Het standaard opslag type is Premium.
- **Netwerk grootte**: de evaluatie beschouwt de netwerk adapter op de on-premises server.

### <a name="calculate-sizing-performance-based"></a>Grootte berekenen (op basis van prestaties)

Als u de grootte van een virtuele machine op basis van prestaties in een Azure-VM-evaluatie gebruikt, worden de volgende opties voor het aanpassen van het formaat aanbevolen:

- De evaluatie houdt rekening met de prestatie geschiedenis van de server om de VM-grootte en het schijf type in azure te identificeren.
- Als u servers importeert met behulp van een CSV-bestand, worden de waarden gebruikt die u opgeeft. Deze methode is vooral nuttig als u de on-premises server overbezett, gebruik laag is en u de Azure-VM wilt optimaliseren om kosten te besparen.
- Als u de prestatie gegevens niet wilt gebruiken, stelt u de grootte van de insluitings criteria in op on-premises, zoals beschreven in de vorige sectie.

#### <a name="calculate-storage-sizing"></a>Opslag grootte berekenen

Voor opslag grootte in een Azure VM-evaluatie wordt Azure Migrate elke schijf die aan de server is gekoppeld, aan een Azure-schijf toewijzen. De grootte werkt als volgt:

1. Beoordeling voegt de IOPS voor lezen en schrijven van een schijf toe voor het ophalen van het totale aantal IOPS dat is vereist. Op dezelfde manier worden de doorvoer-en schrijf waarden toegevoegd om de totale door Voer van elke schijf te verkrijgen. In het geval van evaluaties op basis van een import hebt u de mogelijkheid om het totale aantal IOPS, de totale door Voer en het totaal aantal op te geven. van schijven in het geïmporteerde bestand zonder de afzonderlijke schijf instellingen op te geven. Als u dit doet, wordt de grootte van de afzonderlijke schijf overgeslagen en worden de opgegeven gegevens direct gebruikt voor het berekenen van de grootte en selecteert u een geschikte VM-SKU.

1. Als u het opslag type hebt opgegeven als automatisch, is het geselecteerde type gebaseerd op de ingangs waarden van de werkelijke IOPS en door voer. De evaluatie bepaalt of de schijf moet worden toegewezen aan een Standard-HDD, Standard-SSD of Premium-schijf in Azure. Als het opslag type is ingesteld op een van deze schijf typen, probeert de evaluatie een schijf-SKU te vinden binnen het geselecteerde opslag type.
1. Schijven worden als volgt geselecteerd:
    - Als de evaluatie geen schijf met de vereiste IOPS en door Voer kan vinden, wordt de server gemarkeerd als niet geschikt voor Azure.
    - Als de evaluatie een set geschikte schijven vindt, worden de schijven geselecteerd die de locatie ondersteunen die is opgegeven in de instellingen voor evaluatie.
    - Als er meerdere in aanmerking komende schijven zijn, selecteert de evaluatie de schijf met de laagste kosten.
    - Als de prestatie gegevens voor schijven niet beschikbaar zijn, wordt de grootte van de configuratie schijf gebruikt om een Standard-SSD schijf in azure te vinden.

#### <a name="calculate-network-sizing"></a>Netwerk grootte berekenen

Voor een evaluatie van de Azure-VM probeert de evaluatie een Azure VM te vinden die het aantal en de vereiste prestaties van netwerk adapters ondersteunt die zijn gekoppeld aan de on-premises server.

- Om de effectief netwerk prestaties van de on-premises server te verkrijgen, aggregeert de evaluatie de snelheid van de gegevens overdracht van de server (netwerk uit) in alle netwerk adapters. Vervolgens wordt de comfort factor toegepast. De resulterende waarde wordt gebruikt om een Azure VM te vinden die de vereiste netwerk prestaties kan ondersteunen.
- Naast de netwerk prestaties is de evaluatie ook van oordeel of de virtuele machine van Azure het vereiste aantal netwerk adapters kan ondersteunen.
- Als de netwerk prestatie gegevens niet beschikbaar zijn, beschouwt de evaluatie alleen het aantal netwerk adapters voor VM-grootte.

#### <a name="calculate-compute-sizing"></a>Reken grootte berekenen

Nadat de opslag-en netwerk vereisten zijn berekend, beschouwt de evaluatie de vereisten voor CPU-en RAM-geheugen om een geschikte VM-grootte in azure te vinden.

- Azure Migrate zoekt naar de effectief gebruikte kern geheugens en RAM om een geschikte Azure VM-grootte te vinden.
- Als er geen geschikte grootte wordt gevonden, is de server gemarkeerd als niet geschikt voor Azure.
- Als er een geschikte grootte wordt gevonden, past Azure Migrate de opslag-en netwerk berekeningen toe. Vervolgens worden de locatie-en prijs categorie-instellingen voor de laatste aanbeveling van de VM-grootte toegepast.
- Als er meer Azure VM-grootten in aanmerking komen, wordt de grootte met de laagste kosten aanbevolen.

## <a name="confidence-ratings-performance-based"></a>Betrouwbaarheids classificaties (op basis van prestaties)

Elke Azure VM-evaluatie op basis van prestaties in Azure Migrate is gekoppeld aan een betrouwbaarheids classificatie. De classificatie varieert van één (laagste) tot vijf (hoogste) sterren. De betrouwbaarheids classificatie helpt u bij het schatten van de betrouw baarheid van de aanbevelingen voor de grootte die Azure Migrate biedt.

- De betrouwbaarheids classificatie wordt toegewezen aan een evaluatie. De classificatie is gebaseerd op de beschik baarheid van gegevens punten die nodig zijn om de evaluatie te berekenen.
- Voor een hoge grootte moet de volgende beoordeling worden uitgevoerd:
    - De gebruiks gegevens voor de CPU en het RAM-geheugen.
    - De schijf-IOPS en doorvoer gegevens voor elke schijf die aan de server is gekoppeld.
    - De netwerk-I/O voor het afhandelen van de grootte van de prestaties voor elke netwerk adapter die is gekoppeld aan een server.

Als een van deze gebruiks nummers niet beschikbaar is, zijn de aanbevelingen voor de grootte mogelijk onbetrouwbaar.

> [!NOTE]
> Vertrouwens classificaties zijn niet toegewezen voor servers die worden geëvalueerd met een geïmporteerd CSV-bestand. Classificaties zijn ook niet van toepassing op de on-premises evaluatie.

### <a name="ratings"></a>Waarderingen

Deze tabel bevat de beoordelings betrouwbaarheids classificaties die afhankelijk zijn van het percentage beschik bare gegevens punten:

   **Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
   --- | ---
   0-20% | 1 ster
   21-40% | 2 sterren
   41-60% | 3 sterren
   61-80% | 4 sterren
   81-100% | 5 sterren

### <a name="low-confidence-ratings"></a>Classificaties met lage betrouw baarheid

Hier volgen enkele redenen waarom een evaluatie een lage betrouwbaarheids classificatie kan krijgen:

- U hebt uw omgeving niet geprofileerd gedurende de periode waarvoor u de evaluatie maakt. Als u bijvoorbeeld de beoordeling met de prestatie duur hebt ingesteld op één dag, moet u ten minste één dag wachten nadat u de detectie hebt gestart voor alle gegevens punten die u wilt verzamelen.
- Bij de evaluatie kunnen de prestatiegegevens voor sommige of alle servers in de evaluatieperiode niet worden verzameld. Voor een hoge betrouwbaarheids classificatie moet u het volgende doen: 
    - Servers zijn ingeschakeld voor de duur van de evaluatie
    - Uitgaande verbindingen op poort 443 zijn toegestaan
    - Voor Hyper-V-servers is dynamisch geheugen ingeschakeld 
    
    Bereken de evaluatie opnieuw om de meest recente wijzigingen in de betrouwbaarheidsclassificatie weer te geven.

- Sommige servers zijn gemaakt tijdens de periode waarin de evaluatie is berekend. Stel dat u een evaluatie hebt gemaakt voor de prestatie geschiedenis van de afgelopen maand, maar dat sommige servers slechts een week geleden zijn gemaakt. In dit geval zijn de prestatie gegevens voor de nieuwe servers niet beschikbaar voor de hele duur en is de betrouwbaarheids classificatie laag.

> [!NOTE]
> Als de betrouwbaarheids classificatie van een evaluatie van minder dan vijf sterren is, raden we u aan om ten minste een dag te wachten op het apparaat om de omgeving te profileren en vervolgens de evaluatie opnieuw te berekenen. Anders is de op prestaties gebaseerde grootte mogelijk onbetrouwbaar. In dat geval raden wij u aan de evaluatie over te scha kelen naar de on-premises grootte.

## <a name="calculate-monthly-costs"></a>Maandelijkse kosten berekenen

Nadat de aanbevelingen voor de grootte zijn voltooid, berekent een Azure VM-evaluatie in Azure Migrate reken-en opslag kosten voor na de migratie.

- **Reken kosten**: Azure migrate gebruikt de aanbevolen grootte van de Azure-VM en de API voor Azure-facturering om de maandelijkse kosten voor de server te berekenen.

    De berekening houdt rekening met het volgende:
    - Besturingssysteem
    - Software Assurance
    - Gereserveerde instanties
    - VM tijd actief
    - Locatie
    - Valuta-instellingen

    In de evaluatie worden de kosten voor alle servers geaggregeerd om de totale maandelijkse reken kosten te berekenen.

- **Opslag kosten**: de maandelijkse opslag kosten voor een server worden berekend door de maandelijkse kosten van alle schijven die aan de-server zijn gekoppeld, samen te voegen.

    Beoordeling berekent de totale maandelijkse opslag kosten door het samen voegen van de opslag kosten van alle servers. Op dit moment worden voor de berekening geen aanbiedingen in de evaluatie-instellingen beschouwd.

De kosten worden weer gegeven in de valuta die is opgegeven in de instellingen voor evaluatie.

## <a name="next-steps"></a>Volgende stappen

[Bekijk](best-practices-assessment.md) de aanbevolen procedures voor het maken van evaluaties. 

- Meer informatie over het uitvoeren van analyses voor servers die in [VMware](./tutorial-discover-vmware.md) -en [Hyper-V- ](./tutorial-discover-hyper-v.md) omgeving en [fysieke servers](./tutorial-discover-physical.md)worden uitgevoerd.
- Meer informatie over het beoordelen van servers [die zijn geïmporteerd met een CSV-bestand](./tutorial-discover-import.md).
- Meer informatie over het instellen van de [visualisatie van afhankelijkheden](concepts-dependency-visualization.md).