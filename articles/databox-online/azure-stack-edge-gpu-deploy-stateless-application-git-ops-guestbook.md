---
title: PHP- `Guestbook` app implementeren op Arc-Kubernetes op Azure stack Edge Pro GPU-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe u een PHP- `Guestbook` staatloze toepassing implementeert met redis met behulp van GitOps op een Kubernetes-cluster met Arc-functionaliteit van uw Azure stack Edge Pro-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 920f7912a1289fe92618d893b94943784e4a9a3a
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520724"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-pro-gpu"></a>Implementeer een PHP- `Guestbook` staatloze toepassing met redis op het Kubernetes-cluster op basis van Azure stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u een eenvoudige webtoepassing met meerdere lagen bouwt en implementeert met behulp van Kubernetes en Azure Arc. Dit voor beeld bestaat uit de volgende onderdelen:

- Een redis-model met één instantie om vermeldingen op te slaan `guestbook`
- Meerdere gerepliceerde redis-instanties om Lees bewerkingen te behandelen
- Meerdere web-front-end-instanties

De implementatie wordt uitgevoerd met behulp van GitOps op het Kubernetes-cluster met Arc-functionaliteit op uw Azure Stack Edge Pro-apparaat. 

Deze procedure is bedoeld voor personen die de Kubernetes- [workloads op Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-kubernetes-workload-management.md) hebben bekeken en die bekend zijn met de concepten van [Wat is Azure Arc enabled Kubernetes (preview)](../azure-arc/kubernetes/overview.md).

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term slave, een term die Microsoft niet meer gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.

## <a name="prerequisites"></a>Vereisten

Voordat u de stateless toepassing kunt implementeren, moet u ervoor zorgen dat u de volgende vereisten hebt voltooid op uw apparaat en de client die u gaat gebruiken voor toegang tot het apparaat:

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term slave, een term die Microsoft niet meer gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.

### <a name="for-device"></a>Voor het apparaat

1. U hebt aanmeldings referenties naar een Azure Stack Edge Pro-apparaat met één knoop punt.
    1. Het apparaat wordt geactiveerd. Zie [het apparaat activeren](azure-stack-edge-gpu-deploy-activate.md).
    1. Op het apparaat is de compute-rol geconfigureerd via Azure Portal en heeft het een Kubernetes-cluster. Zie [Configure Compute](azure-stack-edge-gpu-deploy-configure-compute.md).

1. U hebt Azure Arc ingeschakeld op het bestaande Kubernetes-cluster op uw apparaat en u hebt een bijbehorende Azure-Arc-resource in de Azure Portal. Zie [Azure Arc inschakelen op Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)voor gedetailleerde stappen.

### <a name="for-client-accessing-the-device"></a>Voor client toegang tot het apparaat

1. U hebt een Windows-client systeem dat wordt gebruikt om toegang te krijgen tot het Azure Stack Edge Pro-apparaat.
  
    - Windows Power shell 5,0 of hoger wordt uitgevoerd op de client. Als u de meest recente versie van Windows Power shell wilt downloaden, gaat u naar [Windows Power Shell installeren](/powershell/scripting/install/installing-windows-powershell).
    
    - U kunt ook een andere client met een [ondersteund besturings systeem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) hebben. In dit artikel wordt de procedure beschreven voor het gebruik van een Windows-client. 
    
1. U hebt de procedure die wordt beschreven in [toegang tot het Kubernetes-cluster op Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-create-kubernetes-cluster.md)voltooid. U hebt het volgende:
    
    - Geïnstalleerd `kubectl` op de client. <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - Zorg ervoor dat de `kubectl` client versie niet meer dan één versie van de Kubernetes-hoofd versie die wordt uitgevoerd op uw Azure stack Edge Pro-apparaat. 
      - Gebruiken `kubectl version` om te controleren welke versie van kubectl op de client wordt uitgevoerd. Noteer de volledige versie.
      - Ga in de lokale gebruikers interface van uw Azure Stack Edge Pro-apparaat naar **overzicht** en noteer het Kubernetes-software nummer. 
      - Controleer deze twee versies op compatibiliteit van de toewijzing die is opgenomen in de ondersteunde Kubernetes-versie. <!--insert link-->

