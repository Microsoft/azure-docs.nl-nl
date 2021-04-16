---
title: Recovery Services-kluizen maken en configureren
description: In dit artikel leert u hoe u Recovery Services-kluizen maakt en configureert waarin de back-ups en herstelpunten worden opgeslagen. Meer informatie over het gebruik van herstellen tussen regio's om te herstellen in een secundaire regio.
ms.topic: conceptual
ms.date: 04/14/2021
ms.custom: references_regions
ms.openlocfilehash: 5e2983e473fac72d02f0fdbc8c307e96326ac0a6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518572"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Een Recovery Services-kluis maken en configureren

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Opslag redundantie instellen

Azure Backup verwerkt automatisch de opslag voor de kluis. U moet opgeven hoe die opslag wordt gerepliceerd.

> [!NOTE]
> Het **wijzigen van het type opslagreplicatie** (lokaal redundant/geografisch redundant) voor een Recovery Services-kluis moet worden uitgevoerd voordat u back-ups in de kluis configureert. Zodra u de back-up hebt geconfigureerd, wordt de optie om te wijzigen uitgeschakeld.
>
>- Als u de back-up nog niet hebt geconfigureerd, volgt [u deze stappen](#set-storage-redundancy) om de instellingen te controleren en te wijzigen.
>- Als u de back-up al hebt geconfigureerd en van GRS naar LRS moet worden verplaatst, bekijkt u [deze tijdelijke oplossingen.](#how-to-change-from-grs-to-lrs-after-configuring-backup)

1. Selecteer in **het deelvenster Recovery Services-kluizen** de nieuwe kluis. Selecteer eigenschappen **in** de sectie **Instellingen.**
1. Selecteer **in Eigenschappen** onder **Back-upconfiguratie** de optie **Bijwerken.**

1. Selecteer het type opslagreplicatie en selecteer **Opslaan.**

     ![De opslagconfiguratie voor nieuwe kluis instellen](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - Als u Azure als primair eindpunt voor **back-upopslag** gebruikt, kunt u het beste de standaardinstelling Geografisch redundant blijven gebruiken.
   - Als Azure niet uw primaire eindpunt is voor back-upopslag, kiest u **Lokaal redundant**, zodat u de kosten voor Azure-opslag verlaagt.
   - Meer informatie over [geografische](../storage/common/storage-redundancy.md#geo-redundant-storage) en [lokale](../storage/common/storage-redundancy.md#locally-redundant-storage) redundantie.
   - Als u gegevensbeschikbaarheid zonder uitvaltijd in een regio nodig hebt om gegevensopslag te garanderen, kiest u [zone-redundante opslag.](../storage/common/storage-redundancy.md#zone-redundant-storage)

>[!NOTE]
>De opslagreplicatie-instellingen voor de kluis zijn niet relevant voor back-ups van Azure-bestandsdelingen, omdat de huidige oplossing een momentopname is en er geen gegevens worden overgedragen naar de kluis. Momentopnamen worden opgeslagen in hetzelfde opslagaccount als de back-up van de bestands share.

## <a name="set-cross-region-restore"></a>Herstellen tussen regio's instellen

Met de hersteloptie **Herstel tussen regio's (CRR)** kunt u gegevens herstellen in een secundaire, [gekoppelde Azure-regio.](../best-practices-availability-paired-regions.md)

Het ondersteunt de volgende gegevensbronsen:

- Azure-VM's (algemene beschikbaarheid)
- SQL-databases die worden gehost op azure-VM's (preview)
- SAP HANA databases die worden gehost op virtuele Azure-VM's (preview)

Met herstel in verschillende regio's kunt u het volgende doen:

- oefeningen uitvoeren wanneer er een controle- of nalevingsvereiste is
- de gegevens herstellen als er sprake is van een noodherstel in de primaire regio

Wanneer u een VM herstelt, kunt u de VM of de schijf ervan herstellen. Als u herstelt vanuit SQL/SAP HANA databases die worden gehost op virtuele Azure-VM's, kunt u databases of hun bestanden herstellen.

Als u deze functie wilt kiezen, selecteert **u Herstellen tussen regio's inschakelen** in het **deelvenster Back-upconfiguratie.**

Omdat dit proces zich op opslagniveau voort, zijn er gevolgen [voor de prijzen.](https://azure.microsoft.com/pricing/details/backup/)

>[!NOTE]
>Voordat u begint:
>
>- Bekijk de [ondersteuningsmatrix](backup-support-matrix.md#cross-region-restore) voor een lijst met ondersteunde beheerde typen en regio's.
>- De functie Herstel tussen regio's (CRR) voor Virtuele Azure-VM's is nu algemeen beschikbaar in alle openbare Azure-regio's.
>- Herstellen tussen regio's voor SQL- SAP HANA databases is in preview in alle openbare Azure-regio's.
>- CRR is een opt-in-functie op kluisniveau voor elke GRS-kluis (standaard uitgeschakeld).
>- Na het kiezen kan het tot 48 uur duren voordat de back-upitems beschikbaar zijn in secundaire regio's.
>- CrR voor Azure-VM's wordt momenteel ondersteund voor Azure Resource Manager azure-VM's en versleutelde Azure-VM's. Klassieke Azure-VM's worden niet ondersteund. Wanneer aanvullende beheertypen CRR ondersteunen, worden ze **automatisch** ingeschreven.
>- Herstellen tussen **regio's kan** momenteel niet worden teruggeleid naar GRS of LRS zodra de beveiliging voor het eerst wordt gestart.
>- Op dit moment ligt de [RPO](azure-backup-glossary.md#rpo-recovery-point-objective) van de secundaire regio maximaal 12 uur van de primaire regio, hoewel de replicatie van geografisch redundante opslag met leestoegang [(RA-GRS)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) 15 minuten is.

### <a name="configure-cross-region-restore"></a>Herstel in meerdere regio's configureren

Een kluis die is gemaakt met GRS-redundantie bevat de optie om de functie Herstellen tussen regio's te configureren. Elke GRS-kluis heeft een banner die wordt gekoppeld aan de documentatie. Als u CRR voor de kluis wilt configureren, gaat u naar het deelvenster Back-upconfiguratie met de optie voor het inschakelen van deze functie.

 ![Banner back-upconfiguratie](./media/backup-azure-arm-restore-vms/banner.png)

1. Ga vanuit de portal naar uw Recovery Services-kluis > **Eigenschappen** (onder **Instellingen).**
1. Selecteer **onder Back-upconfiguratie** de **optie Bijwerken.**
1. Selecteer **Herstellen tussen regio's inschakelen in deze kluis om** de functionaliteit in te schakelen.

   ![Herstellen tussen regio's inschakelen](./media/backup-azure-arm-restore-vms/backup-configuration.png)

Zie deze artikelen voor meer informatie over back-up en herstel met CRR:

- [Herstel tussen regio's voor Azure-VM's](backup-azure-arm-restore-vms.md#cross-region-restore)
- [Herstellen tussen regio's voor SQL-databases](restore-sql-database-azure-vm.md#cross-region-restore)
- [Herstellen tussen regio's voor SAP HANA databases](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>Versleutelingsinstellingen instellen

Standaard worden de gegevens in de Recovery Services-kluis versleuteld met behulp van door het platform beheerde sleutels. Er zijn geen expliciete acties van uw kant vereist om deze versleuteling in teschakelen. Dit geldt voor alle workloads die een back-up maken van uw Recovery Services-kluis.  U kunt ervoor kiezen om uw eigen sleutel te gebruiken om de back-upgegevens in deze kluis te versleutelen. Dit wordt aangeduid als door de klant beheerde sleutels. Als u back-upgegevens wilt versleutelen met behulp van uw eigen sleutel, moet de versleutelingssleutel worden opgegeven voordat een item voor deze kluis wordt beveiligd. Zodra u versleuteling met uw sleutel hebt ingeschakeld, kan deze niet meer worden teruggedraaid.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Een kluis configureren voor versleuteling met behulp van door de klant beheerde sleutels

Als u uw kluis wilt configureren voor versleuteling met door de klant beheerde sleutels, moeten deze stappen in deze volgorde worden gevolgd:

1. Beheerde identiteit inschakelen voor uw Recovery Services-kluis

1. Machtigingen toewijzen aan de kluis voor toegang tot de versleutelingssleutel in de Azure Key Vault

1. Schakel de beveiliging voor het verwijderen en ops manier van verwijderen op de Azure Key Vault

1. De versleutelingssleutel toewijzen aan de Recovery Services-kluis

Instructies voor elk van deze stappen vindt u [in dit artikel](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys).

## <a name="modifying-default-settings"></a>Standaardinstellingen wijzigen

We raden u ten zeerste aan de standaardinstellingen voor **Type opslagreplicatie** en **Beveiligingsinstellingen** te controleren voordat u back-ups in de kluis configureert.

- **Opslagreplicatietype** is standaard ingesteld op **Geografisch redundant** (GRS). Zodra u de back-up hebt geconfigureerd, wordt de optie om te wijzigen uitgeschakeld.
  - Als u de back-up nog niet hebt geconfigureerd, volgt [u deze stappen](#set-storage-redundancy) om de instellingen te controleren en te wijzigen.
  - Als u de back-up al hebt geconfigureerd en van GRS naar LRS moet worden verplaatst, bekijkt u [deze tijdelijke oplossingen.](#how-to-change-from-grs-to-lrs-after-configuring-backup)

- **Voor de functie** Voor zacht verwijderen is **standaard Ingeschakeld op** nieuw gemaakte kluizen om back-upgegevens te beveiligen tegen onbedoelde of schadelijke verwijderen. [Volg deze stappen om](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) de instellingen te controleren en te wijzigen.

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>Overzetten van GRS naar LRS na het configureren van back-up

Voordat u besluit om over te schakelen van GRS naar lokaal redundante opslag (LRS), moet u de balans tussen lagere kosten en een hogere duurzaamheid van gegevens bekijken die bij uw scenario passen. Als u van GRS naar LRS moet gaan, hebt u twee opties. Ze zijn afhankelijk van uw zakelijke vereisten voor het bewaren van de back-upgegevens:

- [U hoeft eerdere back-upgegevens niet te behouden](#dont-need-to-preserve-previous-backed-up-data)
- [Vorige back-upgegevens moeten behouden blijven](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>U hoeft eerdere back-upgegevens niet te behouden

Als u workloads in een nieuwe LRS-kluis wilt beveiligen, moeten de huidige beveiliging en gegevens worden verwijderd uit de GRS-kluis en moeten back-ups opnieuw worden geconfigureerd.

>[!WARNING]
>De volgende bewerking is destructief en kan niet ongedaan worden gemaakt. Alle back-upgegevens en back-upitems die aan de beveiligde server zijn gekoppeld, worden permanent verwijderd. Ga zorgvuldig te werk.

Stop en verwijder de huidige beveiliging in de GRS-kluis:

1. Schakel de functie Voor verwijderen in de GRS-kluiseigenschappen uit. Volg [deze stappen om](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) het verwijderen van de functie voor soft delete uit te schakelen.

1. Stop de beveiliging en verwijder back-ups uit de bestaande GRS-kluis. Selecteer back-upitems in het menu **Kluisdashboard.** Items die hier worden vermeld en die moeten worden verplaatst naar de LRS-kluis, moeten samen met de back-upgegevens worden verwijderd. Zie beveiligde [items in de cloud verwijderen en](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) beveiligde items [on-premises verwijderen.](backup-azure-delete-vault.md#delete-protected-items-on-premises)

1. Als u van plan bent AFS (Azure-bestands shares), SQL-servers of SAP HANA-servers te verplaatsen, moet u de registratie ervan ook ongedaan maken. Selecteer back-upinfrastructuur in het menu van het **kluisdashboard.** Zie hoe u de [registratie van de SQL-server](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)ongedaan kunt maken, de registratie van een opslagaccount dat is gekoppeld aan [Azure-bestands](manage-afs-backup.md#unregister-a-storage-account)shares ongedaan kunt maken en de registratie van een SAP HANA ongedaan kunt [maken.](sap-hana-db-manage.md#unregister-an-sap-hana-instance)

1. Zodra ze zijn verwijderd uit de GRS-kluis, gaat u door met het configureren van de back-ups voor uw workload in de nieuwe LRS-kluis.

#### <a name="must-preserve-previous-backed-up-data"></a>Vorige back-upgegevens moeten behouden blijven

Als u de huidige beveiligde gegevens in de GRS-kluis wilt bewaren en de beveiliging in een nieuwe LRS-kluis wilt voortzetten, zijn er beperkte opties voor een aantal van de workloads:

- Voor MARS kunt u de beveiliging [stoppen met behoud van gegevens](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) en de agent registreren in de nieuwe LRS-kluis.

  - Azure Backup-service blijven alle bestaande herstelpunten van de GRS-kluis behouden.
  - U moet betalen om de herstelpunten in de GRS-kluis te houden.
  - U kunt de back-upgegevens alleen herstellen voor niet-herstelde herstelpunten in de GRS-kluis.
  - Er moet een nieuwe initiële replica van de gegevens worden gemaakt in de LRS-kluis.

- Voor een Azure-VM [](backup-azure-manage-vms.md#stop-protecting-a-vm) kunt u de beveiliging stoppen met behoud van gegevens voor de VM in de GRS-kluis, de VM verplaatsen naar een andere resourcegroep en vervolgens de VM in de LRS-kluis beveiligen. Zie [richtlijnen en beperkingen voor](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) het verplaatsen van een VM naar een andere resourcegroep.

  Een VM kan in slechts één kluis tegelijk worden beveiligd. De VM in de nieuwe resourcegroep kan echter worden beveiligd in de LRS-kluis, omdat deze wordt beschouwd als een andere VM.

  - Azure Backup-service behoudt de herstelpunten van de GRS-kluis die een back-up hebben.
  - U moet betalen om de herstelpunten in de GRS-kluis te houden (zie Azure Backup [prijzen](azure-backup-pricing.md) voor meer informatie).
  - U kunt de VM indien nodig herstellen vanuit de GRS-kluis.
  - De eerste back-up op de LRS-kluis van de VM in de nieuwe resource is een initiële replica.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](backup-azure-recovery-services-vault-overview.md) Recovery Services-kluizen.
[Meer informatie over](backup-azure-delete-vault.md) Recovery Services-kluizen verwijderen.