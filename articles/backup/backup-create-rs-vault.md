---
title: Recovery Services kluizen maken en configureren
description: In dit artikel vindt u informatie over het maken en configureren van Recovery Services kluizen waarin de back-ups en herstel punten worden opgeslagen. Meer informatie over het gebruik van de bewerking voor het herstellen van meerdere regio's om te herstellen in een secundaire regio.
ms.topic: conceptual
ms.date: 05/30/2019
ms.custom: references_regions
ms.openlocfilehash: 3ba9f47da4a4c9719c313ba196ca121f8cde54ad
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387731"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Een Recovery Services kluis maken en configureren

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Opslag redundantie instellen

Azure Backup beheert automatisch de opslag voor de kluis. U moet opgeven hoe die opslag wordt gerepliceerd.

> [!NOTE]
> Het wijzigen van het **replicatie type** van de opslag (lokaal redundante/geo-redundant) voor een Recovery Services kluis moet worden uitgevoerd voordat u back-ups in de kluis configureert. Zodra u een back-up hebt geconfigureerd, is de optie om te wijzigen uitgeschakeld.
>
>- Als u de back-up nog niet hebt geconfigureerd, [volgt u deze stappen](#set-storage-redundancy) om de instellingen te controleren en te wijzigen.
>- Als u de back-up al hebt geconfigureerd en van GRS naar LRS moet verplaatsen, [raadpleegt u deze oplossingen](#how-to-change-from-grs-to-lrs-after-configuring-backup).

1. Selecteer in het deel venster **Recovery Services kluizen** de nieuwe kluis. Selecteer in de sectie **instellingen** de optie  **Eigenschappen**.
1. In **Eigenschappen**, onder **back-upconfiguratie**, selecteert u **bijwerken**.

1. Selecteer het type opslag replicatie en selecteer **Opslaan**.

     ![De opslagconfiguratie voor nieuwe kluis instellen](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - Als u Azure als een primair eind punt voor back-upopslag gebruikt, wordt u aangeraden de standaard **geo-redundante** instelling te gebruiken.
   - Als Azure niet uw primaire eindpunt is voor back-upopslag, kiest u **Lokaal redundant**, zodat u de kosten voor Azure-opslag verlaagt.
   - Meer informatie over [geo](../storage/common/storage-redundancy.md#geo-redundant-storage) en [lokale](../storage/common/storage-redundancy.md#locally-redundant-storage) redundantie.
   - Als u de beschik baarheid van gegevens zonder downtime in een regio nodig hebt, moet u de gegevens locatie garanderen en vervolgens [zone-redundante opslag](../storage/common/storage-redundancy.md#zone-redundant-storage)kiezen.

>[!NOTE]
>De instellingen voor de opslag replicatie voor de kluis zijn niet relevant voor een back-up van Azure file share, omdat de huidige oplossing moment opname is en er geen gegevens worden overgebracht naar de kluis. Moment opnamen worden opgeslagen in hetzelfde opslag account als de back-up van de bestands share.

## <a name="set-cross-region-restore"></a>Meerdere regio's herstellen instellen

Met de herstel optie **Cross Region Restore (CRR)** kunt u gegevens herstellen in een secundaire, [gekoppelde Azure-regio](../best-practices-availability-paired-regions.md).

Het ondersteunt de volgende gegevens bronnen:

- Azure-VM's
- SQL-data bases die worden gehost op virtuele machines van Azure
- SAP HANA-data bases die worden gehost op virtuele machines van Azure

Met de functie voor het terugzetten van meerdere regio's kunt u:

- oefeningen uitvoeren als er een controle-of nalevings vereiste is
- de gegevens herstellen als er sprake is van een nood geval in de primaire regio

Wanneer u een virtuele machine herstelt, kunt u de virtuele machine of de schijf herstellen. Als u de data bases van SQL/SAP HANA herstelt die worden gehost op virtuele machines van Azure, kunt u data bases of hun bestanden herstellen.

Als u deze functie wilt kiezen, selecteert u de optie voor het **terugzetten van meerdere regio's inschakelen** in het deel venster **back-upconfiguratie** .

Aangezien dit proces zich op het opslag niveau bevindt, zijn er [prijs implicaties](https://azure.microsoft.com/pricing/details/backup/).

>[!NOTE]
>Voordat u begint:
>
>- Bekijk de [ondersteunings matrix](backup-support-matrix.md#cross-region-restore) voor een lijst met ondersteunde beheerde typen en regio's.
>- De functie voor het terugzetten van meerdere regio's (CRR) wordt nu weer gegeven in alle open bare Azure-regio's.
>- CRR is een opt-in-functie op kluis niveau voor elke GRS-kluis (standaard uitgeschakeld).
>- Na het inbrengen kan het tot 48 uur duren voordat de back-upitems beschikbaar zijn in secundaire regio's.
>- Momenteel wordt CRR voor Azure-Vm's alleen ondersteund voor Azure Resource Manager Azure-Vm's. Klassieke Azure-Vm's worden niet ondersteund.  Wanneer extra beheer typen CRR ondersteunen, worden ze **automatisch** Inge schreven.
>- Het terugzetten van meerdere regio's kan op dit moment niet worden teruggedraaid naar GRS of LRS zodra de beveiliging voor de eerste keer wordt gestart.

### <a name="configure-cross-region-restore"></a>Herstel van meerdere regio's configureren

Een kluis die is gemaakt met GRS-redundantie bevat de optie voor het configureren van de functie voor het terugzetten van meerdere regio's. Elke GRS-kluis heeft een banner dat een koppeling naar de documentatie bevat. Als u CRR voor de kluis wilt configureren, gaat u naar het deel venster Back-upconfiguratie. Dit bevat de optie om deze functie in te scha kelen.

 ![Banner back-upconfiguratie](./media/backup-azure-arm-restore-vms/banner.png)

1. Ga in de portal naar uw Recovery Services kluis > **Eigenschappen** (onder **instellingen**).
1. Selecteer **Update** onder **back-upconfiguratie**.
1. Selecteer de optie **voor het terugzetten van meerdere regio's in deze kluis inschakelen** om de functionaliteit in te scha kelen.

   ![Het terugzetten van meerdere regio's inschakelen](./media/backup-azure-arm-restore-vms/backup-configuration.png)

Zie deze artikelen voor meer informatie over back-up en herstel met CRR:

- [Meerdere regio's herstellen voor Azure-Vm's](backup-azure-arm-restore-vms.md#cross-region-restore)
- [Meerdere regio's herstellen voor SQL-data bases](restore-sql-database-azure-vm.md#cross-region-restore)
- [Meerdere regio's herstellen voor SAP HANA-data bases](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>Versleutelings instellingen instellen

Standaard worden de gegevens in de Recovery Services kluis versleuteld met sleutels die door het platform worden beheerd. Er zijn geen expliciete acties vereist van uw einde om deze versleuteling in te scha kelen en is van toepassing op alle werk belastingen waarvan een back-up wordt gemaakt naar uw Recovery Services kluis.  U kunt ervoor kiezen uw eigen sleutel te nemen om de back-upgegevens te versleutelen in deze kluis. Dit wordt door de klant beheerde sleutels genoemd. Als u de back-upgegevens wilt versleutelen met behulp van uw eigen sleutel, moet de versleutelings sleutel worden opgegeven voordat een item in deze kluis wordt beveiligd. Wanneer u versleuteling met uw sleutel inschakelt, kan dit niet worden omgekeerd.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Een kluis configureren om te versleutelen met door de klant beheerde sleutels

Als u uw kluis wilt configureren voor versleuteling met door de klant beheerde sleutels, moet u deze stappen in deze volg orde volgen:

1. Beheerde identiteit inschakelen voor uw Recovery Services kluis

1. Machtigingen toewijzen aan de kluis om toegang te krijgen tot de versleutelings sleutel in de Azure Key Vault

1. Zacht verwijderen inschakelen en beveiliging opschonen op de Azure Key Vault

1. De versleutelings sleutel toewijzen aan de Recovery Services kluis

Instructies voor elk van deze stappen vindt u [in dit artikel](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys).

## <a name="modifying-default-settings"></a>Standaard instellingen wijzigen

We raden u ten zeerste aan de standaardinstellingen voor **Type opslagreplicatie** en **Beveiligingsinstellingen** te controleren voordat u back-ups in de kluis configureert.

- **Type opslag replicatie** is standaard ingesteld op **geo-redundant** (GRS). Zodra u de back-up hebt geconfigureerd, is de optie om te wijzigen uitgeschakeld.
  - Als u de back-up nog niet hebt geconfigureerd, [volgt u deze stappen](#set-storage-redundancy) om de instellingen te controleren en te wijzigen.
  - Als u de back-up al hebt geconfigureerd en van GRS naar LRS moet verplaatsen, [raadpleegt u deze oplossingen](#how-to-change-from-grs-to-lrs-after-configuring-backup).

- **Voorlopig verwijderen** is standaard **ingeschakeld** op nieuwe kluizen om back-upgegevens te beschermen tegen onbedoelde of schadelijke verwijderingen. [Volg deze stappen](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) om de instellingen te controleren en te wijzigen.

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>Wijzigen van GRS naar LRS na configureren van back-up

Voordat u besluit om over te stappen van GRS naar lokaal redundante opslag (LRS), moet u rekening houden met de wissel werking tussen lagere kosten en een hogere gegevens duurzaamheid die aansluit bij uw scenario. Als u van GRS naar LRS moet verplaatsen, hebt u twee opties. Ze zijn afhankelijk van uw bedrijfs vereisten voor het bewaren van de back-upgegevens:

- [U hoeft geen eerdere back-upgegevens te bewaren](#dont-need-to-preserve-previous-backed-up-data)
- [Moet eerdere back-upgegevens behouden](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>U hoeft geen eerdere back-upgegevens te bewaren

Voor het beveiligen van workloads in een nieuwe LRS-kluis moeten de huidige beveiliging en gegevens worden verwijderd in de GRS-kluis en moeten back-ups opnieuw worden geconfigureerd.

>[!WARNING]
>De volgende bewerking is destructief en kan niet ongedaan worden gemaakt. Alle back-upgegevens en back-upitems die aan de beveiligde server zijn gekoppeld, worden definitief verwijderd. Ga zorgvuldig te werk.

Huidige beveiliging op de GRS-kluis stoppen en verwijderen:

1. Schakel de optie zacht verwijderen uit in de kluis eigenschappen van GRS. Volg [deze stappen](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) om het uitvoeren van de tijdelijke verwijdering uit te scha kelen.

1. Stop de beveiliging en verwijder back-ups van de bestaande GRS-kluis. Selecteer in het menu van het kluis dashboard **Back-upitems**. De items die hier worden weer gegeven en die moeten worden verplaatst naar de LRS-kluis, moeten samen met hun back-upgegevens worden verwijderd. Zie [beveiligde items verwijderen in de Cloud](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) en [beveiligde items on-premises verwijderen](backup-azure-delete-vault.md#delete-protected-items-on-premises).

1. Als u van plan bent om AFS (Azure-bestands shares), SQL-servers of SAP HANA servers te verplaatsen, moet u de registratie ervan opheffen. Selecteer in het menu van het kluis dashboard **back-upinfrastructuur**. Zie [de registratie van de SQL-Server](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)ongedaan maken, de [registratie van een opslag account voor Azure-bestands shares ongedaan](manage-afs-backup.md#unregister-a-storage-account)maken en [de registratie van een SAP Hana exemplaar opheffen](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

1. Zodra ze zijn verwijderd uit de GRS-kluis, gaat u door met het configureren van de back-ups voor uw werk belasting in de nieuwe LRS-kluis.

#### <a name="must-preserve-previous-backed-up-data"></a>Moet eerdere back-upgegevens behouden

Als u de huidige beveiligde gegevens in de GRS-kluis wilt behouden en de beveiliging in een nieuwe LRS-kluis wilt voortzetten, zijn er beperkte opties voor sommige werk belastingen:

- Voor MARS kunt u de [beveiliging stoppen met het bewaren van gegevens](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) en de agent registreren in de nieuwe LRS-kluis.

  - Azure Backup service blijft alle bestaande herstel punten van de GRS-kluis behouden.
  - U moet betalen om de herstel punten in de GRS-kluis te blijven gebruiken.
  - U kunt de back-upgegevens alleen herstellen voor niet-verlopen herstel punten in de GRS-kluis.
  - Er moet een nieuwe eerste replica van de gegevens worden gemaakt op de LRS-kluis.

- Voor een Azure-VM kunt u de [beveiliging stoppen met het bewaren van gegevens](backup-azure-manage-vms.md#stop-protecting-a-vm) voor de virtuele machine in de GRS-kluis, de virtuele machine verplaatsen naar een andere resource groep en de virtuele machine vervolgens beveiligen in de LRS-kluis. Zie de [richt lijnen en beperkingen](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) voor het verplaatsen van een virtuele machine naar een andere resource groep.

  Een virtuele machine kan in slechts één kluis tegelijk worden beveiligd. De virtuele machine in de nieuwe resource groep kan echter worden beveiligd op de LRS-kluis, aangezien deze wordt beschouwd als een andere virtuele machine.

  - Azure Backup-Service behoudt de herstel punten waarvan een back-up is gemaakt op de GRS-kluis.
  - U moet betalen om de herstel punten in de GRS-kluis te blijven (Zie [Azure backup prijzen](azure-backup-pricing.md) voor meer informatie).
  - U kunt de virtuele machine, indien nodig, herstellen vanuit de GRS-kluis.
  - De eerste back-up op de LRS-kluis van de virtuele machine in de nieuwe resource is een initiële replica.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](backup-azure-recovery-services-vault-overview.md) Recovery Services kluizen.
[Meer informatie over](backup-azure-delete-vault.md) Recovery Services kluizen verwijderen.
