---
title: VM Insights inschakelen met behulp van Resource Manager-sjablonen
description: In dit artikel wordt beschreven hoe u VM Insights inschakelt voor een of meer virtuele machines van Azure of schaal sets voor virtuele machines met behulp van Azure PowerShell-of Azure Resource Manager sjablonen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: fc0c304a3fea81f44e01d3e815f34e44728ea42e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031859"
---
# <a name="enable-vm-insights-using-resource-manager-templates"></a>VM Insights inschakelen met behulp van Resource Manager-sjablonen
In dit artikel wordt beschreven hoe u VM-inzichten kunt inschakelen voor een virtuele machine of virtuele-machine schaalset met behulp van Resource Manager-sjablonen. Deze procedure kan worden gebruikt voor het volgende:

- Azure virtuele machine
- Schaalset voor virtuele Azure-machines
- Hybride virtuele machine verbonden met Azure-boog

## <a name="prerequisites"></a>Vereisten

- [Een log Analytics-werk ruimte maken en configureren](./vminsights-configure-workspace.md). 
- Zie [ondersteunde besturings systemen](./vminsights-enable-overview.md#supported-operating-systems) om ervoor te zorgen dat het besturings systeem van de virtuele machine of virtuele-machine schaalset die u inschakelt, wordt ondersteund. 

## <a name="resource-manager-templates"></a>Resource Manager-sjablonen

Er zijn voor beelden gemaakt Azure Resource Manager sjablonen voor het voorbereiden van uw virtuele machines en virtuele-machine schaal sets. Deze sjablonen bevatten scenario's die u kunt gebruiken om bewaking in te scha kelen voor een bestaande resource en om een nieuwe resource te maken waarvoor bewaking is ingeschakeld.

>[!NOTE]
>De sjabloon moet worden geïmplementeerd in dezelfde resource groep als de virtuele machine of de schaalset voor virtuele machines die is ingeschakeld.


De Azure Resource Manager sjablonen worden in een archief bestand (. zip) vermeld dat u kunt [downloaden](https://aka.ms/VmInsightsARMTemplates) van onze github opslag plaats. Inhoud van het bestand bevat mappen die elk implementatie scenario vertegenwoordigen met een sjabloon en een parameter bestand. Voordat u ze uitvoert, wijzigt u het parameter bestand en geeft u de vereiste waarden op. 

Het Download bestand bevat de volgende sjablonen voor verschillende scenario's:

- Met de **ExistingVmOnboarding** -sjabloon wordt VM Insights ingeschakeld als de virtuele machine al bestaat.
- Met de **NewVmOnboarding** -sjabloon maakt u een virtuele machine en schakelt u in dat VM Insights deze kan controleren.
- Met de **ExistingVmssOnboarding** -sjabloon wordt VM Insights ingeschakeld als de schaalset van de virtuele machine al bestaat.
- Met de **NewVmssOnboarding** -sjabloon worden virtuele-machine schaal sets gemaakt en kunnen VM Insights deze controleren.
- Met **ConfigureWorkspace** -sjabloon configureert u uw log Analytics-werk ruimte ter ondersteuning van VM Insights door de oplossingen en verzameling van prestatie meter items voor Linux-en Windows-besturings systemen in te scha kelen.

>[!NOTE]
>Als de virtuele-machine schaal sets al aanwezig waren en het upgrade beleid is ingesteld op **hand matig**, wordt er standaard geen VM-inzichten geactiveerd na het uitvoeren van de **ExistingVmssOnboarding** -Azure Resource Manager sjabloon. U moet de exemplaren hand matig bijwerken.

## <a name="deploy-templates"></a>Sjablonen implementeren
De sjablonen kunnen worden geïmplementeerd met [een implementatie methode voor Resource Manager-sjablonen](../../azure-resource-manager/templates/deploy-powershell.md) , met inbegrip van de volgende voor beelden met behulp van Power shell en cli.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az deployment group create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>Volgende stappen

Nu de bewaking voor uw virtuele machines is ingeschakeld, is deze informatie beschikbaar voor analyse met VM Insights.

- Als u gedetecteerde toepassings afhankelijkheden wilt weer geven, raadpleegt u [VM Insights-toewijzing weer geven](vminsights-maps.md).

- Zie [Azure-VM-prestaties weer geven](vminsights-performance.md)om knel punten en het algehele gebruik van de VM-prestaties te identificeren.