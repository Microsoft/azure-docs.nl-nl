---
title: Gebruik kubectl om Kubernetes stateful app te implementeren via een dynamische ingerichte share op Azure Stack Edge Pro GPU-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe u een Kubernetes stateful toepassings implementatie maakt en beheert via een dynamisch ingerichte share met behulp van kubectl op een Microsoft Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 160ba6014bbb2d5cb3ed4e8e4b28a61fe5e8d4cc
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520690"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-storageclass-on-your-azure-stack-edge-pro-gpu-device"></a>Gebruik kubectl om een Kubernetes stateful-toepassing uit te voeren met StorageClass op uw Azure Stack Edge Pro GPU-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u een stateful toepassing met één exemplaar implementeert in Kubernetes met behulp van een StorageClass om opslag en een implementatie dynamisch in te richten. De implementatie maakt gebruik `kubectl` van opdrachten op een bestaand Kubernetes-cluster en implementeert de MySQL-toepassing. 

Deze procedure is bedoeld voor gebruikers die de Kubernetes- [opslag op Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-kubernetes-storage.md) hebben gecontroleerd en bekend zijn met de concepten van [Kubernetes Storage](https://kubernetes.io/docs/concepts/storage/).


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
        - Controleer deze twee versies op compatibiliteit van de toewijzing die is opgenomen in de ondersteunde Kubernetes-versie<!-- insert link-->. 


U bent klaar om een stateful toepassing te implementeren op uw Azure Stack Edge Pro-apparaat. 


## <a name="deploy-mysql"></a>MySQL implementeren

U kunt nu een stateful toepassing uitvoeren door een Kubernetes-implementatie te maken en deze te verbinden met de ingebouwde StorageClass met behulp van een PersistentVolumeClaim (PVC). 

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

1. U gaat de volgende YAML-bestanden gebruiken. Het `mysql-deployment.yml` bestand beschrijft een implementatie die mysql uitvoert en verwijst naar het PVC. Het bestand definieert een volume koppeling voor `/var/lib/mysql` en maakt vervolgens een PVC dat zoekt naar een volume van 20 GB. Een dynamische hw wordt ingericht en de PVC is gebonden aan deze hw.

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
              claimName: mysql-pv-claim-sc
    ```
    
2. Kopieer en sla het `mysql-pvc.yml` bestand op in dezelfde map waarin u het hebt opgeslagen `mysql-deployment.yml` . Als u de ingebouwde StorageClass wilt gebruiken die Azure Stack Edge Pro-apparaat op een gekoppelde gegevens schijf, stelt `storageClassName` u het veld in het PVC-object in op `ase-node-local` en accessModes `ReadWriteOnce` . 

    > [!NOTE] 
    > Zorg ervoor dat de YAML-bestanden de juiste inspringing hebben. U kunt controleren met [yaml pluis](http://www.yamllint.com/) om te valideren en vervolgens op te slaan.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim-sc
    spec:
      storageClassName: ase-node-local
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Implementeer het `mysql-pvc.yaml` bestand.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Hier volgt een voorbeeld uitvoer van de implementatie.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pvc.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim-sc created
    C:\Users\user>
    ```
   Noteer de naam van het PVC dat is gemaakt: in dit voor beeld `mysql-pv-claim-sc` . U gebruikt deze in een latere stap.

4. Implementeer de inhoud van het `mysql-deployment.yml` bestand.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Hier volgt een voorbeeld uitvoer van de implementatie.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
    service/mysql created
    deployment.apps/mysql created
    C:\Users\user>
    ```
    
5. Informatie over de implementatie weer geven.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Thu, 20 Aug 2020 11:14:25 -0700
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
        ClaimName:  mysql-pv-claim-sc
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-695c4d9dcd (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  24s   deployment-controller  Scaled up replica set mysql-695c4d9dcd to 1
    C:\Users\user>
    ```
    

6. Geef een lijst weer van de peulen die door de implementatie zijn gemaakt.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Hier volgt een voorbeeld uitvoer.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                     READY   STATUS    RESTARTS   AGE
    mysql-695c4d9dcd-rvzff   1/1     Running   0          40s
    C:\Users\user>
    ```
    
7. Inspecteer de PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Hier volgt een voorbeeld uitvoer.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim-sc -n userns1
    Name:          mysql-pv-claim-sc
    Namespace:     userns1
    StorageClass:  ase-node-local
    Status:        Bound
    Volume:        pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim-sc","namespace":"userns1"},"spec":{"...
                   pv.kubernetes.io/bind-completed: yes
                   pv.kubernetes.io/bound-by-controller: yes
                   volume.beta.kubernetes.io/storage-provisioner: rancher.io/local-path
                   volume.kubernetes.io/selected-node: k8s-3q7lhq2cl-3q7lhq2
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      20Gi
    Access Modes:  RWO
    VolumeMode:    Filesystem
    Mounted By:    mysql-695c4d9dcd-rvzff
    Events:
      Type    Reason                 Age                From                                                                                                Message
      ----    ------                 ----               ----                                                                                                -------
      Normal  WaitForFirstConsumer   71s (x2 over 77s)  persistentvolume-controller                                                                         waiting for first consumer to be created before binding
      Normal  ExternalProvisioning   62s                persistentvolume-controller                                                                         waiting for a volume to be created, either by external provisioner "rancher.io/local-path" or manually created by system administrator
      Normal  Provisioning           62s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  External provisioner is provisioning volume for claim "userns1/mysql-pv-claim-sc"
      Normal  ProvisioningSucceeded  60s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  Successfully provisioned volume pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Controleren of MySQL wordt uitgevoerd

Als u wilt controleren of de toepassing wordt uitgevoerd, typt u:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql -p`

Geef het wacht woord op wanneer u hierom wordt gevraagd. Het wacht woord is in het `mysql-deployment` bestand.

Hier volgt een voorbeeld uitvoer.

```powershell
C:\Users\user>kubectl exec mysql-695c4d9dcd-rvzff -i -t -n userns1 -- mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
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
C:\Users\user>kubectl delete pvc mysql-pv-claim-sc -n userns1
persistentvolumeclaim "mysql-pv-claim-sc" deleted
C:\Users\user>
```                                                                                         


## <a name="next-steps"></a>Volgende stappen

Zie [een stateless toepassing implementeren op een Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md) voor meer informatie over het configureren van netwerken via kubectl