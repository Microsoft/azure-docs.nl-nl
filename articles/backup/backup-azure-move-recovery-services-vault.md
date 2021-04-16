---
title: Recovery Services Azure Backup kluizen verplaatsen
description: Instructies voor het verplaatsen van een Recovery Services-kluis tussen Azure-abonnementen en -resourcegroepen.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 49d6782af5a9c946eaf92147dab22e4605195d89
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514764"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Een Recovery Services-kluis verplaatsen tussen Azure-abonnementen en -resourcegroepen

In dit artikel wordt uitgelegd hoe u een Recovery Services-kluis die is geconfigureerd voor Azure Backup verplaatst tussen Azure-abonnementen of naar een andere resourcegroep in hetzelfde abonnement. U kunt de Azure Portal of PowerShell gebruiken om een Recovery Services-kluis te verplaatsen.

## <a name="supported-regions"></a>Ondersteunde regio’s

Alle openbare regio's en onafhankelijke regio's worden ondersteund, behalve Frankrijk - centraal, Frankrijk - zuid, Duitsland - noordoost, Duitsland - centraal, China - noord, China North2, China - oost en China East2.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Vereisten voor het verplaatsen van de Recovery Services-kluis

- Tijdens het verplaatsen van de kluis tussen resourcegroepen zijn zowel de bron- als de doelresourcegroepen vergrendeld om schrijf- en verwijderbewerkingen te voorkomen. Zie dit [artikel](../azure-resource-manager/management/move-resource-group-and-subscription.md) voor meer informatie.
- Alleen een beheerdersabonnement heeft de machtigingen om een kluis te verplaatsen.
- Voor het verplaatsen van kluizen tussen abonnementen moet het doelabonnement zich in dezelfde tenant bevinden als het bronabonnement en moet de status ervan zijn ingeschakeld. Zie Abonnement overdragen naar een andere [directory](../role-based-access-control/transfer-subscription.md) en Veelgestelde vragen over Recovery Service-kluizen als u een kluis wilt verplaatsen naar een andere Azure [AD-directory.](/backup-azure-backup-faq.yml#recovery-services-vault)
- U moet zijn machtigingen om schrijfbewerkingen uit te voeren op de doelresourcegroep.
- Als u de kluis verplaatst, wordt alleen de resourcegroep gewijzigd. De Recovery Services-kluis bevindt zich op dezelfde locatie en kan niet worden gewijzigd.
- U kunt slechts één Recovery Services-kluis per regio tegelijk verplaatsen.
- Als een VM niet wordt verplaatst met de Recovery Services-kluis tussen abonnementen of naar een nieuwe resourcegroep, blijven de huidige VM-herstelpunten intact in de kluis totdat ze verlopen.
- Of de VM nu wordt verplaatst met de kluis of niet, u kunt de VM altijd herstellen vanuit de bewaarde back-upgeschiedenis in de kluis.
- De Azure Disk Encryption vereist dat de sleutelkluis en VM's zich in dezelfde Azure-regio en hetzelfde abonnement bevinden.
- Zie dit artikel als u een virtuele machine met beheerde schijven [wilt verplaatsen.](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- De opties voor het verplaatsen van resources die zijn geïmplementeerd via het klassieke model verschillen, afhankelijk van of u de resources binnen een abonnement verplaatst of naar een nieuw abonnement. Zie dit [artikel](../azure-resource-manager/management/move-resource-group-and-subscription.md) voor meer informatie.
- Back-upbeleid dat is gedefinieerd voor de kluis, wordt bewaard nadat de kluis is verplaatst tussen abonnementen of naar een nieuwe resourcegroep.
- U kunt alleen een kluis verplaatsen die een van de volgende typen back-upitems bevat. Alle back-upitems van de typen die hieronder niet worden vermeld, moeten worden gestopt en de gegevens worden definitief verwijderd voordat de kluis wordt verplaatst.
  - Azure Virtual Machines
  - Microsoft Azure Recovery Services-agent (MARS)
  - Microsoft Azure Backup Server (MABS)
  - Data Protection Manager (DPM)
- Als u een kluis met back-upgegevens van een VM verplaatst tussen abonnementen, moet u uw VM's naar hetzelfde abonnement verplaatsen en dezelfde naam voor de doel-VM-resourcegroep gebruiken (als in het oude abonnement) om door te gaan met het maken van back-ups.

> [!NOTE]
> Het verplaatsen van Recovery Services-kluizen Azure Backup tussen Azure-regio's wordt niet ondersteund.<br><br>
> Als u **VM's**(Azure IaaS, Hyper-V, VMware) of fysieke machines hebt geconfigureerd voor herstel na noodherstel met behulp van Azure Site Recovery, wordt de bewerking voor verplaatsen geblokkeerd. Als u kluizen wilt verplaatsen voor Azure Site Recovery, lees dan dit artikel voor meer informatie over het handmatig verplaatsen van kluizen. [](../site-recovery/move-vaults-across-regions.md)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Gebruik Azure Portal Recovery Services-kluis te verplaatsen naar een andere resourcegroep

Een Recovery Services-kluis en de bijbehorende resources naar een andere resourcegroep verplaatsen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Open de lijst met **Recovery Services-kluizen en** selecteer de kluis die u wilt verplaatsen. Wanneer het kluisdashboard wordt geopend, wordt dit weergegeven zoals in de volgende afbeelding.

   ![Recovery Services-kluis openen](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Als u de **essentials-informatie voor** uw kluis niet ziet, selecteert u het vervolgkeuzepictogram. U ziet nu de Essentials-informatie voor uw kluis.

   ![Tabblad Essentials Information](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Selecteer in het overzichtsmenu van de kluis **de optie Wijzigen** naast **Resourcegroep** om het deelvenster **Resources verplaatsen te** openen.

   ![Resourcegroep wijzigen](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. In het **deelvenster Resources verplaatsen** wordt het voor de geselecteerde kluis aanbevolen om de optionele gerelateerde resources te verplaatsen door het selectievakje in te selecteren, zoals wordt weergegeven in de volgende afbeelding.

   ![Abonnement verplaatsen](./media/backup-azure-move-recovery-services/move-resource.png)

5. Als u de doelresourcegroep wilt toevoegen, selecteert u in de vervolgkeuzelijst **Resourcegroep** een bestaande resourcegroep of selecteert u de optie **Een nieuwe groep** maken.

   ![Resource maken](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Nadat u de resourcegroep hebt toegevoegd, bevestigt u dat de hulpprogramma's en scripts die zijn gekoppeld aan verplaatste resources pas werken als ik ze heb bijgewerkt om nieuwe **resource-ID's** te gebruiken. Selecteer vervolgens **OK** om het verplaatsen van de kluis te voltooien.

   ![Bevestigingsbericht](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Gebruik Azure Portal Recovery Services-kluis te verplaatsen naar een ander abonnement

U kunt een Recovery Services-kluis en de bijbehorende resources naar een ander abonnement verplaatsen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Open de lijst met Recovery Services-kluizen en selecteer de kluis die u wilt verplaatsen. Wanneer het kluisdashboard wordt geopend, wordt het weergegeven zoals in de volgende afbeelding.

    ![Recovery Services-kluis openen](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Als u de **Essentials-informatie voor** uw kluis niet ziet, selecteert u het vervolgkeuzepictogram. U ziet nu de Essentials-informatie voor uw kluis.

    ![Tabblad Essentials Information](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Selecteer in het overzichtsmenu van de kluis **de optie Wijzigen** naast **Abonnement** om het deelvenster Resources **verplaatsen te** openen.

   ![Abonnement wijzigen](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selecteer de resources die moeten worden verplaatst.  Hier raden we u aan om de optie Alles selecteren te gebruiken om alle vermelde optionele resources te selecteren.

   ![resource verplaatsen](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Selecteer het doelabonnement in **de** vervolgkeuzelijst Abonnement, waar u de kluis wilt verplaatst.
6. Als u de doelresourcegroep wilt toevoegen, selecteert u in de vervolgkeuzelijst **Resourcegroep** een bestaande resourcegroep of selecteert u de optie **Een nieuwe groep** maken.

   ![Abonnement toevoegen](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Selecteer Ik begrijp dat hulpprogramma's en scripts die zijn gekoppeld aan verplaatste resources pas werken als ik ze heb bijgewerkt om de optie nieuwe **resource-ID's** te gebruiken om te bevestigen. Selecteer vervolgens **OK.**

> [!NOTE]
> Back-up van meerdere abonnementen (RS-kluis en beveiligde VM's zijn in verschillende abonnementen) is geen ondersteund scenario. Bovendien kan de optie voor opslag redundantie van lokaal redundante opslag (LRS) naar globale redundante opslag (GRS) en omgekeerd niet worden gewijzigd tijdens het verplaatsen van de kluis.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>PowerShell gebruiken om de Recovery Services-kluis te verplaatsen

Als u een Recovery Services-kluis wilt verplaatsen naar een andere resourcegroep, gebruikt u `Move-AzureRMResource` de cmdlet . `Move-AzureRMResource` vereist de resourcenaam en het type resource. U kunt beide uit de `Get-AzureRmRecoveryServicesVault` cmdlet halen.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Als u de resources wilt verplaatsen naar een ander abonnement, moet u de `-DestinationSubscriptionId` parameter opnemen.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Nadat u de bovenstaande cmdlets hebt uitgevoerd, wordt u gevraagd om te bevestigen dat u de opgegeven resources wilt verplaatsen. Typ **Y om** te bevestigen. Na een geslaagde validatie wordt de resource verplaatst.

## <a name="use-cli-to-move-recovery-services-vault"></a>CLI gebruiken om de Recovery Services-kluis te verplaatsen

Gebruik de volgende cmdlet om een Recovery Services-kluis te verplaatsen naar een andere resourcegroep:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Geef de parameter op om over te gaan naar een nieuw `--destination-subscription-id` abonnement.

## <a name="post-migration"></a>Na de migratie

1. Het toegangsbeheer voor de resourcegroepen instellen/controleren.  
2. De functie Back-uprapportage en -bewaking moet opnieuw worden geconfigureerd voor de kluis nadat de overstap is voltooid. De vorige configuratie gaat verloren tijdens het verplaatsen.

## <a name="move-an-azure-virtual-machine-to-a-different-recovery-service-vault"></a>Verplaats een virtuele Azure-machine naar een andere Recovery Service-kluis. 

Als u een virtuele Azure-machine wilt verplaatsen waar Azure Backup is ingeschakeld, hebt u twee opties. Ze zijn afhankelijk van uw zakelijke vereisten:

- [U hoeft vorige back-upgegevens niet te bewaren](#dont-need-to-preserve-previous-backed-up-data)
- [Vorige back-upgegevens moeten behouden blijven](#must-preserve-previous-backed-up-data)

### <a name="dont-need-to-preserve-previous-backed-up-data"></a>U hoeft vorige back-upgegevens niet te bewaren

Als u workloads in een nieuwe kluis wilt beveiligen, moeten de huidige beveiliging en gegevens in de oude kluis worden verwijderd en wordt de back-up opnieuw geconfigureerd.

>[!WARNING]
>De volgende bewerking is destructief en kan niet ongedaan worden gemaakt. Alle back-upgegevens en back-upitems die aan de beveiligde server zijn gekoppeld, worden permanent verwijderd. Ga zorgvuldig te werk.

**Stop en verwijder de huidige beveiliging op de oude kluis:**

1. Schakel de functie voor het verwijderen van de kluis uit in de eigenschappen van de kluis. Volg [deze stappen om](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) het verwijderen van de functie voor zacht verwijderen uit te schakelen.

2. Stop de beveiliging en verwijder back-ups uit de huidige kluis. Selecteer back-upitems in het menu **Kluisdashboard.** Items die hier worden vermeld en die moeten worden verplaatst naar de nieuwe kluis, moeten samen met de back-upgegevens worden verwijderd. Zie beveiligde [items in de cloud verwijderen en](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) beveiligde items [on-premises verwijderen.](backup-azure-delete-vault.md#delete-protected-items-on-premises)

3. Als u van plan bent AFS (Azure-bestands shares), SQL-servers of SAP HANA-servers te verplaatsen, moet u de registratie ervan ook ongedaan maken. Selecteer back-upinfrastructuur in het menu van het **kluisdashboard.** Zie hoe u [de registratie van de SQL-server](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)ongedaan kunt maken, de registratie van een opslagaccount dat is gekoppeld aan Azure-bestands [shares](manage-afs-backup.md#unregister-a-storage-account)ongedaan kunt maken en de registratie van een [SAP HANA maken.](sap-hana-db-manage.md#unregister-an-sap-hana-instance)

4. Zodra ze uit de oude kluis zijn verwijderd, gaat u door met het configureren van de back-ups voor uw workload in de nieuwe kluis.

### <a name="must-preserve-previous-backed-up-data"></a>Vorige back-upgegevens moeten behouden blijven

Als u de huidige beveiligde gegevens in de oude kluis wilt bewaren en de beveiliging in een nieuwe kluis wilt voortzetten, zijn er beperkte opties voor enkele van de workloads:

- Voor MARS kunt u de [beveiliging stoppen met gegevens behouden](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) en de agent registreren in de nieuwe kluis.

  - Azure Backup-service blijven alle bestaande herstelpunten van de oude kluis behouden.
  - U moet betalen om de herstelpunten in de oude kluis te houden.
  - U kunt de back-upgegevens alleen herstellen voor niet-herstelde herstelpunten in de oude kluis.
  - Er moet een nieuwe initiële replica van de gegevens worden gemaakt in de nieuwe kluis.

- Voor een Azure-VM [](backup-azure-manage-vms.md#stop-protecting-a-vm) kunt u de beveiliging stoppen met het bewaren van gegevens voor de VM in de oude kluis, de VM verplaatsen naar een andere resourcegroep en vervolgens de VM in de nieuwe kluis beveiligen. Zie [richtlijnen en beperkingen voor](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) het verplaatsen van een VM naar een andere resourcegroep.

  Een VM kan slechts in één kluis tegelijk worden beveiligd. De VM in de nieuwe resourcegroep kan echter worden beveiligd in de nieuwe kluis, omdat deze wordt beschouwd als een andere VM.

  - Azure Backup-service behoudt de herstelpunten die zijn back-up op de oude kluis.
  - U moet betalen om de herstelpunten in de oude kluis te houden (zie Azure Backup [prijzen](azure-backup-pricing.md) voor meer informatie).
  - U kunt de VM, indien nodig, herstellen vanuit de oude kluis.
  - De eerste back-up op de nieuwe kluis van de VM in de nieuwe resource is een initiële replica.

## <a name="next-steps"></a>Volgende stappen

U kunt veel verschillende typen resources verplaatsen tussen resourcegroepen en abonnementen.

Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).