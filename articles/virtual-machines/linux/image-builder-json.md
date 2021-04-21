---
title: Een Azure Image Builder maken (preview)
description: Meer informatie over het maken van een sjabloon voor gebruik met Azure Image Builder.
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: reference
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: cynthn
ms.openlocfilehash: 77460d1675b806e04c72e5f46da0ec4274d99d41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762529"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Preview: Een Azure Image Builder maken 

Azure Image Builder gebruikt een JSON-bestand om informatie door te geven aan de Image Builder service. In dit artikel gaan we de secties van het json-bestand bekijken, zodat u uw eigen bestand kunt bouwen. Zie Azure Image Builder GitHub voor voorbeelden van [volledige .json-bestanden.](https://github.com/Azure/azvmimagebuilder/tree/main/quickquickstarts)

Dit is de basissjabloonindeling:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2020-02-14", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
     },
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
                }
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Type en API-versie

De `type` is het resourcetype, dat moet `"Microsoft.VirtualMachineImages/imageTemplates"` zijn. De `apiVersion` wordt na een periode gewijzigd als de API wordt gewijzigd, maar moet als `"2020-02-14"` preview-versie worden gebruikt.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>Locatie

De locatie is de regio waar de aangepaste afbeelding wordt gemaakt. Voor de Image Builder preview worden de volgende regio's ondersteund:

- VS - oost
- VS - oost 2
- VS - west-centraal
- VS - west
- VS - west 2
- Europa - noord
- Europa -west


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
Standaard gebruikt Image Builder een 'Standard_D1_v2'-build-VM. U kunt dit bijvoorbeeld overschrijven. Als u bijvoorbeeld een afbeelding voor een GPU-VM wilt aanpassen, hebt u een GPU-VM-grootte nodig. Dit is optioneel.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Standaard wordt Image Builder grootte van de afbeelding niet gewijzigd, maar wordt de grootte van de bronafbeelding gebruikt. U kunt **alleen de** grootte van de besturingssysteemschijf vergroten (Win en Linux). Dit is optioneel en een waarde van 0 betekent dat de grootte van de bronafbeelding gelijk blijft. U kunt de grootte van de besturingssysteemschijf niet verkleinen tot kleiner dan de grootte van de bronafbeelding.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Als u geen VNET-eigenschappen opgeeft, maakt Image Builder een eigen VNET, openbaar IP-adres en NSG. Het openbare IP-adres wordt gebruikt om de service te laten communiceren met de build-VM. Als u echter geen openbaar IP-adres wilt of als u wilt dat Image Builder toegang hebben tot uw bestaande VNET-resources, zoals configuratieservers (DSC, Chef, Puppet, Ansible), bestands shares enzovoort, kunt u een VNET opgeven. Bekijk de netwerkdocumentatie [voor meer informatie.](image-builder-networking.md)Dit is optioneel.

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>Tags

Dit zijn sleutel-waardeparen die u kunt opgeven voor de afbeelding die wordt gegenereerd.

## <a name="depends-on-optional"></a>Is afhankelijk van (optioneel)

Deze optionele sectie kan worden gebruikt om ervoor te zorgen dat afhankelijkheden worden voltooid voordat u doorgaat. 

```json
    "dependsOn": [],
```

