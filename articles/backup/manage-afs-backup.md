---
title: Back-ups van Azure-bestandsshares beheren
description: In dit artikel worden algemene taken beschreven voor het beheren en bewaken van de Azure-bestands shares waarvan een back-up is gemaakt door Azure Backup.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 784b22f11b57fb025f6d9401e10d527c83751898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88757622"
---
# <a name="manage-azure-file-share-backups"></a>Back-ups van Azure-bestandsshares beheren

In dit artikel worden algemene taken beschreven voor het beheren en bewaken van de Azure-bestands shares waarvan een back-up is gemaakt door [Azure backup](./backup-overview.md). U leert hoe u beheer taken kunt uitvoeren in de Recovery Services kluis.

## <a name="monitor-jobs"></a>Taken controleren

Wanneer u een back-up-of herstel bewerking uitvoert, maakt de back-upservice een taak voor bijhouden. U kunt de voortgang van alle taken bijhouden op de pagina **Back-uptaken**.

De pagina **Back-uptaken** openen:

1. Open de Recovery Services kluis die u hebt gebruikt voor het configureren van back-ups voor uw bestands shares. Selecteer in het deel venster **overzicht** de optie **back-uptaken** onder het gedeelte **bewaking** .

   ![Bewerkings sectie back-uptaken](./media/manage-afs-backup/backup-jobs.png)

