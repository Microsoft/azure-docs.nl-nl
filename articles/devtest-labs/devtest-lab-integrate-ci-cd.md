---
title: Azure DevTest Labs integreren in uw Azure-pijp lijnen
description: Meer informatie over het integreren van Azure DevTest Labs in uw Azure pipelines continue integratie en leverings pijplijn
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 96f99d41d0a7ea07bf3854292f9c3bd6245414b3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87288930"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Azure DevTest Labs integreren in uw CI/CD-pijp lijn van Azure pipelines

U kunt de uitbrei ding *Azure DevTest Labs taken* gebruiken voor het integreren van de continue integratie van Azure pipelines en de pijp lijnen voor continue levering (CI/cd) met Azure DevTest Labs. De uitbrei ding installeert verschillende taken, waaronder: 

- Een virtuele machine (VM) maken
- Een aangepaste installatiekopie maken vanaf een virtuele machine
- Een VM verwijderen 

Met deze taken kunt u bijvoorbeeld snel een *gouden installatie kopie* -VM implementeren voor een specifieke test taak en vervolgens de virtuele machine verwijderen wanneer de test is voltooid.

In dit artikel wordt beschreven hoe u Azure DevTest Labs-taken gebruikt voor het maken en implementeren van een virtuele machine, het maken van een aangepaste installatie kopie en het verwijderen van de virtuele machine, allemaal als een release pijplijn. Normaal gesp roken voert u de taken afzonderlijk uit in uw eigen aangepaste bouw-, test-en implementatie pijplijnen.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Vereisten

