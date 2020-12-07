---
title: Aanbevolen procedures evalueren in Azure Migrate server-evaluatie
description: Tips voor het maken van evaluaties met Azure Migrate server-evaluatie.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: c1fff5b5b7f6450ad8d1977e55a1f6b255f3d668
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754314"
---
# <a name="best-practices-for-creating-assessments"></a>Aanbevolen procedures voor het maken van evaluaties

[Azure Migrate](./migrate-services-overview.md) biedt een hub aan hulpprogramma's waarmee u apps, infrastructuur en workloads op Microsoft Azure kunt detecteren, evalueren en migreren. De hub bevat Azure Migrate-hulpprogramma's en externe aanbiedingen van onafhankelijke softwareleveranciers (ISV’s).

Dit artikel bevat een overzicht van de aanbevolen procedures voor het maken van evaluaties met behulp van het hulp programma Azure Migrate server Assessment.

## <a name="about-assessments"></a>Over evaluaties

Evaluaties die u maakt met Azure Migrate server-evaluatie zijn een tijdgebonden moment opname van gegevens. Er zijn twee soorten evaluaties die u kunt maken met behulp van Azure Migrate: Server evaluatie:

**Evaluatietype** | **Details**
--- | --- 
**Azure VM** | Evaluaties om uw on-premises servers te migreren naar virtuele Azure-machine. <br/><br/> U kunt uw on-premises [VMware-VM's](how-to-set-up-appliance-vmware.md), [Hyper-V-VM's](how-to-set-up-appliance-hyper-v.md) en [fysieke servers](how-to-set-up-appliance-physical.md) evalueren voor migratie naar Azure met dit evaluatietype. [Meer informatie](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Evaluaties om uw on-premises servers te migreren naar [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> U kunt uw on-premises [VMware-VM’s](how-to-set-up-appliance-vmware.md) evalueren voor migratie naar Azure VMware Solution (AVS) met dit evaluatietype. [Meer informatie](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>Criteria voor het aanpassen van de grootte
Server Assessment biedt twee opties voor criteria voor het aanpassen van de grootte:

**Criteria voor het aanpassen van de grootte** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties die aanbevelingen doen op basis van verzamelde prestatiegegevens | **Azure-VM-evaluatie**: Aanbeveling voor VM-grootte is gebaseerd op verbruiksgegevens voor CPU en geheugen.<br/><br/> Aanbeveling voor schijftype (standaard HDD/SSD of premium-beheerde schijven) is gebaseerd op de IOPS en doorvoer van de on-premises schijven.<br/><br/> **AVS-evaluatie (Azure VMware Solution)** : Aanbeveling voor AVS-knooppunten is gebaseerd op verbruiksgegevens voor CPU en geheugen.
**As-is on-premises** | Evaluaties die geen gebruik maken van prestatiegegevens voor aanbevelingen. | **Azure-VM-evaluatie**: Aanbeveling voor VM-grootte is gebaseerd op de on-premises VM-grootte<br/><br> De aanbeveling voor het schijftype is gebaseerd op de instelling die u selecteert bij het opslagtype voor de evaluatie.<br/><br/> **AVS-evaluatie (Azure VMware Solution)**: Aanbeveling voor AVS-knooppunten is gebaseerd op de on-premises VM-grootte.

#### <a name="example"></a>Voorbeeld
Een voor beeld: als u een on-premises VM hebt met vier kernen van 20% gebruik en geheugen van 8 GB met 10% gebruik, is de Azure VM-evaluatie als volgt:

- **Evaluatie op basis van prestaties**:
    - Identificeert effectief kernen en geheugen op basis van core (4 x 0,20 = 0,8) en geheugen (8 GB x 0,10 = 0,8) gebruik.
    - Past de comfort factor toe die is opgegeven in de eigenschappen van de beoordeling (stel 1,3 x in) om de waarden op te halen die moeten worden gebruikt voor het aanpassen van de grootte. 
    - Hiermee wordt de dichtstbijzijnde VM-grootte in azure aanbevolen die ondersteuning biedt voor meer dan ~ 1,04 kernen (0,8 x 1,3) en ~ 1,04 GB (0,8 x 1,3) geheugen.

- **As-is (zoals on-premises) evaluatie**:
    -  Raadt een VM aan met vier kernen; 8 GB geheugen.


## <a name="best-practices-for-creating-assessments"></a>Aanbevolen procedures voor het maken van evaluaties

Het Azure Migrate apparaat doorlopend uw on-premises omgeving profileert en verstuurt meta gegevens en prestatie gegevens naar Azure. Volg deze aanbevolen procedures voor evaluaties van servers die zijn gedetecteerd met een apparaat:

- **Evaluaties maken als-is**: u kunt de evaluatie van de as-out onmiddellijk maken zodra uw computers in de Azure migrate Portal worden weer gegeven.
- Een **evaluatie op basis van prestaties maken**: na het instellen van de detectie wordt u aangeraden minstens een dag te wachten voordat u een evaluatie op basis van prestaties uitvoert:
    - Het verzamelen van prestatie gegevens kost tijd. Als u op minstens een dag wacht, zorgt u ervoor dat er voldoende prestatie gegevens punten zijn voordat u de evaluatie uitvoert.
    - Wanneer u evaluaties op basis van prestaties uitvoert, moet u ervoor zorgen dat u uw omgeving voor de evaluatie duur profileert. Als u bijvoorbeeld een beoordeling maakt waarbij de duur van de prestatie is ingesteld op één week, moet u na het starten van de detectie worden gewacht totdat alle gegevens punten zijn verzameld. Als u dit niet doet, krijgt de evaluatie van de beoordeling geen vijf sterren.
- **Evaluaties opnieuw berekenen**: aangezien beoordelingen moment opnamen zijn, worden ze niet automatisch bijgewerkt met de meest recente gegevens. Als u een evaluatie met de meest recente gegevens wilt bijwerken, moet u deze opnieuw berekenen.

Volg deze aanbevolen procedures voor evaluaties van servers die in Azure Migrate worden geïmporteerd via. CSV-bestand:

- **Evaluaties maken als-is**: u kunt de evaluatie van de as-out onmiddellijk maken zodra uw computers in de Azure migrate Portal worden weer gegeven.
- Een **evaluatie op basis van prestaties maken**: Hiermee krijgt u een betere schatting van de kosten, met name als u lokale server capaciteit hebt overingericht. De nauw keurigheid van de analyse op basis van prestaties is echter afhankelijk van de prestatie gegevens die u voor de servers hebt opgegeven. 
- **Evaluaties opnieuw berekenen**: aangezien beoordelingen moment opnamen zijn, worden ze niet automatisch bijgewerkt met de meest recente gegevens. Als u een evaluatie met de meest recente geïmporteerde gegevens wilt bijwerken, moet u deze opnieuw berekenen.
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>FTT-formaat parameters voor AVS-evaluaties

De opslag-engine die in AVS wordt gebruikt, is vSAN. vSAN-opslagbeleid definieert de opslagvereisten voor virtuele machines. Deze beleidsregels garanderen het vereiste serviceniveau voor VM’s, omdat ze bepalen hoe opslag wordt toegewezen aan de VM. Dit zijn de beschikbare FTT-RAID-combinaties: 

**FTT (te tolereren fouten)** | **RAID-configuratie** | **Minimumaantal vereiste hosts** | **Overwegingen voor de grootte**
--- | --- | --- | --- 
1 | RAID-1 (spiegelen) | 3 | Een VM van 100 GB verbruikt 200 GB.
1 | RAID-5 (verwijdering coderen) | 4 | Een VM van 100 GB verbruikt 133,33 GB
2 | RAID-1 (spiegelen) | 5 | Een VM van 100 GB verbruikt 300 GB.
2 | RAID-6 (verwijdering coderen) | 6 | Een VM van 100 GB verbruikt 150 GB.
3 | RAID-1 (spiegelen) | 7 | Een VM van 100 GB verbruikt 400 GB.


## <a name="best-practices-for-confidence-ratings"></a>Aanbevolen procedures voor betrouwbaarheids classificaties

Wanneer u evaluaties op basis van prestaties uitvoert, wordt een betrouwbaarheids classificatie van 1 ster (laagste) tot 5 sterren (hoogst) toegekend aan de evaluatie. Vertrouwens classificaties effectief gebruiken:
- Voor de analyse van Azure Migrate server zijn de gebruiks gegevens voor de CPU/het geheugen van de virtuele machine nodig.
- Voor elke schijf die aan de on-premises VM is gekoppeld, is de IOPS/doorvoer gegevens lezen/schrijven vereist.
- Voor elke netwerk adapter die aan de VM is gekoppeld, is het netwerk in/uit-gegevens nodig.

Afhankelijk van het percentage beschik bare gegevens punten voor de geselecteerde duur, wordt de betrouwbaarheids classificatie voor een evaluatie gegeven, zoals beschreven in de volgende tabel.

   **Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
   --- | ---
   0%-20% | 1 ster
   21%-40% | 2 sterren
   41%-60% | 3 sterren
   61%-80% | 4 sterren
   81%-100% | 5 sterren


## <a name="common-assessment-issues"></a>Algemene evaluatie problemen

Hier vindt u informatie over het oplossen van enkele veelvoorkomende omgevings problemen die van invloed zijn op evaluaties.

###  <a name="out-of-sync-assessments"></a>Evaluaties buiten de synchronisatie

Als u computers toevoegt aan of verwijdert uit een groep nadat u een evaluatie hebt gemaakt, wordt de door u gemaakte evaluatie gemarkeerd als **out-of-Sync**. Voer de evaluatie opnieuw uit (**herberekening**) om de groeps wijzigingen weer te geven.

### <a name="outdated-assessments"></a>Verouderde evaluaties

Als er on-premises wijzigingen zijn aangebracht aan virtuele machines die zich in een groep bevinden die is geëvalueerd, wordt de evaluatie gemarkeerd als **verouderd**. Een evaluatie kan worden gemarkeerd als verouderd vanwege een of meer wijzigingen in de volgende eigenschappen:

- Aantal processor kernen
- Toegewezen geheugen
- Opstart type of-firmware
- Naam van besturings systeem, versie en architectuur
- Aantal schijven
- Aantal netwerk adapters
- Wijziging in de schijf grootte (GB toegewezen)
- Update van NIC-eigenschappen. Voor beeld: Mac-adres wijzigingen, IP-adres toevoegen etc.

Voer de evaluatie opnieuw uit (opnieuw **berekenen**) om de wijzigingen weer te geven.

### <a name="low-confidence-rating"></a>Beoordeling met lage betrouw baarheid

Een evaluatie heeft mogelijk niet alle gegevens punten om een aantal redenen:

- U hebt uw omgeving niet geprofileerd gedurende de periode waarvoor u de evaluatie maakt. Als u bijvoorbeeld een *evaluatie op basis van prestaties* maakt waarbij de duur van de prestaties is ingesteld op een week, moet u een periode van minstens een week wachten nadat u de detectie hebt gestart voor alle gegevens punten die u wilt verzamelen. U kunt altijd op **herberekenen** klikken om de meest recente beoordeling van het vertrouwen te bekijken. De vertrouwens classificatie is alleen van toepassing wanneer u een evaluatie *op basis van prestaties* maakt.

- Er zijn enkele VM's uitgeschakeld geweest in de periode waarover de evaluatie wordt berekend. Als bepaalde VM's gedurende een tijdje uitgeschakeld zijn geweest, kunnen er voor deze periode geen prestatiegegevens worden verzameld.

- Er zijn enkele VM’s gemaakt nadat detectie in Server-evaluatie al was gestart. Als u bijvoorbeeld een evaluatie maakt voor de prestatiegeschiedenis van de laatste maand, maar er een week geleden enkele VM's in de omgeving zijn gemaakt. In dit geval zijn er voor de hele periode geen prestatiegegevens van de nieuwe VM’s beschikbaar, waardoor de betrouwbaarheidsclassificatie laag is.

### <a name="migration-tool-guidance-for-avs-assessments"></a>Richt lijnen voor migratie Hulpprogramma's voor AVS-evaluaties

In het Azure-gereedheidsrapport voor AVS-evaluatie (Azure VMware Solution) ziet u de volgende voorgestelde hulpprogramma’s: 
- **VMware HCX of ENTER prise**: voor VMware-machines is de VMware Hybrid Cloud extension (HCX)-oplossing het aanbevolen migratie programma voor het migreren van uw on-premises werk belasting naar uw Azure VMware-oplossing (AVS) Private Cloud. [Meer informatie](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Onbekend**: Voor machines die zijn geïmporteerd via een CSV-bestand, is het standaardhulpprogramma voor migratie onbekend. Voor VMware-machines is het echter raadzaam de VMware Hybrid Cloud extension (HCX)-oplossing te gebruiken.


## <a name="next-steps"></a>Volgende stappen

- [Meer informatie](concepts-assessment-calculation.md) over hoe beoordelingen worden berekend.
- [Meer informatie](how-to-modify-assessment.md) over het aanpassen van een evaluatie.
