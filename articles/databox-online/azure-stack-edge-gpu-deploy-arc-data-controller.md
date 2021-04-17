---
title: Implementeer een Azure Arc-gegevenscontroller op uw Azure Stack Edge Pro GPU-apparaat| Microsoft Docs
description: In dit artikel wordt beschreven hoe u een Azure Arc-gegevenscontroller en Azure Data Services implementeert op Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: d56e03cd650032a775c30b02d939cf934f384fae
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568604"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Azure-Data Services implementeren op uw Azure Stack Edge Pro GPU-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u een Azure Arc-gegevenscontroller maakt en vervolgens Azure Data Services implementeert op Azure Stack Edge Pro GPU-apparaat. 

Azure Arc-gegevenscontroller is het lokale besturingsvlak waarmee Azure-Data Services in door de klant beheerde omgevingen. Zodra u de Azure Arc-gegevenscontroller hebt gemaakt op het Kubernetes-cluster dat wordt uitgevoerd op uw GPU-apparaat Azure Stack Edge Pro, kunt u Azure Data Services zoals SQL Managed Instance (preview) op die gegevenscontroller implementeren.

De procedure voor het maken van een gegevenscontroller en het implementeren van een SQL Managed Instance omvat het gebruik van PowerShell en : een systeemeigen hulpprogramma dat opdrachtregeltoegang biedt tot het `kubectl` Kubernetes-cluster op het apparaat.


## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt toegang tot een GPU Azure Stack Edge Pro apparaat en u hebt uw apparaat geactiveerd zoals beschreven in [Activeren Azure Stack Edge Pro.](azure-stack-edge-gpu-deploy-activate.md)

1. U hebt de rekenrol ingeschakeld op het apparaat. Er is ook een Kubernetes-cluster op het apparaat gemaakt toen u rekenkracht op het apparaat configureerde volgens de instructies in Compute configureren op uw [GPU-apparaat Azure Stack Edge Pro gpu.](azure-stack-edge-gpu-deploy-configure-compute.md)

1. U hebt het Kubernetes API-eindpunt van de **pagina Apparaat** van uw lokale webinterface. Zie de instructies in [Kubernetes API-eindpunt verkrijgen voor meer informatie.](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)

