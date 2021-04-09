---
title: Uw Azure-resources verkennen
description: Meer informatie over het gebruik van de resource Graph-query taal om uw resources te verkennen en te ontdekken hoe ze zijn verbonden.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: a76ce2bb34fd6e99fedf5030c4723b6dd5acbb91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98917721"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Azure-resources verkennen met Resource Graph

Azure resource Graph biedt de mogelijkheid om uw Azure-resources snel en op schaal te verkennen en te detecteren. Het is ontworpen voor snelle reacties en is een fantastische manier om meer te weten te komen over uw omgeving en over de eigenschappen die beschikbaar zijn in uw Azure-resources.

## <a name="explore-virtual-machines"></a>Virtuele machines verkennen

Een algemene resource in Azure is een virtuele machine. Als resource type hebben virtuele machines veel eigenschappen die kunnen worden opgevraagd. Elke eigenschap biedt een optie voor het filteren of zoeken naar precies de bron waarnaar u op zoek bent.

### <a name="virtual-machine-discovery"></a>Detectie van virtuele machines

Laten we beginnen met een eenvoudige query om één VM op te halen uit onze omgeving en de eigenschappen te bekijken die worden geretourneerd.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> De `Search-AzGraph` cmdlet Azure PowerShell retourneert standaard een **PSCustomObject** . Om ervoor te hebben dat de uitvoer er hetzelfde uitziet als wat wordt geretourneerd door Azure CLI, `ConvertTo-Json` wordt de cmdlet gebruikt. De standaard waarde voor **diepte** is _2_. Als u deze instelt op _100_ , worden alle geretourneerde niveaus geconverteerd.

De JSON-resultaten zijn vergelijkbaar met het volgende voor beeld:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

De eigenschappen geven ons aanvullende informatie over de virtuele-machine resource zelf. Deze eigenschappen zijn onder andere: besturings systeem, schijven, tags en de resource groep en het abonnement waarvan het deel uitmaakt.

### <a name="virtual-machines-by-location"></a>Virtuele machines op locatie

Als we de resource van de virtuele machines hebben geleerd, gaan we de **locatie** -eigenschap gebruiken om alle virtuele machines op locatie te tellen. Als u de query wilt bijwerken, worden de limieten verwijderd en wordt het aantal locatie waarden samenvatten.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

De JSON-resultaten zijn vergelijkbaar met het volgende voor beeld:

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

Nu kunt u zien hoeveel virtuele machines er in elke Azure-regio zijn.

### <a name="virtual-machines-by-sku"></a>Virtuele machines per SKU

Als u terugkeert naar de oorspronkelijke eigenschappen van de virtuele machine, gaat u naar de virtuele machines met een SKU-grootte van **Standard_B2s**. Als de JSON wordt weer gegeven, ziet u dat deze is opgeslagen in **Properties. hardwareprofile. vmsize**. De query wordt bijgewerkt, zodat alle virtuele machines worden gevonden die overeenkomen met deze grootte en alleen de naam van de virtuele machine en regio retour neren.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Virtuele machines die zijn verbonden met Premium beheerde schijven

Om de details te verkrijgen van Premium-beheerde schijven die zijn gekoppeld aan deze **Standard_B2s** virtuele machines, wordt de query uitgebreid om de resource-id van die beheerde schijven te retour neren.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Het resultaat is een lijst met schijf-Id's.

### <a name="managed-disk-discovery"></a>Detectie van beheerde schijven

Met de eerste record uit de vorige query gaan we de eigenschappen verkennen die aanwezig zijn op de beheerde schijf die is gekoppeld aan de eerste virtuele machine. De bijgewerkte query maakt gebruik van de schijf-ID en wijzigt het type.

Voor beeld van uitvoer van de vorige query bijvoorbeeld:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Voordat u de query uitvoert, weet u zeker dat het **type** **micro soft. Compute/disks** is? Als u de volledige ID bekijkt, ziet u **/providers/Microsoft.Compute/disks/** als onderdeel van de teken reeks.
Dit teken reeks fragment geeft u een hint voor het type waarnaar moet worden gezocht. Een alternatieve methode is om de limiet te verwijderen per type en in plaats daarvan alleen op het ID-veld te zoeken. Als de ID uniek is, wordt er slechts één record geretourneerd en bevat de eigenschap **type** deze details.

> [!NOTE]
> Voor een goede werking van dit voor beeld moet u het veld ID vervangen door een resultaat uit uw eigen omgeving.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

De JSON-resultaten zijn vergelijkbaar met het volgende voor beeld:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Virtuele machines verkennen voor het vinden van open bare IP-adressen

Met deze set query's worden eerst alle netwerk interfaces (NIC) die zijn verbonden met virtuele machines gezocht en opgeslagen. Vervolgens gebruiken de query's de lijst met Nic's om elke IP-adres bron te vinden die een openbaar IP-adres is en deze waarden op te slaan. Ten slotte bieden de query's een lijst met de open bare IP-adressen.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

Gebruik het bestand (Azure CLI) of de variabele (Azure PowerShell) in de volgende query om de gerelateerde netwerk interface bronnen weer te geven, waarbij een openbaar IP-adres aan de NIC is gekoppeld.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

Gebruik ten slotte de lijst met open bare IP-adres bronnen die zijn opgeslagen in het bestand (Azure CLI) of variabele (Azure PowerShell) om het werkelijke open bare IP-adres van het gerelateerde object en weer gave op te halen.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

`join`Zie de [lijst virtuele machines met hun netwerk interface en openbaar IP-](../samples/advanced.md#join-vmpip) voor beeld voor meer informatie over het uitvoeren van deze stappen in één query met de operator.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [querytaal](query-language.md).
- Zie de taal die wordt gebruikt in [Starter-query's](../samples/starter.md).
- Zie [Geavanceerde query's](../samples/advanced.md) voor geavanceerde gebruikswijzen.
