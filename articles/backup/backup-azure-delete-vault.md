---
title: Een Microsoft Azure Recovery Services kluis verwijderen
description: In dit artikel leert u hoe u afhankelijkheden kunt verwijderen en vervolgens een Azure Backup Recovery Services kluis kunt verwijderen.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: 8df2528edebbb654e501b8aaaac91c24d932604c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670559"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Een Azure Backup Recovery Services kluis verwijderen

In dit artikel wordt beschreven hoe u een [Azure Backup](backup-overview.md) Recovery Services kluis verwijdert. Het bevat instructies voor het verwijderen van afhankelijkheden en het verwijderen van een kluis.

## <a name="before-you-start"></a>Voordat u begint

U kunt een Recovery Services-kluis met een van de volgende afhankelijkheden niet verwijderen:

- U kunt geen kluis verwijderen die beveiligde gegevens bronnen bevat (bijvoorbeeld IaaS Vm's, SQL-data bases, Azure-bestands shares).
- U kunt geen kluis verwijderen die back-upgegevens bevat. Zodra de back-upgegevens zijn verwijderd, krijgt deze de status Voorlopig verwijderd.
- U kunt geen kluis verwijderen die back-upgegevens bevat in de modus voorlopig verwijderd.
- U kunt geen kluis verwijderen die geregistreerde opslag accounts heeft.

Als u probeert de kluis te verwijderen zonder de afhankelijkheden te verwijderen, ontvangt u een van de volgende fout berichten:

- De kluis kan niet worden verwijderd, omdat er nog resources in de kluis aanwezig zijn. Controleer of er geen back-upitems, beveiligde servers of back-upbeheerser vers zijn gekoppeld aan deze kluis. Hef de registratie van de volgende containers die zijn gekoppeld aan deze kluis op voordat u doorgaat met verwijderen.

- De kluis van Recovery Services kan niet worden verwijderd, omdat er zich in de kluis back-upitems bevinden met de status Voorlopig verwijderd. De voorlopig verwijderde items worden definitief verwijderd na 14 dagen na de Verwijder bewerking. Probeer het verwijderen van de kluis nadat de back-upitems definitief zijn verwijderd en er geen item met de status zacht verwijderd is gebleven in de kluis. Zie [voorlopig verwijderen voor Azure backup](./backup-azure-security-feature-cloud.md)voor meer informatie.

## <a name="proper-way-to-delete-a-vault"></a>Juiste manier om een kluis te verwijderen

>[!WARNING]
>De volgende bewerking is destructief en kan niet ongedaan worden gemaakt. Alle back-upgegevens en back-upitems die aan de beveiligde server zijn gekoppeld, worden definitief verwijderd. Ga zorgvuldig te werk.

Als u een kluis goed wilt verwijderen, moet u de stappen in deze volg orde volgen:

- **Stap 1**: Schakel de functie voor voorlopig verwijderen uit. [Hier vindt](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) u een overzicht van de stappen voor het uitschakelen van de tijdelijke verwijdering.

- **Stap 2**: Controleer na het uitschakelen van zacht verwijderen of er eerder items met de status voorlopig verwijderd zijn. Als er items met de status zacht verwijderd zijn, moet u deze *verwijderen* en opnieuw *verwijderen* . [Volg deze stappen](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items) om items te zoeken die u voorlopig verwijdert en deze permanent te verwijderen.

- **Stap 3**: u moet de volgende drie locaties controleren om te controleren of er beveiligde items zijn:

  - In de **Cloud beveiligde items**: Ga naar het menu van het kluis dashboard > **Back-upitems**. Alle items die hier worden vermeld, moeten worden verwijderd met **back-up stoppen** of **back-upgegevens verwijderen** samen met de back-upgegevens.  [Volg deze stappen](#delete-protected-items-in-the-cloud) om deze items te verwijderen.
  - **SQL Server-exemplaar**: Ga naar het menu van het kluis dashboard > beveiligde **-infrastructuur**  >  **servers**. Selecteer in beveiligde servers de server die u wilt verwijderen. Als u de kluis wilt verwijderen, moet u de registratie van alle servers ongedaan maken. Klik met de rechter muisknop op de beveiligde server en selecteer **verwijderen ongedaan maken**.
  - **Mars-beveiligde servers**: Ga naar het menu van het kluis dashboard > beveiligde servers met **back-upinfrastructuur**  >  . Als u MARS-beveiligde servers hebt, moeten alle items die hier worden vermeld, samen met de bijbehorende back-upgegevens worden verwijderd. [Volg deze stappen om de](#delete-protected-items-on-premises) Mars-beveiligde servers te verwijderen.
  - **MABS-of DPM-beheerser vers**: Ga naar het menu van het kluis dashboard > **Backup-infrastructuur**  >  **beheer servers**. Als u DPM of Azure Backup Server (MABS) hebt, moeten alle items die hier worden vermeld, worden verwijderd of geregistreerd samen met hun back-upgegevens. [Volg deze stappen](#delete-protected-items-on-premises) voor het verwijderen van de beheerser vers.

- **Stap 4**: u moet ervoor zorgen dat alle geregistreerde opslag accounts worden verwijderd. Ga naar het menu van het kluis dashboard > opslag accounts voor **back-upinfrastructuur**  >  . Als u hier opslag accounts hebt die hier worden vermeld, moet u de registratie ervan ongedaan maken. Zie [registratie van een opslag account ongedaan maken](manage-afs-backup.md#unregister-a-storage-account)voor meer informatie over het ongedaan maken van de registratie van het account.

Nadat u deze stappen hebt voltooid, kunt u door gaan met [het verwijderen van de kluis](#delete-the-recovery-services-vault).

Als u geen beveiligde items on-premises of in de Cloud hebt, maar nog steeds de kluis verwijdert, voert u de stappen in [de Recovery Services kluis verwijderen uit met behulp van Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager)

## <a name="delete-protected-items-in-the-cloud"></a>Beveiligde items in de Cloud verwijderen

Lees eerst de sectie **[voordat u begint](#before-you-start)** om inzicht te krijgen in de afhankelijkheden en het verwijderings proces van de kluis.

Voer de volgende stappen uit om de beveiliging te stoppen en de back-upgegevens te verwijderen:

1. Ga in de portal naar **Recovery Services kluis** en ga vervolgens naar **Back-upitems**. Selecteer vervolgens in de lijst **type back-upbeheer** de beveiligde items in de Cloud (bijvoorbeeld Azure Virtual Machines, Azure Storage [de Azure files Service] of SQL Server in azure virtual machines).

    ![Selecteer het type back-up.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. U ziet een lijst met alle items voor de categorie. Klik met de rechter muisknop om het back-upitem te selecteren. Afhankelijk van of het back-upitem is beveiligd of niet, wordt in het menu het deel venster **back-up stoppen** of het deel venster **back-upgegevens verwijderen** weer gegeven.

    - Als het deel venster **back-up stoppen** wordt weer gegeven, selecteert u **back-upgegevens verwijderen** in de vervolg keuzelijst. Voer de naam van het back-upitem in (dit veld is hoofdletter gevoelig) en selecteer vervolgens een reden in de vervolg keuzelijst. Voer uw opmerkingen in, als u die hebt. Selecteer vervolgens **back-up stoppen**.

        ![Het deel venster back-up stoppen.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Als het deel venster **back-upgegevens verwijderen** wordt weer gegeven, voert u de naam van het back-upitem in (dit veld is hoofdletter gevoelig) en selecteert u vervolgens een reden in de vervolg keuzelijst. Voer uw opmerkingen in, als u die hebt. Selecteer vervolgens **Verwijderen**.

         ![Het deel venster back-upgegevens verwijderen.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

   Met deze optie worden geplande back-ups verwijderd, worden ook back-ups op aanvraag verwijderd.
3. Controleer het **meldings** pictogram: ![ het pictogram melding.](./media/backup-azure-delete-vault/messages.png) Nadat het proces is voltooid, wordt het volgende bericht weer gegeven: *back-up stoppen en back-upgegevens verwijderen voor* back-upitem *.* *De bewerking is voltooid*.
4. Selecteer **vernieuwen** in het menu **back-** upitems om er zeker van te zijn dat de back-up is verwijderd.

      ![De pagina Back-upitems verwijderen.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Beveiligde items on-premises verwijderen

Lees eerst de sectie **[voordat u begint](#before-you-start)** om inzicht te krijgen in de afhankelijkheden en het verwijderings proces van de kluis.

1. Selecteer in het menu van het kluis dashboard **back-upinfrastructuur**.
2. Afhankelijk van uw on-premises scenario kiest u een van de volgende opties:

      - Voor MARS selecteert u **beveiligde servers** en vervolgens  **Azure backup agent**. Selecteer vervolgens de server die u wilt verwijderen.

        ![Selecteer uw kluis voor MARS om het dash board ervan te openen.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Voor MABS of DPM selecteert u **Backup-beheerser vers**. Selecteer vervolgens de server die u wilt verwijderen.

          ![Voor MABS of DPM selecteert u uw kluis om het dash board ervan te openen.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Het deel venster **verwijderen** wordt weer gegeven met een waarschuwings bericht.

     ![Het deel venster verwijderen.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Bekijk het waarschuwings bericht en de instructies in het dialoog venster toestemming.
    > [!NOTE]
    >
    >- Als de beveiligde server is gesynchroniseerd met Azure-Services en er back-upitems bestaan, wordt in het selectie vakje toestemming het aantal afhankelijke back-upitems en de koppeling voor het weer geven van de back-upitems weer gegeven.
    >- Als de beveiligde server niet is gesynchroniseerd met Azure-Services en er back-upitems bestaan, wordt in het selectie vakje toestemming alleen het aantal back-upitems weer gegeven.
    >- Als er geen back-upitems zijn, wordt in het selectie vakje toestemming gevraagd om te worden verwijderd.

4. Schakel het selectie vakje toestemming in en selecteer vervolgens **verwijderen**.

5. Controleer het **meldings** pictogram ![ back-upgegevens verwijderen ](./media/backup-azure-delete-vault/messages.png) . Nadat de bewerking is voltooid, wordt het volgende bericht weer gegeven: *back-up stoppen en back-upgegevens verwijderen voor ' back-upitem '.* *De bewerking is voltooid*.
6. Selecteer **vernieuwen** in het menu **back-** upitems om er zeker van te zijn dat de back-up wordt verwijderd.

>[!NOTE]
>Als u een on-premises beveiligde item verwijdert uit een portal met afhankelijkheden, ontvangt u een waarschuwing met de melding ' het verwijderen van de server is een destructieve bewerking en kan niet ongedaan worden gemaakt. Alle back-upgegevens (herstel punten die nodig zijn om de gegevens te herstellen) en back-upitems die zijn gekoppeld aan de beveiligde server worden definitief verwijderd. "

Nadat dit proces is voltooid, kunt u de back-upitems uit de beheer console verwijderen:

- [Back-upitems verwijderen uit de MARS-beheer console](#delete-backup-items-from-the-mars-management-console)
- [Back-upitems verwijderen uit de MABS-of DPM-beheer console](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Back-upitems verwijderen uit de MARS-beheer console

>[!NOTE]
>Als u de bron machine verwijdert of kwijtraakt zonder dat de back-up wordt gestopt, mislukt de volgende geplande back-up. Het oude herstel punt verloopt volgens het beleid, maar het laatste enkele herstel punt wordt altijd bewaard totdat u de back-up stopt en de gegevens verwijdert. U kunt dit doen door de stappen in [deze sectie](#delete-protected-items-on-premises)te volgen.

1. Open de MARS-beheer console, ga naar het deel venster **acties** en selecteer **back-up plannen**.
2. Op de pagina **een geplande back-up wijzigen of stoppen** selecteert **u stoppen met het gebruik van dit back-upschema en alle opgeslagen back-ups verwijderen**. Selecteer vervolgens **Volgende**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Selecteer **volt ooien** op de pagina **een geplande back-up stoppen** .

    ![Een geplande back-up stoppen.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. U wordt gevraagd om een beveiligings pincode (persoonlijk identificatie nummer) in te voeren, die u hand matig moet genereren. Als u dit wilt doen, meldt u zich eerst aan bij de Azure Portal.
5. Ga naar **Recovery Services** eigenschappen van de kluis  >  **instellingen**  >  .
6. Onder **BEVEILIGINGS pincode** selecteert u **genereren**. Deze pincode kopiëren. De pincode is slechts vijf minuten geldig.
7. Plak de pincode in de beheer console en selecteer **OK**.

    ![Een beveiligings pincode genereren.](./media/backup-azure-delete-vault/security-pin.png)

8. Op de pagina **voortgang van back-up wijzigen** wordt het volgende bericht weer gegeven: *verwijderde back-upgegevens worden 14 dagen bewaard. Na die tijd worden de back-upgegevens permanent verwijderd.*  

    ![Verwijder de back-upinfrastructuur.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Nadat u de on-premises back-upitems hebt verwijderd, voert u de volgende stappen uit in de portal.

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>Back-upitems verwijderen uit de MABS-of DPM-beheer console

>[!NOTE]
>Als u de bron machine verwijdert of kwijtraakt zonder dat de back-up wordt gestopt, mislukt de volgende geplande back-up. Het oude herstel punt verloopt volgens het beleid, maar het laatste enkele herstel punt wordt altijd bewaard totdat u de back-up stopt en de gegevens verwijdert. U kunt dit doen door de stappen in [deze sectie](#delete-protected-items-on-premises)te volgen.

Er zijn twee methoden die u kunt gebruiken om back-upitems te verwijderen uit de MABS-of DPM-beheer console.

#### <a name="method-1"></a>Methode 1

Voer de volgende stappen uit om de beveiliging te stoppen en de back-upgegevens te verwijderen:

1. Open de DPM Administrator-console en selecteer vervolgens **beveiliging** op de navigatie balk.
2. Selecteer in het weergave paneel het lid van de beveiligings groep dat u wilt verwijderen. Klik met de rechter muisknop om de optie **beveiliging van groeps leden stoppen** te selecteren.
3. Selecteer in het dialoog venster **beveiliging stoppen** de optie **beveiligde gegevens verwijderen** en schakel vervolgens het selectie vakje **opslag online verwijderen** in. Klik vervolgens op **beveiliging stoppen**.

    ![Selecteer beveiligde gegevens verwijderen in het deel venster beveiliging stoppen.](./media/backup-azure-delete-vault/delete-storage-online.png)

    De status van de beveiligde leden wordt gewijzigd in *niet-actieve replica beschikbaar*.

4. Klik met de rechter muisknop op inactieve beveiligings groep en selecteer **inactieve beveiliging verwijderen**.

    ![Inactieve beveiliging verwijderen.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Selecteer in het venster **inactieve beveiliging verwijderen** het selectie vakje **online opslag verwijderen** en selecteer vervolgens **OK**.

    ![Verwijder de online opslag.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Methode 2

Open de beheer console van **MABS-beheer** of **DPM** . Schakel onder **methode voor gegevens beveiliging selecteren** het selectie vakje  **Ik wil online beveiliging** uit.

  ![Selecteer de methode voor gegevens beveiliging.](./media/backup-azure-delete-vault/data-protection-method.png)

Nadat u de on-premises back-upitems hebt verwijderd, voert u de volgende stappen uit in de portal.

## <a name="delete-the-recovery-services-vault"></a>Recovery Services-kluis verwijderen

1. Wanneer alle afhankelijkheden zijn verwijderd, bladert u naar het deel venster **essentiële** elementen in het menu kluis.
2. Controleer of er geen back-upitems, back-upbeheer servers of gerepliceerde items worden weer gegeven. Als items nog steeds in de kluis worden weer gegeven, raadpleegt u de sectie [voordat u begint](#before-you-start) .

3. Als er zich geen items meer in de kluis bevinden, selecteert u **verwijderen** op het kluis dashboard.

    ![Selecteer verwijderen op het kluis dashboard.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Selecteer **Ja** om te controleren of u de kluis wilt verwijderen. De kluis wordt verwijderd. De portal keert terug naar het menu **nieuwe** service.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>De Recovery Services kluis verwijderen met behulp van Power shell

Lees eerst de sectie **[voordat u begint](#before-you-start)** om inzicht te krijgen in de afhankelijkheden en het verwijderings proces van de kluis.

De beveiliging stoppen en de back-upgegevens verwijderen:

- Als u SQL gebruikt in azure Vm's back-up en automatische beveiliging voor SQL-instanties, moet u eerst de automatische beveiliging uitschakelen.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  Meer [informatie](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) over het uitschakelen van de beveiliging voor een met Azure backup beveiligd item.

- Stop de beveiliging en verwijder gegevens voor alle met back-ups beveiligde items in de Cloud (bijvoorbeeld: IaaS VM, Azure-bestands share, enzovoort):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    [Meer informatie](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)   over het uitschakelen van de beveiliging van een item dat is beveiligd met back-up.

- Gebruik de volgende Power shell-opdracht voor het verwijderen van de back-upgegevens van elke MARS Power shell-module voor on-premises bestanden en mappen die zijn beveiligd met een Azure Backup-Agent (MARS) die wordt gebruikt voor het maken van een back-up naar Azure:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Daarna wordt de volgende prompt weer gegeven:

    *Microsoft Azure Backup weet u zeker dat u dit back-upbeleid wilt verwijderen? Verwijderde back-upgegevens worden 14 dagen bewaard. Na die tijd worden de back-upgegevens permanent verwijderd. <br/> [Y] Ja [A] Ja op alle [N] Nee [L] geen naar alle [S] Suspend [?] Help (standaard is ' Y '):*

- Gebruik de volgende opdracht voor het verwijderen van de back-upgegevens in azure voor on-premises machines die zijn beveiligd met MABS (Microsoft Azure Backup Server) of DPM (System Center Data Protection Manager) naar Azure.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Daarna wordt de volgende prompt weer gegeven:

   *Microsoft Azure backup* Weet u zeker dat u dit back-upbeleid wilt verwijderen? Verwijderde back-upgegevens worden 14 dagen bewaard. Na die periode worden de back-upgegevens permanent verwijderd. <br/>
   [Y] Ja [A] Ja op alle [N] Nee [L] geen naar alle [S] Suspend [?] Help (standaard is ' Y '):*

Nadat u de back-upgegevens hebt verwijderd, moet u de registratie van alle on-premises containers en beheerser vers ongedaan maken.

- Voor on-premises bestanden en mappen die zijn beveiligd met Azure Backup-Agent (MARS) back-up naar Azure:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    Meer [informatie](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) over het ongedaan maken van de registratie van een Windows-Server of andere container uit de kluis.

- Voor on-premises machines die zijn beveiligd met MABS (Microsoft Azure Backup Server) of DPM naar Azure (System Center Data Protection beheren:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    Meer [informatie](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) over het ongedaan maken van de registratie van een container voor back-upbeheer vanuit de kluis.

Nadat u de back-ups definitief hebt verwijderd en alle containers hebt geregistreerd, gaat u verder met het verwijderen van de kluis.

Een Recovery Services kluis verwijderen:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

Meer [informatie](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) over het verwijderen van een Recovery Services kluis.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>De Recovery Services kluis verwijderen met behulp van CLI

Lees eerst de sectie **[voordat u begint](#before-you-start)** om inzicht te krijgen in de afhankelijkheden en het verwijderings proces van de kluis.

> [!NOTE]
> Azure Backup CLI ondersteunt momenteel alleen het beheer van back-ups van virtuele Azure-machines, dus de volgende opdracht voor het verwijderen van de kluis werkt alleen als de kluis back-ups van virtuele Azure-machines bevat. U kunt een kluis niet verwijderen met Azure Backup CLI als de kluis een back-upitem bevat van het type andere dan Azure-Vm's.

Als u bestaande Recovery Services kluis wilt verwijderen, voert u de volgende stappen uit:

- De beveiliging stoppen en de back-upgegevens verwijderen

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    Zie dit [artikel](/cli/azure/backup/protection#az-backup-protection-disable)voor meer informatie.

- Een bestaande Recovery Services kluis verwijderen:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Raadpleeg dit [artikel](/cli/azure/backup/vault) voor meer informatie

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>De Recovery Services kluis verwijderen door gebruik te maken van Azure Resource Manager

Deze optie voor het verwijderen van de Recovery Services kluis wordt alleen aanbevolen als alle afhankelijkheden zijn verwijderd en u nog steeds de *kluis verwijdert*. Probeer een of meer van de volgende tips:

- Controleer in het deel venster **Essentials** in het menu kluis of er geen back-upitems, back-upbeheer servers of gerepliceerde items worden weer gegeven. Als er back-upitems zijn, raadpleegt u de sectie [voordat u begint](#before-you-start) .
- Probeer [de kluis opnieuw te verwijderen uit de portal](#delete-the-recovery-services-vault) .
- Als alle afhankelijkheden worden verwijderd en u nog steeds de *kluis verwijdert*, gebruikt u het ARMClient-hulp programma om de volgende stappen uit te voeren (na de opmerking).

1. Ga naar [chocolatey.org](https://chocolatey.org/) om de chocolade te downloaden en te installeren. Installeer vervolgens ARMClient door de volgende opdracht uit te voeren:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Meld u aan bij uw Azure-account en voer de volgende opdracht uit:

    `ARMClient.exe login [environment name]`

3. Verzamel in het Azure Portal de abonnements-ID en de naam van de resource groep voor de kluis die u wilt verwijderen.

Zie [ARMCLIENT README](https://github.com/projectkudu/ARMClient/blob/master/README.md)(Engelstalig) voor meer informatie over de ARMClient-opdracht.

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>De Azure Resource Manager-client gebruiken om een Recovery Services kluis te verwijderen

1. Voer de volgende opdracht uit met de abonnements-ID, de naam van de resource groep en de naam van de kluis. Als u geen afhankelijkheden hebt, wordt de kluis verwijderd wanneer u de volgende opdracht uitvoert:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. Als de kluis niet leeg is, wordt het volgende fout bericht weer gegeven: de *kluis kan niet worden verwijderd omdat er bestaande resources zijn in deze kluis.* Als u een beveiligd item of een container binnen een kluis wilt verwijderen, voert u de volgende opdracht uit:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Zorg ervoor dat de kluis is verwijderd in de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Recovery Services kluizen](backup-azure-recovery-services-vault-overview.md) 
 [Meer informatie over het controleren en beheren van Recovery Services kluizen](backup-azure-manage-windows-server.md)
