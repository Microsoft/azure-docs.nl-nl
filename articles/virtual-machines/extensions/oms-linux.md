---
title: De virtuele-machine-extensie Log Analytics voor Linux
description: Implementeer de Log Analytics-agent op de virtuele Linux-machine met behulp van een extensie voor virtuele machines.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/18/2020
ms.openlocfilehash: 3ac6937d83bd2d21fefc09878408a54aa0eb41f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559066"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>De virtuele-machine-extensie Log Analytics voor Linux

## <a name="overview"></a>Overzicht

Azure Monitor Logboeken biedt bewakings-, waarschuwings-en waarschuwings functies voor het door voeren van waarschuwingen voor Cloud-en on-premises activa. De Log Analytics extensie van de virtuele machine voor Linux wordt gepubliceerd en ondersteund door micro soft. Met de uitbrei ding wordt de Log Analytics agent op virtuele machines van Azure geïnstalleerd en worden virtuele machines geregistreerd in een bestaande Log Analytics-werk ruimte. In dit document vindt u informatie over de ondersteunde platforms, configuraties en implementatie opties voor de Log Analytics extensie van de virtuele machine voor Linux.

>[!NOTE]
>Als onderdeel van de lopende overgang van Microsoft Operations Management Suite (OMS) naar Azure Monitor, wordt de OMS-agent voor Windows of Linux aangeduid als de Log Analytics agent voor Windows en Log Analytics agent voor Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