1. U hebt toegang tot een client die verbinding maakt met uw apparaat. 
    1. In dit artikel wordt een Windows-clientsysteem met PowerShell 5.0 of hoger gebruikt voor toegang tot het apparaat. U kunt elke andere client met een ondersteund [besturingssysteem gebruiken.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 
    1. Installeer `kubectl` op uw client. Voor de clientversie:
        1. Identificeer de Kubernetes-serverversie die op het apparaat is geïnstalleerd. Ga in de lokale gebruikersinterface van het apparaat naar **de pagina Software-updates.** Noteer **de Kubernetes-serverversie** op deze pagina.
        1. Download een client met een versie die maximaal één secundaire versie is verwijderd van de hoofdversie. De clientversie, maar kan de master door maximaal één secundaire versie leiden. Een v1.3-master moet bijvoorbeeld werken met v1.1-, v1.2- en v1.3-knooppunten en moet werken met v1.2-, v1.3- en v1.4-clients. Zie Ondersteuningsbeleid voor Kubernetes-versie en versieverschil voor meer informatie over de [Kubernetes-clientversie.](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)
    
1. Installeer desgewenst [clienthulpprogramma's voor het implementeren en beheren Azure Arc-gegevensservices](../azure-arc/data/install-client-tools.md). Deze hulpprogramma's zijn niet vereist, maar worden wel aanbevolen.  
1. Zorg ervoor dat er voldoende resources beschikbaar zijn op uw apparaat om een gegevenscontroller en één SQL Managed Instance. Voor een gegevenscontroller en SQL Managed Instance hebt u minimaal 16 GB RAM-geheugen en 4 CPU-kernen nodig. Ga voor gedetailleerde richtlijnen naar [Minimale vereisten voor Azure Arc-gegevensservices implementatie.](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements)


## <a name="configure-kubernetes-external-service-ips"></a>Externe KUbernetes-service-IP's configureren

1. Ga naar de lokale webinterface van het apparaat en ga vervolgens naar **Compute.**
1. Selecteer het netwerk dat is ingeschakeld voor rekenkracht. 

    ![Pagina Berekening in lokale webinterface 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Zorg ervoor dat u drie extra externe Kubernetes-service-IP's levert (naast de IP's die u al hebt geconfigureerd voor andere externe services of containers). De gegevenscontroller gebruikt twee service-IP's en het derde IP-adres wordt gebruikt wanneer u een SQL Managed Instance. U hebt één IP-adres nodig voor elke extra gegevensservice die u wilt implementeren. 

    ![Pagina Berekening in lokale webinterface 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. Pas de instellingen toe. Deze nieuwe IP's worden onmiddellijk van kracht op een bestaand Kubernetes-cluster. 


## <a name="deploy-azure-arc-data-controller"></a>Een Azure Arc implementeren

Voordat u een gegevenscontroller implementeert, moet u een naamruimte maken.

### <a name="create-namespace"></a>Een naamruimte maken 

Maak een nieuwe, toegewezen naamruimte waarin u de gegevenscontroller gaat implementeren. U maakt ook een gebruiker en verleent de gebruiker vervolgens toegang tot de naamruimte die u hebt gemaakt. 

> [!NOTE]
> Voor zowel naamruimte als gebruikersnamen zijn de naamconventieconventie voor [DNS-subdomeinen](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) van toepassing.

1. [Maak verbinding met de PowerShell-interface](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Een naamruimte maken. Type:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Maak een gebruiker. Type: 

    `New-HcsKubernetesUser -UserName <User name>`

1. Een configuratiebestand wordt weergegeven als tekst zonder tekst. Kopieer dit bestand en sla het op als *een configuratiebestand.* 

    > [!IMPORTANT]
    > Sla het configuratiebestand niet op als *TXT-bestand,* sla het bestand op zonder bestandsextensie.

1. Het configuratiebestand moet in de map `.kube` van uw gebruikersprofiel op de lokale computer staan. Kopieer het bestand naar die map in uw gebruikersprofiel.

    ![Locatie van configuratiebestand op client](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. Verleen de gebruiker toegang tot de naamruimte die u hebt gemaakt. Type: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Hier is een voorbeelduitvoer van de voorgaande opdrachten. In dit voorbeeld maken we een `myadstest` naamruimte, een gebruiker en hebben we de `myadsuser` gebruiker toegang verleend tot de naamruimte.
    
    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesNamespace -Namespace myadstest
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName myadsuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBD=======//snipped//=======VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: myadsuser
      name: myadsuser@kubernetes
    current-context: myadsuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: myadsuser
      user:
        client-certificate-data: LS0tLS1CRUdJTiBDRV=========//snipped//=====EE9PQotLS0kFURSBLRVktLS0tLQo=
    
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myadstest -UserName myadsuser
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS>
    ```
1. Voeg een DNS-vermelding toe aan het hosts-bestand op uw systeem. 

    1. Voer Kladblok uit als beheerder en open `hosts` het bestand dat zich bevindt op `C:\windows\system32\drivers\etc\hosts` . 
    2. Gebruik de informatie die u hebt opgeslagen op de pagina **Apparaat** in de lokale gebruikersinterface (vereiste) om de vermelding in het hosts-bestand te maken. 

        Kopieer bijvoorbeeld dit eindpunt om de volgende vermelding te maken met het IP-adres van het apparaat `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` en het DNS-domein: 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Als u wilt controleren of u verbinding kunt maken met de Kubernetes-pods, start u een cmd-prompt of een PowerShell-sessie. Type:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
U kunt nu uw toepassingen voor gegevenscontrollers en gegevensservices implementeren in de naamruimte en vervolgens de toepassingen en hun logboeken bekijken.

### <a name="create-data-controller"></a>Gegevenscontroller maken

De gegevenscontroller is een verzameling pods die zijn geïmplementeerd in uw Kubernetes-cluster om een API, de controllerservice, de bootstrapper en de bewakingsdatabases en dashboards te bieden. Volg deze stappen om een gegevenscontroller te maken op het Kubernetes-cluster dat zich op uw Azure Stack Edge bevindt in de naamruimte die u eerder hebt gemaakt.   

1. Verzamel de volgende informatie die u nodig hebt om een gegevenscontroller te maken:

    
    |Kolom 1  |Kolom 2  |
    |---------|---------|
    |Naam van gegevenscontroller     |Een beschrijvende naam voor uw gegevenscontroller. Bijvoorbeeld `arctestdatacontroller`.         |
    |Gebruikersnaam van gegevenscontroller     |Een gebruikersnaam voor de beheerder van de gegevenscontroller. De gebruikersnaam en het wachtwoord van de gegevenscontroller worden gebruikt voor verificatie bij de gegevenscontroller-API om beheerfuncties uit te voeren.          |
    |Wachtwoord voor gegevenscontroller     |Een wachtwoord voor de beheerder van de gegevenscontroller. Kies een veilig wachtwoord en deel dit met alleen gebruikers die beheerdersbevoegdheden voor het cluster nodig hebben.         |
    |Naam van uw Kubernetes-naamruimte     |De naam van de Kubernetes-naamruimte waarin u de gegevenscontroller wilt maken.         |
    |Azure-abonnements-id     |De GUID van het Azure-abonnement voor waar u de gegevenscontrollerresource in Azure wilt maken.         |
    |Naam van Azure-resourcegroep     |De naam van de resourcegroep waarin u de gegevenscontrollerresource in Azure wilt maken.         |
    |Azure-locatie     |De Azure-locatie waar de metagegevens van de gegevenscontrollerresource worden opgeslagen in Azure. Zie Globale Azure-infrastructuur /Producten per regio voor een lijst met beschikbare regio's.|


1. Maak verbinding met de PowerShell-interface. Als u de gegevenscontroller wilt maken, typt u: 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Hier is een voorbeeld van de uitvoer van de voorgaande opdrachten.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    Het voltooien van de implementatie kan ongeveer 5 minuten duren.

    > [!NOTE]
    > De gegevenscontroller die is gemaakt in het Kubernetes-cluster op uw Azure Stack Edge Pro GPU-apparaat werkt alleen in de modus zonder verbinding in de huidige versie. De niet-verbonden modus is voor de gegevenscontroller en niet voor uw apparaat.

### <a name="monitor-data-creation-status"></a>De status van het maken van gegevens controleren

1. Open een ander PowerShell-venster.
1. Gebruik de volgende `kubectl` opdracht om de aanmaakstatus van de gegevenscontroller te controleren. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    Wanneer de controller is gemaakt, moet de status `Ready` zijn.
    Hier is een voorbeeld van de uitvoer van de voorgaande opdracht:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. Gebruik de opdracht om de IP's te identificeren die zijn toegewezen aan de externe services die worden uitgevoerd op de `kubectl get svc -n <namespace>` gegevenscontroller. Hier volgt een voorbeeld van uitvoer:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get svc -n myadstest
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                       AGE
    controldb-svc             ClusterIP      172.28.157.130   <none>        1433/TCP,8311/TCP,8411/TCP                    3d21h
    controller-svc            ClusterIP      172.28.123.251   <none>        443/TCP,8311/TCP,8301/TCP,8411/TCP,8401/TCP   3d21h
    controller-svc-external   LoadBalancer   172.28.154.30    10.57.48.63   30080:31090/TCP                               3d21h
    logsdb-svc                ClusterIP      172.28.52.196    <none>        9200/TCP,8300/TCP,8400/TCP                    3d20h
    logsui-svc                ClusterIP      172.28.85.97     <none>        5601/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdb-svc             ClusterIP      172.28.255.103   <none>        8086/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdc-svc             ClusterIP      172.28.208.191   <none>        8300/TCP,8400/TCP                             3d20h
    metricsui-svc             ClusterIP      172.28.158.163   <none>        3000/TCP,8300/TCP,8400/TCP                    3d20h
    mgmtproxy-svc             ClusterIP      172.28.228.229   <none>        443/TCP,8300/TCP,8311/TCP,8400/TCP,8411/TCP   3d20h
    mgmtproxy-svc-external    LoadBalancer   172.28.166.214   10.57.48.64   30777:30621/TCP                               3d20h
    sqlex-svc                 ClusterIP      None             <none>        1433/TCP                                      3d20h
    PS C:\WINDOWS\system32>
    ```

## <a name="deploy-sql-managed-instance"></a>Sql Managed Instance implementeren

Nadat u de gegevenscontroller hebt gemaakt, kunt u een sjabloon gebruiken om een SQL Managed Instance op de gegevenscontroller te implementeren.

### <a name="deployment-template"></a>Implementatiesjabloon

Gebruik de volgende implementatiesjabloon om een SQL Managed Instance op de gegevenscontroller op uw apparaat te implementeren.

```yml
apiVersion: v1
data:
    password: UGFzc3dvcmQx
    username: bXlhZHN1c2Vy
kind: Secret
metadata:
    name: sqlex-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
    name: sqlex
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
    service:
    type: LoadBalancer
    storage:
    backups:
        className: ase-node-local
        size: 5Gi
    data:
        className: ase-node-local
        size: 5Gi
    datalogs:
        className: ase-node-local
        size: 5Gi
    logs:
        className: ase-node-local
        size: 1Gi
```


#### <a name="metadata-name"></a>Metagegevensnaam
    
De naam van de metagegevens is de naam van de SQL Managed Instance. De gekoppelde pod in de voorgaande `deployment.yaml` krijgt de naam ( is het aantal `sqlex-n` `n` pods dat aan de toepassing is gekoppeld). 
    
#### <a name="password-and-username-data"></a>Wachtwoord- en gebruikersnaamgegevens

De gebruikersnaam en het wachtwoord van de gegevenscontroller worden gebruikt voor verificatie bij de gegevenscontroller-API om beheerfuncties uit te voeren. Het Kubernetes-geheim voor de gebruikersnaam en het wachtwoord van de gegevenscontroller in de implementatiesjabloon zijn met base64 gecodeerde tekenreeksen. 

U kunt een online hulpprogramma gebruiken om uw gewenste gebruikersnaam en wachtwoord te coderen met base64, of u kunt ingebouwde CLI-hulpprogramma's gebruiken, afhankelijk van uw platform. Wanneer u een online Base64-coderingshulpprogramma gebruikt, geeft u de gebruikersnaam en wachtwoordreeksen op (die u hebt ingevoerd tijdens het maken van de gegevenscontroller) in het hulpprogramma. Het hulpprogramma genereert dan de bijbehorende met Base64 gecodeerde tekenreeksen.
    
#### <a name="service-type"></a>Servicetype

Het servicetype moet worden ingesteld op `LoadBalancer` .
    
#### <a name="storage-class-name"></a>Naam van opslagklasse

U kunt de opslagklasse op uw Azure Stack Edge die door de implementatie wordt gebruikt voor gegevens, back-ups, gegevenslogboeken en logboeken. Gebruik de  `kubectl get storageclass` opdracht om de opslagklasse op uw apparaat te laten geïmplementeerd.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
In de voorgaande voorbeelduitvoer moet de opslagklasse op uw apparaat `ase-node-local` worden opgegeven in de sjabloon.
 
#### <a name="spec"></a>Spec

Als u een SQL Managed Instance op uw Azure Stack Edge apparaat wilt maken, kunt u uw geheugen- en CPU-vereisten opgeven in de specificatiesectie van de `deployment.yaml` . Elk met SQL beheerd exemplaar moet minimaal 2 GB geheugen en 1 CPU-kern aanvragen, zoals wordt weergegeven in het volgende voorbeeld. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

Voor gedetailleerde richtlijnen voor het formaat van gegevenscontrollers en 1 SQL Managed Instance, bekijkt u Details van de formaat van [SQL Managed Instance.](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details)

### <a name="run-deployment-template"></a>Implementatiesjabloon uitvoeren

Voer de `deployment.yaml` uit met behulp van de volgende opdracht:

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

Hier volgt een voorbeeld van de uitvoer van de volgende opdracht:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
No resources found.
PS C:\WINDOWS\system32> 
PS C:\WINDOWS\system32> kubectl create -n myadstest -f C:\azure-arc-data-services\sqlex.yaml  secret/sqlex-login-secret created
sqlmanagedinstance.sql.arcdata.microsoft.com/sqlex created
PS C:\WINDOWS\system32> kubectl get pods --namespace myadstest
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-mv2cd   1/1     Running   0          83m
control-w9s9l        2/2     Running   0          78m
controldb-0          2/2     Running   0          78m
controlwd-4bmc5      1/1     Running   0          64m
logsdb-0             1/1     Running   0          64m
logsui-wpmw2         1/1     Running   0          64m
metricsdb-0          1/1     Running   0          64m
metricsdc-fb5r5      1/1     Running   0          64m
metricsui-56qzs      1/1     Running   0          64m
mgmtproxy-2ckl7      2/2     Running   0          64m
sqlex-0              3/3     Running   0          13m
PS C:\WINDOWS\system32>
```

De `sqlex-0` pod in de voorbeelduitvoer geeft de status van de SQL Managed Instance.

## <a name="remove-data-controller"></a>Gegevenscontroller verwijderen

Als u de gegevenscontroller wilt verwijderen, verwijdert u de toegewezen naamruimte waarin u deze hebt geïmplementeerd.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Volgende stappen

- [Implementeer een staatloze toepassing op uw Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).
