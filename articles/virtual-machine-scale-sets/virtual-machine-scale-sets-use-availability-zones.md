---
title: Maak een Azure-schaalset die gebruikmaakt van Beschikbaarheidszones
description: Meer informatie over het maken van schaal sets voor virtuele Azure-machines die gebruikmaken van Beschikbaarheidszones voor verhoogde redundantie tegen storingen
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 08/08/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: c5ddd5846be91e9fc99a251d6ad45ade8bde2937
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016655"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Een schaalset voor virtuele machines maken die gebruikmaakt van Beschikbaarheidszones

Als u de schaal sets van virtuele machines wilt beveiligen tegen fouten op Data Center-niveau, kunt u een schaalset maken over Beschikbaarheidszones. Azure-regio's die Beschikbaarheidszones ondersteunen, hebben mini maal drie afzonderlijke zones, elk met hun eigen onafhankelijke voedings bron, netwerk en koeling. Zie [overzicht van Beschikbaarheidszones](../availability-zones/az-overview.md)voor meer informatie.

## <a name="availability-considerations"></a>Beschikbaarheidsoverwegingen

Wanneer u een schaalset voor een regionale (niet-zonegebonden) implementeert in een of meer zones vanaf API-versie *2017-12-01*, hebt u de volgende beschikbaarheids opties:
- Maximale sprei ding (platformFaultDomainCount = 1)
- Statische vaste sprei ding (platformFaultDomainCount = 5)
- Sprei ding uitgelijnd met opslag schijf fout domeinen (platforFaultDomainCount = 2 of 3)

Met de maximale sprei ding is de schaalset uw Vm's verdeeld over zoveel mogelijk fout domeinen binnen elke zone. Deze verspreiding kan zich in meer of minder dan vijf fout domeinen per zone bevindt. Met statische vaste sprei ding wordt de schaal van uw Vm's verdeeld over precies vijf fout domeinen per zone. Als de schaalset geen vijf verschillende fout domeinen per zone kan vinden om te voldoen aan de toewijzings aanvraag, mislukt de aanvraag.

**We raden u aan om te implementeren met een maximale sprei ding voor de meeste werk belastingen**, omdat deze benadering de beste sprei ding biedt in de meeste gevallen. Als u replica's nodig hebt om te worden verdeeld over verschillende hardware-isolatie-eenheden, raden we u aan om te spreiden over Beschikbaarheidszones en maximale sprei ding binnen elke zone te gebruiken.

> [!NOTE]
> Met maximale sprei ding ziet u slechts één fout domein in de weer gave van de VM-instantie van de schaalset en in de meta gegevens van het exemplaar, ongeacht het aantal fout domeinen dat de Vm's zijn verdeeld. De sprei ding binnen elke zone is impliciet.

### <a name="placement-groups"></a>Plaatsingsgroepen

Wanneer u een schaalset implementeert, hebt u ook de mogelijkheid om te implementeren met één [plaatsings groep](./virtual-machine-scale-sets-placement-groups.md) per beschikbaarheids zone of met meerdere per zone. Voor regionale schaal sets (niet-zonegebonden) is het keuze om één plaatsings groep in de regio te hebben of meerdere in de regio te hebben. Voor de meeste werk belastingen raden we u aan meerdere plaatsings groepen aan te bieden, waarmee u een grotere schaal kunt krijgen. In API-versie *2017-12-01*, schaal sets standaard ingesteld op meerdere plaatsings groepen voor schaal sets met één zone en meerdere zones, maar deze zijn standaard één plaatsings groep voor regionale (niet-zonegebonden) schaal sets.

> [!NOTE]
> Als u de maximale sprei ding gebruikt, moet u meerdere plaatsings groepen gebruiken.

### <a name="zone-balancing"></a>Zone-verdeling

Ten slotte, voor schaal sets die zijn geïmplementeerd in meerdere zones, hebt u ook de mogelijkheid om "Best effort zone balances" of "strikte zone balans" te kiezen. Een schaalset wordt beschouwd als ' evenwichtig ' als elke zone hetzelfde aantal Vm's of + \\ -1 virtuele machines in alle andere zones voor de schaalset heeft. Bijvoorbeeld:

- Een schaalset met 2 Vm's in zone 1, 3 Vm's in zone 2 en 3 virtuele machines in zone 3 wordt als evenwichtig beschouwd. Er is slechts één zone met een ander aantal VM'S en de waarde 1 is kleiner dan de andere zones. 
- Een schaalset met 1 virtuele machine in zone 1, 3 Vm's in zone 2 en 3 Vm's in zone 3 wordt als niet-Balanced beschouwd. Zone 1 heeft twee virtuele machines dan zones 2 en 3.

