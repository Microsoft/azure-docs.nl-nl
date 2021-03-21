---
title: 'Zelfstudie: een schaalset automatisch schalen met Azure-sjablonen'
description: Leer hoe u met sjablonen van Azure Resource Manager automatisch een schaalset met virtuele machines schaalt wanneer de vraag naar CPU toeneemt en afneemt.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 88cec878ca5d3ccab3a232888ff3a3c0b0faa1db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705248"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-an-azure-template"></a>Zelfstudie: Een virtuele-machineschaalset automatisch schalen met een Azure-sjabloon
Wanneer u een schaalset maakt, definieert u het aantal VM-exemplaren dat u wilt uitvoeren. Wanneer de vraag van de toepassing verandert, kunt u het aantal VM-exemplaren automatisch vergroten of verkleinen. De mogelijkheid van automatisch schalen stelt u in staat om altijd te voldoen aan de vraag van klanten houden of om gedurende de levenscyclus van uw app te reageren op wijzigingen in de prestaties van de toepassing. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Automatisch schalen gebruiken met een schaalset
> * Regels voor automatisch schalen maken en gebruiken
> * Stresstest uitvoeren voor VM-exemplaren en regels voor automatisch schalen activeren
> * Automatisch terugschalen bij afname van de vraag

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.29 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 


## <a name="define-an-autoscale-profile"></a>Een profiel voor automatisch schalen definiëren
U definieert een profiel voor automatisch schalen in een Azure-sjabloon met de resourceprovider *Microsoft.insights/autoscalesettings*. Een *profiel* bevat informatie over de capaciteit van de schaalset en eventuele gekoppelde regels. In het volgende voorbeeld wordt een profiel gedefinieerd met de naam *Autoscale by percentage based on CPU usage* en worden de minimum-, maximum- en standaardcapaciteit van *2* VM-exemplaren ingesteld op een maximum van *10*:

```json
{
"type": "Microsoft.insights/autoscalesettings",
"name": "Autoscale",
"apiVersion": "2015-04-01",
"location": "[variables('location')]",
"scale": null,
"properties": {
  "profiles": [
    {
      "name": "Autoscale by percentage based on CPU usage",
      "capacity": {
        "minimum": "2",
        "maximum": "10",
        "default": "2"
      }
    }
  ]
}
```


## <a name="define-a-rule-to-autoscale-out"></a>Een regel voor automatisch uitschalen definiëren
Als de vraag van uw toepassing toeneemt, neemt de belasting van de VM-exemplaren in de schaalset ook toe. Als deze toegenomen belasting consistent is, en geen piekbelasting is, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verhogen. Wanneer deze VM-exemplaren worden gemaakt en uw toepassingen worden geïmplementeerd, zorgt de schaalset ervoor dat er via de load balancer verkeer wordt gedistribueerd naar de exemplaren. U bepaalt welke meetwaarden gegevens moeten worden bewaakt, zoals CPU of schijf, hoe lang de belasting van de toepassing overeen moet komen met een bepaalde drempelwaarde en hoeveel VM-exemplaren er moeten worden toegevoegd aan de schaalset.

In het volgende voorbeeld definiëren we een regel waarmee het aantal VM-exemplaren in een schaalset wordt verhoogd wanneer de gemiddelde CPU-belasting gedurende een periode van vijf minuten groter dan 70% is. Wanneer de regel wordt geactiveerd, wordt het aantal VM-exemplaren met drie verhoogd.

De volgende parameters worden gebruikt voor deze regel:

