---
title: Een Azure SQL Edge-container implementeren in Kubernetes - Azure SQL Edge
description: Meer informatie over het implementeren van een Azure SQL Edge-container in Kubernetes
keywords: SQL Edge, container, kubernetes
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 31a454c93ad5192f387306a8ec557c4e4d3ae991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395288"
---
# <a name="deploy-an-azure-sql-edge-container-in-kubernetes"></a>Het implementeren van een Azure SQL Edge-container in Kubernetes

Azure SQL Edge kan worden geïmplementeerd op een Kubernetes-cluster als een IoT Edge-module via Azure IoT Edge die wordt uitgevoerd op Kubernetes of als zelfstandige containerpod. Voor de rest van dit artikel zullen we ons richten op de implementatie van de zelfstandige container op een kubernetes-cluster. Voor informatie over het implementeren van Azure IoT Edge op Kubernetes, raadpleegt u [Azure IoT Edge op Kubernetes (preview)](https://microsoft.github.io/iotedge-k8s-doc/introduction.html).

In deze zelfstudie ziet u hoe u een maximaal beschikbare Azure SQL Edge-instantie kunt configureren in een container op een kubernetes-cluster. 

> [!div class="checklist"]
> * Een SA-wachtwoord maken
> * Opslag maken
> * De implementatie maken
> * Verbinding maken met SQL Server Management Studio (SSMS)
> * Fout en herstel verifiëren

Kubernetes 1.6 en hoger biedt ondersteuning voor [opslagklassen](https://kubernetes.io/docs/concepts/storage/storage-classes/), [permanente volumeclaims](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims)en het [soort Azure-schijfvolume](https://github.com/kubernetes/examples/tree/master/staging/volumes/azure_disk). U kunt uw Azure SQL Edge-instanties systeemeigen maken en beheren in Kubernetes. In het voorbeeld in dit artikel ziet u hoe u een [implementatie](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) maakt om een configuratie met hoge beschikbaarheid te maken, vergelijkbaar met een clusterexemplaar van een gedeelde schijf. In deze configuratie speelt Kubernetes de rol van de cluster-orchestrator. Wanneer er een fout optreedt in een Azure SQL Edge-instantie in een container, bootstrapt de orchestrator een andere instantie van de container die wordt gekoppeld aan dezelfde permanente opslag.

![Azure SQL Edge in een Kubernetes-cluster](media/deploy-kubernetes/kubernetes-sql-edge.png)

In het voorgaande diagram is `azure-sql-edge` een container in een [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/). Kubernetes organiseert de resources in het cluster. Een [replicaset](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) ervoor zorgt dat de pod automatisch wordt hersteld na een storing in een knooppunt. Toepassingen maken verbinding met de service. In dit geval vertegenwoordigt de service een load balancer die als host fungeert voor een IP-adres dat niet hetzelfde is na het mislukken van de `azure-sql-edge`.

De `azure-sql-edge`-container is mislukt in het volgende diagram. Als orchestrator garandeert Kubernetes het juiste aantal gezonde instanties in de replicaset en wordt een nieuwe container gestart op basis van de configuratie. De orchestrator start een nieuwe pod op hetzelfde knooppunt en `azure-sql-edge` maakt opnieuw verbinding met dezelfde permanente opslag. De service maakt verbinding met de opnieuw gemaakte `azure-sql-edge`.

![Azure SQL Edge in een Kubernetes-cluster nadat de pod is mislukt](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

In het volgende diagram is het knooppunt dat als host fungeert voor de `azure-sql-edge`-container mislukt. De orchestrator start de nieuwe pod op een ander knooppunt en `azure-sql-edge` maakt opnieuw verbinding met dezelfde permanente opslag. De service maakt verbinding met de opnieuw gemaakte `azure-sql-edge`.

![Azure SQL Edge in een Kubernetes-cluster nadat het knooppunt is mislukt](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png)

## <a name="prerequisites"></a>Vereisten

* **Kubernetes-cluster**
   - Voor de zelfstudie is een Kubernetes-cluster vereist. De stappen gebruiken [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) om het cluster te beheren. 

   - Voor het doel van deze zelfstudie gebruiken we Azure Kubernetes Service voor het implementeren van Azure SQL Edge. Zie [Een AKS-cluster (Azure Kubernetes service) implementeren](../aks/tutorial-kubernetes-deploy-cluster.md) voor het maken en verbinden met een Kubernetes-cluster met één knooppunt in AKS met `kubectl`. 

   >[!NOTE]
   >Ter bescherming tegen storingen in een knooppunt heeft een Kubernetes-cluster meer dan één knooppunt nodig.

* **Azure-CLI**
   - De instructies in deze zelfstudie zijn gevalideerd voor Azure CLI 2.10.1.

## <a name="create-a-kubernetes-namespace-for-sql-edge-deployment"></a>Een kubernetes-naamruimte maken voor de implementatie van SQL Edge

Maak een nieuwe naamruimte in het kubernetes-cluster. Deze naamruimte wordt gebruikt voor het implementeren van SQL Edge en alle vereiste artefacten. Zie [Naamruimten](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)voor meer informatie over Kubernetes-naamruimten.

   ```azurecli
   kubectl create namespace <namespace name>
   ```  

## <a name="create-an-sa-password"></a>Een SA-wachtwoord maken

Maak een SA-wachtwoord in het Kubernetes-cluster. Kubernetes kan gevoelige configuratie-informatie, zoals wachtwoorden, beheren als [geheimen](https://kubernetes.io/docs/concepts/configuration/secret/).

Met de volgende opdracht maakt u een wachtwoord voor het SA-account:

   ```azurecli
   kubectl create secret generic mssql --from-literal=SA_PASSWORD="MyC0m9l&xP@ssw0rd" -n <namespace name>
   ```  

   Vervang `MyC0m9l&xP@ssw0rd` door een complex wachtwoord.

## <a name="create-storage"></a>Opslag maken

Configureer een [permanent volume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) en [permanente volumeclaim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volume-claim-protection) in het Kubernetes-cluster. Voer de volgende stappen uit: 

1. Maak een manifest om de opslagklasse en de permanente volumeclaim te definiëren.  In het manifest worden de opslaginrichtings-,-parameters- en [-claimbeleid](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#reclaiming) bepaald. Het Kubernetes-cluster gebruikt dit manifest voor het maken van de permanente opslag. 

   In het volgende YAML-voorbeeld worden een opslagklasse en een permanente volumeclaim gedefinieerd. De inrichting van de opslagklasse is `azure-disk`, omdat dit Kubernetes-cluster zich in Azure bevindt. Het type opslagaccount is `Standard_LRS`. De naam van de permanente volumeclaim is `mssql-data`. De metagegevens van de permanente volumeclaim bevatten een aantekening die wordt gekoppeld aan de opslagklasse. 

   ```yaml
   kind: StorageClass
   apiVersion: storage.k8s.io/v1
   metadata:
        name: azure-disk
   provisioner: kubernetes.io/azure-disk
   parameters:
     storageaccounttype: Standard_LRS
     kind: managed
   ---
   kind: PersistentVolumeClaim
   apiVersion: v1
   metadata:
     name: mssql-data
     annotations:
       volume.beta.kubernetes.io/storage-class: azure-disk
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 8Gi
   ```

   Sla het bestand op (bijvoorbeeld **pvc.yaml**).

2. Maak de permanente volumeclaim in Kubernetes.

   ```azurecli
   kubectl apply -f <Path to pvc.yaml file> -n <namespace name>
   ```

   `<Path to pvc.yaml file>` is de locatie waar u het bestand hebt opgeslagen.

   Het permanente volume wordt automatisch gemaakt als een Azure Storage-account en is gebonden aan de permanente volumeclaim. 

    ![Schermopname van opdracht voor permanente volumeclaim](media/deploy-kubernetes/pvc-cmd.png)

3. Controleer de claim voor de permanente volumeclaim.

   ```azurecli
   kubectl describe pvc <PersistentVolumeClaim>  -n <name of the namespace>
   ```

   De naam van de permanente volumeclaim is `<PersistentVolumeClaim>`.

   In de vorige stap heeft de permanente volumeclaim de naam `mssql-data`. Voer de volgende opdracht uit om de metagegevens van de permanente volumeclaim te bekijken:

   ```azurecli
   kubectl describe pvc mssql-data  -n <namespace name>
   ```

   De geretourneerde metagegevens bevatten een waarde met de naam `Volume`. Deze waarde wordt toegewezen aan de naam van de blob.

   ![Schermopname van geretourneerde metagegevens, inclusief Volume](media/deploy-kubernetes/describe-volume.png)

4. Controleer het permanente volume.

   ```azurecli
   kubectl describe pv -n <namespace name>
   ```

   `kubectl` retourneert metagegevens over het permanente volume dat automatisch is gemaakt en is gebonden aan de permanente volumeclaim. 

## <a name="create-the-deployment"></a>De implementatie maken

In dit voorbeeld wordt de container die de Azure SQL Edge-instantie host, beschreven als een Kubernetes-implementatieobject. De implementatie maakt een replicaset. De replicaset maakt de pod. 

In deze stap maakt u een manifest om de container te beschrijven op basis van de Azure SQL Edge Docker-installatiekopie. Het manifest verwijst naar de `mssql-data`-permanente volumeclaim en het `mssql`-geheim dat u al hebt toegepast op het Kubernetes-cluster. In het manifest wordt ook een [service](https://kubernetes.io/docs/concepts/services-networking/service/)beschreven. Deze service is een load balancer. De load balancer zorgt ervoor dat het IP-adres blijft bestaan nadat het Azure SQL Edge-exemplaar is hersteld. 

1. Maak een manifest (een YAML-bestand) om de implementatie te beschrijven. In het volgende voorbeeld wordt een implementatie beschreven, inclusief een container op basis van de installatiekopie van de Azure SQL Edge-container.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sqledge-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sqledge
  template:
    metadata:
      labels:
        app: sqledge
    spec:
      volumes:
        - name: sqldata
          persistentVolumeClaim:
            claimName: mssql-data
      containers:
        - name: azuresqledge
          image: mcr.microsoft.com/azure-sql-edge:latest
          ports:
            - containerPort: 1433
          volumeMounts:
            - name: sqldata
              mountPath: /var/opt/mssql
          env:
            - name: MSSQL_PID
              value: "Developer"
            - name: ACCEPT_EULA
              value: "Y"
            - name: SA_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mssql
                  key: SA_PASSWORD
            - name: MSSQL_AGENT_ENABLED
              value: "TRUE"
            - name: MSSQL_COLLATION
              value: "SQL_Latin1_General_CP1_CI_AS"
            - name: MSSQL_LCID
              value: "1033"
      terminationGracePeriodSeconds: 30
      securityContext:
        fsGroup: 10001
---
apiVersion: v1
kind: Service
metadata:
  name: sqledge-deployment
spec:
  selector:
    app: sqledge
  ports:
    - protocol: TCP
      port: 1433
      targetPort: 1433
      name: sql
  type: LoadBalancer
```

   Kopieer de voorgaande code naar een nieuw bestand met de naam `sqldeployment.yaml`. Werk de volgende waarden bij: 

   * MSSQL_PID `value: "Developer"`: Hiermee stelt u de container in voor het uitvoeren van Azure SQL Edge Developer Edition. Developer Edition heeft geen licentie voor productiegegevens. Als de implementatie voor productiegebruik is, stelt u de editie in op `Premium`. 

      >[!NOTE]
      >Zie [Licenties voor Azure SQL Edge](https://azure.microsoft.com/pricing/details/sql-edge/)voor meer informatie.

   * `persistentVolumeClaim`: Deze waarde vereist een vermelding voor `claimName:` die wordt toegewezen aan de naam die wordt gebruikt voor de permanente volumeclaim. In deze zelfstudie wordt `mssql-data` gebruikt. 

   * `name: SA_PASSWORD`: Hiermee configureert u de container-installatiekopie om het SA-wachtwoord in te stellen, zoals gedefinieerd in deze sectie.

     ```yaml
     valueFrom:
       secretKeyRef:
         name: mssql
         key: SA_PASSWORD 
     ```

     Wanneer Kubernetes de container implementeert, verwijst deze naar het geheim met de naam `mssql` om de waarde voor het wachtwoord op te halen. 

   >[!NOTE]
   >Met behulp van het servicetype `LoadBalancer` is de Azure SQL Edge-instantie extern toegankelijk (via internet) op poort 1433.

   Sla het bestand op (bijvoorbeeld **sqledgedeploy.yaml**).

2. Maak de implementatie.

   ```azurecli
   kubectl apply -f <Path to sqledgedeploy.yaml file> -n <namespace name>
   ```

   `<Path to sqldeployment.yaml file>` is de locatie waar u het bestand hebt opgeslagen.

   ![Schermafbeelding van de implementatieopdracht](media/deploy-kubernetes/deploy-sql-cmd.png)

   De implementatie en de service worden gemaakt. Het exemplaar van Azure SQL Edge bevindt zich in een container die is verbonden met de permanente opslag.

   Als u de status van de pod wilt weergeven, typt u `kubectl get pod -n <namespace name>`.

   ![Schermafbeelding van de opdracht Get pod](media/deploy-kubernetes/get-sql-pod-cmd.png)

   In de voorgaande afbeelding heeft de pod de status `Running`. Deze status geeft aan dat de container klaar is. Dit kan enkele minuten duren.

   >[!NOTE]
   >Nadat de implementatie is gemaakt, kan het enkele minuten duren voordat de pod zichtbaar is. De vertraging komt doordat het cluster de Azure SQL Edge-container-installatiekopie ophaalt van de docker-hub. Nadat de installatiekopie de eerste keer is opgehaald, kunnen volgende implementaties sneller zijn als de implementatie een knooppunt is waarop de installatiekopie al in de cache is opgeslagen. 

3. Controleer of de services worden uitgevoerd. Voer de volgende opdracht uit:

   ```azurecli
   kubectl get services -n <namespace name>
   ```

   Met deze opdracht worden services geretourneerd die worden uitgevoerd, evenals de interne en externe IP-adressen voor de services. Noteer het externe IP-adres voor de `mssql-deployment`-service. Gebruik dit IP-adres om verbinding te maken met Azure SQL Edge. 

   ![Schermafbeelding van de opdracht Get service](media/deploy-kubernetes/get-service-cmd.png)

   Voor meer informatie over de status van de objecten in het Kubernetes-cluster voert u de volgende handelingen uit:

   ```azurecli
   az aks browse --resource-group <MyResourceGroup> --name <MyKubernetesClustername>
   ```  

## <a name="connect-to-the-azure-sql-edge-instance"></a>Verbinding maken met de SQL Database in de Azure SQL Edge-instantie

Als u de container hebt geconfigureerd zoals beschreven, kunt u verbinding maken met een toepassing van buiten het virtuele netwerk van Azure. Gebruik het `sa`-account en het externe IP-adres voor de service. Gebruik het wachtwoord dat u hebt geconfigureerd als Kubernetes-geheim. Zie [Verbinding maken met Azure SQL Edge](connect.md) voor meer informatie over het maken van een verbinding met een Azure SQL Edge-instantie.

## <a name="verify-failure-and-recovery"></a>Fout en herstel verifiëren

Als u de fout en het herstel wilt controleren, kunt u de pod verwijderen. Voer de volgende stappen uit:

1. Vermeld de pod waarop Azure SQL Edge wordt uitgevoerd.

   ```azurecli
   kubectl get pods -n <namespace name>
   ```

   Noteer de naam van de pod waarop Azure SQL Edge wordt uitgevoerd.

2. Verwijder de pod.

   ```azurecli
   kubectl delete pod sqledge-deployment-7df66c9999-rc9xl
   ```
   `sqledge-deployment-7df66c9999-rc9xl` is de waarde die wordt geretourneerd door de vorige stap voor de naam van de pod. 

Kubernetes maakt de pod automatisch opnieuw om een Azure SQL Edge-instantie te herstellen en maakt verbinding met de permanente opslag. Gebruik `kubectl get pods` om te controleren of een nieuwe pod is geïmplementeerd. Gebruik `kubectl get services` om te controleren of het IP-adres voor de nieuwe container hetzelfde is. 

## <a name="summary"></a>Samenvatting

In deze zelfstudie hebt u geleerd hoe u Azure SQL Edge-containers kunt implementeren in een Kubernetes-cluster voor hoge beschikbaarheid. 

> [!div class="checklist"]
> * Een SA-wachtwoord maken
> * Opslag maken
> * De implementatie maken
> * Verbinding maken met Azure SQL Edge Management Studio (SSMS)
> * Fout en herstel verifiëren

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Kubernetes](../aks/intro-kubernetes.md)
- [Machine Learning en kunstmatige intelligentie met ONNX in SQL Edge](onnx-overview.md).
- [Een end-to-end IoT-oplossing bouwen met SQL Edge met behulp van IoT Edge](tutorial-deploy-azure-resources.md).
- [Gegevensstreaming in Azure SQL Edge](stream-data.md)