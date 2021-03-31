---
title: Gebruik Azure HPC cache en Azure NetApp Files
description: Azure HPC cache gebruiken om de toegang tot gegevens die zijn opgeslagen met Azure NetApp Files te verbeteren
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: e955ddc14bb2b0a7abc0dc815c6955247568876b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86497009"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Een Azure HPC-cache gebruiken met Azure NetApp Files

U kunt [Azure NetApp files](https://azure.microsoft.com/services/netapp/) als een opslag doel gebruiken voor uw Azure HPC-cache. In dit artikel wordt uitgelegd hoe de twee services kunnen samen werken, en krijgt u tips voor het instellen hiervan.

Azure NetApp Files zijn ONTAP-besturings systeem gecombineerd met de schaal baarheid en snelheid van Microsoft Azure. Deze combi natie stelt gebruikers in staat om werk stromen naar de cloud te verplaatsen zonder code te herschrijven.

Het toevoegen van een Azure HPC-cache onderdeel kan bestands toegang verbeteren door meerdere Azure NetApp Files volumes te presen teren in één geaggregeerde naam ruimte. Het kan Edge-caching bieden voor volumes in een andere service regio. Het kan ook de prestaties verbeteren op aanvraag voor volumes die zijn gemaakt bij lagere service niveaus om kosten te besparen.

## <a name="overview"></a>Overzicht

Als u een Azure NetApp Files systeem als back-end-opslag met Azure HPC cache wilt gebruiken, volgt u dit proces.

1. Maak het Azure NetApp Files systeem en de volumes volgens de richt lijnen in [plan uw systeem hieronder](#plan-your-azure-netapp-files-system).
1. Maak de Azure HPC-cache in de regio waar u bestands toegang nodig hebt. (Gebruik de instructies in [een Azure HPC-cache maken](hpc-cache-create.md).)
1. [Geef opslag doelen](#create-storage-targets-in-the-cache) in de cache op die verwijzen naar de Azure NetApp files volumes. Maak één cache-opslag doel voor elk uniek IP-adres dat wordt gebruikt voor toegang tot de volumes.
1. Laat clients [de Azure HPC-cache koppelen](#mount-storage-targets) in plaats van Azure NetApp files-volumes rechtstreeks te koppelen.

## <a name="plan-your-azure-netapp-files-system"></a>Uw Azure NetApp Files-systeem plannen

Let bij het plannen van uw Azure NetApp Files-systeem op de items in deze sectie om ervoor te zorgen dat u het probleemloos kunt integreren met de HPC-cache van Azure.

Lees ook de [documentatie van Azure NetApp files](../azure-netapp-files/index.yml) voordat u volumes maakt voor gebruik met de Azure HPC-cache.

### <a name="nfs-client-access-only"></a>Alleen NFS-client toegang

Azure HPC cache biedt momenteel alleen ondersteuning voor NFS-toegang. Het kan niet worden gebruikt met SMB-ACL of de POSIX-modus bits-volumes.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Exclusief subnet voor Azure NetApp Files

Azure NetApp Files gebruikt één gedelegeerd subnet voor de volumes. Andere resources kunnen dat subnet niet gebruiken. Daarnaast kan slechts één subnet in een virtueel netwerk worden gebruikt voor Azure NetApp Files. Meer informatie vindt u in de [richt lijnen voor het plannen van Azure NetApp files-netwerken](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Grootte van overgedragen subnet

Gebruik de minimale grootte voor het gedelegeerde subnet wanneer u een Azure NetApp Files-systeem maakt voor gebruik met de Azure HPC-cache.

De minimale grootte, die wordt opgegeven met het netmasker/28, biedt 16 IP-adressen. In de praktijk gebruikt Azure NetApp Files slechts drie van die beschik bare IP-adressen voor toegang tot het volume. Dit betekent dat u in uw Azure HPC-cache slechts drie opslag doelen moet maken voor alle volumes.

Als het overgedragen subnet te groot is, is het mogelijk dat de Azure NetApp Files-volumes meer IP-adressen gebruiken dan één Azure HPC-cache-exemplaar kan verwerken. Eén cache kan Maxi maal 10 opslag doelen hebben.

In het Quick start-voor beeld in Azure NetApp Files-documentatie wordt 10.7.0.0/16 gebruikt voor het gedelegeerde subnet. Dit geeft een subnet dat te groot is.

### <a name="capacity-pool-service-level"></a>Service niveau capaciteits groep

Wanneer u het service niveau voor uw capaciteits groep kiest, moet u rekening houden met uw werk stroom. Als u regel matig gegevens naar het Azure NetApp Files-volume schrijft, kunnen de prestaties van de cache worden beperkt als het tijdstip van de back-up traag is. Kies een hoog service niveau voor volumes die regel matig worden geschreven.

Voor volumes met een laag service niveau kan ook een vertraging aan het begin van een taak worden weer gegeven terwijl de inhoud van de cache vooraf wordt ingevuld. Nadat de cache is uitgevoerd met een goede werkset bestanden, moet de vertraging onopgemerkt worden.

Het is belang rijk dat het service niveau van de capaciteits groep van tevoren wordt gepland, omdat het niet kan worden gewijzigd nadat het is gemaakt. Een nieuw volume moet worden gemaakt in een andere capaciteits groep en de gegevens worden gekopieerd.

Houd er rekening mee dat u de opslag limiet van een volume en de grootte van de capaciteits groep kunt wijzigen zonder de toegang te onderbreken.

## <a name="create-storage-targets-in-the-cache"></a>Opslag doelen maken in de cache

Nadat uw Azure NetApp Files systeem is ingesteld en de Azure HPC-cache is gemaakt, definieert u de opslag doelen in de cache die naar de bestandssysteem volumes verwijzen.

Maak één opslag doel voor elk IP-adres dat wordt gebruikt door uw Azure NetApp Files volumes. Het IP-adres wordt weer gegeven op de pagina koppelings instructies van het volume.

Als meerdere volumes hetzelfde IP-adres hebben, kunt u één opslag doel gebruiken voor al deze.  

Volg de [koppelings instructies in de documentatie van Azure NetApp files](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) om de te gebruiken IP-adressen te vinden.

U kunt ook IP-adressen vinden met de Azure CLI:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Export namen op het Azure NetApp Files systeem hebben één padcomponent. Probeer niet om een opslag doel te maken voor de basis export ``/`` in azure NetApp files, omdat die export geen bestands toegang biedt.

Er zijn geen speciale beperkingen voor de virtuele naam ruimte paden voor deze opslag doelen.

## <a name="mount-storage-targets"></a>Opslag doelen koppelen

Client computers moeten de cache koppelen in plaats van de Azure NetApp Files-volumes rechtstreeks te koppelen. Volg de instructies in [de Azure HPC-cache koppelen](hpc-cache-mount.md).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het instellen en gebruiken van [Azure NetApp files](../azure-netapp-files/index.yml)
* [Neem contact op met de ondersteuning](hpc-cache-support-ticket.md)voor hulp bij het plannen en instellen van uw Azure HPC-cache systeem voor het gebruik van Azure NetApp files.
