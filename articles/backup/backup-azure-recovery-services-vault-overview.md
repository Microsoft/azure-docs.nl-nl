---
title: Overzicht van Recovery Services-kluizen
description: Een overzicht van Recovery Services kluizen.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 0ed37446e1ccf0780f924143c8f063964adf0004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755123"
---
# <a name="recovery-services-vaults-overview"></a>Overzicht van Recovery Services-kluizen

In dit artikel worden de functies van een Recovery Services kluis beschreven. Een Recovery Services kluis is een opslag entiteit in azure die gegevens bevat. De gegevens zijn doorgaans kopieën van gegevens, of configuratie-informatie voor virtuele machines (Vm's), werk belastingen, servers of werk stations. U kunt Recovery Services kluizen gebruiken voor het bewaren van back-upgegevens voor verschillende Azure-Services, zoals IaaS Vm's (Linux of Windows) en Azure SQL-data bases. Recovery Services-kluizen ondersteunen System Center DPM, Windows Server, Azure Backup Server en meer. Recovery Services-kluizen maken het eenvoudig om uw back-upgegevens te ordenen, terwijl de beheertaken minimaal zijn. Recovery Services kluizen zijn gebaseerd op het Azure Resource Manager model van Azure, dat functies bevat zoals:

- **Verbeterde mogelijkheden voor het beveiligen van back-upgegevens**: met Recovery Services kluizen biedt Azure backup beveiligings mogelijkheden voor het beveiligen van Cloud back-ups. Met de beveiligings functies kunt u uw back-ups beveiligen en veilig gegevens herstellen, zelfs als er inbreuk is gemaakt op de productie-en back-upservers. [Meer informatie](backup-azure-security-feature.md)

- **Centrale bewaking voor uw hybride IT-omgeving**: met Recovery Services kluizen kunt u niet alleen uw [Azure IaaS-vm's](backup-azure-manage-vms.md) , maar ook uw [on-premises assets](backup-azure-manage-windows-server.md#manage-backup-items) van een centrale Portal bewaken. [Meer informatie](backup-azure-monitoring-built-in-monitor.md)

- **Toegangs beheer op basis van rollen (Azure RBAC)** van Azure: Azure RBAC biedt nauw keurig beheer van toegangs beheer in Azure. [Azure biedt verschillende ingebouwde rollen](../role-based-access-control/built-in-roles.md)en Azure backup heeft drie [ingebouwde rollen voor het beheren van herstel punten](backup-rbac-rs-vault.md). Recovery Services kluizen zijn compatibel met Azure RBAC, waarmee back-up-en herstel toegang wordt beperkt tot de gedefinieerde set gebruikers rollen. [Meer informatie](backup-rbac-rs-vault.md)

- **Voorlopig verwijderen**: met zacht verwijderen, zelfs als een schadelijke actor een back-up verwijdert (of de back-upgegevens per ongeluk zijn verwijderd), worden de back-upgegevens 14 extra dagen bewaard, zodat het back-upitem zonder gegevens verlies kan worden hersteld. De extra 14 dagen voor het bewaren van back-upgegevens in de status ' voorlopig verwijderen ' maken geen kosten voor u. [Meer informatie](backup-azure-security-feature-cloud.md).

- **Restore voor meerdere regio's**: met cross Region Restore (CRR) kunt u virtuele Azure-machines herstellen in een secundaire regio, een Azure-gekoppelde regio. Als u deze functie inschakelt op het niveau van de [kluis](backup-create-rs-vault.md#set-cross-region-restore), kunt u de gerepliceerde gegevens op elk gewenst moment herstellen in de secundaire regio, wanneer u dat hebt gekozen. Zo kunt u de gegevens van de secundaire regio herstellen voor controle-naleving en tijdens uitval scenario's, zonder te wachten tot Azure een ramp declareert (in tegens telling tot de GRS-instellingen van de kluis). [Meer informatie](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Opslag instellingen in de Recovery Services kluis

Een Recovery Service-kluis is een entiteit waarin de back-ups en herstelpunten worden opgeslagen die in de loop van de tijd zijn gemaakt. De Recovery Service-kluis bevat ook de beleidsregels voor back-up die aan de beveiligde virtuele machines zijn gekoppeld.

- Azure Backup beheert automatisch de opslag voor de kluis. Bekijk hoe [opslag instellingen kunnen worden gewijzigd](./backup-create-rs-vault.md#set-storage-redundancy).

- Zie deze artikelen over [geo](../storage/common/storage-redundancy.md#geo-zone-redundant-storage), [lokale](../storage/common/storage-redundancy.md#locally-redundant-storage) en [zonegebonden](../storage/common/storage-redundancy.md#zone-redundant-storage) -redundantie voor meer informatie over opslag redundantie.

## <a name="encryption-settings-in-the-recovery-services-vault"></a>Versleutelings instellingen in de Recovery Services kluis

In deze sectie worden de opties beschreven die beschikbaar zijn voor het versleutelen van uw back-upgegevens die zijn opgeslagen in de Recovery Services kluis.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Versleuteling van back-upgegevens met door het platform beheerde sleutels

Standaard worden al uw gegevens versleuteld met sleutels die door het platform worden beheerd. U hoeft geen expliciete actie van uw end uit te voeren om deze versleuteling in te scha kelen. Dit is van toepassing op alle werk belastingen waarvan een back-up wordt gemaakt naar uw Recovery Services kluis.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Versleuteling van back-upgegevens met door de klant beheerde sleutels

U kunt ervoor kiezen om uw gegevens te versleutelen met versleutelings sleutels die eigendom zijn van en worden beheerd door u. Met Azure Backup kunt u uw RSA-sleutels gebruiken die zijn opgeslagen in de Azure Key Vault voor het versleutelen van uw back-ups. De versleutelings sleutel die wordt gebruikt voor het versleutelen van back-ups is mogelijk anders dan die voor de bron. De gegevens worden beveiligd met behulp van een AES 256-gegevens versleutelings sleutel (DEK), die op zijn beurt wordt beveiligd met uw sleutels. Hiermee krijgt u volledige controle over de gegevens en de sleutels. Als u versleuteling wilt toestaan, moet de Recovery Services kluis toegang krijgen tot de versleutelings sleutel in de Azure Key Vault. U kunt de sleutel uitschakelen of toegang intrekken wanneer dit nodig is. U moet echter versleuteling inschakelen met behulp van uw sleutels voordat u de items in de kluis probeert te beveiligen.

Meer informatie over het versleutelen van uw back-upgegevens [met door de klant beheerde sleutels](encryption-at-rest-with-cmk.md).

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) is een gepersonaliseerde Cloud consultant waarmee u het gebruik van Azure kunt optimaliseren. Het analyseert uw Azure-gebruik en biedt tijdig aanbevelingen om uw implementaties te optimaliseren en te beveiligen. Het biedt aanbevelingen in vier categorieën: hoge Beschik baarheid, beveiliging, prestaties en kosten.

Azure Advisor voorziet in elk uur [aanbevelingen](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) voor vm's waarvan geen back-up is gemaakt, zodat u nooit een back-up maakt van belang rijke vm's. U kunt de aanbevelingen ook beheren door ze uit te stellen.  U kunt de aanbeveling selecteren en back-up inschakelen op Vm's in de regel door de kluis op te geven (waarbij back-ups worden opgeslagen) en het back-upbeleid (schema van back-ups en bewaren van back-ups).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Door de kluis ondersteunde en niet-ondersteunde scenario's](backup-support-matrix.md#vault-support)
- [Veelgestelde vragen over de kluis](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Volgende stappen

Gebruik de volgende artikelen om:

- [Back-up maken van een IaaS-VM](backup-azure-arm-vms-prepare.md)
- [Een back-up maken van een Azure Backup Server](backup-azure-microsoft-azure-backup.md)
- [Back-up maken van een Windows-Server](backup-windows-with-mars-agent.md)
