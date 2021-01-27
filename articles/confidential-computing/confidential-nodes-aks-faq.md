---
title: Veelgestelde vragen over ondersteuning voor vertrouwelijke knoop punten in azure Kubernetes service (AKS)
description: Antwoorden vinden op enkele veelgestelde vragen over de Azure Kubernetes service (AKS) & ondersteuning voor het Azure-knoop punt voor vertrouwelijke Computing (ACC).
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: amgowda
ms.openlocfilehash: 51e2095b0df8fdc965926c6c612c45f582c9b9d2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874481"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>Veelgestelde vragen over de knoop punten van vertrouwelijke computing in azure Kubernetes service (AKS)

In dit artikel worden veelgestelde vragen behandeld over op Intel SGX gebaseerde, vertrouwelijke computing knooppunten in azure Kubernetes service (AKS). Als u nog vragen hebt, kunt u e-mail berichten verzenden acconaks@microsoft.com .

## <a name="what-service-level-agreement-sla-and-azure-support-is-provided-during-the-preview"></a>Wat Service Level Agreement (SLA) en ondersteuning voor Azure wordt geboden tijdens de preview? 

SLA niet op tijdens de preview-versie van het product zoals [hier](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)is gedefinieerd. Product ondersteuning wordt echter geboden via ondersteuning voor Azure.

## <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>Wat is Attestation en hoe kunnen we nagaan of apps worden uitgevoerd in enclaves? 

Attestation is het proces van het demonstreren en valideren of een stukje software op de juiste manier is geïnstantieerd op het specifieke hardwareplatform. Het zorgt er ook voor dat het bewijs kan worden geverifieerd om zekerheid te geven dat het wordt uitgevoerd op een veilig platform en niet is geknoeid. [Meer](attestation.md) informatie over hoe Attestation wordt uitgevoerd voor enclave-apps.

## <a name="can-i-enable-accelerated-networking-with-azure-confidential-computing-aks-clusters"></a>Kan ik versneld netwerken met AKS-clusters van Azure vertrouwelijk computing inschakelen? 

Nee. Versnelde netwerken worden niet ondersteund op vertrouwelijke computer knooppunten op AKS. Zorg ervoor dat versneld netwerken zijn uitgeschakeld in uw implementatie. 

## <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>Kan ik mijn bestaande toepassingen in de container plaatsen en deze uitvoeren op AKS met Azure vertrouwelijk computing? 

Ja, Bekijk de [pagina met vertrouwelijke containers](confidential-containers.md) voor meer informatie over platform inschakelen.

## <a name="what-intel-sgx-driver-version-is-installed-in-the-aks-image"></a>Welke versie van het Intel SGX-stuur programma is geïnstalleerd in de AKS-installatie kopie? 

Op dit moment worden Azure DCSv2 Vm's geïnstalleerd met Intel SGX DCAP 1,33. 

## <a name="can-i-open-an-azure-support-ticket-if-i-run-into-issues"></a>Kan ik een ondersteunings ticket van Azure openen als ik problemen uitvoer? 

Ja. Ondersteuning voor Azure wordt geboden tijdens de preview-versie. Er is geen SLA gekoppeld omdat het product zich in de preview-fase bevindt.

## <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>Kan ik bericht installatie scripts invoegen/Stuur Programma's aanpassen aan de knoop punten die zijn ingericht door AKS? 

Nee. [Op AKS-engine gebaseerde vertrouwelijke computing knooppunten](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) ondersteunen vertrouwelijke reken knooppunten die aangepaste installaties toestaan.

## <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>Moet ik een docker-basis installatie kopie gebruiken om aan de slag te gaan met enclave-toepassingen? 

