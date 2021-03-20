---
title: Onopzettelijke Verwijder beveiliging voor Azure-bestands shares
description: Meer informatie over hoe u met zacht verwijderen uw Azure-bestands shares kunt beveiligen tegen onbedoeld verwijderen.
ms.topic: conceptual
ms.date: 02/02/2020
ms.custom: references_regions
ms.openlocfilehash: 52a116320e07c25d4ee7f00b8063ca15faeb8560
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89179909"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>Onbedoeld verwijderen van beveiliging voor Azure-bestands shares met behulp van Azure Backup

Voor beveiliging tegen Cyber aanvallen of per ongeluk verwijderen is de functie voor het uitvoeren van een [tijdelijke](../storage/files/storage-files-prevent-file-share-deletion.md) verwijdering ingeschakeld voor alle bestands shares in een opslag account wanneer u een back-up configureert voor een bestands share in het betreffende opslag account. Met zacht verwijderen, zelfs als een schadelijke actor de bestands share verwijdert, worden de inhoud van de bestands share en de herstel punten (moment opnamen) gedurende mini maal 14 extra dagen bewaard, waardoor bestands shares zonder gegevens verlies kunnen worden hersteld.  Voorlopig verwijderen wordt ondersteund voor Standard-en Premium Storage-accounts en de instelling wordt ingeschakeld door Azure Backup voor alle opslag accounts die als host fungeren voor back-ups van bestands shares.

In het volgende stroom diagram ziet u de verschillende stappen en statussen van een back-upitem wanneer zacht verwijderen is ingeschakeld voor bestands shares in een opslag account:

 ![Stroom diagram voor zacht verwijderen](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>Wanneer wordt zacht verwijderen ingeschakeld voor bestands shares in mijn opslag account?

Wanneer u back-up voor de eerste keer voor een bestands share in een opslag account configureert, schakelt Azure Backup-Service tijdelijke verwijdering in voor alle bestands shares in het betreffende opslag account.

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>Kan ik het aantal dagen configureren dat mijn moment opnamen en herstel punten behouden blijven in de modus voor zacht verwijderen nadat ik de bestands share heb verwijderd?

Ja, u kunt de Bewaar periode instellen volgens uw vereisten. In [dit document](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal) worden de stappen beschreven voor het configureren van de Bewaar periode. Voor opslag accounts met back-ups van bestands shares moet de instelling minimale Bewaar periode 14 dagen zijn.

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>Azure Backup ik mijn Bewaar instelling opnieuw instellen omdat ik deze in minder dan 14 dagen heb geconfigureerd?

Vanuit het oogpunt van beveiliging kunt u het beste een minimale Bewaar periode van 14 dagen instellen voor opslag accounts met back-ups van bestands shares. Als Azure Backup de instelling in minder dan 14 dagen aanduidt, wordt deze opnieuw ingesteld op 14 dagen.

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>Wat zijn de kosten die tijdens de retentie periode zijn gemaakt?

Tijdens de voorlopig verwijderde periode blijven de kosten van de beveiligde instantie en de opslag van de moment opname bewaard.  Daarnaast worden er kosten in rekening gebracht voor de gebruikte capaciteit tegen het normale tarief voor standaard bestands shares en de opslag snelheid van moment opnamen voor Premium-bestands shares.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>Kan ik een herstel bewerking uitvoeren wanneer mijn gegevens de status zacht verwijderd hebben?

U moet eerst de verwijdering van de tijdelijke verwijderde bestands share ongedaan maken om herstel bewerkingen uit te voeren. Met de bewerking voor het ongedaan maken van de verwijdering wordt de bestands share opgeslagen in de status van de back-up, waar u kunt herstellen naar elk gewenst moment. Ga naar [deze koppeling](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) of Zie het script voor het [verwijderen van bestands shares](./scripts/backup-powershell-script-undelete-file-share.md)voor meer informatie over het ongedaan maken van de verwijdering van de bestands share.

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>Hoe kan ik de gegevens van een bestands share verwijderen uit een opslag account met ten minste één beveiligde bestands share?

Als u ten minste één beveiligde bestands share in een opslag account hebt, betekent dit dat de functie voor voorlopig verwijderen is ingeschakeld voor alle bestands shares in dat account en dat uw gegevens 14 dagen na het verwijderen worden bewaard. Maar als u de gegevens direct wilt verwijderen en niet wilt behouden, volgt u deze stappen:

1. Als u de bestands share al hebt verwijderd terwijl zacht verwijderen was ingeschakeld, verwijdert u eerst de bestands share uit de [bestanden Portal](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) of gebruikt u het script voor het verwijderen van de [Bestands share](./scripts/backup-powershell-script-undelete-file-share.md).
2. Schakel de optie voor het voorlopig verwijderen van bestands shares in uw opslag account uit door de stappen in [dit document](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#disable-soft-delete)te volgen.
3. Verwijder nu de bestands share waarvan u de inhoud onmiddellijk wilt verwijderen.

>[!NOTE]
>U moet stap 2 uitvoeren voordat de volgende geplande back-uptaak wordt uitgevoerd op de beveiligde bestands share in uw opslag account. Omdat telkens wanneer de back-uptaak wordt uitgevoerd, de optie zacht verwijderen opnieuw wordt ingeschakeld voor alle bestands shares in het opslag account.

>[!WARNING]
>Na het uitschakelen van de functie voor voorlopig verwijderen in stap 2, is een Verwijder bewerking die wordt uitgevoerd op de bestands shares een permanente Verwijder bewerking. Dus als u de back-upbestanden per ongeluk verwijdert nadat u de tijdelijke verwijdering hebt uitgeschakeld, gaan alle moment opnamen verloren en kunt u uw gegevens niet meer herstellen.

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>Welke wijzigingen worden Azure Backup wanneer ik de registratie van een opslag account ongedaan maken in de context van de instelling voor voorlopig verwijderen van een bestands share?

Op het moment van de registratie controleert Azure Backup de instelling voor de Bewaar periode voor bestands shares en als deze langer is dan 14 dagen of minder dan veer tien dagen. Als de retentie echter 14 dagen is, beschouwt dit dan als ingeschakeld door Azure Backup en wordt de tijdelijke verwijdering uitgeschakeld tijdens het ongedaan maken van de registratie. Als u de registratie van het opslag account ongedaan wilt maken en de retentie-instelling wilt behouden, moet u deze opnieuw inschakelen in het deel venster opslag account nadat de registratie is voltooid. U kunt [deze koppeling](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) raadplegen voor de configuratie stappen.

## <a name="next-steps"></a>Volgende stappen

Lees meer over het [maken van back-ups van Azure-bestandsshares via de Azure-portal](backup-afs.md).
