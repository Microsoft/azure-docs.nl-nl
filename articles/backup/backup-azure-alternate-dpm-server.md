---
title: Gegevens herstellen van een Azure Backup Server
description: Herstel de gegevens die u hebt beveiligd naar een Recovery Services-kluis vanaf elke Azure Backup Server geregistreerd bij die kluis.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 780c88175397fb06e704e57062ae5c6d3b93d8b8
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519585"
---
# <a name="recover-data-from-azure-backup-server"></a>Gegevens herstellen vanaf Azure Backup Server

U kunt deze Azure Backup Server om de gegevens te herstellen die u hebt gemaakt in een Recovery Services-kluis. Het proces om dit te doen is geïntegreerd in de Azure Backup Server-beheerconsole en is vergelijkbaar met de herstelwerkstroom voor andere Azure Backup onderdelen.

> [!NOTE]
> Dit artikel is van toepassing [op System Center Data Protection Manager 2012 R2](https://support.microsoft.com/kb/3065246)met UR7 of hoger, in combinatie met de meest recente [Azure Backup agent](https://aka.ms/azurebackup_agent).
>
>

Gegevens herstellen van een Azure Backup Server:

1. Selecteer op **het** tabblad Herstel Azure Backup Server de beheerconsole **externe DPM** toevoegen (linksboven in het scherm).

    ![Externe DPM toevoegen](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Download nieuwe kluisreferenties uit de kluis die is gekoppeld aan de **Azure Backup Server** waar de gegevens worden hersteld, kies de Azure Backup Server in  de lijst met Azure Backup-servers die zijn geregistreerd bij de Recovery Services-kluis en geef de wachtwoordzin voor versleuteling op die is gekoppeld aan de server waarvan de gegevens worden hersteld. 

    ![Externe DPM-referenties](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Alleen Azure Backup servers die aan dezelfde registratiekluis zijn gekoppeld, kunnen elkaars gegevens herstellen.
   >
   >

    Zodra de externe Azure Backup Server is toegevoegd, kunt u op het tabblad Herstel door de gegevens van de externe server en de lokale Azure Backup Server **bladeren.**
3. Blader door de beschikbare lijst met productieservers die worden beveiligd door de externe Azure Backup Server selecteer de juiste gegevensbron.

    ![Bladeren door externe DPM-server](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Selecteer **de maand en het jaar** in de  vervolgkeuzekeuze selecteren, selecteer de vereiste hersteldatum voor wanneer het herstelpunt is gemaakt en selecteer de **Hersteltijd.** 

    Er wordt een lijst met bestanden en mappen weergegeven in het onderste deelvenster, waar u naar elke locatie kunt bladeren en deze kunt herstellen.

    ![Externe DPM-serverherstelpunten](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Klik met de rechtermuisknop op het juiste item en selecteer **Herstellen.**

    ![Extern DPM-herstel](./media/backup-azure-alternate-dpm-server/recover.png)
6. Controleer de **selectie herstellen.** Controleer de gegevens en het tijdstip waarop de back-up wordt hersteld, evenals de bron van waaruit de back-up is gemaakt. Als de selectie onjuist is, selecteert u **Annuleren om** terug te gaan naar het tabblad Herstel om het juiste herstelpunt te selecteren. Als de selectie juist is, selecteert u **Volgende.**

    ![Overzicht van extern DPM-herstel](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Selecteer **Herstellen naar een alternatieve locatie.** **Blader** naar de juiste locatie voor het herstel.

    ![Externe alternatieve DPM-herstellocatie](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Kies de optie voor het **maken van een kopie,** **overslaan** of **overschrijven.**

   * **Kopie maken:** hiermee maakt u een kopie van het bestand als er een naamsbotsing is.
   * **Overslaan:** als er een naamsbotsing is, wordt het bestand niet hersteld, waardoor het oorspronkelijke bestand wordt overgeslagen.
   * **Overschrijven:** als er een naamswisseling is, wordt de bestaande kopie van het bestand overschreven.

     Kies de juiste optie om de **beveiliging te herstellen.** U kunt de beveiligingsinstellingen toepassen van de doelcomputer waarop de gegevens worden hersteld of de beveiligingsinstellingen die van toepassing waren op het product op het moment dat het herstelpunt werd gemaakt.

     Bepaal of er **een melding** wordt verzonden zodra het herstel is voltooid.

     ![Externe DPM-herstelmeldingen](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. Het **scherm** Samenvatting bevat de opties die tot nu toe zijn gekozen. Zodra u **Herstellen selecteert,** worden de gegevens hersteld naar de juiste on-premises locatie.

    ![Overzicht van externe DPM-herstelopties](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > De herstel job kan worden bewaakt op het **tabblad Bewaking** van de Azure Backup Server.
   >
   >

    ![Herstel bewaken](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. U kunt Externe **DPM verwijderen selecteren** op **het tabblad Herstel** van de DPM-server om de weergave van de externe DPM-server te verwijderen.

    ![Externe DPM leeg maken](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Foutberichten oplossen

| Nee. | Foutbericht | Stappen voor probleemoplossing |
|:---:|:--- |:--- |
| 1. |Deze server is niet geregistreerd bij de kluis die is opgegeven met de kluisreferentie. |**Oorzaak:** Deze fout wordt weergegeven wanneer het geselecteerde kluisreferentiebestand niet tot de Recovery Services-kluis behoort die is gekoppeld aan Azure Backup Server waarop het herstel wordt geprobeerd. <br> **Oplossing:** Download het kluisreferentiebestand van de Recovery Services-kluis waarvoor Azure Backup Server is geregistreerd. |
| 2. |De herstelbare gegevens zijn niet beschikbaar of de geselecteerde server is geen DPM-server. |**Oorzaak:** Er zijn geen andere Azure Backup-servers die zijn geregistreerd bij de Recovery Services-kluis, of de servers hebben de metagegevens nog niet geüpload, of de geselecteerde server is geen Azure Backup Server (met Windows Server of Windows Client). <br> **Oplossing:** Als er andere servers Azure Backup zijn geregistreerd bij de Recovery Services-kluis, controleert u of de meest recente Azure Backup-agent is geïnstalleerd. <br>Als er andere servers Azure Backup zijn geregistreerd bij de Recovery Services-kluis, wacht u een dag na de installatie om het herstelproces te starten. De taak 's nachts uploadt de metagegevens voor alle beveiligde back-ups naar de cloud. De gegevens zijn beschikbaar voor herstel. |
| 3. |Er is geen andere DPM-server geregistreerd bij deze kluis. |**Oorzaak:** Er zijn geen andere Azure Backup servers die zijn geregistreerd bij de kluis van waaruit het herstel wordt geprobeerd.<br>**Oplossing:** Als er andere servers Azure Backup zijn geregistreerd bij de Recovery Services-kluis, controleert u of de meest recente Azure Backup-agent is geïnstalleerd.<br>Als er andere servers Azure Backup zijn geregistreerd bij de Recovery Services-kluis, wacht u een dag na de installatie om het herstelproces te starten. De taak uploadt elke nacht de metagegevens voor alle beveiligde back-ups naar de cloud. De gegevens zijn beschikbaar voor herstel. |
| 4. |De opgegeven wachtwoordzin voor versleuteling komt niet overeen met de wachtwoordzin die is gekoppeld aan de volgende server: **\<server name>** |**Oorzaak:** De wachtwoordzin voor versleuteling die wordt gebruikt bij het versleutelen van de gegevens uit de gegevens van de Azure Backup Server die worden hersteld, komt niet overeen met de opgegeven wachtwoordzin voor versleuteling. De agent kan de gegevens niet ontsleutelen en het herstel mislukt.<br>**Oplossing:** Geef exact dezelfde wachtwoordzin voor versleuteling op die is gekoppeld aan de Azure Backup Server waarvan de gegevens worden hersteld. |

## <a name="next-steps"></a>Volgende stappen

Lees de andere veelgestelde vragen:

* [Veelvoorkomende vragen](backup-azure-vm-backup-faq.yml) over back-ups van azure-VM's
* [Veelgestelde vragen](backup-azure-file-folder-backup-faq.yml) over de Azure Backup-agent