1. Nadat u **OK** hebt geselecteerd, wordt in het deel venster **back-uptaken** de status van alle taken weer gegeven. Selecteer de naam van de werk belasting die overeenkomt met de bestands share die u wilt bewaken.

   ![Werkbelasting naam](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Een nieuw beleid maken

U kunt een nieuw beleid maken voor back-ups van Azure-bestands shares in de sectie **back-upbeleid** van de Recovery Services kluis. Alle beleids regels die zijn gemaakt bij het configureren van back-ups voor bestands shares, worden weer gegeven met het **beleids type** van de **Azure-bestands share**.

Het bestaande back-upbeleid weer geven:

1. Open de Recovery Services kluis die u hebt gebruikt voor het configureren van de back-up voor de bestands share. Selecteer in het menu Recovery Services kluis **back-upbeleid** in de sectie **beheren** . Alle back-upbeleiden die in de kluis zijn geconfigureerd, worden weer gegeven.

   ![Alle back-upbeleid](./media/manage-afs-backup/all-backup-policies.png)

1. Als u beleids regels die specifiek zijn voor **Azure-bestands share** wilt weer geven, selecteert u **Azure-bestands share** in de vervolg keuzelijst in de rechter bovenhoek.

   ![Azure-bestands share selecteren](./media/manage-afs-backup/azure-file-share.png)

Een nieuw back-upbeleid maken:

1. Selecteer **+ toevoegen** in het deel venster **back-upbeleidsregels** .

   ![Nieuw back-upbeleid](./media/manage-afs-backup/new-backup-policy.png)

1. Selecteer in het deel venster **toevoegen** de optie **Azure-bestands share** als het **beleids type**. Het deel venster **back-upbeleid** voor **Azure-bestands share** wordt geopend. Geef de naam van het beleid, de back-upfrequentie en het Bewaar termijn voor de herstel punten op. Nadat u het beleid hebt gedefinieerd, selecteert u **OK**.

   ![Het back-upbeleid definiëren](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Beleid wijzigen

U kunt een back-upbeleid wijzigen om de back-upfrequentie of het Bewaar bereik te wijzigen.

Een beleid wijzigen:

1. Open de Recovery Services kluis die u hebt gebruikt voor het configureren van de back-up voor de bestands share. Selecteer in het menu Recovery Services kluis **back-upbeleid** in de sectie **beheren** . Alle back-upbeleiden die in de kluis zijn geconfigureerd, worden weer gegeven.

   ![Alle back-upbeleid in de kluis](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Als u beleids regels wilt bekijken die specifiek zijn voor een Azure-bestands share, selecteert u **Azure-bestands share** in de vervolg keuzelijst in de rechter bovenhoek. Selecteer het back-upbeleid dat u wilt wijzigen.

   ![De Azure-bestands share die u wilt aanpassen](./media/manage-afs-backup/azure-file-share-modify.png)

1. Het deel venster **planning** wordt geopend. Bewerk het **back-upschema** en de **Bewaar periode** als vereist en selecteer **Opslaan**. In het deel venster ziet u het bericht ' update wordt uitgevoerd '. Nadat het beleid is gewijzigd, wordt het bericht ' het back-upbeleid is bijgewerkt ' weer gegeven.

   ![Het gewijzigde beleid opslaan](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>De beveiliging voor een bestandsshare stoppen

Er zijn twee manieren om het beveiligen van Azure-bestandsshares te stoppen:

* Stop alle toekomstige back-uptaken en *Verwijder alle herstel punten*.
* Stop alle toekomstige back-uptaken, maar *behoud de herstel punten*.

Er zijn mogelijk kosten verbonden aan het verlaten van de herstel punten in de opslag, omdat de onderliggende moment opnamen die zijn gemaakt door Azure Backup, behouden blijven. Het voor deel van het verlaten van de herstel punten is dat u de bestands share later kunt herstellen. Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/backup/)voor meer informatie over de kosten voor het verlaten van de herstel punten. Als u besluit alle herstel punten te verwijderen, kunt u de bestands share niet herstellen.

Ga als volgt te werk om de beveiliging van een Azure-bestandsshare te stoppen:

1. Open de Recovery Services kluis die de herstel punten voor bestands shares bevat. Selecteer **Back-upitems** onder de sectie **beveiligde items** . De lijst met typen back-upitems wordt weer gegeven.

   ![Back-upitems](./media/manage-afs-backup/backup-items.png)

1. In de lijst **Type back-upbeheer** selecteert u **Azure Storage (Azure-bestanden)**. De lijst **Back-upitems (Azure Storage (Azure files))** wordt weer gegeven.

   ![Azure Storage selecteren (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Selecteer in de lijst **Back-upitems (Azure Storage (Azure files))** het back-upitem waarvoor u de beveiliging wilt stoppen.

1. Selecteer de optie **back-up stoppen** .

   ![Back-up stoppen selecteren](./media/manage-afs-backup/stop-backup.png)

1. In het deel venster **back-up stoppen** selecteert u **back-upgegevens behouden** of **back-upgegevens verwijderen**. Selecteer vervolgens **back-up stoppen**.

    ![Selecteer back-upgegevens behouden of back-upgegevens verwijderen](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>De beveiliging voor een bestandsshare hervatten

Als de optie **back-upgegevens behouden** is geselecteerd toen de beveiliging voor de bestands share werd gestopt, is het mogelijk de beveiliging te hervatten. Als de optie **back-upgegevens verwijderen** is geselecteerd, kan de beveiliging voor de bestands share niet worden hervat.

De beveiliging van de Azure-bestands share hervatten:

1. Open de Recovery Services kluis die de herstel punten voor bestands shares bevat. Selecteer **Back-upitems** onder de sectie **beveiligde items** . De lijst met typen back-upitems wordt weer gegeven.

   ![Back-upitems voor hervatten](./media/manage-afs-backup/backup-items-resume.png)

1. In de lijst **Type back-upbeheer** selecteert u **Azure Storage (Azure-bestanden)**. De lijst **Back-upitems (Azure Storage (Azure files))** wordt weer gegeven.

   ![Lijst met Azure Storage (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Selecteer in de lijst **Back-upitems (Azure Storage (Azure files))** het back-upitem waarvoor u de beveiliging wilt hervatten.

1. Selecteer de optie **back-up hervatten** .

   ![Back-up hervatten selecteren](./media/manage-afs-backup/resume-backup.png)

1. Het deel venster **back-upbeleid** wordt geopend. Selecteer een beleid van uw keuze om de back-up te hervatten.

1. Nadat u een back-upbeleid hebt geselecteerd, selecteert u **Opslaan**. U ziet het bericht ' update wordt uitgevoerd ' in de portal. Nadat de back-up is hervat, wordt het bericht ' het back-upbeleid is bijgewerkt voor de beveiligde Azure-bestands share ' weer gegeven.

   ![Het back-upbeleid is bijgewerkt](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Back-upgegevens verwijderen

U kunt de back-up van een bestands share tijdens het stoppen van de **back-** uptaak verwijderen of op elk gewenst moment nadat u de beveiliging hebt gestopt. Het kan handig zijn om dagen of zelfs weken te wachten voordat u de herstel punten verwijdert. Wanneer u back-upgegevens verwijdert, kunt u geen specifieke herstel punten kiezen die u wilt verwijderen. Als u besluit om uw back-upgegevens te verwijderen, verwijdert u alle herstel punten die zijn gekoppeld aan de bestands share.

In de volgende procedure wordt ervan uitgegaan dat de beveiliging voor de bestands share is gestopt.

Back-upgegevens voor de Azure-bestands share verwijderen:

1. Nadat de back-uptaak is gestopt, zijn de opties **back-up** en **back-upgegevens verwijderen** beschikbaar in het dash board **back-upitem** . Selecteer de optie **back-upgegevens verwijderen** .

   ![Back-upgegevens verwijderen](./media/manage-afs-backup/delete-backup-data.png)

1. Het deel venster **back-upgegevens verwijderen** wordt geopend. Voer de naam van de bestands share in om de verwijdering te bevestigen. U kunt desgewenst meer informatie opgeven in de vakken **reden** of **opmerkingen** . Nadat u zeker weet dat u de back-upgegevens hebt verwijderd, selecteert u **verwijderen**.

   ![Verwijderen van gegevens bevestigen](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Registratie van een opslag account ongedaan maken

Als u uw bestands shares in een bepaald opslag account wilt beveiligen met behulp van een andere Recovery Services kluis, moet u eerst de [beveiliging stoppen voor alle bestands shares](#stop-protection-on-a-file-share) in dat opslag account. Hef de registratie van het account vervolgens op bij de huidige Recovery Services kluis die wordt gebruikt voor de beveiliging.

In de volgende procedure wordt ervan uitgegaan dat de beveiliging is gestopt voor alle bestands shares in het opslag account waarvoor u de registratie ongedaan wilt maken.

De registratie van het opslag account ongedaan maken:

1. Open de Recovery Services kluis waar uw opslag account is geregistreerd.
1. Selecteer in het deel venster **overzicht** de optie **back-upinfrastructuur** in het gedeelte **beheren** .

   ![Back-upinfrastructuur selecteren](./media/manage-afs-backup/backup-infrastructure.png)

1. Het deel venster **back-upinfrastructuur** wordt geopend. Selecteer **opslag accounts** in de sectie **Azure Storage accounts** .

   ![Opslag accounts selecteren](./media/manage-afs-backup/storage-accounts.png)

1. Nadat u **opslag accounts** hebt geselecteerd, wordt er een lijst weer gegeven met opslag accounts die zijn geregistreerd bij de kluis.
1. Klik met de rechter muisknop op het opslag account dat u wilt verwijderen en selecteer **registratie ongedaan maken**.

   ![Selecteer registratie ongedaan maken](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met back-ups van Azure-bestands shares oplossen](./troubleshoot-azure-files.md)voor meer informatie.
