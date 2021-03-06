---
title: Beoordelingen aanpassen voor Azure Migrate | Microsoft Docs
description: Hierin wordt beschreven hoe u de evaluaties aanpast die zijn gemaakt met Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/15/2019
ms.openlocfilehash: 9bda4750f6b4340399bbbe070954dd23930b1ae1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785203"
---
# <a name="customize-an-assessment"></a>Een beoordeling aanpassen

In dit artikel wordt beschreven hoe u beoordelingen kunt aanpassen die zijn gemaakt door Azure Migrate hulp programma voor detectie en evaluatie.

[Azure Migrate](migrate-services-overview.md) biedt een centrale hub om de detectie, evaluatie en migratie van on-premises apps en workloads en openbare en privécloud-VM's bij te houden via Azure. De hub biedt Azure Migrate-hulpprogramma's voor evaluatie en migratie, evenals externe onafhankelijke hulpprogramma's van onafhankelijke softwareverkopers (ISV's).

U kunt het hulp programma voor detectie en bepaling van Azure Migrate gebruiken om evaluaties te maken voor on-premises virtuele VMware-machines en virtuele Hyper-V-machines, in voor bereiding op de migratie naar Azure. Het hulp programma detectie en evaluatie evalueert on-premises servers voor migratie naar Azure IaaS virtuele machines en de Azure VMware-oplossing (AVS). 

## <a name="about-assessments"></a>Over evaluaties

Evaluaties die u met het hulp programma detectie en evaluatie maakt, zijn een tijdgebonden moment opname van gegevens. Er zijn twee soorten evaluaties die u kunt maken met behulp van Azure Migrate: detectie en evaluatie.

