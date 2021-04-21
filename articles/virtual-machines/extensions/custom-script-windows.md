---
title: Aangepaste scriptextensie van Azure voor Windows
description: Configuratietaken voor Windows-VM's automatiseren met behulp van de aangepaste scriptextensie
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: windows
ms.date: 08/31/2020
ms.openlocfilehash: 6341e3abbf591d0e6e0395e17ccf15ec73a3ac43
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835448"
---
# <a name="custom-script-extension-for-windows"></a>Aangepaste scriptextensie voor Windows

De aangepaste scriptextensie downloadt en voert scripts uit op virtuele Azure-machines. Deze extensie is handig voor configuratie na de implementatie, software-installatie of andere configuratie- of beheertaken. Scripts kunnen worden gedownload uit Azure Storage of GitHub, of worden geleverd in Azure Portal tijdens de uitvoering van extensies. De aangepaste scriptextensie kan worden geïntegreerd met Azure Resource Manager-sjablonen en kan worden uitgevoerd met behulp van de Azure CLI, PowerShell, Azure Portal of de Azure Virtual Machine-REST API.

Dit document bevat informatie over het gebruik van de aangepaste scriptextensie met behulp van de Azure PowerShell-module, Azure Resource Manager sjablonen en de stappen voor probleemoplossing op Windows-systemen.

## <a name="prerequisites"></a>Vereisten

> [!NOTE]  
> Gebruik aangepaste scriptextensie niet om Update-AzVM uit te voeren met dezelfde VM als de parameter, omdat deze op zichzelf wacht.  

### <a name="operating-system"></a>Besturingssysteem

De aangepaste scriptextensie voor Windows wordt uitgevoerd op de extensie OSs die wordt ondersteund door extensies;

### <a name="windows"></a>Windows

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows 10
* Windows Server 2016
* Windows Server 2016 Core
* Windows Server 2019
* Windows Server 2019 Core

### <a name="script-location"></a>Scriptlocatie

U kunt de extensie configureren om uw Azure Blob Storage-referenties te gebruiken voor toegang tot Azure Blob Storage. De scriptlocatie kan overal zijn, zolang de VM naar dat eindpunt kan worden gerouteerd, zoals GitHub of een interne bestandsserver.

### <a name="internet-connectivity"></a>Internetverbinding

