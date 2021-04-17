---
title: Azure Hybrid Benefit- en Linux-VM's
description: Meer informatie over Azure Hybrid Benefit u geld kunt besparen op uw virtuele Linux-machines die worden uitgevoerd in Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machines
ms.subservice: azure-hybrid-benefit
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 774f4be6a5aa0e0e772086c52938881c6637b261
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588187"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Hoe Azure Hybrid Benefit van toepassing op virtuele Linux-machines

Azure Hybrid Benefit is een licentievoordeel waarmee u de kosten voor het uitvoeren van uw virtuele Red Hat Enterprise Linux-machines (RHEL) en SUSE Linux Enterprise Server (SLES) in de cloud aanzienlijk kunt verlagen. Met dit voordeel betaalt u alleen voor de infrastructuurkosten van uw VM, omdat uw RHEL- of SLES-abonnement de softwarekosten dekt. Het voordeel is beschikbaar voor alle pay-as-you-go-afbeeldingen (PayG) van RHEL en SLES Marketplace.

Azure Hybrid Benefit voor Linux-VM's is nu openbaar beschikbaar.

## <a name="benefit-description"></a>Beschrijving van het voordeel

Via Azure Hybrid Benefit kunt u uw on-premises RHEL- en SLES-servers migreren naar Azure door bestaande RHEL- en SLES PAYG-VM's in Azure te converteren naar BYOS-facturering (Bring Your Own Subscription). Normaal gesproken worden voor VM's die zijn geïmplementeerd op basis van betalen per gebruik in Azure zowel infrastructuurkosten als softwarekosten in rekening gebracht. Met Azure Hybrid Benefit kunnen betalen per gebruik-VM's zonder herdeployment worden geconverteerd naar een BYOS-factureringsmodel, zodat u downtimerisico's kunt voorkomen.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure Hybrid Benefit kostenvisualisatie op linux-VM's.":::

Nadat u het voordeel op RHEL of SLES VM hebt ingeschakeld, worden er geen kosten meer in rekening gebracht voor de extra softwarekosten die doorgaans worden gemaakt op een betalen per gebruik-VM. In plaats daarvan worden er byOS-kosten in rekening gebracht voor uw VM, die alleen de kosten voor rekenhardware en geen softwarekosten omvatten.

U kunt er ook voor kiezen om een VM met het voordeel weer te converteren naar een payg-factureringsmodel.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Bereik van Azure Hybrid Benefit geschiktheid voor Linux-VM's

Azure Hybrid Benefit is beschikbaar voor alle RHEL- en SLES PAYG-afbeeldingen van Azure Marketplace. Het voordeel is nog niet beschikbaar voor RHEL- of SLES BYOS-afbeeldingen of aangepaste afbeeldingen van Azure Marketplace.

Azure Dedicated Host-exemplaren en hybride SQL-voordelen komen niet in aanmerking voor Azure Hybrid Benefit als u het voordeel al gebruikt met virtuele Linux-VM's.

## <a name="get-started"></a>Aan de slag

### <a name="red-hat-customers"></a>Red Hat-klanten

Azure Hybrid Benefit voor RHEL is beschikbaar voor Red Hat-klanten die aan beide criteria voldoen:

- Actieve of ongebruikte RHEL-abonnementen hebben die in aanmerking komen voor gebruik in Azure
- Een of meer van deze abonnementen hebben ingeschakeld voor gebruik in Azure met het [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) programma

> [!IMPORTANT]
> Zorg ervoor dat het juiste abonnement is ingeschakeld voor het [programma voor cloudtoegang.](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)

Ga als volgende te werk om het voordeel voor Red Hat te gebruiken:

