---
title: Meer informatie over Kubernetes workload Management op Azure Stack Edge Pro-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe Kubernetes-workloads kunnen worden beheerd op uw Azure Stack Edge Pro-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: b962d66349bbed112114c010e8d185ba16c74c8d
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443077"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-pro-device"></a>Beheer van Kubernetes-werk belasting op uw Azure Stack Edge Pro-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Op uw Azure Stack Edge Pro-apparaat wordt een Kubernetes-cluster gemaakt wanneer u een compute-functie configureert. Nadat het Kubernetes-cluster is gemaakt, kunnen in een van beide toepassingen met containers in de Kubernetes-cluster worden geïmplementeerd. Er zijn verschillende manieren om workloads te implementeren in uw Kubernetes-cluster. 

In dit artikel worden de verschillende methoden beschreven die kunnen worden gebruikt voor het implementeren van werk belastingen op uw Azure Stack Edge Pro-apparaat.

## <a name="workload-types"></a>Typen werk belasting

De twee algemene typen werk belastingen die u kunt implementeren op uw Azure Stack Edge Pro-apparaat zijn stateless toepassingen of stateful toepassingen.

- **Stateless toepassingen** behouden hun status niet en slaan geen gegevens op in permanente opslag. Alle gebruikers-en sessie gegevens blijven bij de client. Enkele voor beelden van stateless toepassingen zijn web-frontends zoals nginx en andere webtoepassingen.

    U kunt een Kubernetes-implementatie maken om een stateless toepassing in uw cluster te implementeren. 

- **Stateful toepassingen** vereisen dat hun status wordt opgeslagen. Stateful toepassingen gebruiken permanente opslag, zoals permanente volumes, om gegevens op te slaan voor gebruik door de server of door andere gebruikers. Voor beelden van stateful toepassingen zijn data bases zoals [Azure SQL Edge](../azure-sql-edge/overview.md) en MongoDb.

    U kunt een Kubernetes-implementatie maken om een stateful toepassing te implementeren. 

## <a name="deployment-flow"></a>Implementatie stroom

Als u toepassingen op een Azure Stack Edge Pro-apparaat wilt implementeren, volgt u deze stappen: 
 
1. **Toegang configureren**: eerst gebruikt u de Power shell-runs Pace voor het maken van een gebruiker, het maken van een naam ruimte en het verlenen van gebruikers toegang tot die naam ruimte.
2. **Opslag configureren**: vervolgens gebruikt u de resource Azure stack Edge in de Azure Portal om permanente volumes te maken met behulp van statische of dynamische inrichting voor de stateful toepassingen die u gaat implementeren.
3. **Netwerken configureren**: tot slot gebruikt u de services om toepassingen extern en binnen het Kubernetes-cluster beschikbaar te maken.
 
## <a name="deployment-types"></a>Implementatie typen

Er zijn drie manieren om uw workloads te implementeren. Met elk van deze implementatie methoden kunt u verbinding maken met een afzonderlijke naam ruimte op het apparaat en vervolgens stateless of stateful toepassingen implementeren.

![Implementatie van Kubernetes-werk belasting](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Lokale implementatie**: deze implementatie vindt plaats via het opdracht regel programma voor toegang, zoals `kubectl` waarmee u Kubernetes kunt implementeren `yamls` . U hebt via een bestand toegang tot het Kubernetes-cluster op uw Azure Stack Edge Pro `kubeconfig` . Ga voor meer informatie naar [toegang tot een Kubernetes-cluster via kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **IOT Edge-implementatie**: dit is via IOT Edge, waarmee verbinding wordt gemaakt met de Azure-IOT hub. U maakt verbinding met het Kubernetes-cluster op uw Azure Stack Edge Pro-apparaat via de `iotedge` naam ruimte. De IoT Edge-agents die zijn geïmplementeerd in deze naamruimte, zijn verantwoordelijk voor connectiviteit met Azure. U past de `IoT Edge deployment.json` configuratie toe met behulp van Azure DEVOPS CI/cd. De naam ruimte en het IoT Edge beheer worden uitgevoerd via de Cloud operator.

- **Azure Arc enabled Kubernetes-implementatie**: Azure Arc enabled Kubernetes is een Hybrid management tool waarmee u toepassingen kunt implementeren in uw Kubernetes-clusters. U maakt verbinding met het Kubernetes-cluster op uw Azure Stack Edge Pro-apparaat via de `azure-arc namespace` . De agents die in deze naam ruimte zijn geïmplementeerd, zijn verantwoordelijk voor de connectiviteit met Azure. U past de implementatie configuratie toe met behulp van het GitOps-configuratie beheer. 
    
    Met Azure Arc enabled Kubernetes kunt u ook Azure Monitor voor containers gebruiken om uw cluster weer te geven en te controleren. Ga naar [Wat is Azure Arc enabled Kubernetes?](../azure-arc/kubernetes/overview.md)voor meer informatie.
    
    Vanaf 2021 maart is Azure Arc enabled Kubernetes algemeen beschikbaar voor de gebruikers en gelden de standaard gebruiks kosten. Als een klant met een waardevolle preview-versie is de Azure Arc enabled Kubernetes gratis beschikbaar voor Azure Stack edge-apparaten. Als u de preview-aanbieding wilt beschik bare, maakt u een [ondersteuningsaanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest):

    1. Onder **Probleemtype** selecteert u **Facturering**.
    2. Selecteer onder **Abonnement** uw abonnement.
    3. Onder **service** selecteert u **Mijn services** en selecteert u **Azure stack rand**.
    4. Onder **resource** selecteert u uw resource.
    5. Typ onder **samen vatting** een beschrijving van het probleem.
    6. Onder **probleem type** selecteert u **onverwachte kosten**.
    7. Onder **subtype** van het probleem selecteert u **Help me begrijpen van de kosten voor mijn gratis proef versie**.


## <a name="choose-the-deployment-type"></a>Het implementatie type kiezen

Houd bij het implementeren van toepassingen rekening met de volgende informatie:

- **Eén of meer typen**: u kunt kiezen uit één implementatie optie of een combi natie van verschillende implementatie opties.
- **Cloud versus lokaal**: afhankelijk van uw toepassingen kunt u lokale implementatie kiezen via kubectl of Cloud implementatie via IOT Edge en Azure Arc. 
    - Wanneer u een lokale implementatie kiest, bent u beperkt tot het netwerk waarin uw Azure Stack Edge Pro-apparaat wordt geïmplementeerd.
    - Als u een Cloud agent hebt die u kunt implementeren, moet u uw Cloud operator implementeren en Cloud beheer gebruiken.
- **IOT versus Azure Arc**: de keuze van de implementatie is ook afhankelijk van het doel van uw product scenario. Als u toepassingen of containers implementeert die een diep gaande integratie met IoT of IoT-ecosysteem hebben, selecteert u IoT Edge om uw toepassingen te implementeren. Als u bestaande Kubernetes-implementaties hebt, is Azure Arc de voorkeurs keuze.


## <a name="next-steps"></a>Volgende stappen

Als u een app lokaal wilt implementeren via kubectl, raadpleegt u:

- [Implementeer een staatloze toepassing op uw Azure stack Edge Pro via kubectl](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).

Ga als volgt te werk om een app te implementeren via IoT Edge:

- [Implementeer een voorbeeld module op uw Azure stack Edge Pro via IOT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Zie voor informatie over het implementeren van een app via Azure Arc:

- [Implementeer een toepassing met behulp van Azure Arc](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).
