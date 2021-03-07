---
title: Meer informatie over Kubernetes op rollen gebaseerd toegangs beheer op het apparaat met Azure Stack Edge Pro | Microsoft Docs
description: Hierin wordt beschreven hoe op rollen gebaseerd toegangs beheer op een Azure Stack Edge Pro-apparaat plaatsvindt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 1b38a9807e05385a378fa6103710fb6b393c7b1f
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443145"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes op rollen gebaseerd toegangs beheer op uw Azure Stack Edge Pro GPU-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Wanneer u een compute-functie configureert op uw Azure Stack Edge Pro-apparaat, wordt er een Kubernetes-cluster gemaakt. U kunt Kubernetes met op rollen gebaseerd toegangs beheer (Kubernetes RBAC) gebruiken om de toegang tot de cluster bronnen op uw apparaat te beperken.

In deze artikelen vindt u een overzicht van het Kubernetes RBAC-systeem dat wordt geleverd door Kubernetes en hoe Kubernetes RBAC is geïmplementeerd op uw Azure Stack Edge Pro-apparaat. 

## <a name="kubernetes-rbac"></a>Kubernetes RBAC

Met Kubernetes RBAC kunt u gebruikers of groepen gebruikers toewijzen, toestemming geven voor zaken zoals het maken of wijzigen van resources of Logboeken voor het uitvoeren van werk belastingen van toepassingen. Deze machtigingen kunnen worden ingesteld op een enkele naam ruimte of worden verleend in het hele cluster. 

Bij het instellen van het Kubernetes-cluster wordt één gebruiker gemaakt die overeenkomt met dit cluster en wordt de gebruiker van de Cluster beheerder genoemd.  Een `kubeconfig` bestand is gekoppeld aan de gebruiker van de Cluster beheerder. Het `kubeconfig` bestand is een tekst bestand met alle configuratie-informatie die nodig is om verbinding te maken met het cluster om de gebruiker te verifiëren.

## <a name="namespaces-types"></a>Typen naam ruimten

Kubernetes-resources, zoals peulen en implementaties, worden logisch gegroepeerd in een naam ruimte. Deze groeperingen bieden een manier om een Kubernetes-cluster logisch te verdelen en de toegang te beperken tot het maken, weer geven of beheren van resources. Gebruikers kunnen alleen communiceren met resources binnen hun toegewezen naam ruimten.

Naam ruimten zijn bedoeld voor gebruik in omgevingen met veel gebruikers verspreid over meerdere teams of projecten. Zie [Kubernetes-naam ruimten](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)voor meer informatie.

Uw Azure Stack Edge Pro-apparaat heeft de volgende naam ruimten:

- **Systeem naam ruimte** : deze naam ruimte is de belangrijkste bronnen, zoals netwerk functies, zoals DNS en proxy, of het Kubernetes-dash board. Normaal gesp roken implementeert u uw eigen toepassingen niet in deze naam ruimte. Gebruik deze naam ruimte om problemen met een Kubernetes-cluster op te sporen. 

    Uw apparaat bevat meerdere naam ruimten voor het systeem en de namen die overeenkomen met deze systeem naam ruimten zijn gereserveerd. Hier volgt een lijst met de gereserveerde systeem naam ruimten: 
    - kube-system
    - metallb-systeem
    - DBE-naam ruimte
    - standaardinstelling
    - kubernetes-dash board
    - uitvoeren-node-lease
    - kube-public


    Zorg ervoor dat u geen gereserveerde namen gebruikt voor gebruikers naam ruimten die u maakt. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **Gebruikers naam ruimte** : Dit zijn de naam ruimten die u kunt maken via **kubectl** of via de Power shell-interface van het apparaat voor het lokaal implementeren van toepassingen.
 
- **IOT Edge naam ruimte** : u maakt verbinding met deze `iotedge` naam ruimte om toepassingen te beheren die via IOT Edge zijn geïmplementeerd.

- **Azure ARC-naam ruimte** : u maakt verbinding met deze `azure-arc` naam ruimte voor het beheren van toepassingen die via Azure Arc zijn geïmplementeerd. Met Azure Arc kunt u ook toepassingen implementeren in andere gebruikers naam ruimten. 

## <a name="namespaces-and-users"></a>Naam ruimten en gebruikers

In de praktijk is het belang rijk dat u het cluster opsplitst in meerdere naam ruimten. 

- **Meerdere gebruikers**: als u meerdere gebruikers hebt, kunnen deze gebruikers met meerdere naam ruimten hun toepassingen en services in hun specifieke naam ruimten van elkaar isoleren. 
- **Eén gebruiker**: zelfs als er één gebruiker is, kan de gebruiker met meerdere naam ruimten meerdere versies van de toepassingen uitvoeren in hetzelfde Kubernetes-cluster.