- Registreer of Meld u aan bij uw [Azure DevOps](https://dev.azure.com) -organisatie en [Maak een project](/vsts/organizations/projects/create-project) in de organisatie. 
  
- Installeer de extensie Azure DevTest Labs taken vanuit Visual Studio Marketplace.
  
  1. Ga naar [Azure DevTest Labs taken](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. Selecteer **gratis downloaden**.
  1. Selecteer uw Azure DevOps-organisatie in de vervolg keuzelijst en selecteer **installeren**. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>De sjabloon maken voor het bouwen van een Azure VM 

In deze sectie wordt beschreven hoe u de Azure Resource Manager sjabloon maakt die u gebruikt om een Azure VM op aanvraag te maken.

1. Als u een resource manager-sjabloon in uw abonnement wilt maken, volgt u de procedure in [een resource manager-sjabloon gebruiken](devtest-lab-use-resource-manager-template.md).
   
1. Voordat u de Resource Manager-sjabloon genereert, voegt u het [WinRM-artefact](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) toe als onderdeel van het maken van de virtuele machine. Voor implementatie taken zoals *Azure File Copy* en *Power shell op doel machines* is WinRM-toegang vereist. Voor het WinRM-artefact is een hostnaam vereist als para meter. dit moet de Fully Qualified Domain Name (FQDN) van de virtuele machine zijn. 
   
   > [!NOTE]
   > Wanneer u WinRM gebruikt met een gedeeld IP-adres, moet u een NAT-regel toevoegen om een externe poort toe te wijzen aan de WinRM-poort. U hebt de NAT-regel niet nodig als u de virtuele machine met een openbaar IP-adres maakt.
   
   
1. Sla de sjabloon op uw computer op als een bestand met de naam *CreateVMTemplate.jsop*.
   
1. Check de sjabloon in op uw broncode beheer systeem.

## <a name="create-a-script-to-get-vm-properties"></a>Een script maken voor het ophalen van VM-eigenschappen

Wanneer u taken uitvoert zoals *Azure File Copy* of *Power shell op doel computers* in de release pijplijn, verzamelt het volgende script de waarden die u nodig hebt om een app te implementeren in een VM. Normaal gesp roken gebruikt u deze taken om uw app te implementeren op een Azure-VM. Voor de taken zijn waarden vereist, zoals de naam van de VM-resource groep, het IP-adres en de FQDN.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Het script bestand maken:

1. Open een tekst editor en plak het volgende script in het bestand.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Sla het bestand op met een naam als *GetLabVMParams.ps1* en controleer dit in uw broncode beheersysteem. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Een release-pijplijn in Azure Pipelines maken

Een nieuwe release pijplijn maken:

1. Selecteer op de pagina Azure DevOps-project **pijp lijnen**  >   in het linkernavigatievenster.
1. Selecteer **nieuwe pijp lijn**.
1. Schuif onder **Selecteer een sjabloon** omlaag, selecteer **lege taak** en selecteer vervolgens **Toep assen**.

### <a name="add-and-set-variables"></a>Variabelen toevoegen en instellen

De pijplijn taken gebruiken de waarden die u aan de virtuele machine hebt toegewezen toen u de Resource Manager-sjabloon in de Azure Portal hebt gemaakt. 

Variabelen toevoegen voor de waarden: 

1. Op de pijplijn pagina selecteert u het tabblad **variabelen** .
   
1. Selecteer voor elke variabele **toevoegen** en voer de naam en waarde in:
   
   |Name|Waarde|
   |---|---|
   |*vmName*|De naam van de virtuele machine die u hebt toegewezen in de Resource Manager-sjabloon|
   |*userName*|Gebruikers naam voor toegang tot de virtuele machine|
   |*password*|Wacht woord voor de gebruikers naam. Selecteer het vergrendelings pictogram om het wacht woord te verbergen en te beveiligen.

### <a name="create-a-devtest-labs-vm"></a>Een DevTest Labs-VM maken

De volgende stap is het maken van de gouden installatie kopie-VM die moet worden gebruikt voor toekomstige implementaties. U maakt de virtuele machine in uw Azure DevTest Labs-exemplaar met behulp van de *Azure DEVTEST Labs VM* -taak maken.

1. Selecteer op het tabblad pijp lijn van release **pijp lijn de** tekst met de Hyper link in **fase 1** om **fase taken weer te geven** en selecteer vervolgens het plus teken **+** naast **Agent taak**. 
   
1. Onder **taken toevoegen** selecteert u **Azure DevTest Labs virtuele machine maken** en selecteert u **toevoegen**. 
   
1. Selecteer **Azure DEVTEST Labs VM maken** in het linkerdeel venster. 

1. Vul in het rechterdeelvenster het formulier als volgt in:
   
   |Veld|Waarde|
   |---|---|
   |**Azure RM-abonnement**|Selecteer een service verbinding of abonnement bij **beschik bare Azure-service verbindingen** of **beschik bare Azure-abonnementen** in de vervolg keuzelijst en Selecteer indien nodig **autoriseren** .<br /><br />**Opmerking:** Zie [Azure Resource Manager service-eind punt](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)voor meer informatie over het maken van een verbinding met beperkte machtigingen voor uw Azure-abonnement.|
   |**Naam van Lab**|Selecteer de naam van een bestaand Lab waarin de VM van het lab wordt gemaakt.|
   |**Sjabloon naam**|Geef het volledige pad en de naam op van het sjabloon bestand dat u hebt opgeslagen in de opslag plaats van de bron code. U kunt de ingebouwde eigenschappen gebruiken om het pad te vereenvoudigen, bijvoorbeeld:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Sjabloon parameters**|Voer de para meters in voor de variabelen die u eerder hebt gedefinieerd:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**Uitvoer variabelen**  >  **VM-id Lab**|Voer de variabele voor de gemaakte Lab-VM-ID in. Als u de standaard **labVMId** gebruikt, kunt u naar de variabele in volgende taken verwijzen als *$ (labVMId)*.<br /><br />U kunt een andere naam dan de standaard waarde maken, maar vergeet niet om de juiste naam in volgende taken te gebruiken. U kunt de test-VM-ID in het volgende formulier schrijven:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>De details van de DevTest Labs-VM verzamelen

Voer het script dat u eerder hebt gemaakt uit om de details van de virtuele machine van DevTest Labs te verzamelen. 

1. Selecteer op het tabblad pijp lijn van release **pijp lijn de** tekst met de Hyper link in **fase 1** om **fase taken weer te geven** en selecteer vervolgens het plus teken **+** naast **Agent taak**. 
   
1. Selecteer onder **taken toevoegen** de optie **Azure PowerShell** en selecteer **toevoegen**. 
   
1. Selecteer **Azure PowerShell script: filepath** in het linkerdeel venster. 
   
1. Vul in het rechterdeelvenster het formulier als volgt in:
   
   |Veld|Waarde|
   |---|---|
   |**Type Azure-verbinding**|Selecteer **Azure Resource Manager**.|
   |**Azure-abonnement**|Selecteer uw service verbinding of abonnement.| 
   |**Script type**|Selecteer het **pad naar het script bestand**.|
   |**Scriptpad**|Geef het volledige pad en de naam op van het Power shell-script dat u hebt opgeslagen in de opslag plaats van de bron code. U kunt de ingebouwde eigenschappen gebruiken om het pad te vereenvoudigen, bijvoorbeeld:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Script argumenten**|Voer de naam in van de *labVmId* -variabele die is gevuld door de vorige taak, bijvoorbeeld:<br /><br />`-labVmId '$(labVMId)'`|

Het script verzamelt de vereiste waarden en slaat ze op in omgevings variabelen binnen de release-pijp lijn, zodat u deze eenvoudig kunt raadplegen in de volgende stappen.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Een VM-installatie kopie maken van de DevTest Labs-VM

De volgende taak is het maken van een installatie kopie van de zojuist geïmplementeerde virtuele machine in uw Azure DevTest Labs-exemplaar. U kunt vervolgens de installatie kopie gebruiken om kopieën van de VM op aanvraag te maken wanneer u een dev-taak wilt uitvoeren of een aantal tests wilt uitvoeren. 

1. Selecteer op het tabblad pijp lijn van release **pijp lijn de** tekst met de Hyper link in **fase 1** om **fase taken weer te geven** en selecteer vervolgens het plus teken **+** naast **Agent taak**. 
   
1. Selecteer onder **taken toevoegen** de optie **Azure DevTest Labs aangepaste installatie kopie maken** en selecteer **toevoegen**. 
   
1. Configureer de taak als volgt:
   
   |Veld|Waarde|
   |---|---|
   |**Azure RM-abonnement**|Selecteer uw service verbinding of abonnement.|
   |**Naam van Lab**|Selecteer de naam van een bestaand Lab waarin de installatie kopie wordt gemaakt.|
   |**Naam van aangepaste installatie kopie**|Voer een naam in voor de aangepaste installatie kopie.|
   |**Beschrijving** (optioneel)|Voer een beschrijving in zodat u de juiste installatie kopie later eenvoudig kunt selecteren.|
   |**Bron Lab VM**  >  **VM-id van bron Lab**|Als u de standaard naam van de variabele LabVMId hebt gewijzigd, voert u deze hier in. De standaard waarde is **$ (labVMId)**.|
   |**Uitvoer variabelen**  >  **Aangepaste installatie kopie-id**|U kunt de standaard naam van de variabele indien nodig bewerken.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Uw app implementeren op de DevTest Labs VM (optioneel)

U kunt taken toevoegen om uw app te implementeren op de nieuwe DevTest Labs VM. De taken die u doorgaans gebruikt om de app te implementeren, zijn *Azure File Copy* en *Power shell op doel computers*.

De VM-gegevens die u nodig hebt voor de para meters van deze taken, worden opgeslagen in drie configuratie variabelen met de naam **labVmRgName**, **labVMIpAddress** en **labVMFqdn** binnen de release pijplijn. Als u alleen wilt experimenteren met het maken van een DevTest Labs-VM en een aangepaste installatie kopie, zonder dat u een app implementeert, kunt u deze stap overs Laan.

### <a name="delete-the-vm"></a>De VM verwijderen

De laatste taak is het verwijderen van de virtuele machine die u in uw Azure DevTest Labs-exemplaar hebt geïmplementeerd. Normaal gesp roken verwijdert u de virtuele machine nadat u de ontwikkel taken hebt uitgevoerd of voert u de tests uit die u nodig hebt op de geïmplementeerde VM. 

1. Selecteer op het tabblad pijp lijn van release **pijp lijn de** tekst met de Hyper link in **fase 1** om **fase taken weer te geven** en selecteer vervolgens het plus teken **+** naast **Agent taak**. 
   
1. Onder **taken toevoegen** selecteert u **Azure DevTest Labs virtuele machine verwijderen** en selecteert u **toevoegen**. 
   
1. Configureer de taak als volgt:
   
   - Selecteer uw service verbinding of abonnement onder **Azure RM-abonnement**. 
   - Als u de standaard naam van de variabele LabVMId hebt gewijzigd, voert u deze hier in voor de **test-VM-id**. De standaard waarde is **$ (labVMId)**.
   
### <a name="save-the-release-pipeline"></a>De release pijplijn opslaan

De nieuwe release pijplijn opslaan:

1. Selecteer de **nieuwe release pijplijn** naam op de pagina release pijplijn en voer een nieuwe naam in voor de pijp lijn. 
   
1. Selecteer het pictogram **Opslaan** in de rechter bovenhoek. 

## <a name="create-and-run-a-release"></a>Een release maken en uitvoeren

Een release maken en uitvoeren met de nieuwe pijp lijn:

1. Selecteer **vrijgave** in de rechter bovenhoek op de pagina release pijplijn. 
   
1. Selecteer onder **artefacten** de nieuwste build en selecteer vervolgens **maken**.
   
1. Vernieuw in elke release fase de weer gave van uw DevTest Labs-exemplaar in het Azure Portal om het maken van de VM, het maken van installatie kopieën en het verwijderen van de VM weer te geven.

U kunt de aangepaste installatie kopie gebruiken om Vm's te maken wanneer u ze nodig hebt.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [maken van omgevingen met meerdere vm's met Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md).
- Verken meer Quick Start-sjablonen voor DevTest Labs Automation van de [open bare DevTest Labs github opslag plaats](https://github.com/Azure/azure-quickstart-templates).
- Ga indien nodig naar de pagina voor het [oplossen van problemen met Azure DevOps](/azure/devops/pipelines/troubleshooting) .
 
