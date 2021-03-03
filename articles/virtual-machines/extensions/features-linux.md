---
title: Azure VM-extensies en-functies voor Linux
description: Meer informatie over de uitbrei dingen die beschikbaar zijn voor virtuele machines van Azure in Linux, gegroepeerd op wat ze bieden of verbeteren.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.openlocfilehash: 66687f5954894d962a67767669081de15a30c7c3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672616"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Extensies en functies van virtuele machines voor Linux

Extensies van virtuele Azure-machines (VM's) zijn kleine toepassingen die configuratie na de implementatie en automatiseringstaken voor Azure-VM's bieden. Als op een virtuele machine bijvoorbeeld software of antivirusbeveiliging moet worden geïnstalleerd of een script moet worden uitgevoerd, kan hiervoor een VM-extensie worden gebruikt. Azure VM-extensies kunnen worden uitgevoerd met de Azure CLI, PowerShell, Azure Resource Manager-sjablonen en de Azure-portal. Extensies kunnen worden gebundeld met een nieuwe VM-implementatie of worden uitgevoerd op een bestaand systeem.

Dit artikel bevat een overzicht van VM-extensies, vereisten voor het gebruik van Azure VM-extensies en richt lijnen voor het detecteren, beheren en verwijderen van VM-extensies. Dit artikel bevat algemene informatie omdat er veel VM-uitbrei dingen beschikbaar zijn, elk met een mogelijk unieke configuratie. Details van een specifieke extensie vindt u in elk document dat specifiek is voor de afzonderlijke extensie.

## <a name="use-cases-and-samples"></a>Gebruiks voorbeelden en voor beelden

Er zijn verschillende verschillende Azure VM-extensies beschikbaar, elk met een specifieke use-case. Voorbeelden zijn:

- Pas Power shell desired state configurations toe aan een virtuele machine met de DSC-extensie voor Linux. Zie [Azure desired state Configuration extension](https://github.com/Azure/azure-linux-extensions/tree/master/DSC)(Engelstalig) voor meer informatie.
- De bewaking van een virtuele machine configureren met de VM-extensie van micro soft monitoring agent. Zie [een virtuele Linux-machine controleren](/previous-versions/azure/virtual-machines/linux/tutorial-monitor)voor meer informatie.
- Configureer de bewaking van uw Azure-infra structuur met de extensie chef of Datadog. Zie de blog met chef- [documenten](https://docs.chef.io/azure_portal.html) of [Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)voor meer informatie.

Naast verwerkings-specifieke uitbrei dingen is een aangepaste script extensie beschikbaar voor virtuele Windows-en Linux-machines. Met de aangepaste script extensie voor Linux kan elk bash-script worden uitgevoerd op een virtuele machine. Aangepaste scripts zijn handig voor het ontwerpen van Azure-implementaties waarvoor configuratie moet worden opgegeven dan wat er door het systeem eigen Azure-hulp programma kan worden geboden. Zie [aangepaste script extensie voor Linux VM](custom-script-linux.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u de uitbrei ding op de virtuele machine wilt afhandelen, moet de Azure Linux-agent zijn geïnstalleerd. Sommige individuele extensies hebben vereisten, zoals toegang tot resources of afhankelijkheden.

### <a name="azure-vm-agent"></a>Azure VM-agent

De Azure VM-agent beheert de interacties tussen een Azure-VM en de Azure Fabric-controller. De VM-agent is verantwoordelijk voor veel functionele aspecten van het implementeren en beheren van virtuele Azure-machines, waaronder het uitvoeren van VM-extensies. De Azure VM-agent is vooraf geïnstalleerd op installatie kopieën van Azure Marketplace en kan hand matig worden geïnstalleerd op ondersteunde besturings systemen. De Azure VM-agent voor Linux wordt de Linux-agent genoemd.

Zie [Azure virtual machine agent](agent-linux.md)voor meer informatie over ondersteunde besturings systemen en installatie-instructies.

#### <a name="supported-agent-versions"></a>Ondersteunde agent versies

Er zijn minimale versies van de agent om de best mogelijke ervaring te bieden. Raadpleeg [dit artikel](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) voor meer informatie.

#### <a name="supported-oses"></a>Ondersteunde besturings systemen

De Linux-agent wordt uitgevoerd op meerdere besturings systemen, maar het Framework Extensions heeft een limiet voor de besturings systemen die uitbrei dingen. Raadpleeg [dit artikel](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
) voor meer informatie.

Sommige extensies worden niet ondersteund in alle besturings systemen en kunnen *Fout Code 51, ' niet-ondersteund besturings systeem ',* verzenden. Raadpleeg de documentatie van de afzonderlijke extensie voor ondersteuning.

#### <a name="network-access"></a>Netwerktoegang

Uitbreidings pakketten worden gedownload uit de opslag plaats van de Azure Storage extensie en uploads van uitbreidings status worden naar Azure Storage gepost. Als u een [ondersteunde](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) versie van de agents gebruikt, hoeft u geen toegang toe te staan tot Azure Storage in de VM-regio, zoals de agent kan gebruiken om de communicatie met de Azure Fabric-controller te omleiden voor agent communicatie. Als u een niet-ondersteunde versie van de agent hebt, moet u uitgaande toegang tot Azure Storage in die regio vanuit de VM toestaan.

> [!IMPORTANT]
> Als u de toegang tot *168.63.129.16* hebt geblokkeerd met behulp van de gast firewall, mislukken uitbrei dingen, ongeacht het bovenstaande.

Agents kunnen alleen worden gebruikt voor het downloaden van uitbreidings pakketten en rapportage status. Als een extensie bijvoorbeeld een script moet downloaden van GitHub (aangepast script) of toegang moet hebben tot Azure Storage (Azure Backup), moeten er extra firewall/netwerk beveiligings groep poorten worden geopend. Verschillende uitbrei dingen hebben verschillende vereisten, omdat ze toepassingen in hun eigen recht zijn. Voor uitbrei dingen waarvoor toegang tot Azure Storage is vereist, kunt u toegang toestaan via de Azure NSG-service tags voor [opslag](../../virtual-network/network-security-groups-overview.md#service-tags).

Voor het omleiden van agent verkeers aanvragen heeft de Linux-agent ondersteuning voor de proxy server. De ondersteuning voor deze proxy server is echter niet van toepassing op uitbrei dingen. U moet elke afzonderlijke extensie configureren voor gebruik met een proxy.

## <a name="discover-vm-extensions"></a>VM-extensies detecteren

Er zijn veel verschillende VM-extensies beschikbaar voor gebruik met Azure-VM's. Gebruik [AZ VM extension Image List](/cli/azure/vm/extension/image#az-vm-extension-image-list)voor een volledige lijst. In het volgende voor beeld worden alle beschik bare uitbrei dingen in de *westelijke* locatie weer gegeven:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>VM-extensies uitvoeren

Azure VM-extensies worden uitgevoerd op bestaande Vm's. Dit is handig wanneer u configuratie wijzigingen moet aanbrengen of de connectiviteit op een al geïmplementeerde virtuele machine wilt herstellen. VM-extensies kunnen ook worden gebundeld met Azure Resource Manager sjabloon implementaties. Met behulp van extensies van Resource Manager-sjablonen kunnen virtuele Azure-machines worden geïmplementeerd en geconfigureerd zonder interventie na de implementatie.

U kunt de volgende methoden gebruiken om een uitbrei ding uit te voeren op basis van een bestaande virtuele machine.

### <a name="azure-cli"></a>Azure CLI

Azure VM-extensies kunnen worden uitgevoerd op basis van een bestaande virtuele machine met de opdracht [AZ VM extension set](/cli/azure/vm/extension#az-vm-extension-set) . In het volgende voor beeld wordt de aangepaste script extensie uitgevoerd op een virtuele machine met de naam *myVM* in een resource groep met de naam *myResourceGroup*. Vervang de naam van de resource groep, de VM-naam en het script dat wordt uitgevoerd (https: \/ /RAW.githubusercontent.com/me/project/Hello.sh) door uw eigen gegevens. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Wanneer de uitbrei ding correct wordt uitgevoerd, is de uitvoer vergelijkbaar met het volgende voor beeld:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure Portal

VM-extensies kunnen worden toegepast op een bestaande virtuele machine via de Azure Portal. Selecteer de virtuele machine in de portal, kies **uitbrei dingen** en selecteer vervolgens **toevoegen**. Kies de gewenste uitbrei ding in de lijst met beschik bare uitbrei dingen en volg de instructies in de wizard.

In de volgende afbeelding ziet u de installatie van de aangepaste script extensie voor Linux van de Azure Portal:

![Aangepaste script extensie installeren](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

VM-extensies kunnen worden toegevoegd aan een Azure Resource Manager sjabloon en worden uitgevoerd met de implementatie van de sjabloon. Wanneer u een uitbrei ding met een sjabloon implementeert, kunt u volledig geconfigureerde Azure-implementaties maken. De volgende JSON wordt bijvoorbeeld overgenomen van een resource manager-sjabloon die een set taak verdeling Vm's en Azure SQL Database implementeert, en vervolgens een .NET core-toepassing installeert op elke virtuele machine. De VM-extensie zorgt voor de software-installatie.

Zie de volledige [Resource Manager-sjabloon](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)voor meer informatie.

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

Zie [Azure Resource Manager sjablonen ontwerpen](../windows/template-description.md#extensions)voor meer informatie over het maken van Resource Manager-sjablonen.

## <a name="secure-vm-extension-data"></a>VM-extensie gegevens beveiligen

Wanneer u een VM-extensie uitvoert, kan het nodig zijn om gevoelige informatie te bevatten, zoals referenties, opslag accountnamen en toegangs sleutels voor opslag accounts. Veel VM-extensies bevatten een beveiligde configuratie waarmee gegevens worden versleuteld en alleen worden ontsleuteld in de doel-VM. Elke uitbrei ding heeft een specifiek beveiligd configuratie schema, en elke extensie wordt gedetailleerd beschreven in uitgebreide documentatie.

In het volgende voor beeld ziet u een exemplaar van de aangepaste script extensie voor Linux. De opdracht die moet worden uitgevoerd, bevat een set referenties. In dit voor beeld wordt de opdracht die moet worden uitgevoerd, niet versleuteld:

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

Als u de opdracht verplaatst naar de eigenschap **Execute** naar de **beveiligde** configuratie, wordt de uitvoerings reeks beveiligd, zoals wordt weer gegeven in het volgende voor beeld:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Hoe worden agents en uitbrei dingen bijgewerkt?

De agents en uitbrei dingen delen hetzelfde update mechanisme. Voor sommige updates zijn geen aanvullende firewall regels vereist.

Wanneer er een update beschikbaar is, wordt deze alleen geïnstalleerd op de virtuele machine wanneer er een wijziging is aangebracht in extensies en andere VM-modellen, zoals:

- Gegevensschijven
- Uitbreidingen
- Container voor diagnostische gegevens over opstarten
- Geheimen voor gast besturingssystemen
- VM-grootte
- Netwerkprofiel

Uitgevers maken updates beschikbaar voor regio's op verschillende tijdstippen, zodat u virtuele machines in verschillende regio's op verschillende versies kunt hebben.

#### <a name="agent-updates"></a>Agent updates

De Linux-VM-agent bevat code voor de *inrichtings agent* en *uitbrei ding van extensie*  in één pakket, dat niet kan worden gescheiden. U kunt de *inrichtings agent* uitschakelen als u wilt inrichten op Azure met behulp van Cloud-init. Zie voor het [gebruik van Cloud-init](../linux/using-cloud-init.md).

Ondersteunde versies van de agents kunnen gebruikmaken van automatische updates. De enige code die kan worden bijgewerkt is de *verwerkings code van de uitbrei ding*, niet de inrichtings code. De *inrichtings agent code* wordt uitgevoerd als code.

De *verwerkings code voor uitbrei dingen* is verantwoordelijk voor de communicatie met de Azure-infra structuur en het verwerken van de VM-extensie bewerkingen, zoals installaties, rapportage status, het bijwerken van de afzonderlijke uitbrei dingen en het verwijderen ervan. Updates bevatten beveiligingsfixes, oplossingen voor fouten en verbeteringen in de *verwerkings code van de uitbrei ding*.

Wanneer de agent is geïnstalleerd, wordt er een bovenliggende daemon gemaakt. Deze bovenliggende sleutel voert een onderliggend proces uit dat wordt gebruikt voor het afhandelen van extensies. Als er een update beschikbaar is voor de agent, wordt deze gedownload, wordt het onderliggende proces door de bovenliggende computer gestopt, bijgewerkt en vervolgens opnieuw opgestart. Als er een probleem is met de update, wordt het bovenliggende proces teruggezet naar de vorige onderliggende versie.

Het bovenliggende proces kan niet automatisch worden bijgewerkt. Het bovenliggende item kan alleen worden bijgewerkt door een update van een distributie-pakket.

Als u wilt controleren welke versie u gebruikt, controleert u het `waagent` volgende:

```bash
waagent --version
```

De uitvoer lijkt op die in het volgende voorbeeld:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

In het voor gaande voor beeld is de bovenliggende of ' package Implemented version ' *WALinuxAgent-2.2.17*

De ' doel status agent ' is de automatische update versie.

Het wordt ten zeerste aanbevolen dat u altijd automatisch bijwerken hebt voor de agent, auto [Update. enabled = y](./update-linux-agent.md). Als u dit niet hebt ingeschakeld, moet u de agent hand matig bijwerken en worden er geen problemen met de fout en de beveiliging opgelost.

#### <a name="extension-updates"></a>Extensie-updates

Wanneer een extensie-update beschikbaar is, wordt de extensie door de Linux-agent gedownload en bijgewerkt. Automatische extensie-updates zijn een *kleine* of *hotfix*. Wanneer u de uitbrei ding inricht, kunt u niet alleen *kleine* uitbrei dingen in-of uitschakelen. In het volgende voor beeld ziet u hoe u in een resource manager-sjabloon automatisch secundaire versies bijwerkt met *autoUpgradeMinorVersion ': True, '*:

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

Als u de laatste oplossingen voor problemen met een kleine release wilt ontvangen, kunt u het beste altijd automatisch bijwerken selecteren in uw extensie-implementaties. Hotfix-updates die beveiligings-of sleutel fout correcties bevatten, kunnen niet worden afgemeld.

### <a name="how-to-identify-extension-updates"></a>Uitbreidings updates identificeren

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identificeren of de uitbrei ding is ingesteld met autoUpgradeMinorVersion op een virtuele machine

U kunt vanuit het VM-model zien of de extensie is ingericht met ' autoUpgradeMinorVersion '. Als u wilt controleren, gebruikt u [AZ VM show](/cli/azure/vm#az-vm-show) en geeft u de naam van de resource groep en de VM op als volgt:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

In de volgende voorbeeld uitvoer ziet u dat *autoUpgradeMinorVersion* is ingesteld op *True*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificeren wanneer er een autoUpgradeMinorVersion is opgetreden

Als u wilt zien wanneer een update voor de uitbrei ding is opgetreden, bekijkt u de logboeken van de agent op de virtuele machine op */var/log/waagent.log*.

In het onderstaande voor beeld heeft de VM *micro soft. OSTCExtensions. LinuxDiagnostic-2.3.9025* geïnstalleerd. Er is een hotfix beschikbaar voor *micro soft. OSTCExtensions. LinuxDiagnostic-2.3.9027*:

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

## <a name="agent-permissions"></a>Agent machtigingen

De agent moet worden uitgevoerd als *root* om de taken uit te voeren.

## <a name="troubleshoot-vm-extensions"></a>Problemen met VM-extensies oplossen

Elke VM-extensie kan problemen oplossen die specifiek zijn voor de uitbrei ding. Wanneer u bijvoorbeeld de aangepaste script extensie gebruikt, kunnen Details over het uitvoeren van scripts lokaal worden gevonden op de virtuele machine waarop de uitbrei ding is uitgevoerd. Alle specifieke stappen voor het oplossen van problemen worden beschreven in uitgebreide documentatie.

De volgende stappen voor probleem oplossing zijn van toepassing op alle VM-extensies.

1. Als u het logboek van de Linux-agent wilt controleren, bekijkt u de activiteit wanneer uw extensie is ingericht in */var/log/waagent.log*

2. Raadpleeg de logboeken van de daad werkelijke extensie voor meer informatie over */var/log/Azure/ \<extensionName>*

3. Raadpleeg de sectie extensie-specifieke documentatie voor het oplossen van problemen met fout codes, bekende problemen etc.

3. Bekijk de systeem Logboeken. Controleer op andere bewerkingen die mogelijk zijn verstoord met de extensie, zoals een langlopende installatie van een andere toepassing die exclusieve toegang tot Package Manager vereist.

### <a name="common-reasons-for-extension-failures"></a>Veelvoorkomende redenen voor uitbrei ding van fouten

1. Uitbrei dingen zijn 20 minuten om uit te voeren (uitzonde ringen zijn de CustomScript-extensies, chef en DSC met 90 minuten). Als uw implementatie deze tijd overschrijdt, wordt deze als een time-out gemarkeerd. Dit kan worden veroorzaakt doordat er weinig bron-Vm's, andere VM-configuraties/opstart taken zijn die hoge aantallen bronnen verbruiken, terwijl de extensie probeert in te richten.

2. Er is niet voldaan aan de minimum vereisten. Sommige uitbrei dingen hebben afhankelijkheden van VM-Sku's, zoals HPC-installatie kopieën. Voor uitbrei dingen zijn mogelijk bepaalde netwerk toegangs vereisten vereist, zoals het communiceren met Azure Storage of open bare Services. Andere voor beelden zijn mogelijk toegang tot pakket opslagplaatsen, met onvoldoende schijf ruimte of beveiligings beperkingen.

3. Exclusieve toegang tot pakket beheer. In sommige gevallen kan het voor komen dat er een langlopende VM-configuratie en extensie-installatie conflicteert, waarbij beide exclusieve toegang tot de package manager nodig zijn.

### <a name="view-extension-status"></a>Uitbrei ding status weer geven

Nadat een VM-extensie is uitgevoerd op een virtuele machine, gebruikt u [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view) om de status van de extensie als volgt te retour neren:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

De uitvoer is vergelijkbaar met de volgende voorbeeld uitvoer:

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

De uitvoerings status van de extensie kan ook worden gevonden in de Azure Portal. Als u de status van een uitbrei ding wilt weer geven, selecteert u de virtuele machine, kiest u **uitbrei dingen** en selecteert u de gewenste uitbrei ding.

### <a name="rerun-a-vm-extension"></a>Een VM-extensie opnieuw uitvoeren

Er zijn mogelijk situaties waarin een VM-extensie opnieuw moet worden uitgevoerd. U kunt een uitbrei ding opnieuw uitvoeren door deze te verwijderen en vervolgens de uitbrei ding opnieuw uit te voeren met een gewenste uitvoer methode. Als u een uitbrei ding wilt verwijderen, gebruikt u [AZ VM extension delete](/cli/azure/vm/extension#az-vm-extension-delete) als volgt:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

U kunt een uitbrei ding ook als volgt verwijderen in de Azure Portal:

1. Selecteer een virtuele machine.
2. Kies **uitbrei dingen**.
3. Selecteer de gewenste uitbrei ding.
4. Kies **verwijderen**.

## <a name="common-vm-extension-reference"></a>Naslag informatie over algemene VM-extensies

| Extensie naam | Beschrijving | Meer informatie |
| --- | --- | --- |
| Aangepaste script extensie voor Linux |Scripts uitvoeren op een virtuele Azure-machine |[Aangepaste script extensie voor Linux](custom-script-linux.md) |
| VM-extensie voor toegang |Toegang tot een virtuele Azure-machine herstellen |[VM-extensie voor toegang](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure Diagnostics-extensie |Azure Diagnostics beheren |[Azure Diagnostics-extensie](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM-toegangs uitbreiding |Gebruikers en referenties beheren |[VM-toegangs extensie voor Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van virtuele machines en functies van Azure](overview.md)voor meer informatie over VM-uitbrei dingen.