| Parameter         | Uitleg                                                                                                         | Waarde           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | De prestatiemeetwaarde die u wilt bewaken en waarvoor u acties wilt toepassen op de schaalset.                                                   | Percentage CPU  |
| *timeGrain*       | Hoe vaak de meetwaarden worden verzameld voor analyse.                                                                   | 1 minuut        |
| *timeAggregation* | Hiermee definieert u hoe de verzamelde meetwaarden moeten worden samengevoegd voor analyse.                                                | Average         |
| *timeWindow*      | De hoeveelheid tijd waarna de meetwaarde en drempelwaarde met elkaar worden vergeleken.                                   | 5 minuten       |
| *operator*        | De operator die wordt gebruikt voor het vergelijken van de meetwaarden met de drempelwaarde.                                                     | Greater Than    |
| *threshold*       | De waarde die ervoor zorgt dat de regel voor automatisch schalen een actie activeert.                                                      | 70%             |
| *direction*       | Hiermee definieert u of de schaalset moet worden in- of uitgeschaald als de regel van toepassing is.                                              | Increase        |
| *type*            | Hiermee geeft u het aantal VM-exemplaren aan dat moet worden gewijzigd door een specifieke waarde.                                    | Change Count    |
| *value*           | Het aantal VM-exemplaren dat moet worden in- of uitgeschaald wanneer de regel van toepassing is.                                             | 3               |
| *cooldown*        | De tijd die moet worden gewacht voordat de regel opnieuw wordt toegepast, zodat de acties voor automatisch schalen voldoende tijd hebben om effectief te zijn. | 5 minuten       |

De volgende regel kan worden toegevoegd aan de profielsectie van de resourceprovider *Microsoft.insights/autoscalesettings* uit het vorige gedeelte:

```json
"rules": [
  {
    "metricTrigger": {
      "metricName": "Percentage CPU",
      "metricNamespace": "",
      "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
      "metricResourceLocation": "[variables('location')]",
      "timeGrain": "PT1M",
      "statistic": "Average",
      "timeWindow": "PT5M",
      "timeAggregation": "Average",
      "operator": "GreaterThan",
      "threshold": 70
    },
    "scaleAction": {
      "direction": "Increase",
      "type": "ChangeCount",
      "value": "3",
      "cooldown": "PT5M"
    }
  }
]
```


## <a name="define-a-rule-to-autoscale-in"></a>Een regel voor automatisch inschalen definiëren
In het weekend of 's avonds kan de vraag voor uw toepassing afnemen. Als deze afgenomen belasting consistent is gedurende een bepaalde periode, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verlagen. Deze inschaalactie reduceert de kosten voor het uitvoeren van uw schaalset, aangezien u alleen het aantal exemplaren uitvoert dat vereist is om te voldoen aan de actuele vraag.

In het volgende voorbeeld wordt een regel gedefinieerd waarmee het aantal VM-exemplaren in een schaalset met één wordt verlaagd wanneer de gemiddelde CPU-belasting gedurende een periode van vijf minuten lager dan 30% is. Deze regel kan worden toegevoegd aan het profiel voor automatisch schalen, na de vorige regel voor uitschalen:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
    "metricResourceLocation": "[variables('location')]",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-an-autoscaling-scale-set"></a>Een schaalset voor automatisch schalen maken
We gebruiken een voorbeeldsjabloon om een schaalset te maken en regels voor automatisch schalen toe te passen. U kunt [de volledige sjabloon bekijken](https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json) of [de sectie *Microsoft.insights/autoscalesettings* met de resourceprovider](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/autoscale.json#L220) van de sjabloon.

Maak eerst een resourcegroep met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak nu een schaalset voor virtuele machines met [az deployment group create](/cli/azure/deployment/group). Geef desgevraagd uw eigen gebruikersnaam en wachtwoord op, zoals *azureuser*, voor gebruik als de referenties voor elk VM-exemplaar:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.


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
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Als **stress** is beëindigd op de eerste VM-exemplaren, wordt de gemiddelde CPU-belasting weer normaal. Na nog eens vijf minuten wordt het aantal VM-exemplaren vervolgens ingeschaald. Hierbij worden VM-exemplaren met de hoogste-id's als eerste verwijderd. Wanneer een schaalset gebruikmaakt van Beschikbaarheidssets of Beschikbaarheidszones, worden inschalingsacties gelijkmatig verdeeld over deze VM-exemplaren. In de volgende voorbeelduitvoer ziet u dat één VM-exemplaar wordt verwijderd als de schaalset wordt ingeschaald:

```output
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Sluit *watch* af met `Ctrl-c`. De capaciteit van de schaalset wordt nog steeds om de vijf minuten aangepast en er wordt dan één VM-exemplaar verwijderd totdat het minimum aantal exemplaren van twee is bereikt.


## <a name="clean-up-resources"></a>Resources opschonen
Als u de schaalset en aanvullende resources wilt verwijderen, verwijdert u de resourcegroep en alle bijbehorende resources met [az group delete](/cli/azure/group):

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
