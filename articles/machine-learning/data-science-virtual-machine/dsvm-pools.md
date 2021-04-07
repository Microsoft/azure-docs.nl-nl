---
title: Gedeelde Pools
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over het maken van & een gedeelde groep data Science Virtual Machines (Dsvm) te implementeren als een gedeelde bron voor een team.
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics, team data science process
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: a118d5a3e716a80bda21ffe82a8cefd1da1202f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519708"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Een gedeelde groep data Science maken Virtual Machines

In dit artikel leert u hoe u een gedeelde pool van data Science Virtual Machines (Dsvm) kunt maken voor een team. De voor delen van het gebruik van een gedeelde groep zijn het verbeteren van het resource gebruik, het eenvoudig delen en samen werken en een efficiëntere beheer van DSVM-resources.

U kunt veel methoden en technologieën gebruiken om een pool met Dsvm te maken. Dit artikel richt zich op Pools voor interactieve virtuele machines (Vm's). Een alternatieve beheerde Compute-infra structuur is Azure Machine Learning Reken kracht. Zie [reken cluster maken](../how-to-create-attach-compute-cluster.md)voor meer informatie.

## <a name="interactive-vm-pool"></a>Interactieve VM-groep

Een groep interactieve Vm's die door het hele AI/data Science-Team worden gedeeld, stelt gebruikers in staat om zich aan te melden bij een beschikbaar exemplaar van de DSVM in plaats van dat ze een toegewezen exemplaar voor elke set gebruikers hebben. Deze installatie maakt betere Beschik baarheid en efficiëntere gebruik van resources mogelijk.

U gebruikt de [Azure virtual machine Scale sets](../../virtual-machine-scale-sets/index.yml) -technologie om een interactieve VM-groep te maken. U kunt schaal sets gebruiken voor het maken en beheren van een groep met virtuele machines met gelijke taak verdeling en automatisch schalen.

De gebruiker meldt zich aan bij het IP-of DNS-adres van de hoofd groep. De schaalset stuurt de sessie automatisch door naar een beschik bare DSVM in de schaalset. Omdat gebruikers een consistente en vertrouwde omgeving willen, ongeacht de VM waarin ze zich aanmelden bij, kunnen alle exemplaren van de virtuele machine in de schaalset een gedeeld netwerk station koppelen, zoals een Azure Files share of een NFS-share (Network File System). De gedeelde werk ruimte van de gebruiker wordt normaal gesp roken bewaard op het gedeelde bestands archief dat is gekoppeld aan elk van de instanties.

U vindt een voor beeld Azure Resource Manager sjabloon waarmee een schaalset met Ubuntu DSVM-instanties op [github](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json)wordt gemaakt. U vindt een voor beeld van het [parameter bestand](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) voor de Azure Resource Manager sjabloon op dezelfde locatie.

U kunt de schaalset maken op basis van de sjabloon Azure Resource Manager door waarden op te geven voor het parameter bestand in de Azure CLI:

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az deployment group create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

Bij de voor gaande opdrachten wordt ervan uitgegaan dat u:

* Een kopie van het parameter bestand met de waarden die zijn opgegeven voor uw exemplaar van de schaalset.
* Het aantal VM-exemplaren.
* Verwijzingen naar de Azure Files share.
* Referenties voor het opslag account dat wordt gekoppeld op elke virtuele machine.

Naar het parameter bestand wordt lokaal verwezen in de opdrachten. U kunt ook para meters inline door geven of in uw script vragen om deze in te dienen.  

Met de voor gaande sjabloon kunnen de SSH-en JupyterHub-poort van de front-end-schaalset worden ingesteld op de back-end-pool van Ubuntu Dsvm. Als gebruiker meldt u zich op de normale manier aan bij de virtuele machine op een Secure Shell (SSH) of op JupyterHub. Omdat de VM-exemplaren dynamisch omhoog of omlaag kunnen worden geschaald, moet elke status worden opgeslagen in de gekoppelde Azure Files share. U kunt dezelfde methode gebruiken voor het maken van een groep Windows-Dsvm.

Het [script dat de Azure Files share koppelt](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) , is ook beschikbaar in de Azure DataScienceVM-opslag plaats in github. Het script koppelt de Azure Files-share op het opgegeven koppel punt in het parameter bestand. Het script maakt ook zachte koppelingen naar het gekoppelde station in de basismap van de eerste gebruiker. Een gebruikersspecifieke notebook-map in de Azure Files share is zacht gekoppeld aan de `$HOME/notebooks/remote` Directory, zodat gebruikers hun Jupyter-notebooks kunnen openen, uitvoeren en opslaan. U kunt dezelfde conventie gebruiken wanneer u extra gebruikers op de VM maakt om de Jupyter-werk ruimte van elke gebruiker te laten verwijzen naar de Azure Files share.

Schaal sets voor virtuele machines ondersteunen automatisch schalen. U kunt regels instellen over wanneer u extra instanties wilt maken en wanneer u exemplaren omlaag wilt schalen. U kunt bijvoorbeeld omlaag schalen naar nul-exemplaren om te besparen op kosten voor het gebruik van Cloud-hardware wanneer de Vm's helemaal niet worden gebruikt. De documentatie pagina's van virtuele-machine schaal sets bieden uitgebreide stappen voor automatisch [schalen](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

## <a name="next-steps"></a>Volgende stappen

* [Een algemene identiteit instellen](dsvm-common-identity.md)
* [Bewaar veilig referenties voor toegang tot cloud resources](dsvm-secure-access-keys.md)