Als u een script extern moet downloaden, zoals vanuit GitHub of Azure Storage, moeten er extra firewall- en netwerkbeveiligingsgroeppoorten worden geopend. Als uw script zich bijvoorbeeld in een Azure Storage, kunt u toegang toestaan met behulp van Azure NSG-servicetags voor [opslag.](../../virtual-network/network-security-groups-overview.md#service-tags)

Houd er rekening mee dat de CustomScript-extensie geen enkele manier heeft om certificaatvalidatie te omzeilen. Dus als u downloadt vanaf een beveiligde locatie met bijvoorbeeld als u een zelf-ondertekend certificaat hebt, kunnen er fouten optreden zoals 'Het externe certificaat is ongeldig volgens *de validatieprocedure'.* Controleer of het certificaat correct is geïnstalleerd in het Vertrouwde basiscertificeringsinstanties *op* de virtuele machine.

Als uw script zich op een lokale server bevinden, moet u mogelijk nog extra firewall- en netwerkbeveiligingsgroeppoorten openen.

### <a name="tips-and-tricks"></a>Tips en trucs

* Het hoogste foutpercentage voor deze extensie is vanwege syntaxisfouten in het script. Test de scriptuit voeren zonder fouten en plaats ook extra logboekregistratie in het script om gemakkelijker te vinden waar het is mislukt.
* Schrijf scripts die idempotent zijn. Dit zorgt ervoor dat als ze per ongeluk opnieuw worden uitgevoerd, dit geen systeemwijzigingen veroorzaakt.
* Zorg ervoor dat de scripts geen gebruikersinvoer nodig hebben wanneer ze worden uitgevoerd.
* De uitvoering van het script mag 90 minuten duren. Als het langer duurt, mislukt de inrichting van de extensie.
* Neem opnieuw opstarten niet in het script op. Deze actie veroorzaakt problemen met andere extensies die worden geïnstalleerd. Na het opnieuw opstarten wordt de extensie niet voortgezet.
* Als u een script hebt dat een herstart veroorzaakt, vervolgens toepassingen installeert en scripts uit te voeren, kunt u het opnieuw opstarten plannen met behulp van een geplande Windows-taak of hulpprogramma's zoals DSC, Chef of Puppet-extensies gebruiken.
* Het wordt afgeraden om een script uit te voeren dat een stop- of update van de VM-agent veroorzaakt. Hierdoor kan de extensie de status Overgang hebben, wat kan leiden tot een time-out.
* De extensie voert een script slechts eenmaal uit. Wilt u dat een script bij iedere start wordt uitgevoerd, dan moet u de extensie gebruiken om een geplande Windows-taak te maken.
* Als u wilt plannen wanneer een script wordt uitgevoerd, dan moet u de extensie gebruiken om een geplande Windows-taak te maken.
* Wanneer het script wordt uitgevoerd, ziet u alleen de extensiestatus 'overgang maken' van de Azure-portal of CLI. Als u meer statusupdates van een actief script wilt, dan moet u uw eigen oplossing maken.
* Aangepaste scriptextensie biedt geen systeemeigen ondersteuning voor proxyservers, maar u kunt een hulpprogramma voor bestandsoverdracht gebruiken dat proxyservers in uw script ondersteunt, zoals *Invoke-WebRequest*
* Houd rekening met niet-standaard maplocaties waar uw scripts of opdrachten eventueel gebruik van maken. Pak deze situatie logisch aan.
* De aangepaste scriptextensie wordt uitgevoerd onder het localsystem-account
* Als u van plan bent om de *eigenschappen storageAccountName* en *storageAccountKey te* gebruiken, moeten deze eigenschappen worden opgeslagen in *protectedSettings.*

## <a name="extension-schema"></a>Extensieschema

De configuratie van de aangepaste scriptextensie specificeert zaken als scriptlocatie en de uit te voeren opdracht. U kunt deze configuratie opslaan in configuratiebestanden, deze opgeven op de opdrachtregel of opgeven in een Azure Resource Manager sjabloon.

U kunt gevoelige gegevens opslaan in een beveiligde configuratie, die is versleuteld en alleen wordt ontsleuteld in de virtuele machine. De beveiligde configuratie is handig wanneer de uitvoeringsopdracht geheimen bevat, zoals een wachtwoord of een SAS-bestandsverwijzing (Shared Access Signature), die moeten worden beveiligd.

Deze items moeten worden behandeld als gevoelige gegevens en worden opgegeven in de configuratie van de instelling die is beveiligd met extensies. Instellingsgegevens met beveiligde Azure VM-extensie worden versleuteld en alleen ontsleuteld op de virtuele doelmachine.

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
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {},
            "fileUris": [
                "script location"
            ]
        }
    }
}
```

> [!NOTE]
> De eigenschap managedIdentity **mag niet worden** gebruikt in combinatie met de eigenschappen storageAccountName of storageAccountKey

> [!NOTE]
> Er kan slechts één versie van een extensie op een bepaald moment op een VM worden geïnstalleerd. Het opgeven van een aangepast script tweemaal in dezelfde Resource Manager-sjabloon voor dezelfde VM mislukt.

> [!NOTE]
> We kunnen dit schema gebruiken in de VirtualMachine-resource of als een zelfstandige resource. De naam van de resource moet de indeling 'virtualMachineName/extensionName' hebben, als deze extensie wordt gebruikt als een zelfstandige resource in de ARM-sjabloon.

### <a name="property-values"></a>Eigenschapswaarden

| Name | Waarde/voorbeeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | tekenreeks |
| type | CustomScriptExtension | tekenreeks |
| typeHandlerVersion | 1,10 | int |
| fileUris (bijvoorbeeld) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | matrix |
| tijdstempel (bijvoorbeeld) | 123456789 | 32-bits geheel getal |
| commandToExecute (bijvoorbeeld) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | tekenreeks |
| storageAccountName (bijvoorbeeld) | examplestorageacct | tekenreeks |
| storageAccountKey (bijvoorbeeld) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQffpQj8hg== | tekenreeks |
| managedIdentity (bijvoorbeeld) | { } of { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } of { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json-object |

>[!NOTE]
>Deze eigenschapsnamen zijn casegevoelig. Gebruik de namen zoals hier wordt weergegeven om implementatieproblemen te voorkomen.

#### <a name="property-value-details"></a>Details van eigenschapswaarde

* `commandToExecute`: (**vereist ,** tekenreeks) het script voor het invoerpunt dat moet worden uitgevoerd. Gebruik dit veld in plaats daarvan als uw opdracht geheimen zoals wachtwoorden bevat of als uw fileUris gevoelig zijn.
* `fileUris`: (optioneel, tekenreeksmatrice) de URL's voor bestanden die moeten worden gedownload. Als URL's gevoelig zijn (zoals URL's met sleutels), moet dit veld worden opgegeven in protectedSettings
* `timestamp` (optioneel, 32-bits geheel getal) gebruik dit veld alleen om een nieuwe run van het script te activeren door de waarde van dit veld te wijzigen.  Een geheel getal is acceptabel; deze mag alleen anders zijn dan de vorige waarde.
* `storageAccountName`: (optioneel, tekenreeks) de naam van het opslagaccount. Als u opslagreferenties opgeeft, moeten `fileUris` alle URL's voor Azure Blobs zijn.
* `storageAccountKey`: (optioneel, tekenreeks) de toegangssleutel van het opslagaccount
* `managedIdentity`: (optioneel, json-object) de [beheerde identiteit voor](../../active-directory/managed-identities-azure-resources/overview.md) het downloaden van bestanden
  * `clientId`: (optioneel, tekenreeks) de client-id van de beheerde identiteit
  * `objectId`: (optioneel, tekenreeks) de object-id van de beheerde identiteit

De volgende waarden kunnen worden ingesteld in openbare of beveiligde instellingen. De extensie weigert elke configuratie waarbij de onderstaande waarden zijn ingesteld in zowel openbare als beveiligde instellingen.

* `commandToExecute`
* `fileUris`

Het gebruik van openbare instellingen kan handig zijn voor het debuggen, maar het wordt aanbevolen om beveiligde instellingen te gebruiken.

Openbare instellingen worden in duidelijke tekst verzonden naar de VM waarop het script wordt uitgevoerd.  Beveiligde instellingen worden versleuteld met behulp van een sleutel die alleen bekend is bij Azure en de VM. De instellingen worden opgeslagen op de VM terwijl ze zijn verzonden, dat wil zeggen, als de instellingen zijn versleuteld, worden ze versleuteld opgeslagen op de virtueleM. Het certificaat dat wordt gebruikt om de versleutelde waarden te ontsleutelen, wordt opgeslagen op de VM en gebruikt voor het ontsleutelen van instellingen (indien nodig) tijdens runtime.

####  <a name="property-managedidentity"></a>Eigenschap: managedIdentity
> [!NOTE]
> Deze eigenschap **moet** alleen worden opgegeven in beveiligde instellingen.

CustomScript (versie 1.10 en [](../../active-directory/managed-identities-azure-resources/overview.md) hoger) ondersteunt beheerde identiteiten voor het downloaden van bestanden van URL's die zijn opgegeven in de instelling 'fileUris'. Het biedt CustomScript toegang tot Azure Storage persoonlijke blobs of containers zonder dat de gebruiker geheimen zoals SAS-tokens of opslagaccountsleutels moet doorgeven.

Als u deze functie wilt [](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) gebruiken, moet de gebruiker een door het systeem toegewezen of door de gebruiker toegewezen identiteit toevoegen aan de VM of VMSS waar CustomScript naar verwachting wordt uitgevoerd, en de beheerde identiteit toegang verlenen tot de [Azure Storage-container](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) [of blob](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access).

Als u de door het systeem toegewezen identiteit wilt gebruiken op de doel-VM/VMSS, stelt u het veld 'managedidentity' in op een leeg json-object. 

> Voorbeeld:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Als u de door de gebruiker toegewezen identiteit wilt gebruiken op de doel-VM/VMSS, configureert u het veld 'managedidentity' met de client-id of de object-id van de beheerde identiteit.

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
> De eigenschap managedIdentity **mag niet worden** gebruikt in combinatie met de eigenschappen storageAccountName of storageAccountKey

## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager sjablonen. Het JSON-schema, dat in de vorige sectie is beschreven, kan worden gebruikt in een Azure Resource Manager om de aangepaste scriptextensie uit te voeren tijdens de implementatie. De volgende voorbeelden laten zien hoe u de aangepaste scriptextensie gebruikt:

* [Zelfstudie: Extensies voor virtuele machines implementeren met Azure Resource Manager-sjablonen](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Toepassing met twee lagen implementeren in Windows en Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell-implementatie

De `Set-AzVMCustomScriptExtension` opdracht kan worden gebruikt om de aangepaste scriptextensie toe te voegen aan een bestaande virtuele machine. Zie [Set-AzVMCustomScriptExtension voor meer informatie.](/powershell/module/az.compute/set-azvmcustomscriptextension)

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

In dit voorbeeld hebt u drie scripts die worden gebruikt om uw server te bouwen. Met **commandToExecute wordt** het eerste script aangeroepen. Vervolgens hebt u opties voor het aanroepen van de andere scripts. U kunt bijvoorbeeld een hoofdscript hebben dat de uitvoering bepaalt, met de juiste foutafhandeling, logboekregistratie en statusbeheer. De scripts worden gedownload naar de lokale computer om ze uit te voeren. In roept u bijvoorbeeld aan door toe te voegen aan het script en herhaalt u dit proces voor de `1_Add_Tools.ps1` andere scripts die u `2_Add_Features.ps1`  `.\2_Add_Features.ps1` definieert in `$settings` .

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
    -Settings $settings `
    -ProtectedSettings $protectedSettings;
