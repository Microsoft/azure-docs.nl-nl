---
title: Aangepaste script extensie versie 1 (afgeschaft) uitvoeren op virtuele Linux-machines in azure
description: Configuratie taken voor Linux-VM'S automatiseren met behulp van de aangepaste script extensie v1
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: afce76c8f5330f7d73ff58cc9b9307af2b8832f4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962124"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Gebruik de aangepaste script extensie versie 1 van Azure met virtuele Linux-machines

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

De aangepaste script extensie versie 1 downloadt en voert scripts uit op virtuele machines van Azure. Deze uitbrei ding is handig voor configuratie na de implementatie, software-installatie of een andere configuratie/beheer taak. U kunt scripts downloaden van Azure Storage of een andere toegankelijke Internet locatie, of u kunt deze opgeven voor de runtime van de uitbrei ding.

De aangepaste script extensie kan worden geïntegreerd met Azure Resource Manager sjablonen. U kunt dit ook uitvoeren met behulp van Azure CLI, Power shell, de Azure Portal of de Azure Virtual Machines REST API.

In dit artikel wordt beschreven hoe u de aangepaste script extensie van Azure CLI gebruikt en hoe u de extensie kunt uitvoeren met behulp van een Azure Resource Manager sjabloon. Dit artikel bevat ook stappen voor het oplossen van problemen met Linux-systemen.

Er zijn twee aangepaste Linux-script uitbreidingen:

* Versie 1: micro soft. OSTCExtensions. CustomScriptForLinux

* Versie 2: micro soft. Azure. Extensions. CustomScript

Wijzig in plaats daarvan nieuwe en bestaande implementaties om de nieuwe versie ([micro soft. Azure. Extensions. CustomScript](custom-script-linux.md)) te gebruiken. De nieuwe versie is bedoeld als kant-en-klare vervanging. Voor de migratie hoeft u slechts de naam en versie te wijzigen. De configuratie van de extensie hoeft niet te worden gewijzigd.

### <a name="operating-system"></a>Besturingssysteem

Ondersteunde Linux-distributies:

* CentOS 6,5 en hoger
* Debian 8 en hoger
  * Debian 8,7 wordt niet geleverd met Python2 in de meest recente installatie kopieën, wat CustomScriptForLinux verbreekt.
* FreeBSD
* OpenSUSE 13,1 en hoger
* Oracle Linux 6,4 en hoger
* SUSE Linux Enterprise Server 11 SP3 en hoger
* Ubuntu 12,04 en hoger

### <a name="script-location"></a>Locatie van script

U kunt de extensie gebruiken om uw Azure Blob Storage-referenties te gebruiken voor toegang tot Azure Blob-opslag. Het is ook mogelijk dat de locatie van het script een wille keurige waar is, zolang de virtuele machine kan worden doorgestuurd naar dat eind punt, zoals GitHub, interne bestands server, enzovoort.

### <a name="internet-connectivity"></a>Internet verbinding