Raadpleeg het artikel [overzicht van Azure monitor agents](../../azure-monitor/agents/agents-overview.md#supported-operating-systems) voor meer informatie over de ondersteunde Linux-distributies.

### <a name="agent-and-vm-extension-version"></a>Versie van agent en VM-extensie
De volgende tabel bevat een overzicht van de versie van de Log Analytics VM-extensie en Log Analytics agent bundel voor elke release. Er wordt een koppeling naar de release opmerkingen voor de Log Analytics agent bundel versie opgenomen. Release opmerkingen bevatten informatie over fout oplossingen en nieuwe functies die beschikbaar zijn voor een bepaalde agent versie.  

| Versie van de Linux VM-extensie Log Analytics | Versie van Log Analytics agent bundel | 
|--------------------------------|--------------------------|
| 1.13.33 | [1.13.33](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.33-0) |
| 1.13.27 | [1.13.27](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.27-0) |
| 1.13.15 | [1.13.9-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.9-0) |
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0-9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center de Log Analytics agent automatisch inrichten en verbindt deze met een standaard Log Analytics werk ruimte die is gemaakt door ASC in uw Azure-abonnement. Als u Azure Security Center gebruikt, voert u de stappen in dit document niet uit. Als u dit doet, wordt de geconfigureerde werk ruimte overschreven en wordt de verbinding met Azure Security Center verbroken.

### <a name="internet-connectivity"></a>Internetconnectiviteit

De Log Analytics agent-extensie voor Linux vereist dat de virtuele doel machine is verbonden met internet. 

## <a name="extension-schema"></a>Extensieschema

De volgende JSON toont het schema voor de uitbrei ding van de Log Analytics agent. De uitbrei ding vereist de werk ruimte-ID en de werkruimte sleutel van de doel Log Analytics werkruimte; deze waarden vindt u in de Azure Portal [log Analytics-werk ruimte](../../azure-monitor/vm/quick-collect-linux-computer.md#obtain-workspace-id-and-key) . Omdat de werkruimte sleutel moet worden behandeld als gevoelige gegevens, moet deze worden opgeslagen in een configuratie met een beveiligde instelling. De beveiligde instellings gegevens voor de Azure VM-extensie zijn versleuteld en worden alleen ontsleuteld op de virtuele doel machine. Houd er rekening mee dat **workspaceId** en **workspaceKey** hoofdletter gevoelig zijn.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>In het bovenstaande schema wordt ervan uitgegaan dat deze wordt geplaatst op het hoofd niveau van de sjabloon. Als u de virtuele machine in de sjabloon plaatst, `type` `name` moeten de eigenschappen en worden gewijzigd, zoals hieronder beschreven. [](#template-deployment)

### <a name="property-values"></a>Eigenschaps waarden

| Name | Waarde/voor beeld |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publisher | Micro soft. EnterpriseCloud. monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.13 |
| workspaceId (bijvoorbeeld) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (bijvoorbeeld) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI + rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ = = |


## <a name="template-deployment"></a>Sjabloonimplementatie

>[!NOTE]
>Bepaalde onderdelen van de VM-extensie Log Analytics worden ook verzonden in de [Diagnostische VM-extensie](./diagnostics-linux.md). Vanwege deze architectuur kunnen er conflicten optreden als beide uitbrei dingen worden geïnstantieerd in dezelfde ARM-sjabloon. Om deze runtime-conflicten te voor komen, gebruikt u de- [ `dependsOn` instructie](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) om te controleren of de uitbrei dingen opeenvolgend zijn geïnstalleerd. De uitbrei dingen kunnen in een van beide volg orde worden geïnstalleerd.

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager sjablonen. Sjablonen zijn ideaal bij het implementeren van een of meer virtuele machines waarvoor de configuratie na implementatie vereist is, zoals het onboarden van Azure Monitor-Logboeken. Een voor beeld van een resource manager-sjabloon met de extensie van de Log Analytics agent-VM vindt u in de [Galerie van Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)start. 

De JSON-configuratie voor een extensie van een virtuele machine kan worden genest in de resource van de virtuele machine of worden geplaatst op het hoofd niveau of op de hoogste niveaus van een JSON-sjabloon van Resource Manager. De plaatsing van de JSON-configuratie is van invloed op de waarde van de naam en het type van de resource. Zie voor meer informatie [naam en type voor onderliggende resources instellen](../../azure-resource-manager/templates/child-resource-name-type.md). 

In het volgende voor beeld wordt ervan uitgegaan dat de VM-extensie is genest in de resource van de virtuele machine. Bij het nesten van de extensie bron wordt de JSON in het `"resources": []` object van de virtuele machine geplaatst.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Bij het plaatsen van de JSON van de extensie in de hoofdmap van de sjabloon, bevat de resource naam een verwijzing naar de bovenliggende virtuele machine en het type weerspiegelt de geneste configuratie.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Implementatie van Azure CLI

De Azure CLI kan worden gebruikt voor het implementeren van de Log Analytics agent-VM-extensie op een bestaande virtuele machine. Vervang de waarde van *myWorkspaceKey* hieronder door de werkruimte sleutel en de *myWorkspaceId* -waarde met uw werk ruimte-id. Deze waarden zijn te vinden in uw Log Analytics-werk ruimte in de Azure Portal onder *Geavanceerde instellingen*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit het Azure Portal en met behulp van de Azure CLI. Als u de implementatie status van uitbrei dingen voor een bepaalde virtuele machine wilt bekijken, voert u de volgende opdracht uit met behulp van de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Uitvoer voor uitvoering van extensie wordt vastgelegd in het volgende bestand:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Fout codes en hun betekenis

| Foutcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 9 | De aanroep is voor tijdig ingeschakeld | [Werk de Azure Linux-agent](./update-linux-agent.md) bij naar de meest recente beschik bare versie. |
| 10 | De VM is al verbonden met een Log Analytics-werk ruimte | Als u de virtuele machine wilt verbinden met de werk ruimte die is opgegeven in het uitbreidings schema, stelt u stopOnMultipleConnections in op False in open bare instellingen of verwijdert u deze eigenschap. Deze VM wordt eenmaal gefactureerd voor elke werk ruimte waarmee deze is verbonden. |
| 11 | Ongeldige configuratie van de extensie | Volg de voor gaande voor beelden om alle eigenschaps waarden in te stellen die nodig zijn voor de implementatie. |
| 17 | Installatie van Log Analytics-pakket is mislukt | 
| 19 | Installatie fout OMI-pakket | 
| 20 | Installatie fout van het SCX-pakket |
| 51 | Deze extensie wordt niet ondersteund op het besturings systeem van de virtuele machine | |
| 52 | Deze uitbrei ding is mislukt vanwege een ontbrekende afhankelijkheid | Controleer de uitvoer en logboeken voor meer informatie over welke afhankelijkheden ontbreken. |
| 53 | Deze uitbrei ding is mislukt vanwege ontbrekende of onjuiste configuratie parameters | Controleer de uitvoer en logboeken voor meer informatie over wat er mis ging. Controleer ook de juistheid van de werk ruimte-ID en controleer of de machine is verbonden met internet. |
| 55 | Kan geen verbinding maken met de Azure Monitor-service of de vereiste pakketten ontbreken of met dpkg Package Manager is vergrendeld| Controleer of het systeem toegang heeft tot internet of dat er een geldige HTTP-proxy is ingesteld. Controleer bovendien de juistheid van de werk ruimte-ID en controleer of krul-en tar-hulpprogram ma's zijn geïnstalleerd. |

Meer informatie over het oplossen van problemen vindt u in de [log Analytics-agent-for-Linux Troubleshooting Guide (Engelstalig](../../azure-monitor/visualize/vmext-troubleshoot.md)).

### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.
