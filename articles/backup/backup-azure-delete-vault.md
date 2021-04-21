---
title: Een Recovery Services Microsoft Azure kluis verwijderen
description: In dit artikel leert u hoe u afhankelijkheden verwijdert en vervolgens een Azure Backup Recovery Services-kluis verwijdert.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: bb6be070ac0fb408ac37c8ae7b003b54da5d6dea
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773653"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Een Recovery Services Azure Backup kluis verwijderen

In dit artikel wordt beschreven hoe u een [Azure Backup](backup-overview.md) Recovery Services-kluis verwijdert. Het bevat instructies voor het verwijderen van afhankelijkheden en vervolgens het verwijderen van een kluis.

## <a name="before-you-start"></a>Voordat u begint

U kunt een Recovery Services-kluis met een van de volgende afhankelijkheden niet verwijderen:

- U kunt een kluis met beveiligde gegevensbronnen (bijvoorbeeld IaaS-VM's, SQL-databases, Azure-bestands shares) niet verwijderen.
- U kunt een kluis met back-upgegevens niet verwijderen. Zodra de back-upgegevens zijn verwijderd, krijgt deze de status Voorlopig verwijderd.
- U kunt een kluis met back-upgegevens met de status Verwijderd niet verwijderen.
- U kunt een kluis met geregistreerde opslagaccounts niet verwijderen.

Als u de kluis probeert te verwijderen zonder de afhankelijkheden te verwijderen, krijgt u een van de volgende foutberichten te zien:

- De kluis kan niet worden verwijderd, omdat er nog resources in de kluis aanwezig zijn. Zorg ervoor dat er geen back-upitems, beveiligde servers of back-upbeheerservers aan deze kluis zijn gekoppeld. Verwijder de registratie van de volgende containers die aan deze kluis zijn gekoppeld voordat u doorgaat met verwijderen.

- De kluis van Recovery Services kan niet worden verwijderd, omdat er zich in de kluis back-upitems bevinden met de status Voorlopig verwijderd. De tijdelijke verwijderde items worden na 14 dagen verwijderen definitief verwijderd. Probeer de kluis te verwijderen nadat de back-upitems permanent zijn verwijderd en er geen item met de status 'soft deleted' in de kluis is. Zie Voor meer informatie [Soft delete for Azure Backup](./backup-azure-security-feature-cloud.md).

## <a name="proper-way-to-delete-a-vault"></a>De juiste manier om een kluis te verwijderen

>[!WARNING]
>De volgende bewerking is destructief en kan niet ongedaan worden gemaakt. Alle back-upgegevens en back-upitems die aan de beveiligde server zijn gekoppeld, worden permanent verwijderd. Ga zorgvuldig te werk.

Als u een kluis op de juiste manier wilt verwijderen, moet u de stappen in deze volgorde volgen:

- **Stap 1:** schakel de functie voor het verwijderen van de functie voor soft delete uit. [Kijk hier voor](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) de stappen voor het uitschakelen van het verwijderen van de functie.

- **Stap 2:** nadat u het verwijderen van de zachte status hebt uitschakelen, controleert u of er items zijn die eerder de status Zacht verwijderd hebben. Als er items zijn die de status  'soft deleted' hebben, moet u het verwijderen van items verwijderen.  [Volg deze stappen om](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items) te zoeken naar items die u wilt verwijderen en deze permanent te verwijderen.

- **Stap 3:** u moet alle volgende drie locaties controleren om te controleren of er beveiligde items zijn:

  - **Met de cloud beveiligde items:** ga naar het menu van het kluisdashboard > **Back-upitems**. Alle items die hier worden vermeld, moeten worden verwijderd met **Back-up** stoppen of Back-upgegevens **verwijderen,** samen met de back-upgegevens.  [Volg deze stappen om](#delete-protected-items-in-the-cloud) deze items te verwijderen.
  - **SQL Server exemplaar: ga** naar het menu van het kluisdashboard > **Back-up maken van infrastructuur** beveiligde  >  **servers.** Selecteer in Beveiligde servers de server waarop u de registratie ongedaan wilt maken. Als u de kluis wilt verwijderen, moet u de registratie van alle servers ongedaan maken. Klik met de rechtermuisknop op de beveiligde server en selecteer **Registratie ongedaan maken.**
  - **Met MARS beveiligde servers:** ga naar het menu van het kluisdashboard > **Back-upinfrastructuur**  >  **beveiligde servers**. Als u met MARS beveiligde servers hebt, moeten alle items die hier worden vermeld, samen met de back-upgegevens worden verwijderd. [Volg deze stappen om](#delete-protected-items-on-premises) met MARS beveiligde servers te verwijderen.
  - **MABS- of DPM-beheerservers:** ga naar het menu van het kluisdashboard > **Backup Infrastructure** Backup Management  >  **Servers .** Als u DPM of Azure Backup Server (MABS) hebt, moeten alle items die hier worden vermeld, samen met de back-upgegevens worden verwijderd of uitgeschreven. [Volg deze stappen om](#delete-protected-items-on-premises) de beheerservers te verwijderen.

- **Stap 4:** u moet ervoor zorgen dat alle geregistreerde opslagaccounts worden verwijderd. Ga naar het menu van het kluisdashboard > **Back-up maken van**  >  **infrastructuuropslagaccounts.** Als hier opslagaccounts worden vermeld, moet u de registratie van alle accounts ongedaan maken. Zie Registratie van een opslagaccount ongedaan maken voor meer informatie over het ongedaan maken van [de registratie van het account.](manage-afs-backup.md#unregister-a-storage-account)
- **Stap 5:** zorg ervoor dat er geen privé-eindpunten zijn gemaakt voor de kluis. Ga naar het menu van  het kluisdashboard > Privé-eindpuntVerbindingen onder Instellingen > als er privé-eindpuntverbindingen zijn gemaakt of geprobeerd te worden gemaakt in de kluis. Zorg ervoor dat ze worden verwijderd voordat u doorgaat met het verwijderen van de kluis. 

Nadat u deze stappen hebt voltooid, kunt u doorgaan met het [verwijderen van de kluis](#delete-the-recovery-services-vault).

Als u geen beveiligde items on-premises of in de cloud hebt, maar nog steeds de fout krijgt bij het verwijderen van de kluis, voert u de stappen uit in De [Recovery Services-kluis](#delete-the-recovery-services-vault-by-using-azure-resource-manager) verwijderen met behulp van Azure Resource Manager

## <a name="delete-protected-items-in-the-cloud"></a>Beveiligde items in de cloud verwijderen

Lees eerst de sectie **[Voordat u begint](#before-you-start)** om inzicht te krijgen in de afhankelijkheden en het verwijderingsproces voor de kluis.

Voer de volgende stappen uit om de beveiliging te stoppen en de back-upgegevens te verwijderen:

1. Ga vanuit de portal naar **Recovery Services-kluis** en ga vervolgens naar **Back-upitems.** Selecteer vervolgens  in de lijst Type back-upbeheer de beveiligde items in de cloud (bijvoorbeeld Azure Virtual Machines, Azure Storage [de Azure Files-service] of SQL Server in Azure Virtual Machines).

    ![Selecteer het back-uptype.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. U ziet een lijst met alle items voor de categorie. Klik met de rechtermuisknop om het back-upitem te selecteren. Afhankelijk van of het back-upitem al dan niet is beveiligd, wordt in het menu het deelvenster **Back-up** stoppen of het **deelvenster Back-upgegevens** verwijderen weergegeven.

    - Als het **deelvenster Back-up** stoppen wordt weergegeven, selecteert **u Back-upgegevens** verwijderen in de vervolgkeuzelijst. Voer de naam van het back-upitem in (dit veld is hoofd-gevoelig) en selecteer vervolgens een reden in de vervolgkeuzelijst. Voer uw opmerkingen in, als u deze hebt. Selecteer vervolgens **Back-up stoppen.**

        ![Het deelvenster Back-up stoppen.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Als het **deelvenster Back-upgegevens** verwijderen wordt weergegeven, voert u de naam van het back-upitem in (dit veld is hoofd- en hoofdgevoelig) en selecteert u vervolgens een reden in de vervolgkeuzelijst. Voer uw opmerkingen in, indien u die hebt. Selecteer vervolgens **Verwijderen**.

         ![Het deelvenster Back-upgegevens verwijderen.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

   Met deze optie worden geplande back-ups verwijderd en worden ook back-ups op aanvraag verwijderd.
3. Controleer het **meldingspictogram:** ![ het meldingspictogram.](./media/backup-azure-delete-vault/messages.png) Nadat het proces is voltooien, geeft de service het volgende bericht weer: Back-up stoppen en back-upgegevens verwijderen voor "Back-upitem" .  *De bewerking is voltooid.*
4. Selecteer **Vernieuwen** in het menu **Back-upitems** om ervoor te zorgen dat het back-upitem is verwijderd.

      ![De pagina Back-upitems verwijderen.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Beveiligde items on-premises verwijderen

Lees eerst de sectie **[Voordat u begint](#before-you-start)** om inzicht te krijgen in de afhankelijkheden en het verwijderingsproces voor de kluis.

1. Selecteer back-upinfrastructuur in het menu van het **kluisdashboard.**
2. Afhankelijk van uw on-premises scenario kiest u een van de volgende opties:

      - Voor MARS selecteert u **Beveiligde servers** en vervolgens  **Azure Backup Agent**. Selecteer vervolgens de server die u wilt verwijderen.

        ![Selecteer voor MARS uw kluis om het dashboard te openen.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Selecteer back-upbeheerservers voor MABS **of** DPM. Selecteer vervolgens de server die u wilt verwijderen.

          ![Selecteer voor MABS of DPM uw kluis om het dashboard te openen.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Het **deelvenster** Verwijderen wordt weergegeven met een waarschuwingsbericht.

     ![Het deelvenster Verwijderen.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Bekijk het waarschuwingsbericht en de instructies in het selectievakje toestemming.
    > [!NOTE]
    >
    >- Als de beveiligde server is gesynchroniseerd met Azure-services en er back-upitems bestaan, wordt in het toestemmings selectievakje het aantal afhankelijke back-upitems en de koppeling weergegeven om de back-upitems weer te geven.
    >- Als de beveiligde server niet is gesynchroniseerd met Azure-services en er back-upitems bestaan, wordt in het toestemmingsvak alleen het aantal back-upitems weergegeven.
    >- Als er geen back-upitems zijn, wordt in het toestemmings selectievakje om verwijdering gevraagd.

4. Schakel het selectievakje toestemming in en selecteer **vervolgens Verwijderen.**

5. Controleer het **meldingspictogram** ![ Back-upgegevens ](./media/backup-azure-delete-vault/messages.png) verwijderen. Nadat de bewerking is uitgevoerd, wordt het volgende bericht weergegeven: Back-up stoppen en *back-upgegevens verwijderen voor Back-upitem.* *De bewerking is voltooid.*
6. Selecteer **Vernieuwen** in het menu **Back-upitems** om ervoor te zorgen dat het back-upitem wordt verwijderd.

>[!NOTE]
>Als u een on-premises beveiligd item verwijdert uit een portal die afhankelijkheden bevat, ontvangt u een waarschuwing met de melding 'De registratie van de server verwijderen is een destructieve bewerking en kan niet ongedaan worden gemaakt. Alle back-upgegevens (herstelpunten die nodig zijn om de gegevens te herstellen) en back-upitems die zijn gekoppeld aan de beveiligde server, worden permanent verwijderd.

Nadat dit proces is voltooien, kunt u de back-upitems verwijderen uit de beheerconsole:

- [Back-upitems verwijderen uit de MARS-beheerconsole](#delete-backup-items-from-the-mars-management-console)
- [Back-upitems verwijderen uit de MABS- of DPM-beheerconsole](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Back-upitems verwijderen uit de MARS-beheerconsole

>[!NOTE]
>Als u de bronmachine hebt verwijderd of verloren zonder de back-up te stoppen, mislukt de volgende geplande back-up. Het oude herstelpunt verloopt volgens het beleid, maar het laatste herstelpunt blijft altijd behouden totdat u de back-up stopt en de gegevens verwijdert. U kunt dit doen door de stappen in deze [sectie te volgen.](#delete-protected-items-on-premises)

1. Open de MARS-beheerconsole, ga naar het **deelvenster Acties** en selecteer **Back-up plannen.**
2. Selecteer op **de pagina Een geplande back-up** wijzigen of stoppen de optie Gebruik van dit back-upschema stoppen en verwijder alle **opgeslagen back-ups.** Selecteer vervolgens **Volgende**.

    ![Een geplande back-up wijzigen of stoppen.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Selecteer op **de pagina Een geplande back-up** stoppen de optie **Voltooien.**

    ![Een geplande back-up stoppen.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. U wordt gevraagd een beveiligingspincode (persoonlijk identificatienummer) in te voeren die u handmatig moet genereren. Hiervoor moet u zich eerst aanmelden bij de Azure Portal.
5. Ga naar **Instellingen van Recovery Services-kluisEigenschappen**  >    >  .
6. Selecteer **genereren onder Pincode** voor **beveiliging.** Kopieer deze pincode. De pincode is slechts vijf minuten geldig.
7. Plak de pincode in de beheerconsole en selecteer **OK.**

    ![Genereer een beveiligingspin pincode.](./media/backup-azure-delete-vault/security-pin.png)

8. Op de **pagina Voortgang van back-up** wijzigen wordt het volgende bericht weergegeven: Verwijderde back-upgegevens worden *14 dagen bewaard. Na die tijd worden de back-upgegevens permanent verwijderd.*  

    ![Verwijder de back-upinfrastructuur.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Nadat u de on-premises back-upitems hebt verwijderd, volgt u de volgende stappen vanuit de portal.

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>Back-upitems verwijderen uit de MABS- of DPM-beheerconsole

>[!NOTE]
>Als u de bronmachine hebt verwijderd of verloren zonder de back-up te stoppen, mislukt de volgende geplande back-up. Het oude herstelpunt verloopt volgens het beleid, maar het laatste herstelpunt blijft altijd behouden totdat u de back-up stopt en de gegevens verwijdert. U kunt dit doen door de stappen in deze [sectie te volgen.](#delete-protected-items-on-premises)

Er zijn twee methoden die u kunt gebruiken om back-upitems te verwijderen uit de MABS- of DPM-beheerconsole.

#### <a name="method-1"></a>Methode 1

Ga als volgt te werk om de beveiliging te stoppen en back-upgegevens te verwijderen:

1. Open de DPM Administrator-console selecteer vervolgens **Beveiliging** op de navigatiebalk.
2. Selecteer in het weergavevenster het lid van de beveiligingsgroep dat u wilt verwijderen. Klik met de rechtermuisknop om de optie **Beveiliging van groepsleden stoppen te** selecteren.
3. Selecteer in **het dialoogvenster Beveiliging** stoppen de optie Beveiligde gegevens **verwijderen** en schakel vervolgens het selectievakje **Opslag online** verwijderen in. Selecteer vervolgens **Beveiliging stoppen.**

    ![Selecteer Beveiligde gegevens verwijderen in het deelvenster Beveiliging stoppen.](./media/backup-azure-delete-vault/delete-storage-online.png)

    De status van het beveiligde lid wordt gewijzigd *in inactieve replica die beschikbaar is.*

4. Klik met de rechtermuisknop op de inactieve beveiligingsgroep en **selecteer Inactieve beveiliging verwijderen.**

    ![Inactieve beveiliging verwijderen.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Schakel in **het venster Inactieve** beveiliging verwijderen het selectievakje **Online storage** verwijderen in en selecteer **vervolgens OK.**

    ![Verwijder online storage.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Methode 2

Open de **MABS-beheerconsole** **of DPM-beheerconsole.** Schakel **onder Methode voor gegevensbeveiliging selecteren** het selectievakje Ik wil  **onlinebeveiliging** uit.

  ![Selecteer de methode voor gegevensbeveiliging.](./media/backup-azure-delete-vault/data-protection-method.png)

Nadat u de on-premises back-upitems hebt verwijderd, volgt u de volgende stappen vanuit de portal.

## <a name="delete-the-recovery-services-vault"></a>Recovery Services-kluis verwijderen

1. Wanneer alle afhankelijkheden zijn verwijderd, schuift u naar het deelvenster **Essentials** in het kluismenu.
2. Controleer of er geen back-upitems, back-upbeheerservers of gerepliceerde items worden vermeld. Als er nog steeds items in de kluis worden weergegeven, raadpleegt u de [sectie Voordat u begint.](#before-you-start)

3. Wanneer de kluis geen items meer heeft, selecteert u **Verwijderen** op het kluisdashboard.

    ![Selecteer Verwijderen op het kluisdashboard.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Selecteer **Ja** om te controleren of u de kluis wilt verwijderen. De kluis wordt verwijderd. De portal gaat terug naar het menu **Nieuwe** service.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>De Recovery Services-kluis verwijderen met behulp van PowerShell

Lees eerst de sectie **[Voordat u begint](#before-you-start)** om inzicht te krijgen in de afhankelijkheden en het verwijderingsproces voor de kluis.

De beveiliging stoppen en de back-upgegevens verwijderen:

- Als u SQL gebruikt in azure-VM's voor back-ups en automatische beveiliging voor SQL-exemplaren hebt ingeschakeld, schakelt u eerst de automatische beveiliging uit.

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

  [Meer informatie](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) over het uitschakelen van beveiliging voor een Azure Backup beveiligd item.

- Stop de beveiliging en verwijder gegevens voor alle back-upbeveiligingsitems in de cloud (bijvoorbeeld: IaaS-VM, Azure-bestands share, bijvoorbeeld):

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

    [Meer informatie](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)   met about wordt de beveiliging voor een item met back-upbeveiliging uitgeschakeld.

- Gebruik de volgende PowerShell-opdracht om de back-upgegevens uit elke MARS PowerShell-module te verwijderen voor on-premises bestanden en mappen die worden beveiligd met behulp van mars-back-up (Azure Backup Agent) in Azure:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Daarna wordt de volgende prompt weergegeven:

    *Microsoft Azure Backup Weet u zeker dat u dit back-upbeleid wilt verwijderen? Verwijderde back-upgegevens worden 14 dagen bewaard. Na die tijd worden back-upgegevens permanent verwijderd. <br/> [Y] Ja [A] Ja op alle [N] Nee [L] Nee voor alle [S] suspend [?] Help (standaard 'Y'):*

- Voor on-premises machines die zijn beveiligd met MABS (Microsoft Azure Backup Server) of DPM (System Center Data Protection Manager) naar Azure, gebruikt u de volgende opdracht om de back-upgegevens in Azure te verwijderen.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Daarna wordt de volgende prompt weergegeven:

   *Microsoft Azure Backup* Weet u zeker dat u dit back-upbeleid wilt verwijderen? Verwijderde back-upgegevens worden 14 dagen bewaard. Na die periode worden de back-upgegevens permanent verwijderd. <br/>
   [Y] Ja [A] Ja op alle [N] Nee [L] Nee voor alle [S] suspend [?] Help (standaard 'Y'):*

Nadat u de back-upgegevens heeft verwijderd, moet u de registratie van on-premises containers en beheerservers verwijderen.

- Voor on-premises bestanden en mappen die zijn beveiligd met Azure Backup Agent (MARS) die een back-up maakt naar Azure:

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

    [Meer informatie over](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) het uitschrijven van de registratie van een Windows Server of een andere container uit de kluis.

- Voor on-premises machines die worden beveiligd met MABS (Microsoft Azure Backup Server) of DPM naar Azure (System Center Data Protection Beheren:

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

    [Meer informatie over](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) het uitschrijven van de registratie van een Backup-beheercontainer uit de kluis.

Nadat u een back-up van gegevens permanent heeft verwijderd en alle containers niet meer heeft geregistreerd, gaat u verder met het verwijderen van de kluis.

Een Recovery Services-kluis verwijderen:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Meer informatie](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) over het verwijderen van een Recovery Services-kluis.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>De Recovery Services-kluis verwijderen met cli

Lees eerst de sectie **[Voordat u begint](#before-you-start)** om inzicht te krijgen in de afhankelijkheden en het verwijderingsproces voor de kluis.

> [!NOTE]
> Momenteel ondersteunt Azure Backup CLI alleen het beheer van back-ups van virtuele Azure-VM's. De volgende opdracht voor het verwijderen van de kluis werkt dus alleen als de kluis Azure VM-back-ups bevat. U kunt een kluis niet verwijderen met behulp Azure Backup CLI, als de kluis een ander back-upitem van het type bevat dan Azure-VM's.

Voer de volgende stappen uit om een bestaande Recovery Services-kluis te verwijderen:

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

    Zie dit artikel voor [meer informatie.](/cli/azure/backup/protection#az_backup_protection_disable)

- Een bestaande Recovery Services-kluis verwijderen:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Zie dit artikel voor meer [informatie](/cli/azure/backup/vault)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>De Recovery Services-kluis verwijderen met behulp van Azure Resource Manager

Deze optie voor het verwijderen van de Recovery Services-kluis wordt alleen aanbevolen als alle afhankelijkheden zijn verwijderd en u nog steeds de fout voor het verwijderen van *de kluis krijgt.* Probeer een of alle van de volgende tips:

- Controleer in **het deelvenster Essentials** in het kluismenu of er geen back-upitems, back-upbeheerservers of gerepliceerde items worden vermeld. Als er back-upitems zijn, raadpleegt u de [sectie Voordat u begint.](#before-you-start)
- Probeer [de kluis opnieuw te verwijderen uit de portal.](#delete-the-recovery-services-vault)
- Als alle afhankelijkheden zijn verwijderd en u nog steeds de fout voor het verwijderen van de kluis *krijgt,* gebruikt u het hulpprogramma ARMClient om de volgende stappen uit te voeren (na de opmerking).

1. Ga naar [chocolatey.org](https://chocolatey.org/) Chocolatey te downloaden en te installeren. Installeer vervolgens ARMClient door de volgende opdracht uit te voeren:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Meld u aan bij uw Azure-account en voer de volgende opdracht uit:

    `ARMClient.exe login [environment name]`

3. Verzamel in Azure Portal de abonnements-id en de naam van de resourcegroep voor de kluis die u wilt verwijderen.

Zie ARMClient README voor meer informatie over de [ARMClient-opdracht.](https://github.com/projectkudu/ARMClient/blob/master/README.md)

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>De client Azure Resource Manager Recovery Services gebruiken om een Recovery Services-kluis te verwijderen

1. Voer de volgende opdracht uit met behulp van uw abonnements-id, resourcegroepnaam en kluisnaam. Als u geen afhankelijkheden hebt, wordt de kluis verwijderd wanneer u de volgende opdracht hebt uitgevoerd:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. Als de kluis niet leeg is, ontvangt u het volgende foutbericht: De kluis kan niet worden verwijderd omdat deze *kluis bestaande resources bevat.* Voer de volgende opdracht uit om een beveiligd item of een beveiligde container in een kluis te verwijderen:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Zorg Azure Portal in de Azure Portal dat de kluis is verwijderd.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Recovery Services-kluizen](backup-azure-recovery-services-vault-overview.md) 
 [Meer informatie over het bewaken en beheren van Recovery Services-kluizen](backup-azure-manage-windows-server.md)
