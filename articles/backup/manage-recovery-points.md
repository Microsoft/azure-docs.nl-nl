---
title: Herstelpunten beheren
description: Meer informatie over hoe de Azure Backup-Service herstel punten beheert voor virtuele machines
ms.topic: conceptual
ms.date: 11/08/2020
ms.openlocfilehash: 256df693aba0f799c24bcba6defe846e5c37ccaa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94428629"
---
# <a name="manage-recovery-points"></a>Herstelpunten beheren

In dit artikel wordt beschreven hoe retentie werkt voor virtuele machines. Telkens wanneer er back-ups plaatsvinden, worden er herstel punten gemaakt op basis waarvan de herstel bewerkingen kunnen worden uitgevoerd.

Voor virtuele machines is de eerste back-up een volledige back-up en de volgende back-ups zijn incrementele back-ups.

## <a name="recovery-points-and-retention"></a>Herstel punten en retentie

### <a name="scheduled-initial-and-incremental-backup"></a>Geplande initiële en incrementele back-up

Laten we een vereenvoudigd voor beeld maken van de virtuele machine *v1* met een gegevens schijf die bestaat uit vier blokken: blok 1, blok 2, blok 3 en blok 4. Elk blok heeft een grootte van 16 KB.

![Virtuele machine met vier blokken](./media/manage-recovery-points/four-blocks.png)

**Stap 1-eerste back-up:** De eerste back-up is een volledige back-up. Het fungeert als basis lijn waarop volgende incrementele back-ups worden toegepast. Stel dat er gegevens zijn geschreven naar blok 1 en blok 2 op de bron-VM. Dezelfde gegevens worden gerepliceerd als D1 en D2 op de Recovery Services kluis opslag.

![Eerste back-up wordt gerepliceerd](./media/manage-recovery-points/initial-backup.png)

**Stap 2-incrementele back-up 1:** Houd rekening met de nieuwe gegevens die zijn toegevoegd aan blok 3 van de virtuele machine. Dezelfde gegevens worden gerepliceerd op de volgende incrementele back-up en alleen het gewijzigde blok wordt opgeslagen als D3.  Bij elke stap, zelfs als 1 KB van het blok wordt gewijzigd, wordt het hele 16 KB-blok geüpload in het herstel punt.

![Eerste incrementele back-up](./media/manage-recovery-points/first-incremental-backup.png)

**Stap 3-incrementele back-up 2:**  Bekijk nu dat er gegevens wijzigingen zijn in blok 3 en blok 2 op de bron-VM. Deze wijzigingen worden gerepliceerd op de volgende incrementele back-up als D3 en D2.

![Tweede incrementele back-up](./media/manage-recovery-points/second-incremental-backup.png)

### <a name="on-demand-backup"></a>Back-ups op aanvraag

U kunt ervoor kiezen om een back-up op aanvraag van een virtuele machine uit te voeren nadat u de beveiliging hebt ingesteld.

- De back-up op aanvraag is een volledige back-up als deze wordt geactiveerd vóór de eerste geplande eerste back-up.
- Als de eerste back-up is voltooid en een back-up op aanvraag wordt geactiveerd, wordt er een incrementele back-up gemaakt.
- De retentie tijd van herstel punten die zijn gemaakt voor een back-up op aanvraag is de Bewaar waarde die u opgeeft wanneer u de back-up wilt activeren.

### <a name="storage-cost"></a>Opslagkosten

Het **herstel punt** dat voor de eerste back-up is gemaakt, bevat alle blokken die de gegevens bevatten. De volgende incrementele herstel punten bestaan alleen uit de blokken met gewijzigde gegevens. De opslag kosten komen overeen met de som van alle blokken over alle herstel punten.

We gaan het bovenstaande voor beeld gebruiken om inzicht te krijgen in de opslag kosten na elke stap:

|Stap  |Back-uptype  |Blokken gewijzigd  |Opslagtype |
|------|---------|---------|---------|
|1     |     Eerste back-up    | Blok 1, blok 2        |    Corresponderend met herstel punt 1 (D1 + D2)     |
|2     |  Incrementele back-up 1       |  Blok 3       |   Corresponderend met herstel punt 1 (D1 + D2) + herstel punt 2 (D3)      |
|3     |    Incrementele back-up 2     |    Blok 2, blok 3     |   Corresponderend met herstel punt 1 (D1 + D2) + herstel punt 2 (D3) + herstel punt 3 (D2 + D3)      |

