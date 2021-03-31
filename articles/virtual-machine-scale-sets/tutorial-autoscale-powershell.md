---
title: 'Zelfstudie: een schaalset automatisch schalen met Azure PowerShell'
description: Leer hoe u met Azure PowerShell automatisch een schaalset met virtuele machines schaalt wanneer de vraag naar CPU toeneemt en afneemt.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: 8ee124f866a5241620671ff84c24f3713f62efe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89078466"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Zelfstudie: Een virtuele-machineschaalset automatisch schalen met Azure PowerShell

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Wanneer u een schaalset maakt, definieert u het aantal VM-exemplaren dat u wilt uitvoeren. Wanneer de vraag van de toepassing verandert, kunt u het aantal VM-exemplaren automatisch vergroten of verkleinen. De mogelijkheid van automatisch schalen stelt u in staat om altijd te voldoen aan de vraag van klanten houden of om gedurende de levenscyclus van uw app te reageren op wijzigingen in de prestaties van de toepassing. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Automatisch schalen gebruiken met een schaalset
> * Regels voor automatisch schalen maken en gebruiken
> * Stresstest uitvoeren voor VM-exemplaren en regels voor automatisch schalen activeren
> * Automatisch terugschalen bij afname van de vraag

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Er is een bekend probleem dat van invloed is op de Azure PowerShell-moduleversie 6.8.1 of hoger, inclusief de huidige versie van de Azure Cloud Shell. Voor deze zelfstudie kan alleen moduleversie 6.0.0 tot 6.8.0 van Azure PowerShell worden gebruikt. Voer `Get-Module -ListAvailable AzureRM` uit om de versie te bekijken. Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzureRmAccount` uitvoeren om verbinding te kunnen maken met Azure.


## <a name="create-a-scale-set"></a>Een schaalset maken
Het is makkelijker om de regels voor automatisch schalen te maken als u eerst enkele variabelen definieert voor uw schaalset. In het volgende voorbeeld worden variabelen gedefinieerd voor de schaalset met de naam *myScaleSet* in de resourcegroep met de naam *myResourceGroup* in de regio *VS - oost*. Uw abonnements-id kunt u opvragen met [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Als er abonnementen aan uw account zijn gekoppeld, wordt alleen het eerste abonnement geretourneerd. Pas de namen en de abonnements-id als volgt aan:

```azurepowershell-interactive
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroup"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```

