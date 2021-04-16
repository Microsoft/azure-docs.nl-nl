---
title: Overzicht van Recovery Services-kluizen
description: Een overzicht van Recovery Services-kluizen.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 2f2018f0f3d3135d632418c2e591e6ad938d62d2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517448"
---
# <a name="recovery-services-vaults-overview"></a>Overzicht van Recovery Services-kluizen

In dit artikel worden de functies van een Recovery Services-kluis beschreven. Een Recovery Services-kluis is een opslagentiteit in Azure waarin gegevens worden opgeslagen. De gegevens zijn doorgaans kopieën van gegevens of configuratie-informatie voor virtuele machines (VM's), werkbelastingen, servers of werkstations. U kunt Recovery Services-kluizen gebruiken voor het opslaan van back-upgegevens voor verschillende Azure-services, zoals IaaS-VM's (Linux of Windows) en Azure SQL databases. Recovery Services-kluizen ondersteunen System Center DPM, Windows Server, Azure Backup Server en meer. Recovery Services-kluizen maken het eenvoudig om uw back-upgegevens te ordenen, terwijl de beheertaken minimaal zijn. Recovery Services-kluizen zijn gebaseerd op Azure Resource Manager model van Azure, dat functies biedt zoals:

- **Verbeterde mogelijkheden voor het beveiligen van back-upgegevens:** met Recovery Services-kluizen biedt Azure Backup beveiligingsmogelijkheden voor het beveiligen van back-ups in de cloud. De beveiligingsfuncties zorgen ervoor dat u uw back-ups kunt beveiligen en gegevens veilig kunt herstellen, zelfs als de productie- en back-upservers zijn aangetast. [Meer informatie](backup-azure-security-feature.md)

- **Centrale bewaking voor uw** hybride IT-omgeving: met Recovery Services-kluizen kunt u niet alleen uw [Azure IaaS-VM's](backup-azure-manage-vms.md) bewaken, maar ook [uw on-premises assets](backup-azure-manage-windows-server.md#manage-backup-items) vanuit een centrale portal. [Meer informatie](backup-azure-monitoring-built-in-monitor.md)

- **Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)**: Azure RBAC biedt een fijner toegangsbeheerbeheer in Azure. [Azure biedt verschillende ingebouwde rollen en](../role-based-access-control/built-in-roles.md)Azure Backup drie ingebouwde rollen voor [het beheren van herstelpunten.](backup-rbac-rs-vault.md) Recovery Services-kluizen zijn compatibel met Azure RBAC, waarmee back-up- en hersteltoegang wordt beperkt tot de gedefinieerde set gebruikersrollen. [Meer informatie](backup-rbac-rs-vault.md)

- **Soft Delete:** met een zachte verwijderperiode worden de back-upgegevens nog 14 dagen bewaard, zelfs als een kwaadwillende actor een back-up verwijdert (of als er per ongeluk back-upgegevens worden verwijderd), zodat het back-upitem zonder gegevensverlies kan worden hersteld. De extra 14 dagen retentieperiode voor back-upgegevens met de status 'soft delete' heeft geen kosten voor u. [Meer informatie](backup-azure-security-feature-cloud.md).

- **Herstellen tussen regio's:** met CROSS Region Restore (CRR) kunt u azure-VM's herstellen in een secundaire regio, een gekoppelde Azure-regio. Als u deze functie op [kluisniveau inschakelen,](backup-create-rs-vault.md#set-cross-region-restore)kunt u de gerepliceerde gegevens op elk moment naar keuze herstellen in de secundaire regio. Hiermee kunt u de secundaire regiogegevens herstellen voor controle-naleving en tijdens uitvalscenario's, zonder te wachten tot Azure een noodtoestand heeft gedeclareerde (in tegenstelling tot de GRS-instellingen van de kluis). [Meer informatie](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Opslaginstellingen in de Recovery Services-kluis

Een Recovery Service-kluis is een entiteit waarin de back-ups en herstelpunten worden opgeslagen die in de loop van de tijd zijn gemaakt. De Recovery Service-kluis bevat ook de beleidsregels voor back-up die aan de beveiligde virtuele machines zijn gekoppeld.

- Azure Backup verwerkt automatisch de opslag voor de kluis. Zie hoe [opslaginstellingen kunnen worden gewijzigd.](./backup-create-rs-vault.md#set-storage-redundancy)

- Zie deze artikelen over [geografische,](../storage/common/storage-redundancy.md#geo-zone-redundant-storage)lokale en [](../storage/common/storage-redundancy.md#locally-redundant-storage) zonale redundantie voor meer informatie over opslag [redundantie.](../storage/common/storage-redundancy.md#zone-redundant-storage)

## <a name="encryption-settings-in-the-recovery-services-vault"></a>Versleutelingsinstellingen in de Recovery Services-kluis

In deze sectie worden de beschikbare opties besproken voor het versleutelen van uw back-upgegevens die zijn opgeslagen in de Recovery Services-kluis.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Versleuteling van back-upgegevens met behulp van door het platform beheerde sleutels

Standaard worden al uw gegevens versleuteld met behulp van door het platform beheerde sleutels. U hoeft geen expliciete actie van uw kant uit te voeren om deze versleuteling in teschakelen. Dit geldt voor alle workloads van uw Recovery Services-kluis.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Versleuteling van back-upgegevens met door de klant beheerde sleutels

U kunt ervoor kiezen om uw gegevens te versleutelen met behulp van versleutelingssleutels die eigendom zijn van en worden beheerd door u. Azure Backup kunt u uw RSA-sleutels gebruiken die zijn opgeslagen in de Azure Key Vault voor het versleutelen van uw back-ups. De versleutelingssleutel die wordt gebruikt voor het versleutelen van back-ups, kan verschillen van de versleutelingssleutel die wordt gebruikt voor de bron. De gegevens worden beveiligd met een op AES 256 gebaseerde gegevensversleutelingssleutel (DEK), die op zijn beurt wordt beveiligd met uw sleutels. Dit geeft u volledige controle over de gegevens en de sleutels. Als u versleuteling wilt toestaan, moet aan de Recovery Services-kluis toegang worden verleend tot de versleutelingssleutel in de Azure Key Vault. U kunt de sleutel uitschakelen of de toegang zo nodig intrekken. U moet echter versleuteling inschakelen met behulp van uw sleutels voordat u items naar de kluis probeert te beveiligen.

Lees meer over het versleutelen van uw back-upgegevens [met behulp van door de klant beheerde sleutels.](encryption-at-rest-with-cmk.md)

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) is een gepersonaliseerde cloudconsultant die het gebruik van Azure optimaliseert. Het analyseert uw Azure-gebruik en biedt tijdig aanbevelingen om uw implementaties te optimaliseren en te beveiligen. Het biedt aanbevelingen in vier categorieën: Hoge beschikbaarheid, Beveiliging, Prestaties en Kosten.

Azure Advisor aanbevelingen per [uur](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) voor VM's waar geen back-up van is, zodat u nooit het maken van back-up van belangrijke VM's mist. U kunt de aanbevelingen ook bepalen door ze uit te sluimeren.  U kunt de aanbeveling selecteren en back-up inschakelen op VM's in de regel door de kluis (waar back-ups worden opgeslagen) en het back-upbeleid (planning van back-ups en retentie van back-ups) op te geven.

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>Aanvullende bronnen

- [Door Vault ondersteunde en niet-ondersteunde scenario's](backup-support-matrix.md#vault-support)
- [Veelgestelde vragen over Vault](backup-azure-backup-faq.yml)

## <a name="next-steps"></a>Volgende stappen

Gebruik de volgende artikelen voor het volgende:

- [Een back-up maken van een IaaS-VM](backup-azure-arm-vms-prepare.md)
- [Een back-up maken van Azure Backup Server](backup-azure-microsoft-azure-backup.md)
- [Een back-up maken van een Windows-server](backup-windows-with-mars-agent.md)
