---
title: De virtuele-machine-extensie Log Analytics voor Windows
description: Implementeer de Log Analytics-agent op virtuele Windows-machines met behulp van een extensie van een virtuele machine.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 06/26/2020
ms.openlocfilehash: 7757bd765bcb02782b6199f71c4a6e460b7b8143
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559015"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>De virtuele-machine-extensie Log Analytics voor Windows

Azure Monitor Logboeken biedt bewakings mogelijkheden voor Cloud-en on-premises assets. De extensie van de virtuele machine van Log Analytics agent voor Windows wordt gepubliceerd en ondersteund door micro soft. Met de uitbrei ding wordt de Log Analytics agent op virtuele machines van Azure geïnstalleerd en worden virtuele machines geregistreerd in een bestaande Log Analytics-werk ruimte. In dit document vindt u informatie over de ondersteunde platforms, configuraties en implementatie opties voor de Log Analytics extensie van de virtuele machine voor Windows.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

Raadpleeg het artikel [overzicht van Azure monitor agents](../../azure-monitor/agents/agents-overview.md#supported-operating-systems) voor meer informatie over de ondersteunde Windows-besturings systemen.

### <a name="agent-and-vm-extension-version"></a>Versie van agent en VM-extensie
De volgende tabel bevat een overzicht van de versie van de Windows Log Analytics VM-extensie en Log Analytics agent bundel voor elke release. 

| Log Analytics bundel versie van Windows-agent | Windows VM-extensie versie Log Analytics | Releasedatum | Opmerkingen bij de release |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18053| 1.0.18053.0 | Oktober 2020   | <ul><li>Nieuwe agent probleem Oplosser</li><li>Updates voor het verwerken van certificaat wijzigingen in Azure-Services door de agent</li></ul> |
| 10.20.18040 | 1.0.18040.2 | Augustus 2020   | <ul><li>Hiermee wordt een probleem in azure Arc opgelost</li></ul> |
| 10.20.18038 | 1.0.18038 | April 2020   | <ul><li>Hiermee schakelt u connectiviteit via een persoonlijke koppeling met behulp van Azure Monitor Beveiligingsbereiken</li><li>Voegt opname beperking toe om een onverwachte, onbedoelde instroom te voor komen bij opname naar een werk ruimte</li><li>Voegt ondersteuning toe voor aanvullende Azure Government Clouds en regio's</li><li>Hiermee wordt een bug opgelost waarbij HealthService.exe vastgelopen</li></ul> |
| 10.20.18029 | 1.0.18029 | Maart 2020   | <ul><li>Hiermee wordt ondersteuning voor het ondertekenen van SHA-2-code toegevoegd</li><li>Verbetert de installatie en het beheer van de VM-extensie</li><li>Hiermee wordt een fout in azure Arc voor servers-integratie opgelost</li><li>Voegt een ingebouwd hulp programma voor probleem oplossing toe voor klant ondersteuning</li><li>Voegt ondersteuning toe voor aanvullende Azure Government regio's</li> |
| 10.20.18018 | 1.0.18018 | Oktober 2019 | <ul><li> Kleine oplossingen voor fouten en stabilisatie verbeteringen </li></ul> |
| 10.20.18011 | 1.0.18011 | Juli 2019 | <ul><li> Kleine oplossingen voor fouten en stabilisatie verbeteringen </li><li> Verhoogde MaxExpressionDepth tot 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Juni 2019 | <ul><li> Kleine oplossingen voor fouten en stabilisatie verbeteringen </li><li> De mogelijkheid om standaard referenties uit te scha kelen is toegevoegd bij het maken van een proxy verbinding (ondersteuning voor WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Maart 2019 | <ul><li>Problemen met kleine stabilisatie </li></ul> |
| 10.19.10006 | n.v.t. | Dec 2018 | <ul><li> Problemen met kleine stabilisatie </li></ul> | 
| 8.0.11136 | n.v.t. | Sept 2018 |  <ul><li> Er is ondersteuning toegevoegd voor het detecteren van een wijziging in de resource-ID bij verplaatsen van de VM </li><li> Er is ondersteuning toegevoegd voor de resource-ID voor rapportage bij gebruik van een installatie zonder extensie </li></ul>| 
| 8.0.11103 | n.v.t. |  April 2018 | |
| 8.0.11081 | 1.0.11081 | Nov 2017 | | 
| 8.0.11072 | 1.0.11072 | Sept 2017 | |
| 8.0.11049 | 1.0.11049 | Feb 2017 | |


### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center de Log Analytics agent automatisch te voorzien van een verbinding met de standaard Log Analytics-werk ruimte van het Azure-abonnement. Als u Azure Security Center gebruikt, voert u de stappen in dit document niet uit. Als u dit doet, wordt de geconfigureerde werk ruimte overschreven en wordt de verbinding met Azure Security Center verbroken.

### <a name="internet-connectivity"></a>Internetconnectiviteit
De Log Analytics agent-extensie voor Windows vereist dat de virtuele doel machine is verbonden met internet. 

## <a name="extension-schema"></a>Extensieschema

De volgende JSON toont het schema voor de uitbrei ding van de Log Analytics agent. De uitbrei ding vereist de werk ruimte-ID en de werkruimte sleutel van de doel Log Analytics werk ruimte. Deze kunt u vinden in de instellingen voor de werk ruimte in de Azure Portal. Omdat de werkruimte sleutel moet worden behandeld als gevoelige gegevens, moet deze worden opgeslagen in een configuratie met een beveiligde instelling. De beveiligde instellings gegevens voor de Azure VM-extensie zijn versleuteld en worden alleen ontsleuteld op de virtuele doel machine. Houd er rekening mee dat **workspaceId** en **workspaceKey** hoofdletter gevoelig zijn.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Eigenschaps waarden

| Name | Waarde/voor beeld |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Micro soft. EnterpriseCloud. monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (bijvoorbeeld) * | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (bijvoorbeeld) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI + rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ = = |

\* De workspaceId wordt de consumerId in de Log Analytics-API genoemd.

> [!NOTE]
> Zie Azure [Connect Windows computers to Azure monitor](../../azure-monitor/agents/agent-windows.md)voor extra eigenschappen.

## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager sjablonen. Het JSON-schema dat in de vorige sectie wordt beschreven, kan worden gebruikt in een Azure Resource Manager sjabloon om de Log Analytics agent-extensie uit te voeren tijdens de implementatie van een Azure Resource Manager-sjabloon. Een voorbeeld sjabloon met de extensie van de Log Analytics agent-VM vindt u in de [Galerie van Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)start. 

>[!NOTE]
>De sjabloon biedt geen ondersteuning voor het opgeven van meer dan één werk ruimte-ID en werkruimte sleutel wanneer u de agent wilt configureren om te rapporteren aan meerdere werk ruimten. Zie [een werk ruimte toevoegen of verwijderen](../../azure-monitor/agents/agent-manage.md#adding-or-removing-a-workspace)als u de agent wilt configureren om te rapporteren aan meerdere werk ruimten.  

De JSON voor een extensie van een virtuele machine kan worden genest in de resource van de virtuele machine of worden geplaatst op het hoofd niveau of op de hoogste niveaus van een JSON-sjabloon van Resource Manager. De plaatsing van de JSON is van invloed op de waarde van de naam en het type van de resource. Zie voor meer informatie [naam en type voor onderliggende resources instellen](../../azure-resource-manager/templates/child-resource-name-type.md). 

In het volgende voor beeld wordt ervan uitgegaan dat de extensie Log Analytics is genest in de resource van de virtuele machine. Bij het nesten van de extensie bron wordt de JSON in het `"resources": []` object van de virtuele machine geplaatst.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Bij het plaatsen van de JSON van de extensie in de hoofdmap van de sjabloon, bevat de resource naam een verwijzing naar de bovenliggende virtuele machine en het type weerspiegelt de geneste configuratie. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>PowerShell-implementatie

De `Set-AzVMExtension` opdracht kan worden gebruikt voor het implementeren van de extensie van de log Analytics agent virtuele machine op een bestaande virtuele machine. Voordat u de opdracht uitvoert, moeten de open bare en persoonlijke configuraties worden opgeslagen in een Power shell-Hash-tabel. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit het Azure Portal en met behulp van de module Azure PowerShell. Als u de implementatie status van extensies voor een bepaalde virtuele machine wilt bekijken, voert u de volgende opdracht uit met behulp van de module Azure PowerShell.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Uitvoer voor uitvoering van extensie wordt vastgelegd in bestanden die zijn gevonden in de volgende map:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.