Zie Resourceafhankelijkheden definiëren [voor meer informatie.](../../azure-resource-manager/templates/define-resource-dependency.md#dependson)

## <a name="identity"></a>Identiteit

Vereist: als Image Builder machtigingen hebt voor het lezen/schrijven van afbeeldingen, moet u in scripts van Azure Storage een Azure User-Assigned-identiteit maken die machtigingen heeft voor de afzonderlijke resources. Raadpleeg de documentatie Image Builder meer informatie over hoe Image Builder machtigingen werken en relevante [stappen.](image-builder-user-assigned-identity.md)


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


Image Builder ondersteuning voor een User-Assigned identiteit:
* Ondersteunt slechts één identiteit
* Biedt geen ondersteuning voor aangepaste domeinnamen

Zie Wat zijn beheerde [identiteiten voor Azure-resources? voor meer informatie.](../../active-directory/managed-identities-azure-resources/overview.md)
Zie Beheerde identiteiten configureren voor Azure-resources op een [Azure-VM](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity)met behulp van Azure CLI voor meer informatie over het implementeren van deze functie.

## <a name="properties-source"></a>Eigenschappen: bron

De `source` sectie bevat informatie over de bronafbeelding die wordt gebruikt door Image Builder. Image Builder biedt momenteel alleen standaard ondersteuning voor het maken van Hyper-V-generatie (Gen1) 1-afbeeldingen naar de Azure Shared Image Gallery (SIG) of beheerde afbeelding. Als u Gen2-afbeeldingen wilt maken, moet u een Gen2-bron-afbeelding gebruiken en distribueren naar VHD. Daarna moet u een beheerde afbeelding maken van de VHD en deze in de SIG injecteren als een Gen2-afbeelding.

De API vereist een 'SourceType' dat de bron definieert voor de build van de afbeelding. Er zijn momenteel drie typen:
- PlatformImage: aangegeven dat de bronafbeelding een Marketplace-afbeelding is.
- ManagedImage: gebruik deze als u begint met een reguliere beheerde afbeelding.
- SharedImageVersion: dit wordt gebruikt wanneer u een versie van een afbeelding in een Shared Image Gallery als bron gebruikt.


> [!NOTE]
> Wanneer u bestaande aangepaste Windows-afbeeldingen gebruikt, kunt u de Sysprep-opdracht maximaal acht keer uitvoeren op één Windows-afbeelding. Zie de [sysprep-documentatie](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep) voor meer informatie.

### <a name="platformimage-source"></a>PlatformImage-bron 
Azure Image Builder biedt ondersteuning voor Windows Server- en client- en Linux Azure Marketplace-afbeeldingen. Kijk hier [voor](../image-builder-overview.md#os-support) de volledige lijst. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


De eigenschappen hier zijn dezelfde die worden gebruikt om VM's te maken en voer met az cli de onderstaande uit om de eigenschappen op te halen: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

U kunt 'nieuwste' gebruiken in de versie. De versie wordt geëvalueerd wanneer de build van de afbeelding plaatsvindt, niet wanneer de sjabloon wordt verzonden. Als u deze functionaliteit gebruikt met de Shared Image Gallery-bestemming, kunt u voorkomen dat u de sjabloon opnieuw moet inzenden en de build van de afbeelding met intervallen opnieuw uitvoeren, zodat uw afbeeldingen opnieuw worden gemaakt op basis van de meest recente afbeeldingen.

#### <a name="support-for-market-place-plan-information"></a>Ondersteuning voor informatie over het Market Place-plan
U kunt ook plangegevens opgeven, bijvoorbeeld:
```json
    "source": {
        "type": "PlatformImage",
        "publisher": "RedHat",
        "offer": "rhel-byos",
        "sku": "rhel-lvm75",
        "version": "latest",
        "planInfo": {
            "planName": "rhel-lvm75",
            "planProduct": "rhel-byos",
            "planPublisher": "redhat"
       }
```
### <a name="managedimage-source"></a>ManagedImage-bron

Hiermee stelt u de bronafbeelding in als een bestaande beheerde afbeelding van een ge generaliseerde VHD of VM.

> [!NOTE]
> De door de bron beheerde afbeelding moet van een ondersteund besturingssysteem zijn en de afbeelding moet dezelfde regio hebben als uw Azure Image Builder sjabloon. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

De `imageId` moet de ResourceId van de beheerde afbeelding zijn. Gebruik om `az image list` beschikbare afbeeldingen weer te geven.


### <a name="sharedimageversion-source"></a>SharedImageVersion-bron
Hiermee stelt u de bronafbeelding een bestaande versie van de afbeelding in een Shared Image Gallery.

> [!NOTE]
> De door de bron beheerde afbeelding moet van een ondersteund besturingssysteem zijn en de afbeelding moet dezelfde regio hebben als uw Azure Image Builder-sjabloon. Als dat niet het zo is, repliceert u de versie van de afbeelding naar de regio Image Builder Sjabloon.


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

De `imageVersionId` moet de ResourceId van de versie van de afbeelding zijn. Gebruik [az sig image-version list om](/cli/azure/sig/image-version#az_sig_image_version_list) de versies van de afbeeldingen weer te geven.


## <a name="properties-buildtimeoutinminutes"></a>Eigenschappen: buildTimeoutInMinutes

Standaard wordt de Image Builder 240 minuten uitgevoerd. Daarna wordt er een time-out gemaakt en wordt gestopt, ongeacht of het bouwen van de afbeelding is voltooid. Als de time-out is geraakt, ziet u een fout die vergelijkbaar is met deze:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Als u geen buildTimeoutInMinutes-waarde opgeeft of op 0 in stelt, wordt de standaardwaarde gebruikt. U kunt de waarde verhogen of verlagen, tot het maximum van 960 minuten (16 uur). Voor Windows wordt u aangeraden dit niet lager dan 60 minuten in te stellen. Als u de time-out hebt, bekijkt u de logboeken [om](image-builder-troubleshoot.md#customization-log)te zien of de aanpassingsstap wacht op iets als gebruikersinvoer. 

Als u meer tijd nodig hebt om aanpassingen te voltooien, stelt u dit in op wat u denkt dat u nodig hebt, met wat overhead. Stel deze echter niet te hoog in omdat u mogelijk moet wachten tot er een time-out is opgetreden voordat er een fout wordt weergegeven. 


## <a name="properties-customize"></a>Eigenschappen: aanpassen

Image Builder ondersteunt meerdere 'aanpasbare gebruikers'. Aanpasfuncties zijn functies die worden gebruikt om uw afbeelding aan te passen, zoals het uitvoeren van scripts of het opnieuw opstarten van servers. 

Bij gebruik van `customize` : 
- U kunt meerdere aanpaslers gebruiken, maar ze moeten een unieke `name` hebben.
- Aanpasers worden uitgevoerd in de volgorde die is opgegeven in de sjabloon.
- Als één aanpassing mislukt, mislukt het hele aanpassingsonderdeel en wordt er een foutmelding weergegeven.
- U wordt ten zeerste aangeraden het script grondig te testen voordat u het in een sjabloon gebruikt. Het is eenvoudiger om het script op uw eigen VM te debuggen.
- Plaats geen gevoelige gegevens in de scripts. 
- De scriptlocaties moeten openbaar toegankelijk zijn, tenzij u [MSI gebruikt.](./image-builder-user-assigned-identity.md)

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
De sectie Aanpassen is een matrix. Azure Image Builder worden in sequentiële volgorde door de aanwijzers uitgevoerd. Elke fout in een aanwijzer mislukt het buildproces. 

> [!NOTE]
> Inline-opdrachten kunnen worden bekeken in de definitie van de afbeeldingssjabloon en door Microsoft-ondersteuning hulp bij een ondersteuningscase. Als u gevoelige informatie hebt, moet deze worden verplaatst naar scripts in Azure Storage, waar toegang verificatie vereist.
 
### <a name="shell-customizer"></a>Shell-aanwijzer

De shell-aanwijzer ondersteunt het uitvoeren van shellscripts. Deze moeten openbaar toegankelijk zijn voor de IB om er toegang toe te krijgen.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Ondersteuning voor het besturingssysteem: Linux 
 
Eigenschappen aanpassen:

- **type** – Shell 
- **name:** naam voor het bijhouden van de aanpassing 
- **scriptUri:** URI naar de locatie van het bestand 
- **inline:** matrix van shell-opdrachten, gescheiden door komma's.
- **sha256Checksum:** waarde van sha256 checksum van het bestand, genereert u dit lokaal en vervolgens Image Builder controlesum en valideren.
    * Voer de volgende stappen uit om de sha256Checksum te genereren met behulp van een terminal op Mac/Linux: `sha256sum <fileName>`

> [!NOTE]
> Inline-opdrachten worden opgeslagen als onderdeel van de definitie van de sjabloon voor afbeeldingen. U kunt deze zien wanneer u de definitie van de afbeelding dumpt. Deze zijn ook zichtbaar voor Microsoft-ondersteuning in het geval van een ondersteuningscase voor het oplossen van problemen. Als u gevoelige opdrachten of waarden hebt, wordt het sterk aanbevolen dat deze worden verplaatst naar scripts en een gebruikersidentiteit gebruiken om te verifiëren Azure Storage.

#### <a name="super-user-privileges"></a>Supergebruikersbevoegdheden
Voor opdrachten die moeten worden uitgevoerd met supergebruikersbevoegdheden, moeten ze worden vooraf laten gaan door . U kunt deze toevoegen aan scripts of inline opdrachten `sudo` gebruiken, bijvoorbeeld:
```json
                "type": "Shell",
                "name": "setupBuildPath",
                "inline": [
                    "sudo mkdir /buildArtifacts",
                    "sudo cp /tmp/index.html /buildArtifacts/index.html"
```
Voorbeeld van een script met sudo waarnaar u kunt verwijzen met behulp van scriptUri:
```bash
#!/bin/bash -e

echo "Telemetry: creating files"
mkdir /myfiles

echo "Telemetry: running sudo 'as-is' in a script"
sudo touch /myfiles/somethingElevated.txt
```

### <a name="windows-restart-customizer"></a>Windows-aanwijzer voor opnieuw opstarten 
Met de aanwijzer Voor opnieuw opstarten kunt u een Windows-VM opnieuw opstarten en wachten tot deze weer online is. Hierdoor kunt u software installeren die opnieuw moet worden opgestart.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

Ondersteuning voor besturingssysteem: Windows
 
Eigenschappen aanpassen:
- **Type:** WindowsRestart
- **restartCommand : opdracht** om het opnieuw opstarten uit te voeren (optioneel). De standaardwaarde is `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** : opdracht om te controleren of opnieuw opstarten is geslaagd (optioneel). 
- **restartTimeout:** time-out voor opnieuw opstarten opgegeven als een tekenreeks van magnitude en eenheid. Bijvoorbeeld `5m` (5 minuten) of `2h` (2 uur). De standaardwaarde is: '5m'

### <a name="linux-restart"></a>Linux opnieuw opstarten  
Er is geen linux-aanwijzer voor opnieuw opstarten, maar als u stuurprogramma's of onderdelen installeert waarvoor opnieuw opstarten is vereist, kunt u deze installeren en opnieuw opstarten aanroepen met behulp van de Shell-aanwijzer. Er is een SSH-time-out van 20 minuten voor de build-VM.

### <a name="powershell-customizer"></a>PowerShell-aanwijzer 
De shell-aanwijzer ondersteunt het uitvoeren van PowerShell-scripts en inline-opdrachten. De scripts moeten openbaar toegankelijk zijn voor de IB om er toegang toe te krijgen.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

Besturingssysteemondersteuning: Windows en Linux

Eigenschappen aanpassen:

- **type** : PowerShell.
- **scriptUri:** URI naar de locatie van het PowerShell-scriptbestand. 
- **inline:** inlineopdrachten die moeten worden uitgevoerd, gescheiden door komma's.
- **validExitCodes:** optionele, geldige codes die kunnen worden geretourneerd door de script-/inline-opdracht. Hierdoor wordt gemeld dat de script-/inline-opdracht mislukt.
- **runElevated: optioneel,** booleaanse, ondersteuning voor het uitvoeren van opdrachten en scripts met verhoogde machtigingen.
- **sha256Checksum:** de waarde van sha256-controlesum van het bestand. U genereert deze lokaal en vervolgens Image Builder controlesum en validatie.
    * De sha256Checksum genereren met behulp van een PowerShell op Windows [Get-Hash](/powershell/module/microsoft.powershell.utility/get-filehash)


### <a name="file-customizer"></a>Bestands aanpassen

Met de file customizer kan Image Builder een bestand downloaden uit een GitHub- of Azure-opslag. Als u een build-pijplijn voor de build van een afbeelding hebt die afhankelijk is van build-artefacten, kunt u de bestands aanpassen instellen om te downloaden van de build-share en de artefacten naar de afbeelding verplaatsen.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

Besturingssysteemondersteuning: Linux en Windows 

Eigenschappen van bestands aanpassen:

- **sourceUri:** een toegankelijk opslag-eindpunt. Dit kan GitHub- of Azure-opslag zijn. U kunt slechts één bestand downloaden, niet een volledige map. Als u een map wilt downloaden, gebruikt u een gecomprimeerd bestand en decomprimeert u het met behulp van de Shell- of PowerShell-aanpasders. 

> [!NOTE]
> Als de sourceUri een Azure Storage-account is, ongeacht of de blob als openbaar is gemarkeerd, moet u de beheerde gebruikersidentiteit machtigingen verlenen om toegang tot de blob te lezen. Raadpleeg dit voorbeeld [om](./image-builder-user-assigned-identity.md#create-a-resource-group) de opslagmachtigingen in te stellen.

- **destination:** dit is het volledige doelpad en de bestandsnaam. Elk pad en de subdirectorieën waarnaar wordt verwezen, moeten bestaan. Gebruik de Shell- of PowerShell-aanpasmogelijkheden om deze vooraf in te stellen. U kunt de script customizers gebruiken om het pad te maken. 

Dit wordt ondersteund door Windows-directories en Linux-paden, maar er zijn enkele verschillen: 
- Linux-besturingssysteem: het enige pad waar Image Builder naar kan schrijven, is /tmp.
- Windows: geen padbeperking, maar het pad moet bestaan.
 
 
Als er een fout is opgetreden bij het downloaden van het bestand of het bestand in een opgegeven map te zetten, mislukt de stap aanpassen en wordt dit weergegeven in het bestand customization.log.

> [!NOTE]
> De bestands aanpassen is alleen geschikt voor kleine bestanddownloads, < 20 MB. Voor grotere bestanddownloads gebruikt u een script of inline opdracht, de code gebruiken om bestanden te downloaden, zoals Linux `wget` of `curl` , Windows, `Invoke-WebRequest` .

### <a name="windows-update-customizer"></a>Windows Update-aanwijzer
Deze aanwijzer is gebaseerd op de [community Windows Update Provisioner](https://packer.io/docs/provisioners/community-supported.html) for Packer. Dit is een open source-project dat wordt beheerd door de Packer-community. Microsoft test en valideert de inrichting met de Image Builder-service en ondersteunt het onderzoeken van problemen met de service en werkt aan het oplossen van problemen, maar het open source-project wordt niet officieel ondersteund door Microsoft. Zie de projectopslagplaats voor gedetailleerde documentatie over en hulp Windows Update inrichtingsmanager.

```json
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
OS support: Windows
```

Eigenschappen aanpassen:
- **type:**  WindowsUpdate.
- **searchCriteria:** optioneel, definieert welk type updates zijn geïnstalleerd (Aanbevolen, Belangrijk enzovoort), BrowseOnly=0 en IsInstalled=0 (aanbevolen) de standaardinstelling is.
- **filters** : optioneel: hiermee kunt u een filter opgeven om updates op te nemen of uit te sluiten.
- **updateLimit:** optioneel, definieert hoeveel updates er kunnen worden geïnstalleerd, standaard 1000.
 
> [!NOTE]
> De Windows Update kan mislukken als er openstaande Windows-herstarts of toepassingsinstallaties actief zijn. Normaal gesproken wordt deze fout meestal weergegeven in customization.log, `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` . We raden u ten zeerste aan om Windows Opnieuw opstarten toe [](/powershell/module/microsoft.powershell.utility/start-sleep) te voegen en/of toepassingen voldoende tijd te geven om hun installaties te voltooien met behulp van slaapstand- of wachtopdrachten in de inlineopdrachten of scripts voordat u de Windows Update.

### <a name="generalize"></a>Generaliseren 
Standaard wordt in Azure Image Builder 'deprovision'-code uitgevoerd aan het einde van elke fase voor het aanpassen van de afbeelding, om de afbeelding te generaliseren. Generaliseren is een proces waarbij de afbeelding wordt ingesteld, zodat deze opnieuw kan worden gebruikt om meerdere VM's te maken. Voor Windows-VM's maakt Azure Image Builder gebruik van Sysprep. Voor Linux voert Azure Image Builder 'waagent -deprovision' uit. 

De opdrachten Image Builder gebruikers kunnen generaliseren, zijn mogelijk niet geschikt voor elke situatie, dus met Azure Image Builder kunt u deze opdracht zo nodig aanpassen. 

Als u bestaande aanpassing migreert en u verschillende Sysprep-/waagent-opdrachten gebruikt, kunt u de algemene Image Builder-opdrachten gebruiken. Als het maken van de VM mislukt, gebruikt u uw eigen Sysprep- of waagent-opdrachten.

Als Azure Image Builder een aangepaste Windows-afbeelding maakt en u er een VM van maakt en er vervolgens achter komt dat het maken van de VM mislukt of niet is voltooid, moet u de Windows Server Sysprep-documentatie bekijken of een ondersteuningsaanvraag indienen bij het klantondersteuningsteam van Windows Server Sysprep, dat problemen kan oplossen en advies kan geven over het juiste Sysprep-gebruik.


#### <a name="default-sysprep-command"></a>Standaard Sysprep-opdracht
```powershell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```
#### <a name="default-linux-deprovision-command"></a>Standaardopdracht voor het deprovision van Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>De opdrachten overschrijven
Als u de opdrachten wilt overschrijven, gebruikt u de PowerShell- of Shell-scriptinrichten om de opdrachtbestanden met de exacte bestandsnaam te maken en deze in de juiste mappen te zetten:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder deze opdrachten leest, worden deze weggeschreven naar de AIB-logboeken, 'customization.log'. Zie [Probleemoplossing voor](image-builder-troubleshoot.md#customization-log) het verzamelen van logboeken.
 
## <a name="properties-distribute"></a>Eigenschappen: distribueren

Azure Image Builder ondersteunt drie distributiedoelen: 

- **managedImage** : beheerde afbeelding.
- **sharedImage:** Shared Image Gallery.
- **VHD:** VHD in een opslagaccount.

U kunt een afbeelding distribueren naar beide doeltypen in dezelfde configuratie.

> [!NOTE]
> De standaardopdracht AIB sysprep bevat geen '/mode:vm', maar dit is mogelijk vereist bij het maken van afbeeldingen waarop de HyperV-rol is geïnstalleerd. Als u dit opdrachtargument wilt toevoegen, moet u de sysprep-opdracht overschrijven.

Omdat u meer dan één doel kunt hebben om naar te distribueren, houdt Image Builder een status bij voor elk distributiedoel dat toegankelijk is door een query uit te voeren op de `runOutputName` .  De `runOutputName` is een object dat u na distributie kunt opvragen voor informatie over die distributie. U kunt bijvoorbeeld een query uitvoeren op de locatie van de VHD of in regio's waar de versie van de afbeelding is gerepliceerd, of de SIG-versie van de afbeelding die is gemaakt. Dit is een eigenschap van elk distributiedoel. De `runOutputName` moet uniek zijn voor elk distributiedoel. Hier is een voorbeeld: hiermee wordt een query uitgevoerd op Shared Image Gallery distributie:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2020-02-14
```

Uitvoer:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Distribueren: managedImage

De uitvoer van de afbeelding is een beheerde afbeeldingsresource.

```json
{
       "type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
            "<name>": "<value>"
        }
}
```
 
Eigenschappen distribueren:
- **type** – managedImage 
- **imageId:** resource-id van de doelafbeelding, verwachte indeling: /subscriptions/ \<subscriptionId> /resourceGroups/ \<destinationResourceGroupName> /providers/Microsoft.Compute/images/\<imageName>
- **locatie:** de locatie van de beheerde afbeelding.  
- **runOutputName:** unieke naam voor het identificeren van de distributie.  
- **artifactTags: optionele** tags voor sleutel-waardeparen die door de gebruiker zijn opgegeven.
 
 
> [!NOTE]
> De doelresourcegroep moet bestaan.
> Als u wilt dat de installatie afbeelding wordt gedistribueerd naar een andere regio, wordt de implementatietijd vergroot. 

### <a name="distribute-sharedimage"></a>Distribueren: sharedImage 
De Azure Shared Image Gallery is een nieuwe service voor het beheer van afbeeldingen waarmee u replicatie van de afbeeldingsregio, versiebeheer en het delen van aangepaste afbeeldingen kunt beheren. Azure Image Builder distributie met deze service, zodat u afbeeldingen kunt distribueren naar regio's die worden ondersteund door galerieën met gedeelde afbeeldingen. 
 
Een Shared Image Gallery bestaat uit: 
 
- Galerie: container voor meerdere gedeelde afbeeldingen. Een galerie wordt geïmplementeerd in één regio.
- Definities van afbeeldingen: een conceptuele groepering voor afbeeldingen. 
- Versies van de afbeelding: dit is een type afbeelding dat wordt gebruikt voor het implementeren van een VM of schaalset. Versies van de afbeelding kunnen worden gerepliceerd naar andere regio's waar VM's moeten worden geïmplementeerd.
 
Voordat u naar de galerie met afbeeldingen kunt distribueren, moet u een galerie en een definitie van een afbeelding maken. [Zie Gedeelde afbeeldingen.](../shared-images-cli.md) 

```json
{
    "type": "SharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Eigenschappen distribueren voor galerieën met gedeelde afbeeldingen:

- **type** - sharedImage  
- **galleryImageId:** id van de galerie met gedeelde afbeeldingen. Dit kan in twee indelingen worden opgegeven:
    * Automatisch versien: Image Builder genereert een monotone versienummer voor u. Dit is handig voor wanneer u afbeeldingen opnieuw wilt bouwen op basis van dezelfde sjabloon: de indeling is: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` .
    * Expliciet versiegebruik: u kunt het versienummer doorgeven dat u door Image Builder wilt laten gebruiken. De indeling is: `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName:** unieke naam voor het identificeren van de distributie.  
- **artifactTags: optionele** tags voor sleutel-waardeparen die door de gebruiker zijn opgegeven.
- **replicationRegions:** matrix met regio's voor replicatie. Een van de regio's moet de regio zijn waarin de galerie wordt geïmplementeerd. Het toevoegen van regio's betekent een toename van de buildtijd, omdat de build pas wordt voltooid als de replicatie is voltooid.
- **excludeFromLatest** (optioneel) Hiermee kunt u markeren dat de versie van de afbeelding die u maakt, niet wordt gebruikt als de nieuwste versie in de SIG-definitie. De standaardwaarde is 'false'.
- **storageAccountType** (optioneel) AIB ondersteunt het opgeven van deze typen opslag voor de versie van de afbeelding die moet worden gemaakt:
    * 'Standard_LRS'
    * 'Standard_ZRS'


> [!NOTE]
> Als de sjabloon voor de afbeelding en waarnaar wordt verwezen zich niet op dezelfde locatie bevinden, ziet u extra tijd `image definition` om afbeeldingen te maken. Image Builder momenteel geen parameter voor de versieresource van de afbeelding heeft, nemen `location` we deze over van de bovenliggende `image definition` . Als een definitie van een afbeelding bijvoorbeeld westus is en u wilt dat de versie van de afbeelding wordt gerepliceerd naar eastus, wordt er een blob gekopieerd naar westus. Hier wordt een resource voor de versie van de afbeelding gemaakt in westus en vervolgens gerepliceerd naar eastus. Om de extra replicatietijd te voorkomen, moet u ervoor zorgen dat de sjabloon en de afbeelding `image definition` zich op dezelfde locatie bevinden.


### <a name="distribute-vhd"></a>Distribueren: VHD  
U kunt uitvoer naar een VHD. Vervolgens kunt u de VHD kopiëren en deze gebruiken om te publiceren naar Azure MarketPlace of gebruiken met Azure Stack.  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
Ondersteuning voor het besturingssysteem: Windows en Linux

VHD-parameters distribueren:

- **type** - VHD.
- **runOutputName:** unieke naam voor het identificeren van de distributie.  
- **tags:** optionele door de gebruiker opgegeven sleutelwaardepaartags.
 
Azure Image Builder niet toestaan dat de gebruiker een opslagaccountlocatie opgeeft, maar u kunt de status van de opvragen om `runOutputs` de locatie op te halen.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Zodra de VHD is gemaakt, kopieert u deze zo snel mogelijk naar een andere locatie. De VHD wordt opgeslagen in een opslagaccount in de tijdelijke resourcegroep die is gemaakt wanneer de afbeeldingssjabloon wordt verzonden naar de Azure Image Builder service. Als u de afbeeldingssjabloon verwijdert, verliest u de VHD. 

## <a name="image-template-operations"></a>Sjabloonbewerkingen voor afbeeldingen

### <a name="starting-an-image-build"></a>Een build van een afbeelding starten
Als u een build wilt starten, moet u 'Uitvoeren' aanroepen op de resource Afbeeldingssjabloon, voorbeelden van `run` opdrachten:

```PowerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```


```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

### <a name="cancelling-an-image-build"></a>Een build van een afbeelding annuleren
Als u een build van een afbeelding die u denkt dat onjuist is, wacht op invoer van de gebruiker of u denkt dat het nooit wordt voltooid, kunt u de build annuleren.

De build kan op elk moment worden geannuleerd. Als de distributiefase is gestart, kunt u nog steeds annuleren, maar u moet alle afbeeldingen ops schonen die mogelijk niet zijn voltooid. Met de opdracht annuleren wordt niet gewacht tot de annulering is voltooid. Controleer of de voortgang wordt geannuleerd met behulp `lastrunstatus.runstate` van deze [statusopdrachten](image-builder-troubleshoot.md#customization-log).


Voorbeelden van `cancel` opdrachten:

```powerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Cancel -Force
```

```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Cancel 
```

## <a name="next-steps"></a>Volgende stappen

Er zijn .json-voorbeeldbestanden voor verschillende scenario's in [azure Image Builder GitHub.](https://github.com/azure/azvmimagebuilder)