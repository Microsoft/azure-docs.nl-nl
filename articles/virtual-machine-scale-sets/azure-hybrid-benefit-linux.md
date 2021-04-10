---
title: Azure Hybrid Benefit voor schaal sets voor virtuele Linux-machines
description: Meer informatie over hoe Azure Hybrid Benefit kunnen worden toegepast op schaal sets voor virtuele machines om u te helpen geld te besparen op uw virtuele Linux-machines die worden uitgevoerd op Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machine-scale-sets
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/20/2021
ms.author: mathapli
ms.openlocfilehash: a714434c39a0c40c2e908f2d0c424f02851921a6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933675"
---
# <a name="azure-hybrid-benefit-for-linux-virtual-machine-scale-set-public-preview"></a>Azure Hybrid Benefit voor virtuele Linux-machine schaal sets (open bare preview)

De **Azure Hybrid Benefit voor de schaalset voor virtuele Linux-machines bevindt zich nu in de open bare preview**. AHB-voor deel helpt u bij het verlagen van de kosten van het uitvoeren van uw RHEL-en SLES- [schaal sets voor virtuele machines](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

Met dit voor deel betaalt u alleen voor de kosten van de infra structuur van uw schaalset. Het voor deel is beschikbaar voor alle installatie kopieën van RHEL en SLES Marketplace pay-as-u-go (PAYG).


>[!NOTE]
> In dit artikel wordt de Azure Hybrid Benefit voor Linux VMSS beschreven. Er is een afzonderlijk [artikel beschikbaar in deze [Ahb voor Linux-vm's](https://docs.microsoft.com/azure/virtual-machines/linux/azure-hybrid-benefit-linux), die sinds november 2020 al beschikbaar zijn voor Azure-klanten.

## <a name="benefit-description"></a>Beschrijving van voor deel
Met Azure Hybrid kunt u de bestaande Cloud Access-licenties van Red Hat of SUSE gebruiken en kunt u de virtuele-machine Scale set-instanties voor het maken van uw BYOS-facturering (uw eigen abonnement) omzetten. 

De schaalset voor virtuele machines die via PAYG Marketplace-installatie kopieën worden geïmplementeerd, brengt zowel de infra structuur als de software kosten in rekening. Met AHB kunnen PAYG-instanties worden geconverteerd naar een BYOS-facturerings model zonder opnieuw te worden geïmplementeerd.

:::image type="content" source="./media/azure-hybrid-benefit-linux/azure-hybrid-benefit-linux-cost.png" alt-text="Azure Hybrid Benefit kosten visualisatie op Linux-Vm's.":::

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux"></a>Bereik van Azure Hybrid Benefit geschiktheid voor Linux
Azure Hybrid Benefit is beschikbaar voor alle RHEL-en SLES-PAYG-installatie kopieën van Azure Marketplace. Het voor deel is nog niet beschikbaar voor RHEL-of SLES BYOS-afbeeldingen of aangepaste installatie kopieën van Azure Marketplace.

Azure dedicated host instances en SQL Hybrid bene fits komen niet in aanmerking voor Azure Hybrid Benefit als u het voor deel van Linux-Vm's al gebruikt.

## <a name="get-started"></a>Aan de slag

### <a name="red-hat-customers"></a>Red Hat-klanten

Azure Hybrid Benefit voor RHEL is beschikbaar voor Red Hat-klanten die aan beide criteria voldoen:

- Actieve of ongebruikte RHEL-abonnementen hebben die in aanmerking komen voor gebruik in azure
- Een of meer van deze abonnementen hebben ingeschakeld voor gebruik in azure met het [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) -programma

> [!IMPORTANT]
> Zorg ervoor dat het juiste abonnement is ingeschakeld op het [Cloud-Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) -programma.

Begin met het gebruik van het voor deel voor Red Hat:

1. Schakel uw in aanmerking komende RHEL-abonnementen in Azure in met behulp van de [Red Hat Cloud Access-klant interface](https://access.redhat.com/management/cloud).

   De Azure-abonnementen die u tijdens het activerings proces van Red Hat Cloud Access opgeeft, mogen vervolgens de functie Azure Hybrid Benefit gebruiken.
1. Pas Azure Hybrid Benefit toe op een van de bestaande en nieuwe RHEL PAYG virtuele-machine schaal sets. U kunt Azure Portal of Azure CLI gebruiken om het voor deel in te scha kelen.
1. Volg de aanbevolen [volgende stappen](https://access.redhat.com/articles/5419341) voor het configureren van update bronnen voor uw RHEL-vm's en voor RHEL-nalevings richtlijnen voor het abonnement.


### <a name="suse-customers"></a>SUSE-klanten

Ga als volgt te beginnen met het gebruik van het voor deel voor SUSE:

1. Meld u aan bij het SUSE open bare Cloud programma.
1. Pas het voor deel toe op uw zojuist gemaakte of bestaande schaalset voor virtuele machines via de Azure Portal of Azure CLI.
1. Registreer uw Vm's die het voor deel ontvangen met een afzonderlijke bron van updates.


## <a name="enable-and-disable-the-benefit-on-azure-portal"></a>Het voor deel in azure Portal in-en uitschakelen 
De portal-ervaring voor het inschakelen en uitschakelen van AHB op de virtuele-machine schaalset is **momenteel niet beschikbaar**.

## <a name="enable-and-disable-the-benefit-using-azure-cli"></a>De voor delen in-en uitschakelen met Azure CLI

U kunt de `az vmss update` opdracht gebruiken om bestaande vm's bij te werken. Voor RHEL Vm's voert u de opdracht uit met een `--license-type` para meter van `RHEL_BYOS` . Voor SLES Vm's voert u de opdracht uit met een `--license-type` para meter van `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>CLI-voor beeld om het voor deel in te scha kelen
```azurecli
# This will enable the benefit on a RHEL VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>CLI-voor beeld om het voor deel uit te scha kelen
Als u het voor deel wilt uitschakelen, gebruikt u de `--license-type` waarde `None` :

```azurecli
# This will disable the benefit on a VM
az vmss update -g myResourceGroup -n myVmName --license-type None
```

>[!NOTE]
> Schaal sets hebben een [' upgrade beleid '](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) dat bepaalt hoe vm's up-to-date worden gebracht met het nieuwste model voor schaal sets. Als uw VMSS echter ' automatisch ' upgrade beleid heeft, wordt AHB-voor deel automatisch toegepast wanneer de VM-exemplaren worden bijgewerkt. Als VMSS ' Rolling '-upgrade beleid heeft, op basis van de geplande updates, wordt AHB toegepast.
In het geval van ' hand matig ' upgrade beleid moet u ' hand matige upgrade ' uitvoeren van elke bestaande VM.  

### <a name="cli-example-to-upgrade-virtual-machine-scale-set-instances-in-case-of-manual-upgrade-policy"></a>CLI-voor beeld voor het bijwerken van exemplaren van de schaalset voor virtuele machines in het geval van het beleid ' hand matig upgraden ' 
```azurecli
# This will bring VMSS instances up to date with latest VMSS model 
az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
```

## <a name="apply-the-azure-hybrid-benefit-at-virtual-machine-scale-set-create-time"></a>De Azure Hybrid Benefit Toep assen op de schaalset voor virtuele machines maken 
Naast het Toep assen van de Azure Hybrid Benefit op een bestaande set met betalen per gebruik, kunt u deze aanroepen op het moment dat de schaalset voor virtuele machines wordt gemaakt. Dit zijn de voor delen van threefold:
- U kunt PAYG-en BYOS-instanties voor virtuele machines inrichten met behulp van dezelfde installatie kopie en hetzelfde proces.
- Er worden toekomstige wijzigingen in de licentie modus ingeschakeld, iets niet alleen beschikbaar met een BYOS-installatie kopie.
- De exemplaren van de virtuele-machine schaalset worden standaard verbonden met de Red Hat Update infrastructure (RHUI) om ervoor te zorgen dat deze up-to-date en beveiligd blijft. U kunt het bijgewerkte mechanisme na implementatie op elk gewenst moment wijzigen.

### <a name="cli-example-to-create-virtual-machine-scale-set-with-ahb-benefit"></a>CLI-voor beeld voor het maken van een schaalset voor virtuele machines met AHB-voor deel
```azurecli
# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type RHEL_BYOS 

# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type SLES_BYOS
```

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het maken en bijwerken van Vm's en het toevoegen van licentie typen (RHEL_BYOS, SLES_BYOS) voor Azure Hybrid Benefit met behulp van de Azure CLI](/cli/azure/vmss)