Verschillende inschakelen (Isv's en OSS-projecten) bieden manieren om vertrouwelijke containers in te scha kelen. Bekijk de [pagina met vertrouwelijke containers](confidential-containers.md) voor meer informatie en afzonderlijke verwijzingen naar implementaties.

## <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>Kan ik ACCe knoop punten uitvoeren met andere Standard AKS Sku's (bouw een cluster met heterogene-knooppunt groep)? 

Ja, u kunt verschillende knooppunt groepen binnen hetzelfde AKS-cluster uitvoeren, met inbegrip van ACCe knoop punten. Als u uw enclave-toepassingen wilt richten op een specifieke knooppunt groep, kunt u overwegen om knooppunt selecties toe te voegen of EPC-limieten toe te passen Meer informatie vindt u [hier](confidential-nodes-aks-get-started.md)op de pagina snel starten op vertrouwelijke knoop punten.

## <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>Kan ik Windows-knoop punten en Windows-containers uitvoeren met ACC? 

Momenteel niet. Neem contact met ons op als u Windows-knoop punten of containers nodig hebt. 

## <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>Wat moet ik doen als mijn container groter is dan het beschik bare EPC-geheugen? 

Het EPC-geheugen is van toepassing op het deel van uw toepassing dat is geprogrammeerd om te worden uitgevoerd in de enclave. De totale grootte van de container is niet de juiste manier om deze te vergelijken met het Maxi maal beschik bare EPC-geheugen. DCSv2 machines met SGX, staan in feite het maximale VM-geheugen van 32 GB toe, waarbij uw niet-vertrouwde deel van de toepassing zou gebruiken. Als uw container echter meer dan het beschik bare EPC-geheugen gebruikt, kan dit van invloed zijn op de prestaties van het gedeelte van het programma dat wordt uitgevoerd in de enclave.

Voor een beter beheer van het EPC-geheugen in de worker-knoop punten, kunt u het EPC-geheugen beheer van limieten beheren via Kubernetes. Volg het voor beeld hieronder als referentie

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```

## <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>Wat gebeurt er als mijn enclave meer dan het Maxi maal beschik bare EPC-geheugen verbruikt? 

Het totale beschik bare EPC-geheugen wordt gedeeld tussen de enclave-toepassingen in dezelfde Vm's of worker-knoop punten. Als uw toepassing gebruikmaakt van EPC-geheugen meer dan beschikbaar is, kan dit gevolgen hebben voor de prestaties van de toepassing. Daarom wordt u aangeraden per toepassing in uw implementatie yaml-bestand in te stellen voor een beter beheer van het beschik bare EPC-geheugen per worker-knoop punten, zoals wordt weer gegeven in de bovenstaande voor beelden. U kunt er ook voor kiezen om de VM-grootten van de worker-knooppunt groep te verplaatsen of meer knoop punten toe te voegen. 

## <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>Waarom kan ik Forks () en exec gebruiken om meerdere processen in mijn enclave-toepassing uit te voeren? 

Azure vertrouwelijk computing DCsv2 SKU-Vm's ondersteunen momenteel één adres ruimte voor het programma dat wordt uitgevoerd in een enclave. Eén proces is een huidige beperking die is ontworpen om hoge beveiliging te bieden. Vertrouwelijke container-inschakelers kunnen echter alternatieve implementaties hebben om deze beperking te overwinnen.

## <a name="do-you-automatically-install-any-additional-daemonsets-to-expose-the-sgx-drivers"></a>Installeert u automatisch extra daemonsets om de SGX-Stuur Programma's zichtbaar te maken? 

Ja. De naam van de daemonset is SGX-apparaat-plugin en SGX-quote-helper. Lees [hier](confidential-nodes-aks-overview.md)meer over de respectieve doel einden.  

## <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>Wat is de VM-SKU die ik moet kiezen voor vertrouwelijke computer knooppunten? 

DCSv2 Sku's. De [DCSv2-sku's](../virtual-machines/dcv2-series.md) zijn beschikbaar in de [ondersteunde regio's](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).

## <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>Kan ik nog steeds niet-enclave containers op vertrouwelijke computing knooppunten plannen en uitvoeren? 

Ja. De Vm's hebben ook een normaal geheugen waarmee standaard-container werkbelastingen kunnen worden uitgevoerd. Houd rekening met het beveiligings-en bedreigings model van uw toepassingen voordat u besluit om de implementatie modellen te implementeren.

## <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>Kan ik AKS inrichten met DCSv2-knooppunt groepen via Azure Portal? 

Ja. Azure CLI kan ook worden gebruikt als een alternatief zoals [hier](confidential-nodes-aks-get-started.md)wordt beschreven.

## <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>Welke Ubuntu-versie en VM-generatie wordt ondersteund? 

18,04 op gen 2. 

## <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>Kunnen we de huidige Intel SGX DCAP Diver-versie wijzigen op AKS? 

Nee. Als u aangepaste installaties wilt uitvoeren, raden we u aan om implementaties van [AKS-engine voor vertrouwelijke computing](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) te kiezen. 

## <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>Welke versie van Kubernetes wordt ondersteund en aanbevolen? 

Kubernetes-versie 1,16 en hoger wordt ondersteund en aanbevolen 

## <a name="what-are-the-known-current-limitation-or-technical-limitations-of-the-product-in-preview"></a>Wat zijn de bekende huidige beperkingen of technische beperkingen van het product als preview? 

- Biedt alleen ondersteuning voor Ubuntu 18,04 gen 2 VM-knoop punten 
- Geen ondersteuning voor Windows-knoop punten of ondersteuning voor Windows-containers
- Op EPC-geheugen gebaseerde horizontale pod automatisch schalen wordt niet ondersteund. Schalen op basis van CPU en normaal geheugen wordt ondersteund.
- Dev Spaces op AKS voor vertrouwelijke apps worden momenteel niet ondersteund

## <a name="next-steps"></a>Volgende stappen
Bekijk de [pagina met vertrouwelijke containers](confidential-containers.md) voor meer informatie over vertrouwelijke containers.