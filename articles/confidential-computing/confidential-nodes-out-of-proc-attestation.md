---
title: Attestation-ondersteuning met Intel SGX-offerte helper DaemonSet in Azure (preview)
description: Een DaemonSet voor het genereren van de prijsopgave buiten het Intel SGX-toepassingsproces. In dit artikel wordt uitgelegd hoe de attestation-faciliteit buiten het proces wordt geboden voor vertrouwelijke workloads die in een container worden uitgevoerd.
ms.service: container-service
ms.subservice: confidential-computing
author: agowdamsft
ms.topic: overview
ms.date: 2/12/2021
ms.author: amgowda
ms.openlocfilehash: 849fd7afa3f9365f31ee8e03d9f9cc2174d64304
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484401"
---
# <a name="platform-software-management-with-intel-sgx-quote-helper-daemonset-preview"></a>Platformsoftwarebeheer met Intel SGX quote helper DaemonSet (preview)

[Enclavetoepassingen die](confidential-computing-enclaves.md) externe attestation uitvoeren, vereisen een gegenereerde prijsopgave. Deze prijsopgave biedt cryptografisch bewijs van de identiteit en de status van de toepassing, evenals de omgeving waarin de enclave wordt uitgevoerd. Voor het genereren van de prijsopgave zijn vertrouwde softwareonderdelen vereist die deel uitmaken van de Intel Platform Software Components (PSW).

## <a name="overview"></a>Overzicht
 
Intel ondersteunt twee attestation-modi voor het genereren van de offerte:

- *In-process host* de vertrouwde softwareonderdelen binnen het enclavetoepassingsproces.

- *Out-of-process host* de vertrouwde softwareonderdelen buiten de enclavetoepassing.
 
Intel SGX-toepassingen (Software Guard Extension) die zijn gebouwd met behulp van de Open Enclave SDK, maken standaard gebruik van de attestation-modus in het proces. Intel SGX-toepassingen maken de attestation-modus buiten het proces mogelijk. Als u deze modus wilt gebruiken, hebt u extra hosting nodig en moet u de vereiste onderdelen, zoals Architectural Enclave Service Manager (AESM), extern aan de toepassing blootstellen.

Deze functie verbetert de uptime voor uw enclave-apps tijdens updates van het Intel-platform of DCAP-stuurprogramma-updates. Daarom raden we u aan deze te gebruiken.