### <a name="recovery-point-expiration"></a>Verval datum herstel punt

Elk herstel punt heeft een Bewaar duur, zoals is opgegeven in het back-upbeleid. Het opschonen gebeurt met regel matige tussen pozen en alle herstel punten die zijn verlopen, worden opgeruimd.

Wanneer het herstel punt is verlopen, wordt het verwijderd of samengevoegd.

### <a name="case-1-initial-recovery-point-expires"></a>Voor beeld 1: het eerste herstel punt verloopt

Wanneer het eerste herstel punt verloopt, wordt het samengevoegd met het volgende incrementele herstel punt. Alle gegevens blokken die worden overschreven in het incrementele herstel punt worden verwijderd en de rest worden samengevoegd. De incrementele back-up wordt dan de eerste volledige back-up. Laten we een voor beeld bekijken:

- *Herstel punt 1* dat tijdens de eerste back-up is gemaakt, heeft de volledige back-up van de virtuele machine.
- Wanneer *herstel punt 1* verloopt, is *herstel punt 2* de volgende volledige back-up.
- Blok D1 wordt samengevoegd met *herstel punt 2* en D2 wordt verwijderd, omdat de gegevens in blok 2 worden overschreven in *herstel punt 2*. Deze wijziging wordt vastgelegd als blok D2.
- Blok keren D1 wordt in de opeenvolgende herstel punten bewaard, totdat er wijzigingen zijn aangebracht vóór de volgende back-up.

![Eerste geval](./media/manage-recovery-points/first-case.png)

### <a name="case-2-in-between-incremental-recovery-point-expires"></a>Voor beeld 2: tussen incrementeel herstel punt verloopt

- Als *herstel punt 2* vóór *herstel punt 1* verloopt, worden de gegevens van *herstel punt 2* samengevoegd met het volgende beschik bare herstel punt: *herstel punt 3*. Blok keer D3 wordt dus samengevoegd met *herstel punt 3*.
- *Herstel punt 1* is nog steeds de volledige back-up met blok D1 en D2.

![Tweede geval](./media/manage-recovery-points/second-case.png)

### <a name="case-3-on-demand-recovery-point-expires"></a>Voor beeld 3: herstel punt op aanvraag verloopt

In dit voor beeld wordt een schema (dagelijks back-upbeleid) gepland om te worden uitgevoerd met een Bewaar periode van *n* dagen.  Als er een back-up op aanvraag wordt geactiveerd op de vierde dag voordat de volgende geplande back-up en de retentie periode 10 dagen worden opgegeven, wordt er nog steeds een incrementele back-up gemaakt. Er wordt een herstel punt (*RP1 op aanvraag*) gemaakt na *herstel punt 3* en vóór *herstel punt 4*.  Aan het einde van de dag 14 wordt het herstel punt op aanvraag (*RP1 op aanvraag*) verlopen en wordt het samengevoegd met het volgende beschik bare herstel punt. De gegevens blokken die nog op de server aanwezig zijn, worden samengevoegd, terwijl de gegevens blokken die zijn gewijzigd (overschreven of verwijderd) worden verwijderd uit het verlopen herstel punt.

![Derde geval](./media/manage-recovery-points/third-case.png)

### <a name="impact-of-policy-change-on-recovery-points"></a>Gevolgen van beleids wijzigingen op herstel punten

