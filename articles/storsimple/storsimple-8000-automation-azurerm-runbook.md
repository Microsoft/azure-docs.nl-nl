---
title: Azure Automation Runbook gebruiken voor het beheren van StorSimple-apparaten
description: Meer informatie over het gebruik van Azure Automation Runbook voor het beheren van uw StorSimple 8000 Series-apparaat in Azure Portal.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: b5f2a86fb7c776436ea707ef293a66c033014c33
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021075"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Azure Automation runbooks gebruiken voor het beheren van StorSimple-apparaten

In dit artikel wordt beschreven hoe Azure Automation runbooks worden gebruikt voor het beheren van uw StorSimple 8000 Series-apparaat in Azure Portal. Er is een voor beeld van een runbook opgenomen om u te helpen bij het configureren van uw omgeving om dit runbook uit te voeren.


## <a name="configure-add-and-run-azure-runbook"></a>Azure runbook configureren, toevoegen en uitvoeren

In deze sectie vindt u een voor beeld van een Windows Power shell-script voor StorSimple en worden de verschillende stappen beschreven die nodig zijn om het script te importeren in een runbook en vervolgens het runbook te publiceren en uit te voeren.

### <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat:

* een actief Azure-abonnement dat is gekoppeld aan uw StorSimple-Apparaatbeheer service, geregistreerd bij een StorSimple 8000 Series-apparaat.

* Windows Power shell 5,0 is geïnstalleerd op uw computer (of uw Windows Server-host voor uw StorSimple als u er een gebruikt).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Een Automation runbook-module maken in Windows Power shell

Voer de volgende stappen uit om een Automation-module te maken voor het StorSimple van de 8000-serie:

1. Start Windows Power shell. Maak een nieuwe map en wijzig de map in de nieuwe map.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Down load NUGET cli](https://www.nuget.org/downloads) in de map die u in de vorige stap hebt gemaakt. Er zijn verschillende versies van _nuget.exe_. Kies de versie die overeenkomt met uw SDK. Elke download koppeling verwijst rechtstreeks naar een _exe_ -bestand. Zorg ervoor dat u met de rechter muisknop klikt en het bestand op uw computer opslaat in plaats van het uit te voeren vanuit de browser.

    U kunt ook de volgende opdracht uitvoeren om het script te downloaden en op te slaan in dezelfde map die u eerder hebt gemaakt.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Down load de afhankelijke SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Down load het script uit het voorbeeld project GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Een Azure Automation Runbook-module maken voor StorSimple van de 8000-serie. Typ in het venster Windows Power shell de volgende opdrachten:

    ```powershell
        # set path variables
        $downloadDir = "C:\scripts\StorSimpleSDKTools"
        $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

        #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
        mkdir "$moduleDir"
        Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
        Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

        #Don't change the name of the Archive
        compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

6. Controleer of een zip-bestand van de Automation-module is gemaakt in `C:\scripts\StorSimpleSDKTools` .

    ![Scherm opname toont de map StorSimpleSDKTools met het zip-bestand van de Automation-module](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. De volgende uitvoer wordt weer gegeven wanneer de Automation-module wordt gemaakt via de Windows Power shell.

    ```powershell
    mkdir C:\scripts\StorSimpleSDKTools
    ```

    ```Output
        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools
    ```

    ```powershell
    wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 717.48 ms
    ```

    ```powershell
    wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    # set path variables
    $downloadDir = "C:\scripts\StorSimpleSDKTools"
    $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"
    #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
    mkdir "$moduleDir"
    ```

    ```Output
        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series
    ```

    ```powershell
    Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
    Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    #Don't change the name of the Archive
    compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

### <a name="import-publish-and-run-automation-runbook"></a>Automation-runbook importeren, publiceren en uitvoeren

1. Maak een Azure run as Automation-account in de Azure Portal. Ga hiervoor naar **Azure marketplace > alles** en zoek vervolgens naar **Automation**. Selecteer **Automation-accounts**.

    ![zoeken-automatisering](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. Op de Blade **Automation-account toevoegen** :

   1. Geef de **naam** van uw Automation-account op.
   2. Selecteer het **abonnement** dat is gekoppeld aan uw StorSimple-Apparaatbeheer service.
   3. Maak een nieuwe resource groep of Selecteer deze uit een bestaande resource groep.
   4. Selecteer een **locatie** (indien mogelijk hetzelfde als waar uw service wordt uitgevoerd).
   5. Zorg ervoor dat de optie standaard **uitvoeren als-account maken** is geselecteerd.
   6. Controleer eventueel **vastmaken aan dash board**. Klik op **Create**.

       ![Scherm afbeelding toont het deel venster Automation-account toevoegen met waarden voor abonnement, een uitvoeren als-account voor Azure maken en 2 maken](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Nadat het Automation-account is gemaakt, wordt u hiervan op de hoogte gebracht. Ga naar [een uitvoeren als-account maken](../automation/manage-runas-account.md)voor meer informatie over het maken van een Automation-account.

3. Om ervoor te zorgen dat het Automation-account dat is gemaakt, toegang heeft tot de StorSimple Apparaatbeheer-service, moet u de juiste machtigingen toewijzen aan het Automation-account. Ga naar **toegangs beheer** in uw StorSimple-Apparaatbeheer service. Klik op **+ toevoegen** en geef de naam van uw Azure Automation-account op. **Sla** de instellingen op.

    ![add-permissions-Automation-account](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. Ga in het zojuist gemaakte account naar **gedeelde Resources > modules** en klik op **+ add module**.

5. Blader op de Blade **module toevoegen** naar de locatie van de gezipte module en selecteer en open de module. Klik op **OK**.

    ![Scherm afbeelding toont het deel venster module toevoegen met een waarde voor het uploaden van bestanden en OK](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Ga naar **proces automatisering > Runbooks en klik op + een Runbook toevoegen**. Klik op **een bestaand Runbook importeren** op de Blade **runbook toevoegen** . Ga naar het Windows Power shell-script bestand voor het **Runbook-bestand**. Het type runbook wordt automatisch geselecteerd. Geef een naam en een optionele beschrijving voor het runbook op. Klik op **Create**.

    ![invoeg module](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Het runbook wordt toegevoegd aan de lijst met runbooks. Selecteer en klik op dit runbook.

    ![Klik op-nieuw-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Bewerk het runbook en klik op **test venster**. Geef de para meters op, zoals de naam van de StorSimple-Apparaatbeheer service, de naam van het StorSimple-apparaat en het-abonnement. **Start** de test. Het rapport wordt gegenereerd wanneer de uitvoering is voltooid. Ga voor meer informatie naar [How to test a runbook](../automation/learn/automation-tutorial-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Scherm afbeelding toont de test pan waar u parameter waarden canenter en een test start](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Controleer de uitvoer van het runbook in het test venster. Als u tevreden bent, sluit u het deel venster. Klik op **publiceren** en wanneer u wordt gevraagd om bevestiging, bevestig het runbook en publiceer het.

    ![Scherm afbeelding toont het deel venster Power shell-Runbook bewerken waarin wordt gevraagd of u wilt door gaan met het publiceren van het Runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Volgende stappen

[Gebruik StorSimple Apparaatbeheer service om uw StorSimple-apparaat te beheren](storsimple-8000-manager-service-administration.md).