Als u deze functie wilt inschakelen in een AKS-cluster (Azure Kubernetes Services), voegt u de opdracht toe aan de Azure CLI wanneer u de `--enable-sgxquotehelper` invoegseling confidential computing inschakelen. 

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled and SGX Quote Helper
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom --enable-sgxquotehelper
```

Zie [Quickstart: Deploy an AKS cluster with confidential computing nodes by using the Azure CLI (Snelstart: Een AKS-cluster met confidential computing-knooppunten implementeren met behulp van de Azure CLI) voor meer informatie.](confidential-nodes-aks-get-started.md)

## <a name="benefits-of-the-out-of-process-mode"></a>Voordelen van de out-of-process-modus

In de volgende lijst worden enkele van de belangrijkste voordelen van deze attestation-modus beschreven:

-   Er zijn geen updates vereist voor het genereren van prijsopgaveonderdelen van PSW voor elke containertoepassing. Containereigenaren hoeven geen updates binnen hun container te beheren. Containereigenaren vertrouwen in plaats daarvan op de providerinterface die de gecentraliseerde service buiten de container aanroept. De provider werkt de container bij en beheert deze.

-   U hoeft zich geen zorgen te maken over attestation-fouten vanwege out-of-date PSW-onderdelen. De provider beheert de updates voor deze onderdelen.

-   De modus out-of-process biedt een beter gebruik van EPC-geheugen dan in de procesmodus. In de procesmodus moet elke enclavetoepassing een exemplaar maken van de kopie van QE en PCE voor externe attestation. In de out-of-process-modus is het niet nodig dat de container deze enclaves host en gebruikt daarom geen enclavegeheugen uit het containerquotum.

-   Wanneer u het Intel SGX-stuurprogramma upstreamt naar een Linux-kernel, is er afdwinging voor een enclave met hogere bevoegdheden. Met deze bevoegdheid kan de enclave PCE aanroepen, waardoor de enclavetoepassing die wordt uitgevoerd in de procesmodus wordt breekt. Enclaves krijgen deze machtiging standaard niet. Voor het verlenen van deze bevoegdheid aan een enclavetoepassing moeten wijzigingen in het installatieproces van de toepassing worden aangebracht. In de out-of-process-modus zorgt de provider van de service die out-of-process-aanvragen verwerkt er daarentegen voor dat de service met deze bevoegdheid wordt geïnstalleerd.

-   U hoeft niet te controleren op achterwaartse compatibiliteit met PSW en DCAP. Updates van onderdelen van PSW voor het genereren van offerten worden door de provider gecontroleerd op compatibiliteit met eerdere versies vóór de updates worden toegepast. Dit helpt u compatibiliteitsproblemen af te handelen voordat u updates implementeert voor vertrouwelijke workloads.

## <a name="confidential-workloads"></a>Vertrouwelijke workloads

De offerteaanvraag en het genereren van offertes worden afzonderlijk uitgevoerd, maar op dezelfde fysieke computer. Het genereren van offertes wordt gecentraliseerd en aanvragen voor aanhalingstekens van alle entiteiten worden ontvangen. Elke entiteit kan alleen aanhalingstekens aanvragen als de interface correct is gedefinieerd en detecteerbaar is.

![Diagram met de relaties tussen de offerte-requestor, het genereren van offertes en de interface.](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

Dit abstracte model is van toepassing op het scenario met vertrouwelijke workloads, door gebruik te maken van de AESM-service die al beschikbaar is. AESM wordt in een container geplaatst en geïmplementeerd als een DaemonSet in het Kubernetes-cluster. Kubernetes garandeert dat één exemplaar van een AESM-servicecontainer, verpakt in een pod, op elk agent-knooppunt kan worden geïmplementeerd. De nieuwe Intel SGX quote DaemonSet is afhankelijk van de DaemonSet sgx-device-plugin, omdat de AESM-servicecontainer EPC-geheugen aanvraagt van de sgx-device-plugin voor het starten van QE- en PCE-enclaves.

Elke container moet ervoor kiezen om out-of-process offertegeneratie te gebruiken door de omgevingsvariabele in te stellen `SGX_AESM_ADDR=1` tijdens het maken. De container moet ook het pakket libsgx-quote-ex bevatten dat verantwoordelijk is voor het door sturen van de aanvraag naar de standaard Unix-domeinsocket.

Een toepassing kan nog steeds de in-process attestation gebruiken zoals voorheen, maar in-process en out-of-process kunnen niet tegelijkertijd worden gebruikt binnen een toepassing. De out-of-process-infrastructuur is standaard beschikbaar en verbruikt resources.

## <a name="sample-implementation"></a>Voorbeeld van implementatie

Het volgende Docker-bestand is een voorbeeld voor een toepassing op basis van Open Enclave. Stel de `SGX_AESM_ADDR=1` omgevingsvariabele in het Docker-bestand in of stel deze in op het implementatiebestand. Het volgende voorbeeld bevat details voor het Docker-bestand en de implementatie. 

  > [!Note] 
  > Om de attestation buiten het proces goed te laten werken, moet de libsgx-quote-ex van Intel worden verpakt in de toepassingscontainer.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for details
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# This sets the flag for out-of-process attestation mode. Alternatively you can set this flag on the deployment files.
ENV SGX_AESM_ADDR=1 

CMD make run
```
U kunt ook de attestation-modus buiten het proces instellen in het YAML-bestand van de implementatie. U doet dit als volgt:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>Volgende stappen

[Quickstart: Een AKS-cluster met Confidential Computing-knooppunten implementeren met behulp van de Azure CLI](./confidential-nodes-aks-get-started.md)

[Quickstart-voorbeelden van vertrouwelijke containers](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2-SKU's](../virtual-machines/dcv2-series.md)
