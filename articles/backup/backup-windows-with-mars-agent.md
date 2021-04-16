---
title: Een back-up maken van Windows-machines met behulp van de MARS-agent
description: Gebruik de MARS-agent (Microsoft Azure Recovery Services) om back-up te maken van Windows-machines.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 54628c15ffb51c7157132c9a91f41c16873df340
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519167"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>Back-up maken van Windows Server-bestanden en -mappen naar Azure

In dit artikel wordt uitgelegd hoe u een back-up maakt van Windows-machines met behulp van [de Azure Backup-service](backup-overview.md) en de MARS-agent (Microsoft Azure Recovery Services). MARS wordt ook wel de Azure Backup genoemd.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * De vereisten controleren
> * Maak een back-upbeleid en -planning.
> * Voer een back-up op aanvraag uit.

## <a name="before-you-start"></a>Voordat u begint

* Meer informatie over [hoe Azure Backup MARS-agent gebruikt om back-up te maken van Windows-machines.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
* Meer informatie over de [back-uparchitectuur](backup-architecture.md#architecture-back-up-to-dpmmabs) die de MARS-agent op een secundaire MABS- of Data Protection Manager server.
* Bekijk [wat er wordt ondersteund en waar u een back-up van kunt maken](backup-support-matrix-mars-agent.md) met de MARS-agent.
* [Controleer de internettoegang](install-mars-agent.md#verify-internet-access) op de computers van wie u een back-up wilt maken.
* Als de MARS-agent niet is geïnstalleerd, lees dan hier hoe u deze [installeert.](install-mars-agent.md)

## <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Het back-upbeleid geeft aan wanneer momentopnamen van de gegevens moeten worden gemaakt om herstelpunten te maken. Ook wordt aangegeven hoe lang herstelpunten moeten worden behouden. U gebruikt de MARS-agent om een back-upbeleid te configureren.

Azure Backup wordt niet automatisch rekening gehouden met zomer- en wintertijd. Deze standaardwaarde kan een discrepantie veroorzaken tussen de werkelijke tijd en de geplande back-uptijd.

Ga als volgt te werk om een back-upbeleid te maken:

1. Nadat u de MARS-agent hebt gedownload en geregistreerd, opent u de console van de agent. U vindt deze door te zoeken naar **Microsoft Azure Backup** op uw machine.  

1. Selecteer **back-up** **plannen onder Acties.**

    ![Een back-up van de Windows Server plannen](./media/backup-configure-vault/schedule-first-backup.png)
1. Selecteer in de wizard Back-up plannen **de optie Aan de slag**  >  **Volgende.**
1. Selecteer **onder Items selecteren om een back-up van** te maken de optie Items **toevoegen.**

    ![Items toevoegen om een back-up van te maken](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. Selecteer in **het vak Items** selecteren de items waar u een back-up van wilt maken en selecteer vervolgens **OK.**

    ![Items selecteren waarvan u een back-up wilt maken](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Selecteer op **de pagina Items selecteren om een back-up** te maken de optie **Volgende.**
1. Geef op **de pagina Back-upschema** opgeven op wanneer dagelijkse of wekelijkse back-ups moeten worden gemaakt. Selecteer vervolgens **Volgende**.

    * Er wordt een herstelpunt gemaakt wanneer een back-up wordt gemaakt.
    * Het aantal herstelpunten dat in uw omgeving wordt gemaakt, is afhankelijk van uw back-upschema.
    * U kunt maximaal drie dagelijkse back-ups per dag plannen. In het volgende voorbeeld worden twee dagelijkse back-ups uitgevoerd, één om middernacht en één om 18:00 uur.

        ![Een dagelijks back-upschema instellen](./media/backup-configure-vault/day-schedule.png)

    * U kunt ook wekelijkse back-ups uitvoeren. In het volgende voorbeeld worden back-ups elke alternatieve zondag en woensdag om 9:30 uur en 13:00 uur gemaakt.

        ![Een wekelijks back-upschema instellen](./media/backup-configure-vault/week-schedule.png)

1. Geef op **de pagina Bewaarbeleid** selecteren op hoe u historische kopieën van uw gegevens opgeslagen. Selecteer vervolgens **Volgende**.

    * Retentie-instellingen geven aan welke herstelpunten moeten worden opgeslagen en hoe lang ze moeten worden opgeslagen.
    * Voor een instelling voor dagelijkse retentie geeft u aan dat op het tijdstip dat is opgegeven voor de dagelijkse retentie, het meest recente herstelpunt voor het opgegeven aantal dagen wordt bewaard. U kunt ook een maandelijks bewaarbeleid opgeven om aan te geven dat het herstelpunt dat op de 30e van elke maand is gemaakt, twaalf maanden moet worden opgeslagen.
    * Retentie voor dagelijkse en wekelijkse herstelpunten valt meestal samen met het back-upschema. Dus wanneer het schema een back-up activeert, wordt het herstelpunt dat door de back-up wordt gemaakt, opgeslagen voor de duur die wordt opgegeven door het dagelijkse of wekelijkse bewaarbeleid.
    * In het volgende voorbeeld:

        * Dagelijkse back-ups om middernacht en 18:00 uur worden zeven dagen bewaard.
        * Back-ups die op een zaterdag om middernacht en 18:00 uur worden gemaakt, worden vier weken bewaard.
        * Back-ups die op de laatste zaterdag van de maand om middernacht en 18:00 uur zijn gemaakt, worden 12 maanden bewaard.
        * Back-ups die op de afgelopen zaterdag in maart zijn gemaakt, worden tien jaar bewaard.

        ![Voorbeeld van een bewaarbeleid](./media/backup-configure-vault/retention-example.png)

1. Bepaal op **de pagina Eerste back-uptype** kiezen of u de eerste back-up via het netwerk wilt maken of offline back-up wilt gebruiken. Als u de eerste back-up via het netwerk wilt maken, selecteert **u Automatisch via het netwerk**  >  **Volgende.**

    Zie Use Azure Data Box for [offline backup (Offline back-up gebruiken) voor meer informatie over offline back-ups.](offline-backup-azure-data-box.md)

    ![Een eerste back-uptype kiezen](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Controleer de **informatie** op de pagina Bevestiging en selecteer **voltooien.**

    ![Het back-uptype bevestigen](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Nadat u de wizard voor het maken van een back-upschema hebt doorlopen, selecteert u **Sluiten**.

    ![De voortgang van het back-upschema weergeven](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Maak een beleid op elke computer waarop de agent is geïnstalleerd.

### <a name="do-the-initial-backup-offline"></a>De eerste back-up offline maken

U kunt een eerste back-up automatisch via het netwerk uitvoeren of offline een back-up maken. Offline seeding voor een eerste back-up is handig als u grote hoeveelheden gegevens hebt waarvoor veel netwerkbandbreedte nodig is om over te dragen.

Een offline overdracht doen:

1. Schrijf de back-upgegevens naar een faseringslocatie.
1. Gebruik het hulpprogramma AzureOfflineBackupDiskPrep om de gegevens van de faseringslocatie naar een of meer SATA-schijven te kopiëren.

    Het hulpprogramma maakt een Azure Import-taak. Zie Wat is de [Azure Import/Export-service voor meer informatie.](../import-export/storage-import-export-service.md)
1. Verzend de SATA-schijven naar een Azure-datacenter.

    In het datacenter worden de schijfgegevens gekopieerd naar een Azure-opslagaccount. Azure Backup gegevens van het opslagaccount naar de kluis gekopieerd en incrementele back-ups worden gepland.

Zie Use Azure Data Box for offline backup (Gegevens gebruiken voor offline [back-up) voor meer informatie over offline seeding.](offline-backup-azure-data-box.md)

### <a name="enable-network-throttling"></a>Netwerkbeperking inschakelen

U kunt bepalen hoe de MARS-agent netwerkbandbreedte gebruikt door netwerkbeperking in te stellen. Beperking is handig als u tijdens werkuren een back-up wilt maken van gegevens, maar u wilt bepalen hoeveel bandbreedte de back-up- en herstelactiviteit gebruikt.

Netwerkbeperking in Azure Backup gebruikt [Quality of Service (QoS) op](/windows-server/networking/technologies/qos/qos-policy-top) het lokale besturingssysteem.

Netwerkbeperking voor back-ups is beschikbaar op Windows Server 2012 en hoger en op Windows 8 en hoger. Op besturingssystemen moeten de nieuwste servicepacks worden uitgevoerd.

Netwerkbeperking inschakelen:

1. Selecteer eigenschappen wijzigen in de **MARS-agent.**
1. Selecteer op **het tabblad Beperking de** optie Beperking van **internetbandbreedtegebruik inschakelen voor back-upbewerkingen.**

    ![Netwerkbeperking instellen voor back-upbewerkingen](./media/backup-configure-vault/throttling-dialog.png)
1. Geef de toegestane bandbreedte op tijdens werkuren en niet-werkuren. Bandbreedtewaarden beginnen bij 512 Kbps en gaan tot 1023 Mbps. Selecteer vervolgens **OK**.

## <a name="run-an-on-demand-backup"></a>Een on-demand back-up uitvoeren

1. Selecteer in de MARS-agent Nu **een back-up maken.**

    ![Nu een back-up maken in Windows Server](./media/backup-configure-vault/backup-now.png)

1. Als de marsagentversie 2.0.9169.0 of hoger is, kunt u een aangepaste bewaardatum instellen. Kies in **de sectie Back-up** behouden tot een datum in de agenda.

   ![De agenda gebruiken om een retentiedatum aan te passen](./media/backup-configure-vault/mars-ondemand.png)

1. Controleer de **instellingen** op de pagina Bevestiging en selecteer **Back-up maken.**
1. Selecteer **Sluiten** om de wizard te sluiten. Als u de wizard sluit voordat de back-up is gemaakt, blijft de wizard op de achtergrond worden uitgevoerd.

Nadat de eerste back-up is voltooid, wordt de status **Taak** voltooid weergegeven in de Back-upconsole.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Bewaargedrag voor back-upbeleid op aanvraag instellen

> [!NOTE]
> Deze informatie geldt alleen voor MARS-agentversies die ouder zijn dan 2.0.9169.0.
>

| Optie back-upschema | Duur van gegevensretentie
| -- | --
| Dag | **Standaardretentie:** gelijk aan de 'retentie in dagen voor dagelijkse back-ups'. <br/><br/> **Uitzondering:** als een dagelijkse geplande back-up die is ingesteld voor langetermijnretentie (weken, maanden of jaren) mislukt, wordt een on-demand back-up die direct na de fout wordt geactiveerd, beschouwd voor langetermijnretentie. Anders wordt de volgende geplande back-up overwogen voor langetermijnretentie.<br/><br/> **Voorbeeldscenario:** de geplande back-up op donderdag om 8:00 uur is mislukt. Deze back-up moest worden overwogen voor wekelijkse, maandelijkse of jaarlijkse retentie. De eerste back-up op aanvraag die wordt geactiveerd vóór de volgende geplande back-up op vrijdag om 8:00 uur, wordt dus automatisch gelabeld voor wekelijkse, maandelijkse of jaarlijkse retentie. Deze back-up vervangt de back-up van donderdag 8:00 uur.
| Week | **Standaardretentie:** één dag. Back-ups op aanvraag die worden gemaakt voor een gegevensbron met een wekelijks back-upbeleid, worden de volgende dag verwijderd. Ze worden verwijderd, zelfs als het de meest recente back-ups voor de gegevensbron zijn. <br/><br/> **Uitzondering:** als een wekelijks geplande back-up die is ingesteld voor langetermijnretentie (weken, maanden of jaren) mislukt, wordt een on-demand back-up die direct na de fout wordt geactiveerd, beschouwd voor langetermijnretentie. Anders wordt de volgende geplande back-up overwogen voor langetermijnretentie. <br/><br/> **Voorbeeldscenario:** De geplande back-up op donderdag om 8:00 uur is mislukt. Deze back-up moest worden overwogen voor maandelijkse of jaarlijkse retentie. Daarom wordt de eerste back-up op aanvraag die wordt geactiveerd vóór de volgende geplande back-up op donderdag om 8:00 uur automatisch getagd voor maandelijkse of jaarlijkse retentie. Deze back-up vervangt de back-up van donderdag 8:00 uur.

Zie Een [back-upbeleid maken voor meer informatie.](#create-a-backup-policy)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [herstellen van bestanden in Azure](backup-azure-restore-windows-server.md).
* [Veelvoorkomende vragen over het maken van back-up van bestanden en mappen](backup-azure-file-folder-backup-faq.yml)
