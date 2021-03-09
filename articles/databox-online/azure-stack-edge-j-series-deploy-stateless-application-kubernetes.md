---
title: Kubernetes stateless toepassing implementeren op Azure Stack Edge Pro GPU-apparaat met behulp van kubectl | Microsoft Docs
description: Hierin wordt beschreven hoe u een Kubernetes stateless toepassings implementatie maakt en beheert met behulp van kubectl op een Microsoft Azure Stack Edge Pro-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 40065189f3c1dd52f462985aef344e9cbeb12f0b
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522373"
---
# <a name="deploy-a-kubernetes-stateless-application-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Een Kubernetes stateless toepassing implementeren via kubectl op uw Azure Stack Edge Pro GPU-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u een staatloze toepassing implementeert met behulp van kubectl-opdrachten op een bestaand Kubernetes-cluster. Dit artikel begeleidt u stapsgewijs door het proces voor het maken en instellen van een Peul in uw stateless toepassing.

## <a name="prerequisites"></a>Vereisten

Voordat u een Kubernetes-cluster kunt maken en het `kubectl` opdracht regel programma gebruikt, moet u het volgende controleren:

- U hebt aanmeldings referenties naar een Azure Stack Edge Pro-apparaat met één knoop punt.

- Windows Power shell 5,0 of hoger is geïnstalleerd op een Windows-client systeem om toegang te krijgen tot het Azure Stack Edge Pro-apparaat. U kunt ook een andere client met een ondersteund besturings systeem hebben. In dit artikel wordt de procedure beschreven voor het gebruik van een Windows-client. Als u de meest recente versie van Windows Power shell wilt downloaden, gaat u naar [Windows Power Shell installeren](/powershell/scripting/install/installing-windows-powershell).

- Compute is ingeschakeld op het Azure Stack Edge Pro-apparaat. Als u Compute wilt inschakelen, gaat u naar de **Compute** -pagina in de lokale gebruikers interface van het apparaat. Selecteer vervolgens een netwerk interface die u wilt inschakelen voor compute. Selecteer **Inschakelen**. Het inschakelen van Compute-resultaten bij het maken van een virtuele switch op het apparaat op die netwerk interface. Zie [Compute Network inschakelen op uw Azure stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)voor meer informatie.

- Uw Azure Stack Edge Pro-apparaat heeft een Kubernetes-cluster server waarop versie v 1,9 of hoger wordt uitgevoerd. Zie [een Kubernetes-cluster maken en beheren op Microsoft Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-create-kubernetes-cluster.md)voor meer informatie.

- U hebt geïnstalleerd `kubectl` .

## <a name="deploy-a-stateless-application"></a>Een stateless toepassing implementeren

Voordat we beginnen, hebt u het volgende nodig:

1. Er is een Kubernetes-cluster gemaakt.
2. Een naam ruimte instellen.
3. Er is een gebruiker met de naam ruimte gekoppeld.
4. De gebruikers configuratie is opgeslagen in `C:\Users\<username>\.kube` .
5. Geïnstalleerd `kubectl` .

Nu kunt u beginnen met het uitvoeren en beheren van stateless toepassings implementaties op een Azure Stack Edge Pro-apparaat. Voordat u begint met `kubectl` het gebruik van, moet u controleren of u de juiste versie van hebt `kubectl` .

### <a name="verify-you-have-the-correct-version-of-kubectl-and-set-up-configuration"></a>Controleer of u de juiste versie van kubectl hebt en configuratie hebt ingesteld

Controleren van de versie van `kubectl` :

1. Controleer of de versie van `kubectl` is groter dan of gelijk is aan 1,9:

   ```powershell
   kubectl version
   ```
    
   Hieronder ziet u een voor beeld van de uitvoer:
    
   ```powershell
   PS C:\WINDOWS\system32> C:\windows\system32\kubectl.exe version
   Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
   Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
   ```

   In dit geval is de client versie van kubectl v 1.15.2 en is deze compatibel om door te gaan.

2. Bekijk een lijst met de peulingen die worden uitgevoerd op uw Kubernetes-cluster. Een Pod is een toepassings container, of proces, dat wordt uitgevoerd op uw Kubernetes-cluster.

   ```powershell
   kubectl get pods -n <namespace-string>
   ```
    
   Hieronder ziet u een voor beeld van het gebruik van de opdracht:
    
   ```powershell
   PS C:\WINDOWS\system32> kubectl get pods -n "test1"
   No resources found.
   PS C:\WINDOWS\system32>
   ```
    
   In de uitvoer moet worden vermeld dat er geen resources (peulen) worden gevonden omdat er geen toepassingen op uw cluster worden uitgevoerd.

   Met de opdracht wordt de mapstructuur van ' C:\Users \\ &lt; username &gt; \\ . uitvoeren \" met configuratie bestanden gevuld. Met het opdracht regel programma kubectl worden deze bestanden gebruikt voor het maken en beheren van stateless toepassingen op uw Kubernetes-cluster.