### <a name="roles-and-rolebindings"></a>Rollen en RoleBindings

Kubernetes heeft het concept van rol-en functie binding waarmee u machtigingen kunt verlenen aan gebruikers of resources op naam ruimte niveau en op cluster niveau. 

- **Rollen**: u kunt machtigingen definiëren voor gebruikers als een **rol** en vervolgens **rollen** gebruiken om machtigingen te verlenen binnen een naam ruimte. 
- **RoleBindings**: Zodra u de functies hebt gedefinieerd, kunt u **RoleBindings** gebruiken om rollen toe te wijzen voor een bepaalde naam ruimte. 

Met deze benadering kunt u een enkel Kubernetes-cluster logisch scheiden, met gebruikers die alleen toegang hebben tot de toepassings resources in hun toegewezen naam ruimte. 

## <a name="kubernetes-rbac-on-azure-stack-edge-pro"></a>Kubernetes RBAC op Azure Stack Edge Pro

In de huidige implementatie van Kubernetes RBAC kunt u met Azure Stack Edge Pro de volgende acties uitvoeren vanuit een beperkte Power shell-runs Pace:

- Maak naam ruimten.  
- Maak extra gebruikers.
- Verleen u beheerders toegang tot de naam ruimten die u hebt gemaakt. Denk eraan dat u geen toegang hebt tot de rol cluster beheerder of een weer gave van de resources in het hele cluster.
- Haal `kubeconfig` het bestand op met informatie voor toegang tot het Kubernetes-cluster.


Het Azure Stack Edge Pro-apparaat heeft meerdere naam ruimten van het systeem en u kunt gebruikers naam ruimten maken met `kubeconfig` bestanden voor toegang tot deze naam ruimten. De gebruikers hebben volledige controle over deze naam ruimten en kunnen gebruikers maken of wijzigen, of gebruikers toegang verlenen. Alleen de Cluster beheerder heeft volledige toegang tot systeem naam ruimten en cluster bronnen. Een `aseuser` heeft alleen-lezen toegang tot systeem naam ruimten.

Hier volgt een diagram waarin de implementatie van Kubernetes RBAC op Azure Stack Edge Pro-apparaat wordt weer gegeven.

![RBAC op Azure Stack Edge Pro-apparaat Kubernetes](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

In dit diagram hebben Anne, Bob en Chuck alleen toegang tot toegewezen gebruikers naam ruimten, in dit geval, `ns1` `ns2` en `ns3` respectievelijk. Binnen deze naam ruimten hebben ze beheerders toegang. De Cluster beheerder van de andere kant heeft beheerders toegang tot systeem naam ruimten en cluster bronnen.

Als gebruiker kunt u naam ruimten en gebruikers maken, gebruikers toewijzen aan naam ruimten of `kubeconfig` bestanden downloaden. Voor gedetailleerde stapsgewijze instructies gaat u naar [Access Kubernetes cluster via kuebctl op uw Azure stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).


Wanneer u met naam ruimten en gebruikers op uw Azure Stack Edge Pro-apparaten werkt, gelden de volgende voor behoud:

- U mag geen bewerkingen uitvoeren zoals het maken van gebruikers, het verlenen of intrekken van naam ruimte toegang aan de gebruiker voor een van de naam ruimten van het systeem. Voor beelden van naam ruimten van het systeem zijn `kube-system` , `metallb-system` , `kubernetes-dashboard` , `default` ,, `kube-node-lease` `kube-public` . Systeem naam ruimten bevatten ook de naam ruimten die zijn gereserveerd voor implementatie typen, zoals `iotedge` (IOT Edge naam ruimte) en `azure-arc` (Azure Arc namespace).
- U kunt gebruikers naam ruimten maken en binnen die naam ruimten extra gebruikers maken en toegang tot de naam ruimte voor deze gebruikers verlenen of intrekken.
- U mag geen naam ruimten maken met namen die identiek zijn aan die voor een wille keurige systeem naam ruimte. De namen voor systeem naam ruimten zijn gereserveerd.  
- U mag geen gebruikers naam ruimten maken met namen die al worden gebruikt door andere gebruikers naam ruimten. Als u bijvoorbeeld een `test-ns` andere naam ruimte hebt gemaakt, kunt u deze niet maken `test-ns` .
- U mag geen gebruikers maken met namen die al zijn gereserveerd. `aseuser`Is bijvoorbeeld een gereserveerde gebruiker en kan niet worden gebruikt.


## <a name="next-steps"></a>Volgende stappen

Zie [toegang tot een Kubernetes-cluster via kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md)voor informatie over het maken van een gebruiker, het maken van een naam ruimte en het verlenen van gebruikers toegang tot de naam ruimte.