Maak nu een virtuele-machineschaalset met [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Om het verkeer te distribueren naar de verschillende VM-exemplaren, wordt er ook een load balancer gemaakt. De load balancer bevat regels voor het distribueren van verkeer op TCP-poort 80, en voor het toestaan van verkeer van Extern bureaublad op TCP-poort 3389 en externe toegang via PowerShell op TCP-poort 5985. Geef desgevraagd uw eigen beheerdersreferenties op voor de VM-exemplaren in de schaalset:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName $myResourceGroup `
  -VMScaleSetName $myScaleSet `
  -Location $myLocation `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer"
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.

## <a name="create-a-rule-to-autoscale-out"></a>Een regel maken voor automatisch uitschalen
Als de vraag van uw toepassing toeneemt, neemt de belasting van de VM-exemplaren in de schaalset ook toe. Als deze toegenomen belasting consistent is, en geen piekbelasting is, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verhogen. Wanneer deze VM-exemplaren worden gemaakt en uw toepassingen worden geïmplementeerd, zorgt de schaalset ervoor dat er via de load balancer verkeer wordt gedistribueerd naar de exemplaren. U bepaalt welke meetwaarden gegevens moeten worden bewaakt, zoals CPU of schijf, hoe lang de belasting van de toepassing overeen moet komen met een bepaalde drempelwaarde en hoeveel VM-exemplaren er moeten worden toegevoegd aan de schaalset.

We gaan een regel maken met [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) waarmee het aantal VM-exemplaren in een schaalset wordt verhoogd wanneer de gemiddelde CPU-belasting gedurende een periode van vijf minuten groter dan 70% is. Wanneer de regel wordt geactiveerd, wordt het aantal VM-exemplaren met drie verhoogd.

De volgende parameters worden gebruikt voor deze regel:

| Parameter               | Uitleg                                                                                                         | Waarde          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | De prestatiemeetwaarde die u wilt bewaken en waarvoor u acties wilt toepassen op de schaalset.                                                   | Percentage CPU |
| *-TimeGrain*            | Hoe vaak de meetwaarden worden verzameld voor analyse.                                                                   | 1 minuut       |
| *-MetricStatistic*      | Hiermee definieert u hoe de verzamelde meetwaarden moeten worden samengevoegd voor analyse.                                                | Average        |
| *-TimeWindow*           | De hoeveelheid tijd waarna de meetwaarde en drempelwaarde met elkaar worden vergeleken.                                   | 5 minuten      |
| *-Operator*             | De operator die wordt gebruikt voor het vergelijken van de meetwaarden met de drempelwaarde.                                                     | Greater Than   |
| *-Threshold*            | De waarde die ervoor zorgt dat de regel voor automatisch schalen een actie activeert.                                                      | 70%            |
| *-ScaleActionDirection* | Hiermee definieert u of de schaalset moet worden in- of uitgeschaald als de regel van toepassing is.                                             | Increase       |
| *-ScaleActionScaleType* | Hiermee geeft u het aantal VM-exemplaren aan dat moet worden gewijzigd door een specifieke waarde.                                    | Change Count   |
| *-ScaleActionValue*     | Het percentage VM-exemplaren dat moet worden gewijzigd wanneer de regel wordt geactiveerd.                                            | 3              |
| *-ScaleActionCooldown*  | De tijd die moet worden gewacht voordat de regel opnieuw wordt toegepast, zodat de acties voor automatisch schalen voldoende tijd hebben om effectief te zijn. | 5 minuten      |

In het volgende voorbeeld wordt een object met de naam *myRuleScaleOut* gemaakt dat deze regel voor opschalen bevat. De parameter *-MetricResourceId* gebruikt de variabelen die eerder zijn gedefinieerd voor de abonnements-id, de naam van een resourcegroep en de naam van de schaalset:

```azurepowershell-interactive
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic "Average" `
  -TimeWindow 00:05:00 `
  -Operator "GreaterThan" `
  -Threshold 70 `
  -ScaleActionDirection "Increase" `
  -ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 3 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-autoscale-in"></a>Een regel maken voor automatisch inschalen
In het weekend of 's avonds kan de vraag voor uw toepassing afnemen. Als deze afgenomen belasting consistent is gedurende een bepaalde periode, kunt u regels voor automatisch schalen configureren om het aantal VM-exemplaren in de schaalset te verlagen. Deze inschaalactie reduceert de kosten voor het uitvoeren van uw schaalset, aangezien u alleen het aantal exemplaren uitvoert dat vereist is om te voldoen aan de actuele vraag.

We gaan nu met [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) een regel maken waarmee het aantal VM-exemplaren in een schaalset wordt verlaagd wanneer de gemiddelde CPU-belasting gedurende een periode van vijf minuten lager dan 30% is. Wanneer de regel wordt geactiveerd, wordt het aantal VM-exemplaren met een verlaagd. In het volgende voorbeeld wordt een object met de naam *myRuleScaledown* gemaakt dat deze regel voor omlaag schalen bevat. De parameter *-MetricResourceId* gebruikt de variabelen die eerder zijn gedefinieerd voor de abonnements-id, de naam van een resourcegroep en de naam van de schaalset:

```azurepowershell-interactive
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator "LessThan" `
  -MetricStatistic "Average" `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection "Decrease" `
  -ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 1
```


## <a name="define-an-autoscale-profile"></a>Een profiel voor automatisch schalen definiëren
U koppelt de regels voor automatisch schalen aan een schaalset door een profiel te maken. In dit profiel voor automatisch schalen worden de standaard-, maximum- en minimumcapaciteit ingesteld voor de schaalset en worden de regels voor automatisch schalen gekoppeld. Maak een profiel voor automatisch schalen met [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). In het volgende voorbeeld worden de minimum- en standaardcapaciteit ingesteld op *2* VM-exemplaren en het maximum op *10*. De regels voor uit- en inschalen die zijn gemaakt in de voorgaande stappen, worden vervolgens gekoppeld:

```azurepowershell-interactive
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-profile-to-a-scale-set"></a>Profiel voor automatisch schalen toepassen op een schaalset
De laatste stap is het toepassen van het profiel voor automatisch schalen op uw schaalset. De schaalset kan dan vervolgens automatisch in- of uitschalen op basis van de vraag van de toepassing. U kunt het profiel als volgt toepassen met [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting):

```azurepowershell-interactive
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfile $myScaleProfile
```


## <a name="generate-cpu-load-on-scale-set"></a>CPU-belasting genereren voor schaalset
Voor het testen van de regels voor automatisch schalen gaan we wat CPU-belasting genereren voor de VM-exemplaren in de schaalset. Deze gesimuleerde CPU-belasting zorgt ervoor dat de schaalset automatisch wordt uitgeschaald en het aantal VM-exemplaren dus wordt verhoogd. Als de gesimuleerde CPU-belasting vervolgens weer afneemt, wordt de schaalset ingeschaald en zakt het aantal VM-exemplaren.

Als u de NAT-poorten wilt opvragen die u moet verbinden met VM-exemplaren in een schaalset, moet u eerst het load balancer-object opvragen met [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Bekijk vervolgens de binnenkomende NAT-regels met [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig):

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

In de volgende voorbeelduitvoer ziet u de exemplaarnaam, het openbare IP-adres van de load balancer en het poortnummer waarnaar de NAT-regels verkeer doorsturen:

```powershell
Name        Protocol FrontendPort BackendPort
----        -------- ------------ -----------
myRDPRule.0 Tcp             50001        3389
myRDPRule.1 Tcp             50002        3389
```

De *naam* van de regel komt overeen met de naam van het VM-exemplaar, zoals wordt weergegeven in een eerdere [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm)-opdracht. Als u bijvoorbeeld verbinding wilt maken met het VM-exemplaar *0*, gebruikt u *myRDPRule.0* en maakt u verbinding met poort *50001*. Als u verbinding wilt maken met het VM-exemplaar *1*, gebruikt u de waarde *myRDPRule.1* en maakt u verbinding met poort *50002*.

Bekijk het openbare IP-adres van de load balancer met [Get-AzureRmPublicIPAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIP | Select IpAddress
```

Voorbeelduitvoer:

```powershell
IpAddress
---------
52.168.121.216
```

Maak een externe verbinding met uw eerste VM-exemplaar. Geef uw eigen openbare IP-adres en poortnummer van het vereiste VM-exemplaar op, zoals is gedaan in de voorgaande opdrachten. Voer desgevraagd de referenties in die u hebt gebruikt bij het maken van de schaalset (standaard *azureuser* en *P\@ssw0rd!* in de voorbeeldopdrachten). Als u Azure Cloud Shell gebruikt, voert u deze stap uit vanaf een lokale PowerShell-prompt of vanuit de Extern bureaublad-client. In het volgende voorbeeld wordt verbinding gemaakt met het VM-exemplaar *0*:

```powershell
mstsc /v 52.168.121.216:50001
```

Als u bent aangemeld, opent u Internet Explorer via de taakbalk.

- Selecteer **OK** om te bevestigen dat u de *aanbevolen instellingen voor beveiliging, privacy en compatibiliteit wilt gebruiken*.
- Typ *http://download.sysinternals.com/files/CPUSTRES.zip* in de adresbalk.
- Als verbeterde beveiliging van Internet Explorer wordt ingeschakeld, kiest u **Toevoegen** om het domein *http://download.sysinternals.com* toe te voegen aan de lijst met vertrouwde sites.
- Als u wordt gevraagd het bestand te downloaden, selecteert u **Openen**, selecteert u het hulpprogramma *CPUSTRES. EXE* en kiest u **Uitvoeren**.

U kunt CPU-belasting genereren door voor twee threads het selectievakje **Actief** in te schakelen. Selecteer voor beide threads *Maximum* in de vervolgkeuzelijst **Activiteit**. U kunt Taakbeheer openen om te controleren of de CPU-belasting op de VM 100% is.

![Het hulpprogramma CPUStres genereert belasting op het VM-exemplaar](media/tutorial-autoscale-powershell/cpu-stress-load.PNG)

Laat de sessie met Extern bureaublad geopend en start een andere verbinding met Extern bureaublad. Maak verbinding met het tweede VM-exemplaar via het poortnummer dat u hebt opgevraagd met de vorige cmdlet [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig):

```powershell
mstsc /v 52.168.121.216:50002
```

Zodra u aangemeld bij het tweede VM-exemplaar, herhaalt u de vorige stappen om *CPUSTRES. EXE* te downloaden en uitvoeren. Start opnieuw twee **Actieve** threads en stel de activiteit in op *Maximum*.

Het hulpprogramma **CPUStres** wordt uitgevoerd zolang u de beide Extern bureaublad-sessies geopend houdt.


## <a name="monitor-the-active-autoscale-rules"></a>De actieve regels voor automatisch schalen bewaken
Gebruik **while** om het aantal VM-exemplaren in de schaalset te controleren. Het duurt vijf minuten voordat de regels voor automatisch schalen het proces van uitschalen starten in reactie op de CPU-belasting die met **CPUStres** wordt gegenereerd op elk van de VM-exemplaren:

```azurepowershell-interactive
while (1) {Get-AzureRmVmssVM `
    -ResourceGroupName $myResourceGroup `
    -VMScaleSetName $myScaleSet; sleep 10}
```

Als de CPU-drempelwaarde is bereikt, zorgen de regels voor automatisch schalen ervoor dat het aantal VM-exemplaren in de schaalset wordt verhoogd. In de volgende uitvoer ziet u dat er drie VM's worden gemaakt op het moment dat de schaalset gaat uitschalen:

```powershell
ResourceGroupName         Name Location          Sku Capacity InstanceID ProvisioningState
-----------------         ---- --------          --- -------- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_2   eastus Standard_DS2                   2         Succeeded
MYRESOURCEGROUP   myScaleSet_3   eastus Standard_DS2                   3         Succeeded
MYRESOURCEGROUP   myScaleSet_4   eastus Standard_DS2                   4          Creating
MYRESOURCEGROUP   myScaleSet_5   eastus Standard_DS2                   5          Creating
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Creating
```

Sluit **CPUStres** af in de sessies van Extern bureaublad met de VM-exemplaren. De gemiddelde CPU-belasting van de schaalset is nu weer normaal. Na nog eens vijf minuten wordt het aantal VM-exemplaren vervolgens ingeschaald. Hierbij worden VM-exemplaren met de hoogste-id's als eerste verwijderd. Wanneer een schaalset gebruikmaakt van Beschikbaarheidssets of Beschikbaarheidszones, worden inschalingsacties gelijkmatig verdeeld over deze VM-exemplaren. In de volgende voorbeelduitvoer ziet u dat één VM-exemplaar wordt verwijderd als de schaalset wordt ingeschaald:

```powershell
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Deleting
```

Sluit *while* af met `Ctrl-c`. De capaciteit van de schaalset wordt nog steeds om de vijf minuten aangepast en er wordt dan één VM-exemplaar verwijderd totdat het minimum aantal exemplaren van twee is bereikt.


## <a name="clean-up-resources"></a>Resources opschonen
Als u de schaalset en aanvullende resources wilt verwijderen, verwijdert u de resourcegroep en alle bijbehorende resources met [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). De parameter `-Force` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen. De parameter `-AsJob` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u een schaalset automatisch kunt in- of uitschalen met Azure PowerShell:

> [!div class="checklist"]
> * Automatisch schalen gebruiken met een schaalset
> * Regels voor automatisch schalen maken en gebruiken
> * Stresstest uitvoeren voor VM-exemplaren en regels voor automatisch schalen activeren
> * Automatisch terugschalen bij afname van de vraag