3. Controleer de mapstructuur van ' C:\Users \\ &lt; username. uitvoeren ' hand matig &gt; \\ \" om te controleren of het *kubectl* heeft gevuld met de volgende submappen:

   ```powershell
   PS C:\Users\username> ls .kube
    
    
      Directory: C:\Users\user\.kube
    
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----         2/18/2020 11:05 AM                cache
   d-----         2/18/2020 11:04 AM                http-cache
   -a----         2/18/2020 10:41 AM           5377 config
   ```

> [!NOTE]
> Als u een lijst met alle kubectl-opdrachten wilt weer geven, typt u `kubectl --help` .

### <a name="create-a-stateless-application-using-a-deployment"></a>Een staatloze toepassing maken met behulp van een implementatie

Nu u hebt gecontroleerd of de kubectl-opdracht regel versie juist is en u de vereiste configuratie bestanden hebt, kunt u een stateless toepassings implementatie maken.

Een Pod is de basis uitvoerings eenheid van een Kubernetes-toepassing, de kleinste en eenvoudigste eenheid in het Kubernetes-object model dat u maakt of implementeert. Een pod omvat ook opslag resources, een uniek netwerk-IP-adres en opties die bepalen hoe de container (s) moeten worden uitgevoerd.

Het type stateless toepassing dat u maakt, is een nginx-webserver implementatie.

Alle kubectl-opdrachten die u gebruikt om stateless toepassings implementaties te maken en te beheren, moeten de naam ruimte opgeven die aan de configuratie is gekoppeld. U hebt de naam ruimte gemaakt terwijl u verbonden bent met het cluster op het Azure Stack Edge Pro-apparaat in het [cluster een Kubernetes maken en beheren in Microsoft Azure stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md) -zelf studie met `New-HcsKubernetesNamespace` .

Als u de naam ruimte in een kubectl-opdracht wilt opgeven, gebruikt u `kubectl <command> -n <namespace-string>` .

Volg deze stappen om een nginx-implementatie te maken:

1. Een staatloze toepassing Toep assen door een Kubernetes-implementatie object te maken:

   ```powershell
   kubectl apply -f <yaml-file> -n <namespace-string>
   ```

   In dit voor beeld is het pad naar het YAML-bestand van de toepassing een externe bron.

   Hier volgt een voor beeld van het gebruik van de opdracht en de uitvoer:

   ```powershell
   PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment.yaml -n "test1"
    
   deployment.apps/nginx-deployment created
   ```

   U kunt ook de volgende prijs verlaging opslaan op uw lokale computer en het pad en de bestands naam in de para meter *-f* vervangen. Bijvoorbeeld ' C:\Kubernetes\deployment.yaml '. De configuratie voor de toepassings implementatie is als volgt:

   ```markdown
   apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 2 # tells deployment to run 2 pods matching the template
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:1.7.9
           ports:
           - containerPort: 80
   ```

   Met deze opdracht maakt u een standaard nginx met twee peulen om uw toepassing uit te voeren.

2. De beschrijving ophalen van de Kubernetes nginx-implementatie die u hebt gemaakt:

   ```powershell
   kubectl describe deployment nginx-deployment -n <namespace-string>
   ```

   Een voor beeld van het gebruik van de opdracht, met uitvoer, wordt hieronder weer gegeven:
    
   ```powershell
   PS C:\Users\user> kubectl describe deployment nginx-deployment -n "test1"
    
   Name:                   nginx-deployment
   Namespace:              test1
   CreationTimestamp:      Tue, 18 Feb 2020 13:35:29 -0800
   Labels:                 <none>
   Annotations:            deployment.kubernetes.io/revision: 1
                           kubectl.kubernetes.io/last-applied-configuration:
                             {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"test1"},"spec":{"repl...
   Selector:               app=nginx
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.7.9
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
   Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
   OldReplicaSets:  <none>
   NewReplicaSet:   nginx-deployment-5754944d6c (2/2 replicas created)
   Events:
     Type    Reason             Age    From                   Message
     ----    ------             ----   ----                   -------
     Normal  ScalingReplicaSet  2m22s  deployment-controller  Scaled up replica set nginx-deployment-5754944d6c to 2
   ```

   Voor de instelling *replica's* ziet u het volgende:
    
   ```powershell
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   ```

   De instelling *replica's* geeft aan dat uw implementatie specificatie twee peulen vereist en dat deze peulen zijn gemaakt en bijgewerkt en gereed zijn voor gebruik.

   > [!NOTE]
   > Een replicaset vervangt de peulen die worden verwijderd of beëindigd om een wille keurige reden, zoals in het geval van een storing in het knoop punt of een verstoring van een upgrade van het apparaat. Daarom wordt u aangeraden een replicaset te gebruiken, zelfs als uw toepassing slechts één Pod vereist.

