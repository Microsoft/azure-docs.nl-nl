---
title: Zelfstudie - Een back-up maken van een Windows-server naar Azure
description: In deze zelfstudie wordt beschreven hoe u een back-up maakt van een on-premises Windows-server naar een Recovery Services-kluis.
ms.topic: tutorial
ms.date: 08/22/2018
ms.custom: mvc
ms.openlocfilehash: d2990b5950cf8812367c3a59c6cace39e4085e2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88261902"
---
# <a name="back-up-windows-server-to-azure"></a>Een back-up maken van een Windows-server naar Azure

U kunt Azure Backup gebruiken om uw Windows-server te beschermen tegen beschadigingen, aanvallen en noodgevallen. Azure Backup biedt een lichtgewicht hulpprogramma dat bekendstaat als Microsoft Azure Recovery Services-agent (MARS). De MARS-agent wordt geïnstalleerd op de Windows-server om bestanden en mappen te beschermen, evenals serverconfiguratiegegevens (via Windows Server System State). In deze zelfstudie wordt uitgelegd hoe u de MARS-agent kunt gebruiken om van uw Windows-server een back-up te maken naar Azure. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * De MARS-agent downloaden en instellen
> * Back-uptijden en retentieschema configureren voor back-ups van uw server
> * Een back-up op aanvraag uitvoeren

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op <https://portal.azure.com>.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Voordat u een back-up kunt maken van uw Windows-server, moet u een locatie maken om de back-ups (of herstelpunten) te bewaren. Een [Recovery Services-kluis](backup-azure-recovery-services-vault-overview.md) is een container in Azure waarin de back-ups van uw Windows-server worden opgeslagen. Volg de onderstaande stappen om een Recovery Services-kluis in Azure Portal te maken.

1. Selecteer in het menu links **Alle services** en typ **Recovery Services** in de lijst met services. Selecteer **Recovery Services-kluizen**.

   ![Recovery Services-kluis openen](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2. Selecteer in het menu **Recovery Services-kluizen** de optie **Toevoegen**.

   ![Informatie opgeven voor de kluis](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3. Typ in het menu **Recovery Services-kluis**

    * *myRecoveryServicesVault* in bij **Naam**.
    * De huidige abonnements-id wordt weergegeven in **Abonnement**.
    * Selecteer voor **Resourcegroep** de optie **Bestaande gebruiken** en kies *myResourceGroup*. Als *myResourceGroup* niet bestaat, selecteert u **Nieuwe maken** en typt u *myResourceGroup*.
    * Kies in de vervolgkeuzelijst **Locatie** de optie *Europa - west*.
    * Selecteer **Maken** om de Recovery Services-kluis te maken.

Zodra de kluis is gemaakt, wordt deze weergegeven in de lijst met Recovery Services-kluizen.

## <a name="download-recovery-services-agent"></a>De Recovery Services-agent downloaden

De Microsoft Azure Recovery Services-agent (MARS) maakt een koppeling tussen de Windows-server en uw Recovery Services-kluis. In de volgende procedure wordt uitgelegd hoe u de agent downloadt naar uw server.

1. Selecteer **myRecoveryServicesVault** in de lijst met Recovery Services-kluizen om het dashboard ervan te openen.

   ![Selecteer de kluis om het dashboard te openen](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2. Selecteer in het menu van het kluisdashboard **Back-up**.

3. In het menu **Doel van back-up**:

   * Selecteer voor **Waar wordt uw workload uitgevoerd?** de optie **On-premises**
   * Selecteer voor **Waarvan wilt u een back-up maken?** de opties **Bestanden en mappen** en **Systeemstatus**

   ![Menu Doel van back-up](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)

4. Selecteer **Infrastructuur voorbereiden** om het menu **Infrastructuur voorbereiden** te openen.

5. Selecteer in het menu **Infrastructuur voorbereiden** de optie **Agent voor Windows-server of Windows-client downloaden** om *MARSAgentInstaller.exe* te downloaden.

    ![Agent voor Windows Server Essentials of Windows-client downloaden](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    Het installatieprogramma opent een afzonderlijke browser en downloadt **MARSAgentInstaller.exe**.

6. Voordat u het gedownloade bestand uitvoert, selecteert u **Downloaden** in het menu 'Infrastructuur voorbereiden' en slaat u het bestand **Vault Credentials** op. Kluisreferenties zijn vereist om de MARS-agent te verbinden met de Recovery Services-kluis.

    ![Kluisreferenties downloaden](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)

## <a name="install-and-register-the-agent"></a>De agent installeren en registreren

1. Zoek naar en dubbelklik op het gedownloade bestand **MARSagentinstaller.exe**.
2. De **installatiewizard voor de Microsoft Azure Recovery Services-agent** wordt weergegeven. Terwijl u de wizard doorloopt, geeft u de volgende gegevens op wanneer u daarom wordt gevraagd en selecteert u vervolgens **Registreren**.
   * Een locatie voor de installatie en de cachemap.
   * De gegevens van uw proxyserver als u een proxyserver gebruikt om verbinding te maken met internet.
   * Uw gebruikersnaam en wachtwoord als u gebruikmaakt van een geverifieerde proxyserver.

     ![Wizard Setup van Microsoft Azure Recovery Services-agent](./media/tutorial-backup-windows-server-to-azure/mars-installer.png)

3. Aan het einde van de wizard selecteert u **Doorgaan naar registratie** en geeft u het bestand **Vault Credentials** op dat u in de vorige procedure hebt gedownload.

4. Geef, wanneer hierom wordt gevraagd, een wachtwoordzin voor versleuteling op om de back-ups van uw Windows-server te versleutelen. Bewaar de wachtwoordzin op een veilige locatie. Microsoft kan de wachtwoordzin niet herstellen als u deze kwijtraakt.

5. Selecteer **Finish**.

## <a name="configure-backup-and-retention"></a>Back-up en retentie configureren

U gebruikt de Microsoft Azure Recovery Services-agent om te plannen wanneer er back-ups van uw Windows-server naar Azure moeten worden gemaakt. Voer de volgende stappen uit op de server waarnaar u de agent hebt gedownload.

1. Open de Microsoft Azure Recovery Services-agent. U vindt deze door te zoeken naar **Microsoft Azure Backup** op uw machine.

2. Selecteer in de console van de Recovery Services-agent de optie **Back-up plannen** in het deelvenster **Acties**.

    ![Back-up plannen](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. Selecteer **Volgende** om naar de pagina **Item selecteren voor back-up** te navigeren.

4. Selecteer **Items toevoegen** en selecteer **Systeemstatus** in het dialoogvenster dat wordt geopend, evenals de bestanden of mappen waarvan u een back-up wilt maken. Selecteer vervolgens **OK**.

5. Selecteer **Next**.

6. Geef op de pagina **Back-upschema opgeven (Systeemstatus)** de tijd van de dag of van de week op waarop de back-up voor de systeemstatus moet worden geactiveerd. Selecteer vervolgens **Volgende**.

7. Selecteer op de pagina **Retentiebeleid selecteren (Systeemstatus)** het retentiebeleid voor de back-up van de systeemstatus. Selecteer vervolgens **Volgende**.

8. Selecteer op dezelfde manier het back-upschema en het retentiebeleid voor de geselecteerde bestanden en mappen.

9. Selecteer op de pagina **Eerste back-uptype kiezen** de optie **Automatisch via het netwerk** en selecteer vervolgens **Volgende**.

10. Lees de informatie op de pagina **Bevestiging** en selecteer vervolgens **Voltooien**.

11. Nadat u de wizard voor het maken van een back-upschema hebt doorlopen, selecteert u **Sluiten**.

## <a name="perform-an-on-demand-backup"></a>Een back-up op aanvraag uitvoeren

U hebt een planning ingesteld voor het uitvoeren van back-uptaken. U hebt echter nog geen back-up gemaakt van de server. In het kader van herstel na een noodgeval doet u er verstandig aan om een back-up op aanvraag te maken, zodat gegevenstolerantie voor uw server is gewaarborgd.

1. Selecteer in de console van de Microsoft Azure Recovery Services-agent de optie **Nu back-up maken**.

    ![Nu back-up maken](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2. Selecteer in de wizard **Nu back-up maken** een bestand in **Bestanden en mappen** of de **systeemstatus** waarvan u een back-up wilt maken. Selecteer vervolgens **Volgende**
3. Controleer op de pagina **Bevestiging** de instellingen die de wizard **Nu back-up maken** gebruikt om een back-up van uw server te maken. Selecteer vervolgens **Back-up maken**.
4. Selecteer **Sluiten** om de wizard te sluiten. Als u de wizard sluit voordat het back-upproces is voltooid, blijft de wizard op de achtergrond aanwezig.
5. Nadat de eerste back-up is voltooid, wordt de status **Taak voltooid** weergegeven in het deelvenster **Taken** in de console van de MARS-agent.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure Portal gebruikt voor het volgende:

> [!div class="checklist"]
>
> * Een Recovery Services-kluis maken
> * De Microsoft Azure Recovery Services-agent downloaden
> * De agent installeren
> * Een back-up configureren voor een Windows-server
> * Een back-up op aanvraag uitvoeren

Ga door naar de volgende zelfstudie om bestanden te herstellen van Azure naar een Windows-server

> [!div class="nextstepaction"]
> [Bestanden herstellen van Azure naar een Windows-server](./tutorial-backup-restore-files-windows-server.md)
