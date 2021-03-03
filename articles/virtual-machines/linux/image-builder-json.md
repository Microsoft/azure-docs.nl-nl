---
title: Een Azure Image Builder-sjabloon maken (preview)
description: Meer informatie over het maken van een sjabloon voor gebruik met Azure Image Builder.
author: danielsollondon
ms.author: danis
ms.date: 02/18/2021
ms.topic: reference
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: cynthn
ms.openlocfilehash: c2e4a2c2700af99a074dfd640177a6baefe763e2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670433"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Voor beeld: een Azure Image Builder-sjabloon maken 

Azure Image Builder gebruikt een. JSON-bestand om informatie door te geven aan de Image Builder-service. In dit artikel gaan we verder met de secties van het JSON-bestand, zodat u uw eigen kunt bouwen. Zie de [Azure Image Builder github](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)voor meer voor beelden van de volledige json-bestanden.

Dit is de basis indeling van de sjabloon:

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

Het `type` is het resource type, dat moet zijn `"Microsoft.VirtualMachineImages/imageTemplates"` . De `apiVersion` wordt na verloop van tijd gewijzigd wanneer de API wordt gewijzigd, maar moet `"2020-02-14"` voor preview zijn.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>Locatie

De locatie is de regio waar de aangepaste installatie kopie wordt gemaakt. Voor de preview-versie van Image Builder worden de volgende regio's ondersteund:

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
Standaard wordt met de opbouw functie voor installatie kopieën een VM voor het bouwen van een Standard_D1_v2 gebruikt. u kunt dit bijvoorbeeld negeren als u een installatie kopie voor een GPU-VM wilt aanpassen, moet u een GPU VM-grootte hebben. Dit is optioneel.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Standaard wordt de grootte van de installatie kopie niet door de opbouw functie voor installatie kopieën gewijzigd, wordt de grootte van de bron afbeelding gebruikt. U kunt **alleen** de grootte van de besturingssysteem schijf (Win en Linux) verg Roten, dit is optioneel en de waarde 0 betekent dat de grootte van de bron afbeelding gelijk blijft. U kunt de grootte van de besturingssysteem schijf niet verkleinen tot deze kleiner is dan de grootte van de bron installatie kopie.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Als u geen VNET-eigenschappen opgeeft, maakt de opbouw functie voor installatie kopieën een eigen VNET, openbaar IP-adres en NSG. Het open bare IP-adres wordt gebruikt om te communiceren met de build-VM, maar als u niet wilt dat een openbaar IP-adres of de opbouw functie voor installatie kopieën toegang heeft tot uw bestaande VNET-resources, zoals configuratie servers (DSC, chef, puppet, Ansible), bestands shares enzovoort, kunt u een VNET opgeven. Raadpleeg de [documentatie bij netwerken](image-builder-networking.md)voor meer informatie. Dit is optioneel.

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

