---
title: App terugzetten vanuit een back-up
description: Meer informatie over het herstellen van uw app vanuit een back-up. Bepaalde gekoppelde data bases kunnen samen met de app in één bewerking worden hersteld.
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: c1b0ce62905424032c2100a1a032fa43ba97578f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999396"
---
# <a name="restore-an-app-in-azure"></a>Een app in Azure herstellen
In dit artikel wordt beschreven hoe u een app kunt herstellen in [Azure app service](../app-service/overview.md) waarvan u eerder een back-up hebt gemaakt (zie een back-up van [uw app maken in azure](manage-backup.md)). U kunt uw app met de gekoppelde data bases op aanvraag herstellen naar een eerdere status of een nieuwe app maken op basis van een van de back-ups van de oorspronkelijke app. Azure App Service ondersteunt de volgende data bases voor back-up en herstel:
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL in-app](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Herstellen vanuit back-ups is beschikbaar voor apps die worden uitgevoerd in de laag **Standard** en **Premium** . Zie [een app omhoog schalen in azure](manage-scale-up.md)voor meer informatie over het omhoog schalen van uw app. Met de **Premium** -laag kunnen een groter aantal dagelijkse back-ups worden uitgevoerd dan de **Standard** -laag.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Een app herstellen vanuit een bestaande back-up
1. Klik op de pagina **instellingen** van uw app in de Azure Portal op **back-ups** om de pagina **back-ups** weer te geven. Klik vervolgens op **herstellen**.
   
    ![Kies nu herstellen][ChooseRestoreNow]
2. Selecteer op de pagina **herstellen** eerst de bron van de back-up.
   
    ![Scherm afbeelding die laat zien waar de back-upbron moet worden geselecteerd.](./media/web-sites-restore/021ChooseSource1.png)
   
    Met de optie **app-back-up** worden alle bestaande back-ups van de huidige app weer gegeven. u kunt er eenvoudig een selecteren.
    Met de optie **opslag** kunt u elk back-upzip-bestand selecteren op basis van een bestaand Azure Storage account en elke container in uw abonnement.
    Als u een back-up van een andere app wilt herstellen, gebruikt u de optie **opslag** .
3. Geef vervolgens de bestemming voor het herstellen van de app op in de **terugzet bestemming**.
   
    ![Scherm afbeelding die laat zien waar u de bestemming voor het herstellen van de app kunt opgeven.](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Als u **overschrijven** kiest, worden alle bestaande gegevens in uw huidige app gewist en overschreven. Voordat u op **OK** klikt, moet u ervoor zorgen dat deze precies overeenkomt met wat u wilt doen.
   > 
   > 
   
   > [!WARNING]
   > Als de App Service gegevens naar de data base schrijft terwijl u deze herstelt, kan dit leiden tot symptomen zoals een schending van de primaire sleutel en het verlies van gegevens. U wordt aangeraden de App Service eerst te stoppen voordat u begint met het herstellen van de data base.
   > 
   > 
   
    U kunt **bestaande app** selecteren om de back-up van de app te herstellen naar een andere app in dezelfde resource groep. Voordat u deze optie gebruikt, moet u al een andere app in uw resource groep hebben gemaakt met een spiegel database configuratie die is gedefinieerd in de app-back-up. U kunt ook een **nieuwe** app maken om uw inhoud te herstellen naar.

4. Klik op **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Een back-up van een opslag account downloaden of verwijderen
1. Selecteer op de pagina **overzicht** van de Azure Portal **opslag accounts**. Er wordt een lijst met bestaande opslag accounts weer gegeven.
2. Selecteer het opslag account dat de back-up bevat die u wilt downloaden of verwijderen. De pagina voor het opslag account wordt weer gegeven.
3. Selecteer op de pagina opslag account de gewenste container
   
    ![Containers weer geven][ViewContainers]
4. Selecteer een back-upbestand dat u wilt downloaden of verwijderen.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Klik op **downloaden** of **verwijderen** , afhankelijk van wat u wilt doen.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Een herstel bewerking bewaken
Als u meer wilt weten over het slagen of mislukken van de herstel bewerking van de app, gaat u naar de pagina **activiteiten logboek** in de Azure Portal.  
 

Schuif omlaag om de gewenste terugzet bewerking te vinden en klik om deze te selecteren.

Op de pagina Details wordt de beschik bare informatie weer gegeven met betrekking tot de herstel bewerking.

## <a name="automate-with-scripts"></a>Automatiseren met scripts

U kunt back-upbeheer automatiseren met scripts met behulp van [Azure cli](/cli/azure/install-azure-cli) of [Azure PowerShell](/powershell/azure/).

Zie voor voor beelden:

- [Azure CLI-voorbeelden](samples-cli.md)
- [Azure PowerShell-voor beelden](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
