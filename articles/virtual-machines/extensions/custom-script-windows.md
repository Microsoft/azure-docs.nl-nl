---
title: Aangepaste script extensie voor Azure voor Windows
description: Windows VM-configuratie taken automatiseren met behulp van de aangepaste script extensie
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/31/2020
ms.author: robreed
ms.openlocfilehash: 8d11ff6eaab8ed6a13c3c2aa1b712cc57e7825ea
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960968"
---
# <a name="custom-script-extension-for-windows"></a>Aangepaste scriptextensie voor Windows

De aangepaste script extensie downloadt en voert scripts uit op virtuele machines van Azure. Deze uitbrei ding is handig voor configuratie na de implementatie, software-installatie of andere configuratie-of beheer taken. Scripts kunnen worden gedownload uit Azure Storage of GitHub, of worden geleverd in Azure Portal tijdens de uitvoering van extensies. De aangepaste script extensie kan worden geïntegreerd met Azure Resource Manager sjablonen en kan worden uitgevoerd met behulp van de Azure CLI, Power shell, Azure Portal of de Azure virtual machine REST API.

In dit document wordt beschreven hoe u de aangepaste script extensie gebruikt met behulp van de module Azure PowerShell, Azure Resource Manager sjablonen en Details voor probleem oplossing op Windows-systemen.

## <a name="prerequisites"></a>Vereisten

> [!NOTE]  
> Gebruik de aangepaste script extensie niet om Update-AzVM uit te voeren met dezelfde VM als de para meter, omdat deze wordt gewacht op zichzelf.  

### <a name="operating-system"></a>Besturingssysteem

De aangepaste script extensie voor Windows wordt uitgevoerd op de extensie OSs die wordt ondersteund.
### <a name="windows"></a>Windows

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows 10
* Windows Server 2016
* Windows Server 2016 core
* Windows Server 2019
* Windows Server 2019 core

### <a name="script-location"></a>Locatie van script

U kunt de extensie configureren voor het gebruik van uw Azure Blob Storage-referenties voor toegang tot Azure Blob-opslag. De locatie van het script kan overal bestaan, zolang de virtuele machine naar dat eind punt kan worden geleid, zoals GitHub of een interne bestands server.

### <a name="internet-connectivity"></a>Internet verbinding