Zie [resource afhankelijkheden definiëren](../../azure-resource-manager/templates/define-resource-dependency.md#dependson)voor meer informatie.

## <a name="identity"></a>Identiteit

Vereist: voor de opbouw functie voor installatie kopieën die machtigingen voor lezen/schrijven moeten hebben, leest u in scripts van Azure Storage u moet een Azure User-Assigned-identiteit maken die machtigingen heeft voor de afzonderlijke resources. Raadpleeg de [documentatie](image-builder-user-assigned-identity.md)voor meer informatie over de werking van de opbouw functie voor installatie kopieën en de relevante stappen.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


Ondersteuning voor Image Builder voor een User-Assigned identiteit:
* Ondersteunt slechts één identiteit
* Biedt geen ondersteuning voor aangepaste domein namen

Zie [Wat is beheerde identiteiten voor Azure-resources?](../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie.
Voor meer informatie over het implementeren van deze functie raadpleegt u [beheerde identiteiten voor Azure-resources configureren op een virtuele Azure-machine met behulp van Azure cli](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity).

## <a name="properties-source"></a>Eigenschappen: Bron

De `source` sectie bevat informatie over de bron installatie kopie die wordt gebruikt door de opbouw functie voor afbeeldingen. De opbouw functie voor installatie kopieën ondersteunt momenteel alleen systeem eigen ondersteuning voor het maken van Hyper-V-generatie (gen1) 1 installatie kopieën in de Azure Shared Image Gallery (SIG) of een beheerde installatie kopie. Als u Gen2-installatie kopieën wilt maken, moet u een Gen2-bron installatie kopie gebruiken en distribueren naar VHD. Daarna moet u een beheerde installatie kopie maken van de VHD en deze in de SIG injecteren als een Gen2-installatie kopie.

Voor de API is een source type vereist dat de bron voor de build van de installatie kopie definieert. momenteel zijn er drie typen:
- PlatformImage: er is aangegeven dat de bron afbeelding een Marketplace-installatie kopie is.
- ManagedImage: gebruik dit wanneer u vanaf een normale beheerde installatie kopie begint.
- SharedImageVersion: dit wordt gebruikt wanneer u een installatie kopie versie in een galerie met gedeelde afbeeldingen als de bron gebruikt.


> [!NOTE]
> Wanneer u bestaande Windows-aangepaste installatie kopieën gebruikt, kunt u de Sysprep-opdracht Maxi maal acht keer uitvoeren op één Windows-installatie kopie. Raadpleeg de documentatie van [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep) voor meer informatie.

### <a name="platformimage-source"></a>PlatformImage-bron 
Azure Image Builder biedt ondersteuning voor Windows Server-en client-en Linux Azure Marketplace-installatie kopieën. Zie [hier](../image-builder-overview.md#os-support) voor de volledige lijst. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


De eigenschappen die hier worden gebruikt voor het maken van VM'S met behulp van AZ CLI, voert u de onderstaande stappen uit om de eigenschappen op te halen: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

U kunt ' meest recent ' gebruiken in de versie, de versie wordt geëvalueerd wanneer de installatie kopie wordt gemaakt, niet wanneer de sjabloon wordt verzonden. Als u deze functie gebruikt met het doel van de gedeelde installatie kopie galerie, kunt u voor komen dat de sjabloon opnieuw wordt verzonden en de installatie kopie met intervallen opnieuw uitvoeren, zodat uw installatie kopieën opnieuw worden gemaakt uit de meest recente installatie kopieën.

#### <a name="support-for-market-place-plan-information"></a>Ondersteuning voor informatie over het marktplaats plan
U kunt ook plan gegevens opgeven, bijvoorbeeld:
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

Hiermee stelt u de bron installatie kopie als een bestaande beheerde installatie kopie van een gegeneraliseerde VHD of virtuele machine.

> [!NOTE]
> De door de bron beheerde installatie kopie moet van een ondersteund besturings systeem zijn en de installatie kopie moet dezelfde regio hebben als uw Azure Image Builder-sjabloon. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

De `imageId` moet de ResourceID van de beheerde installatie kopie zijn. Gebruiken `az image list` om de beschik bare installatie kopieën weer te geven.


### <a name="sharedimageversion-source"></a>SharedImageVersion-bron
Hiermee stelt u de bron afbeelding een versie van een bestaande installatie kopie in een galerie met gedeelde afbeeldingen.

> [!NOTE]
> De door de bron beheerde installatie kopie moet van een ondersteund besturings systeem zijn en de installatie kopie moet dezelfde regio hebben als uw Azure Image Builder-sjabloon. als dat niet het geval is, repliceert u de installatie kopie versie naar de sjabloon regio voor installatie kopie Builder.


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

De `imageVersionId` moet de ResourceID van de versie van de installatie kopie zijn. Gebruik [AZ sig installatie kopie](/cli/azure/sig/image-version#az-sig-image-version-list) van de lijst met installatie kopieën om versie-versies weer te geven.


## <a name="properties-buildtimeoutinminutes"></a>Eigenschappen: buildTimeoutInMinutes

Standaard wordt de opbouw functie voor installatie kopieën gedurende 240 minuten uitgevoerd. Daarna wordt de time-out en stopt, ongeacht of de installatie kopie is gemaakt. Als de time-out wordt weer gegeven, ziet u een fout die vergelijkbaar is met de volgende:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Als u geen buildTimeoutInMinutes-waarde opgeeft of als u deze instelt op 0, wordt de standaard waarde gebruikt. U kunt de waarde verg Roten of verkleinen, tot het maximum van 960mins (16hrs). Voor Windows raden we u aan dit 60 minuten niet in te stellen. Als u merkt dat u de time-out hebt, raadpleegt u de [Logboeken](image-builder-troubleshoot.md#customization-log)om te zien of de stap voor aanpassing wacht op een soort gebruikers invoer. 

Als u vindt dat er meer tijd nodig is voor het volt ooien van aanpassingen, stelt u deze in op wat u denkt dat u nodig hebt, met een beetje extra overhead. Stel deze echter niet te hoog in omdat u mogelijk moet wachten op time-out voordat u een fout ziet. 


## <a name="properties-customize"></a>Eigenschappen: aanpassen

De opbouw functie voor installatie kopieën ondersteunt meerdere ' Customizers '. Customizers zijn functies die worden gebruikt voor het aanpassen van uw installatie kopie, zoals het uitvoeren van scripts of het opnieuw opstarten van servers. 

Bij gebruik van `customize` : 
- U kunt meerdere aanpassingen gebruiken, maar ze moeten uniek zijn `name` .
- Aanpassingen worden uitgevoerd in de volg orde die is opgegeven in de sjabloon.
- Als een aanpassings functie mislukt, mislukt het hele aanpassings onderdeel en wordt er een fout melding weer gegeven.
- U wordt aangeraden het script grondig te testen voordat u het in een sjabloon kunt gebruiken. Fout opsporing van het script op uw eigen VM is eenvoudiger.
- Plaats geen gevoelige gegevens in de scripts. 
- De script locaties moeten openbaar toegankelijk zijn, tenzij u [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)gebruikt.

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

 
De sectie Customize is een matrix. De opbouw functie voor installatie kopieën van Azure wordt door de aanpassings functies in sequentiële volg orde uitgevoerd. Als er een fout optreedt in een aanpassings proces, mislukt het buildproces. 

> [!NOTE]
> Inline-opdrachten kunnen worden weer gegeven in de definitie van de afbeeldings sjabloon en door Microsoft Ondersteuning bij het helpen bij een ondersteunings aanvraag. Als u gevoelige informatie hebt, moet deze worden verplaatst naar scripts in Azure Storage, waar toegang vereist is.
 
### <a name="shell-customizer"></a>Shell-aanpassing

Shell-aanpassing ondersteunt het uitvoeren van shell scripts. deze moeten openbaar toegankelijk zijn voor de IB om ze te openen.

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

Ondersteuning voor besturings systeem: Linux 
 
Eigenschappen aanpassen:

- **type** – shell 
- **naam** -naam voor het bijhouden van de aanpassing 
- **scriptUri** -URI naar de locatie van het bestand 
- **inline** -matrix van shell opdrachten, gescheiden door komma's.
- **sha256Checksum** -waarde van de sha256-controlesom van het bestand, u genereert dit lokaal en vervolgens wordt de opbouw functie voor installatie kopieën gecontroleerd en gevalideerd.
    * De sha256Checksum genereren met behulp van een Terminal op Mac/Linux-uitvoering: `sha256sum <fileName>`

> [!NOTE]
> Inline-opdrachten worden opgeslagen als onderdeel van de definitie van de afbeeldings sjabloon, maar u kunt deze zien wanneer u de definitie van de installatie kopie dumpen. deze zijn ook zichtbaar voor Microsoft Ondersteuning in het geval van een ondersteunings aanvraag voor het oplossen van problemen. Als u gevoelige opdrachten of waarden hebt, wordt het ten zeerste aangeraden deze naar scripts te verplaatsen en een gebruikers-id te gebruiken voor het verifiëren van Azure Storage.

#### <a name="super-user-privileges"></a>Super gebruikers privileges
Om opdrachten uit te voeren met super gebruikers bevoegdheden, moeten ze worden voorafgegaan door `sudo` , kunt u deze toevoegen aan scripts of de inline-opdrachten gebruiken, bijvoorbeeld:
```json
                "type": "Shell",
                "name": "setupBuildPath",
                "inline": [
                    "sudo mkdir /buildArtifacts",
                    "sudo cp /tmp/index.html /buildArtifacts/index.html"
```
Voor beeld van een script met sudo dat u kunt raadplegen met behulp van scriptUri:
```bash
#!/bin/bash -e

echo "Telemetry: creating files"
mkdir /myfiles

echo "Telemetry: running sudo 'as-is' in a script"
sudo touch /myfiles/somethingElevated.txt
```

### <a name="windows-restart-customizer"></a>Aanpassings venster voor Windows opnieuw starten 
Met de aanpassings functie voor opnieuw opstarten kunt u een Windows-VM opnieuw opstarten en wachten totdat deze weer online is. Hierdoor kunt u software installeren waarvoor opnieuw moet worden opgestart.  

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

BESTURINGSSYSTEEM ondersteuning: Windows
 
Eigenschappen aanpassen:
- **Type**: WindowsRestart
- **restartCommand** : opdracht voor het uitvoeren van de herstart (optioneel). De standaardwaarde is `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – opdracht om te controleren of opnieuw opstarten is geslaagd (optioneel). 
- **restartTimeout** : de time-out voor opnieuw opstarten is opgegeven als een teken reeks van grootte en eenheid. Bijvoorbeeld `5m` (5 minuten) of `2h` (2 uur). De standaard waarde is: ' 5 min. '

### <a name="linux-restart"></a>Linux opnieuw opstarten  
Er is geen Linux-aanpassings programma nodig. Als u echter Stuur Programma's installeert of onderdelen die opnieuw moeten worden opgestart, kunt u deze installeren en een herstart aanroepen met de shell-aanpassings programma. er is een 20min SSH-time-out voor de build-VM.

### <a name="powershell-customizer"></a>Power shell-aanpassing 
Shell Customize ondersteunt het uitvoeren van Power shell-scripts en de inline-opdracht, maar de scripts moeten openbaar toegankelijk zijn voor de IB om ze te openen.

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

BESTURINGSSYSTEEM ondersteuning: Windows en Linux

Eigenschappen aanpassen:

- **type** – Power shell.
- **scriptUri** -URI naar de locatie van het Power shell-script bestand. 
- **inline** : inline-opdrachten die moeten worden uitgevoerd, gescheiden door komma's.
- **validExitCodes** – optioneel, geldige codes die kunnen worden geretourneerd door de script/inline opdracht, waardoor het mislukken van de script/inline-opdracht wordt voor komen.
- **runElevated** : optioneel, Booleaans, ondersteuning voor het uitvoeren van opdrachten en scripts met verhoogde machtigingen.
- **sha256Checksum** -waarde van de sha256-controlesom van het bestand, u genereert dit lokaal en vervolgens wordt de opbouw functie voor installatie kopieën gecontroleerd en gevalideerd.
    * De sha256Checksum genereren met behulp van een Power shell op Windows [Get-hash](/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Bestands aanpassing

Met de file Customizer kunt u met Image Builder een bestand downloaden van een GitHub of Azure-opslag. Als u een pijp lijn voor het bouwen van een installatie kopie hebt die afhankelijk is van het bouwen van artefacten, kunt u de bestands aanpassings functie zo instellen dat deze wordt gedownload via de build-share en de artefacten verplaatsen naar de installatie kopie.  

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

Ondersteuning voor besturings systeem: Linux en Windows 

Eigenschappen van bestands aanpassing:

- **sourceUri** : een toegankelijk eind punt dat kan worden github of Azure Storage. U kunt slechts één bestand downloaden, niet een volledige map. Als u een map wilt downloaden, gebruikt u een gecomprimeerd bestand en comprimeert u het met de shell-of Power shell-aanpassingen. 

> [!NOTE]
> Als de sourceUri een Azure Storage-account is, ongeacht of de blob is gemarkeerd als openbaar, moet u de beheerde gebruikers identiteits machtigingen verlenen om toegang te krijgen tot de blob. Raadpleeg dit [voor beeld](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-user-assigned-identity#create-a-resource-group) om de machtigingen voor opslag in te stellen.

- **doel** : dit is het volledige doelpad en de bestands naam. Elk pad en submappen waarnaar wordt verwezen moeten bestaan, de shell-of Power shell-aanpassingen gebruiken om deze vooraf in te stellen. U kunt de script Customizers gebruiken om het pad te maken. 

Dit wordt ondersteund door Windows-mappen en Linux-paden, maar er zijn enkele verschillen: 
- Linux-besturings systeem: de alleen de opbouw functie van het pad naar de afbeelding kan schrijven naar/tmp.
- Windows: geen pad beperken, maar het pad moet bestaan.
 
 
Als er een fout optreedt bij het downloaden van het bestand of in een opgegeven map worden geplaatst, mislukt de stap voor het aanpassen en wordt deze weer gegeven in de aanpassings. log.

> [!NOTE]
> De bestands aanpassing is alleen geschikt voor kleine bestands downloads, < 20 MB. Voor grotere bestands downloads kunt u een script of inline opdracht gebruiken, de code gebruiken om bestanden te downloaden, zoals Linux `wget` of `curl` Windows, `Invoke-WebRequest` .

### <a name="windows-update-customizer"></a>Windows Update aanpassing
Deze aanpassings is gebaseerd op de [community Windows Update inrichting](https://packer.io/docs/provisioners/community-supported.html) voor de verpakker, een open-source project dat wordt beheerd door de Packer-community. Micro soft test en valideert de inrichtings functie met de service voor installatie kopie maken, en ondersteunt problemen met onderzoeken, en werkt om problemen op te lossen, maar het open-source project wordt niet officieel ondersteund door micro soft. Raadpleeg de project opslagplaats voor gedetailleerde documentatie over en hulp bij de Windows Update-inrichting.

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
- **type**  – windowsupdate.
- **searchCriteria** -optioneel, definieert welk type updates wordt geïnstalleerd (aanbevolen, belang rijk enz.), BrowseOnly = 0 en IsInstalled = 0 (aanbevolen) is de standaard instelling.
- **filters** : optioneel, Hiermee kunt u een filter opgeven om updates op te nemen of uit te sluiten.
- **updateLimit** – optioneel, definieert het aantal updates dat kan worden geïnstalleerd, standaard 1000.
 
> [!NOTE]
> De Windows Update-aanpassings bewerking kan mislukken als er openstaande Windows-startingen of toepassings installaties nog steeds worden uitgevoerd. deze fout kan meestal worden weer geven in de aanpassingen. log, `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` . U kunt het beste overwegen om in een Windows-computer opnieuw op te starten en/of toepassingen voldoende tijd te geven om de installatie te volt ooien met behulp van [slapen] of wacht opdrachten ( https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/start-sleep?view=powershell-7) in de inline-opdrachten of-scripts voordat u Windows Update uitvoert.

### <a name="generalize"></a>Generaliseren 
Azure Image Builder voert standaard ook de code ' provisioning ' uit aan het einde van elke aanpassings fase van de installatie kopie tot ' generalize ' in de installatie kopie. Generalize is een proces waarbij de installatie kopie wordt ingesteld zodat deze opnieuw kan worden gebruikt om meerdere virtuele machines te maken. Voor virtuele Windows-machines maakt Azure Image Builder gebruik van Sysprep. Voor Linux voert Azure Image Builder ' waagent-deprovision ' uit. 

De opdrachten Image Builder-gebruikers om te generaliseren zijn mogelijk niet geschikt voor elke situatie. Daarom kunt u met Azure Image Builder deze opdracht aanpassen, als dat nodig is. 

Als u bestaande aanpassingen migreert en u verschillende Sysprep/waagent-opdrachten gebruikt, kunt u de algemene opdrachten van de opbouw functie voor afbeeldingen gebruiken en als het maken van de virtuele machine mislukt, gebruikt u uw eigen Sysprep-of waagent-opdrachten.

Als de opbouw functie van Azure image een Windows-aangepaste installatie kopie maakt en u hiervan een VM maakt, kunt u er vervolgens voor zorgen dat de VM wordt gemaakt of mislukt, moet u de Windows Server Sysprep-documentatie raadplegen of een ondersteunings aanvraag met het ondersteunings team van Windows Server Sysprep gebruiken, die problemen kan oplossen met het juiste gebruik van Sysprep.


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
#### <a name="default-linux-deprovision-command"></a>Standaard opdracht voor het ongedaan maken van Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>De opdrachten overschrijven
Als u de opdrachten wilt onderdrukken, gebruikt u de Power shell-of shell-script inrichtingen om de opdracht bestanden met de exacte bestands naam te maken en deze in de juiste directory's te plaatsen:

* Windows: c:\DeprovisioningScript.ps1
* Linux:/tmp/DeprovisioningScript.sh

Met de opbouw functie voor installatie kopieën worden deze opdrachten gelezen, die worden wegge schreven naar de AIB-logboeken Customization. log. Zie [probleem oplossing](image-builder-troubleshoot.md#customization-log) voor het verzamelen van Logboeken.
 
## <a name="properties-distribute"></a>Eigenschappen: distribueren

Azure Image Builder ondersteunt drie distributie doelen: 

- **managedImage** -beheerde installatie kopie.
- Galerie met **sharedImage** -gedeelde afbeeldingen.
- **VHD** -VHD in een opslag account.

U kunt een installatie kopie distribueren naar beide doel typen in dezelfde configuratie.

> [!NOTE]
> De standaard AIB Sysprep-opdracht bevat niet '/mode: VM ', maar dit is mogelijk vereist bij het maken van installatie kopieën waarop de hyper-v-rol is geïnstalleerd. Als u dit opdracht argument moet toevoegen, moet u de Sysprep-opdracht onderdrukken.

Omdat er meer dan één doel kan zijn om naar te distribueren, houdt Image Builder een status bij voor elk distributie doel dat toegankelijk is door query's uit te stellen op de `runOutputName` .  Het `runOutputName` is een-object waarmee u een query kunt uitvoeren op distributie voor informatie over die distributie. U kunt bijvoorbeeld een query uitvoeren op de locatie van de VHD, of regio's waarnaar de versie van de installatie kopie is gerepliceerd, of de versie van de SIG-installatie kopie is gemaakt. Dit is een eigenschap van elke distributie doel. De `runOutputName` moet uniek zijn voor elk distributie doel. Hier volgt een voor beeld van het uitvoeren van een query op de distributie van een gedeelde installatie kopie galerie:

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

De uitvoer van de installatie kopie is een beheerde afbeeldings bron.

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
- **imageId** : de resource-id van de doel afbeelding, de verwachte indeling:/Subscriptions/ \<subscriptionId> /resourceGroups/ \<destinationResourceGroupName> /providers/Microsoft.Compute/images/\<imageName>
- **locatie** : locatie van de beheerde installatie kopie.  
- **runOutputName** : een unieke naam voor het identificeren van de distributie.  
- **artifactTags** -optionele door de gebruiker opgegeven sleutel waarde-paar tags.
 
 
> [!NOTE]
> De doel resource groep moet bestaan.
> Als u wilt dat de afbeelding wordt gedistribueerd naar een andere regio, neemt de implementatie tijd toe. 

### <a name="distribute-sharedimage"></a>Distribueren: sharedImage 
De galerie met gedeelde Azure-installatie kopieën is een nieuwe service voor het beheer van installatie kopieën waarmee u de installatie kopie regio kunt beheren, versie beheer en delen van aangepaste installatie kopieën. Azure Image Builder ondersteunt de distributie met deze service, zodat u installatie kopieën kunt distribueren naar regio's die worden ondersteund door de galerie met gedeelde afbeeldingen. 
 
Een galerie met gedeelde afbeeldingen bestaat uit: 
 
- Galerie-container voor meerdere gedeelde installatie kopieën. Een galerie wordt in één regio geïmplementeerd.
- Afbeeldings definities: een conceptuele groepering voor installatie kopieën. 
- Installatie kopie versies: dit is een afbeeldings type dat wordt gebruikt voor het implementeren van een virtuele machine of schaalset. Installatie kopie versies kunnen worden gerepliceerd naar andere regio's waar Vm's moeten worden geïmplementeerd.
 
Voordat u naar de galerie met installatie kopieën kunt distribueren, moet u een galerie en een definitie van een installatie kopie maken. Zie [gedeelde installatie kopieën](../shared-images-cli.md). 

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

Eigenschappen voor gedeelde afbeeldings galerieën distribueren:

- **type** -sharedImage  
- **galleryImageId** : id van de galerie met gedeelde afbeeldingen kan in twee indelingen worden opgegeven:
    * Automatisch versie beheer: met de opbouw functie voor installatie kopieën wordt een monoton versie nummer voor u gegenereerd. Dit is handig als u wilt dat de installatie kopieën van dezelfde sjabloon opnieuw worden samengesteld: de volgende indeling: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` .
    * Expliciete versie beheer: u kunt het versie nummer door geven dat door de opbouw functie voor installatie kopieën moet worden gebruikt. De indeling is: `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName** : een unieke naam voor het identificeren van de distributie.  
- **artifactTags** -optionele door de gebruiker opgegeven sleutel waarde-paar tags.
- **replicationRegions** : matrix van regio's voor replicatie. Een van de regio's moet de regio zijn waarin de galerie wordt geïmplementeerd. Het toevoegen van regio's resulteert in een toename van de bouw tijd, omdat de build niet kan worden voltooid totdat de replicatie is voltooid.
- **excludeFromLatest** (optioneel) Hiermee kunt u de versie van de installatie kopie die u maakt, markeren als de meest recente versie in de definitie van de SIG. de standaard waarde is ' false '.
- **storageAccountType** (optioneel) AIB ondersteunt het opgeven van deze typen opslag voor de installatie kopie versie die moet worden gemaakt:
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> Als de afbeeldings sjabloon en waarnaar wordt verwezen `image definition` , zich niet op dezelfde locatie bevinden, wordt er meer tijd voor het maken van installatie kopieën weer geven. De opbouw functie voor installatie kopieën heeft momenteel geen `location` para meter voor de bron van de afbeeldings versie `image definition` . Als de definitie van een installatie kopie zich bijvoorbeeld in westus bevindt en u wilt dat de versie van de installatie kopie wordt gerepliceerd naar oostus, wordt een BLOB gekopieerd naar westus, hiervan wordt een afbeeldings versie bron in westus gemaakt en vervolgens gerepliceerd naar oostus. Zorg ervoor dat de `image definition` sjabloon en de installatie kopie zich op dezelfde locatie bevinden om de extra replicatie tijd te voor komen.


### <a name="distribute-vhd"></a>Distribueren: VHD  
U kunt naar een VHD uitvoeren. U kunt de VHD vervolgens kopiëren en gebruiken om te publiceren naar Azure MarketPlace of gebruiken met Azure Stack.  

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
 
BESTURINGSSYSTEEM ondersteuning: Windows en Linux

VHD-para meters distribueren:

- **type** -VHD.
- **runOutputName** : een unieke naam voor het identificeren van de distributie.  
- **Tags** -optioneel door de gebruiker opgegeven sleutel waarde-paar tags.
 
De gebruiker kan met Azure Image Builder geen locatie opgeven voor het opslag account, maar u kunt wel een query uitvoeren op de status van de `runOutputs` om de locatie op te halen.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Zodra de VHD is gemaakt, kopieert u deze naar een andere locatie, zo snel mogelijk. De VHD wordt opgeslagen in een opslag account in de tijdelijke resource groep die is gemaakt wanneer de installatie kopie sjabloon wordt verzonden naar de Azure Image Builder-service. Als u de afbeeldings sjabloon verwijdert, gaat de VHD verloren. 

## <a name="image-template-operations"></a>Bewerkingen voor installatie kopie sjablonen

### <a name="starting-an-image-build"></a>Genereren van een installatie kopie starten
Als u een build wilt starten, moet u uitvoeren op de bron van de afbeeldings sjabloon, voor beelden van `run` opdrachten:

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

### <a name="cancelling-an-image-build"></a>Genereren van een installatie kopie annuleren
Als u een installatie kopie-build uitvoert waarvan u denkt dat ze onjuist zijn, wordt gewacht op invoer van de gebruiker of dat u het probleem nooit kunt volt ooien. vervolgens annuleert u de build.

De build kan op elk gewenst moment worden geannuleerd. Als de distributie fase is gestart, kunt u nog steeds annuleren, maar u moet alle installatie kopieën opschonen die mogelijk niet zijn voltooid. De Annuleer opdracht wacht niet op Annuleren om te volt ooien. Controleer of de `lastrunstatus.runstate` voortgang is geannuleerd met behulp van deze status [opdrachten](image-builder-troubleshoot.md#customization-log).


Voor beelden van `cancel` opdrachten:

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

Er zijn voor beelden van json-bestanden voor verschillende scenario's in de [Azure Image Builder-github](https://github.com/danielsollondon/azvmimagebuilder).