```

### <a name="running-scripts-from-a-local-share"></a>Scripts uitvoeren vanuit een lokale share

In dit voorbeeld wilt u mogelijk een lokale SMB-server gebruiken voor uw scriptlocatie. Als u dit doet, hoeft u geen andere instellingen op te geven, behalve **commandToExecute.**

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

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Aangepaste scripts meer dan één keer uitvoeren met CLI

Als u de aangepaste scriptextensie meer dan één keer wilt uitvoeren, kunt u deze actie alleen uitvoeren onder de volgende voorwaarden:

* De parameter **extension name** is hetzelfde als de vorige implementatie van de extensie.
* Werk de configuratie bij, anders wordt de opdracht niet opnieuw uitgevoerd. U kunt een dynamische eigenschap toevoegen aan de opdracht, zoals een tijdstempel.

U kunt ook de eigenschap [ForceUpdateTag instellen](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) op **true.**

### <a name="using-invoke-webrequest"></a>Met Invoke-WebRequest

Als u [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) in uw script gebruikt, moet u de parameter opgeven, anders ontvangt u de volgende fout bij het controleren van `-UseBasicParsing` de gedetailleerde status:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Virtuele-machineschaalsets

Zie [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) voor het implementeren van de aangepaste scriptextensie op een schaalset

## <a name="classic-vms"></a>Klassieke VM's

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Als u de aangepaste scriptextensie wilt implementeren op klassieke VM's, kunt u de cmdlets Azure Portal of classic Azure PowerShell gebruiken.

### <a name="azure-portal"></a>Azure Portal

Navigeer naar uw klassieke VM-resource. Selecteer **Extensies** onder **Instellingen.**

Klik **op + Toevoegen** en kies aangepaste **scriptextensie** in de lijst met resources.

Selecteer op **de pagina Extensie** installeren het lokale PowerShell-bestand, vul eventuele argumenten in en klik op **OK.**

### <a name="powershell"></a>PowerShell

Gebruik de cmdlet [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure.service/set-azurevmcustomscriptextension) om de aangepaste scriptextensie toe te voegen aan een bestaande virtuele machine.

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

Gegevens over de status van extensie-implementaties kunnen worden opgehaald uit de Azure Portal en met behulp van de Azure PowerShell module. Voer de volgende opdracht uit om de implementatietoestand van extensies voor een bepaalde VM te bekijken:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Extensie-uitvoer wordt vastgelegd in bestanden in de volgende map op de virtuele doelmachine.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

De opgegeven bestanden worden gedownload naar de volgende map op de virtuele doelmachine.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

waarbij `<n>` een decimaal geheel getal is, dat kan veranderen tussen uitvoeringen van de extensie.  De `1.*` waarde komt overeen met de werkelijke, huidige waarde van de `typeHandlerVersion` extensie.  De werkelijke map kan bijvoorbeeld `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2` zijn.  

Bij het uitvoeren van `commandToExecute` de opdracht stelt de extensie deze map (bijvoorbeeld ) in als de huidige `...\Downloads\2` werkmap. Met dit proces kunnen relatieve paden worden gebruikt om de bestanden te vinden die via de eigenschap zijn `fileURIs` gedownload. Zie de onderstaande tabel voor voorbeelden.

Omdat het absolute downloadpad in de tijd kan variëren, is het beter om waar mogelijk te kiezen voor relatieve script-/bestandspaden in `commandToExecute` de tekenreeks. Bijvoorbeeld:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Padgegevens na het eerste URI-segment worden bewaard voor bestanden die zijn gedownload via de `fileUris` eigenschappenlijst.  Zoals u in de onderstaande tabel kunt zien, worden gedownloade bestanden in downloadsubmaps in kaart gebracht om de structuur van de waarden weer te `fileUris` geven.  

#### <a name="examples-of-downloaded-files"></a>Voorbeelden van gedownloade bestanden

| URI in fileUris | Relatief gedownloade locatie | Absolute gedownloade locatie <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> De absolute mappaden veranderen gedurende de levensduur van de VM, maar niet binnen één uitvoering van de CustomScript-extensie.

### <a name="support"></a>Ondersteuning

Als u op enig moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op de [MSDN Azure- en Stack Overflow forums.](https://azure.microsoft.com/support/forums/) U kunt ook een ondersteuning voor Azure indienen. Ga naar de [ondersteuning voor Azure site](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning krijgen. Lees de veelgestelde vragen Microsoft Azure ondersteuning voor [meer Ondersteuning voor Azure over het gebruik van een Microsoft Azure.](https://azure.microsoft.com/support/faq/)
