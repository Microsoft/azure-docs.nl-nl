---
title: Virtuele machines in een Azure Resource Manager-sjabloon | Microsoft Azure
description: Meer informatie over hoe de bron van de virtuele machine wordt gedefinieerd in een Azure Resource Manager sjabloon.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: 32b73be3faf6eedb92220725b292a3e69cf7f965
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555989"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Virtuele machines in een Azure Resource Manager-sjabloon

In dit artikel worden aspecten van een Azure Resource Manager sjabloon beschreven die van toepassing zijn op virtuele machines. In dit artikel wordt geen volledige sjabloon beschreven voor het maken van een virtuele machine. u hebt resource definities nodig voor opslag accounts, netwerk interfaces, open bare IP-adressen en virtuele netwerken. Zie het [overzicht van Resource Manager-sjablonen](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)voor meer informatie over hoe deze bronnen samen kunnen worden gedefinieerd.

Er zijn veel [sjablonen in de galerie](https://azure.microsoft.com/documentation/templates/?term=VM) die de VM-resource bevatten. Niet alle elementen die kunnen worden opgenomen in een sjabloon, worden hier beschreven.

 

In dit voor beeld ziet u een typische resource sectie van een sjabloon voor het maken van een opgegeven aantal Vm's:

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
        "adminUsername": "[parameters('adminUsername')]", 
        "adminPassword": "[parameters('adminPassword')]" 
      }, 
      "storageProfile": { 
        "imageReference": { 
          "publisher": "MicrosoftWindowsServer", 
          "offer": "WindowsServer", 
          "sku": "2012-R2-Datacenter", 
          "version": "latest" 
        }, 
        "osDisk": { 
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>In dit voor beeld wordt gebruikgemaakt van een opslag account dat eerder is gemaakt. U kunt het opslag account maken door het te implementeren vanuit de sjabloon. Het voor beeld is ook afhankelijk van een netwerk interface en de afhankelijke resources die in de sjabloon zouden worden gedefinieerd. Deze resources worden niet weer gegeven in het voor beeld.
>
>

## <a name="api-version"></a>API-versie

Wanneer u resources implementeert met behulp van een sjabloon, moet u een versie van de API opgeven die moet worden gebruikt. In het voor beeld wordt de resource van de virtuele machine weer gegeven met dit apiVersion-element:

```json
"apiVersion": "2016-04-30-preview",
```

De versie van de API die u in de sjabloon opgeeft, is van invloed op de eigenschappen die u in de sjabloon kunt definiëren. Over het algemeen moet u de meest recente API-versie selecteren bij het maken van sjablonen. Voor bestaande sjablonen kunt u bepalen of u wilt door gaan met het gebruik van een eerdere API-versie of de sjabloon voor de nieuwste versie bijwerken om te profiteren van nieuwe functies.

Gebruik deze mogelijkheden voor het ophalen van de nieuwste API-versies:

- REST API: [alle resource providers weer geven](/rest/api/resources/providers)
- Power shell- [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)
- Azure CLI- [AZ provider show](/cli/azure/provider)


## <a name="parameters-and-variables"></a>Para meters en variabelen

Met [para meters](../../azure-resource-manager/templates/template-syntax.md) kunt u eenvoudig waarden voor de sjabloon opgeven wanneer u deze uitvoert. Dit gedeelte para meters wordt gebruikt in het voor beeld:

```json
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Wanneer u de voorbeeld sjabloon implementeert, voert u waarden in voor de naam en het wacht woord van het beheerders account op elke virtuele machine en het aantal Vm's dat moet worden gemaakt. U hebt de optie om parameter waarden op te geven in een afzonderlijk bestand dat wordt beheerd met de sjabloon of waarmee waarden worden opgegeven wanneer u hierom wordt gevraagd.

Met [variabelen](../../azure-resource-manager/templates/template-syntax.md) kunt u eenvoudig waarden instellen in de sjabloon die in het hele spel worden gebruikt of die in de loop van de tijd kunnen worden gewijzigd. Deze variabelen sectie wordt in het voor beeld gebruikt:

```json
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

Wanneer u de voorbeeld sjabloon implementeert, worden variabelen waarden gebruikt voor de naam en id van het eerder gemaakte opslag account. Variabelen worden ook gebruikt om de instellingen voor de diagnostische uitbrei ding op te geven. Gebruik de [Aanbevolen procedures voor het maken van Azure Resource Manager sjablonen](../../azure-resource-manager/templates/template-best-practices.md) waarmee u kunt bepalen hoe u de para meters en variabelen in uw sjabloon wilt structureren.

## <a name="resource-loops"></a>Resource lussen

Als u meer dan één virtuele machine voor uw toepassing nodig hebt, kunt u een copy-element in een sjabloon gebruiken. Dit optionele element loopt door het maken van het aantal Vm's dat u hebt opgegeven als para meter:

```json
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

U ziet ook in het voor beeld dat de lus-index wordt gebruikt bij het opgeven van een aantal waarden voor de resource. Als u bijvoorbeeld een aantal exemplaren van drie hebt ingevoerd, zijn de namen van de besturingssysteem schijven myOSDisk1, myOSDisk2 en myOSDisk3:

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>In dit voor beeld worden beheerde schijven voor de virtuele machines gebruikt.
>
>

Als u een lus voor een resource in de sjabloon wilt maken, moet u mogelijk de lus gebruiken bij het maken of openen van andere resources. Meerdere Vm's kunnen bijvoorbeeld niet gebruikmaken van dezelfde netwerk interface, dus als uw sjabloon een lus heeft gemaakt door drie Vm's te maken, moet er ook een lus worden gemaakt met het maken van drie netwerk interfaces. Bij het toewijzen van een netwerk interface aan een virtuele machine wordt de lus-index gebruikt om deze te identificeren:

```json
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Afhankelijkheden

De meeste resources zijn afhankelijk van andere resources om goed te kunnen werken. Virtuele machines moeten worden gekoppeld aan een virtueel netwerk en hiervoor is een netwerk interface vereist. Het element [dependsOn](../../azure-resource-manager/templates/define-resource-dependency.md) wordt gebruikt om ervoor te zorgen dat de netwerk interface gereed is om te worden gebruikt voordat de virtuele machines worden gemaakt:

```json
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Resource Manager implementeert in parallelle resources die niet afhankelijk zijn van een andere resource die wordt geïmplementeerd. Wees voorzichtig bij het instellen van afhankelijkheden omdat u per ongeluk uw implementatie kunt vertragen door onnodige afhankelijkheden op te geven. Afhankelijkheden kunnen worden gekoppeld aan meerdere resources. De netwerk interface is bijvoorbeeld afhankelijk van het open bare IP-adres en de bronnen van het virtuele netwerk.

Hoe weet u of een afhankelijkheid vereist is? Bekijk de waarden die u in de sjabloon hebt ingesteld. Als een element in de resource definitie van de virtuele machine verwijst naar een andere resource die in dezelfde sjabloon is geïmplementeerd, hebt u een afhankelijkheid nodig. Uw voor beeld van een virtuele machine definieert bijvoorbeeld een netwerk profiel:

```json
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Als u deze eigenschap wilt instellen, moet de netwerk interface aanwezig zijn. Daarom moet u een afhankelijkheid hebben. U moet ook een afhankelijkheid instellen als er een resource (een onderliggend item) is gedefinieerd in een andere resource (een bovenliggend). De diagnostische instellingen en aangepaste script extensies zijn bijvoorbeeld beide gedefinieerd als onderliggende resources van de virtuele machine. Ze kunnen pas worden gemaakt als de virtuele machine bestaat. Daarom worden beide resources gemarkeerd als afhankelijk van de virtuele machine.

## <a name="profiles"></a>Profielen

Er worden verschillende profiel elementen gebruikt bij het definiëren van een virtuele-machine bron. Sommige zijn vereist en sommige zijn optioneel. De elementen hardwareProfile, osProfile, storageProfile en networkProfile zijn bijvoorbeeld vereist, maar de diagnosticsProfile is optioneel. Deze profielen definiëren instellingen zoals:
   
- [size](../sizes.md)
- [naam](/azure/architecture/best-practices/resource-naming) en referenties
- instellingen voor schijf en [besturings systeem](cli-ps-findimage.md)
- [netwerk interface](/previous-versions/azure/virtual-network/virtual-network-deploy-multinic-classic-ps) 
- Diagnostische gegevens over opstarten

## <a name="disks-and-images"></a>Schijven en installatie kopieën
   
In azure kunnen VHD-bestanden [schijven of installatie kopieën](../managed-disks-overview.md)vertegenwoordigen. Wanneer het besturings systeem in een VHD-bestand speciaal is bedoeld voor een specifieke virtuele machine, wordt dit een schijf genoemd. Wanneer het besturings systeem in een VHD-bestand wordt gegeneraliseerd om veel Vm's te maken, wordt dit een installatie kopie genoemd.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Nieuwe virtuele machines en nieuwe schijven maken op basis van een platform installatie kopie

Wanneer u een virtuele machine maakt, moet u bepalen welk besturings systeem u wilt gebruiken. Het element imageReference wordt gebruikt voor het definiëren van het besturings systeem van een nieuwe virtuele machine. In het voor beeld ziet u een definitie voor een Windows Server-besturings systeem:

```json
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Als u een Linux-besturings systeem wilt maken, kunt u deze definitie gebruiken:

```json
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Configuratie-instellingen voor de schijf met het besturings systeem worden toegewezen aan het osDisk-element. In het voor beeld wordt een nieuwe beheerde schijf gedefinieerd met de cache modus ingesteld op **readwrite** en de schijf wordt gemaakt op basis van een [platform installatie kopie](cli-ps-findimage.md):

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Nieuwe virtuele machines maken op basis van bestaande beheerde schijven

Als u virtuele machines wilt maken op basis van bestaande schijven, verwijdert u de imageReference-en osProfile-elementen en definieert u deze schijf instellingen:

```json
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Nieuwe virtuele machines maken op basis van een beheerde installatie kopie

Als u een virtuele machine wilt maken op basis van een beheerde installatie kopie, wijzigt u het imageReference-element en definieert u deze schijf instellingen:

```json
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>Gegevens schijven koppelen

U kunt eventueel ook gegevens schijven toevoegen aan de Vm's. Het [aantal schijven](../sizes.md) is afhankelijk van de grootte van de besturingssysteem schijf die u gebruikt. Met de grootte van de virtuele machines die zijn ingesteld op Standard_DS1_v2, is het maximum aantal gegevens schijven dat kan worden toegevoegd aan de Vm's twee. In het voor beeld wordt één beheerde gegevens schijf aan elke virtuele machine toegevoegd:

```json
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>Uitbreidingen

Hoewel [uitbrei dingen](../extensions/features-windows.md) een afzonderlijke resource zijn, zijn ze nauw verbonden met vm's. Extensies kunnen worden toegevoegd als een onderliggende resource van de virtuele machine of als een afzonderlijke resource. In het voor beeld ziet u de [uitbrei ding van diagnostische gegevens](../extensions/diagnostics-template.md) die wordt toegevoegd aan de vm's:

```json
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

Deze extensie resource gebruikt de variabele storagenaam en de diagnostische variabelen om waarden op te geven. Als u de gegevens wilt wijzigen die door deze uitbrei ding worden verzameld, kunt u meer prestatie meter items toevoegen aan de variabele wadperfcounters. U kunt er ook voor kiezen om de diagnostische gegevens in een ander opslag account te plaatsen dan waar de VM-schijven worden opgeslagen.

Er zijn veel uitbrei dingen die u op een virtuele machine kunt installeren, maar de handigste is waarschijnlijk de [aangepaste script extensie](../extensions/custom-script-windows.md). In het voor beeld wordt een Power shell-script met de naam start.ps1 uitgevoerd op elke virtuele machine wanneer deze voor het eerst wordt gestart:

```json
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

Het start.ps1 script kan veel configuratie taken uitvoeren. De gegevens schijven die zijn toegevoegd aan de virtuele machines in het voor beeld, worden bijvoorbeeld niet geïnitialiseerd. u kunt een aangepast script gebruiken om ze te initialiseren. Als u meerdere opstart taken hebt, kunt u het start.ps1-bestand gebruiken om andere Power shell-scripts in azure Storage aan te roepen. In het voor beeld wordt Power shell gebruikt, maar u kunt elke script methode gebruiken die beschikbaar is in het besturings systeem dat u gebruikt.

U kunt de status van de geïnstalleerde uitbrei dingen bekijken via de uitbrei dingen in de portal:

![Status van uitbrei ding ophalen](./media/template-description/virtual-machines-show-extensions.png)

U kunt ook informatie over de uitbrei ding ophalen met behulp van de Power shell **-opdracht Get-AzVMExtension** , de **VM-extensie** Azure cli-opdracht ophalen of de **extensie gegevens ophalen** rest API.

## <a name="deployments"></a>Implementaties

Wanneer u een sjabloon implementeert, traceert Azure de resources die u als groep hebt geïmplementeerd en wijst deze automatisch een naam toe aan deze geïmplementeerde groep. De naam van de implementatie is hetzelfde als de naam van de sjabloon.

Als u op de hoogte bent van de status van resources in de implementatie, bekijkt u de resource groep in de Azure Portal:

![Implementatie gegevens ophalen](./media/template-description/virtual-machines-deployment-info.png)
    
Het is geen probleem om dezelfde sjabloon te gebruiken om resources te maken of om bestaande resources bij te werken. Wanneer u opdrachten gebruikt om sjablonen te implementeren, hebt u de mogelijkheid om te zeggen welke [modus](../../azure-resource-manager/templates/deploy-powershell.md) u wilt gebruiken. De modus kan worden ingesteld op **voltooid** of **Incrementeel**. De standaard instelling is incrementele updates. Wees voorzichtig wanneer u de **volledige** modus gebruikt, omdat u per ongeluk resources kunt verwijderen. Wanneer u de modus instelt op **voltooid**, worden alle resources in de resource groep die zich niet in de sjabloon bevinden, door Resource Manager verwijderd.

## <a name="next-steps"></a>Volgende stappen

- Maak uw eigen sjabloon met behulp van [ontwerp Azure Resource Manager sjablonen](../../azure-resource-manager/templates/template-syntax.md).
- Implementeer de sjabloon die u hebt gemaakt met een [virtuele Windows-machine maken met een resource manager-sjabloon](ps-template.md).
- Meer informatie over het beheren van de virtuele machines die u hebt gemaakt door [Windows-Vm's maken en beheren te bekijken met de module Azure PowerShell](tutorial-manage-vm.md).
- Zie [Azure Resource Manager-sjabloon verwijzing](/azure/templates/)voor de JSON-syntaxis en-eigenschappen van resource typen in sjablonen.