Het is mogelijk dat de virtuele machines in de schaalset zijn gemaakt, maar dat de implementatie van de virtuele machines niet kan worden geïmplementeerd. Deze Vm's met uitbrei ding van fouten worden nog steeds geteld bij het bepalen of een schaalset is verdeeld. Zo wordt bijvoorbeeld een schaalset met drie Vm's in zone 1, 3 Vm's in zone 2 en 3 Vm's in zone 3 beschouwd als evenwichtig, zelfs als alle uitbrei dingen in zone 1 zijn mislukt en alle uitbrei dingen zijn geslaagd in zones 2 en 3.

Met een zone saldo van de hoogste inspanning probeert de schaalset in en uit te schalen terwijl het saldo wordt gehandhaafd. Als dit echter om een bepaalde reden niet mogelijk is (bijvoorbeeld als één zone uitvalt, kan de schaalset geen nieuwe virtuele machine in die zone maken), kan de schaalset tijdelijk onevenwichtig worden geschaald. Bij volgende scale-out-pogingen voegt de schaalset Vm's toe aan zones die meer Vm's nodig hebben voor de schaalset die moet worden verdeeld. Op de volgende schaal van pogingen verwijdert de schaalset ook Vm's uit zones die minder Vm's nodig hebben voor de schaalset die moet worden verdeeld. Met ' strikte zone balans ' kan de schaalset geen pogingen doen om in of uit te schalen als dat zo is, zou dit leiden tot onevenwichtigheid.

Stel *zoneBalance* in op *False* als u de zone balans voor de beste inspanning wilt gebruiken. Deze instelling is de standaard waarde in API-versie *2017-12-01*. Als u strikte zone balans wilt gebruiken, stelt u *zoneBalance* in op *waar*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Enkelvoudige zone en zone-redundante schaal sets

Wanneer u een schaalset voor virtuele machines implementeert, kunt u kiezen voor het gebruik van één beschikbaarheids zone in een regio of in meerdere zones.

Wanneer u een schaalset in één zone maakt, bepaalt u in welke zone alle VM-exemplaren worden uitgevoerd en wordt de schaalset alleen beheerd en automatisch geschaald in die zone. Een zone-redundante schaalset stelt u in staat om een enkele schaalset te maken die meerdere zones omspant. Als VM-exemplaren worden gemaakt, worden deze standaard gelijkmatig verdeeld over zones. Als er een onderbreking optreedt in een van de zones, wordt een schaalset niet automatisch uitgeschaald om de capaciteit te verg Roten. Een best practice is het configureren van regels voor automatisch schalen op basis van CPU-of geheugen gebruik. Met de regels voor automatisch schalen kunt u instellen dat de schaalset reageert op een verlies van de VM-exemplaren in die ene zone door nieuwe instanties in de resterende operationele zones te schalen.

Als u Beschikbaarheidszones wilt gebruiken, moet uw schaalset worden gemaakt in een [ondersteunde Azure-regio](../availability-zones/az-region.md). U kunt een schaalset maken die gebruikmaakt van Beschikbaarheidszones met een van de volgende methoden:

