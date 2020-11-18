---
title: Confidential Computing-knooppunten in Azure Kubernetes Service (AKS) (openbare preview)
description: Confidential Computing-knooppunten in AKS
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: 1b945ac9f656a227bcc3335cb0ec995626f98f77
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564171"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service-public-preview"></a>Confidential Computing-knooppunten in Azure Kubernetes Service (openbare preview)

Met [Azure Confidential Computing](overview.md) kunt u uw gevoelige gegevens tijdens het gebruik beschermen. De onderliggende infrastructuren beveiligen deze gegevens van andere toepassingen, beheerders en cloudproviders met een hardwarematige, vertrouwde uitvoeringsomgeving.

## <a name="overview"></a>Overzicht

Azure Kubernetes Service (AKS) biedt ondersteuning voor het toevoegen van [DCsv2 Confidential Computing-knooppunten](confidential-computing-enclaves.md) mogelijk gemaakt door Intel SGX. Op deze knooppunten kunnen gevoelige workloads worden uitgevoerd binnen een hardwarematige, vertrouwde uitvoeringsomgeving door code op gebruikersniveau toe te staan persoonlijke gebieden van het geheugen toe te wijzen. Deze persoonlijke geheugengebieden worden enclaves genoemd. Enclaves zijn ontworpen voor het beveiligen van code en gegevens van processen die met hogere bevoegdheid worden uitgevoerd. Het SGX-uitvoeringsmodel haalt de tussenliggende lagen van Hypervisor en het besturingssysteem van de gast en de host. Met het *hardwarematige, per container geïsoleerde uitvoeringsmodel* kunnen toepassingen rechtstreeks worden uitgevoerd met de CPU, terwijl het speciale blok geheugen versleuteld blijft. Vertrouwelijke rekenknooppunten helpen bij de algemene beveiligingspostuur van containertoepassingen op AKS en zijn een uitstekende toevoeging voor een containerstrategie die voorzien is van krachtige beveiliging. 

![overzicht van sgx-knoop punt](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>Functies van vertrouwelijke AKS-knooppunten

- Hardwarematige isolatie van de containers op procesniveau via het vertrouwde SGX-uitvoeringsmodel 
- Clusters van heterogene knooppuntpools (een combinatie van vertrouwelijke en niet-vertrouwelijke knooppuntpools)
- Op geheugen gebaseerde pod-planning met Encrypted Page Cache (EPC)
- Vooraf geïnstalleerd SGX DCAP-stuurprogramma
- Vooraf geïnstalleerde Intel FSGS-patch
- Ondersteunt CPU-verbruik op basis van horizontaal, automatisch schalen van pods en clusters
- Helper voor bevestiging van out-of-process via AKS-daemonset
- Linux-containers biden ondersteuning via Ubuntu 18.04 Gen 2 VM-werkknooppunten

## <a name="aks-provided-daemon-sets-addon"></a>Door AKS geleverde daemonsets (invoegtoepassing)

#### <a name="sgx-device-plugin"></a>SGX Device Plugin <a id="sgx-plugin"></a>

SGX Device Plugin implementeert de interface voor de Kubernetes-apparaatinvoegtoepassing voor het EPC-geheugen. Deze invoegtoepassing maakt van het EPC-geheugen feitelijk een extra resourcetype in Kubernetes. Gebruikers kunnen limieten voor deze resource opgeven, net als voor andere resources. Naast de planningsfunctie kan SGX met behulp van de apparaatinvoegtoepassing machtigingen voor apparaatstuurprogramma's toewijzen aan de containers voor vertrouwelijke werkbelastingen. Een voorbeeld van een implementatie van de op het EPC-geheugen gebaseerde implementatiesample (`kubernetes.azure.com/sgx_epc_mem_in_MiB`) vindt u [hier](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)

#### <a name="sgx-quote-helper-service"></a>Helper-service voor SGX-offertes <a id="sgx-quote"></a>

Enclavetoepassingen die externe bevestiging uitvoeren, moeten een offerte genereren. De offerte voorziet in een cryptografisch bewijs van de identiteit en de status van de toepassing en de omgeving waarin de enclave wordt uitgevoerd. Het genereren van offertes is afhankelijk van bepaalde vertrouwde softwareonderdelen van Intel, die deel uitmaken van SGX Platform Software Components (PSW/DCAP). Deze PSW is verpakt als een daemonset die per knooppunt wordt uitgevoerd. PSW kan worden gebruikt bij het aanvragen van een offerte ter bevestiging vanuit enclave-apps. Door gebruik te maken van de door de AKS verstrekte service, kan de compatibiliteit tussen de PSW en andere softwareonderdelen in de host beter worden onderhouden. [Lees meer](confidential-nodes-out-of-proc-attestation.md) over de details van het gebruik en de functie.

## <a name="programming--application-models"></a>Programmeren en toepassingsmodellen

### <a name="confidential-containers"></a>Vertrouwelijke containers

[Vertrouwelijke containers](confidential-containers.md) voeren bestaande programma's in runtime uit, evenals de **meestgebruikte programmeertalen** (Python, Node, Java, enzovoort), in combinatie met hun bestaande bibliotheekafhankelijkheden, zonder wijziging of hercompilatie van de broncode. Dit model is het snelste model naar vertrouwelijkheid, mogelijk gemaakt via opensourceprojecten en Azure-partners. De containerinstallatiekopieën die gereed zijn gemaakt om in de beveiligde enclaves te worden uitgevoerd, worden vertrouwelijke containers genoemd.

### <a name="enclave-aware-containers"></a>Enclave-compatibele containers

AKS ondersteunt toepassingen die zijn geprogrammeerd om te worden uitgevoerd op vertrouwelijke knooppunten en maken gebruik van een **speciale instructieset** die beschikbaar wordt gesteld via de SDK's en frameworks. Dit toepassingsmodel biedt de meeste controle over uw toepassingen met een computer met een laagste Trusted Computing Base (TCB). [Lees meer](enclave-aware-containers.md) over enclave-compatibele containers.

## <a name="next-steps"></a>Volgende stappen

[AKS-cluster implementeren met Confidential Computing-knooppunten](./confidential-nodes-aks-get-started.md)

[Quickstart met samples van vertrouwelijke containers](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU-lijst](../virtual-machines/dcv2-series.md)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions