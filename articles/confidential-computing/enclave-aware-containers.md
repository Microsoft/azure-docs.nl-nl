---
title: Enclave-compatibele containers in Azure
description: Enclave-compatibele toepassingscontainers ondersteunen Azure Kubernetes Service (AKS)
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: b4a348bbc73e6cfb2fabaa8e401af0ddf7265354
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561905"
---
# <a name="enclave-aware-containers"></a>Enclave-compatibele containers

Een enclave is een beveiligde geheugenregio die vertrouwelijkheid biedt voor het uitvoeren van gegevens en code. Het is een instantie van een TEE (Trusted Execution Environment), die door hardware wordt beveiligd. Confidential Computing-knooppunten in AKS maken gebruik van [Intel Software Guard Extensions (SGX)](https://software.intel.com/sgx) om geïsoleerde enclaveomgevingen te maken in de knooppunten tussen containertoepassingen.

Net als bij virtuele Intel SGX-machines hebben containertoepassingen die zijn ontwikkeld om in enclaves te worden uitgevoerd twee onderdelen:

- Een niet-vertrouwd onderdeel (de host genaamd) en
- Een vertrouwd onderdeel (de enclave genaamd).

![Architectuur van enclave-compatibele containers](./media/enclave-aware-containers/enclaveawarecontainer.png)

Met de toepassingsarchitectuur van enclave-compatibele containers hebt u de meeste controle over de implementatie en blijft de code-footprint in de enclave laag. Het minimaliseren van de code die in de enclave wordt uitgevoerd, vermindert de voor aanvallen kwetsbare oppervlakken.   

## <a name="enablers"></a>Enablers

### <a name="open-enclave-sdk"></a>Open Enclave-SDK
Open Enclave SDK is een hardware-neutrale, open-sourcebibliotheek voor het ontwikkelen van C- en C++-toepassingen die gebruikmaken van op hardware gebaseerde Trusted Execution Environments. De huidige implementatie biedt ondersteuning voor Intel SGX en preview-ondersteuning voor [OP-TEE OS on Arm TrustZone](https://optee.readthedocs.io/en/latest/general/about.html).

Ga [hier](https://github.com/openenclave/openenclave/tree/master/docs/GettingStartedDocs) aan de slag met de op Open Enclave gebaseerde containertoepassing

### <a name="intel-sgx-sdk"></a>Intel SGX SDK
Intel onderhoudt de Software Development Kit voor het bouwen van SGX-toepassingen voor werkbelastingen van Linux- en Windows-containers. Windows-containers worden momenteel niet ondersteund door Confidential Computing-knooppunten van AKS.

Ga [hier](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/sdk.html) aan de slag met toepassingen op basis van Intel SGX

### <a name="confidential-consortium-framework-ccf"></a>Confidential Consortium Framework (CCF)
Confidential Consortium Framework (CCF) is een open-sourceframework voor het bouwen van een nieuwe categorie veilige, maximaal beschikbare en goed presterende toepassingen die gericht zijn op rekenkracht en gegevens van meerdere partijen. CCF maakt grootschalige, vertrouwelijke netwerken mogelijk die voldoen aan belangrijke bedrijfsvereisten. Zodoende wordt een manier geboden om de productie en de bedrijfsacceptatie van op een consortium gebaseerde blockchain- en rekentechnologie van meerdere partijen te versnellen.

Ga [hier](https://github.com/Microsoft/CCF) aan de slag met Confidential Computing van Azure en CCF

### <a name="confidential-inferencing-onnx-runtime"></a>ONNX Runtime voor vertrouwelijke deductie

Met open source, op enclaves gebaseerde ONNX Runtime wordt een beveiligd kanaal tussen de client en de deductieservice tot stand gebracht. Dit garandeert dat noch de aanvraag noch het antwoord de beveiligde enclave kan verlaten. 

Met deze oplossing kunt u gebruikmaken van bestaande, met Machine Learning (ML) getrainde modellen en deze vertrouwelijk uitvoeren terwijl u een vertrouwensrelatie tussen de client en de server tot stand brengt door middel van bevestigingen en verificaties. 

Ga [hier](https://aka.ms/confidentialinference) aan de slag met ML-model lift-and-shift naar ONNX Runtime

### <a name="edgeless-rt"></a>Edgeless RT

Edgeless RT is een opensource-project dat is gebaseerd op de Open Enclave-SDK. Hiermee wordt ondersteuning toegevoegd voor Go en aanvullende C++ functies. Ga [hier](https://github.com/edgelesssys/edgelessrt) aan de slag met een eenvoudige vertrouwelijke Go-toepassing met behulp van uw vertrouwde VS code-omgeving. Volg [hier](https://github.com/edgelesssys/edgelessrt/blob/master/docs/ERTAzureAKSDeployment.md) de instructies voor Edgeless-toepassingen op AKS


## <a name="container-based-sample-implementations"></a>Implementaties van samples op basis van containers

[Azure-samples voor enclave-compatibele containers in AKS](https://github.com/Azure-Samples/confidential-computing/tree/main/containersamples)

<!-- LINKS - external -->
[Azure Attestation](../attestation/overview.md)


<!-- LINKS - internal -->
[Virtuele DC-machine ](./virtual-machine-solutions.md)
[Vertrouwelijke containers](./confidential-containers.md)