- [Azure-portal](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Azure Resource Manager-sjablonen](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Het proces voor het maken van een schaalset die gebruikmaakt van een beschikbaarheids zone, is hetzelfde als de informatie in het [artikel aan](quick-create-portal.md)de slag. Wanneer u een ondersteunde Azure-regio selecteert, kunt u een schaalset maken in een of meer beschik bare zones, zoals wordt weer gegeven in het volgende voor beeld:

![Een schaalset maken in één beschikbaarheids zone](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

De schaalset en ondersteunende resources, zoals de Azure load balancer en het open bare IP-adres, worden in de door u opgegeven zone gemaakt.

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

Het proces voor het maken van een schaalset die gebruikmaakt van een beschikbaarheids zone, is hetzelfde als de informatie in het [artikel aan](quick-create-cli.md)de slag. Als u Beschikbaarheidszones wilt gebruiken, moet u uw schaalset maken in een ondersteunde Azure-regio.

Voeg de `--zones` para meter toe aan de opdracht [AZ vmss Create](/cli/azure/vmss) en geef op welke zone moet worden gebruikt (bijvoorbeeld zone *1*, *2* of *3*). In het volgende voor beeld wordt een schaalset met één zone gemaakt met de naam *myScaleSet* in zone *1*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

Zie voor een volledig voor beeld van een schaalset met één zone en netwerk bronnen [Dit cli-voorbeeld script](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh)

### <a name="zone-redundant-scale-set"></a>Zone-redundante schaalset

Als u een zone-redundante schaalset wilt maken, gebruikt u een openbaar IP-adres en load balancer voor de *standaard* -SKU. Voor uitgebreide redundantie maakt de *standaard* -SKU zone-redundante netwerk bronnen. Zie [Azure Load Balancer Standard-overzicht](../load-balancer/load-balancer-overview.md) en [Standard Load Balancer en Beschikbaarheidszones](../load-balancer/load-balancer-standard-availability-zones.md)voor meer informatie.

Als u een zone-redundante schaalset wilt maken, geeft u meerdere zones op met de `--zones` para meter. In het volgende voor beeld wordt een zone-redundante schaalset gemaakt met de naam *myScaleSet* voor de zones *1, 2, 3*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1 2 3
```

Het duurt enkele minuten om alle resources en virtuele machines van de schaalset te maken en te configureren in de zone (s) die u opgeeft. Zie voor een volledig voor beeld van een zone-redundante schaalset en netwerk bronnen [Dit cli-voorbeeld script](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

Als u Beschikbaarheidszones wilt gebruiken, moet u uw schaalset maken in een ondersteunde Azure-regio. Voeg de `-Zone` para meter toe aan de opdracht [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) en geef op welke zone u wilt gebruiken (zoals zone *1*, *2* of *3*).

In het volgende voor beeld wordt een schaalset met één zone gemaakt met de naam *myScaleSet* in *VS-Oost 2* zone *1*. De Azure-netwerkresources voor het virtuele netwerk, het openbare IP-adres en de load balancer worden automatisch gemaakt. Geef desgevraagd uw eigen beheerdersreferenties op voor de VM-exemplaren in de schaalset:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>Zone-redundante schaalset

Als u een zone-redundante schaalset wilt maken, geeft u meerdere zones op met de `-Zone` para meter. In het volgende voor beeld wordt een zone-redundante schaalset gemaakt met de naam *myScaleSet* in *VS-Oost 2* zones *1, 2, 3*. De zone-redundante Azure-netwerk resources voor het virtuele netwerk, het open bare IP-adres en de load balancer worden automatisch gemaakt. Geef desgevraagd uw eigen beheerdersreferenties op voor de VM-exemplaren in de schaalset:

```powershell
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen gebruiken

Het proces voor het maken van een schaalset die gebruikmaakt van een beschikbaarheids zone, is hetzelfde als die wordt beschreven in het artikel aan de slag voor [Linux](quick-create-template-linux.md) of [Windows](quick-create-template-windows.md). Als u Beschikbaarheidszones wilt gebruiken, moet u uw schaalset maken in een ondersteunde Azure-regio. Voeg de `zones` eigenschap toe aan het resource type *micro soft. Compute/virtualMachineScaleSets* in uw sjabloon en geef op welke zone u wilt gebruiken (zoals zone *1*, *2* of *3*).

In het volgende voor beeld wordt een Linux-schaalset met één zone gemaakt met de naam *myScaleSet* in *VS-Oost 2* zone *1*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Zie voor een volledig voor beeld van een schaalset met één zone en netwerk bronnen [deze voorbeeld sjabloon voor Resource Manager](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Zone-redundante schaalset

Als u een zone-redundante schaalset wilt maken, geeft u meerdere waarden op in de `zones` eigenschap voor het resource type *micro soft. Compute/virtualMachineScaleSets* . In het volgende voor beeld wordt een zone-redundante schaalset gemaakt met de naam *myScaleSet* in *VS-Oost 2* zones *1, 2, 3*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Als u een openbaar IP-adres of een load balancer maakt, geeft u de eigenschap *"SKU": {"naam": "Standard"} "* op om zone-redundante netwerk bronnen te maken. U moet ook een netwerk beveiligings groep en-regels maken om verkeer toe te staan. Zie [Azure Load Balancer Standard-overzicht](../load-balancer/load-balancer-overview.md) en [Standard Load Balancer en Beschikbaarheidszones](../load-balancer/load-balancer-standard-availability-zones.md)voor meer informatie.

Zie voor een volledig voor beeld van een zone-redundante schaalset en netwerk bronnen [deze voorbeeld sjabloon voor Resource Manager](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>Volgende stappen

Nu u een schaalset in een beschikbaarheids zone hebt gemaakt, kunt u meer informatie over het [implementeren van toepassingen op schaal sets voor virtuele machines](tutorial-install-apps-cli.md) of het [gebruik van automatisch schalen met virtuele-machine schaal sets](tutorial-autoscale-cli.md).
