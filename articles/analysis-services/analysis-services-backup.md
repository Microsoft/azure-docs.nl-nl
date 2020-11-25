---
title: Back-up en herstel van Azure Analysis Services Data Base | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een back-up maakt van model meta gegevens en gegevens van een Azure Analysis Services Data Base.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: af1850f77c1d13c761bfc2a143074b5067b349b4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013000"
---
# <a name="analysis-services-database-backup-and-restore"></a>Back-up en herstel van Analysis Services-Data Base

Het maken van een back-up van tabellaire model databases in Azure Analysis Services is veel hetzelfde als voor on-premises Analysis Services. Het belangrijkste verschil is waar u uw back-upbestanden opslaat. Back-upbestanden moeten worden opgeslagen in een container in een [Azure-opslag account](../storage/common/storage-account-create.md). U kunt een opslag account en een container gebruiken die u al hebt, of ze kunnen worden gemaakt bij het configureren van opslag instellingen voor uw server.

> [!NOTE]
> Het maken van een opslag account kan resulteren in een nieuwe factureer bare service. Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie.
> 
> 

> [!NOTE]
> Als het opslag account zich in een andere regio bevindt, configureert u Firewall-instellingen voor het opslag account om toegang vanaf **geselecteerde netwerken** toe te staan. Geef in het **adres bereik** van de firewall het IP-adres bereik op voor de regio waarin de Analysis Services-server zich bevindt. Het configureren van Firewall instellingen voor opslag accounts om toegang vanaf alle netwerken toe te staan, wordt ondersteund, maar het kiezen van geselecteerde netwerken en het opgeven van een IP-adres bereik verdient de voor keur. Zie [Veelgestelde vragen over netwerk connectiviteit](analysis-services-network-faq.md#backup-and-restore)voor meer informatie.

Back-ups worden opgeslagen met de extensie. ABF. Voor in-Memory tabellaire modellen worden zowel model gegevens als meta gegevens opgeslagen. Voor DirectQuery-modellen in tabel vorm worden alleen model meta gegevens opgeslagen. Back-ups kunnen worden gecomprimeerd en versleuteld, afhankelijk van de opties die u kiest.


## <a name="configure-storage-settings"></a>Opslag instellingen configureren
Voordat u een back-up maakt, moet u de opslag instellingen voor uw server configureren.


### <a name="to-configure-storage-settings"></a>Opslag instellingen configureren
1.  Klik in Azure Portal >- **instellingen** op **back-up**.

    ![Back-ups in instellingen](./media/analysis-services-backup/aas-backup-backups.png)

2.  Klik op **ingeschakeld** en klik vervolgens op **opslag instellingen**.

    ![Inschakelen](./media/analysis-services-backup/aas-backup-enable.png)

3. Selecteer uw opslag account of maak een nieuwe.

4. Selecteer een container of maak een nieuwe.

    ![Container selecteren](./media/analysis-services-backup/aas-backup-container.png)

5. Sla de back-upinstellingen op.

    ![Back-upinstellingen opslaan](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Back-up maken met behulp van SSMS

1. Klik in SSMS met de rechter muisknop op een Data Base > **Maak een back-up**.

2. Klik in back- **updatabasebestand**  >  **Backup file** op **Bladeren**.

3. Controleer in het dialoog venster **bestand opslaan als** het mappad en typ een naam voor het back-upbestand. 

4. Selecteer opties in het dialoog venster **back-updatabase** .

    **Overschrijven van bestand toestaan** : Selecteer deze optie als u de back-upbestanden met dezelfde naam wilt overschrijven. Als deze optie niet is ingeschakeld, kan het bestand dat u opslaat niet dezelfde naam hebben als een bestand dat al op dezelfde locatie bestaat.

    **Compressie Toep assen** : Selecteer deze optie om het back-upbestand te comprimeren. Gecomprimeerde back-upbestanden besparen schijf ruimte, maar vereisen iets hoger CPU-gebruik. 

    **Back-upbestand versleutelen** : Selecteer deze optie om het back-upbestand te versleutelen. Voor deze optie is een door de gebruiker opgegeven wacht woord vereist om het back-upbestand te beveiligen. Het wacht woord voor komt dat de back-upgegevens op een andere manier worden gelezen dan een herstel bewerking. Als u ervoor kiest om back-ups te versleutelen, moet u het wacht woord opslaan op een veilige locatie.

5. Klik op **OK** om het back-upbestand te maken en op te slaan.


### <a name="powershell"></a>PowerShell
[Back-up-ASDatabase](/powershell/module/sqlserver/backup-asdatabase) -cmdlet gebruiken.

## <a name="restore"></a>Herstellen
Bij het herstellen moet het back-upbestand zich in het opslag account bevinden dat u hebt geconfigureerd voor uw server. Als u een back-upbestand van een on-premises locatie naar uw opslag account wilt verplaatsen, gebruikt u [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) of het [AzCopy](../storage/common/storage-use-azcopy-v10.md) -opdracht regel programma. 



> [!NOTE]
> Als u een on-premises server wilt herstellen, moet u alle domein gebruikers uit de rollen van het model verwijderen en ze weer toevoegen aan de rollen als Azure Active Directory gebruikers.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Herstellen met behulp van SSMS

1. Klik in SSMS met de rechter muisknop op een Data Base > **herstellen**.

2. Klik in het dialoog venster **back-updatabase** in **back-upbestand** op **Bladeren**.

3. Selecteer in het dialoog venster **database bestanden zoeken** het bestand dat u wilt herstellen.

4. Selecteer de data base in **Data Base herstellen**.

5. Opties opgeven. Beveiligings opties moeten overeenkomen met de back-upopties die u hebt gebruikt bij het maken van een back-up.


### <a name="powershell"></a>PowerShell

Gebruik de cmdlet [Restore-ASDatabase](/powershell/module/sqlserver/restore-asdatabase) .


## <a name="related-information"></a>Gerelateerde informatie

[Azure-opslagaccounts](../storage/common/storage-account-create.md)  
[Hoge Beschik baarheid](analysis-services-bcdr.md)      
[Veelgestelde vragen over de netwerk verbinding Analysis Services](analysis-services-network-faq.md)