---
title: Azure VM-extensies en-functies voor Windows
description: Meer informatie over de uitbrei dingen die beschikbaar zijn voor virtuele machines van Azure, gegroepeerd op wat ze bieden of verbeteren.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/30/2018
ms.openlocfilehash: eb1b05afbaa57fb86033c568142da32443adc1f9
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559775"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Extensies en functies van virtuele machines voor Windows

Extensies van virtuele Azure-machines (VM's) zijn kleine toepassingen die configuratie na de implementatie en automatiseringstaken voor Azure-VM's bieden. Als op een virtuele machine bijvoorbeeld software of antivirusbeveiliging moet worden geïnstalleerd of een script moet worden uitgevoerd, kan hiervoor een VM-extensie worden gebruikt. Azure VM-extensies kunnen worden uitgevoerd met de Azure CLI, PowerShell, Azure Resource Manager-sjablonen en de Azure-portal. Extensies kunnen worden gebundeld met een nieuwe VM-implementatie of worden uitgevoerd op een bestaand systeem.

Dit artikel bevat een overzicht van VM-extensies, vereisten voor het gebruik van Azure VM-extensies en richt lijnen voor het detecteren, beheren en verwijderen van VM-extensies. Dit artikel bevat algemene informatie omdat er veel VM-uitbrei dingen beschikbaar zijn, elk met een mogelijk unieke configuratie. Details van een specifieke extensie vindt u in elk document dat specifiek is voor de afzonderlijke extensie.

 

## <a name="use-cases-and-samples"></a>Gebruiks voorbeelden en voor beelden

Er zijn verschillende verschillende Azure VM-extensies beschikbaar, elk met een specifieke use-case. Voorbeelden zijn:

- Pas Power shell desired state configurations toe aan een virtuele machine met de DSC-extensie voor Windows. Zie [Azure desired state Configuration extension](dsc-overview.md)(Engelstalig) voor meer informatie.
- De bewaking van een virtuele machine configureren met de VM-extensie Log Analytics agent. Zie [Azure Vm's verbinden met Azure monitor-logboeken](../../azure-monitor/vm/quick-collect-azurevm.md)voor meer informatie.
- Configureer een virtuele Azure-machine met behulp van chef. Zie [Azure VM-implementatie automatiseren met chef](/azure/developer/chef/windows-vm-configure)voor meer informatie.
- Configureer de bewaking van uw Azure-infra structuur met de Datadog-extensie. Zie de [Datadog-blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)voor meer informatie.


Naast verwerkings-specifieke uitbrei dingen is een aangepaste script extensie beschikbaar voor virtuele Windows-en Linux-machines. Met de aangepaste script extensie voor Windows kan elk Power shell-script worden uitgevoerd op een virtuele machine. Aangepaste scripts zijn handig voor het ontwerpen van Azure-implementaties waarvoor configuratie moet worden opgegeven dan wat er door het systeem eigen Azure-hulp programma kan worden geboden. Zie [aangepaste script extensie voor Windows VM](custom-script-windows.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u de uitbrei ding op de virtuele machine wilt afhandelen, moet de Azure Windows-agent zijn geïnstalleerd. Sommige individuele extensies hebben vereisten, zoals toegang tot resources of afhankelijkheden.

### <a name="azure-vm-agent"></a>Azure VM-agent

De Azure VM-agent beheert de interacties tussen een Azure-VM en de Azure Fabric-controller. De VM-agent is verantwoordelijk voor veel functionele aspecten van het implementeren en beheren van virtuele Azure-machines, waaronder het uitvoeren van VM-extensies. De Azure VM-agent is vooraf geïnstalleerd op installatie kopieën van Azure Marketplace en kan hand matig worden geïnstalleerd op ondersteunde besturings systemen. De Azure VM-agent voor Windows wordt ook wel de Windows-gast agent genoemd.

Zie [Azure virtual machine agent](agent-windows.md)voor meer informatie over ondersteunde besturings systemen en installatie-instructies.

#### <a name="supported-agent-versions"></a>Ondersteunde agent versies

Er zijn minimale versies van de agent om de best mogelijke ervaring te bieden. Raadpleeg [dit artikel](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) voor meer informatie.

#### <a name="supported-oses"></a>Ondersteunde besturings systemen

De Windows-gast agent wordt uitgevoerd op meerdere besturings systemen, maar het Framework Extensions heeft een limiet voor de besturings systemen die uitbrei dingen. Raadpleeg [dit artikel](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
) voor meer informatie.

Sommige extensies worden niet ondersteund in alle besturings systemen en kunnen *Fout Code 51, ' niet-ondersteund besturings systeem ',* verzenden. Raadpleeg de documentatie van de afzonderlijke extensie voor ondersteuning.

#### <a name="network-access"></a>Netwerktoegang

Uitbreidings pakketten worden gedownload uit de opslag plaats van de Azure Storage extensie en uploads van uitbreidings status worden naar Azure Storage gepost. Als u een [ondersteunde](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) versie van de agents gebruikt, hoeft u geen toegang tot Azure Storage toe te staan in de VM-regio, zoals de agent kan gebruiken om de communicatie te omleiden naar de Azure Fabric-controller voor agent communicatie (HostGAPlugin-functie via het geprivilegieerde kanaal op particuliere IP- [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)). Als u een niet-ondersteunde versie van de agent hebt, moet u uitgaande toegang tot Azure Storage in die regio vanuit de VM toestaan.

> [!IMPORTANT]
> Als u de toegang tot *168.63.129.16* hebt geblokkeerd met behulp van de gast firewall of met een proxy, mislukt de extensies, ongeacht het bovenstaande. Poorten 80, 443 en 32526 zijn vereist.

Agents kunnen alleen worden gebruikt voor het downloaden van uitbreidings pakketten en rapportage status. Als een extensie bijvoorbeeld een script moet downloaden van GitHub (aangepast script) of toegang moet hebben tot Azure Storage (Azure Backup), moeten er extra firewall/netwerk beveiligings groep poorten worden geopend. Verschillende uitbrei dingen hebben verschillende vereisten, omdat ze toepassingen in hun eigen recht zijn. Voor uitbrei dingen waarvoor toegang tot Azure Storage of Azure Active Directory vereist is, kunt u toegang tot opslag of AzureActiveDirectory via de [Azure NSG-service Tags](../../virtual-network/network-security-groups-overview.md#service-tags) toestaan.

De Windows-gast agent heeft geen proxyserver ondersteuning voor u om aanvragen voor agent verkeer om te leiden via. Dit betekent dat de Windows-gast agent afhankelijk is van uw aangepaste proxy (als u er een hebt) om toegang te krijgen tot bronnen op internet of op de host via IP-168.63.129.16.

## <a name="discover-vm-extensions"></a>VM-extensies detecteren

Er zijn veel verschillende VM-extensies beschikbaar voor gebruik met Azure-VM's. Als u een volledige lijst wilt weer geven, gebruikt u [Get-AzVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage). In het volgende voor beeld worden alle beschik bare uitbrei dingen in de *westelijke* locatie weer gegeven:

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>VM-extensies uitvoeren

Azure VM-extensies worden uitgevoerd op bestaande Vm's. Dit is handig wanneer u configuratie wijzigingen moet aanbrengen of de connectiviteit op een al geïmplementeerde virtuele machine wilt herstellen. VM-extensies kunnen ook worden gebundeld met Azure Resource Manager sjabloon implementaties. Met behulp van extensies van Resource Manager-sjablonen kunnen virtuele Azure-machines worden geïmplementeerd en geconfigureerd zonder interventie na de implementatie.

U kunt de volgende methoden gebruiken om een uitbrei ding uit te voeren op basis van een bestaande virtuele machine.

### <a name="powershell"></a>PowerShell

Er bestaan verschillende Power shell-opdrachten voor het uitvoeren van afzonderlijke uitbrei dingen. Als u een lijst wilt weer geven, gebruikt u [Get-opdracht](/powershell/module/microsoft.powershell.core/get-command) en filter bij *extensie*:

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

Dit geeft een uitvoer die er ongeveer als volgt uitziet:

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

In het volgende voor beeld wordt de aangepaste script extensie gebruikt om een script van een GitHub-opslag plaats naar de virtuele doel machine te downloaden en vervolgens het script uit te voeren. Zie [overzicht van aangepaste script extensies](custom-script-windows.md)voor meer informatie over de aangepaste script extensie.

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

In het volgende voor beeld wordt de extensie VM-toegang gebruikt om het beheerders wachtwoord van een Windows-virtuele machine opnieuw in te stellen op een tijdelijk wacht woord. Zie [extern bureaublad-service opnieuw instellen in een Windows-VM](../troubleshooting/reset-rdp.md)voor meer informatie over de extensie voor VM-toegang. Wanneer u dit hebt gedaan, moet u het wacht woord bij de eerste aanmelding opnieuw instellen:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

De `Set-AzVMExtension` opdracht kan worden gebruikt om een VM-extensie te starten. Zie de [Naslag Gids set-AzVMExtension](/powershell/module/az.compute/set-azvmextension)voor meer informatie.


### <a name="azure-portal"></a>Azure Portal

VM-extensies kunnen worden toegepast op een bestaande virtuele machine via de Azure Portal. Selecteer de virtuele machine in de portal, kies **uitbrei dingen** en selecteer vervolgens **toevoegen**. Kies de gewenste uitbrei ding in de lijst met beschik bare uitbrei dingen en volg de instructies in de wizard.

In het volgende voor beeld ziet u de installatie van de micro soft-antimalware-extensie van de Azure Portal:

![Antimalware-extensie installeren](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

VM-extensies kunnen worden toegevoegd aan een Azure Resource Manager sjabloon en worden uitgevoerd met de implementatie van de sjabloon. Wanneer u een uitbrei ding met een sjabloon implementeert, kunt u volledig geconfigureerde Azure-implementaties maken. De volgende JSON wordt bijvoorbeeld overgenomen van een resource manager-sjabloon en implementeert een set taak verdeling Vm's en een Azure SQL Database en installeert vervolgens een .NET core-toepassing op elke virtuele machine. De VM-extensie zorgt voor de software-installatie.

Zie de [volledige Resource Manager-sjabloon](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)voor meer informatie.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
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
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Zie [Azure Resource Manager sjablonen ontwerpen met Windows VM-extensies](../windows/template-description.md#extensions)voor meer informatie over het maken van Resource Manager-sjablonen.

## <a name="secure-vm-extension-data"></a>VM-extensie gegevens beveiligen

Wanneer u een VM-extensie uitvoert, kan het nodig zijn om gevoelige informatie te bevatten, zoals referenties, opslag accountnamen en toegangs sleutels voor opslag accounts. Veel VM-extensies bevatten een beveiligde configuratie waarmee gegevens worden versleuteld en alleen worden ontsleuteld in de doel-VM. Elke uitbrei ding heeft een specifiek beveiligd configuratie schema, en elke extensie wordt gedetailleerd beschreven in uitgebreide documentatie.

In het volgende voor beeld ziet u een exemplaar van de aangepaste script extensie voor Windows. De opdracht die moet worden uitgevoerd, bevat een set referenties. In dit voor beeld wordt de opdracht die moet worden uitgevoerd, niet versleuteld:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
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
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
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
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Op een Azure IaaS-VM die gebruikmaakt van extensies, ziet u in de console certificaten mogelijk certificaten met het onderwerp **_Windows Azure CRP Certificate Generator_**. Op een klassieke RDFE-VM hebben deze certificaten de object naam **_Windows Azure Service Management voor uitbrei dingen_**.

Deze certificaten beveiligen de communicatie tussen de virtuele machine en de host tijdens de overdracht van beveiligde instellingen (wachtwoord, andere aanmeldingsgegevens) die worden gebruikt door extensies. De certificaten worden gebouwd door de Azure-infrastructuurcontroller en doorgegeven aan de VM-agent. Als u de VM elke dag stopt en start, wordt er mogelijk een nieuw certificaat gemaakt door de infrastructuurcontroller. Het certificaat wordt opgeslagen in het persoonlijke certificaatarchief van de computer. Deze certificaten kunnen worden verwijderd. De VM-agent maakt de certificaten zo nodig opnieuw.

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

#### <a name="listing-extensions-deployed-to-a-vm"></a>Uitbrei dingen weer geven die zijn geïmplementeerd op een VM

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Agent updates

De Windows-gast agent bevat alleen *code voor extensie verwerking*. de *Windows-inrichtings code* is afzonderlijk. U kunt de Windows-gast agent verwijderen. Het is niet mogelijk om de automatische update van het venster gast agent uit te scha kelen.

De *verwerkings code voor uitbrei dingen* is verantwoordelijk voor de communicatie met de Azure-infra structuur en het verwerken van de VM-extensie bewerkingen, zoals installaties, rapportage status, het bijwerken van de afzonderlijke uitbrei dingen en het verwijderen ervan. Updates bevatten beveiligingsfixes, oplossingen voor fouten en verbeteringen in de *verwerkings code van de uitbrei ding*.

Zie [geïnstalleerde Windows-gast agent detecteren](agent-windows.md#detect-the-vm-agent)om te controleren welke versie wordt uitgevoerd.

#### <a name="extension-updates"></a>Extensie-updates

Wanneer een extensie-update beschikbaar is, wordt de uitbrei ding gedownload en bijgewerkt met de Windows-gast agent. Automatische extensie-updates zijn een *kleine* of *hotfix*. Wanneer u de uitbrei ding inricht, kunt u niet alleen *kleine* uitbrei dingen in-of uitschakelen. In het volgende voor beeld ziet u hoe u in een resource manager-sjabloon automatisch secundaire versies bijwerkt met *autoUpgradeMinorVersion ': True, '*:

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

Als u de laatste oplossingen voor problemen met een kleine release wilt ontvangen, kunt u het beste altijd automatisch bijwerken selecteren in uw extensie-implementaties. Hotfix-updates die beveiligings-of sleutel fout correcties bevatten, kunnen niet worden afgemeld.

### <a name="how-to-identify-extension-updates"></a>Uitbreidings updates identificeren

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identificeren of de uitbrei ding is ingesteld met autoUpgradeMinorVersion op een virtuele machine

U kunt vanuit het VM-model zien of de extensie is ingericht met ' autoUpgradeMinorVersion '. Als u wilt controleren, gebruikt u [Get-AzVm](/powershell/module/az.compute/get-azvm) en geeft u de naam van de resource groep en de VM op als volgt:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

In de volgende voorbeeld uitvoer ziet u dat *autoUpgradeMinorVersion* is ingesteld op *True*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificeren wanneer er een autoUpgradeMinorVersion is opgetreden

Als u wilt zien wanneer een update voor de uitbrei ding is opgetreden, raadpleegt u de logboeken van de agent op de virtuele machine op *C:\WindowsAzure\Logs\WaAppAgent.log*

In het volgende voor beeld had de VM *micro soft. compute. CustomScriptExtension 1,8* geïnstalleerd. Er is een hotfix beschikbaar voor versie *1,9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Agent machtigingen

De agent moet als *lokaal systeem* worden uitgevoerd om de taken uit te voeren.

## <a name="troubleshoot-vm-extensions"></a>Problemen met VM-extensies oplossen

Elke VM-extensie kan problemen oplossen die specifiek zijn voor de uitbrei ding. Wanneer u bijvoorbeeld de aangepaste script extensie gebruikt, kunnen Details over het uitvoeren van scripts lokaal worden gevonden op de virtuele machine waarop de uitbrei ding is uitgevoerd. Alle specifieke stappen voor het oplossen van problemen worden beschreven in uitgebreide documentatie.

De volgende stappen voor probleem oplossing zijn van toepassing op alle VM-extensies.

1. Als u het logboek van de Windows-gast agent wilt controleren, bekijkt u de activiteit wanneer uw extensie is ingericht in *C:\WindowsAzure\Logs\WaAppAgent.log*

2. Raadpleeg de logboeken van de daad werkelijke extensie voor meer informatie over *C:\WindowsAzure\Logs\Plugins \\ <extensionName>*

3. Raadpleeg de onderwerpen over probleem oplossing voor specifieke documentatie voor fout codes, bekende problemen, enzovoort.

4. Bekijk de systeem Logboeken. Controleer op andere bewerkingen die mogelijk zijn verstoord met de extensie, zoals een langlopende installatie van een andere toepassing die exclusieve toegang tot Package Manager vereist.

### <a name="common-reasons-for-extension-failures"></a>Veelvoorkomende redenen voor uitbrei ding van fouten

1. Uitbrei dingen zijn 20 minuten om uit te voeren (uitzonde ringen zijn de CustomScript-extensies, chef en DSC met 90 minuten). Als uw implementatie deze tijd overschrijdt, wordt deze als een time-out gemarkeerd. Dit kan worden veroorzaakt doordat er weinig bron-Vm's, andere VM-configuraties/opstart taken zijn die hoge aantallen bronnen verbruiken, terwijl de extensie probeert in te richten.

2. Er is niet voldaan aan de minimum vereisten. Sommige uitbrei dingen hebben afhankelijkheden van VM-Sku's, zoals HPC-installatie kopieën. Voor uitbrei dingen zijn mogelijk bepaalde netwerk toegangs vereisten vereist, zoals het communiceren met Azure Storage of open bare Services. Andere voor beelden zijn mogelijk toegang tot pakket opslagplaatsen, met onvoldoende schijf ruimte of beveiligings beperkingen.

3. Exclusieve toegang tot pakket beheer. In sommige gevallen kan het voor komen dat er een langlopende VM-configuratie en extensie-installatie conflicteert, waarbij beide exclusieve toegang tot de package manager nodig zijn.

### <a name="view-extension-status"></a>Uitbrei ding status weer geven

Nadat een VM-extensie is uitgevoerd op een virtuele machine, gebruikt u [Get-AzVM](/powershell/module/az.compute/get-azvm) om de extensie status te retour neren. *Substatussen [0]* toont aan dat de extensie is ingericht, wat betekent dat de implementatie is gelukt naar de virtuele machine, maar dat de uitvoering van de uitbrei ding in de virtuele machine is mislukt, *Substatussen [1]*.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

De uitvoer is vergelijkbaar met de volgende voorbeeld uitvoer:

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

De uitvoerings status van de extensie kan ook worden gevonden in de Azure Portal. Als u de status van een uitbrei ding wilt weer geven, selecteert u de virtuele machine, kiest u **uitbrei dingen** en selecteert u de gewenste uitbrei ding.

### <a name="rerun-vm-extensions"></a>VM-extensies opnieuw uitvoeren

Er zijn mogelijk situaties waarin een VM-extensie opnieuw moet worden uitgevoerd. U kunt een uitbrei ding opnieuw uitvoeren door deze te verwijderen en vervolgens de uitbrei ding opnieuw uit te voeren met een gewenste uitvoer methode. Als u een uitbrei ding wilt verwijderen, gebruikt u [Remove-AzVMExtension](/powershell/module/az.compute/remove-azvmextension) als volgt:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

U kunt een uitbrei ding ook als volgt verwijderen in de Azure Portal:

1. Selecteer een virtuele machine.
2. Kies **uitbrei dingen**.
3. Selecteer de gewenste uitbrei ding.
4. Kies **verwijderen**.

## <a name="common-vm-extensions-reference"></a>Naslag informatie over algemene VM-extensies
| Extensie naam | Beschrijving | Meer informatie |
| --- | --- | --- |
| Aangepaste scriptextensie voor Windows |Scripts uitvoeren op een virtuele Azure-machine |[Aangepaste scriptextensie voor Windows](custom-script-windows.md) |
| DSC-extensie voor Windows |Uitbrei ding Power shell DSC (desired state Configuration) |[DSC-extensie voor Windows](dsc-overview.md) |
| Azure Diagnostics-extensie |Azure Diagnostics beheren |[Azure Diagnostics extensie](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM-toegangs uitbreiding |Gebruikers en referenties beheren |[VM-toegangs extensie voor Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van virtuele machines en functies van Azure](overview.md)voor meer informatie over VM-uitbrei dingen.