1. U hebt een [GitOps-configuratie die u kunt gebruiken om een implementatie van Azure Arc uit te voeren](https://github.com/kagoyal/dbehaikudemo). In dit voor beeld gebruikt u de volgende `yaml` bestanden om te implementeren op uw Azure stack Edge Pro-apparaat.

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>Configuratie implementeren

Volg deze stappen om de Azure Arc-resource te configureren voor het implementeren van een GitOps-configuratie via de Azure Portal: 

1. Ga in uw Azure Portal naar de Azure-Arc-resource die u hebt gemaakt toen u Azure-boog op het Kubernetes-cluster op uw apparaat inschakelde. 

    ![Naar Azure Arc-resource gaan](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Ga naar **configuraties** en selecteer **+ Configuratie toevoegen**.

    ![Scherm afbeelding toont het Azure Arc enabled Kubernetes-cluster waarvoor configuratie toevoegen is geselecteerd.](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. In **Configuratie toevoegen** voert u de juiste waarden voor de velden in en selecteert u vervolgens **Toep assen**.

    |Parameter  |Beschrijving |
    |---------|---------|
    |Configuratie naam     | Naam voor de configuratie bron.        |
    |Naam operator instantie     |Exemplaar naam van de operator om een specifieke configuratie te identificeren. Name is een teken reeks van Maxi maal 253 tekens die alleen kleine letters, alfanumeriek, afbreek streepjes en punten moeten bevatten.         |
    |Operator naam ruimte     | Ingesteld op **demotestguestbook** om overeen te komen met de naam ruimte die in de implementatie is opgegeven `yaml` . <br> Het veld definieert de naam ruimte waarin de operator is geïnstalleerd. Name is een teken reeks van Maxi maal 253 tekens die alleen kleine letters, alfanumeriek, afbreek streepjes en punten moeten bevatten.         |
    |URL van opslag plaats     |<br>Het pad naar de Git-opslag plaats in `http://github.com/username/repo` of `git://github.com/username/repo` de indeling waar uw GitOps-configuratie zich bevindt.         |
    |Operator bereik     | Selecteer **naam ruimte**. <br>Met deze para meter wordt het bereik gedefinieerd waarop de operator is geïnstalleerd. Selecteer naam ruimte om uw operator te installeren in de naam ruimte die is opgegeven in de implementatie YAML-bestanden.       |
    |Type operator     | Standaard behouden. <br>Met deze para meter wordt het type van de operator opgegeven: standaard ingesteld als stroom.        |
    |Operator-para meters     | Laat dit leeg. <br>Deze para meter bevat para meters die moeten worden door gegeven aan de stroom operator.        |
    |Helm     | Stel deze para meter in op **uitgeschakeld**. <br>Schakel deze optie in als u implementaties op basis van een grafiek wilt uitvoeren.        |


    ![Configuratie toevoegen](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. De configuratie-implementatie wordt gestart en de **operator status** wordt weer gegeven als **in behandeling**. 

    ![Scherm afbeelding toont het Azure Arc enabled Kubernetes-cluster met de status in behandeling tijdens het vernieuwen.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. De implementatie duurt enkele minuten. Wanneer de implementatie is voltooid, wordt de status van de **operator** weer gegeven als **geïnstalleerd**.

    ![Scherm afbeelding toont het Azure Arc enabled Kubernetes-cluster met een geïnstalleerde status.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)

## <a name="verify-deployment"></a>Implementatie controleren

De implementatie via de GitOps-configuratie maakt een `demotestguestbook` naam ruimte zoals opgegeven in de implementatie `yaml` bestanden die zich bevinden in de Git opslag plaats.

1. Nadat u de GitOps-configuratie hebt toegepast, [maakt u verbinding met de Power shell-interface van het apparaat](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Voer de volgende opdracht uit om een lijst weer te geven met de peuling in de `demotestguestbook` naam ruimte die overeenkomt met de implementatie.

    `kubectl get pods -n <your-namespace>`
  
    Hier volgt een voorbeeld uitvoer.
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. In dit voor beeld is de frontend-service geïmplementeerd als type: LoadBalancer. U moet het IP-adres van deze service zoeken om de weer te geven `guestbook` . Voer de volgende opdracht uit.

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. De frontend-service van `type:LoadBalancer` heeft een extern IP-adres. Deze IP is afkomstig uit het IP-adres bereik dat u hebt opgegeven voor externe services bij het configureren van de instellingen van het berekenings netwerk op het apparaat. Gebruik dit IP-adres om de op-URL weer te geven `guestbook` : `https://<external-IP-address>` .

    ![Gastenboek weer geven](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>Implementatie verwijderen

Als u de implementatie wilt verwijderen, kunt u de configuratie uit de Azure Portal verwijderen. Als u de configuratie verwijdert, worden de gemaakte objecten verwijderd, inclusief implementaties en services.

1. Ga in het Azure Portal naar de configuratie van de Azure Arc-resource >. 
1. Zoek de configuratie die u wilt verwijderen. Selecteer de... om het context menu te openen en **verwijderen** te selecteren.
    ![Configuratie verwijderen](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

Het kan enkele minuten duren voordat de configuratie is verwijderd.
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [gebruik van Kubernetes-dash board voor het bewaken van implementaties op uw Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)