3. Om het Peul in uw implementatie weer te geven:

   ```powershell
   kubectl get pods -l app=nginx -n <namespace-string>
   ```
    
   Een voor beeld van het gebruik van de opdracht, met uitvoer, wordt hieronder weer gegeven:
    
   ```powershell
   PS C:\Users\user> kubectl get pods -l app=nginx -n "test1"
    
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-5754944d6c-7wqjd   1/1     Running   0          3m13s
   nginx-deployment-5754944d6c-nfj2h   1/1     Running   0          3m13s
   ```

   De uitvoer controleert of we twee peulen hebben met unieke namen waarnaar we kunnen verwijzen met behulp van kubectl.

4. Informatie weer geven over een afzonderlijke pod in uw implementatie:

   ```powershell
   kubectl describe pod <podname-string> -n <namespace-string>
   ```

  Een voor beeld van het gebruik van de opdracht, met uitvoer, wordt hieronder weer gegeven:

   ```powershell
   PS C:\Users\user> kubectl describe pod "nginx-deployment-5754944d6c-7wqjd" -n "test1"

   Name:           nginx-deployment-5754944d6c-7wqjd
   Namespace:      test1
   Priority:       0
   Node:           k8s-1d9qhq2cl-n1/10.128.46.184
   Start Time:     Tue, 18 Feb 2020 13:35:29 -0800
   Labels:         app=nginx
                   pod-template-hash=5754944d6c
   Annotations:    <none>
   Status:         Running
   IP:             172.17.246.200
   Controlled By:  ReplicaSet/nginx-deployment-5754944d6c
    Containers:
      nginx:
        Container ID:   docker://280b0f76bfdc14cde481dc4f2b8180cf5fbfc90a084042f679d499f863c66979
        Image:          nginx:1.7.9
        Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Tue, 18 Feb 2020 13:35:35 -0800
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-8gksw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True
    Volumes:
      default-token-8gksw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-8gksw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason     Age    From                       Message
      ----    ------     ----   ----                       -------
      Normal  Scheduled  4m58s  default-scheduler          Successfully assigned test1/nginx-deployment-5754944d6c-7wqjd to k8s-1d9qhq2cl-n1
      Normal  Pulling    4m57s  kubelet, k8s-1d9qhq2cl-n1  Pulling image "nginx:1.7.9"
      Normal  Pulled     4m52s  kubelet, k8s-1d9qhq2cl-n1  Successfully pulled image "nginx:1.7.9"
      Normal  Created    4m52s  kubelet, k8s-1d9qhq2cl-n1  Created container nginx
      Normal  Started    4m52s  kubelet, k8s-1d9qhq2cl-n1  Started container nginx
   ```

### <a name="rescale-the-application-deployment-by-increasing-the-replica-count"></a>De toepassings implementatie opnieuw schalen door het aantal replica's te verhogen

Elk Pod is bedoeld om één exemplaar van een bepaalde toepassing uit te voeren. Als u de schaal van uw toepassing horizon taal wilt aanpassen om meerdere exemplaren uit te voeren, kunt u het aantal peulen voor elke instantie verhogen tot één. In Kubernetes wordt dit ook wel replicatie genoemd.
U kunt het aantal peulen in de implementatie van uw toepassing verhogen door een nieuw YAML-bestand toe te passen. Het YAML-bestand wijzigt de instelling van replica's in 4, waardoor het aantal peulen in uw implementatie wordt verhoogd naar vier peulen. Het aantal van 2 tot 4 verhogen:

```powershell
PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment-scale.yaml -n "test1"
```

U kunt ook de volgende prijs verlaging op uw lokale computer opslaan en het pad en de bestands naam voor de para meter *-f* vervangen door `kubectl apply` . Bijvoorbeeld ' C:\Kubernetes\deployment-scale.yaml '. De configuratie voor de schaal van de toepassings implementatie is:

```markdown
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```

Controleren of de implementatie vier peulen heeft:

```powershell
kubectl get pods -l app=nginx
```

Voor beeld van een uitvoer van een implementatie met een schaal aanpassing van twee tot vier peulen wordt hieronder weer gegeven:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -l app=nginx

NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-148880595-4zdqq   1/1       Running   0          25s
nginx-deployment-148880595-6zgi1   1/1       Running   0          25s
nginx-deployment-148880595-fxcez   1/1       Running   0          2m
nginx-deployment-148880595-rwovn   1/1       Running   0          2m
```

Zoals u kunt zien in de uitvoer, hebt u nu vier peul in uw implementatie waarmee uw toepassing kan worden uitgevoerd.

### <a name="delete-a-deployment"></a>Een implementatie verwijderen

Als u de implementatie wilt verwijderen, inclusief alle peulen, moet u `kubectl delete deployment` de naam van de implementatie *nginx-Deployment* en de naam van de naam ruimte opgeven. De implementatie verwijderen:

   ```powershell
   kubectl delete deployment nginx-deployment -n <namespace-string>
   ```

Een voor beeld van het gebruik van de opdracht, met uitvoer, wordt hieronder weer gegeven:

```powershell
PS C:\Users\user> kubectl delete deployment nginx-deployment -n "test1"
deployment.extensions "nginx-deployment" deleted
```

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Kubernetes](azure-stack-edge-gpu-kubernetes-overview.md)