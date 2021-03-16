---
title: Azure Backup Recovery Services kluizen verplaatsen
description: Instructies voor het verplaatsen van een Recovery Services kluis over Azure-abonnementen en resource groepen.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 4f75bec533181b29625fb0a10cc26d03f2875036
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466368"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Een Recovery Services kluis verplaatsen over Azure-abonnementen en-resource groepen

In dit artikel wordt uitgelegd hoe u een Recovery Services kluis kunt verplaatsen die is geconfigureerd voor Azure Backup over Azure-abonnementen of naar een andere resource groep in hetzelfde abonnement. U kunt de Azure Portal of Power shell gebruiken om een Recovery Services kluis te verplaatsen.

## <a name="supported-regions"></a>Ondersteunde regio’s

Alle open bare regio's en soevereine regio's worden ondersteund, met uitzonde ring van Frankrijk-centraal, Frankrijk-zuid, Duitsland-noordoost, Duitsland-centraal, China-noord, China North2, China-oost en China Oost2.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Vereisten voor het verplaatsen van Recovery Services kluis

- Tijdens het verplaatsen van de kluis over resource groepen worden de bron-en doel resource groepen vergrendeld, waardoor er geen schrijf-en verwijder bewerkingen kunnen worden uitgevoerd. Zie dit [artikel](../azure-resource-manager/management/move-resource-group-and-subscription.md) voor meer informatie.
- Alleen beheerders abonnement heeft de machtigingen om een kluis te verplaatsen.
- Voor het verplaatsen van kluizen tussen abonnementen moet het doel abonnement zich in dezelfde Tenant bevinden als het bron abonnement en moet de status zijn ingeschakeld. Als u een kluis wilt verplaatsen naar een andere Azure AD-Directory, raadpleegt u het [abonnement overdragen naar een andere veelgestelde vragen over de Directory](../role-based-access-control/transfer-subscription.md) en [Recovery service-kluis](backup-azure-backup-faq.md#recovery-services-vault).
- U moet gemachtigd zijn om schrijf bewerkingen uit te voeren op de doel resource groep.
- Door de kluis te verplaatsen, wijzigt u alleen de resource groep. De Recovery Services kluis bevindt zich op dezelfde locatie en kan niet worden gewijzigd.
- U kunt slechts één Recovery Services kluis per regio tegelijk verplaatsen.
- Als een virtuele machine niet wordt verplaatst met de Recovery Services kluis in abonnementen of naar een nieuwe resource groep, blijven de huidige VM-herstel punten intact in de kluis totdat ze verlopen.
- Of de virtuele machine wordt verplaatst met de kluis of niet, u kunt de virtuele machine altijd herstellen met behulp van de behouden Back-upgeschiedenis in de kluis.
- De Azure Disk Encryption vereist dat de sleutel kluis en de virtuele machines zich in dezelfde Azure-regio en hetzelfde abonnement bevinden.
- Als u een virtuele machine wilt verplaatsen met beheerde schijven, raadpleegt u dit [artikel](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- De opties voor het verplaatsen van resources die via het klassieke model zijn geïmplementeerd, zijn afhankelijk van het feit of u de resources in een abonnement verplaatst of naar een nieuw abonnement. Zie dit [artikel](../azure-resource-manager/management/move-resource-group-and-subscription.md) voor meer informatie.
- Het back-upbeleid dat voor de kluis is gedefinieerd, blijft behouden nadat de kluis over alle abonnementen of naar een nieuwe resource groep is verplaatst.
- U kunt alleen een kluis verplaatsen die een van de volgende typen back-upitems bevat. Alle back-upitems van typen die hieronder niet worden vermeld, moeten worden gestopt en de gegevens worden permanent verwijderd voordat de kluis wordt verplaatst.
  - Azure Virtual Machines
  - Microsoft Azure Recovery Services-agent (MARS)
  - Microsoft Azure Backup-Server (MABS)
  - Data Protection Manager (DPM)
- Als u een kluis met back-upgegevens voor de VM verplaatst, moet u de virtuele machines verplaatsen naar hetzelfde abonnement en dezelfde doel-VM-resource groepsnaam gebruiken (zoals in het oude abonnement) om door te gaan met back-ups.

> [!NOTE]
> Het is niet mogelijk om Recovery Services kluizen voor Azure Backup over Azure-regio's te verplaatsen.<br><br>
> Als u virtuele machines (Azure IaaS, Hyper-V, VMware) of fysieke computers hebt geconfigureerd voor herstel na nood gevallen met **Azure site Recovery**, wordt de verplaatsings bewerking geblokkeerd. Als u kluizen voor Azure Site Recovery wilt verplaatsen, raadpleegt u [dit artikel](../site-recovery/move-vaults-across-regions.md) voor meer informatie over het hand matig verplaatsen van kluizen.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Azure Portal gebruiken om Recovery Services kluis te verplaatsen naar een andere resource groep

Een Recovery Services kluis en de bijbehorende resources verplaatsen naar een andere resource groep:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Open de lijst met **Recovery Services kluizen** en selecteer de kluis die u wilt verplaatsen. Wanneer het kluis dashboard wordt geopend, wordt het weer gegeven zoals in de volgende afbeelding.

   ![Recovery Services kluis openen](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Als u de **essentiële** informatie voor uw kluis niet ziet, selecteert u het pictogram vervolg keuzelijst. U ziet nu de essentiële informatie voor uw kluis.

   ![Het tabblad informatie over Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Selecteer in het menu kluis overzicht de optie **wijzigen** naast de **resource groep** om het deel venster **resources verplaatsen** te openen.

   ![Resource groep wijzigen](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. In het deel venster **resources verplaatsen** kunt u voor de geselecteerde kluis het beste de optionele gerelateerde resources verplaatsen door het selectie vakje te selecteren, zoals in de volgende afbeelding wordt weer gegeven.

   ![Abonnement verplaatsen](./media/backup-azure-move-recovery-services/move-resource.png)

5. Als u de doel resource groep wilt toevoegen, selecteert u in de vervolg keuzelijst **resource groep** een bestaande resource groep of selecteert u **een nieuwe groep maken** .

   ![Resource maken](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Nadat u de resource groep hebt toegevoegd, **moet u weten dat de hulpprogram ma's en scripts die zijn gekoppeld aan verplaatste resources, niet werken totdat ze zijn bijgewerkt** met de optie nieuwe resource-id's en vervolgens **OK** selecteren om de kluis te volt ooien.

   ![Bevestigings bericht](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Azure Portal gebruiken om Recovery Services kluis te verplaatsen naar een ander abonnement

U kunt een Recovery Services kluis en de bijbehorende resources naar een ander abonnement verplaatsen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Open de lijst met Recovery Services kluizen en selecteer de kluis die u wilt verplaatsen. Wanneer het kluis dashboard wordt geopend, wordt het weer gegeven zoals in de volgende afbeelding.

    ![Recovery Services kluis openen](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Als u de **essentiële** informatie voor uw kluis niet ziet, selecteert u het pictogram vervolg keuzelijst. U ziet nu de essentiële informatie voor uw kluis.

    ![Het tabblad informatie over Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. Selecteer in het menu kluis overzicht de optie **wijzigen** naast **abonnement** om het deel venster **resources verplaatsen** te openen.

   ![Abonnement wijzigen](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selecteer de resources die u wilt verplaatsen. u wordt aangeraden de optie **Alles selecteren** te gebruiken om alle vermelde optionele resources te selecteren.

   ![resource verplaatsen](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Selecteer het doel abonnement in de vervolg keuzelijst **abonnement** , waar u de kluis wilt verplaatsen.
6. Als u de doel resource groep wilt toevoegen, selecteert u in de vervolg keuzelijst **resource groep** een bestaande resource groep of selecteert u **een nieuwe groep maken** .

   ![Abonnement toevoegen](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Selecteer **Ik begrijp dat hulp middelen en scripts die zijn gekoppeld aan verplaatste resources, niet werken totdat ze de optie nieuwe resource-id's gebruiken** om te bevestigen, en selecteer vervolgens **OK**.

> [!NOTE]
> Back-up tussen meerdere abonnementen (RS-kluis en beveiligde Vm's bevinden zich in verschillende abonnementen) is geen ondersteund scenario. Daarnaast kan de optie voor opslag redundantie van lokale redundante opslag (LRS) naar globale redundante opslag (GRS) en vice versa niet worden gewijzigd tijdens de bewerking voor het verplaatsen van de kluis.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Power shell gebruiken om Recovery Services kluis te verplaatsen

Als u een Recovery Services kluis wilt verplaatsen naar een andere resource groep, gebruikt u de `Move-AzureRMResource` cmdlet. `Move-AzureRMResource` vereist de resource naam en het resource type. U kunt beide van de `Get-AzureRmRecoveryServicesVault` cmdlet ophalen.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Als u de resources naar een ander abonnement wilt verplaatsen, neemt u de `-DestinationSubscriptionId` para meter op.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Nadat u de bovenstaande cmdlets hebt uitgevoerd, wordt u gevraagd om te bevestigen dat u de opgegeven resources wilt verplaatsen. Typ **Y** om te bevestigen. Na een geslaagde validatie wordt de resource verplaatst.

## <a name="use-cli-to-move-recovery-services-vault"></a>CLI gebruiken om Recovery Services kluis te verplaatsen

Als u een Recovery Services kluis wilt verplaatsen naar een andere resource groep, gebruikt u de volgende cmdlet:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Als u wilt overstappen op een nieuw abonnement, geeft u de `--destination-subscription-id` para meter op.

## <a name="post-migration"></a>Na de migratie

1. De toegangs controle voor de resource groepen instellen/controleren.  
2. De functie voor het rapporteren en controleren van back-ups moet opnieuw worden geconfigureerd voor de kluis nadat de verplaatsing is voltooid. De vorige configuratie gaat verloren tijdens de verplaatsings bewerking.

## <a name="move-an-azure-virtual-machine-to-a-different-recovery-service-vault"></a>Verplaats een virtuele machine van Azure naar een andere Recovery service-kluis. 

Als u een virtuele machine van Azure wilt verplaatsen waarvoor Azure Backup is ingeschakeld, hebt u twee opties. Ze zijn afhankelijk van uw bedrijfs vereisten:

- [U hoeft geen eerdere back-upgegevens te bewaren](#dont-need-to-preserve-previous-backed-up-data)
- [Moet eerdere back-upgegevens behouden](#must-preserve-previous-backed-up-data)

### <a name="dont-need-to-preserve-previous-backed-up-data"></a>U hoeft geen eerdere back-upgegevens te bewaren

Ter bescherming van workloads in een nieuwe kluis moeten de huidige beveiliging en gegevens worden verwijderd in de oude kluis en moet de back-up opnieuw worden geconfigureerd.

>[!WARNING]
>De volgende bewerking is destructief en kan niet ongedaan worden gemaakt. Alle back-upgegevens en back-upitems die aan de beveiligde server zijn gekoppeld, worden definitief verwijderd. Ga zorgvuldig te werk.

**Huidige beveiliging op de oude kluis stoppen en verwijderen:**

1. Schakel de optie voorlopig verwijderen uit in de kluis eigenschappen. Volg [deze stappen](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) om het uitvoeren van de tijdelijke verwijdering uit te scha kelen.

2. Stop de beveiliging en verwijder back-ups van de huidige kluis. Selecteer in het menu van het kluis dashboard **Back-upitems**. Items die hier worden weer gegeven en die moeten worden verplaatst naar de nieuwe kluis, moeten samen met hun back-upgegevens worden verwijderd. Zie [beveiligde items verwijderen in de Cloud](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) en [beveiligde items on-premises verwijderen](backup-azure-delete-vault.md#delete-protected-items-on-premises).

3. Als u van plan bent om AFS (Azure-bestands shares), SQL-servers of SAP HANA servers te verplaatsen, moet u de registratie ervan opheffen. Selecteer in het menu van het kluis dashboard **back-upinfrastructuur**. Zie [de registratie van de SQL-Server](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)ongedaan maken, de [registratie van een opslag account voor Azure-bestands shares ongedaan](manage-afs-backup.md#unregister-a-storage-account)maken en [de registratie van een SAP Hana exemplaar opheffen](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

4. Zodra ze zijn verwijderd uit de oude kluis, gaat u door met het configureren van de back-ups voor uw werk belasting in de nieuwe kluis.

### <a name="must-preserve-previous-backed-up-data"></a>Moet eerdere back-upgegevens behouden

Als u de huidige beveiligde gegevens in de oude kluis wilt behouden en de beveiliging in een nieuwe kluis wilt voortzetten, zijn er beperkte opties voor sommige werk belastingen:

- Voor MARS kunt u de [beveiliging stoppen met het bewaren van gegevens](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) en de agent registreren in de nieuwe kluis.

  - Azure Backup service blijft alle bestaande herstel punten van de oude kluis behouden.
  - U moet betalen om de herstel punten in de oude kluis te blijven gebruiken.
  - U kunt de back-upgegevens alleen herstellen voor niet-verlopen herstel punten in de oude kluis.
  - Er moet een nieuwe eerste replica van de gegevens worden gemaakt op de nieuwe kluis.

- Voor een Azure-VM kunt u de [beveiliging stoppen met het bewaren van gegevens](backup-azure-manage-vms.md#stop-protecting-a-vm) voor de virtuele machine in de oude kluis, de VM verplaatsen naar een andere resource groep en de virtuele machine vervolgens beveiligen in de nieuwe kluis. Zie de [richt lijnen en beperkingen](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) voor het verplaatsen van een virtuele machine naar een andere resource groep.

  Een virtuele machine kan in slechts één kluis tegelijk worden beveiligd. De virtuele machine in de nieuwe resource groep kan echter worden beveiligd op de nieuwe kluis, aangezien deze wordt beschouwd als een andere virtuele machine.

  - Azure Backup-Service behoudt de herstel punten waarvan een back-up is gemaakt op de oude kluis.
  - U moet betalen om de herstel punten in de oude kluis te blijven (Zie [Azure backup prijzen](azure-backup-pricing.md) voor meer informatie).
  - U kunt de virtuele machine, indien nodig, herstellen vanuit de oude kluis.
  - De eerste back-up op de nieuwe kluis van de virtuele machine in de nieuwe resource is een eerste replica.

## <a name="next-steps"></a>Volgende stappen

U kunt verschillende soorten resources verplaatsen tussen resource groepen en abonnementen.

Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).