---
title: 'Zelfstudie: een schaalset automatisch schalen met de Azure CLI'
description: Leer hoe u met Azure CLI automatisch een schaalset met virtuele machines schaalt wanneer de vraag naar CPU toeneemt en afneemt
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/18/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 68f311a949d6c7663c5602c444d1b7b9af09dcad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016723"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli"></a>Zelfstudie: Een virtuele-machineschaalset automatisch schalen met Azure CLI

Wanneer u een schaalset maakt, definieert u het aantal VM-exemplaren dat u wilt uitvoeren. Wanneer de vraag van de toepassing verandert, kunt u het aantal VM-exemplaren automatisch vergroten of verkleinen. De mogelijkheid van automatisch schalen stelt u in staat om altijd te voldoen aan de vraag van klanten houden of om gedurende de levenscyclus van uw app te reageren op wijzigingen in de prestaties van de toepassing. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Automatisch schalen gebruiken met een schaalset
> * Regels voor automatisch schalen maken en gebruiken
> * Stresstest uitvoeren voor VM-exemplaren en regels voor automatisch schalen activeren
> * Automatisch terugschalen bij afname van de vraag

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor deze zelfstudie is versie 2.0.32 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-scale-set"></a>Een schaalset maken

Maak als volgt een resourcegroep met [az group create](/cli/azure/group):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak nu een virtuele-machineschaalset met [az vmss create](/cli/azure/vmss). In het volgende voorbeeld wordt een schaalset gemaakt met *2* exemplaren en worden er SSH-sleutels gegenereerd als deze nog niet bestaan:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="define-an-autoscale-profile"></a>Een profiel voor automatisch schalen definiëren

