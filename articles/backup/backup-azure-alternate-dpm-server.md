---
title: Gegevens herstellen vanuit een Azure Backup Server
description: Herstel de gegevens die u hebt beveiligd met een Recovery Services kluis van een Azure Backup Server die is geregistreerd bij die kluis.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: ed8c937f97ec7a74662a8b46a354b0a6db39a2b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98806025"
---
# <a name="recover-data-from-azure-backup-server"></a>Gegevens herstellen vanaf Azure Backup Server

U kunt Azure Backup Server gebruiken om de gegevens te herstellen waarvan u een back-up hebt gemaakt naar een Recovery Services kluis. Het proces hiervoor is geïntegreerd in de Azure Backup Server-beheer console en is vergelijkbaar met de herstel werk stroom voor andere Azure Backup-onderdelen.

> [!NOTE]
> Dit artikel is van toepassing op [System Center Data Protection Manager 2012 R2 met UR7 of hoger](https://support.microsoft.com/kb/3065246), gecombineerd met de [nieuwste Azure backup-agent](https://aka.ms/azurebackup_agent).
>
>

Gegevens herstellen vanaf een Azure Backup Server:

1. Selecteer op het tabblad **herstel** van de Azure backup server-beheer console de optie **externe DPM toevoegen** (in de linkerbovenhoek van het scherm).

    ![Externe DPM toevoegen](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Down load nieuwe **kluis referenties** van de kluis die is gekoppeld aan de **Azure backup server** waar de gegevens worden hersteld. Kies de Azure backup server in de lijst met Azure backup servers die zijn geregistreerd bij de Recovery Services kluis, en geef de **wachtwoordzin voor versleuteling** op die is gekoppeld aan de server waarvan de gegevens worden hersteld.

    ![Externe DPM-referenties](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Alleen Azure Backup-servers die zijn gekoppeld aan dezelfde registratie kluis, kunnen elkaars gegevens herstellen.
   >
   >

    Zodra de externe Azure Backup Server is toegevoegd, kunt u door de gegevens van de externe server en de lokale Azure Backup Server bladeren op het tabblad **herstel** .
3. Blader door de beschik bare lijst met productie servers die worden beveiligd door de externe Azure Backup Server en selecteer de juiste gegevens bron.

    ![Bladeren door externe DPM-server](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Selecteer **de maand en het jaar** in de vervolg keuzelijst **herstel punten** , selecteer de vereiste **herstel datum** voor wanneer het herstel punt is gemaakt en selecteer de **herstel tijd**.

    Een lijst met bestanden en mappen wordt weer gegeven in het onderste deel venster, dat kan worden gebladerd en teruggezet naar een wille keurige locatie.

    ![Herstel punten voor externe DPM-server](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Klik met de rechter muisknop op het betreffende item en selecteer **herstellen**.

    ![Externe DPM-herstel](./media/backup-azure-alternate-dpm-server/recover.png)
6. Controleer de **herstel selectie**. Controleer de gegevens en het tijdstip van de back-up die worden hersteld, evenals de bron van waaruit de back-up is gemaakt. Als de selectie onjuist is, selecteert u **Annuleren** om terug te gaan naar het tabblad herstel om het juiste herstel punt te selecteren. Als de selectie juist is, selecteert u **volgende**.

    ![Externe DPM-herstel samenvatting](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Selecteer **herstellen naar een andere locatie**. **Blader** naar de juiste locatie voor het herstel.

    ![Alternatieve locatie voor externe DPM-herstel bewerking](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Kies de optie met betrekking tot het maken van een **kopie**, **overs Laan** of **overschrijven**.

   * **Kopie maken** : maakt een kopie van het bestand als er sprake is van een naam conflict.
   * **Overs Laan** : als er sprake is van een naam conflict, wordt het bestand niet hersteld, waardoor het oorspronkelijke bestand blijft.
   * **Overschrijven** : als er sprake is van een naam conflict, wordt de bestaande kopie van het bestand overschreven.

     Kies de juiste optie voor het herstellen van de **beveiliging**. U kunt de beveiligings instellingen van de doel computer waarop de gegevens worden hersteld of de beveiligings instellingen die van toepassing zijn op het product Toep assen op het moment dat het herstel punt werd gemaakt.

     Hiermee wordt aangegeven of een **melding** wordt verzonden wanneer het herstel is voltooid.

     ![Externe DPM-herstel meldingen](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. In het scherm **samen vatting** worden de opties weer gegeven die tot nu toe zijn gekozen. Wanneer u **herstellen** selecteert, worden de gegevens hersteld naar de juiste on-premises locatie.

    ![Samen vatting van externe DPM-herstel opties](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > De herstel taak kan worden bewaakt op het tabblad **controle** van de Azure backup server.
   >
   >

    ![Bewakings herstel](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. U kunt **externe DPM wissen** selecteren op het tabblad **herstel** van de DPM-server om de weer gave van de externe DPM-server te verwijderen.

    ![Externe DPM wissen](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Fout berichten oplossen

| Nee. | Foutbericht | Stappen voor probleemoplossing |
|:---:|:--- |:--- |
| 1. |Deze server is niet geregistreerd bij de kluis die is opgegeven door de kluis referentie. |**Oorzaak:** Deze fout wordt weer gegeven wanneer het kluis referentie bestand dat is geselecteerd niet hoort bij de Recovery Services kluis die is gekoppeld aan Azure Backup Server waarop de herstel poging is uitgevoerd. <br> **Oplossing:** Down load het kluis referentie bestand van de Recovery Services kluis waarnaar de Azure Backup Server is geregistreerd. |
| 2. |De herstel bare gegevens zijn niet beschikbaar of de geselecteerde server is geen DPM-server. |**Oorzaak:** Er zijn geen andere Azure Backup-servers geregistreerd bij de Recovery Services kluis, of de servers hebben de meta gegevens nog niet geüpload of de geselecteerde server is geen Azure Backup Server (met behulp van Windows Server of Windows-client). <br> **Oplossing:** Als er andere Azure Backup-servers zijn geregistreerd bij de Recovery Services kluis, controleert u of de meest recente Azure Backup-Agent is geïnstalleerd. <br>Als er andere Azure Backup-servers zijn geregistreerd bij de Recovery Services kluis, wacht u op een dag na de installatie om het herstel proces te starten. De taken voor de hele nacht worden geüpload de meta gegevens voor alle beveiligde back-ups naar de Cloud. De gegevens zijn beschikbaar voor herstel. |
| 3. |Er is geen andere DPM-server geregistreerd in deze kluis. |**Oorzaak:** Er zijn geen andere Azure Backup-servers die zijn geregistreerd bij de kluis van waaruit de herstel bewerking wordt uitgevoerd.<br>**Oplossing:** Als er andere Azure Backup-servers zijn geregistreerd bij de Recovery Services kluis, controleert u of de meest recente Azure Backup-Agent is geïnstalleerd.<br>Als er andere Azure Backup-servers zijn geregistreerd bij de Recovery Services kluis, wacht u op een dag na de installatie om het herstel proces te starten. De werk lastly uploadt de meta gegevens voor alle beveiligde back-ups naar de Cloud. De gegevens zijn beschikbaar voor herstel. |
| 4. |De opgegeven wachtwoordzin voor versleuteling komt niet overeen met de wachtwoordzin die is gekoppeld aan de volgende server: **\<server name>** |**Oorzaak:** De wachtwoordzin voor versleuteling die wordt gebruikt bij het versleutelen van de gegevens van de Azure Backup Server gegevens die worden hersteld, komt niet overeen met de opgegeven versleutelings wachtwoordzin. De agent kan de gegevens niet ontsleutelen, waardoor het herstel mislukt.<br>**Oplossing:** Geef precies dezelfde wachtwoordzin voor versleuteling op die is gekoppeld aan de Azure Backup Server waarvan de gegevens worden hersteld. |

## <a name="next-steps"></a>Volgende stappen

Lees de andere veelgestelde vragen:

* [Veelgestelde vragen](backup-azure-vm-backup-faq.yml) over back-ups van Azure-vm's
* [Veelgestelde vragen](backup-azure-file-folder-backup-faq.md) over de Azure Backup-agent
