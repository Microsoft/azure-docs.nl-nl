---
title: Azure Hybrid Benefit-en Linux-Vm's
description: Meer informatie over hoe Azure Hybrid Benefit u kan helpen om geld te besparen op uw virtuele Linux-machines die worden uitgevoerd op Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 1bc108f76ac35b13474de18d473f5728dbad9d23
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560013"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Hoe Azure Hybrid Benefit van toepassing is op virtuele Linux-machines

Azure Hybrid Benefit is een voor deel van de licentie verlening waarmee u de kosten van het uitvoeren van uw Red Hat Enterprise Linux (RHEL) en SUSE Linux Enterprise Server (SLES) virtuele machines (Vm's) in de Cloud aanzienlijk kunt verlagen. Met dit voor deel betaalt u alleen voor de kosten van de infra structuur van uw VM omdat uw RHEL-of SLES-abonnement de software kosten dekt. Het voor deel is van toepassing op alle RHEL-installatie kopieën (PAYG) van de SLES Marketplace.

Azure Hybrid Benefit voor Linux-Vm's is nu openbaar beschikbaar.

## <a name="benefit-description"></a>Beschrijving van voor deel

Via Azure Hybrid Benefit kunt u uw on-premises RHEL-en SLES-servers naar Azure migreren door bestaande RHEL-en SLES PAYG-Vm's te converteren naar Azure om uw eigen abonnement (BYOS) over te brengen. Vm's die zijn geïmplementeerd vanuit PAYG-installatie kopieën in azure, bevatten doorgaans kosten voor de infra structuur en software kosten. Met Azure Hybrid Benefit kunnen PAYG Vm's worden geconverteerd naar een BYOS facturerings model zonder een herimplementatie, zodat u uitval tijd risico kunt voor komen.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure Hybrid Benefit kosten visualisatie op Linux-Vm's.":::

Nadat u het voor deel voor een RHEL-of SLES-VM hebt ingeschakeld, worden er geen kosten meer in rekening gebracht voor de extra software die doorgaans op een PAYG-VM is gemaakt. In plaats daarvan begint uw virtuele machine met de kosten voor BYOS, die alleen de kosten voor de berekenings-hardware en geen software kosten omvat.

U kunt er ook voor kiezen om een VM die het voor deel heeft ingeschakeld, te converteren naar een PAYG-facturerings model.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Bereik van Azure Hybrid Benefit geschiktheid voor Linux-Vm's

Azure Hybrid Benefit is beschikbaar voor alle RHEL-en SLES-PAYG-installatie kopieën van Azure Marketplace. Het voor deel is nog niet beschikbaar voor RHEL-of SLES BYOS-installatie kopieën of aangepaste installatie kopieën van Azure Marketplace.

Gereserveerde instanties, Azure-specifieke host-instanties en SQL Hybrid bene fits komen niet in aanmerking voor Azure Hybrid Benefit als u het voor deel van Linux-Vm's al gebruikt.

## <a name="get-started"></a>Aan de slag

### <a name="red-hat-customers"></a>Red Hat-klanten

Azure Hybrid Benefit voor RHEL is beschikbaar voor Red Hat-klanten die aan beide criteria voldoen:

- Actieve of ongebruikte RHEL-abonnementen hebben die in aanmerking komen voor gebruik in azure
- Een of meer van deze abonnementen hebben ingeschakeld voor gebruik in azure met het [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) -programma

Begin met het gebruik van het voor deel voor Red Hat:

1. Schakel een of meer van uw in aanmerking komende RHEL-abonnementen in voor gebruik in azure met behulp van de [Red Hat Cloud Access-klant interface](https://access.redhat.com/management/cloud).

   De Azure-abonnementen die u tijdens het activerings proces van Red Hat Cloud Access opgeeft, mogen vervolgens de functie Azure Hybrid Benefit gebruiken.
1. Pas Azure Hybrid Benefit toe op uw bestaande RHEL PAYG-Vm's en op alle nieuwe RHEL Vm's die u implementeert vanuit Azure Marketplace PAYG installatie kopieën.
1. Volg de aanbevolen [volgende stappen](https://access.redhat.com/articles/5419341) voor het configureren van update bronnen voor uw RHEL-vm's en voor RHEL-nalevings richtlijnen voor het abonnement.


### <a name="suse-customers"></a>SUSE-klanten

Ga als volgt te beginnen met het gebruik van het voor deel voor SUSE:

1. Meld u aan bij het SUSE open bare Cloud programma.
1. Pas het voor deel toe op uw bestaande Vm's via de Azure CLI.
1. Registreer uw Vm's die het voor deel ontvangen met een afzonderlijke bron van updates.


## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Het voor deel in de Azure CLI inschakelen en uitschakelen

U kunt de `az vm update` opdracht gebruiken om bestaande vm's bij te werken. Voor RHEL Vm's voert u de opdracht uit met een `--license-type` para meter van `RHEL_BYOS` . Voor SLES Vm's voert u de opdracht uit met een `--license-type` para meter van `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>CLI-voor beeld om het voor deel in te scha kelen
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>CLI-voor beeld om het voor deel uit te scha kelen
Als u het voor deel wilt uitschakelen, gebruikt u de `--license-type` waarde `None` :

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>CLI-voor beeld om het voor deel in te scha kelen voor een groot aantal Vm's
Als u het voor deel voor een groot aantal Vm's wilt inschakelen, kunt u de `--ids` para meter in de Azure CLI gebruiken:

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

In de volgende voor beelden ziet u twee methoden voor het ophalen van een lijst met resource-Id's: één op het niveau van de resource groep en één op het abonnements niveau.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>De Azure Hybrid Benefit Toep assen tijdens het maken van de VM
U kunt de Azure Hybrid Benefit niet alleen Toep assen op bestaande Vm's met betalen per gebruik, maar ook op het moment van het maken van een VM. Dit zijn de voor delen van threefold:
- U kunt virtuele machines van zowel PAYG als BYOS inrichten met behulp van dezelfde installatie kopie en hetzelfde proces.
- Er worden toekomstige wijzigingen in de licentie modus ingeschakeld, iets niet alleen beschikbaar met een BYOS-installatie kopie of als u uw eigen VM meebrengt.
- De virtuele machine wordt standaard verbonden met de Red Hat Update infrastructure (RHUI) om ervoor te zorgen dat deze up-to-date en beveiligd blijft. U kunt het bijgewerkte mechanisme na implementatie op elk gewenst moment wijzigen.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>De Azure Hybrid Benefit status van een virtuele machine controleren
U kunt de Azure Hybrid Benefit status van een virtuele machine weer geven met behulp van de Azure CLI of met behulp van Azure Instance Metadata Service.

### <a name="azure-cli"></a>Azure CLI

U kunt de `az vm get-instance-view` opdracht voor dit doel gebruiken. Zoek naar een `licenseType` veld in het antwoord. Als het `licenseType` veld bestaat en de waarde is `RHEL_BYOS` of `SLES_BYOS` , is het voor deel van uw virtuele machine ingeschakeld.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azire Instance Metadata Service

Vanuit de VM zelf kunt u een query uitvoeren op de attestende meta gegevens in azure Instance Metadata Service om de waarde van de virtuele machine te bepalen `licenseType` . Een `licenseType` waarde van `RHEL_BYOS` of `SLES_BYOS` geeft aan dat het voor deel van uw virtuele machine is ingeschakeld. Meer [informatie over Attestation-meta gegevens](./instance-metadata-service.md#attested-data).

## <a name="compliance"></a>Naleving

### <a name="red-hat"></a>Red Hat

Klanten die Azure Hybrid Benefit gebruiken voor RHEL, komen overeen met de standaard [juridische voor waarden](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) en [privacyverklaring](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) die zijn gekoppeld aan de Azure Marketplace RHEL-aanbiedingen.

Klanten die Azure Hybrid Benefit voor RHEL gebruiken, hebben drie opties voor het leveren van software-updates en-patches voor deze Vm's:

- [Infra structuur voor Red Hat update](../workloads/redhat/redhat-rhui.md) (standaard optie)
- Red Hat Satellite-Server
- Red Hat Subscription Manager

Klanten die de RHUI-optie kiezen, kunnen RHUI blijven gebruiken als de belangrijkste update bron voor hun Azure Hybrid Benefit RHEL-Vm's zonder RHEL-abonnementen aan deze Vm's toe te voegen. Klanten die de RHUI-optie kiezen, zijn verantwoordelijk voor het garanderen van de naleving van het RHEL-abonnement.

Klanten die een Red Hat Satellite-Server of Red Hat Subscription manager kiezen, moeten de RHUI-configuratie verwijderen en vervolgens een RHEL-abonnement met Cloud toegang toevoegen aan hun Azure Hybrid Benefit RHEL Vm's.  

Zie voor meer informatie over de naleving van Red Hat-abonnementen, software-updates en bronnen voor Azure Hybrid Benefit RHEL Vm's het [Red Hat-artikel over het gebruik van RHEL-abonnementen met Azure Hybrid Benefit](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Als u Azure Hybrid Benefit voor uw SLES-Vm's wilt gebruiken, moet u eerst zijn geregistreerd bij het [SuSE open bare Cloud-programma](https://www.suse.com/media/guide/suse_public_cloud_service_provider_program_overview.pdf). Nadat u SUSE-abonnementen hebt aangeschaft, moet u uw virtuele machines die gebruikmaken van deze abonnementen, registreren in uw eigen bron van updates. Gebruik het SUSE-klanten centrum, de server voor abonnements beheer of SUSE Manager voor deze registratie.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
*V: kan ik een licentie type van `RHEL_BYOS` met een SLES-installatie kopie of andersom gebruiken?*

A: Nee, dat kan niet. Als u een licentie type opgeeft dat niet goed overeenkomt met de distributie die wordt uitgevoerd op uw VM, worden er geen meta gegevens voor facturering bijgewerkt. Maar als u per ongeluk het verkeerde licentie type invoert, wordt het voor deel nog steeds mogelijk als u uw VM opnieuw bijwerkt naar het juiste licentie type.

*V: Ik heb een Red Hat Cloud-toegang geregistreerd, maar ik kan het voor deel van mijn RHEL-Vm's nog steeds niet inschakelen. Wat moet ik doen?*

A: het kan enige tijd duren voordat de inschrijving van uw Red Hat Cloud Access-abonnement van Red Hat naar Azure wordt door gegeven. Neem contact op met micro soft ondersteuning als u na één werkdag nog steeds de fout ziet.

*V: Ik heb een virtuele machine geïmplementeerd met behulp van een RHEL BYOS ' gouden installatie kopie '. Kan ik de facturering voor deze afbeeldingen converteren van BYOS naar PAYG?*

A: Nee, dat kan niet. Azure Hybrid Benefit ondersteunt alleen conversie op installatie kopieën met betalen per gebruik.

*V: Ik heb mijn eigen RHEL-installatie kopie van on-premises (via Azure Migrate, Azure Site Recovery of anderszins) geüpload naar Azure. Kan ik de facturering voor deze afbeeldingen converteren van BYOS naar PAYG?*

A: Nee, dat kan niet. De Azure Hybrid Benefit mogelijkheid is momenteel alleen beschikbaar voor RHEL-en SLES-installatie kopieën in azure Marketplace. 

*V: Ik heb mijn eigen RHEL-installatie kopie van on-premises (via Azure Migrate, Azure Site Recovery of anderszins) geüpload naar Azure. Moet ik iets doen om te profiteren van Azure Hybrid Benefit?*

A: Nee, dat is niet alles. RHEL-installatie kopieën die u uploadt, worden al beschouwd als BYOS en worden alleen in rekening gebracht voor de kosten van Azure-infra structuur. U bent verantwoordelijk voor de RHEL-abonnements kosten, net zoals u dat voor uw on-premises omgevingen hebt. 

*V: kan ik Azure Hybrid Benefit gebruiken op Vm's die zijn geïmplementeerd vanuit Azure Marketplace RHEL en SLES SAP-installatie kopieën?*

A: Ja, dat is mogelijk. U kunt het licentie type van `RHEL_BYOS` voor RHEL-vm's en `SLES_BYOS` voor conversies van vm's die zijn geïmplementeerd vanuit Azure Marketplace RHEL en SLES SAP-installatie kopieën gebruiken.

*V: kan ik Azure Hybrid Benefit gebruiken op schaal sets voor virtuele machines voor RHEL en SLES?*

A: Nee, dat kan niet. Virtuele-machine schaal sets bevinden zich momenteel niet in het bereik van Azure Hybrid Benefit voor RHEL en SLES.

*V: kan ik Azure Hybrid Benefit gebruiken op gereserveerde instanties voor RHEL en SLES?*

A: Nee, dat kan niet. Gereserveerde instanties bevinden zich momenteel niet in het bereik van Azure Hybrid Benefit voor RHEL en SLES.

*V: kan ik Azure Hybrid Benefit gebruiken op een virtuele machine die is geïmplementeerd voor SQL Server op RHEL-installatie kopieën?*

A: Nee, dat kan niet. Er is geen plan voor de ondersteuning van deze.
 

## <a name="common-problems"></a>Algemene problemen
In deze sectie vindt u veelvoorkomende problemen die u kunt tegen komen en stappen voor de oplossing.

| Fout | Oplossing |
| ----- | ---------- |
| "De actie kan niet worden voltooid omdat uit onze gegevens blijkt dat u Red Hat Cloud Access niet hebt ingeschakeld voor uw Azure-abonnement............ | Als u het voor deel van RHEL Vm's wilt gebruiken, moet u eerst [uw Azure-abonnementen registreren bij Red Hat Cloud Access](https://access.redhat.com/management/cloud).

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het maken en bijwerken van Vm's en het toevoegen van licentie typen (RHEL_BYOS, SLES_BYOS) voor Azure Hybrid Benefit met behulp van de Azure CLI](/cli/azure/vm?preserve-view=true&view=azure-cli-latest)
