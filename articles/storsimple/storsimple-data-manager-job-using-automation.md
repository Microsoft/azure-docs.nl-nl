---
title: Azure Automation gebruiken om een taak in StorSimple Data Manager te starten
description: Meer informatie over het gebruik van Azure Automation voor het activeren van StorSimple Data Manager taken
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 3d0103857b6f759560129dbe8e693ec6c0d7291e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961206"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Azure Automation gebruiken om een taak te activeren

In dit artikel wordt uitgelegd hoe u de functie voor gegevens transformatie binnen de StorSimple Data Manager-service kunt gebruiken om StorSimple te transformeren. U kunt een gegevens transformatie taak op twee manieren starten: 

 - De .NET SDK gebruiken
 - Azure Automation runbook gebruiken
 
In dit artikel vindt u informatie over het maken van een Azure Automation runbook en het vervolgens gebruiken om een taak voor gegevens transformatie te initiëren. Ga voor meer informatie over het initiëren van gegevens transformatie via .NET SDK naar [.NET SDK gebruiken om taken voor gegevens transformatie te activeren](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat:

*   Azure PowerShell geïnstalleerd op de client computer. [Down load Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
*   Een correct geconfigureerde taak definitie in een StorSimple Data Manager-service binnen een resource groep.
*   Down load  [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) het bestand uit de GitHub-opslag plaats. 
*   Down load [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) script uit de GitHub-opslag plaats.

## <a name="step-by-step-procedure"></a>Stapsgewijze procedure

### <a name="set-up-the-automation-account"></a>Het Automation-account instellen

1. Maak een Azure run as Automation-account in de Azure Portal. Ga hiervoor naar **Azure marketplace > alles** en zoek vervolgens naar **Automation**. Selecteer **Automation-accounts**.

    ![Run as Automation-account maken](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Klik op **+ toevoegen** om een nieuw Automation-account toe te voegen.

    ![Run as Automation-account 2 maken](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. In het **onderdeel Automation toevoegen**:

   1. Geef de **naam** van uw Automation-account op.
   2. Selecteer het **abonnement** dat is gekoppeld aan uw StorSimple Data Manager service.
   3. Maak een nieuwe resource groep of Selecteer deze uit een bestaande resource groep.
   4. Selecteer een **locatie**.
   5. Zorg ervoor dat de optie standaard **uitvoeren als-account maken** is geselecteerd.
   6. Als u een koppeling voor snelle toegang wilt krijgen op het dash board, controleert **u het vastmaken aan dash board**. Klik op **Create**.

      ![Run as Automation-account 3 maken](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Nadat het Automation-account is gemaakt, wordt u hiervan op de hoogte gebracht.
    
      ![Melding voor de implementatie van het Automation-account](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Ga naar [een uitvoeren als-account maken](../automation/manage-runas-account.md)voor meer informatie.

3. Ga in het zojuist gemaakte account naar **gedeelde Resources > modules** en klik op **+ add module**.

    ![Module 1 importeren](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Blader naar de locatie van `DataTransformationApp.zip` het bestand op de lokale computer en selecteer en open de module. Klik op **OK** om de module te importeren.

    ![Module 2 importeren](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Wanneer Azure Automation een module in uw account importeert, worden de meta gegevens van de module geëxtraheerd. Deze bewerking kan enkele minuten duren.

   ![Module 4 importeren](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. U ontvangt een melding dat de module wordt geïmplementeerd en een andere melding wanneer het proces is voltooid.  De status in **modules** wordt gewijzigd in **beschikbaar**.

    ![Module 5 importeren](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Automation-runbook importeren, publiceren en uitvoeren

Voer de volgende stappen uit om het runbook te importeren, te publiceren en uit te voeren om de taak definitie te activeren.

1. Open uw Automation-account in Azure Portal. Ga naar **proces automatisering > Runbooks** en klik op **+ een runbook toevoegen**.

    ![Runbook 1 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. Klik in **Runbook toevoegen** op **een bestaand runbook importeren**.

3. Ga naar het Azure PowerShell script bestand `Trigger-DataTransformation-Job.ps1` voor het **Runbook-bestand**. Het type runbook wordt automatisch geselecteerd. Geef een naam en een optionele beschrijving voor het runbook op. Klik op **Create**.

    ![Runbook 2 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Het nieuwe runbook wordt weer gegeven in de lijst met runbooks voor het Automation-account. Selecteer en klik op dit runbook.

    ![Runbook 3 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Bewerk het runbook en klik op **test** venster.

    ![Runbook 4 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Geef de para meters op, zoals de naam van uw StorSimple Data Manager service, de gekoppelde resource groep en de naam van de taak definitie. **Start** de test. Het rapport wordt gegenereerd wanneer de uitvoering is voltooid. Ga voor meer informatie naar How to [test a runbook](../automation/learn/automation-tutorial-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Runbook 8 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Controleer de uitvoer van het runbook in het test venster. Als u tevreden bent, sluit u het deel venster. Klik op **publiceren** en wanneer u wordt gevraagd om bevestiging, bevestig het runbook en publiceer het.

    ![Runbook 6 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Ga terug naar **Runbooks** en selecteer het zojuist gemaakte runbook.

    ![Runbook 7 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Start** het runbook. Voer in **Runbook starten** alle para meters in. Klik op **OK** om de gegevens transformatie taak te verzenden en te starten.

10. Als u de voortgang van de taak in Azure Portal wilt controleren, gaat u naar **taken** in uw StorSimple Data Manager-service. Selecteer en klik op de taak om de taak details weer te geven.

    ![Runbook 10 toevoegen](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Volgende stappen

[Gebruik StorSimple Data Manager gebruikers interface om uw gegevens te transformeren](storsimple-data-manager-ui.md).