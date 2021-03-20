---
title: Back-up van Windows-systeem status maken in azure
description: Meer informatie over het maken van een back-up van de systeem status van Windows Server-computers naar Azure.
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: 1b3573d757d2f7b1ffec9ae718aa791488960f3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91332883"
---
# <a name="back-up-windows-system-state-to-azure"></a>Back-up van Windows-systeem status maken in azure

In dit artikel wordt uitgelegd hoe u een back-up maakt van de systeem status van Windows Server naar Azure. Het is bedoeld om u te helpen door de basis beginselen.

Als u meer wilt weten over Azure Backup, lees dan dit [overzicht](backup-overview.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan waarmee u toegang hebt tot alle services van Azure.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy-for-the-vault"></a>Opslagredundantie instellen voor de kluis

Wanneer u een Recovery Services-kluis maakt, zorg er dan voor dat de opslagredundantie op de gewenste manier is geconfigureerd.

1. Selecteer in het deel venster **Recovery Services kluizen** de nieuwe kluis.

    ![De nieuwe kluis in de lijst met Recovery Services-kluizen selecteren](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Wanneer u de kluis selecteert, wordt het deel venster **Recovery Services kluis** beperkt en het deel venster instellingen (*met de naam van de kluis bovenaan*) en het deel venster kluis Details geopend.

    ![De opslagconfiguratie voor nieuwe kluis bekijken](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. In het deel venster instellingen van de nieuwe kluis gebruikt u de verticale schuif balk om naar het gedeelte beheren te gaan en selecteert u **back-upinfrastructuur**.
    Het deel venster back-upinfrastructuur wordt geopend.
3. Selecteer in het deel venster back-upinfrastructuur de optie **back-up configureren** om het deel venster **back-upconfiguratie** te openen.

    ![De opslagconfiguratie voor nieuwe kluis instellen](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Kies het type opslagreplicatie dat geschikt is voor uw kluis.

    ![Opties voor opslag configuratie](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration-for-vault.png)

    Uw kluis heeft standaard geografisch redundante opslag. Als Azure uw primaire eindpunt is voor back-upopslag, blijf dan **Geografisch redundant** gebruiken. Als Azure niet uw primaire eindpunt is voor back-upopslag, kiest u **Lokaal redundant**, zodat u de kosten voor Azure-opslag verlaagt. Lees meer over [geo-redundante](../storage/common/storage-redundancy.md#geo-redundant-storage), [lokaal redundante](../storage/common/storage-redundancy.md#locally-redundant-storage) en [zone redundante](../storage/common/storage-redundancy.md#zone-redundant-storage) opslag opties in dit [overzicht van opslag redundantie](../storage/common/storage-redundancy.md).

Nu u een kluis hebt gemaakt, configureert u deze voor het maken van een back-up van de Windows-systeem status.

## <a name="configure-the-vault"></a>De kluis configureren

1. Selecteer in het deel venster Recovery Services kluis (voor de kluis die u zojuist hebt gemaakt) de optie **back-up** in de sectie aan de slag en selecteer in het deel venster aan de slag **met back-up** de optie **back-up van doel**.

    ![Back-upinstellingen openen](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Het deel venster doel van de **back-up** wordt geopend.

    ![Deel venster back-updoel openen](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. Selecteer **On-premises** in het menu **Waar wordt uw workload uitgevoerd?**.

    U kiest **on-premises** omdat uw Windows-Server of Windows-computer een fysieke machine is die zich niet in azure bevindt.

3. Selecteer in het menu **waarvan wilt u een back-up maken?** de optie **systeem status** en selecteer **OK**.

    ![Bestanden en mappen configureren](./media/backup-azure-system-state/backup-goal-system-state.png)

    Nadat u **OK** hebt geselecteerd, wordt er een vinkje weer gegeven naast **back-updoel** en wordt het deel venster **infra structuur voorbereiden** geopend.

    ![Nu de back-updoelstelling is geconfigureerd, gaat u de infrastructuur voorbereiden](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. Selecteer **agent voor Windows Server of Windows-client downloaden** in het deel venster **infra structuur voorbereiden** .

    ![Infrastructuur voorbereiden](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Als u Windows Server Essential gebruikt, kunt u ervoor kiezen om de agent voor Windows Server Essential te downloaden. In een pop-upmenu wordt gevraagd of u MARSAgentInstaller.exe wilt uitvoeren of opslaan.

    ![Dialoogvenster MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. Selecteer in het pop-upmenu downloaden de optie **Opslaan**.

    Standaard wordt het bestand **MARSagentinstaller.exe** opgeslagen in de map Downloads. Wanneer het installatie programma is voltooid, wordt er een pop-upvenster weer gegeven waarin u wordt gevraagd of u het installatie programma wilt uitvoeren of de map wilt openen.

    ![MARS-installatie programma is voltooid](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    U hoeft de agent nog niet te installeren. U kunt de agent installeren nadat u de kluis referenties hebt gedownload.

6. Selecteer **downloaden** in het deel venster **infra structuur voorbereiden** .

    ![kluisreferenties downloaden](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    De kluis referenties worden gedownload naar de map **down loads** . Nadat de kluis referenties zijn gedownload, ziet u een pop-upvenster waarin u wordt gevraagd of u de referenties wilt openen of opslaan. Selecteer **Opslaan**. Als u per ongeluk **openen** selecteert, laat het dialoog venster dat de kluis referenties probeert te openen, mislukken. U kunt de kluis referenties niet meer openen. Ga door naar de volgende stap. De kluis referenties bevinden zich in de map **down loads** .

    ![kluisreferenties downloaden is voltooid](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > De kluis referenties moeten alleen worden opgeslagen op een locatie die lokaal is voor de Windows-Server waarop u de agent wilt gebruiken.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>De agent installeren en registreren

> [!NOTE]
> Het inschakelen van back-ups via de Azure Portal is niet beschikbaar. Gebruik de Microsoft Azure Recovery Services-agent om een back-up te maken van de systeem status van Windows Server.
>

1. Zoek en dubbelklik op **MARSagentinstaller.exe** in de map Downloads (of een andere locatie waar het bestand is opgeslagen).

    Het installatieprogramma geeft een reeks berichten tijdens het uitpakken, installeren en registreren van de Recovery Services-agent.

    ![uitvoeren van installatiereferenties van de Recovery Services-agent](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Doorloop de installatiewizard van de Microsoft Azure Recovery Services Agent. Om de wizard volledig door te kunnen lopen, moet u:

   * Een locatie voor de installatie en de cachemap kiezen.
   * De informatie van uw proxyserver invoeren als u een proxyserver gebruikt om verbinding te maken met internet.
   * Uw gebruikersnaam en wachtwoord invoeren als u gebruikmaakt van een geverifieerde proxyserver.
   * De gedownloade kluisreferenties invoeren
   * De wachtwoordzin voor de versleuteling op een veilige locatie opslaan.

     > [!NOTE]
     > Als u de wachtwoordzin kwijtraakt of vergeet, kan micro soft u niet helpen bij het herstellen van de back-upgegevens. Sla het bestand daarom op een veilige locatie op. Het is vereist om een back-up te herstellen.
     >
     >

De agent wordt nu geïnstalleerd en uw computer wordt geregistreerd bij de kluis. U kunt nu uw back-up configureren en plannen.

## <a name="back-up-windows-server-system-state"></a>Back-ups maken van Windows Server-systeemstatus

De eerste back-up bevat twee taken:

* De back-up plannen
* De eerste keer een back-up maken van de systeem status

Gebruik de Microsoft Azure Recovery Services-agent om de eerste back-up uit te voeren.

> [!NOTE]
> U kunt back-ups maken van de systeem status op Windows Server 2008 R2 via Windows Server 2016. Het maken van een back-up van de systeem status wordt niet ondersteund op client-Sku's. Systeem status wordt niet weer gegeven als een optie voor Windows-clients of Windows Server 2008 SP2-machines.
>
>

### <a name="to-schedule-the-backup-job"></a>De back-up plannen

1. Open de Microsoft Azure Recovery Services-agent. U vindt deze door te zoeken naar **Microsoft Azure Backup** op uw machine.

    ![De Azure Recovery Services-agent starten](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. Selecteer in de Recovery Services-agent de optie **back-up plannen**.

    ![Een back-up van de Windows Server plannen](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Selecteer **volgende** op de pagina **aan de slag** van de wizard Back-up plannen.

4. Selecteer op de pagina **items selecteren voor back-up** de optie **items toevoegen**.

5. Selecteer **systeem status** en selecteer vervolgens **OK**.

6. Selecteer **Next**.

7. Selecteer de vereiste back-upfrequentie en het Bewaar beleid voor uw systeem status back-ups op de volgende pagina's.

8. Controleer de informatie op de pagina Bevestiging en selecteer vervolgens **volt ooien**.

9. Nadat u de wizard voor het maken van een back-upschema hebt doorlopen, selecteert u **Sluiten**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Voor de eerste keer een back-up maken van de Windows Server-systeem status

1. Zorg ervoor dat er geen updates in behandeling zijn voor Windows Server waarvoor opnieuw opstarten is vereist.

2. Selecteer in de Recovery Services-agent **Nu back-up maken** om de eerste seeding via het netwerk te volt ooien.

    ![Windows Server maakt nu een back-up](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Selecteer **systeem status** in het scherm **back-upitem selecteren** dat wordt weer gegeven en selecteer **volgende**.

4. Controleer op de pagina Bevestiging de instellingen die de wizard Nu back-up maken gebruikt om een back-up te maken van de machine. Selecteer vervolgens **Back-up maken**.

5. Selecteer **Sluiten** om de wizard te sluiten. Als u de wizard sluit voordat het back-upproces is voltooid, blijft de wizard op de achtergrond aanwezig.
    > [!NOTE]
    > De MARS-agent wordt geactiveerd `SFC /verifyonly` als onderdeel van de voor controle voor elke systeem status back-up. Dit is om ervoor te zorgen dat bestanden waarvan een back-up is gemaakt als onderdeel van de systeem status, de juiste versies hebben die overeenkomen met de Windows-versie. Meer informatie over System File Checker (SFC) vindt u in [dit artikel](/windows-server/administration/windows-commands/sfc).
    >

Nadat de eerste back-up is voltooid, wordt de status **Taak voltooid** weergegeven in de back-upconsole.

  ![IR voltooid](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Vragen?

Als u vragen hebt, kunt u [ons feedback sturen](https://feedback.azure.com/forums/258995-azure-backup).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [back-ups maken van Windows-machines](backup-windows-with-mars-agent.md).
* Nu u een back-up hebt gemaakt van uw Windows Server-systeem status, kunt u [uw kluizen en servers beheren](backup-azure-manage-windows-server.md).
* Als u een back-up moet herstellen, [zet de bestanden dan terug naar een Windows-machine](backup-azure-restore-windows-server.md) aan de hand van dit artikel.
