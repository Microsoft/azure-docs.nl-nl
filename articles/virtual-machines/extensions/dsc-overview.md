---
title: Overzicht van desired state Configuration voor Azure
description: Meer informatie over het gebruik van de Microsoft Azure extensie-handler voor Power shell desired state Configuration (DSC). Het artikel bevat vereisten, architectuur en cmdlets.
services: virtual-machines
documentationcenter: ''
author: mgoedtel
manager: evansma
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 07/13/2020
ms.author: magoedte
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: dcdc325633aff5ab828cb1c82f4bb2d8becee967
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560035"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Inleiding tot de uitbreiding van de Desired State Configuration-handler

De Azure VM-agent en de bijbehorende uitbrei dingen maken deel uit van Microsoft Azure infrastructuur services. VM-extensies zijn software onderdelen waarmee de VM-functionaliteit wordt uitgebreid en verschillende bewerkingen voor VM-beheer worden vereenvoudigd.

De primaire use-case voor de Azure desired state Configuration (DSC)-extensie is het Boots trappen van een virtuele machine naar de service voor de [Azure Automation State Configuration (DSC)](../../automation/automation-dsc-overview.md).
De service biedt [voor delen](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service) die voortdurend beheer bieden over de configuratie van de virtuele machine en de integratie met andere operationele hulpprogram ma's, zoals Azure-bewaking.
Het gebruik van de uitbrei ding voor het registreren van virtuele machines bij de service biedt een flexibele oplossing die zelfs kan worden gebruikt in azure-abonnementen.

U kunt de DSC-extensie onafhankelijk van de Automation DSC-service gebruiken.
Hiermee wordt echter alleen een configuratie naar de virtuele machine gepusht.
Er is geen doorlopende rapportage beschikbaar, anders dan lokaal in de virtuele machine.

In dit artikel vindt u informatie over beide scenario's: het gebruik van de DSC-extensie voor het onboarden van Automation en het gebruik van de DSC-extensie als een hulp middel voor het toewijzen van configuraties aan Vm's met behulp van de Azure SDK.

## <a name="prerequisites"></a>Vereisten

- **Lokale computer**: voor interactie met de Azure VM-extensie moet u de Azure portal of de Azure PowerShell SDK gebruiken.
- **Gast agent**: de virtuele Azure-machine die is geconfigureerd door de DSC-configuratie moet een besturings systeem zijn dat Windows Management Framework (WMF) 4,0 of hoger ondersteunt. Zie de [versie geschiedenis](../../automation/automation-dsc-extension-history.md)van de DSC-extensie voor een volledige lijst met ondersteunde versies van het besturings systeem.

## <a name="terms-and-concepts"></a>Voor waarden en concepten

In deze hand leiding wordt ervan uitgegaan dat u bekend bent met de volgende concepten:

- **Configuratie**: een DSC-configuratie document.
- **Node**: een doel voor een DSC-configuratie. In dit document verwijst het *knoop punt* altijd naar een virtuele machine van Azure.
- **Configuratie gegevens**: een. psd1-bestand met omgevings gegevens voor een configuratie.

## <a name="architecture"></a>Architectuur

