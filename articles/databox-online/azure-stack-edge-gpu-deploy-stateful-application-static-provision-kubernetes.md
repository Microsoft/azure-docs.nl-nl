---
title: Gebruik kubectl om Kubernetes stateful-app te implementeren via een statisch ingerichte share op Azure Stack Edge Pro-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe u een Kubernetes stateful-toepassings implementatie maakt en beheert via een statisch ingerichte share met behulp van kubectl op een Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/09/2021
ms.author: alkohli
ms.openlocfilehash: 01ba8e1f22deb376fd461be24d3f66f0a7f5e1ae
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102610481"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-a-persistentvolume-on-your-azure-stack-edge-pro-device"></a>Gebruik kubectl om een Kubernetes stateful-toepassing uit te voeren met een PersistentVolume op uw Azure Stack Edge Pro-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u een stateful-toepassing met één instantie in Kubernetes implementeert met behulp van een PersistentVolume (PV) en een implementatie. De implementatie maakt gebruik `kubectl` van opdrachten op een bestaand Kubernetes-cluster en implementeert de MySQL-toepassing. 

Deze procedure is bedoeld voor gebruikers die de Kubernetes- [opslag op Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-kubernetes-storage.md) hebben gecontroleerd en bekend zijn met de concepten van [Kubernetes Storage](https://kubernetes.io/docs/concepts/storage/). 

Azure Stack Edge Pro biedt ook ondersteuning voor het uitvoeren van Azure SQL Edge-containers en deze kunnen op een vergelijk bare manier worden geïmplementeerd als voor MySQL. Zie [Azure SQL Edge](../azure-sql-edge/overview.md)(Engelstalig) voor meer informatie.


## <a name="prerequisites"></a>Vereisten

Voordat u de stateful toepassing kunt implementeren, moet u voldoen aan de volgende vereisten op het apparaat en de client die u gaat gebruiken voor toegang tot het apparaat:

### <a name="for-device"></a>Voor het apparaat

- U hebt aanmeldings referenties naar een Azure Stack Edge Pro-apparaat met één knoop punt.
    - Het apparaat wordt geactiveerd. Zie [het apparaat activeren](azure-stack-edge-gpu-deploy-activate.md).
    - Op het apparaat is de compute-rol geconfigureerd via Azure Portal en heeft het een Kubernetes-cluster. Zie [Configure Compute](azure-stack-edge-gpu-deploy-configure-compute.md).

### <a name="for-client-accessing-the-device"></a>Voor client toegang tot het apparaat

- U hebt een Windows-client systeem dat wordt gebruikt om toegang te krijgen tot het Azure Stack Edge Pro-apparaat.
    - Windows Power shell 5,0 of hoger wordt uitgevoerd op de client. Als u de meest recente versie van Windows Power shell wilt downloaden, gaat u naar [Windows Power Shell installeren](/powershell/scripting/install/installing-windows-powershell).
    
    - U kunt ook een andere client met een [ondersteund besturings systeem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) hebben. In dit artikel wordt de procedure beschreven voor het gebruik van een Windows-client. 
    
    - U hebt de procedure die wordt beschreven in [toegang tot het Kubernetes-cluster op Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-create-kubernetes-cluster.md)voltooid. U hebt het volgende:
      - Een `userns1` naam ruimte is gemaakt via de `New-HcsKubernetesNamespace` opdracht. 
      - Een gebruiker heeft gemaakt `user1` via de `New-HcsKubernetesUser` opdracht. 
      - De `user1` toegang tot `userns1` via de `Grant-HcsKubernetesNamespaceAccess` opdracht is verleend.       
      - Geïnstalleerd `kubectl` op de client en het `kubeconfig` bestand met de gebruikers configuratie opgeslagen op C: \\ gebruikers \\ &lt; gebruikers naam &gt; \\ . uitvoeren. 
    
    - Zorg ervoor dat de `kubectl` client versie niet meer dan één versie van de Kubernetes-hoofd versie die wordt uitgevoerd op uw Azure stack Edge Pro-apparaat. 
        - Gebruiken `kubectl version` om te controleren welke versie van kubectl op de client wordt uitgevoerd. Noteer de volledige versie.
        - Ga in de lokale gebruikers interface van uw Azure Stack Edge Pro-apparaat naar **overzicht** en noteer het Kubernetes-software nummer. 
        - Controleer deze twee versies op compatibiliteit van de toewijzing die is opgenomen in de ondersteunde Kubernetes-versie.<!-- insert link--> 


U bent klaar om een stateful toepassing te implementeren op uw Azure Stack Edge Pro-apparaat. 

## <a name="provision-a-static-pv"></a>Een statisch HW inrichten

Als u een PV statisch wilt inrichten, moet u een share op het apparaat maken. Volg deze stappen om een HW te richten op uw SMB-share. 

> [!NOTE]
> - Het specifieke voor beeld dat in dit artikel wordt gebruikt, werkt niet met NFS-shares. Over het algemeen kunnen NFS-shares op uw Azure Stack edge-apparaat worden ingericht met niet-database toepassingen.
> - Als u stateful toepassingen wilt implementeren die gebruikmaken van opslag volumes om permanente opslag te bieden, raden we u aan om te gebruiken `StatefulSet` . Dit voor beeld wordt gebruikt `Deployment` met slechts één replica en is geschikt voor ontwikkeling en testen. 

1. Kies of u een Edge-share of een Edge-lokale share wilt maken. Volg de instructies in [een share toevoegen](azure-stack-edge-manage-shares.md#add-a-share) om een share te maken. Zorg ervoor dat u het selectie vakje inschakelt voor **het gebruik van de share met Edge Compute**.

    ![Rand lokale share voor hw](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/edge-local-share-static-provision-1.png)

    1. In plaats van een nieuwe share te maken, moet u de share koppelen als u besluit een bestaande share te gebruiken.
    
        Ga in de Azure Portal voor uw Azure Stack Edge-resource naar **shares**. Selecteer en klik in de bestaande lijst met shares op een share die u wilt gebruiken.

        ![Bestaande lokale share selecteren voor hw](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-1.png)

    1. Selecteer **koppelen** en bevestig koppelen wanneer u hierom wordt gevraagd.  

        ![Bestaande lokale share koppelen voor hw](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-2.png)

1. Noteer de share naam. Wanneer deze share wordt gemaakt, wordt er automatisch een object met persistent volume gemaakt in het Kubernetes-cluster dat overeenkomt met de SMB-share die u hebt gemaakt. 

## <a name="deploy-mysql"></a>MySQL implementeren

U kunt nu een stateful toepassing uitvoeren door een Kubernetes-implementatie te maken en deze te verbinden met de HW die u in de vorige stap hebt gemaakt met behulp van een PersistentVolumeClaim (PVC). 

`kubectl`Voor alle opdrachten die u gebruikt voor het maken en beheren van stateful toepassings implementaties moet u de naam ruimte opgeven die aan de configuratie is gekoppeld. Als u de naam ruimte in een kubectl-opdracht wilt opgeven, gebruikt u `kubectl <command> -n <your-namespace>` .

1. Bekijk een lijst met de peulingen die worden uitgevoerd op uw Kubernetes-cluster in uw naam ruimte. Een Pod is een toepassings container, of proces, dat wordt uitgevoerd op uw Kubernetes-cluster.

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   Hier volgt een voorbeeld van het gebruik van de opdracht:
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   In de uitvoer moet worden vermeld dat er geen resources (peulen) worden gevonden omdat er geen toepassingen op uw cluster worden uitgevoerd.

1. U gaat de volgende YAML-bestanden gebruiken. Het `mysql-deployment.yml` bestand beschrijft een implementatie die mysql uitvoert en verwijst naar het PVC. Het bestand definieert een volume koppeling voor `/var/lib/mysql` en maakt vervolgens een PVC dat zoekt naar een volume van 20 GB. 

    Aan deze claim wordt voldaan door een bestaande HW die statisch is ingericht tijdens het maken van de share in de vorige stap. Op uw apparaat wordt een groot PV van 32 TB voor elke share gemaakt. De HW voldoet aan de vereisten die zijn ingesteld door PVC en het PVC moet aan deze PV zijn gebonden.

    Kopieer het volgende bestand en sla het op in `mysql-deployment.yml` een map op de Windows-client die u gebruikt om toegang te krijgen tot het Azure stack Edge Pro-apparaat.
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim
    ```
    
2. Kopieer en sla het `mysql-pv.yml` bestand op in dezelfde map waarin u het hebt opgeslagen `mysql-deployment.yml` . Als u de SMB-share wilt gebruiken die u eerder hebt gemaakt met `kubectl` , stelt u het `volumeName` veld in het PVC-object in op de naam van de share. 

    > [!NOTE] 
    > Zorg ervoor dat de YAML-bestanden de juiste inspringing hebben. U kunt controleren met [yaml pluis](http://www.yamllint.com/) om te valideren en vervolgens op te slaan.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim
    spec:
      volumeName: <smb-share-name-here>
      storageClassName: ""
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Implementeer het `mysql-pv.yaml` bestand.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Hier volgt een voorbeeld uitvoer van de implementatie.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pv.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim created
    
    C:\Users\user>
    ```
   Noteer de naam van het gemaakte PVC. U gebruikt deze in een latere stap. 

4. Implementeer de inhoud van het `mysql-deployment.yml` bestand.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Hier volgt een voorbeeld uitvoer van de implementatie.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
        service/mysql created
        deployment.apps/mysql created
    ```
    
5. Informatie over de implementatie weer geven.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Tue, 18 Aug 2020 09:44:58 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Progressing    True    NewReplicaSetAvailable
      Available      True    MinimumReplicasAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-c85f7f79c (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  10m   deployment-controller  Scaled up replica set mysql-c85f7f79c to 1
    
    C:\Users\user>
    ```
    

6. Geef een lijst weer van de peulen die door de implementatie zijn gemaakt.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Hier volgt een voorbeeld uitvoer.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                    READY   STATUS    RESTARTS   AGE
    mysql-c85f7f79c-vzz7j   1/1     Running   1          14m
    
    C:\Users\user>
    ```
    
7. Inspecteer de PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Hier volgt een voorbeeld uitvoer.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim -n userns1
    Name:          mysql-pv-claim
    Namespace:     userns1
    StorageClass:
    Status:        Bound
    Volume:        mylocalsmbshare1
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim","namespace":"userns1"},"spec":{"acc...
                   pv.kubernetes.io/bind-completed: yes
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      32Ti
    Access Modes:  RWO,RWX
    VolumeMode:    Filesystem
    Mounted By:    mysql-c85f7f79c-vzz7j
    Events:        <none>
    
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Controleren of MySQL wordt uitgevoerd


Als u een opdracht wilt uitvoeren op een container in een pod met MySQL, typt u:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql`

Hier volgt een voorbeeld uitvoer.

```powershell
C:\Users\user>kubectl exec mysql-c85f7f79c-vzz7j -i -t -n userns1 -- mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## <a name="delete-a-deployment"></a>Een implementatie verwijderen

Als u de implementatie wilt verwijderen, verwijdert u de geïmplementeerde objecten op naam. Tot deze objecten behoren implementatie, service en PVC.
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

Hier volgt een voor beeld van de uitvoer van wanneer u de implementatie en de service verwijdert.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
Hier volgt een voor beeld van de uitvoer van wanneer u het PVC verwijdert.

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim -n userns1
persistentvolumeclaim "mysql-pv-claim" deleted
C:\Users\user>
```

De PV is niet meer gekoppeld aan het PVC, omdat het PVC is verwijderd. Als de HW is ingericht toen de share werd gemaakt, moet u de share verwijderen. Volg deze stappen:

1. Ontkoppel de share. Ga in Azure Portal naar uw **Azure stack Edge resource > shares** en selecteer en klik op de share die u wilt ontkoppelen. Selecteer **ontkoppelen** en bevestig de bewerking. Wacht tot de share is ontkoppeld. Met de ontkoppeling wordt de share (en dus de bijbehorende PersistentVolume) vrijgegeven uit het Kubernetes-cluster. 

    ![Lokale share ontkoppelen voor hw](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/unmount-edge-local-share-1.png)

1. U kunt nu **verwijderen** selecteren en bevestigen dat u de share wilt verwijderen. Hiermee verwijdert u ook de share en de bijbehorende hw.

    ![Lokale share voor hw verwijderen](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/delete-edge-local-share-1.png)


## <a name="next-steps"></a>Volgende stappen

Zie [een stateful toepassing implementeren met behulp van dynamische inrichting op een Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md) voor meer informatie over het dynamisch inrichten van opslag.
