---
title: Een back-up maken van Azure-bestands shares in de Azure Portal
description: Meer informatie over het gebruik van de Azure Portal om een back-up te maken van Azure-bestands shares in de Recovery Services-kluis
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: e7f44a71388468be432bdfcb0eb2bf67c0fcc8ef
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519932"
---
# <a name="back-up-azure-file-shares"></a>Een back-up maken van Azure-bestandsshares

In dit artikel wordt uitgelegd hoe u een back-up kunt maken van Azure-bestands [shares](../storage/files/storage-files-introduction.md) vanuit Azure Portal.

In dit artikel leert u het volgende:

* Maak een Recovery Services-kluis.
* Back-up configureren vanuit de Recovery Services-kluis
* Back-up configureren vanuit het deelvenster Bestandsdeel
* Een back-uptaak op aanvraag uitvoeren om een herstelpunt te maken

## <a name="prerequisites"></a>Vereisten

* [Meer informatie](azure-file-share-backup-overview.md) over de back-upoplossing op basis van momentopnamen van Azure-bestands share.
* Zorg ervoor dat de bestands share aanwezig is in een van de [ondersteunde typen opslagaccounts.](azure-file-share-support-matrix.md)
* Identificeer of maak een [Recovery Services-kluis](#create-a-recovery-services-vault) in dezelfde regio als het opslagaccount dat als host voor de bestands share wordt gebruikt.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Back-up configureren vanuit de Recovery Services-kluis

In de volgende stappen wordt uitgelegd hoe u back-ups voor meerdere bestands shares kunt configureren vanuit het deelvenster Recovery Services-kluis:

1. Open in [Azure Portal](https://portal.azure.com/)de Recovery Services-kluis die u wilt gebruiken voor het configureren van back-ups voor de bestands share.

1. Selecteer in **het deelvenster Recovery Services-kluis** de **optie +Back-up** in het menu bovenaan.

   ![Recovery Services-kluis](./media/backup-afs/recovery-services-vault.png)

    1. Stel in **het deelvenster Doel** van de back-up Waar wordt uw workload **uitgevoerd?** in op **Azure** door de **optie Azure** in de vervolgkeuzelijst te selecteren.

          ![Azure kiezen als workload](./media/backup-afs/backup-goal.png)

    2. In **Waar wilt u een back-up van maken?** selecteert u **Azure-bestands** share in de vervolgkeuzelijst.

          ![Azure FileShare selecteren](./media/backup-afs/select-azure-file-share.png)

    3. Selecteer **Back-up** om de Azure-bestandsextensie in de kluis te registreren.

          ![Selecteer Back-up om de Azure-bestands share te koppelen aan de kluis](./media/backup-afs/register-extension.png)

1. Nadat u **Back-up hebt geselecteerd,** wordt **het deelvenster Back-up** geopend. Als u het opslagaccount wilt selecteren dat als host voor de bestands share die u wilt beveiligen, selecteert u de **tekst Koppeling** selecteren onder het **tekstvak Opslagaccount.**

   ![Kies de koppeling Selecteren](./media/backup-afs/choose-select-link.png)

1. Het **deelvenster Opslagaccount selecteren** wordt aan de rechterkant geopend, met een reeks gevonden ondersteunde opslagaccounts. Ze zijn gekoppeld aan deze kluis of aanwezig in dezelfde regio als de kluis, maar zijn nog niet gekoppeld aan een Recovery Services-kluis.

1. Selecteer een account in de lijst met gevonden opslagaccounts en selecteer **OK.**

   ![Een selectie maken uit de ontdekte opslagaccounts](./media/backup-afs/select-discovered-storage-account.png)

1. De volgende stap is het selecteren van de bestands shares waar u een back-up van wilt maken. Selecteer de **knop Toevoegen** in de sectie **Bestandsshares naar back-up.**

   ![Selecteer de bestands shares om een back-up van te maken](./media/backup-afs/select-file-shares-to-back-up.png)

1. Het **contextvenster Bestands shares** selecteren wordt aan de rechterkant geopend. Azure zoekt in het opslagaccount naar bestands shares van waar een back-up van kan worden uitgevoerd. Als u onlangs uw bestands shares hebt toegevoegd en deze niet in de lijst worden weergegeven, wacht u even tot de bestands shares worden weergegeven.

1. Selecteer in **de lijst Bestands** shares selecteren een of meer van de bestands shares waar u een back-up van wilt maken. Selecteer **OK**.

   ![De bestands shares selecteren](./media/backup-afs/select-file-shares.png)

1. Als u een back-upbeleid voor uw bestands share wilt kiezen, hebt u drie opties:

   * Kies het standaardbeleid.<br>
   Met deze optie kunt u dagelijkse back-ups inschakelen die 30 dagen worden bewaard. Als u geen bestaand back-upbeleid in de kluis hebt, wordt het back-upvenster geopend met de standaardbeleidsinstellingen. Als u de standaardinstellingen wilt kiezen, kunt u rechtstreeks **Back-up inschakelen selecteren.**

   * Een nieuw beleid maken <br>

      1. Als u een nieuw back-upbeleid voor uw bestands share wilt maken, selecteert u de koppelingstekst onder de vervolgkeuzelijst in de sectie **Back-upbeleid.**<br>

         ![Nieuw beleid maken](./media/backup-afs/create-new-policy.png)

      1. Het **contextvenster Back-upbeleid** wordt aan de rechterkant geopend. Geef een beleidsnaam op in het tekstvak en kies de retentieperiode op basis van uw vereisten. Alleen de optie voor dagelijkse retentie is standaard ingeschakeld. Als u een wekelijkse, maandelijkse of jaarlijkse retentie wilt, selecteert u het bijbehorende selectievakje en geeft u de gewenste bewaarwaarde op.

      1. Nadat u de retentiewaarden en een geldige beleidsnaam heeft opgegeven, selecteert u **OK**.<br>

         ![Naam- en retentiewaarden voor beleid opgeven](./media/backup-afs/policy-name.png)

   * Kies een van de bestaande back-upbeleidsregels <br>

      Als u een van de bestaande back-upbeleidsregels voor  het configureren van beveiliging wilt kiezen, selecteert u het gewenste beleid in de vervolgkeuzelijst Back-upbeleid.<br>

      ![Bestaand beleid kiezen](./media/backup-afs/choose-existing-policy.png)

1. Selecteer **Back-up inschakelen** om de bestands share te beveiligen.

   ![Back-up inschakelen kiezen](./media/backup-afs/enable-backup.png)

Nadat u een back-upbeleid hebt ingesteld, wordt er een momentopname van de bestands shares gemaakt op het geplande tijdstip. Het herstelpunt wordt ook bewaard voor de gekozen periode.

>[!NOTE]
>Azure Backup ondersteunt nu beleid met dagelijkse/wekelijkse/maandelijkse/jaarlijkse retentie voor back-ups van Azure-bestands share.

## <a name="configure-backup-from-the-file-share-pane"></a>Back-up configureren vanuit het deelvenster Bestandsdeel

In de volgende stappen wordt uitgelegd hoe u back-ups kunt configureren voor afzonderlijke bestands shares vanuit het deelvenster van de desbetreffende bestands share:

1. Open in [Azure Portal](https://portal.azure.com/)het opslagaccount dat als host voor de bestands share wordt gebruikt en waar u een back-up van wilt maken.

1. Selecteer in het opslagaccount de tegel met het label **Bestands shares.** U kunt ook naar **Bestands shares** navigeren via de inhoudsopgave voor het opslagaccount.

   ![Storage-account](./media/backup-afs/storage-account.png)

1. In de lijst met bestands share ziet u alle bestands shares die aanwezig zijn in het opslagaccount. Selecteer de bestands share van waar u een back-up van wilt maken.

   ![Lijst met bestands shares](./media/backup-afs/file-shares-list.png)

1. Selecteer **Back-up** in **de sectie** Bewerkingen van het deelvenster Bestandsdeel. Het **deelvenster Back-up** configureren wordt aan de rechterkant geladen.

   ![Het deelvenster Back-up configureren](./media/backup-afs/configure-backup.png)

1. Voor de selectie van de Recovery Services-kluis gaat u op een van de volgende dingen te werk:

    * Als u al een kluis hebt, selecteert u het keuzerondje Bestaande **Recovery** Services-kluis selecteren en kiest u een van de bestaande kluizen in de **vervolgkeuzelijst** Kluisnaam.

       ![Bestaande kluis selecteren](./media/backup-afs/select-existing-vault.png)

    * Als u geen kluis hebt, selecteert u het **keuzerondje Nieuwe** Recovery Services-kluis maken. Geef een naam op voor de kluis. Deze wordt gemaakt in dezelfde regio als de bestands share. De kluis wordt standaard gemaakt in dezelfde resourcegroep als de bestands share. Als u een andere resourcegroep  wilt kiezen, selecteert u de koppeling Nieuwe maken onder de vervolgkeuzekeuzegroep **Resourcetype** en geeft u een naam op voor de resourcegroep. Selecteer **OK** om door te gaan.

       ![Nieuwe kluis maken](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >Als het opslagaccount is geregistreerd bij een kluis of als er enkele beveiligde shares zijn binnen het opslagaccount dat als host voor de bestands share wordt gebruikt, wordt de naam van de Recovery Services-kluis vooraf ingevuld en kunt u deze niet bewerken Meer informatie vindt u [hier](backup-azure-files-faq.yml#why-can-t-i-change-the-vault-to-configure-backup-for-the-file-share-).

1. Voor de **selectie back-upbeleid** gaat u op een van de volgende dingen te werk:

    * Laat het standaardbeleid staan. Er worden dagelijkse back-ups gepland met een bewaarperiode van 30 dagen.

    * Selecteer een bestaand back-upbeleid in  de vervolgkeuzelijst Back-upbeleid als u er een hebt.

       ![Back-upbeleid kiezen](./media/backup-afs/choose-backup-policy.png)

    * Maak een nieuw beleid met dagelijkse/wekelijkse/maandelijkse/jaarlijkse retentie op basis van uw vereisten.  

         1. Selecteer de **tekst Een nieuwe beleidskoppeling** maken.

         2. Het **contextvenster** Back-upbeleid wordt aan de rechterkant geopend. Geef een beleidsnaam op in het tekstvak en kies de retentieperiode op basis van uw vereisten. Alleen de optie voor dagelijkse retentie is standaard ingeschakeld. Als u een wekelijkse, maandelijkse of jaarlijkse retentie wilt, selecteert u het bijbehorende selectievakje en geeft u de gewenste bewaarwaarde op.

         3. Nadat u de retentiewaarden en een geldige beleidsnaam heeft opgegeven, selecteert u **OK**.

            ![Nieuw back-upbeleid maken](./media/backup-afs/create-new-backup-policy.png)

1. Selecteer **Back-up inschakelen** om de bestands share te beveiligen.

   ![Selecteer Back-up inschakelen](./media/backup-afs/select-enable-backup.png)

1. U kunt de voortgang van de configuratie volgen in de portalmeldingen of door de back-uptaken te bewaken in de kluis die u gebruikt om de bestands share te beveiligen.

   ![Meldingen in portal](./media/backup-afs/portal-notifications.png)

1. Nadat de back-upbewerking configureren is voltooid, selecteert u **Back-up** in **de sectie Bewerkingen** van het deelvenster Bestandsdeel. Het contextdeelvenster **met de kluis Essentials** wordt aan de rechterkant geladen. Van hier kunt u back-up- en herstelbewerkingen op aanvraag activeren.

   ![Essentiële onderdelen van de kluis](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>Een back-up job op aanvraag uitvoeren

Mogelijk wilt u af en toe een back-upmomentopname of herstelpunt genereren buiten de tijden die zijn gepland in het back-upbeleid. Een veelvoorkomende reden voor het genereren van een back-up op aanvraag is direct nadat u het back-upbeleid hebt geconfigureerd. Op basis van de planning in het back-upbeleid kan het uren of dagen duren voordat er een momentopname wordt gemaakt. Als u uw gegevens wilt beschermen totdat het back-upbeleid actief wordt, start u een back-up op aanvraag. Het maken van een back-up op aanvraag is vaak vereist voordat u geplande wijzigingen aan uw bestands shares aan te brengen.

### <a name="from-the-recovery-services-vault"></a>Vanuit de Recovery Services-kluis

1. Open de Recovery Services-kluis die u hebt gebruikt om een back-up van uw bestands share te maken. Selecteer in **het deelvenster** Overzicht de optie **Back-upitems** in **de sectie Beveiligde items.**

   ![Back-upitems selecteren](./media/backup-afs/backup-items.png)

1. Nadat u **Back-upitems hebt** geselecteerd, wordt er een nieuw deelvenster met alle **typen** back-upbeheer weergegeven naast het **deelvenster** Overzicht.

   ![Lijst met back-upbeheertypen](./media/backup-afs/backup-management-types.png)

1. Selecteer in **de lijst Type** back-upbeheer Azure Storage **(Azure Files)**. U ziet een lijst met alle bestands shares en de bijbehorende opslagaccounts waar een back-up van wordt gemaakt met behulp van deze kluis.

   ![Azure Storage (Azure Files) back-upitems](./media/backup-afs/azure-files-backup-items.png)

1. Selecteer in de lijst met Azure-bestands shares de wante bestands share. De **details van het back-upitem** worden weergegeven. Selecteer in het menu **Back-upitem** de optie **Nu back-up maken.** Omdat deze back-up job op aanvraag is, is er geen bewaarbeleid gekoppeld aan het herstelpunt.

   ![Selecteer Nu back-up maken](./media/backup-afs/backup-now.png)

1. Het **deelvenster Nu back-up** maken wordt geopend. Geef de laatste dag op die u van het herstelpunt wilt bewaren. U kunt een maximale bewaarperiode van tien jaar hebben voor een back-up op aanvraag.

   ![Retentiedatum kiezen](./media/backup-afs/retention-date.png)

1. Selecteer **OK om** de back-up job op aanvraag te bevestigen die wordt uitgevoerd.

1. Controleer de portalmeldingen om de voltooiing van de back-up van de taak bij te houden. U kunt de voortgang van de taak volgen in het kluisdashboard. Selecteer **Back-uptaken**  >  **die worden uitgevoerd.**

### <a name="from-the-file-share-pane"></a>Vanuit het deelvenster bestandsdeel

1. Open het deelvenster Overzicht van **de** bestands share waarvoor u een back-up op aanvraag wilt maken.

1. Selecteer **Back-up** in de **sectie** Bewerking. Het contextdeelvenster **met de kluis Essentials** wordt aan de rechterkant geladen. Selecteer **Nu back-up** maken om een back-up op aanvraag te maken.

   ![Selecteer Nu back-up maken](./media/backup-afs/select-backup-now.png)

1. Het **deelvenster Nu back-up** maken wordt geopend. Geef de retentie voor het herstelpunt op. U kunt een maximale bewaarperiode van tien jaar hebben voor een back-up op aanvraag.

   ![Back-updatum behouden](./media/backup-afs/retain-backup-date.png)

1. Selecteer **OK** om te bevestigen.

>[!NOTE]
>Azure Backup wordt het opslagaccount vergrendeld wanneer u de beveiliging voor een bestands share in het bijbehorende account configureert. Dit biedt bescherming tegen onbedoeld verwijderen van een opslagaccount met back-up van bestands shares.

## <a name="best-practices"></a>Aanbevolen procedures

* Verwijder geen momentopnamen die zijn gemaakt door Azure Backup. Het verwijderen van momentopnamen kan leiden tot het verlies van herstelpunten en/of herstelfouten.

* Verwijder de vergrendeling voor het opslagaccount niet door de Azure Backup. Als u de vergrendeling verwijdert, is uw opslagaccount gevoelig voor onbedoelde verwijdering. Als het account wordt verwijderd, verliest u uw momentopnamen of back-ups.

## <a name="next-steps"></a>Volgende stappen

Leer hoe u het volgende doet:

* [Azure-bestands shares herstellen](restore-afs.md)
* [Back-ups van Azure-bestands delen beheren](manage-afs-backup.md)