**Evaluatietype** | **Details**
--- | --- 
**Azure VM** | Evaluaties om uw on-premises servers te migreren naar virtuele Azure-machine. <br/><br/> U kunt uw on-premises [VMware-VM's](how-to-set-up-appliance-vmware.md), [Hyper-V-VM's](how-to-set-up-appliance-hyper-v.md) en [fysieke servers](how-to-set-up-appliance-physical.md) evalueren voor migratie naar Azure met dit evaluatietype. (concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Evaluaties om uw on-premises servers te migreren naar [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> U kunt uw on-premises [VMware-VM’s](how-to-set-up-appliance-vmware.md) evalueren voor migratie naar Azure VMware Solution (AVS) met dit evaluatietype. [Meer informatie](concepts-azure-vmware-solution-assessment-calculation.md)

Opties voor het aanpassen van de grootte in Azure Migrate-evaluaties:

**Criteria voor het aanpassen van de grootte** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties die aanbevelingen doen op basis van verzamelde prestatiegegevens | **Azure-VM-evaluatie**: Aanbeveling voor VM-grootte is gebaseerd op verbruiksgegevens voor CPU en geheugen.<br/><br/> Aanbeveling voor schijftype (standaard HDD/SSD of premium-beheerde schijven) is gebaseerd op de IOPS en doorvoer van de on-premises schijven.<br/><br/>**Azure SQL-evaluatie**: de Azure SQL-configuratie is gebaseerd op prestatie gegevens van SQL-instanties en data bases, waaronder: CPU-gebruik, geheugen gebruik, IOPS (gegevens en logboek bestanden), door Voer en latentie van i/o-bewerkingen<br/><br/>**AVS-evaluatie (Azure VMware Solution)** : Aanbeveling voor AVS-knooppunten is gebaseerd op verbruiksgegevens voor CPU en geheugen.
**As-is on-premises** | Evaluaties die geen gebruik maken van prestatiegegevens voor aanbevelingen. | **Azure-VM-evaluatie**: Aanbeveling voor VM-grootte is gebaseerd op de on-premises VM-grootte<br/><br> De aanbeveling voor het schijftype is gebaseerd op de instelling die u selecteert bij het opslagtype voor de evaluatie.<br/><br/> **AVS-evaluatie (Azure VMware Solution)**: Aanbeveling voor AVS-knooppunten is gebaseerd op de on-premises VM-grootte.

## <a name="how-is-an-assessment-done"></a>Hoe wordt een evaluatie uitgevoerd?

Een evaluatie in Azure Migrate detectie en evaluatie heeft drie fasen. De evaluatie begint met een geschiktheids analyse, gevolgd door het aanpassen van de grootte, en ten slotte een maandelijkse schatting van de kosten. Een machine gaat alleen door naar een latere fase als deze door de voorgaande fase is gekomen. Als een machine bijvoorbeeld de Azure-geschiktheids controle niet kan uitvoeren, wordt deze gemarkeerd als ongeschikt voor Azure en wordt de grootte en de kosten voor de berekening niet gewijzigd. [Meer informatie.](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>Wat is een Azure VM-evaluatie?

**Eigenschap** | **Details**
--- | ---
**Doellocatie** | De Azure-locatie waarnaar u wilt migreren.<br/> Azure VM-evaluatie ondersteunt momenteel deze doel regio's: Australië-oost, Australië-zuidoost, Brazilië-zuid, Canada-centraal, Canada-oost, Centraal-India, centraal VS, China-oost, China-noord, Azië-oost, VS-Oost, Oost-VS2, Duitsland-centraal, Duitsland-noordoost, Japan-Oost, Japan-West, Korea-centraal, Korea-Zuid, Noord-Centraal VS, Europa-Noord, Zuid-Centraal VS, Zuidoost-Azië, India-zuid, UK-zuid, UK-west US gov-Arizona , US Gov-Texas, US Gov-Virginia, VS-West-Centraal, Europa-west, West-India, VS-West en West-VS2.
**Opslagtype** | U kunt deze eigenschap gebruiken om het type schijven op te geven dat u wilt verplaatsen in Azure.<br/><br/> Voor de optie voor on-premises grootte kunt u het type doel opslag opgeven als Premium-beheerde schijven, Standard-SSD-beheerde schijven of Standard-HDD-beheerde schijven. Voor een schaal op basis van prestaties kunt u het type doel schijf opgeven als automatische, Premium beheerde schijven, Standard-HDD-beheerde schijven of Standard-SSD-beheerde schijven.<br/><br/> Wanneer u het opslag type opgeeft als automatisch, wordt de aanbevolen schijf uitgevoerd op basis van de prestatie gegevens van de schijven (IOPS en door Voer). Als u het opslag type opgeeft als Premium/standaard, wordt een schijf-SKU aanbevolen binnen het geselecteerde opslag type. Als u een VM-SLA met één exemplaar van 99,9% wilt uitvoeren, kunt u het opslag type opgeven als Premium-beheerde schijven. Dit zorgt ervoor dat alle schijven in de evaluatie worden aanbevolen als Premium-beheerde schijven. Azure
**Gereserveerde instanties (RI)** | Met deze eigenschap kunt u opgeven of u [gereserveerde instanties](https://azure.microsoft.com/pricing/reserved-vm-instances/) in azure hebt, kosten ramingen in de beoordeling worden uitgevoerd in RI-kortingen. Gereserveerde instanties worden momenteel alleen ondersteund voor de aanbieding betalen naar gebruik in Azure Migrate.
**Criterium voor het aanpassen van de grootte** | Het criterium dat moet worden gebruikt om virtuele machines met een juiste grootte te gebruiken voor Azure. U kunt de grootte van de virtuele machines aanpassen op *basis van de prestaties* , zonder rekening te *houden* met de prestatie geschiedenis.
**Prestatiegeschiedenis** | De duur die u moet overwegen om de prestatie gegevens van machines te evalueren. Deze eigenschap is alleen van toepassing als het formaat van het criterium *op basis van prestaties* is.
**Percentiel gebruik** | De percentielwaarde van de prestatievoorbeeldset waarmee rekening moet worden gehouden voor een juiste groottebepaling. Deze eigenschap is alleen van toepassing wanneer de grootte van het formaat *op basis van prestaties* is.
**VM-reeks** |     U kunt de VM-reeks opgeven die u wilt overwegen voor de juiste grootte. Als u bijvoorbeeld een productie omgeving hebt die u niet van plan bent om te migreren naar A-serie Vm's in azure, kunt u een-serie uitsluiten van de lijst of reeks en wordt de rechter grootte alleen uitgevoerd in de geselecteerde reeks.
**Comfortfactor** | Bij de evaluatie van de Azure-VM wordt een buffer (comfort factor) beschouwd tijdens de beoordeling. Deze buffer wordt toegepast boven op de gegevens over machinegebruik voor VM's (CPU, geheugen, schijf en netwerk). De comfortfactor houdt rekening met factoren zoals seizoensgebonden gebruik, een korte prestatiegeschiedenis en een mogelijke gebruikstoename in de toekomst.<br/><br/> Een VM met 10 kernen en een gebruik van 20% komt bijvoorbeeld gewoonlijk overeen met een VM met 2 kernen. Met een comfortfactor van 2,0x is het resultaat echter een VM met 4 kernen.
**Aanbieding** | De [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) waarop u bent geregistreerd. Azure Migrate maakt dienovereenkomstig een schatting van de kosten.
**Valuta** | Factureringsvaluta.
**Korting (%)** | Een abonnement-specifieke korting die u bovenop de Azure-aanbieding ontvangt.<br/> De standaardinstelling is 0%.
**VM tijd actief** | Als uw Vm's niet 24x7 worden uitgevoerd in azure, kunt u de duur (aantal dagen per maand en het aantal uren per dag) opgeven waarvoor ze worden uitgevoerd en de kosten ramingen dienovereenkomstig worden uitgevoerd.<br/> De standaardwaarde is 31 dagen per maand en 24 uur per dag.
**Azure Hybrid Benefit** | Geef op of u Software Assurance hebt en in aanmerking komt voor [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Als deze optie is ingesteld op Ja, worden niet-Windows Azure-prijzen voor Windows-VM's gehanteerd. Standaard is Ja.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Wat is een Azure VMware-oplossing (AVS)-evaluatie?

Dit is what's opgenomen in een AVS-evaluatie:


| **Eigenschap** | **Details** |
| - | - |
| **Doellocatie** | Hiermee geeft u de automatische AVS-Cloud locatie op waarnaar u wilt migreren.<br/><br/> AVS-evaluatie ondersteunt momenteel deze doel regio's: VS-Oost, Europa-west, VS-West. |
| **Opslagtype** | Hiermee geeft u de opslag engine moet worden gebruikt in AVS.<br/><br/> AVS-evaluaties bieden alleen ondersteuning voor vSAN als een standaard type opslag. |
**Gereserveerde instanties (RIs)** | Met deze eigenschap kunt u gereserveerde instanties in AVS opgeven. RIs wordt momenteel niet ondersteund voor AVS-knoop punten. |
**Knooppunt type** | Hiermee geeft u het [AVS-knooppunt type](../azure-vmware/concepts-private-clouds-clusters.md) op dat wordt gebruikt om de on-premises vm's toe te wijzen. Houd er rekening mee dat het standaard knooppunt type AV36 is. <br/><br/> Azure Migrate wordt een vereist aantal knoop punten aanbevolen voor de virtuele machines die moeten worden gemigreerd naar AVS. |
**FTT-instelling, RAID-niveau** | Hiermee geeft u de toepasselijke fout op voor verdragen en RAID-combi Naties. De geselecteerde FTT-optie in combi natie met de on-premises VM-schijf vereiste bepaalt de totale vSAN-opslag die is vereist in AVS. |
**Criterium voor het aanpassen van de grootte** | Hiermee stelt u de criteria in die moeten worden gebruikt voor de _juiste grootte_ van VM'S voor AVS. U kunt kiezen voor een hoge grootte op _basis van prestaties_ of _als on-premises,_ zonder rekening te houden met de prestatie geschiedenis. |
**Prestatiegeschiedenis** | Hiermee stelt u de duur in waarmee rekening moet worden gehouden bij het evalueren van de prestatie gegevens van machines. Deze eigenschap is alleen van toepassing wanneer de grootte criteria _op basis van prestaties_ zijn. |
**Percentiel gebruik** | Hiermee geeft u de percentiel waarde van de voorbereidings Voorbeeldset op die in aanmerking komt voor de juiste grootte. Deze eigenschap is alleen van toepassing wanneer de grootte van het formaat op basis van prestaties is gebaseerd.|
**Comfortfactor** | Tijdens de evaluatie houdt Azure Migrate rekening met een buffer (comfortfactor). Deze buffer wordt toegepast boven op de gegevens over machinegebruik voor VM's (CPU, geheugen, schijf en netwerk). De comfortfactor houdt rekening met factoren zoals seizoensgebonden gebruik, een korte prestatiegeschiedenis en een mogelijke gebruikstoename in de toekomst.<br/><br/> Een VM met 10 kernen en een gebruik van 20% komt bijvoorbeeld gewoonlijk overeen met een VM met 2 kernen. Met een comfortfactor van 2,0x is het resultaat echter een VM met 4 kernen. |
**Aanbieding** | Hier wordt de [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) weer gegeven die u hebt Inge schreven. Azure Migrate maakt dienovereenkomstig een schatting van de kosten.|
**Valuta** | Hier wordt de facturerings valuta voor uw account weer gegeven. |
**Korting (%)** | Een lijst met alle abonnements kortingen die boven op de Azure-aanbieding worden weer gegeven. De standaardinstelling is 0%. |
**Azure Hybrid Benefit** | Hiermee geeft u op of u Software Assurance hebt en in aanmerking komt voor [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Hoewel dit geen invloed heeft op de prijzen van Azure VMware-oplossingen vanwege de prijs op basis van het knoop punt, kunnen klanten nog steeds hun on-premises OS-licenties (op basis van micro soft) in AVS Toep assen met behulp van hybride Azure-voor delen. Andere leveranciers van software-besturings systemen moeten hun eigen licentie voorwaarden opgeven, bijvoorbeeld RHEL. |
**vCPU overabonnement** | Hiermee geeft u de verhouding van het aantal virtuele kernen aan dat is gekoppeld aan 1 fysieke kern in het AVS-knoop punt. De standaard waarde in de berekeningen is 4 vCPU: 1 fysieke kern in AVS. <br/><br/> API-gebruikers kunnen deze waarde instellen als een geheel getal. Houd er rekening mee dat vCPU-overabonnement > 4:1 mogelijk een afname van de prestaties kan veroorzaken, maar kan worden gebruikt voor werk belastingen van het type webserver. |

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Welke eigenschappen worden gebruikt voor het maken en aanpassen van een Azure SQL-evaluatie?

Dit is what's opgenomen in de Azure SQL Assessment-eigenschappen:

**Eigenschap** | **Details**
--- | ---
**Doellocatie** | De Azure-regio waarnaar u wilt migreren. Azure SQL-configuratie en aanbevelingen voor kosten zijn gebaseerd op de locatie die u opgeeft.
**Type doel implementatie** | Het doel implementatie type waarvoor u de evaluatie wilt uitvoeren: <br/><br/> Selecteer **Aanbevolen** als u Azure migrate wilt beoordelen van de gereedheid van uw SQL-servers voor de migratie naar Azure SQL mi en Azure SQL DB, en aanbevolen de meest geschikte optie voor de doel implementatie, doellaag, Azure SQL-configuratie en maandelijkse schattingen te kiezen.<br/><br/>Selecteer **Azure SQL DB** als u wilt dat uw SQL-servers alleen worden gemigreerd naar Azure SQL-data bases en de doellaag, de Azure SQL DB-configuratie en maandelijkse schattingen bekijken.<br/><br/>Selecteer **Azure SQL mi** als u wilt dat uw SQL-servers alleen worden gemigreerd naar Azure SQL-data bases en de doel laag, de configuratie van Azure SQL mi en maandelijkse schattingen bekijken.
**Gereserveerde capaciteit** | Hiermee wordt de gereserveerde capaciteit opgegeven, zodat rekening wordt gehouden met kosten ramingen in de evaluatie.<br/><br/> Als u een gereserveerde capaciteits optie selecteert, kunt u geen korting (%) opgeven.
**Criteria voor het aanpassen van de grootte** | Deze eigenschap wordt gebruikt om de grootte van de Azure SQL-configuratie te wijzigen. <br/><br/> De standaard instelling is **gebaseerd op prestaties** , wat betekent dat de evaluatie de SQL Server instanties en data bases prestatie gegevens verzamelt om een optimaal formaat van Azure SQL Managed instance en/of Azure SQL database laag/configuratie aanbeveling aan te bevelen.
**Prestatiegeschiedenis** | Met de prestatie geschiedenis wordt de duur opgegeven die wordt gebruikt wanneer prestatie gegevens worden geëvalueerd.
**Percentiel gebruik** | Percentiel gebruik geeft de percentiel waarde van het voor beeld van de prestaties die wordt gebruikt voor supportte.
**Comfortfactor** | De buffer die wordt gebruikt tijdens de evaluatie. IT-accounts voor problemen zoals seizoen gebruik, korte prestatie geschiedenis en waarschijnlijk toename van toekomstig gebruik.<br/><br/> Zo resulteert een 10-kern exemplaar met 20% gebruik doorgaans in een exemplaar met twee kernen. Met een comfort factor van 2,0 is het resultaat een exemplaar van vier kernen.
**Aanbieding/licentie programma** | De [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) waarin u bent Inge schreven. Op dit moment kunt u alleen kiezen uit betalen per gebruik en betalen naar gebruik-dev/test. Houd er rekening mee dat u extra korting kunt verkrijgen door gereserveerde capaciteit en Azure Hybrid Benefit boven op de aanbieding voor betalen naar gebruik toe te passen.
**Servicelaag** | De meest geschikte service tier-optie voor uw bedrijfs behoeften voor migratie naar Azure SQL Database en/of Azure SQL Managed instance:<br/><br/>**Aanbevolen** als u wilt dat Azure migrate de beste geschikte servicelaag voor uw servers aanbeveelt. Dit kan algemeen gebruik of bedrijfs kritiek zijn. <br/><br/> **Algemeen** Als u een Azure SQL-configuratie wilt die is ontworpen voor budget gerichte workloads. [Meer informatie](https://docs.microsoft.com/azure/azure-sql/database/service-tier-general-purpose) <br/><br/> **Bedrijfskritiek** Als u een Azure SQL-configuratie wilt die is ontworpen voor workloads met lage latentie met hoge tolerantie voor fouten en snelle failovers. [Meer informatie](https://docs.microsoft.com/azure/azure-sql/database/service-tier-business-critical)
**Valuta** | De facturerings valuta voor uw account.
**Korting (%)** | Alle abonnements kortingen die u boven op de Azure-aanbieding ontvangt. De standaardinstelling is 0%.
**Azure Hybrid Benefit** | Hiermee geeft u op of u al een SQL Server licentie hebt. <br/><br/> Als u dit wel doet en de actieve Software Assurance van SQL Server-abonnementen wordt gebruikt, kunt u voor de Azure Hybrid Benefit aanvragen wanneer u licenties naar Azure brengt.

## <a name="edit-assessment-properties"></a>Beoordelings eigenschappen bewerken

Ga als volgt te werk om de evaluatie-eigenschappen te bewerken nadat u een evaluatie hebt gemaakt:

1. Klik in het Azure Migrate project op **servers**.
2. Klik in **Azure migrate: detectie en evaluatie** op het aantal evaluaties.
3. Klik in de **beoordeling** op de relevante evaluatie > **Eigenschappen bewerken**.
5. Pas de eigenschappen van de evaluatie aan overeenkomstig de bovenstaande tabellen.
6. Klik op **Opslaan** om de evaluatie bij te werken.


U kunt ook de evaluatie-eigenschappen bewerken tijdens het maken van een evaluatie.


## <a name="next-steps"></a>Volgende stappen

- Meer [informatie](concepts-assessment-calculation.md) over hoe Azure-VM-evaluaties worden berekend.
- Meer [informatie](concepts-azure-sql-assessment-calculation.md) over hoe Azure SQL-evaluaties worden berekend.
- Meer [informatie](concepts-azure-vmware-solution-assessment-calculation.md) over hoe AVS-evaluaties worden berekend.