Als u automatisch schalen wilt inschakelen voor een schaalset, moet u eerst een profiel voor automatisch schalen definiëren. In dit profiel worden de minimum-, maximum- en standaardcapaciteit ingesteld voor de schaalset. Met behulp van deze limieten kunt u de kosten in de hand houden doordat er niet steeds VM-exemplaren hoeven te worden gemaakt, en kunt u aanvaardbare prestaties realiseren met een minimum aantal exemplaren die altijd kunnen worden ingeschaald. U maakt een profiel voor automatisch schalen met [az monitor autoscale create](/cli/azure/monitor/autoscale#az-monitor-autoscale-create). In het volgende voorbeeld worden de minimum- en standaardcapaciteit ingesteld op *2* VM-exemplaren en het maximum op *10*:

```azurecli-interactive
az monitor autoscale create \
  --resource-group myResourceGroup \
  --resource myScaleSet \
  --resource-type Microsoft.Compute/virtualMachineScaleSets \
  --name autoscale \
  --min-count 2 \
  --max-count 10 \
  --count 2
```

## <a name="create-a-rule-to-autoscale-out"></a>Een regel maken voor automatisch uitschalen

Als de vraag van uw toepassing toeneemt, neemt de belasting van de VM-exemplaren in de schaalset ook toe. Als deze toegenomen belasting consistent is, en geen piekbelasting is, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verhogen. Wanneer deze VM-exemplaren worden gemaakt en uw toepassingen worden geïmplementeerd, zorgt de schaalset ervoor dat er via de load balancer verkeer wordt gedistribueerd naar de exemplaren. U bepaalt welke meetwaarden gegevens moeten worden bewaakt, zoals CPU of schijf, hoe lang de belasting van de toepassing overeen moet komen met een bepaalde drempelwaarde en hoeveel VM-exemplaren er moeten worden toegevoegd aan de schaalset.

We gaan een regel maken met [az monitor autoscale create](/cli/azure/monitor/autoscale/rule#az-monitor-autoscale-rule-create) waarmee het aantal VM-exemplaren in een schaalset wordt verhoogd wanneer de gemiddelde CPU-belasting gedurende een periode van vijf minuten groter dan 70% is. Wanneer de regel wordt geactiveerd, wordt het aantal VM-exemplaren met drie verhoogd.

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU > 70 avg 5m" \
  --scale out 3
```

## <a name="create-a-rule-to-autoscale-in"></a>Een regel maken voor automatisch inschalen

In het weekend of 's avonds kan de vraag voor uw toepassing afnemen. Als deze afgenomen belasting consistent is gedurende een bepaalde periode, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verlagen. Deze inschaalactie reduceert de kosten voor het uitvoeren van uw schaalset, aangezien u alleen het aantal exemplaren uitvoert dat vereist is om te voldoen aan de actuele vraag.

We gaan nog een regel maken met [az monitor autoscale create](/cli/azure/monitor/autoscale/rule#az-monitor-autoscale-rule-create), maar met deze regel wordt het aantal VM-exemplaren in een schaalset verlaagd wanneer de gemiddelde CPU-belasting gedurende een periode van vijf minuten minder dan 30% is. In het volgende voorbeeld wordt er een regel gedefinieerd voor het inschalen van het aantal VM-exemplaren met één:

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU < 30 avg 5m" \
  --scale in 1
```

## <a name="generate-cpu-load-on-scale-set"></a>CPU-belasting genereren voor schaalset

Voor het testen van de regels voor automatisch schalen gaan we wat CPU-belasting genereren voor de VM-exemplaren in de schaalset. Deze gesimuleerde CPU-belasting zorgt ervoor dat de schaalset automatisch wordt uitgeschaald en het aantal VM-exemplaren dus wordt verhoogd. Als de gesimuleerde CPU-belasting vervolgens weer afneemt, wordt de schaalset ingeschaald en zakt het aantal VM-exemplaren.

Gebruik eerst [az vmss list-instance-connection-info](/cli/azure/vmss) om de adressen en poorten op te vragen waarmee VM-exemplaren in een schaalset moeten worden verbonden:

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

In de volgende voorbeelduitvoer ziet u de exemplaarnaam, het openbare IP-adres van de load balancer en het poortnummer waarnaar de NAT-regels (Network Address Translation) verkeer doorsturen:

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

Ga met SSH naar uw eerste VM-exemplaar. Geef met de parameter `-p` uw eigen openbare IP-adres en poortnummer op, zoals is vastgesteld in de voorgaande opdracht:

```console
ssh azureuser@13.92.224.66 -p 50001
```

Als u bent aangemeld, installeert u het hulpprogramma **stress**. Start *10* **stress**-werkrollen die CPU-belasting genereren. Deze werkrollen worden gedurende *420* seconden uitgevoerd, wat voldoende is om met de regels voor automatisch schalen de gewenste actie te implementeren.

```console
sudo apt-get update
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Wanneer **stress** uitvoer toont die lijkt op *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, drukt u op de *Enter*-toets om terug te keren naar de prompt.

Om te controleren of **stress** CPU-belasting genereert, onderzoekt u de actieve systeembelasting met het hulpprogramma **top**:

```console
top
```

Sluit **top** af en verbreek vervolgens de verbinding met het VM-exemplaar. **stress** wordt nog gewoon uitgevoerd op het VM-exemplaar.

```console
Ctrl-c
exit
```

Maak verbinding met het tweede VM-exemplaar met het poortnummer dat u hebt opgevraagd met de eerdere opdracht [az vmss list-instance-connection-info](/cli/azure/vmss):

```console
ssh azureuser@13.92.224.66 -p 50003
```

Voer **stress** met tien werkrollen uit op dit tweede VM-exemplaar.

```console
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Wanneer **stress** uitvoer toont die lijkt op *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, drukt u op de *Enter*-toets om terug te keren naar de prompt.

Verbreek de verbinding met het tweede VM-exemplaar. **stress** wordt nog gewoon uitgevoerd op het VM-exemplaar.

```console
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>De actieve regels voor automatisch schalen bewaken

Gebruik **watch** om het aantal VM-exemplaren in de schaalset te controleren. Het duurt vijf minuten voordat de regels voor automatisch schalen het proces van uitschalen starten in reactie op de CPU-belasting die met **stress** wordt gegenereerd op elk van de VM-exemplaren:

```azurecli-interactive
watch az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

Als de CPU-drempelwaarde is bereikt, zorgen de regels voor automatisch schalen ervoor dat het aantal VM-exemplaren in de schaalset wordt verhoogd. In de volgende uitvoer ziet u dat er drie VM's worden gemaakt op het moment dat de schaalset gaat uitschalen:

```output
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            myResourceGroup  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            myResourceGroup  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             myResourceGroup  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             myResourceGroup  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Als **stress** is beëindigd op de eerste VM-exemplaren, wordt de gemiddelde CPU-belasting weer normaal. Na nog eens vijf minuten wordt het aantal VM-exemplaren vervolgens ingeschaald. Hierbij worden VM-exemplaren met de hoogste-id's als eerste verwijderd. Wanneer een schaalset gebruikmaakt van Beschikbaarheidssets of Beschikbaarheidszones, worden inschalingsacties gelijkmatig verdeeld over deze VM-exemplaren. In de volgende voorbeelduitvoer ziet u dat één VM-exemplaar wordt verwijderd als de schaalset wordt ingeschaald:

```output
           6  True                  eastus      myScaleSet_6  Deleting             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Sluit *watch* af met `Ctrl-c`. De capaciteit van de schaalset wordt nog steeds om de vijf minuten aangepast en er wordt dan één VM-exemplaar verwijderd totdat het minimum aantal exemplaren van twee is bereikt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de schaalset en aanvullende resources wilt verwijderen, verwijdert u de resourcegroep en alle bijbehorende resources met [az group delete](/cli/azure/group). De parameter `--no-wait` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid. De parameter `--yes` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een schaalset automatisch kunt in- of uitschalen met Azure CLI:

> [!div class="checklist"]
> * Automatisch schalen gebruiken met een schaalset
> * Regels voor automatisch schalen maken en gebruiken
> * Stresstest uitvoeren voor VM-exemplaren en regels voor automatisch schalen activeren
> * Automatisch terugschalen bij afname van de vraag
