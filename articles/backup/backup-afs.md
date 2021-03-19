---
title: Back-ups maken van Azure-bestands shares in de Azure Portal
description: Meer informatie over het gebruik van de Azure Portal voor het maken van back-ups van Azure-bestands shares in de Recovery Services kluis
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: ca49f1ad48ab0534b27b91ad6a5a50b393cda782
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88890345"
---
# <a name="back-up-azure-file-shares"></a>Een back-up maken van Azure-bestandsshares

In dit artikel wordt uitgelegd hoe u een back-up van [Azure-bestands shares](../storage/files/storage-files-introduction.md) maakt vanuit het Azure Portal.

In dit artikel leert u het volgende:

* Maak een Recovery Services-kluis.
* Back-up van de Recovery Services kluis configureren
* Back-up configureren in het deel venster bestands share
* Een back-uptaak op aanvraag uitvoeren om een herstelpunt te maken

## <a name="prerequisites"></a>Vereisten

* [Meer informatie](azure-file-share-backup-overview.md) over de back-upoplossing op basis van een moment opname van Azure file share.
* Zorg ervoor dat de bestands share aanwezig is in een van de [ondersteunde typen opslag accounts](azure-file-share-support-matrix.md).
* Identificeer of maak een [Recovery Services kluis](#create-a-recovery-services-vault) in dezelfde regio als het opslag account dat als host fungeert voor de bestands share.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Back-up van de Recovery Services kluis configureren

In de volgende stappen wordt uitgelegd hoe u een back-up kunt configureren voor meerdere bestands shares in het deel venster Recovery Services kluis:

1. Open in de [Azure Portal](https://portal.azure.com/)de Recovery Services kluis die u wilt gebruiken voor het configureren van de back-up voor de bestands share.

1. Selecteer in het deel venster **Recovery Services kluis** de **plus back-up** in het menu bovenaan.

   ![Recovery Services-kluis](./media/backup-afs/recovery-services-vault.png)

    1. In het deel venster doel van de **back-up** stelt u in **waar wordt uw workload uitgevoerd?** naar **Azure** door de optie **Azure** te selecteren in de vervolg keuzelijst.

          ![Kies Azure als workload](./media/backup-afs/backup-goal.png)

    2. Selecteer in **waarvan wilt u een back-up maken?** de optie **Azure-bestands share** in de vervolg keuzelijst.

          ![Azure-bestands share selecteren](./media/backup-afs/select-azure-file-share.png)

    3. Selecteer **back-up** om de extensie van de Azure-bestands share in de kluis te registreren.

          ![Selecteer back-up om de Azure-bestands share te koppelen aan de kluis](./media/backup-afs/register-extension.png)

1. Nadat u **back-up** hebt geselecteerd, wordt het deel venster **back-up** geopend. Als u het opslag account wilt selecteren dat als host fungeert voor de bestands share die u wilt beveiligen, selecteert u het **tekstvak koppelings** tekst onder het veld voor het **opslag account** selecteren.

   ![Kies de koppeling selecteren](./media/backup-afs/choose-select-link.png)

1. Het **deel venster opslag account selecteren** wordt aan de rechter kant weer gegeven, met een reeks gedetecteerde ondersteunde opslag accounts. Ze zijn gekoppeld aan deze kluis of bevinden zich in dezelfde regio als de kluis, maar nog niet gekoppeld aan een Recovery Services kluis.

1. Selecteer een account in de lijst met gedetecteerde opslag accounts en selecteer **OK**.

   ![Selecteer een van de gedetecteerde opslag accounts](./media/backup-afs/select-discovered-storage-account.png)

1. De volgende stap is het selecteren van de bestands shares waarvan u een back-up wilt maken. Selecteer de knop **toevoegen** in de sectie **Bestands shares naar back-up** .

   ![Selecteer de bestands shares waarvan u een back-up wilt maken](./media/backup-afs/select-file-shares-to-back-up.png)

1. Het deel venster **Bestands shares context selecteren** wordt aan de rechter kant geopend. Azure zoekt naar het opslag account voor bestands shares waarvan een back-up kan worden gemaakt. Als u onlangs uw bestands shares hebt toegevoegd en deze niet in de lijst ziet, moet u enige tijd toestaan dat de bestands shares worden weer gegeven.

1. Selecteer in de lijst **Bestands shares selecteren** een of meer van de bestands shares waarvan u een back-up wilt maken. Selecteer **OK**.

   ![Selecteer de bestands shares](./media/backup-afs/select-file-shares.png)

1. Als u een back-upbeleid voor de bestands share wilt kiezen, hebt u drie opties:

   * Kies het standaard beleid.<br>
   Met deze optie kunt u dagelijks een back-up inschakelen die 30 dagen wordt bewaard. Als u geen bestaand back-upbeleid in de kluis hebt, wordt het deel venster back-up geopend met de standaard beleids instellingen. Als u de standaard instellingen wilt kiezen, kunt u rechtstreeks **back-up inschakelen** selecteren.

   * Een nieuw beleid maken <br>

      1. Als u een nieuw back-upbeleid voor de bestands share wilt maken, selecteert u de koppelings tekst onder de vervolg keuzelijst in het gedeelte **back-upbeleid** .<br>

         ![Nieuw beleid maken](./media/backup-afs/create-new-policy.png)

      1. Het context venster **back-upbeleid** wordt aan de rechter kant geopend. Geef in het tekstvak een beleids naam op en kies de Bewaar periode volgens uw vereiste. Alleen de optie dagelijkse Bewaar periode is standaard ingeschakeld. Als u wekelijks, maandelijks of jaarlijks wilt bewaren, selecteert u het betreffende selectie vakje en geeft u de gewenste Bewaar waarde op.

      1. Nadat u de Bewaar waarden en een geldige beleids naam hebt opgegeven, selecteert u **OK**.<br>

         ![Beleids naam en bewaar waarden opgeven](./media/backup-afs/policy-name.png)

   * Kies een van de bestaande back-beleidsregels <br>

      Als u een van de bestaande back-beleidsregels voor het configureren van beveiliging wilt kiezen, selecteert u het gewenste beleid in de vervolg keuzelijst **back-upbeleid** .<br>

      ![Bestaand beleid kiezen](./media/backup-afs/choose-existing-policy.png)

1. Selecteer **back-up inschakelen** om te beginnen met het beveiligen van de bestands share.

   ![Kies back-up inschakelen](./media/backup-afs/enable-backup.png)

Nadat u een back-upbeleid hebt ingesteld, wordt er op het geplande tijdstip een moment opname van de bestands shares gemaakt. Het herstel punt wordt ook voor de gekozen periode bewaard.

>[!NOTE]
>Azure Backup biedt nu ondersteuning voor beleids regels met een dagelijks/wekelijks/maandelijks/jaarlijks bewaren van back-ups van Azure-bestands share.

## <a name="configure-backup-from-the-file-share-pane"></a>Back-up configureren in het deel venster bestands share

In de volgende stappen wordt uitgelegd hoe u een back-up kunt configureren voor afzonderlijke bestands shares in het deel venster bestands share:

1. Open in het [Azure Portal](https://portal.azure.com/)het opslag account dat als host fungeert voor de bestands share waarvan u een back-up wilt maken.

1. Selecteer in het opslag account de tegel gelabelde **Bestands shares**. U kunt ook naar **Bestands shares** navigeren via de inhouds opgave van het opslag account.

   ![Storage-account](./media/backup-afs/storage-account.png)

1. In de vermelding van de bestands share ziet u alle bestands shares die aanwezig zijn in het opslag account. Selecteer de bestands share waarvan u een back-up wilt maken.

   ![Lijst met bestands shares](./media/backup-afs/file-shares-list.png)

1. Selecteer **back-up** in de sectie **bewerkingen** van het deel venster bestands share. Het deel venster **back-up configureren** wordt aan de rechter kant geladen.

   ![Het deel venster back-up configureren](./media/backup-afs/configure-backup.png)

1. Voer een van de volgende handelingen uit voor de Recovery Services kluis electie:

    * Als u al een kluis hebt, selecteert u het keuze rondje bestaande Recovery Services kluis **selecteren** en kiest u een van de bestaande kluizen in de vervolg keuzelijst **kluis naam** .

       ![Bestaande kluis selecteren](./media/backup-afs/select-existing-vault.png)

    * Als u geen kluis hebt, selecteert u het keuze rondje nieuwe Recovery Services kluis **maken** . Geef een naam op voor de kluis. Deze wordt gemaakt in dezelfde regio als de bestands share. Standaard wordt de kluis gemaakt in dezelfde resource groep als de bestands share. Als u een andere resource groep wilt kiezen, selecteert u **nieuwe koppeling maken** onder de vervolg keuzelijst **resource type** en geeft u een naam op voor de resource groep. Selecteer **OK** om door te gaan.

       ![Nieuwe kluis maken](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >Als het opslag account is geregistreerd bij een kluis of als er weinig beveiligde shares zijn binnen het opslag account dat als host fungeert voor de bestands share die u wilt beveiligen, wordt de naam van de Recovery Services kluis vooraf ingevuld en kunt u deze niet [meer](backup-azure-files-faq.md#why-cant-i-change-the-vault-to-configure-backup-for-the-file-share)bewerken.

1. Voer een van de volgende handelingen uit voor de selectie van het **back-upbeleid** :

    * Het standaard beleid blijven. Dagelijks back-ups worden gepland met een Bewaar periode van 30 dagen.

    * Selecteer een bestaand back-upbeleid, als u er een hebt, in de vervolg keuzelijst **back-upbeleid** .

       ![Back-upbeleid kiezen](./media/backup-afs/choose-backup-policy.png)

    * Maak een nieuw beleid met een dagelijks/wekelijks/maandelijks/jaarlijks Bewaar periode volgens uw vereiste.  

         1. Selecteer de koppelings tekst voor **een nieuw beleid maken** .

         2. Het context venster **back-upbeleid** wordt aan de rechter kant geopend. Geef in het tekstvak een beleids naam op en kies de Bewaar periode volgens uw vereiste. Alleen de optie dagelijkse Bewaar periode is standaard ingeschakeld. Als u wekelijks, maandelijks of jaarlijks wilt bewaren, selecteert u het betreffende selectie vakje en geeft u de gewenste Bewaar waarde op.

         3. Nadat u de Bewaar waarden en een geldige beleids naam hebt opgegeven, selecteert u **OK**.

            ![Nieuw back-upbeleid maken](./media/backup-afs/create-new-backup-policy.png)

1. Selecteer **back-up inschakelen** om te beginnen met het beveiligen van de bestands share.

   ![Selecteer Back-up inschakelen](./media/backup-afs/select-enable-backup.png)

1. U kunt de voortgang van de configuratie in de portal meldingen volgen of door de back-uptaken te controleren onder de kluis die u gebruikt om de bestands share te beveiligen.

   ![Meldingen in portal](./media/backup-afs/portal-notifications.png)

1. Na het volt ooien van de back-upbewerking configureren selecteert u **back-up** in het gedeelte **bewerkingen** van het deel venster bestands share. De lijst met **kritieke kluizen** van de context deel venster wordt aan de rechter kant geladen. Hier kunt u back-ups op aanvraag en herstel bewerkingen activeren.

   ![Essentiële informatie over de kluis](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>Een back-uptaak op aanvraag uitvoeren

In sommige gevallen wilt u mogelijk een back-upmomentopname of een herstel punt genereren, buiten het tijdstip dat is gepland in het back-upbeleid. Een veelvoorkomende reden voor het genereren van een back-up op aanvraag is direct nadat u het back-upbeleid hebt geconfigureerd. Op basis van de planning in het back-upbeleid, kan het uren of dagen duren totdat er een moment opname wordt gemaakt. Als u uw gegevens wilt beschermen totdat het back-upbeleid actief wordt, start u een back-up op aanvraag. Het maken van een back-up op aanvraag is vaak vereist voordat u geplande wijzigingen aanbrengt aan uw bestands shares.

### <a name="from-the-recovery-services-vault"></a>Uit de Recovery Services kluis

1. Open de Recovery Services kluis die u hebt gebruikt voor het maken van een back-up van uw bestands share. Selecteer in het deel venster **overzicht** de optie **Back-upitems** onder de sectie **beveiligde items** .

   ![Back-upitems selecteren](./media/backup-afs/backup-items.png)

1. Nadat u **Back-upitems** hebt geselecteerd, wordt er een nieuw deel venster met alle **typen back-upbeheer** weer gegeven naast het deel venster **overzicht** .

   ![Lijst met typen back-upbeheer](./media/backup-afs/backup-management-types.png)

1. Selecteer **Azure Storage (Azure files)** in de lijst **type back-upbeheer** . U ziet een lijst met alle bestands shares en de bijbehorende opslag accounts waarvan een back-up is gemaakt met behulp van deze kluis.

   ![Back-upitems van Azure Storage (Azure Files)](./media/backup-afs/azure-files-backup-items.png)

1. Selecteer de gewenste bestands share uit de lijst met Azure-bestands shares. De details van het **back-upitem** worden weer gegeven. Selecteer in het menu **back-upitem** **Nu back-up maken**. Omdat deze back-uptaak op aanvraag is, is er geen Bewaar beleid gekoppeld aan het herstel punt.

   ![Selecteer nu back-up](./media/backup-afs/backup-now.png)

1. Het deel venster **Nu back-up** wordt geopend. Geef de laatste dag op die u van het herstelpunt wilt bewaren. U kunt een maximale Bewaar periode van tien jaar voor een back-up op aanvraag hebben.

   ![Bewaar datum kiezen](./media/backup-afs/retention-date.png)

1. Selecteer **OK** om te bevestigen dat de back-uptaak op aanvraag wordt uitgevoerd.

1. Controleer de portal meldingen om het volt ooien van de back-uptaak uit te voeren. U kunt de voortgang van de taak in het kluis dashboard bewaken. Selecteer de **back-uptaken** die worden  >  **uitgevoerd**.

### <a name="from-the-file-share-pane"></a>In het deel venster bestands share

1. Open het deel venster **overzicht** van de bestands share waarvoor u een back-up op aanvraag wilt maken.

1. Selecteer **back-up** onder de sectie **bewerking** . De lijst met **kritieke kluizen** van de context deel venster wordt aan de rechter kant geladen. Selecteer **Nu back-up** om een back-up op aanvraag te maken.

   ![Selecteer nu back-up](./media/backup-afs/select-backup-now.png)

1. Het deel venster **Nu back-up** wordt geopend. Geef de Bewaar periode voor het herstel punt op. U kunt een maximale Bewaar periode van tien jaar voor een back-up op aanvraag hebben.

   ![Back-updatum bewaren](./media/backup-afs/retain-backup-date.png)

1. Selecteer **OK** om te bevestigen.

>[!NOTE]
>Azure Backup vergrendelt het opslag account wanneer u de beveiliging voor een bestands share configureert in het bijbehorende account. Dit biedt beveiliging tegen onbedoeld verwijderen van een opslag account met back-ups van bestands shares.

## <a name="best-practices"></a>Aanbevolen procedures

* Verwijder geen moment opnamen die zijn gemaakt door Azure Backup. Het verwijderen van momentopnamen kan leiden tot het verlies van herstelpunten en/of herstelfouten.

* Verwijder de vergren deling van het opslag account door Azure Backup niet. Als u de vergren deling verwijdert, zal uw opslag account gevoelig zijn voor onbedoeld verwijderen en, als het wordt verwijderd, uw moment opnamen of back-ups verloren gaan.

## <a name="next-steps"></a>Volgende stappen

Leer hoe u het volgende doet:

* [Azure-bestands shares herstellen](restore-afs.md)
* [Back-ups van Azure-bestands shares beheren](manage-afs-backup.md)