Als u een script extern moet downloaden, zoals van GitHub of Azure Storage, moeten er extra firewall-en netwerk beveiligings groep poorten worden geopend. Als uw script zich bijvoorbeeld in Azure Storage bevindt, kunt u toegang toestaan met de Azure NSG-service tags voor [opslag](../../virtual-network/network-security-groups-overview.md#service-tags).

Als uw script zich op een lokale server bevindt, moet u mogelijk nog steeds extra firewall-en netwerk beveiligings groep poorten openen.

### <a name="tips-and-tricks"></a>Tips en trucs

* Het hoogste fout aantal voor deze uitbrei ding is het gevolg van syntaxis fouten in het script, het testen van het script wordt uitgevoerd zonder fouten, en het is ook mogelijk extra logboek registratie in het script uit te voeren, zodat u gemakkelijker kunt vinden waar het is mislukt.
* Schrijf scripts die idempotent zijn. Dit zorgt ervoor dat als ze per ongeluk worden uitgevoerd, er geen systeem wijzigingen optreden.
* Zorg ervoor dat de scripts geen gebruikersinvoer nodig hebben wanneer ze worden uitgevoerd.
* De uitvoering van het script mag 90 minuten duren. Als het langer duurt, mislukt de inrichting van de extensie.
* Neem opnieuw opstarten niet in het script op. Deze actie veroorzaakt problemen met andere extensies die worden geïnstalleerd. Na het opnieuw opstarten wordt de extensie niet voortgezet.
* Als u een script hebt dat ertoe leidt dat de computer opnieuw wordt opgestart en vervolgens toepassingen installeert en scripts uitvoert, kunt u het opnieuw opstarten plannen met een geplande Windows-taak of gebruikmaken van hulpprogram ma's zoals DSC, chef of Puppet-extensies.
* Het is niet raadzaam om een script uit te voeren dat de VM-agent stopt te stoppen of bij te werken. Dit kan de uitbrei ding in een overgangs status, tot een time-out leiden.
* De extensie voert een script slechts eenmaal uit. Wilt u dat een script bij iedere start wordt uitgevoerd, dan moet u de extensie gebruiken om een geplande Windows-taak te maken.
* Als u wilt plannen wanneer een script wordt uitgevoerd, dan moet u de extensie gebruiken om een geplande Windows-taak te maken.
* Wanneer het script wordt uitgevoerd, ziet u alleen de extensiestatus 'overgang maken' van de Azure-portal of CLI. Als u meer statusupdates van een actief script wilt, dan moet u uw eigen oplossing maken.
* Aangepaste script extensie biedt geen systeem eigen ondersteuning voor proxy servers, maar u kunt wel een hulp programma voor bestands overdracht gebruiken dat proxy servers in uw script ondersteunt, zoals *krul*
* Houd rekening met niet-standaard maplocaties waar uw scripts of opdrachten eventueel gebruik van maken. Pak deze situatie logisch aan.
* Aangepaste script extensie wordt uitgevoerd onder het LocalSystem-account
* Als u van plan bent de eigenschappen *storageAccountName* en *storageAccountKey* te gebruiken, moet u deze eigenschappen co in *protectedSettings*.

## <a name="extension-schema"></a>Extensieschema

De configuratie van de aangepaste script extensie bevat dingen als de script locatie en de opdracht die moet worden uitgevoerd. U kunt deze configuratie opslaan in configuratie bestanden, opgeven op de opdracht regel of in een Azure Resource Manager sjabloon opgeven.

U kunt gevoelige gegevens in een beveiligde configuratie opslaan, die zijn versleuteld en alleen worden ontsleuteld in de virtuele machine. De beveiligde configuratie is handig wanneer de uitvoering-opdracht geheimen bevat zoals een wacht woord.

Deze items moeten worden behandeld als gevoelige gegevens en worden opgegeven in de configuratie van de instellingen voor beveiligde extensies. De beveiligde instellings gegevens voor de Azure VM-extensie zijn versleuteld en worden alleen ontsleuteld op de virtuele doel machine.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.10",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> de eigenschap managedIdentity **mag niet** worden gebruikt in combi natie met storageAccountName-of storageAccountKey-eigenschappen

> [!NOTE]
> Er kan slechts één versie van een uitbrei ding op een virtuele machine op een bepaald moment worden geïnstalleerd. Als u een aangepast script twee keer opgeeft in dezelfde resource manager-sjabloon voor dezelfde virtuele machine, mislukt de installatie.

> [!NOTE]
> We kunnen dit schema gebruiken in de VirtualMachine-resource of als zelfstandige resource. De naam van de resource moet de volgende indeling hebben: "virtualMachineName/extensie naam" als deze uitbrei ding wordt gebruikt als een zelfstandige resource in de ARM-sjabloon.

### <a name="property-values"></a>Eigenschaps waarden

| Name | Waarde/voor beeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| publisher | Microsoft.Compute | tekenreeks |
| type | CustomScriptExtension | tekenreeks |
| typeHandlerVersion | 1,10 | int |
| fileUris (bijvoorbeeld) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | matrix |
| tijds tempel (bijvoorbeeld) | 123456789 | 32-bits geheel getal |
| commandToExecute (bijvoorbeeld) | Power shell-ExecutionPolicy unrestricted-file configure-music-app.ps1 | tekenreeks |
| storageAccountName (bijvoorbeeld) | examplestorageacct | tekenreeks |
| storageAccountKey (bijvoorbeeld) | TmJK/1N3AbAZ3q/+ hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg = = | tekenreeks |
| managedIdentity (bijvoorbeeld) | {} of {"clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232"} of {"objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b"} | JSON-object |

>[!NOTE]
>Deze eigenschaps namen zijn hoofdletter gevoelig. Als u implementatie problemen wilt voor komen, gebruikt u de namen zoals hier wordt weer gegeven.

#### <a name="property-value-details"></a>Details van eigenschaps waarde

* `commandToExecute`: (**vereist**, teken reeks) het ingangs punt script dat moet worden uitgevoerd. Gebruik dit veld als uw opdracht geheimen bevat zoals wacht woorden of als uw fileUris gevoelig zijn.
* `fileUris`: (optioneel, teken reeks matrix) de Url's voor bestanden die moeten worden gedownload.
* `timestamp` (optioneel, 32-bits geheel getal) gebruik dit veld alleen om een opnieuw uitvoeren van het script te activeren door de waarde van dit veld te wijzigen.  Een gehele waarde is acceptabel; de waarde mag alleen gelijk zijn aan die van de vorige.
* `storageAccountName`: (optioneel, String) de naam van het opslag account. Als u opslag referenties opgeeft, `fileUris` moeten alle url's voor Azure-blobs zijn.
* `storageAccountKey`: (optioneel, String) de toegangs sleutel van het opslag account
* `managedIdentity`: (optioneel, JSON-object) de [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) voor het downloaden van bestand (en)
  * `clientId`: (optioneel, String) de client-ID van de beheerde identiteit
  * `objectId`: (optioneel, String) de object-ID van de beheerde identiteit

De volgende waarden kunnen worden ingesteld in de open bare of beveiligde instellingen. de uitbrei ding weigert de configuratie, waarbij de waarden hieronder worden ingesteld in de open bare en beveiligde instellingen.

* `commandToExecute`

Het gebruik van open bare instellingen kan handig zijn voor het opsporen van fouten, maar het wordt aanbevolen dat u beveiligde instellingen gebruikt.

Open bare instellingen worden in ongecodeerde tekst verzonden naar de virtuele machine waarop het script wordt uitgevoerd.  Beveiligde instellingen worden versleuteld met een sleutel die alleen bekend is bij Azure en de virtuele machine. De instellingen worden opgeslagen naar de virtuele machine wanneer ze zijn verzonden, dat wil zeggen, als de instellingen zijn versleuteld, zijn versleuteld opgeslagen op de VM. Het certificaat dat wordt gebruikt voor het ontsleutelen van de versleutelde waarden wordt opgeslagen op de virtuele machine en wordt gebruikt voor het ontsleutelen van instellingen (indien nodig) tijdens runtime.

####  <a name="property-managedidentity"></a>Eigenschap: managedIdentity
> [!NOTE]
> Deze eigenschap **moet** alleen worden opgegeven in beveiligde instellingen.

CustomScript (versie 1,10 en hoger) ondersteunt [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) voor het downloaden van bestanden van url's die zijn opgenomen in de instelling ' fileUris '. Hiermee kan CustomScript toegang krijgen tot Azure Storage persoonlijke blobs of containers zonder dat de gebruiker geheimen zoals SAS-tokens of opslag account-sleutels hoeft door te geven.

Als u deze functie wilt gebruiken, moet de gebruiker een door het [systeem toegewezen](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) of door de [gebruiker toegewezen](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) identiteit toevoegen aan de virtuele machine of VMSS waar CustomScript naar verwachting wordt uitgevoerd en [de beheerde identiteit toegang verlenen aan de Azure storage container of BLOB](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access).

Als u de door het systeem toegewezen identiteit op de doel-VM-VMSS wilt gebruiken, stelt u het veld managedidentity in op een leeg JSON-object. 

> Voorbeeld:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Als u de door de gebruiker toegewezen identiteit op de doel-VM-VMSS wilt gebruiken, configureert u het veld managedidentity met de client-ID of de object-ID van de beheerde identiteit.

> Voorbeelden:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> de eigenschap managedIdentity **mag niet** worden gebruikt in combi natie met storageAccountName-of storageAccountKey-eigenschappen

## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager sjablonen. Het JSON-schema, dat in de vorige sectie wordt beschreven, kan worden gebruikt in een Azure Resource Manager sjabloon voor het uitvoeren van de aangepaste script extensie tijdens de implementatie. In de volgende voor beelden ziet u hoe u de aangepaste script extensie gebruikt:

* [Zelfstudie: Extensies voor virtuele machines implementeren met Azure Resource Manager-sjablonen](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Toepassing met twee lagen implementeren in Windows en Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell-implementatie

De `Set-AzVMCustomScriptExtension` opdracht kan worden gebruikt om de aangepaste script extensie toe te voegen aan een bestaande virtuele machine. Zie [set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension)voor meer informatie.

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Aanvullende voorbeelden

### <a name="using-multiple-scripts"></a>Meerdere scripts gebruiken

In dit voor beeld hebt u drie scripts die worden gebruikt voor het bouwen van uw server. De **commandToExecute** roept het eerste script aan. vervolgens hebt u opties om te zien hoe de andere worden aangeroepen. U kunt bijvoorbeeld een hoofd script hebben dat de uitvoering beheert, met de juiste fout afhandeling, logboek registratie en status beheer. De scripts worden gedownload naar de lokale machine voor het uitvoeren van. U kunt bijvoorbeeld `1_Add_Tools.ps1` aanroepen `2_Add_Features.ps1` door toe te voegen  `.\2_Add_Features.ps1` aan het script en dit proces herhalen voor de andere scripts die u in definieert `$settings` .

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Scripts uitvoeren vanaf een lokale share

In dit voor beeld wilt u mogelijk een lokale SMB-server gebruiken voor de locatie van uw script. Als u dit doet, hoeft u geen andere instellingen op te geven, met uitzonde ring van **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Aangepaste script meermaals uitvoeren met CLI

Als u de aangepaste script extensie meer dan één keer wilt uitvoeren, kunt u deze actie alleen doen in deze omstandigheden:

* De extensie **naam** parameter is hetzelfde als de vorige implementatie van de uitbrei ding.
* Als u de configuratie anders bijwerkt, wordt de opdracht niet opnieuw uitgevoerd. U kunt een dynamische eigenschap toevoegen aan de opdracht, zoals een tijds tempel.

U kunt de eigenschap [updatetag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) ook instellen op **True**.

### <a name="using-invoke-webrequest"></a>Invoke-WebRequest gebruiken

Als u [invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) in uw script gebruikt, moet u de para meter opgeven, `-UseBasicParsing` anders wordt de volgende fout weer gegeven bij het controleren van de gedetailleerde status:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Zie [add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0) voor meer informatie over het implementeren van de aangepaste script extensie voor een schaalset.

## <a name="classic-vms"></a>Klassieke VM's

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Als u de aangepaste script extensie op klassieke Vm's wilt implementeren, kunt u de Azure Portal of de klassieke Azure PowerShell-cmdlets gebruiken.

### <a name="azure-portal"></a>Azure Portal

Navigeer naar uw klassieke VM-resource. Selecteer **extensies** onder **instellingen**.

Klik op **+ toevoegen** en kies **aangepaste script extensie** in de lijst met resources.

Selecteer op de pagina **extensie installeren** het lokale Power shell-bestand en vul eventuele argumenten in en klik op **OK**.

### <a name="powershell"></a>PowerShell

U kunt de cmdlet [set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure.service/set-azurevmcustomscriptextension) gebruiken om de aangepaste script extensie toe te voegen aan een bestaande virtuele machine.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit het Azure Portal en met behulp van de module Azure PowerShell. Voer de volgende opdracht uit om de implementatie status van extensies voor een bepaalde virtuele machine te bekijken:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Uitvoer van de extensie wordt vastgelegd in bestanden die zijn gevonden in de volgende map op de virtuele doel machine.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

De opgegeven bestanden worden gedownload naar de volgende map op de virtuele doel machine.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

waar `<n>` is een decimaal geheel getal. Dit kan veranderen tussen de uitvoeringen van de uitbrei ding.  De `1.*` waarde komt overeen met de werkelijke, huidige `typeHandlerVersion` waarde van de extensie.  De daad werkelijke map kan bijvoorbeeld zijn `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2` .  

Wanneer de opdracht wordt uitgevoerd `commandToExecute` , wordt deze map door de extensie ingesteld (bijvoorbeeld `...\Downloads\2` ) als de huidige werkmap. Dit proces zorgt ervoor dat relatieve paden kunnen worden gebruikt om de bestanden te vinden die via de eigenschap zijn gedownload `fileURIs` . Zie de onderstaande tabel voor voor beelden.

Omdat het absolute Download traject na verloop van tijd kan variëren, is het beter om te kiezen voor relatieve script-en bestands paden in de `commandToExecute` teken reeks, indien mogelijk. Bijvoorbeeld:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Padgegevens na het eerste URI-segment worden bewaard voor bestanden die worden gedownload via de `fileUris` Eigenschappen lijst.  Zoals in de onderstaande tabel wordt weer gegeven, worden gedownloade bestanden toegewezen in submappen voor downloaden om de structuur van de waarden weer te geven `fileUris` .  

#### <a name="examples-of-downloaded-files"></a>Voor beelden van gedownloade bestanden

| URI in fileUris | Relatieve gedownloade locatie | Absolute gedownloade locatie <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> de absolute mappaden worden gewijzigd gedurende de levens duur van de virtuele machine, maar niet binnen één uitvoering van de CustomScript-extensie.

### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.