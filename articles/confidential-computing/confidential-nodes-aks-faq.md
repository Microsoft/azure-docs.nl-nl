---
title: Veelgestelde vragen over ondersteuning voor vertrouwelijke knoop punten in azure Kubernetes service (AKS)
description: Antwoorden vinden op enkele veelgestelde vragen over de Azure Kubernetes service (AKS) & ondersteuning voor het Azure-knoop punt voor vertrouwelijke Computing (ACC).
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 02/09/2020
ms.author: amgowda
ms.openlocfilehash: 550995f0be3d634e7e9f24a8bf6826916003308e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653394"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>Veelgestelde vragen over de knoop punten van vertrouwelijke computing in azure Kubernetes service (AKS)

In dit artikel worden veelgestelde vragen behandeld over op Intel SGX gebaseerde, vertrouwelijke computing knooppunten in azure Kubernetes service (AKS). Als u nog vragen hebt, kunt u e-mail berichten verzenden **acconaks@microsoft.com** .

<a name="1"></a>
### <a name="are-the-confidential-computing-nodes-on-aks-in-ga"></a>Bevinden zich de vertrouwelijke computing knooppunten op AKS in GA? ###
Ja

<a name="2"></a>
### <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>Wat is Attestation en hoe kunnen we nagaan of apps worden uitgevoerd in enclaves? ###
Attestation is het proces van het demonstreren en valideren of een stukje software op de juiste manier is geïnstantieerd op het specifieke hardwareplatform. Het zorgt er ook voor dat het bewijs kan worden geverifieerd om zekerheid te geven dat het wordt uitgevoerd op een veilig platform en niet is geknoeid. [Meer](attestation.md) informatie over hoe Attestation wordt uitgevoerd voor enclave-apps.

<a name="3"></a>
### <a name="can-i-enable-accelerated-networking-with-azure-confidential-computing-aks-clusters"></a>Kan ik versneld netwerken met AKS-clusters van Azure vertrouwelijk computing inschakelen? ###
Nee. Versnelde netwerken worden niet ondersteund op virtuele DCSv2-machines die Makeup vertrouwelijk computing nodes op AKS. 

<a name="4"></a>
### <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>Kan ik mijn bestaande toepassingen in de container plaatsen en deze uitvoeren op AKS met Azure vertrouwelijk computing? ###
Ja, Bekijk de [pagina met vertrouwelijke containers](confidential-containers.md) voor meer informatie over platform inschakelen.

<a name="5"></a>
### <a name="what-version-of-intel-sgx-driver-version-is-on-the-aks-image-for-confidential-nodes"></a>Welke versie van Intel SGX-stuur programma-versie bevindt zich op de AKS-installatie kopie voor vertrouwelijke knoop punten? ### 
Op dit moment worden Azure DCSv2 Vm's geïnstalleerd met Intel SGX DCAP 1,33. 

<a name="6"></a>
### <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>Kan ik bericht installatie scripts invoegen/Stuur Programma's aanpassen aan de knoop punten die zijn ingericht door AKS? ###
Nee. [Op AKS-engine gebaseerde vertrouwelijke computing knooppunten](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) ondersteunen vertrouwelijke reken knooppunten die aangepaste installaties toestaan en volledige controle over uw Kubernetes-besturings vlak hebben.
<a name="7"></a>

### <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>Moet ik een docker-basis installatie kopie gebruiken om aan de slag te gaan met enclave-toepassingen? ###
Verschillende inschakelen (Isv's en OSS-projecten) bieden manieren om vertrouwelijke containers in te scha kelen. Bekijk de [pagina met vertrouwelijke containers](confidential-containers.md) voor meer informatie en afzonderlijke verwijzingen naar implementaties.

<a name="8"></a>
### <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>Kan ik ACCe knoop punten uitvoeren met andere Standard AKS Sku's (bouw een cluster met heterogene-knooppunt groep)? ###

Ja, u kunt verschillende knooppunt groepen binnen hetzelfde AKS-cluster uitvoeren, met inbegrip van ACCe knoop punten. Als u uw enclave-toepassingen wilt richten op een specifieke knooppunt groep, kunt u overwegen om knooppunt selecties toe te voegen of EPC-limieten toe te passen Meer informatie vindt u [hier](confidential-nodes-aks-get-started.md)op de pagina snel starten op vertrouwelijke knoop punten.

<a name="9"></a>
### <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>Kan ik Windows-knoop punten en Windows-containers uitvoeren met ACC? ###
Momenteel niet. Neem contact op met het product team op *acconaks@microsoft.com* Als u Windows-knoop punten of containers nodig hebt. 

<a name="10"></a>
### <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>Wat moet ik doen als mijn container groter is dan het beschik bare EPC-geheugen? ###
Het EPC-geheugen is van toepassing op het deel van uw toepassing dat is geprogrammeerd om te worden uitgevoerd in de enclave. De totale grootte van de container is niet de juiste manier om deze te vergelijken met het Maxi maal beschik bare EPC-geheugen. DCSv2 machines met SGX, staan in feite het maximale VM-geheugen van 32 GB toe, waarbij uw niet-vertrouwde deel van de toepassing zou gebruiken. Als uw container echter meer dan het beschik bare EPC-geheugen gebruikt, kan dit van invloed zijn op de prestaties van het gedeelte van het programma dat wordt uitgevoerd in de enclave.

Voor een beter beheer van het EPC-geheugen in de worker-knoop punten, kunt u het EPC-geheugen beheer van limieten beheren via Kubernetes. Volg het voor beeld hieronder als referentie.

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
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively, you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```
<a name="11"></a>
### <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>Wat gebeurt er als mijn enclave meer dan het Maxi maal beschik bare EPC-geheugen verbruikt? ###

Het totale beschik bare EPC-geheugen wordt gedeeld tussen de enclave-toepassingen in dezelfde Vm's of worker-knoop punten. Als uw toepassing gebruikmaakt van EPC-geheugen meer dan beschikbaar is, kan dit gevolgen hebben voor de prestaties van de toepassing. Daarom wordt u aangeraden per toepassing in uw implementatie yaml-bestand in te stellen voor een beter beheer van het beschik bare EPC-geheugen per worker-knoop punten, zoals wordt weer gegeven in de bovenstaande voor beelden. U kunt er ook voor kiezen om de VM-grootten van de worker-knooppunt groep te verplaatsen of meer knoop punten toe te voegen. 

<a name="12"></a>
### <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>Waarom kan ik Forks () en exec gebruiken om meerdere processen in mijn enclave-toepassing uit te voeren? ###

Azure vertrouwelijk computing DCsv2 SKU-Vm's ondersteunen momenteel één adres ruimte voor het programma dat wordt uitgevoerd in een enclave. Eén proces is een huidige beperking die is ontworpen om hoge beveiliging te bieden. Vertrouwelijke container-inschakelers kunnen echter alternatieve implementaties hebben om deze beperking te overwinnen.
<a name="13"></a>
### <a name="do-you-automatically-install-any-additional-daemonset-to-expose-the-sgx-drivers"></a>Installeert u automatisch een extra daemonset om de SGX-Stuur Programma's zichtbaar te maken? ###

Ja. De naam van de daemonset is SGX-apparaat-plugin. Lees [hier](confidential-nodes-aks-overview.md)meer over de respectieve doel einden.  

<a name="14"></a>
### <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>Wat is de VM-SKU die ik moet kiezen voor vertrouwelijke computer knooppunten? ###

DCSv2 Sku's. De [DCSv2-sku's](../virtual-machines/dcv2-series.md) zijn beschikbaar in de [ondersteunde regio's](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).

<a name="15"></a>
### <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>Kan ik nog steeds niet-enclave containers op vertrouwelijke computing knooppunten plannen en uitvoeren? ###

Ja. De Vm's hebben ook een normaal geheugen waarmee standaard-container werkbelastingen kunnen worden uitgevoerd. Houd rekening met het beveiligings-en bedreigings model van uw toepassingen voordat u besluit om de implementatie modellen te implementeren.
<a name="16"></a>

### <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>Kan ik AKS inrichten met DCSv2-knooppunt groepen via Azure Portal? ###

Ja. Azure CLI kan ook worden gebruikt als een alternatief zoals [hier](confidential-nodes-aks-get-started.md)wordt beschreven.

<a name="17"></a>
### <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>Welke Ubuntu-versie en VM-generatie wordt ondersteund? ###
18,04 op gen 2. 

<a name="18"></a>
### <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>Kunnen we de huidige Intel SGX DCAP Diver-versie wijzigen op AKS? ###

Nee. Als u aangepaste installaties wilt uitvoeren, raden we u aan om implementaties van [AKS-engine voor vertrouwelijke computing](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) te kiezen. 

<a name="19"></a>

### <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>Welke versie van Kubernetes wordt ondersteund en aanbevolen? ###

Kubernetes-versie 1,16 en hoger wordt ondersteund en aanbevolen. 

<a name="20"></a>
### <a name="what-are-the-known-current-limitations-of-the-product"></a>Wat zijn de bekende huidige beperkingen van het product? ###

- Biedt alleen ondersteuning voor Ubuntu 18,04 gen 2 VM-knoop punten 
- Geen ondersteuning voor Windows-knoop punten of ondersteuning voor Windows-containers
- Op EPC-geheugen gebaseerde horizontale pod automatisch schalen wordt niet ondersteund. Schalen op basis van CPU en normaal geheugen wordt ondersteund.
- Dev Spaces op AKS voor vertrouwelijke apps worden momenteel niet ondersteund

<a name="21"></a>
### <a name="will-only-signed-and-trusted-images-be-loaded-in-the-enclave-for-confidential-computing"></a>Worden alleen ondertekende en vertrouwde installatie kopieën geladen in de enclave voor vertrouwelijke computing? ###
Niet systeem eigen tijdens de initialisatie van enclave, maar de hand tekening van een Attestation-proces kan worden gevalideerd. Ref [.](../attestation/basic-concepts.md#benefits-of-policy-signing) 

### <a name="next-steps"></a>Volgende stappen
Bekijk de [pagina met vertrouwelijke containers](confidential-containers.md) voor meer informatie over vertrouwelijke containers.