1. Schakel een of meer van uw in aanmerking komende RHEL-abonnementen in voor gebruik in Azure met behulp van [de Red Hat Cloud Access interface](https://access.redhat.com/management/cloud).

   De Azure-abonnementen die u tijdens het Red Hat Cloud Access inschakelen, mogen vervolgens de functie Azure Hybrid Benefit gebruiken.
1. Pas Azure Hybrid Benefit toe op uw bestaande RHEL PAYG-VM's en nieuwe RHEL-VM's die u implementeert vanuit Azure Marketplace PAYG-afbeeldingen. U kunt een Azure Portal of Azure CLI gebruiken om het voordeel in te stellen.
1. Volg de aanbevolen [volgende stappen voor](https://access.redhat.com/articles/5419341) het configureren van updatebronnen voor uw RHEL-VM's en voor nalevingsrichtlijnen voor RHEL-abonnementen.


### <a name="suse-customers"></a>SUSE-klanten

Ga als volgende te werk om het voordeel voor SUSE te gebruiken:

1. Registreer u bij het SUSE Public Cloud Program.
1. Pas het voordeel toe op uw nieuwe of bestaande VM's via de Azure Portal of Azure CLI.
1. Registreer uw VM's die het voordeel ontvangen met een afzonderlijke bron van updates.

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Schakel het voordeel in de Azure Portal

U kunt het voordeel voor bestaande VM's inschakelen door naar de optie **Configuratie** aan de linkerkant te gaan en de stappen daar te volgen. U kunt het voordeel op nieuwe VM's inschakelen tijdens het maken van de VM.

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-vm"></a>Azure Portal om het voordeel voor een bestaande VM mogelijk te maken:
1. Ga [naar Microsoft Azure-portal](https://portal.azure.com/)
1. Ga naar de pagina Een virtuele machine maken in de portal.
 ![AHB tijdens het maken van een VM](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. Klik op het selectievakje om AHB-conversie in teschakelen en licenties voor cloudtoegang te gebruiken.
 ![AHB tijdens het maken van VM-selectievakje](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. Een virtuele machine maken volgens de volgende set instructies
1. Controleer **de** blade Configuratie en u ziet dat de optie is ingeschakeld. 
![Blade AHB-configuratie na het maken](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-during-creation-of-vm"></a>Azure Portal om het voordeel mogelijk te maken tijdens het maken van een VM:
1. Ga [naar Microsoft Azure-portal](https://portal.azure.com/)
1. Open de pagina Virtuele machine waarop u de conversie wilt toepassen.
1. Ga naar **de optie** Configuratie aan de linkerkant. U ziet de sectie Licentieverlening. Schakel het selectievakje Ja in en schakel het selectievakje Bevestiging in om de AHB-conversie in teschakelen.
![Blade AHB-configuratie na het maken](./media/azure-hybrid-benefit/create-configuration-blade.png)

>[!NOTE]
> Als u een  aangepaste momentopname of een gedeelde afbeelding **(SIG)** van een RHEL- of SLES PAYG Marketplace-afbeelding hebt gemaakt, kunt u Azure CLI alleen gebruiken om een Azure Hybrid Benefit. Dit is een bekende beperking en er is momenteel geen tijdlijn om deze mogelijkheid ook in Azure Portal te bieden.

## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Het voordeel in- en uitschakelen in de Azure CLI

U kunt de opdracht gebruiken `az vm update` om bestaande VM's bij te werken. Voer voor RHEL-VM's de opdracht uit met `--license-type` de parameter `RHEL_BYOS` . Voer voor SLES-VM's de opdracht uit met `--license-type` de parameter `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>CLI-voorbeeld om het voordeel in te stellen
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>CLI-voorbeeld om het voordeel uit te schakelen
Als u het voordeel wilt uitschakelen, gebruikt u `--license-type` de waarde `None` :

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>CLI-voorbeeld voor het inschakelen van het voordeel op een groot aantal VM's
Als u het voordeel op een groot aantal VM's wilt inschakelen, kunt u de `--ids` parameter in de Azure CLI gebruiken:

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

In de volgende voorbeelden worden twee methoden voor het verkrijgen van een lijst met resource-ID's weergegeven: één op het niveau van de resourcegroep en één op abonnementsniveau.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>De virtuele Azure Hybrid Benefit tijdens het maken van de VM toepassen
Naast het toepassen van de Azure Hybrid Benefit op bestaande betalen per gebruik-VM's, kunt u deze aanroepen op het moment dat de VM wordt gemaakt. De voordelen ervan zijn drieledig:
- U kunt zowel payg- als BYOS-VM's inrichten met behulp van dezelfde afbeelding en hetzelfde proces.
- Hiermee worden toekomstige wijzigingen in de licentiemodus mogelijk, iets dat niet beschikbaar is met een byos-alleen-afbeelding of als u uw eigen VM gebruikt.
- De VM wordt standaard verbonden met Red Hat Update Infrastructure (RHUI) om ervoor te zorgen dat deze up-to-date en veilig blijft. U kunt het bijgewerkte mechanisme na de implementatie op elk moment wijzigen.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>Controleer de Azure Hybrid Benefit status van een VM
U kunt de status van Azure Hybrid Benefit VM weergeven met behulp van de Azure CLI of met behulp van Azure Instance Metadata Service.

### <a name="azure-cli"></a>Azure CLI

U kunt hiervoor `az vm get-instance-view` de opdracht gebruiken. Zoek naar een `licenseType` veld in het antwoord. Als het `licenseType` veld bestaat en de waarde of `RHEL_BYOS` `SLES_BYOS` is, is het voordeel ingeschakeld op uw VM.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azire Instance Metadata Service

Vanuit de VM zelf kunt u een query uitvoeren op de attested metagegevens in Azure Instance Metadata Service om de waarde van de VM te `licenseType` bepalen. De waarde of geeft aan dat het voordeel is ingeschakeld `licenseType` `RHEL_BYOS` voor uw `SLES_BYOS` VM. [Meer informatie over attested metadata](./instance-metadata-service.md#attested-data).

## <a name="compliance"></a>Naleving

### <a name="red-hat"></a>Red Hat

Klanten die gebruikmaken Azure Hybrid Benefit voor RHEL, [](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) gaan akkoord met de standaard juridische voorwaarden en [privacyverklaring](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) die zijn gekoppeld aan de Azure Marketplace RHEL-aanbiedingen.

Klanten die gebruikmaken Azure Hybrid Benefit RHEL hebben drie opties voor het leveren van software-updates en patches voor deze VM's:

- [Red Hat Update Infrastructure](../workloads/redhat/redhat-rhui.md) (standaardoptie)
- Red Hat Satellite Server
- Red Hat Subscription Manager

Klanten die de optie RHUI kiezen, kunnen RHUI blijven gebruiken als de belangrijkste updatebron voor hun Azure Hybrid Benefit RHEL-VM's zonder RHEL-abonnementen aan deze VM's te koppelen. Klanten die de optie RHUI kiezen, zijn verantwoordelijk voor de naleving van het RHEL-abonnement.

Klanten die Red Hat Satellite Server of Red Hat Subscription Manager kiezen, moeten de RHUI-configuratie verwijderen en vervolgens een RHEL-abonnement met Cloud Access ingeschakeld koppelen aan hun Azure Hybrid Benefit RHEL-VM's.  

Zie het Red Hat-artikel over het gebruik van RHEL-abonnementen met Azure Hybrid Benefit voor meer informatie over de naleving van Red Hat-abonnementen, software-updates en bronnen voor Azure Hybrid Benefit [RHEL-VM'Azure Hybrid Benefit.](https://access.redhat.com/articles/5419341)

### <a name="suse"></a>SUSE

Zie [SUSE Linux Enterprise](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/)en Azure Hybrid Benefit voor informatie over het gebruik van Azure Hybrid Benefit voor uw SLES-VM's en voor informatie over het over stappen van SLES PAYG naar BYOS of het over stappen van SLES BYOS naar PAYG. 

## <a name="azure-hybrid-benefit-on-reserved-instances-is-in-preview"></a>Azure Hybrid Benefit voor gereserveerde instanties is in preview

Met Azure Reservations (Azure Reserved Virtual Machine Instances) kunt u geld besparen door een abonnement voor één of drie jaar te maken voor meerdere producten. Meer informatie over [gereserveerde instanties vindt u hier.](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations) De Azure Hybrid Benefit is beschikbaar in preview voor [gereserveerde](https://review.docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations#charges-covered-by-reservation)instanties van virtuele machines . Dit betekent dat als u rekenkosten hebt aangeschaft tegen een gereduceerd tarief met behulp van ri, u AHB-voordeel kunt toepassen op de licentiekosten voor RHEL en SUSE. De stappen voor het toepassen van AHB-voordelen voor een RI-instantie blijven precies hetzelfde als voor een gewone VM.
![AHB voor RIs](./media/azure-hybrid-benefit/reserved-instances.png)

>[!NOTE]
>Als u al reserveringen voor RHEL- of SUSE PAYG-software op Azure Marketplace hebt aangeschaft, wacht u tot de reserveringsbestelling is voltooid voordat u de Azure Hybrid Benefit..


## <a name="frequently-asked-questions"></a>Veelgestelde vragen
*V: Kan ik een licentietype van gebruiken `RHEL_BYOS` met een SLES-afbeelding of omgekeerd?*

A: Nee, dat kunt u niet. Als u probeert een licentietype in te voeren dat onjuist overeenkomt met de distributie die wordt uitgevoerd op uw VM, worden er geen factureringsmetagegevens bijgewerkt. Maar als u per ongeluk het verkeerde licentietype hebt invoeren, wordt het voordeel nog steeds ingeschakeld als u uw virtuele VM opnieuw bijprofeert naar het juiste licentietype.

*V: Ik heb me geregistreerd bij Red Hat Cloud Access maar kan het voordeel nog steeds niet inschakelen op mijn RHEL-VM's. Wat moet ik doen?*

A: Het kan enige tijd duren Red Hat Cloud Access registratie van uw abonnement is doorgegeven van Red Hat naar Azure. Als de fout na één werkdag nog steeds wordt weergegeven, neem dan contact op met Microsoft Ondersteuning.

*V: Ik heb een VM geïmplementeerd met behulp van RHEL BYOS 'golden image'. Kan ik de facturering voor deze afbeeldingen converteren van BYOS naar PAYG?*

A: Nee, dat kunt u niet. Azure Hybrid Benefit biedt alleen ondersteuning voor conversie voor betalen per gebruikt-afbeeldingen.

*V: Ik heb mijn eigen RHEL-afbeelding van on-premises geüpload (via Azure Migrate, Azure Site Recovery of anderszins) naar Azure. Kan ik de facturering voor deze afbeeldingen converteren van BYOS naar PAYG?*

A: Nee, dat kunt u niet. De Azure Hybrid Benefit is momenteel alleen beschikbaar voor RHEL- en SLES-afbeeldingen in Azure Marketplace. 

*V: Ik heb mijn eigen RHEL-afbeelding van on-premises geüpload (via Azure Migrate, Azure Site Recovery of anderszins) naar Azure. Moet ik iets doen om te profiteren van Azure Hybrid Benefit?*

A: Nee, dat hoeft u niet. RHEL-afbeeldingen die u uploadt, worden al beschouwd als BYOS en er worden alleen kosten in rekening gebracht voor de Azure-infrastructuur. U bent verantwoordelijk voor de kosten van het RHEL-abonnement, net als voor uw on-premises omgevingen. 

*V: Kan ik Azure Hybrid Benefit virtuele Azure Marketplace RHEL- en SLES SAP-afbeeldingen?*

A: Ja, dat kunt u. U kunt het licentietype van gebruiken voor RHEL-VM's en voor conversies van VM's die zijn geïmplementeerd `RHEL_BYOS` `SLES_BYOS` vanuit Azure Marketplace RHEL- en SLES SAP-afbeeldingen.

*V: Kan ik Azure Hybrid Benefit virtuele-machineschaalsets gebruiken voor RHEL en SLES?*

A: Ja, Azure Hybrid Benefit virtuele-machineschaalsets voor RHEL en SLES is in preview. U vindt [hier meer informatie over dit voordeel en hoe u dit kunt gebruiken.](/azure/virtual-machine-scale-sets/azure-hybrid-benefit-linux) 

*V: Kan ik Azure Hybrid Benefit gereserveerde instanties voor RHEL en SLES gebruiken?*

A: Ja, Azure Hybrid Benefit gereserveerde instantie voor RHEL en SLES is in preview. U vindt [hier meer informatie over dit voordeel en hoe u dit kunt gebruiken.](#azure-hybrid-benefit-on-reserved-instances-is-in-preview)

*V: Kan ik Azure Hybrid Benefit virtuele machine gebruiken die is geïmplementeerd voor SQL Server RHEL-afbeeldingen?*

A: Nee, dat is niet mogelijk. Er is geen plan om deze virtuele machines te ondersteunen.

*V: Kan ik een Azure Hybrid Benefit mijn RHEL Virtual Data Center-abonnement?*

A: Nee, dat is niet mogelijk. VDC wordt helemaal niet ondersteund in Azure, met inbegrip van AHB.  
 

## <a name="common-problems"></a>Algemene problemen
In deze sectie vindt u algemene problemen die u kunt tegenkomen en stappen voor het oplossen van problemen.

| Fout | Oplossing |
| ----- | ---------- |
| 'De actie kan niet worden voltooid omdat onze records laten zien dat u de actie niet hebt Red Hat Cloud Access uw Azure-abonnement.... | Als u het voordeel wilt gebruiken met RHEL-VM's, moet u eerst uw [Azure-abonnementen registreren bij Red Hat Cloud Access](https://access.redhat.com/management/cloud).

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het maken en bijwerken van VM's en het toevoegen van licentietypen (RHEL_BYOS, SLES_BYOS) voor Azure Hybrid Benefit met behulp van de Azure CLI](/cli/azure/vm)
