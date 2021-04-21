---
title: Onderhoudsbeheer voor virtuele Azure-machines met CLI
description: Leer hoe u kunt bepalen wanneer onderhoud wordt toegepast op uw Azure-VM's met behulp van Onderhoudsbeheer en CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/20/2020
ms.author: cynthn
ms.openlocfilehash: c57f66eca5d15024c6b10e8fad12ddb575b9f894
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765895"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>Updates controleren met Onderhoudsbeheer en de Azure CLI

Met onderhoudsbeheer kunt u bepalen wanneer u platformupdates wilt toepassen op de hostinfrastructuur van uw geïsoleerde VM's en toegewezen Azure-hosts. In dit onderwerp worden de Azure CLI-opties voor onderhoudsbeheer besproken. Zie Platformupdates beheren met Onderhoudsbeheer voor meer informatie over de voordelen van het gebruik van onderhoudsbeheer, de beperkingen en andere [beheeropties.](maintenance-control.md)

## <a name="create-a-maintenance-configuration"></a>Een onderhoudsconfiguratie maken

Gebruik `az maintenance configuration create` om een onderhoudsconfiguratie te maken. In dit voorbeeld wordt een onderhoudsconfiguratie met de *naam myConfig* gemaakt met het bereik voor de host. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host\
   --location eastus
```

Kopieer de configuratie-id uit de uitvoer voor later gebruik.

Het `--maintenance-scope host` gebruik van zorgt ervoor dat de onderhoudsconfiguratie wordt gebruikt voor het beheren van updates voor de hostinfrastructuur.

Als u probeert een configuratie met dezelfde naam te maken, maar op een andere locatie, krijgt u een foutmelding. Configuratienamen moeten uniek zijn voor uw resourcegroep.

U kunt een query uitvoeren op beschikbare onderhoudsconfiguraties met behulp van `az maintenance configuration list` .

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Een onderhoudsconfiguratie maken met een gepland venster
U kunt ook een gepland venster declareeren wanneer Azure de updates op uw resources gaat toepassen. In dit voorbeeld wordt een onderhoudsconfiguratie met de naam myConfig gemaakt met een gepland venster van 5 uur op de vierde maandag van elke maand. Wanneer u een gepland venster maakt, hoeft u de updates niet langer handmatig toe te passen.

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Month Fourth Monday" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

> [!IMPORTANT]
> De **duur** van het *onderhoud moet 2 uur* of langer zijn. Het **terugkeerpatroon** voor onderhoud moet worden ingesteld op ten minste één keer in 35 dagen.

Het terugkeerpatroon voor onderhoud kan dagelijks, wekelijks of maandelijks worden uitgedrukt. Een aantal voorbeelden:
- **dagelijks**- maintenance-window-recur-every: "Day" **of** "3Days"
- **weekly**- maintenance-window-recur-every: "3Weeks" **of** "Week Saturday,Sunday"
- **monthly**- maintenance-window-recur-every: "Month day23,day24" **or** "Month Last Sunday" **or** "Month Fourth Monday"


## <a name="assign-the-configuration"></a>De configuratie toewijzen

Gebruik `az maintenance assignment create` om de configuratie toe te wijzen aan uw geïsoleerde VM of Azure Dedicated Host.

### <a name="isolated-vm"></a>Geïsoleerde VM

Pas de configuratie toe op een VM met behulp van de id van de configuratie. Geef de naam van de VM op en geef deze op voor , en geef de resourcegroep voor op voor de VM in en geef de locatie van `--resource-type virtualMachines` `--resource-name` de `--resource-group` VM voor `--location` op. 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Toegewezen host

Als u een configuratie wilt toepassen op een toegewezen host, moet u `--resource-type hosts` , met de naam van de `--resource-parent-name` hostgroep en `--resource-parent-type hostGroups` opnemen. 

De parameter `--resource-id` is de id van de host. U kunt [az vm host get-instance-view gebruiken om](/cli/azure/vm/host#az_vm_host_get_instance_view) de id van uw toegewezen host op te halen.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Configuratie controleren

U kunt controleren of de configuratie correct is toegepast of controleren welke configuratie momenteel wordt toegepast met behulp van `az maintenance assignment list` .

### <a name="isolated-vm"></a>Geïsoleerde VM

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Toegewezen host 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Controleren op updates die in behandeling zijn

Gebruik `az maintenance update list` om te zien of er updates in behandeling zijn. Werk --subscription bij om de id te zijn van het abonnement dat de VM bevat.

Als er geen updates zijn, wordt met de opdracht een foutbericht weergegeven dat de volgende tekst bevat: `Resource not found...StatusCode: 404` .

Als er updates zijn, wordt er slechts één geretourneerd, zelfs als er meerdere updates in behandeling zijn. De gegevens voor deze update worden geretourneerd in een -object:

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>Geïsoleerde VM

Controleer of er updates in behandeling zijn voor een geïsoleerde VM. In dit voorbeeld is de uitvoer opgemaakt als een tabel voor leesbaarheid.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Toegewezen host

Controleren op in behandeling zijnde updates voor een toegewezen host. In dit voorbeeld is de uitvoer opgemaakt als een tabel voor leesbaarheid. Vervang de waarden voor de resources door uw eigen waarden.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Updates toepassen

Gebruik om `az maintenance apply update` in behandeling zijnde updates toe te passen. Als deze opdracht is geslaagd, retourneert deze JSON met de details van de update.

### <a name="isolated-vm"></a>Geïsoleerde VM

Maak een aanvraag om updates toe te passen op een geïsoleerde VM.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Toegewezen host

Updates toepassen op een toegewezen host.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>De status van het toepassen van updates controleren 

U kunt de voortgang van de updates controleren met behulp van `az maintenance applyupdate get` . 

U kunt gebruiken als de naam van de update om de resultaten voor de laatste update weer te geven, of vervangen door de naam van de update die is geretourneerd toen `default` `myUpdateName` u hebt `az maintenance applyupdate create` geuit.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime is het tijdstip waarop de update is voltooid, ofwel geïnitieerd door u of door het platform voor het geval er geen zelfonderhoudsvenster is gebruikt. Als er nog nooit een update is toegepast via onderhoudsbeheer, wordt de standaardwaarde weergegeven.

### <a name="isolated-vm"></a>Geïsoleerde VM

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Toegewezen host

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Een onderhoudsconfiguratie verwijderen

Gebruik `az maintenance configuration delete` om een onderhoudsconfiguratie te verwijderen. Als u de configuratie verwijdert, wordt het onderhoudsbeheer verwijderd uit de gekoppelde resources.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --resource-name myConfig
```

## <a name="next-steps"></a>Volgende stappen
Zie Onderhoud en [updates voor meer informatie.](maintenance-and-updates.md)
