---
title: Azure Hybrid Benefit-en Linux-Vm's
description: Met de Azure Hybrid Benefit kunt u geld besparen op uw virtuele Linux-machines die worden uitgevoerd op Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 35bed58f95deebb78d8e787c8bc3f522ce7004df
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326966"
---
# <a name="azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Azure Hybrid Benefit: hoe dit van toepassing is voor Linux Virtual Machines

## <a name="overview"></a>Overzicht

Met Azure Hybrid Benefit kunt u uw virtuele machines (Vm's) van on-Red Hat Enterprise Linux premises (RHEL) en SUSE Linux Enterprise Server (SLES) eenvoudiger migreren naar Azure met behulp van uw eigen reeds bestaande Red Hat-of SUSE-software abonnement. Met dit voor deel betaalt u alleen voor de kosten van de infra structuur van uw VM omdat de software kosten worden gedekt door uw RHEL-of SLES-abonnement. Het voor deel is van toepassing op alle RHEL-installatie kopieën (PAYG) van de SLES Marketplace.

> [!IMPORTANT]
> Azure Hybrid Benefit voor Linux-Vm's is nu beschikbaar voor openbaar


## <a name="benefit-description"></a>Beschrijving van voor deel

Via Azure Hybrid Benefit kunt u eenvoudig uw on-premises RHEL-en SLES-servers naar Azure migreren door bestaande RHEL-en SLES PAYG-Vm's te converteren naar Azure om uw eigen abonnement (BYOS) in te zetten. Virtuele machines die zijn geïmplementeerd op basis van PAYG-installatie kopieën in azure, bevatten doorgaans kosten voor de infra structuur en software kosten. Met de Azure Hybrid Benefit kunnen PAYG Vm's worden geconverteerd naar een BYOS facturerings model zonder een herimplementatie, waardoor uitval tijd risico wordt voor komen.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure Hybrid Benefit kosten visualisatie op Linux-Vm's.":::

Als u het voor deel inschakelt op een RHEL-of SLES-VM, worden er geen kosten in rekening gebracht voor de extra software die doorgaans op een PAYG-VM is gemaakt. In plaats daarvan begint uw VM met het uitbrengen van de kosten voor BYOS, die alleen de kosten voor de berekenings-hardware en geen software kosten bevatten.

Indien gewenst kunt u ook een virtuele machine converteren waarvoor het voor deel is ingeschakeld op het PAYG-facturerings model.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Bereik van Azure Hybrid Benefit geschiktheid voor Linux-Vm's

Azure Hybrid Benefit is beschikbaar voor alle RHEL-en SLES Marketplace PAYG-installatie kopieën. Het voor deel is nog niet beschikbaar voor RHEL of SLES Marketplace BYOS-installatie kopieën of aangepaste installatie kopieën.

Gereserveerde instanties, toegewezen hosts en SQL Hybrid bene fits komen niet in aanmerking voor de Azure Hybrid Benefit als u het voor deel van Linux-Vm's al gebruikt.

## <a name="how-to-get-started"></a>Hoe gaat u aan de slag

### <a name="red-hat-customers"></a>Red Hat-klanten

Azure Hybrid Benefit voor RHEL is beschikbaar voor klanten met een actief/ongebruikte RHEL-abonnement die in aanmerking komen voor gebruik in Azure en waarvoor een of meer van deze abonnementen zijn ingeschakeld voor gebruik in azure met het [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) Program. 

1.  Schakel een of meer van uw in aanmerking komende RHEL-abonnementen in voor gebruik in azure met behulp van de [Red Hat Cloud Access-klant interface](https://access.redhat.com/management/cloud).
1.  De Azure-abonnement (en) die u hebt ingevoerd tijdens het activerings proces van Red Hat Cloud Access, kunnen vervolgens de functie Azure Hybrid Benefit gebruiken.
1.  Pas de Azure Hybrid Benefit toe op een van uw bestaande RHEL PAYG-Vm's en op alle nieuwe RHEL Vm's die u implementeert vanuit Azure Marketplace PAYG-installatie kopieën.
1.  Volg de aanbevolen [volgende stappen](https://access.redhat.com/articles/5419341) voor het configureren van update bronnen voor uw RHEL-vm's en voor RHEL-nalevings richtlijnen voor het abonnement.


### <a name="suse-customers"></a>SUSE-klanten

1.    Registreren bij het SUSE open bare-Cloud programma
1.    Pas het voor deel toe op uw bestaande Vm's via Azure CLI
1.    Registreer uw Vm's om het voor deel te ontvangen met een afzonderlijke bron van updates


### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Het voor deel in de Azure CLI inschakelen en uitschakelen

U kunt de opdracht AZ VM update gebruiken om bestaande Vm's bij te werken. Voor RHEL Vm's voert u de opdracht uit met de para meter---type licentie van RHEL_BYOS. Voor SLES Vm's voert u de opdracht uit met de para meter---type licentie van SLES_BYOS.

#### <a name="cli-example-to-enable-the-benefit"></a>CLI-voor beeld om het voor deel in te scha kelen:
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>CLI-voor beeld om het voor deel uit te scha kelen:
Als u het voor deel wilt uitschakelen, gebruikt u de waarde geen in licentie type
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>CLI-voor beeld om het voor deel in te scha kelen voor een groot aantal Vm's
Als u het voor deel voor een groot aantal Vm's wilt inschakelen, kunt u de `--ids` para meter in de Azure CLI gebruiken.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

In de volgende voor beelden ziet u twee methoden voor het ophalen van een lijst met resource-Id's, één op het niveau van de resource groep, één op het abonnements niveau.
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>De AHB-status van een virtuele machine controleren
U kunt de status van de AHB van een virtuele machine op twee manieren weer geven: met behulp van Azure CLI of Azure Instance Metadata Service (Azure IMDS).


### <a name="azure-cli"></a>Azure CLI

De `az vm get-instance-view` opdracht kan worden gebruikt voor dit doel einde. Zoek naar een License type veld in het antwoord. Als het veld License type bestaat en de waarde RHEL_BYOS of SLES_BYOS is, is voor uw virtuele machine het voor deel ingeschakeld.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azire Instance Metadata Service

Vanuit de VM zelf kunt u een query uitvoeren op de IMDS Attestende meta gegevens om de License type van de virtuele machine te bepalen. De License type-waarde ' RHEL_BYOS ' of ' SLES_BYOS ' geeft aan dat het voor deel van uw virtuele machine is ingeschakeld. Meer informatie over [hier](./instance-metadata-service.md#attested-data) Geattesteerde meta gegevens

## <a name="compliance"></a>Naleving

### <a name="red-hat"></a>Red Hat

Klanten die gebruikmaken van de Azure Hybrid Benefit voor RHEL, komen overeen met de standaard [juridische voor waarden](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) en [privacyverklaring](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) die zijn gekoppeld aan de Azure Marketplace RHEL-aanbiedingen.

Klanten die Azure Hybrid Benefit voor RHEL gebruiken, hebben drie opties voor het leveren van software-updates en-patches voor deze Vm's:

1.  [Red Hat Update infrastructure (RHUI)](../workloads/redhat/redhat-rhui.md) (standaard optie)
1.  Red Hat Satellite-Server
1.  Red Hat Subscription Manager

Klanten die de RHUI-optie kiezen, kunnen RHUI blijven gebruiken als de belangrijkste update bron voor hun AHB RHEL-Vm's, zonder dat er RHEL-abonnementen aan deze Vm's worden gekoppeld.  Klanten die de RHUI-optie kiezen, zijn verantwoordelijk voor het garanderen van de naleving van het RHEL-abonnement.

Klanten die een Red Hat Satellite-Server of Red Hat Subscription manager kiezen, moeten de RHUI-configuratie verwijderen en vervolgens een RHEL-abonnement voor Cloud toegang toevoegen aan hun AHB RHEL VM ('s).  

Meer informatie over de naleving van Red Hat-abonnementen, software-updates en bronnen voor AHB RHEL Vm's vindt u [hier](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Als u Azure Hybrid Benefit voor uw SLES-Vm's wilt gebruiken, moet u eerst zijn geregistreerd bij het SUSE open bare Cloud-programma. Meer informatie over het programma vindt u hier. Zodra u SUSE-abonnementen hebt aangeschaft, moet u uw virtuele machines met behulp van de abonnementen registreren op uw eigen bron van updates via het SUSE-klanten centrum, de server voor abonnements beheer of de beheerder van SUSE.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
*V: kan ik het licentie type ' RHEL_BYOS ' gebruiken met een SLES-installatie kopie of vice versa?*

A: Nee, dat kan niet. Als u een licentie type opgeeft dat onjuist overeenkomt met de distributie die op uw virtuele machine wordt uitgevoerd, worden er geen meta gegevens voor facturering bijgewerkt. Als u echter per ongeluk het verkeerde licentie type invoert, wordt het voor deel nog steeds mogelijk als u uw VM opnieuw bijwerkt naar het juiste licentie type.

*V: Ik heb een Red Hat Cloud-toegang geregistreerd, maar ik kan het voor deel van mijn RHEL-Vm's nog steeds niet inschakelen. Wat moet ik doen?*

A: het kan enige tijd duren voordat de inschrijving van uw Red Hat Cloud Access-abonnement van Red Hat naar Azure wordt door gegeven. Neem contact op met micro soft ondersteuning als u na één werkdag nog steeds de fout ziet.

*V: Ik heb een virtuele machine geïmplementeerd met behulp van een RHEL BYOS ' gouden installatie kopie '. Kan ik de facturering voor deze afbeeldingen converteren van BYOS naar PAYG?*

A: Nee, dat kan niet. Azure Hybrid Benefit ondersteunt alleen conversie op installatie kopieën met betalen per gebruik.

*V: Ik heb een virtuele machine geïmplementeerd met behulp van een RHEL BYOS ' gouden installatie kopie '. Kan ik de facturering voor deze afbeeldingen converteren van BYOS naar PAYG?*

A: Nee, dat kan niet. Azure Hybrid Benefit ondersteunt alleen conversie op installatie kopieën met betalen per gebruik.

*V: Ik heb mijn eigen RHEL-installatie kopie geüpload van on-premises (via Azure Migrate, ASR of anderszins) naar Azure. Kan ik de facturering voor deze afbeeldingen converteren van BYOS naar PAYG?*

A: Nee, dat kan niet. De Azure Hybrid Benefit mogelijkheid is vandaag alleen beschikbaar voor RHEL-en SLES-installatie kopieën in de Marketplace. 

*V: Ik heb mijn eigen RHEL-installatie kopie geüpload van on-premises (via Azure Migrate, ASR of anderszins) naar Azure. Moet ik iets doen om te profiteren van Azure Hybrid Benefit?*

A: Nee, niet. RHEL-installatie kopieën die u uploadt, worden al beschouwd als BYOS en u betaalt alleen voor de kosten van de Azure-infra structuur. U bent zelf verantwoordelijk voor de kosten voor RHEL-abonnementen, net als bij uw on-premises omgevingen. 

*V: kan ik Azure Hybrid Benefit gebruiken op Vm's die zijn geïmplementeerd vanuit Marketplace RHEL en SLES SAP-installatie kopieën?*

A: Ja, dat is mogelijk. U kunt het licentie type ' RHEL_BYOS ' gebruiken voor RHEL-Vm's en ' SLES_BYOS ' voor conversies van Vm's die zijn geïmplementeerd op Marketplace RHEL en SLES SAP-installatie kopieën.

*V: kan ik Azure Hybrid Benefit gebruiken op de virtuele-machine Scale set (VMSS) voor RHEL en SLES?*

A: Nee, dat kan niet. VMSS zijn momenteel niet binnen het bereik van Azure Hybrid Benefit vandaag voor RHEL en SLES.

*V: kan ik Azure Hybrid Benefit gebruiken op gereserveerde instanties (RIs) voor RHEL en SLES?*

A: Nee, dat kan niet. RIs bevindt zich momenteel niet binnen het bereik van Azure Hybrid Benefit voor RHEL en SLES.

*V: kan ik Azure Hybrid Benefit gebruiken op een virtuele machine die is geïmplementeerd voor SQL Server op RHEL-installatie kopieën?*

A: Nee, dat kan niet. Er is geen plan voor de ondersteuning van deze.
 

## <a name="common-issues"></a>Algemene problemen
Deze sectie bevat een lijst met veelvoorkomende problemen die kunnen optreden en stappen voor de oplossing.

| Fout | Oplossing |
| ----- | ---------- |
| "De actie kan niet worden voltooid omdat uit onze gegevens blijkt dat u Red Hat Cloud Access niet hebt ingeschakeld voor uw Azure-abonnement............ | Als u het voor deel van RHEL Vm's wilt gebruiken, moet u uw Azure-abonnement (en) eerst registreren bij Red Hat Cloud Access. Bezoek deze koppeling voor meer informatie over het registreren van uw Azure-abonnementen voor Red Hat Cloud Access

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het maken en bijwerken van Vm's en het toevoegen van licentie typen (RHEL_BYOS, SLES_BYOS) voor Azure Hybrid Benefit met behulp van [Azure cli.](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest&preserve-view=true)