Wanneer een beleid wordt gewijzigd, wordt dit toegepast op zowel nieuwe als bestaande herstel punten. Zie [impact van beleids wijzigingen op herstel punten](backup-architecture.md#impact-of-policy-change-on-recovery-points)voor meer informatie.

### <a name="impact-of-stop-protection-on-recovery-points"></a>Gevolgen van het stoppen van beveiliging op herstel punten

Er zijn twee manieren om het beveiligen van een virtuele machine te stoppen:

- **Stop de beveiliging en verwijder de back-upgegevens.** Met deze optie worden alle toekomstige back-uptaken gestopt om uw VM te beveiligen en worden alle herstel punten verwijderd. Als [voorlopig verwijderen](backup-azure-security-feature-cloud.md) is ingeschakeld, blijven de verwijderde gegevens 14 dagen bewaard. Er worden geen kosten in rekening gebracht voor artikelen met de status zacht verwijderd. De gegevens kunnen worden verwijderd binnen de periode van 14 dagen. Als voorlopig verwijderen niet is ingeschakeld, worden de gegevens onmiddellijk opgeruimd en kunt u de virtuele machine niet herstellen of de **back-** upoptie voor hervatten gebruiken.
- **Stop de beveiliging en behoud de back-upgegevens.** Met deze optie worden alle toekomstige back-uptaken gestopt om uw VM te beveiligen. De Azure Backup-Service behoudt echter altijd de herstel punten waarvan een back-up is gemaakt. U moet betalen om de herstel punten in de kluis te blijven (Zie [Azure backup prijzen](https://azure.microsoft.com/pricing/details/backup/) voor meer informatie). U kunt de VM zo nodig herstellen. Als u besluit de beveiliging van de virtuele machine te hervatten, kunt u de **back-** upoptie voor hervatten gebruiken. Nadat het maken van de back-up is hervat, worden de Bewaar regels toegepast op de verval punten. U kunt ook de back-upgegevens verwijderen met de optie  **back-upgegevens verwijderen** .

## <a name="impact-of-deleting-a-vm-without-stop-protection"></a>Gevolgen van het verwijderen van een virtuele machine zonder de beveiliging te stoppen

Het verwijderen van een virtuele machine zonder de beveiliging te stoppen heeft invloed op herstel punten en is een ongewenste scenario. IDEA liter moeten worden gestopt voordat u de virtuele machine verwijdert. Omdat de bron niet bestaat, zullen de geplande back-ups mislukken met de [VMNotFoundV2-fout](backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found). De herstel punten worden regel matig gereinigd op basis van het Bewaar beleid, maar de laatste kopie van de virtuele machine blijft permanent. u wordt dan ook gefactureerd. Afhankelijk van uw scenario hebt u de volgende twee opties:

- **Optie 1:** Herstel de virtuele machine met behulp van een van de herstel punten. Als u de verwijderde virtuele machine wilt herstellen, herstelt u met dezelfde naam en in dezelfde resource groep. Als u de herstelde VM beveiligt naar dezelfde kluis, worden de bestaande herstel punten automatisch toegevoegd.
- **Optie 2:** Ga naar de Recovery Services kluis en stop de beveiliging met gegevens verwijderen.

### <a name="impact-of-expired-recovery-points-for-items-in-soft-deleted-state"></a>Gevolgen van verlopen herstel punten voor items met de status zacht verwijderd

Als [voorlopig verwijderen](backup-azure-security-feature-cloud.md) is ingeschakeld voor de Recovery Services-kluis, blijft het verlopen herstel punt in de modus voorlopig verwijderen en wordt het niet opgeschoond. Er worden geen kosten in rekening gebracht wanneer een herstel punt de status zacht verwijderd heeft.

### <a name="impact-of-churn-on-backup-performance"></a>Gevolgen van verloop van de back-upprestaties

Stel dat de totale opslag van een virtuele machine 8 TB is en dat het verloop 5% is. De bijbehorende incrementele back-upopslag is dan 5% van 8 TB van 0,4 TB. Een hogere verloop komt overeen met een grotere back-end-opslag voor latere incrementele back-ups. Het verloop heeft invloed op de prestaties van de back-up. Meer het verloop, het langzamer maken van het back-upproces en het gebruik van back-end-opslag.

Als u wilt weten hoe verloop de prestaties van de back-up beïnvloedt, raadpleegt u het volgende scenario:

|Virtuele machines  |VM1  |VM2  |VM3  |
|---------|---------|---------|---------|
|Aantal gegevens schijven    | 4 (A1, A2, A3, A4)        | 4 (B1, B2, B3, B4)        |  4 (C1, C2, C3, C4)       |
|Grootte van elke schijf   |      4 TB   | 4 TB        |  4 TB       |
|Verloop van back-upgegevens    |   A1-4 TB      | B1-1 TB; B2-1 TB <br> B3-1 TB; B4-1 TB  |   C1-2 TB; C4-2 TB      |

De prestaties van de back-up hebben de volg orde VM2>VM3>VM1. De reden hiervoor is dat de gegevens over de verschillende schijven worden verspreid. Omdat het maken van een back-up van schijven parallel plaatsvindt, worden in VM2 de beste prestaties weer gegeven.

## <a name="next-steps"></a>Volgende stappen

- [Architectuur en onderdelen van Azure Backup](backup-architecture.md)
