---
title: Onderhouds beheer voor virtuele Azure-machines met CLI
description: Meer informatie over hoe u kunt bepalen wanneer onderhoud wordt toegepast op uw Azure-Vm's met behulp van onderhouds beheer en CLI.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/20/2020
ms.author: cynthn
ms.openlocfilehash: d94cd649df9da6b36ac484d4fc1e6acef7a21bb7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026162"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>Updates beheren met onderhouds beheer en de Azure CLI

Met de onderhouds controle kunt u bepalen wanneer platform updates moeten worden toegepast op de host-infra structuur van uw geïsoleerde Vm's en voor Azure toegewezen hosts. In dit onderwerp worden de Azure CLI-opties voor onderhouds beheer beschreven. Zie [platform updates beheren met onderhouds beheer](maintenance-control.md)voor meer informatie over de voor delen van het gebruik van onderhouds beheer, de beperkingen en andere beheer opties.

## <a name="create-a-maintenance-configuration"></a>Een onderhoudsconfiguratie maken

Gebruiken `az maintenance configuration create` om een onderhouds configuratie te maken. In dit voor beeld wordt een onderhouds configuratie gemaakt met de naam *myConfig* scoped to the host. 

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

Kopieer de configuratie-ID uit de uitvoer, zodat u deze later kunt gebruiken.

Met `--maintenance-scope host` kunt u ervoor zorgen dat de onderhouds configuratie wordt gebruikt voor het beheren van updates voor de host-infra structuur.

Als u probeert een configuratie met dezelfde naam te maken, maar op een andere locatie, krijgt u een fout melding. Configuratie namen moeten uniek zijn voor uw resource groep.

U kunt een query uitvoeren voor beschik bare onderhouds configuraties met `az maintenance configuration list` .

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Een onderhouds configuratie maken met een gepland venster
U kunt ook een gepland venster declareren wanneer de updates worden toegepast op de resources in Azure. In dit voor beeld wordt een onderhouds configuratie met de naam myConfig gemaakt met een geplande periode van vijf uur op de vierde maandag van elke maand. Wanneer u een gepland venster hebt gemaakt, hoeft u de updates niet meer hand matig toe te passen.

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
> De **duur** van het onderhoud moet *2 uur* of langer zijn. **Terugkeer patroon** van onderhoud moet worden ingesteld op ten minste één keer in 35 dagen.

Terugkeer patroon van onderhoud kan worden uitgedrukt als dagelijks, wekelijks of maandelijks. Een aantal voorbeelden:
- **dagelijks** onderhoud-venster-keer-elke: "dag" **of** "3Days"
- **wekelijks** onderhoud: venster herhalen-elke: "3Weeks" **of** "week zaterdag, zondag"
- **maandelijks**-onderhouds venster-elke: "month day23, day24" **of** "month afgelopen zondag" **of** "month 4e maandag"


## <a name="assign-the-configuration"></a>De configuratie toewijzen

Gebruiken `az maintenance assignment create` om de configuratie toe te wijzen aan uw geïsoleerde virtuele machine of de toegewezen Azure-host.

### <a name="isolated-vm"></a>Geïsoleerde VM

Pas de configuratie toe op een virtuele machine met behulp van de ID van de configuratie. Geef `--resource-type virtualMachines` de naam van de virtuele machine voor en `--resource-name` de resource groep voor voor de virtuele machine in `--resource-group` , en de locatie van de virtuele machine voor `--location` . 

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

Als u een configuratie wilt Toep assen op een specifieke host, moet `--resource-type hosts` u `--resource-parent-name` de naam van de hostgroep en gebruiken `--resource-parent-type hostGroups` . 

De para meter `--resource-id` is de id van de host. U kunt [AZ VM host Get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view) gebruiken om de id van uw specifieke host op te halen.

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

U kunt controleren of de configuratie correct is toegepast, of controleren welke configuratie momenteel wordt toegepast met `az maintenance assignment list` .

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


## <a name="check-for-pending-updates"></a>Controleren op updates die nog niet zijn uitgevoerd

Gebruiken `az maintenance update list` om te zien of er updates in behandeling zijn. Update--abonnement als ID voor het abonnement dat de virtuele machine bevat.

Als er geen updates zijn, wordt er een fout bericht weer gegeven met de volgende tekst: `Resource not found...StatusCode: 404` .

Als er updates zijn, wordt er slechts één geretourneerd, zelfs als er meerdere updates in behandeling zijn. De gegevens voor deze update worden geretourneerd in een object:

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

Controleren op updates die in behandeling zijn voor een geïsoleerde virtuele machine. In dit voor beeld wordt de uitvoer opgemaakt als een tabel voor de Lees baarheid.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Toegewezen host

Controleren op updates die in behandeling zijn voor een specifieke host. In dit voor beeld wordt de uitvoer opgemaakt als een tabel voor de Lees baarheid. Vervang de waarden voor de resources door uw eigen gegevens.

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

Gebruik `az maintenance apply update` om in behandeling zijnde updates toe te passen. Als de opdracht is voltooid, wordt JSON geretourneerd met de details van de update.

### <a name="isolated-vm"></a>Geïsoleerde VM

Een aanvraag maken om updates op een geïsoleerde virtuele machine toe te passen.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Toegewezen host

Updates Toep assen op een specifieke host.

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

## <a name="check-the-status-of-applying-updates"></a>De status van het Toep assen van updates controleren 

U kunt de voortgang van de updates controleren met behulp van `az maintenance applyupdate get` . 

U kunt `default` als update naam gebruiken om de resultaten voor de laatste update te bekijken of door `myUpdateName` de naam te vervangen van de update die is geretourneerd tijdens het uitvoeren van `az maintenance applyupdate create` .

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
LastUpdateTime is de tijd waarop de update is voltooid, ofwel door u of door het platform is gestart in het geval dat het zelf onderhoud niet is gebruikt. Als er nog nooit een update is toegepast via de onderhouds controle, wordt de standaard waarde weer gegeven.

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


## <a name="delete-a-maintenance-configuration"></a>Een onderhouds configuratie verwijderen

Gebruik `az maintenance configuration delete` om een onderhouds configuratie te verwijderen. Als u de configuratie verwijdert, wordt de onderhouds controle van de bijbehorende resources verwijderd.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --resource-name myConfig
```

## <a name="next-steps"></a>Volgende stappen
Zie [onderhoud en updates](maintenance-and-updates.md)voor meer informatie.
