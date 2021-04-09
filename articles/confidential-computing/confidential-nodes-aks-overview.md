---
title: Vertrouwelijke computing-knoop punten op de Azure Kubernetes-service (AKS)
description: Confidential Computing-knooppunten in AKS
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 2/08/2021
ms.author: amgowda
ms.openlocfilehash: 203185d9f6def2204906b8722f1969b14eee8787
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933148"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service"></a>Vertrouwelijke computing-knoop punten op de Azure Kubernetes-service

Met [Azure Confidential Computing](overview.md) kunt u uw gevoelige gegevens tijdens het gebruik beschermen. De onderliggende vertrouwelijke computing infrastructuur beveiligt deze gegevens van andere toepassingen, beheerders en cloud providers met een hardwarematige, door hardware ondersteunde vertrouwde uitvoerings container omgevingen. Als u vertrouwelijke computing knooppunten toevoegt, kunt u de container toepassing uitvoeren in een geïsoleerde, met hardware beveiligde en attest bare omgeving.

## <a name="overview"></a>Overzicht

Azure Kubernetes Service (AKS) biedt ondersteuning voor het toevoegen van [DCsv2 Confidential Computing-knooppunten](confidential-computing-enclaves.md) mogelijk gemaakt door Intel SGX. Met deze knoop punten kunt u gevoelige werk belastingen uitvoeren binnen een hardware-gebaseerde Trust Execution Environment (TEE). T de code op gebruikers niveau van containers toestaan om persoonlijke geheugen gebieden toe te wijzen om de code rechtstreeks uit te voeren met een CPU. Deze privé geheugen regio's die rechtstreeks met de CPU worden uitgevoerd, worden enclaves genoemd. Enclaves helpen bij het beschermen van de vertrouwelijkheid van gegevens, gegevens integriteit en code-integriteit van andere processen die worden uitgevoerd op dezelfde knoop punten. Het Intel SGX-uitvoerings model verwijdert ook de tussenliggende lagen van het gast besturingssysteem, het besturings systeem van de host en Hyper Visor, waardoor de aanval wordt verkleind surface area. Met het *geïsoleerde op hardware gebaseerd* model voor het uitvoeren van een container in een knoop punt kunnen toepassingen rechtstreeks worden uitgevoerd met de CPU, terwijl het speciaal versleutelde geheugen blok per container wordt bewaard. Vertrouwelijke computing-knoop punten met vertrouwelijke containers vormen een uitstekende aanvulling op de beveiligings planning en ingrijpende container strategie van het vertrouwens niveau van nul.

![overzicht van sgx-knoop punt](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>Functies van vertrouwelijke AKS-knooppunten

- Hardwarematige en proces niveau isolatie van containers via Intel SGX Trusted Execution Environment (TEE) 
- Clusters van heterogene knooppuntpools (een combinatie van vertrouwelijke en niet-vertrouwelijke knooppuntpools)
- Op geheugen gebaseerde pod-planning (Encrypted page cache) (EPC vereist)
- Intel SGX DCAP-stuur programma vooraf geïnstalleerd
- Op CPU-verbruik gebaseerd horizon taal pod automatisch schalen en cluster automatisch schalen
- Linux-containers biden ondersteuning via Ubuntu 18.04 Gen 2 VM-werkknooppunten

## <a name="confidential-computing-add-on-for-aks"></a>Add-on voor vertrouwelijke computing voor AKS
De functie add-on biedt extra mogelijkheden op AKS wanneer u groeps knooppunten met vertrouwelijke Computing op het cluster uitvoert. Met deze invoeg toepassing worden de onderstaande functies ingeschakeld.

#### <a name="azure-device-plugin-for-intel-sgx"></a>Azure Device-invoeg toepassing voor Intel SGX <a id="sgx-plugin"></a>

De invoeg toepassing voor apparaten implementeert de Kubernetes Device-invoeg toepassing voor het geheugen van de versleutelde pagina cache (EPC) en maakt de apparaatstuurprogramma's van de knoop punten beschikbaar. In feite maakt deze invoeg toepassing EPC-geheugen als een ander resource type in Kubernetes. Gebruikers kunnen limieten voor deze resource opgeven, net als voor andere resources. Naast de plannings functie, helpt de invoeg toepassing voor apparaten Intel SGX-apparaatstuurprogramma's machtigingen toe te wijzen aan de containers voor vertrouwelijke werk belasting. Met deze ontwikkelaar van de invoeg toepassing kan voor komen dat de Intel SGX-stuur programma-volumes in de implementatie bestanden worden gekoppeld. Een voorbeeld van een implementatie van de op het EPC-geheugen gebaseerde implementatiesample (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) vindt u [hier](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)


## <a name="programming-models"></a>Programmeermodellen

### <a name="confidential-containers"></a>Vertrouwelijke containers

Met [vertrouwelijke containers](confidential-containers.md) kunt u bestaande niet-gewijzigde container toepassingen van de meest **voorkomende programmeer talen** runtime (python, knoop punt, Java, enzovoort) op een vertrouwelijke plaats uitvoeren. Voor Dit verpakkings model hoeft u geen bron code te wijzigen of opnieuw te compileren. Dit is de snelste methode voor vertrouwelijkheid die kan worden bereikt door uw standaard docker-containers te verpakken met Open-Source projecten of Azure-partner oplossingen. In Dit verpakkings-en uitvoerings model worden alle onderdelen van de container toepassing geladen in de Trusted boundary (enclave). Dit model werkt goed voor de schap-container toepassingen die beschikbaar zijn in de markt of aangepaste apps die momenteel worden uitgevoerd op knoop punten met algemene doel einden.

### <a name="enclave-aware-containers"></a>Enclave-compatibele containers
Vertrouwelijke computing knooppunten in AKS ondersteunen ook containers die zijn geprogrammeerd om in een enclave te worden uitgevoerd om een **speciale instructieset** te gebruiken die beschikbaar is via de CPU. Met dit programmeer model kunt u uw uitvoerings stroom nauw keurig beheren en moet u speciale Sdk's en frameworks gebruiken. Dit programmeer model biedt de meeste controle over de toepassings stroom met een TCB (laagste Trusted Computing Base). Enclaveing voor de ontwikkeling van een container houdt in dat er niet-vertrouwde en vertrouwde onderdelen aan de container toepassing worden toegevoegd, zodat u het normale geheugen en het EPC-geheugen (Encrypted page cache) kunt beheren waar enclave wordt uitgevoerd. [Lees meer](enclave-aware-containers.md) over enclave-compatibele containers.

## <a name="next-steps"></a>Volgende stappen

[AKS-cluster implementeren met Confidential Computing-knooppunten](./confidential-nodes-aks-get-started.md)

[Quickstart met samples van vertrouwelijke containers](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU-lijst](../virtual-machines/dcv2-series.md)

[Ingrijpende indieping met webinar-sessie met vertrouwelijke containers](https://www.youtube.com/watch?reload=9&v=FYZxtHI_Or0&feature=youtu.be)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
