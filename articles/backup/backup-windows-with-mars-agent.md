---
title: Back-ups maken van Windows-machines met behulp van de MARS-agent
description: Gebruik de Microsoft Azure Recovery Services (MARS)-agent om een back-up te maken van Windows-machines.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 54932192d61633da55657e2ba57adf4e30c4fbc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98702765"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>Back-ups maken van Windows Server-bestanden en-mappen naar Azure

In dit artikel wordt uitgelegd hoe u een back-up maakt van Windows-computers met behulp van de [Azure backup](backup-overview.md) -service en de Microsoft Azure Recovery Services-agent (Mars). MARS wordt ook wel de Azure Backup-Agent genoemd.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * De vereisten controleren
> * Maak een back-upbeleid en-schema.
> * Een back-up op aanvraag uitvoeren.

## <a name="before-you-start"></a>Voordat u begint

* Meer informatie over hoe [Azure backup de Mars-agent gebruikt om een back-up te maken van Windows-machines](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Meer informatie over de [back-uparchitectuur](backup-architecture.md#architecture-back-up-to-dpmmabs) voor het uitvoeren van de Mars-agent op een secundaire MABS of Data Protection Manager-server.
* Bekijk [wat wordt ondersteund en waarvan u een back-up kunt maken](backup-support-matrix-mars-agent.md) door de Mars-agent.
* [Controleer de Internet toegang](install-mars-agent.md#verify-internet-access) op de computers waarvan u een back-up wilt maken.
* Als de MARS-agent niet is geïnstalleerd, leest u [hier](install-mars-agent.md)hoe u deze installeert.

## <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Het back-upbeleid geeft aan wanneer moment opnamen van de gegevens moeten worden gemaakt om herstel punten te maken. Ook wordt aangegeven hoe lang de herstel punten moeten worden bewaard. U gebruikt de MARS-agent om een back-upbeleid te configureren.

Met Azure Backup wordt niet automatisch gezomertijd (zomer tijd) in rekening gebracht. Deze standaard instelling kan een verschil veroorzaken tussen de werkelijke tijd en de geplande back-uptijd.

Ga als volgt te werk om een back-upbeleid te maken:

1. Nadat u de MARS-agent hebt gedownload en geregistreerd, opent u de agent console. U vindt deze door te zoeken naar **Microsoft Azure Backup** op uw machine.  

1. Onder **acties**, selecteert u **back-up plannen**.

    ![Een back-up van de Windows Server plannen](./media/backup-configure-vault/schedule-first-backup.png)
1. Selecteer **aan** de slag volgende in de wizard Back-up plannen  >  .
1. Selecteer **items toevoegen** onder **items selecteren waarvan u een back-up wilt maken**.

    ![Items toevoegen voor back-up](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. Selecteer in het vak **items selecteren** de optie items waarvan u een back-up wilt maken en selecteer vervolgens **OK**.

    ![Items selecteren waarvan u een back-up wilt maken](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. Selecteer **volgende** op de pagina **items selecteren voor back-up** .
1. Geef op de pagina **back-upschema opgeven** op wanneer u dagelijks of wekelijks back-ups wilt maken. Selecteer vervolgens **Volgende**.

    * Wanneer er een back-up wordt gemaakt, wordt er een herstel punt aangemaakt.
    * Het aantal herstel punten dat in uw omgeving wordt gemaakt, is afhankelijk van het back-upschema.
    * U kunt Maxi maal drie dagelijkse back-ups per dag plannen. In het volgende voor beeld worden twee dagelijkse back-ups uitgevoerd, een om middernacht en één om 6:00 uur.

        ![Een dagelijks back-upschema instellen](./media/backup-configure-vault/day-schedule.png)

    * U kunt ook wekelijkse back-ups uitvoeren. In het volgende voor beeld worden back-ups gemaakt op elke tweede zondag en woensdag om 9:30 uur en 1:00 uur.

        ![Een wekelijks back-upschema instellen](./media/backup-configure-vault/week-schedule.png)

1. Geef op de pagina **retentie beleid selecteren** op hoe historische kopieën van uw gegevens moeten worden opgeslagen. Selecteer vervolgens **Volgende**.

    * Met de Bewaar instellingen geeft u op welke herstel punten moeten worden opgeslagen en hoe lang ze moeten worden opgeslagen.
    * Voor een dagelijkse Bewaar instelling geeft u aan dat op het tijdstip dat is opgegeven voor de dagelijkse retentie, het laatste herstel punt wordt bewaard gedurende het opgegeven aantal dagen. Of u kunt een maandelijks Bewaar beleid opgeven om aan te geven dat het herstel punt dat op de 30e van elke maand is gemaakt, 12 maanden moet worden bewaard.
    * Het bewaren van dagelijkse en wekelijkse herstel punten valt meestal samen met het back-upschema. Wanneer het schema een back-up activeert, wordt het herstel punt dat door de back-up wordt gemaakt, opgeslagen voor de duur dat het dagelijkse of wekelijkse Bewaar beleid is opgegeven.
    * In het volgende voorbeeld:

        * Dagelijkse back-ups op middernacht en 6:00 uur worden gedurende zeven dagen bewaard.
        * Back-ups die zijn gemaakt op zaterdag om middernacht en 6:00 uur, worden vier weken bewaard.
        * Back-ups die zijn gemaakt op de laatste zaterdag van de maand om middernacht en 6:00 uur, worden twaalf maanden bewaard.
        * Back-ups die zijn gemaakt op de laatste zaterdag van maart, worden tien jaar bewaard.

        ![Voor beeld van een Bewaar beleid](./media/backup-configure-vault/retention-example.png)

1. Bepaal op de pagina **eerste back-uptype kiezen** of u de eerste back-up wilt maken via het netwerk of offline back-up wilt gebruiken. Als u de eerste back-up via het netwerk wilt maken, selecteert u **automatisch via het netwerk**  >  **volgende**.

    Zie [Azure data box gebruiken voor offline back-ups](offline-backup-azure-data-box.md)voor meer informatie over offline back-ups.

    ![Een eerste back-uptype kiezen](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Controleer de informatie op de pagina **bevestiging** en selecteer vervolgens **volt ooien**.

    ![Het back-uptype bevestigen](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Nadat u de wizard voor het maken van een back-upschema hebt doorlopen, selecteert u **Sluiten**.

    ![De voortgang van het back-upschema weer geven](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Maak een beleid op elke computer waarop de agent is geïnstalleerd.

### <a name="do-the-initial-backup-offline"></a>De eerste back-up offline uitvoeren

U kunt automatisch een back-up uitvoeren via het netwerk of u kunt een back-up maken van offline. Offline seeding voor een eerste back-up is handig als u grote hoeveel heden gegevens hebt waarvoor een groot aantal netwerk bandbreedte moet worden overgedragen.

Een offline-overdracht uitvoeren:

1. Schrijf de back-upgegevens naar een faserings locatie.
1. Gebruik het hulp programma AzureOfflineBackupDiskPrep om de gegevens van de faserings locatie naar een of meer SATA-schijven te kopiëren.

    Het hulp programma maakt een Azure import-taak. Zie [Wat is de Azure import/export-service](../import-export/storage-import-export-service.md)voor meer informatie.
1. Verzend de SATA-schijven naar een Azure-Data Center.

    In het Data Center worden de schijf gegevens gekopieerd naar een Azure-opslag account. Azure Backup kopieert de gegevens van het opslag account naar de kluis en incrementele back-ups worden gepland.

Zie [Azure data box gebruiken voor offline back-ups](offline-backup-azure-data-box.md)voor meer informatie over offline seeding.

### <a name="enable-network-throttling"></a>Netwerk beperking inschakelen

U kunt bepalen hoe de MARS-agent netwerk bandbreedte gebruikt door netwerk beperking in te scha kelen. Beperking is handig als u tijdens werk uren een back-up van gegevens wilt maken, maar u wilt bepalen hoeveel band breedte de back-up-en herstel activiteit gebruikt.

Netwerk beperking in Azure Backup maakt gebruik van [Quality of service (QoS)](/windows-server/networking/technologies/qos/qos-policy-top) op het lokale besturings systeem.

Netwerk beperking voor back-ups is beschikbaar op Windows Server 2012 en hoger, en op Windows 8 en hoger. Besturings systemen moeten de nieuwste service packs uitvoeren.

Netwerk beperking inschakelen:

1. Selecteer in de MARS-agent de optie **Eigenschappen wijzigen**.
1. Op het tabblad **beperking** selecteert u **beperking van Internet bandbreedte gebruik inschakelen voor back-upbewerkingen**.

    ![Netwerk beperking instellen voor back-upbewerkingen](./media/backup-configure-vault/throttling-dialog.png)
1. Geef de toegestane band breedte op tijdens werk uren en niet-werk uren. Bandbreedte waarden beginnen bij 512 kbps en gaan tot 1.023 Mbps. Selecteer vervolgens **OK**.

## <a name="run-an-on-demand-backup"></a>Een on-demand back-up uitvoeren

1. Selecteer **Nu back-up maken** in de Mars-agent.

    ![Nu back-up maken in Windows Server](./media/backup-configure-vault/backup-now.png)

1. Als de MARS agent-versie 2.0.9169.0 of nieuwer is, kunt u een aangepaste Bewaar datum instellen. Kies in de sectie **back-Uplade behouden** een datum in de agenda.

   ![De kalender gebruiken om een Bewaar datum aan te passen](./media/backup-configure-vault/mars-ondemand.png)

1. Controleer de instellingen op de pagina **bevestiging** en selecteer back- **up**.
1. Selecteer **Sluiten** om de wizard te sluiten. Als u de wizard sluit voordat de back-up is voltooid, blijft de wizard op de achtergrond actief.

Nadat de eerste back-up is voltooid, wordt de status **taak voltooid** weer gegeven in de back-upconsole.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Bewaar gedrag van back-upbeleid op aanvraag instellen

> [!NOTE]
> Deze informatie is alleen van toepassing op de MARS-agent versies die ouder zijn dan 2.0.9169.0.
>

| Back-upschema optie | Duur van bewaren van gegevens
| -- | --
| Dag | **Standaard retentie**: gelijk aan de Bewaar periode in dagen voor dagelijkse back-ups. <br/><br/> **Uitzonde ring**: als een dagelijkse geplande back-up die is ingesteld voor lange termijn retentie (weken, maanden of jaren) mislukt, wordt een back-up op aanvraag die direct na de storing is geactiveerd, gezien als lange termijn retentie. Anders wordt de volgende geplande back-up overwogen voor lange termijn retentie.<br/><br/> **Voorbeeld scenario**: de geplande back-up op donderdag om 8:00 uur is mislukt. Deze back-up moet worden overwogen voor wekelijkse, maandelijkse of jaarlijkse retentie. De eerste back-up op aanvraag die wordt geactiveerd vóór de volgende geplande back-up op vrijdag om 8:00 uur wordt automatisch gelabeld voor wekelijkse, maandelijkse of jaarlijkse retentie. Deze back-up vervangt de donderdag 8:00 uur.
| Week | **Standaard retentie**: één dag. Back-ups op aanvraag die worden uitgevoerd voor een gegevens bron met een wekelijks back-upbeleid, worden de volgende dag verwijderd. Ze worden verwijderd, zelfs als ze de meest recente back-ups voor de gegevens bron zijn. <br/><br/> **Uitzonde ring**: als een wekelijkse geplande back-up die is ingesteld voor lange termijn retentie (weken, maanden of jaren) mislukt, wordt een back-up op aanvraag die direct na de storing is geactiveerd, gezien als lange termijn retentie. Anders wordt de volgende geplande back-up overwogen voor lange termijn retentie. <br/><br/> **Voorbeeld scenario**: de geplande back-up op donderdag om 8:00 uur is mislukt. Voor deze back-up moet maandelijks of jaarlijks retentie worden overwogen. De eerste back-up op aanvraag die wordt geactiveerd vóór de volgende geplande back-up op donderdag om 8:00 uur wordt automatisch gelabeld voor maandelijks of jaarlijks bewaren. Deze back-up vervangt de donderdag 8:00 uur.

Zie [een back-upbeleid maken](#create-a-backup-policy)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [herstellen van bestanden in azure](backup-azure-restore-windows-server.md).
* [Veelgestelde vragen over het maken van back-ups van bestanden en mappen](backup-azure-file-folder-backup-faq.md)
