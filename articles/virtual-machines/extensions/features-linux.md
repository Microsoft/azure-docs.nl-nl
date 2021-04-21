---
title: Azure VM-extensies en -functies voor Linux
description: Ontdek welke extensies beschikbaar zijn voor virtuele Azure-machines in Linux, gegroepeerd op wat ze bieden of verbeteren.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 03/30/2018
ms.openlocfilehash: bdbbc4c421b83fd041c7d900fb0edd01c4d636e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785087"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Extensies en functies van virtuele machines voor Linux

Extensies van virtuele Azure-machines (VM's) zijn kleine toepassingen die configuratie na de implementatie en automatiseringstaken voor Azure-VM's bieden. Als op een virtuele machine bijvoorbeeld software of antivirusbeveiliging moet worden geïnstalleerd of een script moet worden uitgevoerd, kan hiervoor een VM-extensie worden gebruikt. Azure VM-extensies kunnen worden uitgevoerd met de Azure CLI, PowerShell, Azure Resource Manager-sjablonen en de Azure-portal. Extensies kunnen worden gebundeld met een nieuwe VM-implementatie of worden uitgevoerd op een bestaand systeem.

Dit artikel bevat een overzicht van VM-extensies, vereisten voor het gebruik van Azure VM-extensies en richtlijnen voor het detecteren, beheren en verwijderen van VM-extensies. Dit artikel bevat algemene informatie omdat er veel VM-extensies beschikbaar zijn, elk met een mogelijk unieke configuratie. Extensiespecifieke details vindt u in elk document dat specifiek is voor de afzonderlijke extensie.

## <a name="use-cases-and-samples"></a>Gebruiksvoorbeelden en voorbeelden

Er zijn verschillende Azure VM-extensies beschikbaar, elk met een specifieke use-case. Voorbeelden zijn:

- PowerShell Desired State-configuraties toepassen op een VM met de DSC-extensie voor Linux. Zie Azure [Desired State configuration extension (Azure Desired State-configuratie-extensie) voor meer informatie.](https://github.com/Azure/azure-linux-extensions/tree/master/DSC)
- Configureer de bewaking van een VM met de Microsoft Monitoring Agent VM-extensie. Zie How [to monitor a Linux VM (Een linux-VM bewaken) voor meer informatie.](/previous-versions/azure/virtual-machines/linux/tutorial-monitor)
- Configureer de bewaking van uw Azure-infrastructuur met de extensie Chef of Datadog. Zie de [Chef-documenten](https://docs.chef.io/azure_portal.html) of [datadog-blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)voor meer informatie.

Naast processpecifieke extensies is er een aangepaste scriptextensie beschikbaar voor virtuele Windows- en Linux-machines. Met de aangepaste scriptextensie voor Linux kan elk Bash-script worden uitgevoerd op een VM. Aangepaste scripts zijn handig voor het ontwerpen van Azure-implementaties waarvoor configuratie is vereist, naast wat systeemeigen Azure-hulpprogramma's kunnen bieden. Zie Aangepaste scriptextensie [voor Linux-VM voor meer informatie.](custom-script-linux.md)

## <a name="prerequisites"></a>Vereisten

Als u de extensie op de VM wilt verwerken, moet de Azure Linux-agent zijn geïnstalleerd. Sommige afzonderlijke extensies hebben vereisten, zoals toegang tot resources of afhankelijkheden.

### <a name="azure-vm-agent"></a>Azure VM-agent

De Azure VM-agent beheert interacties tussen een Azure-VM en de Azure-fabriccontroller. De VM-agent is verantwoordelijk voor veel functionele aspecten van het implementeren en beheren van Azure-VM's, waaronder het uitvoeren van VM-extensies. De Azure VM-agent is vooraf geïnstalleerd op Azure Marketplace en kan handmatig worden geïnstalleerd op ondersteunde besturingssystemen. De Azure VM-agent voor Linux staat bekend als de Linux-agent.

Zie Azure Virtual Machine Agent voor meer informatie over ondersteunde besturingssystemen en [installatie-instructies.](agent-linux.md)

#### <a name="supported-agent-versions"></a>Ondersteunde agentversies

Om de best mogelijke ervaring te bieden, zijn er minimale versies van de agent. Raadpleeg [dit artikel](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) voor meer informatie.

#### <a name="supported-oses"></a>Ondersteunde BESe's

De Linux-agent wordt uitgevoerd op meerdere besturingssysteem, maar het uitbreidingskader heeft een limiet voor de besturingssysteembesturingssysteemextensies. Raadpleeg [dit artikel](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
) voor meer informatie.

Sommige extensies worden niet in alle besturingssystemen ondersteund en kunnen *foutcode 51, 'Niet-ondersteund besturingssysteem' worden weergegeven.* Raadpleeg de documentatie voor afzonderlijke extensies voor ondersteuning.

#### <a name="network-access"></a>Netwerktoegang

Extensiepakketten worden gedownload uit de opslagplaats Azure Storage extensie en uploads naar de extensiestatus worden naar Azure Storage. Als u [](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) een ondersteunde versie van de agents gebruikt, hoeft u geen toegang tot Azure Storage toe te staan in de VM-regio. U kunt de agent ook gebruiken om de communicatie om te leiden naar de Azure-fabriccontroller voor agentcommunicatie. Als u een niet-ondersteunde versie van de agent gebruikt, moet u uitgaande toegang tot Azure Storage in die regio toestaan vanaf de VM.

> [!IMPORTANT]
> Als u de toegang tot *168.63.129.16* hebt geblokkeerd met behulp van de gastfirewall, mislukken extensies ongeacht het bovenstaande.

Agents kunnen alleen worden gebruikt om extensiepakketten te downloaden en de rapportagestatus te rapporteren. Als een extensie-installatie bijvoorbeeld een script moet downloaden van GitHub (aangepast script) of toegang nodig heeft tot Azure Storage (Azure Backup), moeten er extra firewall-/netwerkbeveiligingsgroeppoorten worden geopend. Verschillende extensies hebben verschillende vereisten, omdat het toepassingen zijn die op zichzelf staan. Voor extensies waarvoor toegang tot Azure Storage is vereist, kunt u toegang toestaan met behulp van Azure NSG-servicetags voor [opslag.](../../virtual-network/network-security-groups-overview.md#service-tags)

Voor het omleiden van aanvragen voor agentverkeer heeft de Linux-agent proxyserverondersteuning. Deze proxyserverondersteuning is echter niet van toepassing op extensies. U moet elke afzonderlijke extensie configureren om met een proxy te werken.

## <a name="discover-vm-extensions"></a>VM-extensies ontdekken

Er zijn veel verschillende VM-extensies beschikbaar voor gebruik met Azure-VM's. Gebruik [az vm extension image list](/cli/azure/vm/extension/image#az_vm_extension_image_list)om een volledige lijst weer te geven. In het volgende voorbeeld worden alle beschikbare extensies op de *locatie westus* vermeld:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>VM-extensies uitvoeren

Azure VM-extensies worden uitgevoerd op bestaande VM's. Dit is handig wanneer u configuratiewijzigingen moet aanbrengen of connectiviteit wilt herstellen op een reeds geïmplementeerde VM. VM-extensies kunnen ook worden gebundeld met Azure Resource Manager sjabloonimplementaties. Door extensies met Resource Manager te gebruiken, kunnen azure-VM's worden geïmplementeerd en geconfigureerd zonder tussenkomst na de implementatie.

De volgende methoden kunnen worden gebruikt om een extensie uit te voeren op een bestaande VM.

### <a name="azure-cli"></a>Azure CLI

Azure VM-extensies kunnen worden uitgevoerd op een bestaande VM met de [opdracht az vm extension set.](/cli/azure/vm/extension#az_vm_extension_set) In het volgende voorbeeld wordt de aangepaste scriptextensie uitgevoerd op een VM met de *naam myVM* in een resourcegroep met de *naam myResourceGroup.* Vervang de naam van de voorbeeldresourcegroep, de VM-naam en het uit te voeren script (https: \/ /raw.githubusercontent.com/me/project/hello.sh) door uw eigen gegevens. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Wanneer de extensie correct wordt uitgevoerd, is de uitvoer vergelijkbaar met het volgende voorbeeld:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

VM-extensies kunnen worden toegepast op een bestaande VM via de Azure Portal. Selecteer de VM in de portal, kies **Extensies** en selecteer **vervolgens Toevoegen.** Kies de extensie die u wilt gebruiken in de lijst met beschikbare extensies en volg de instructies in de wizard.

In de volgende afbeelding ziet u de installatie van de aangepaste Linux-scriptextensie van de Azure Portal:

![Aangepaste scriptextensie installeren](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

VM-extensies kunnen worden toegevoegd aan Azure Resource Manager sjabloon en worden uitgevoerd met de implementatie van de sjabloon. Wanneer u een extensie met een sjabloon implementeert, kunt u volledig geconfigureerde Azure-implementaties maken. De volgende JSON is bijvoorbeeld afkomstig uit een Resource Manager-sjabloon die een set VM's met load balanced implementeert en Azure SQL Database, en vervolgens een .NET Core-toepassing op elke VM installeert. De VM-extensie zorgt voor de software-installatie.

Zie de volledige sjabloon voor Resource Manager [informatie.](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Zie Voor meer informatie over het maken Resource Manager sjablonen [Authoring Azure Resource Manager templates](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>VM-extensiegegevens beveiligen

Wanneer u een VM-extensie gebruikt, kan het nodig zijn gevoelige informatie op te nemen, zoals referenties, namen van opslagaccounts en toegangssleutels voor opslagaccounts. Veel VM-extensies bevatten een beveiligde configuratie die gegevens versleutelt en deze alleen ontsleutelt binnen de doel-VM. Elke extensie heeft een specifiek beveiligd configuratieschema en elke extensie wordt beschreven in extensiespecifieke documentatie.

In het volgende voorbeeld ziet u een exemplaar van de aangepaste scriptextensie voor Linux. De uit te voeren opdracht bevat een set referenties. In dit voorbeeld is de uit te voeren opdracht niet versleuteld:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Als u de **opdracht voor het uitvoeren van** de eigenschap naar de beveiligde configuratie **verplaatst,** wordt de uitvoeringsreeks beveiligd, zoals wordt weergegeven in het volgende voorbeeld:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

### <a name="how-do-agents-and-extensions-get-updated"></a>Hoe worden agents en extensies bijgewerkt?

De agents en extensies delen hetzelfde updatemechanisme. Voor sommige updates zijn geen aanvullende firewallregels vereist.

Wanneer een update beschikbaar is, wordt deze alleen op de VM geïnstalleerd wanneer er een wijziging in extensies is en andere wijzigingen in het VM-model, zoals:

- Gegevensschijven
- Uitbreidingen
- Diagnostische container voor opstarten
- Geheimen van gast-besturingssysteem
- VM-grootte
- Netwerkprofiel

Uitgevers maken updates op verschillende tijdstippen beschikbaar voor regio's, zodat het mogelijk is dat u VM's in verschillende regio's in verschillende versies kunt hebben.

#### <a name="agent-updates"></a>Agentupdates

De Linux-VM-agent bevat *code voor inrichtingsagent* en *code*  voor het verwerken van extensies in één pakket, die niet kunnen worden gescheiden. U kunt de *inrichtingsagent uitschakelen* wanneer u deze wilt inrichten in Azure met behulp van cloud-init. Zie [cloud-init gebruiken om dit te doen.](../linux/using-cloud-init.md)

Ondersteunde versies van agents kunnen automatische updates gebruiken. De enige code die kan worden bijgewerkt, is de *code voor de verwerking* van extensies, niet de inrichtingscode. De *code van de inrichtingsagent* is run-once-code.

De code voor de verwerking van *extensies* is verantwoordelijk voor de communicatie met de Azure-fabric en voor het verwerken van de bewerkingen voor VM-extensies, zoals installaties, rapportagestatus, het bijwerken van de afzonderlijke extensies en het verwijderen ervan. Updates bevatten beveiligingsfixes, bugfixes en verbeteringen in de *code voor de verwerking van extensies.*

Wanneer de agent is geïnstalleerd, wordt er een bovenliggende daemon gemaakt. Met dit bovenliggende proces wordt vervolgens een onderliggend proces gemaakt dat wordt gebruikt om extensies te verwerken. Als er een update beschikbaar is voor de agent, wordt deze gedownload, stopt het bovenliggende proces het onderliggende proces, werkt deze bij en start het vervolgens opnieuw op. Als er een probleem is met de update, wordt het bovenliggende proces terug naar de vorige onderliggende versie.

Het bovenliggende proces kan niet automatisch worden bijgewerkt. Het bovenliggende pakket kan alleen worden bijgewerkt door een distributiepakketupdate.

Als u wilt controleren welke versie u wilt uitvoeren, controleert u `waagent` de als volgt:

```bash
waagent --version
```

De uitvoer lijkt op die in het volgende voorbeeld:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.6.0
Goal state agent: 2.2.18
```

In de voorgaande voorbeelduitvoer is *WALinuxAgent-2.2.17* de bovenliggende of 'pakket geïmplementeerde versie'

De 'Doeltoestandagent' is de versie van automatische updates.

Het wordt ten zeerste aanbevolen dat u altijd automatische updates voor de agent hebt, [AutoUpdate.Enabled=y.](./update-linux-agent.md) Als u dit niet hebt ingeschakeld, moet u de agent handmatig blijven bijwerken en geen fout- en beveiligingsfixes krijgen.

#### <a name="extension-updates"></a>Extensie-updates

Wanneer een extensie-update beschikbaar is, downloadt de Linux-agent de extensie en werkt deze bij. Automatische extensie-updates zijn *secundair of* *hotfix.* U kunt zich bij het inrichten van de extensie in- of uit-kiezen voor *extensies* Kleine updates. In het volgende voorbeeld ziet u hoe u automatisch kleine versies in een Resource Manager sjabloon bij te werken met *autoUpgradeMinorVersion": true,'*:

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

Het wordt ten zeerste aanbevolen om altijd automatische updates te selecteren in uw extensie-implementaties om de meest recente oplossingen voor kleine release-fouten op te halen. Hotfix-updates die beveiligings- of sleutel bugfixes hebben, kunnen niet worden uitgekeerd.

### <a name="how-to-identify-extension-updates"></a>Extensie-updates identificeren

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Bepalen of de extensie is ingesteld met autoUpgradeMinorVersion op een VM

U kunt in het VM-model zien of de extensie is ingericht met 'autoUpgradeMinorVersion'. Als u dit wilt controleren, gebruikt u [az vm show](/cli/azure/vm#az_vm_show) en geeft u de resourcegroep en VM-naam als volgt op:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

In de volgende voorbeelduitvoer ziet *u dat autoUpgradeMinorVersion* is ingesteld op *true:*

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificeren wanneer een autoUpgradeMinorVersion heeft plaatsgevonden

Als u wilt zien wanneer er een update van de extensie is opgetreden, controleert u de agentlogboeken op de VM op */var/log/waagent.log.*

In het onderstaande voorbeeld is *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025* geïnstalleerd op de VM. Er was een hotfix beschikbaar *voor Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027:*

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>Agentmachtigingen

Als u de taken wilt uitvoeren, moet de agent worden uitgevoerd als *root.*

## <a name="troubleshoot-vm-extensions"></a>Problemen met VM-extensies oplossen

Elke VM-extensie kan stappen voor probleemoplossing hebben die specifiek zijn voor de extensie. Wanneer u bijvoorbeeld de aangepaste scriptextensie gebruikt, kunt u de details van de scriptuitvoering lokaal vinden op de VM waarop de extensie is uitgevoerd. Extensiespecifieke stappen voor probleemoplossing worden beschreven in extensiespecifieke documentatie.

De volgende stappen voor probleemoplossing zijn van toepassing op alle VM-extensies.

1. Als u het logboek van de Linux-agent wilt controleren, bekijkt u de activiteit wanneer de extensie werd ingericht in */var/log/waagent.log*

2. Controleer de werkelijke extensielogboeken voor meer informatie in */var/log/azure/ \<extensionName>*

3. Raadpleeg secties voor probleemoplossing voor extensiespecifieke documentatie voor foutcodes, bekende problemen, enzovoort.

3. Bekijk de systeemlogboeken. Controleer op andere bewerkingen die de extensie mogelijk hebben verstoren, zoals een langdurige installatie van een andere toepassing die exclusieve pakketbeheertoegang vereist.

### <a name="common-reasons-for-extension-failures"></a>Veelvoorkomende redenen voor extensiefouten

1. Extensies moeten 20 minuten worden uitgevoerd (uitzonderingen zijn de CustomScript-extensies, Chef en DSC die 90 minuten bevatten). Als uw implementatie deze tijd overschrijdt, wordt deze gemarkeerd als een time-out. De oorzaak hiervoor kan zijn dat er weinig resource-VM's zijn, andere VM-configuraties/opstarttaken die grote hoeveelheden resources verbruiken terwijl de extensie bezig is met het inrichten.

2. Er is niet voldaan aan de minimale vereisten. Sommige extensies zijn afhankelijk van VM-SKU's, zoals HPC-afbeeldingen. Extensies vereisen mogelijk bepaalde netwerktoegangsvereisten, zoals communicatie met Azure Storage of openbare services. Andere voorbeelden zijn toegang tot pakket-opslagplaatsen, te weinig schijfruimte of beveiligingsbeperkingen.

3. Exclusieve toegang tot pakketbeheer. In sommige gevallen kan een langdurige VM-configuratie en extensie-installatie conflicteren, waarbij beide exclusieve toegang tot pakketbeheer nodig hebben.

### <a name="view-extension-status"></a>Extensiestatus weergeven

Nadat een VM-extensie is uitgevoerd op een VM, gebruikt u [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view) om de extensiestatus als volgt te retourneren:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

De uitvoer is vergelijkbaar met de volgende voorbeelduitvoer:

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

De uitvoeringsstatus van de extensie vindt u ook in de Azure Portal. Als u de status van een extensie wilt weergeven, selecteert u de VM, kiest u **Extensies** en selecteert u vervolgens de gewenste extensie.

### <a name="rerun-a-vm-extension"></a>Een VM-extensie opnieuw gebruiken

Er kunnen gevallen zijn waarin een VM-extensie opnieuw moet worden gebruikt. U kunt een extensie opnieuw uitvoeren door deze te verwijderen en vervolgens de extensie opnieuw uit te voeren met een uitvoeringsmethode van uw keuze. Als u een extensie wilt verwijderen, gebruikt [u az vm extension delete](/cli/azure/vm/extension#az_vm_extension_delete) als volgt:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

U kunt een extensie in de Azure Portal als volgt verwijderen:

1. Selecteer een VM.
2. Kies **Extensies.**
3. Selecteer de gewenste extensie.
4. Kies **Verwijderen.**

## <a name="common-vm-extension-reference"></a>Algemene naslag voor VM-extensies

| Extensienaam | Description | Meer informatie |
| --- | --- | --- |
| Aangepaste scriptextensie voor Linux |Scripts uitvoeren op een virtuele Azure-machine |[Aangepaste scriptextensie voor Linux](custom-script-linux.md) |
| VM-extensie voor toegang |Weer toegang krijgen tot een virtuele Azure-machine |[VM-extensie voor toegang](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics-extensie |Beheer Azure Diagnostics |[Azure Diagnostics-extensie](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM Access-extensie |Gebruikers en referenties beheren |[VM-toegangsextensie voor Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Volgende stappen

Zie Overzicht van extensies en functies van virtuele Azure-machines voor meer informatie over [VM-extensies.](overview.md)