De Azure DSC-uitbrei ding maakt gebruik van het Azure VM-agent Framework voor het leveren, door voeren en rapporteren van DSC-configuraties die worden uitgevoerd op virtuele Azure-machines. De DSC-extensie accepteert een configuratie document en een set para meters. Als er geen bestand wordt gegeven, wordt een [standaard configuratie script](#default-configuration-script) Inge sloten met de extensie. Het standaard configuratie script wordt alleen gebruikt voor het instellen van meta gegevens in [lokale Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

Wanneer de uitbrei ding voor de eerste keer wordt aangeroepen, wordt een versie van WMF geïnstalleerd met behulp van de volgende logica:

- Als het besturings systeem van de Azure-VM Windows Server 2016 is, wordt er geen actie ondernomen. Voor Windows Server 2016 is al de meest recente versie van Power shell geïnstalleerd.
- Als de eigenschap **wmfVersion** is opgegeven, wordt die versie van WMF geïnstalleerd, tenzij die versie niet compatibel is met het besturings systeem van de virtuele machine.
- Als er geen eigenschap **wmfVersion** is opgegeven, wordt de meest recente versie van WMF geïnstalleerd.

Voor het installeren van WMF moet de computer opnieuw worden opgestart. Nadat het opnieuw is opgestart, wordt het zip-bestand gedownload dat is opgegeven in de eigenschap **modulesUrl** , indien opgegeven. Als deze locatie zich in Azure Blob-opslag bevindt, kunt u een SAS-token opgeven in de eigenschap **sasToken** om het bestand te openen. Nadat de zip is gedownload en uitgepakt, wordt de configuratie functie die is gedefinieerd in **configurationFunction** uitgevoerd om een. MOF-bestand ([Managed Object Format](/windows/win32/wmisdk/managed-object-format--mof-)) te genereren. De uitbrei ding wordt vervolgens uitgevoerd met `Start-DscConfiguration -Force` behulp van het gegenereerde. MOF-bestand. De uitbrei ding legt uitvoer vast en schrijft deze naar het status kanaal van Azure.

### <a name="default-configuration-script"></a>Standaard configuratie script

De Azure DSC-extensie bevat een standaard configuratie script dat is bedoeld om te worden gebruikt wanneer u een virtuele machine onboardt naar de Azure Automation DSC-service. De script parameters zijn afgestemd op de Configureer bare eigenschappen van [lokale Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig). Zie voor script parameters [standaard configuratie script](dsc-template.md#default-configuration-script) in [desired state configuration extension met Azure Resource Manager sjablonen](dsc-template.md). Voor het volledige script raadpleegt u de [Azure Quick Start-sjabloon in github](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Informatie voor registratie met de service voor Azure Automation status configuratie (DSC)

Wanneer u de DSC-extensie gebruikt om een knoop punt te registreren bij de status configuratie service, moeten er drie waarden worden gegeven.

- RegistrationUrl: het HTTPS-adres van het Azure Automation-account
- RegistrationKey: een gedeeld geheim dat wordt gebruikt voor het registreren van knoop punten bij de service
- NodeConfigurationName: de naam van de knooppunt configuratie (MOF) die moet worden opgehaald van de service om de serverrol te configureren

Deze informatie kan worden weer gegeven in de Azure Portal of u kunt Power shell gebruiken.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

Zorg ervoor dat de knooppunt configuratie bestaat in de Azure-status configuratie voor de naam van de knooppunt configuratie.  Als dat niet het geval is, wordt een fout geretourneerd door de implementatie van de extensie.  Zorg er ook voor dat u de naam van de *knooppunt configuratie* gebruikt en niet de configuratie.
Een configuratie wordt gedefinieerd in een script dat wordt gebruikt [voor het compileren van de knooppunt configuratie (MOF-bestand)](../../automation/automation-dsc-compile.md).
De naam is altijd de configuratie, gevolgd door een punt `.` en een `localhost` of een specifieke computer naam.

## <a name="dsc-extension-in-resource-manager-templates"></a>DSC-extensie in Resource Manager-sjablonen

In de meeste scenario's zijn Resource Manager-implementatie sjablonen de verwachte manier om met de DSC-extensie te werken. Voor meer informatie en voor beelden van het toevoegen van de DSC-uitbrei ding in de Resource Manager-implementatie sjablonen, Zie [desired state Configuration extension with Azure Resource Manager Templates](dsc-template.md)(Engelstalig).

## <a name="dsc-extension-powershell-cmdlets"></a>Power shell-cmdlets voor DSC-extensies

De Power shell-cmdlets die worden gebruikt voor het beheren van de DSC-uitbrei ding, worden het beste gebruikt in interactieve probleem oplossing en scenario's voor het verzamelen van gegevens. U kunt de-cmdlets gebruiken om DSC-extensie-implementaties te verpakken, te publiceren en te controleren. Cmdlets voor de DSC-extensie zijn nog niet bijgewerkt om met het [standaard configuratie script](#default-configuration-script)te werken.

De cmdlet **Publish-AzVMDscConfiguration** gaat in een configuratie bestand, scant het voor afhankelijke DSC-resources en maakt vervolgens een zip-bestand. Het zip-bestand bevat de configuratie-en DSC-resources die nodig zijn om de configuratie te kunnen afnemen. De cmdlet kan het pakket ook lokaal maken met behulp van de para meter *-OutputArchivePath* . Anders publiceert de cmdlet het zip-bestand naar de Blob-opslag en wordt het beveiligd met een SAS-token.

Het. ps1-configuratie script dat door de cmdlet wordt gemaakt, bevindt zich in het zip-bestand in de hoofdmap van de archiefmap. De map module wordt geplaatst in de archiefmap in resources.

De cmdlet **set-AzVMDscExtension** injecteert de instellingen die de Power shell DSC-extensie vereist in een VM-configuratie object.

De cmdlet **Get-AzVMDscExtension** haalt de DSC-extensie status van een specifieke virtuele machine op.

Met de cmdlet **Get-AzVMDscExtensionStatus** wordt de status opgehaald van de DSC-configuratie die door de DSC-extensie-handler wordt aangenomen. Deze actie kan worden uitgevoerd op één virtuele machine of op een groep Vm's.

De cmdlet **Remove-AzVMDscExtension** verwijdert de extensie-handler van een specifieke virtuele machine. Met deze cmdlet wordt de configuratie niet verwijderd, wordt WMF verwijderd of worden de toegepaste instellingen op de virtuele machine *niet* gewijzigd. Alleen de extensie-handler wordt verwijderd.

Belang rijke informatie over cmdlets voor Resource Manager DSC-extensies:

- Azure Resource Manager-cmdlets zijn synchroon.
- De para meters *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version* en *Location* zijn allemaal vereist.
- *ArchiveResourceGroupName* is een optionele para meter. U kunt deze para meter opgeven wanneer uw opslag account hoort bij een andere resource groep dan het item waar de virtuele machine is gemaakt.
- Gebruik de schakel optie auto **Update** om de extensie-handler automatisch bij te werken naar de meest recente versie wanneer deze beschikbaar is. Deze para meter kan ertoe leiden dat de virtuele machine opnieuw wordt opgestart wanneer een nieuwe versie van WMF wordt uitgebracht.

### <a name="get-started-with-cmdlets"></a>Aan de slag met cmdlets

De Azure DSC-extensie kan gebruikmaken van DSC-configuratie documenten om Azure-Vm's direct te configureren tijdens de implementatie. Met deze stap wordt het knoop punt niet geregistreerd voor Automation. Het knoop punt wordt *niet* centraal beheerd.

In het volgende voor beeld ziet u een eenvoudig voor beeld van een configuratie. Sla de configuratie lokaal op als iisInstall.ps1.

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

De volgende opdrachten plaatsen het iisInstall.ps1 script op de opgegeven virtuele machine. De opdrachten voeren ook de configuratie uit en melden vervolgens weer aan de status.

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Implementatie van Azure CLI

De Azure CLI kan worden gebruikt om de DSC-extensie te implementeren op een bestaande virtuele machine.

Voor een virtuele machine met Windows:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

Voor een virtuele machine met Linux:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Azure Portal functionaliteit

DSC instellen in de portal:

1. Ga naar een virtuele machine.
2. Selecteer onder **Instellingen** de optie **Extensies**.
3. Selecteer op de nieuwe pagina die wordt gemaakt, de optie **+ toevoegen** en selecteer vervolgens **Power shell desired state Configuration**.
4. Klik op **maken** onder aan de pagina met informatie over de extensie.

De portal verzamelt de volgende invoer:

- **Configuratie modules of script**: dit veld is verplicht (het formulier is niet bijgewerkt voor het [standaard configuratie script](#default-configuration-script)). Configuratie modules en-scripts vereisen een. ps1-bestand met een configuratie script of een zip-bestand met een. ps1-configuratie script in de hoofdmap. Als u een zip-bestand gebruikt, moeten alle afhankelijke resources zijn opgenomen in module mappen in de. zip. U kunt het zip-bestand maken met behulp van de cmdlet **Publish-AzureVMDscConfiguration-OutputArchivePath** die is opgenomen in de Azure PowerShell SDK. Het zip-bestand wordt geüpload naar de Blob-opslag van uw gebruiker en beveiligd met een SAS-token.

- **Module-gekwalificeerde naam van de configuratie**: u kunt meerdere configuratie functies in een. ps1-bestand toevoegen. Voer de naam in van de configuratie. ps1-script gevolgd door \\ en de naam van de configuratie functie. Als uw PS1-script bijvoorbeeld de naam configuration.ps1 heeft en de configuratie **IisInstall** is, voert u **configuration.ps1 \iisinstall** in.

- **Configuratie argumenten**: als de configuratie functie argumenten accepteert, voert u deze hier in de notatie **argumentName1 = waarde1, argumentName2 = waarde2**. Deze indeling is een andere indeling waarin configuratie argumenten worden geaccepteerd in Power shell-cmdlets of Resource Manager-sjablonen.

- **PSD1-bestand van de configuratie gegevens**: als voor uw configuratie een bestand met configuratie gegevens is vereist in `.psd1` , gebruikt u dit veld om het gegevens bestand te selecteren en te uploaden naar de Blob-opslag van uw gebruiker. Het bestand met configuratie gegevens wordt beveiligd door een SAS-token in Blob Storage.

- **WMF-versie**: Hiermee geeft u de versie van Windows Management Framework (WMF) op die op uw virtuele machine moet worden geïnstalleerd. Als u deze eigenschap instelt op laatst, wordt de meest recente versie van WMF geïnstalleerd. Momenteel zijn de enige mogelijke waarden voor deze eigenschap 4,0, 5,0, 5,1 en meest recent. Deze mogelijke waarden zijn onderhevig aan updates. De standaard waarde is **meest recent**.

- **Gegevens verzameling**: Hiermee wordt bepaald of de extensie telemetrie verzamelt. Zie [Azure DSC extension-gegevens verzameling](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/)voor meer informatie.

- **Versie**: Hiermee geeft u de versie op van de DSC-extensie die moet worden geïnstalleerd. Zie de versie geschiedenis van de [DSC-extensie](/powershell/scripting/dsc/getting-started/azuredscexthistory)voor meer informatie over versies.

- **Secundaire versie van automatische upgrade**: dit veld is gekoppeld aan de schakel optie voor automatisch **bijwerken** in de cmdlets en maakt het mogelijk dat de uitbrei ding tijdens de installatie automatisch wordt bijgewerkt naar de meest recente versie. Met Ja wordt de extensie-handler geïnstrueerd de meest recente beschik bare versie te gebruiken. Als u **Nee** **klikt** , wordt de **versie** die is opgegeven, geforceerd geïnstalleerd. Het selecteren van **Ja** of **Nee** is gelijk aan het selecteren van **Nee**.

## <a name="logs"></a>Logboeken

De logboeken voor de uitbrei ding worden opgeslagen op de volgende locatie: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Volgende stappen

- Voor meer informatie over Power shell DSC gaat u naar het [Power shell-documentatie centrum](/powershell/scripting/dsc/overview/overview).
- Bekijk de [Resource Manager-sjabloon voor de DSC-extensie](dsc-template.md).
- Voor meer functionaliteit die u kunt beheren met Power shell DSC, en voor meer DSC-resources, gaat u naar de [Power shell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Zie voor meer informatie over het door geven van gevoelige para meters in configuraties [veilig beheer referenties met de DSC-extensie-handler](dsc-credentials.md).