Als u een script extern moet downloaden, zoals GitHub of Azure Storage, moeten er extra poorten voor de firewall/netwerk beveiligings groep worden geopend. Als uw script zich bijvoorbeeld in Azure Storage bevindt, kunt u toegang toestaan via de Azure NSG-service tags voor [opslag](../../virtual-network/network-security-groups-overview.md#service-tags).

Als uw script zich op een lokale server bevindt, moet u mogelijk nog steeds extra firewall-of netwerk beveiligings groep poorten openen.

### <a name="tips-and-tricks"></a>Tips en trucs

* De meeste fouten voor deze extensie worden veroorzaakt door syntaxisfouten in het script. Test de scriptuitvoeringen zonder fouten en schakel aanvullende logboekregistratie in het script in om gemakkelijker te achterhalen waar het misging.
* Schrijf scripts die idempotent zijn, zodat het systeem niet wordt gewijzigd als de scripts per ongeluk opnieuw meer dan één keer worden uitgevoerd.
* Zorg ervoor dat de scripts geen gebruikers invoer vereisen wanneer ze worden uitgevoerd.
* Er is 90 minuten om het script uit te voeren, wat langer resulteert in een mislukte inrichting van de uitbrei ding.
* Start de computer niet opnieuw op in het script, waardoor er problemen zijn met andere uitbrei dingen die worden geïnstalleerd en na het opnieuw opstarten. de uitbrei ding wordt na het opnieuw opstarten niet voortgezet. 
* Als u een script hebt dat ertoe leidt dat de computer opnieuw wordt opgestart, installeert u toepassingen en voert u scripts, enz. U moet de herstart plannen met behulp van een cron-taak of gebruikmaken van hulpprogram ma's zoals DSC, of chef, puppet-extensies.
* De uitbrei ding voert slechts één script uit, als u een script wilt uitvoeren op elke keer dat u opstart, dan kunt u een [Cloud-init-installatie kopie](../linux/using-cloud-init.md)  gebruiken en een script [per opstart](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) module gebruiken. U kunt ook het script gebruiken om een systeem-service-eenheid te maken.
* Als u wilt plannen wanneer een script wordt uitgevoerd, moet u de extensie gebruiken om een cron-taak te maken.
* Wanneer het script wordt uitgevoerd, ziet u alleen de extensiestatus 'overgang maken' van de Azure-portal of CLI. Als u meer frequente status updates van een actief script wilt, moet u uw eigen oplossing maken.
* Aangepaste script extensie biedt geen systeem eigen ondersteuning voor proxy servers, maar u kunt wel een hulp programma voor bestands overdracht gebruiken dat proxy servers in uw script ondersteunt, zoals *krul*.
* Houd er rekening mee dat niet-standaard adreslijst locaties waarvan uw scripts of opdrachten afhankelijk zijn, logica hebben om dit te kunnen afhandelen.

## <a name="extension-schema"></a>Extensieschema

De configuratie van de aangepaste script extensie bevat dingen als de script locatie en de opdracht die moet worden uitgevoerd. U kunt deze configuratie opslaan in configuratie bestanden, opgeven op de opdracht regel of in een Azure Resource Manager sjabloon opgeven. 

U kunt gevoelige gegevens in een beveiligde configuratie opslaan, die zijn versleuteld en alleen worden ontsleuteld in de virtuele machine. De beveiligde configuratie is handig wanneer de uitvoering-opdracht geheimen bevat zoals een wacht woord.

Deze items moeten worden behandeld als gevoelige gegevens en worden opgegeven in de configuratie van de instellingen voor beveiligde extensies. De beveiligde instellings gegevens voor de Azure VM-extensie zijn versleuteld en worden alleen ontsleuteld op de virtuele doel machine.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>Eigenschaps waarden

| Name | Waarde/voor beeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| publisher | Micro soft. OSTCExtensions | tekenreeks |
| type | CustomScriptForLinux | tekenreeks |
| typeHandlerVersion | 1.5 | int |
| fileUris (bijvoorbeeld) | `https://github.com/MyProject/Archive/MyPythonScript.py` | matrix |
| commandToExecute (bijvoorbeeld) | python-MyPythonScript.py \<my-param1\> | tekenreeks |
| enableInternalDNSCheck | waar | booleaans |
| storageAccountName (bijvoorbeeld) | examplestorageacct | tekenreeks |
| storageAccountKey (bijvoorbeeld) | TmJK/1N3AbAZ3q/+ hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg = = | tekenreeks |

### <a name="property-value-details"></a>Details van eigenschaps waarde

* `fileUris`: (optioneel, teken reeks matrix) de URI-lijst van de scripts
* `enableInternalDNSCheck`: (optioneel, BOOL) de standaard waarde is True, ingesteld op false om DNS-controle uit te scha kelen.
* `commandToExecute`: (optioneel, teken reeks) het entry point-script dat moet worden uitgevoerd
* `storageAccountName`: (optioneel, String) de naam van het opslag account
* `storageAccountKey`: (optioneel, String) de toegangs sleutel van het opslag account

De volgende waarden kunnen worden ingesteld in de open bare of beveiligde instellingen, maar u moet deze waarden niet in de open bare en beveiligde instellingen hebben ingesteld.

* `commandToExecute`

Het gebruik van open bare instellingen kan handig zijn voor het opsporen van fouten, maar het wordt ten zeerste aanbevolen om beveiligde instellingen te gebruiken.

Open bare instellingen worden in ongecodeerde tekst verzonden naar de virtuele machine waarop het script wordt uitgevoerd.  Beveiligde instellingen worden versleuteld met een sleutel die alleen bekend is bij Azure en de virtuele machine. De instellingen worden opgeslagen op de VM als ze zijn verzonden, d.w.z. als de instellingen zijn versleuteld, worden deze versleuteld opgeslagen op de VM. Het certificaat dat wordt gebruikt voor het ontsleutelen van de versleutelde waarden wordt opgeslagen op de virtuele machine en wordt gebruikt voor het ontsleutelen van instellingen (indien nodig) tijdens runtime.

## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager sjablonen. Het JSON-schema dat in de vorige sectie wordt beschreven, kan worden gebruikt in een Azure Resource Manager sjabloon voor het uitvoeren van de aangepaste script extensie tijdens het implementeren van een Azure Resource Manager sjabloon.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>Deze eigenschaps namen zijn hoofdletter gevoelig. Als u implementatie problemen wilt voor komen, gebruikt u de namen zoals hier wordt weer gegeven.

## <a name="azure-cli"></a>Azure CLI

Wanneer u Azure CLI gebruikt om de aangepaste script extensie uit te voeren, moet u een configuratie bestand of-bestanden maken. U moet mini maal ' commandToExecute ' hebben.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Desgewenst kunt u de instellingen in de opdracht opgeven als een JSON-indelings teken reeks. Hierdoor kan de configuratie worden opgegeven tijdens de uitvoering en zonder een afzonderlijk configuratie bestand.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI-voorbeelden

#### <a name="public-configuration-with-no-script-file"></a>Open bare configuratie zonder script bestand

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI-opdracht:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Open bare en beveiligde configuratie bestanden

U gebruikt een openbaar configuratie bestand om de URI van het script bestand op te geven. U gebruikt een beveiligd configuratie bestand om de opdracht op te geven die moet worden uitgevoerd.

Openbaar configuratie bestand:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Beveiligd configuratie bestand:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Azure CLI-opdracht:

```azurecli
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer de aangepaste script extensie wordt uitgevoerd, wordt het script gemaakt of gedownload in een directory die er ongeveer als volgt uitziet. De uitvoer van de opdracht wordt ook opgeslagen in deze map `stdout` en in `stderr` bestanden.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Controleer eerst het logboek van de Linux-agent om problemen op te lossen, Controleer of de uitbrei ding is uitgevoerd.

```bash
/var/log/waagent.log
```

U moet de uitvoering van de extensie controleren. deze ziet er ongeveer als volgt uit:

```output
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```

Enkele punten om te noteren:

1. Wanneer de opdracht wordt uitgevoerd, wordt ingeschakeld.
1. Down load is gekoppeld aan het downloaden van het CustomScript-extensie pakket van Azure, niet de script bestanden die zijn opgegeven in fileUris.
1. U kunt ook zien naar welk logboek bestand deze wordt geschreven `/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

Ga vervolgens als volgt te werk om het logboek bestand te controleren:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

U moet de afzonderlijke uitvoering controleren. deze ziet er ongeveer als volgt uit:

```output
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47
2018/04/26 15:29:47
```

Hier ziet u het volgende:

* De opdracht inschakelen starten is dit logboek
* De instellingen die zijn door gegeven aan de extensie
* De extensie die het bestand downloadt en het resultaat hiervan.
* De opdracht die wordt uitgevoerd en het resultaat.

U kunt ook de uitvoerings status van de aangepaste script extensie ophalen met behulp van Azure CLI:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

De uitvoer ziet er als volgt uit:

```output
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Volgende stappen

Zie [CustomScript extension opslag plaats](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)voor een overzicht van de code, de huidige problemen en versies.