---
title: Een Azure Arc-gegevens controller implementeren op uw Azure Stack Edge Pro GPU-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe u een Azure-Arc-gegevens controller en Azure Data Services implementeert op uw Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 57633df8c6482a9b0645813519991282bdbf22c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102633509"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Azure-Data Services implementeren op uw Azure Stack Edge Pro GPU-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt het proces beschreven voor het maken van een Azure Arc-gegevens controller en het implementeren van Azure-Data Services op uw Azure Stack Edge Pro GPU-apparaat. 

Azure Arc data controller is het lokale besturings vlak waarmee Azure-Data Services in door de klant beheerde omgevingen worden ingeschakeld. Wanneer u de Azure Arc-gegevens controller hebt gemaakt op het Kubernetes-cluster dat wordt uitgevoerd op uw Azure Stack Edge Pro-apparaat, kunt u Azure Data Services, zoals SQL Managed instance (preview), implementeren op die gegevens controller.

De procedure voor het maken van een gegevens controller en het implementeren van een SQL Managed instance is het gebruik van Power shell en `kubectl` -een systeem eigen hulp programma dat opdracht regel toegang biedt tot het Kubernetes-cluster op het apparaat.


## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt toegang tot een Azure Stack Edge Pro-apparaat en u hebt uw apparaat geactiveerd zoals beschreven in [Azure stack Edge Pro activeren](azure-stack-edge-gpu-deploy-activate.md).

1. U hebt de compute-functie ingeschakeld op het apparaat. Er is ook een Kubernetes-cluster op het apparaat gemaakt toen u Compute op het apparaat hebt geconfigureerd volgens de instructies in [Configure Compute op uw Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-deploy-configure-compute.md).

1. U hebt het Kubernetes API-eind punt van de pagina **apparaat** van uw lokale webgebruikersinterface. Zie de instructies in [Get KUBERNETES API endpoint](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)(Engelstalig) voor meer informatie.

1. U hebt toegang tot een client die verbinding maakt met uw apparaat. 
    1. In dit artikel wordt gebruikgemaakt van een Windows-client systeem met Power shell 5,0 of hoger voor toegang tot het apparaat. U kunt elke andere client met een [ondersteund besturings systeem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)gebruiken. 
    1. Installeer `kubectl` op de client. Voor de client versie:
        1. Identificeer de Kubernetes-Server versie die op het apparaat is geïnstalleerd. Ga in de lokale gebruikers interface van het apparaat naar de pagina **software-updates** . Noteer de **Kubernetes-Server versie** op deze pagina.
        1. Download een client met een versie die maximaal één secundaire versie is verwijderd van de hoofdversie. De client versie, maar kan het hoofd naar een secundaire versie leiden. Een v 1.3-Master zou bijvoorbeeld moeten werken met de knoop punten v 1.1, v 1.2 en v 1.3, en moet werken met clients met de v 1.2, v 1.3 en v 1.4. Zie [ondersteunings beleid voor Kubernetes-versie en-versie](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)voor meer informatie over de Kubernetes-client versie.
    
1. [Installeer eventueel client hulpprogramma's voor het implementeren en beheren van gegevens services die geschikt zijn voor Azure Arc](../azure-arc/data/install-client-tools.md). Deze hulpprogram ma's zijn niet vereist, maar worden aanbevolen.  
1. Zorg ervoor dat er voldoende bronnen beschikbaar zijn op uw apparaat om een gegevens controller en één SQL-beheerd exemplaar in te richten. Voor gegevens controller en één SQL Managed instance hebt u Mini maal 16 GB RAM-geheugen en 4 CPU-kernen nodig. Voor gedetailleerde informatie gaat u naar [minimale vereisten voor Azure Arc enabled Data Services-implementatie](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements).


## <a name="configure-kubernetes-external-service-ips"></a>IP-adressen van Kubernetes External service configureren

1. Ga naar de lokale web-UI van het apparaat en ga vervolgens naar **Compute**.
1. Selecteer het netwerk dat voor Compute is ingeschakeld. 

    ![Pagina Berekening in lokale webinterface 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Zorg ervoor dat u drie extra IP-adressen van Kubernetes External service biedt (naast de Ip's die u al hebt geconfigureerd voor andere externe services of containers). De gegevens controller gebruikt twee service-Ip's en het derde IP-adres wordt gebruikt bij het maken van een SQL-beheerd exemplaar. U hebt één IP-adres nodig voor elke extra gegevens service die u gaat implementeren. 

    ![Pagina Berekening in lokale webinterface 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. De instellingen Toep assen en deze nieuwe IP-adressen worden direct van kracht op een al bestaande Kubernetes-cluster. 


## <a name="deploy-azure-arc-data-controller"></a>Azure Arc data controller implementeren

Voordat u een gegevens controller implementeert, moet u een naam ruimte maken.

### <a name="create-namespace"></a>Een naamruimte maken 

Maak een nieuwe, toegewezen naam ruimte waarin u de gegevens controller gaat implementeren. U maakt ook een gebruiker en verleent vervolgens gebruikers de toegang tot de naam ruimte die u hebt gemaakt. 

> [!NOTE]
> Voor zowel naam ruimte-als gebruikers namen gelden de [naamgevings regels voor DNS-subdomeinen](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) .

1. [Verbinding maken met de Power shell-interface](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Een naamruimte maken. Type:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Maak een gebruiker. Type: 

    `New-HcsKubernetesUser -UserName <User name>`

1. Een configuratie bestand wordt weer gegeven als tekst zonder opmaak. Kopieer dit bestand en sla het op als een *configuratie* bestand. 

    > [!IMPORTANT]
    > Sla het configuratie bestand niet op als *. txt* -bestand, sla het bestand zonder bestands extensie op.

1. Het configuratie bestand moet zich in de `.kube` map van uw gebruikers profiel op de lokale computer bevinden. Kopieer het bestand naar de map in uw gebruikers profiel.

    ![Locatie van het configuratie bestand op de client](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. Verleen de gebruiker toegang tot de naam ruimte die u hebt gemaakt. Type: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Hier volgt een voor beeld van de uitvoer van de voor gaande opdrachten. In dit voor beeld maken we een `myadstest` naam ruimte, een `myadsuser` gebruiker en krijgen ze toegang tot de naam ruimte.
    
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

    1. Voer Klad blok als Administrator uit en open het `hosts` bestand dat zich bevindt in `C:\windows\system32\drivers\etc\hosts` . 
    2. Gebruik de informatie die u hebt opgeslagen op de pagina **apparaat** in de lokale gebruikers interface (vereisten) om de vermelding in het hosts-bestand te maken. 

        Kopieer bijvoorbeeld dit eind punt `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` om de volgende vermelding te maken met het IP-adres van het apparaat en het DNS-domein: 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Als u wilt controleren of u verbinding kunt maken met de Kubernetes, start u een cmd-prompt of een Power shell-sessie. Type:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
U kunt nu uw data controller-en Data Services-toepassingen in de naam ruimte implementeren en vervolgens de toepassingen en de bijbehorende logboeken weer geven.

### <a name="create-data-controller"></a>Gegevenscontroller maken

De gegevens controller is een verzameling van de peulen die worden geïmplementeerd in uw Kubernetes-cluster om een API, de controller service, de Boots Trapper en de bewakings databases en-dash boards te bieden. Volg deze stappen voor het maken van een gegevens controller op het Kubernetes-cluster dat zich op uw Azure Stack edge-apparaat bevindt in de naam ruimte die u eerder hebt gemaakt.   

1. Verzamel de volgende informatie die u nodig hebt om een gegevens controller te maken:

    
    |Kolom 1  |Kolom 2  |
    |---------|---------|
    |Naam van gegevens controller     |Een beschrijvende naam voor uw gegevens controller. Bijvoorbeeld `arctestdatacontroller`.         |
    |Gebruikers naam gegevens controller     |Een gebruikers naam voor de gebruiker van de gegevens controller beheerder. De gebruikers naam en het wacht woord van de gegevens controller worden gebruikt voor de verificatie van de data controller-API om beheer functies uit te voeren.          |
    |Wacht woord voor gegevens controller     |Een wacht woord voor de gebruiker van de gegevens controller beheerder. Kies een veilig wacht woord en deel het met alleen de gebruikers die cluster beheerder bevoegdheden nodig hebben.         |
    |Naam van uw Kubernetes-naam ruimte     |De naam van de Kubernetes-naam ruimte waarin u de gegevens controller wilt maken.         |
    |Azure-abonnements-ID     |De GUID van het Azure-abonnement waarvoor u de gegevens controller bron in azure wilt maken.         |
    |Naam van Azure-resourcegroep     |De naam van de resource groep waar u de gegevens controller resource in azure wilt maken.         |
    |Azure-locatie     |De Azure-locatie waar de meta gegevens van de resource controller bron worden opgeslagen in Azure. Zie globale Azure-infra structuur/-producten per regio voor een lijst met beschik bare regio's.|


1. Maak verbinding met de PowerShell-interface. Als u de gegevens controller wilt maken, typt u: 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Hier volgt een voor beeld van de uitvoer van de voor gaande opdrachten.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    Het volt ooien van de implementatie kan ongeveer 5 minuten duren.

    > [!NOTE]
    > De gegevens controller die is gemaakt op het Kubernetes-cluster op uw Azure Stack Edge Pro-apparaat werkt alleen in de modus zonder verbinding in de huidige versie.

### <a name="monitor-data-creation-status"></a>Status van het maken van gegevens bewaken

1. Open een ander Power shell-venster.
1. Gebruik de volgende `kubectl` opdracht om de aanmaak status van de gegevens controller te controleren. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    Wanneer de controller wordt gemaakt, moet de status zijn `Ready` .
    Hier volgt een voor beeld van de uitvoer van de voor gaande opdracht:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. Als u de IP-adressen wilt identificeren die zijn toegewezen aan de externe services die worden uitgevoerd op de gegevens controller, gebruikt u de `kubectl get svc -n <namespace>` opdracht. Hier volgt een voorbeeld van uitvoer:

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

## <a name="deploy-sql-managed-instance"></a>SQL Managed instance implementeren

Nadat u de gegevens controller hebt gemaakt, kunt u een sjabloon gebruiken om een SQL-beheerd exemplaar te implementeren op de gegevens controller.

### <a name="deployment-template"></a>Implementatiesjabloon

Gebruik de volgende implementatie sjabloon om een SQL-beheerd exemplaar te implementeren op de gegevens controller op het apparaat.

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


#### <a name="metadata-name"></a>Naam van meta gegevens
    
De naam van de meta gegevens is de naam van het SQL Managed instance. De gekoppelde pod in de voor gaande `deployment.yaml` zijn de naam zoals `sqlex-n` ( `n` het aantal peulen dat is gekoppeld aan de toepassing). 
    
#### <a name="password-and-username-data"></a>Wacht woord en gebruikers naam gegevens

De gebruikers naam en het wacht woord van de gegevens controller worden gebruikt voor de verificatie van de data controller-API om beheer functies uit te voeren. Het Kubernetes-geheim voor de gebruikers naam en het wacht woord van de gegevens controller in de implementatie sjabloon zijn base64-gecodeerde teken reeksen. 

U kunt een online hulp programma gebruiken om uw gewenste gebruikers naam en wacht woord te coderen of u kunt ingebouwde CLI-hulpprogram ma's gebruiken, afhankelijk van uw platform. Wanneer u een online base64-coderings programma gebruikt, geeft u de gebruikers naam en het wacht woord op (die u hebt ingevoerd tijdens het maken van de gegevens controller) in het hulp programma. het hulp programma genereert de bijbehorende met base64 gecodeerde teken reeksen.
    
#### <a name="service-type"></a>Servicetype

Het Service type moet worden ingesteld op `LoadBalancer` .
    
#### <a name="storage-class-name"></a>Naam van opslag klasse

U kunt de opslag klasse op uw Azure Stack edge-apparaat identificeren dat door de implementatie wordt gebruikt voor gegevens, back-ups, gegevens logboeken en Logboeken. Gebruik de  `kubectl get storageclass` opdracht om de opslag klasse te verkrijgen die op uw apparaat is geïmplementeerd.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
In de voor gaande voorbeeld uitvoer moet de opslag klasse `ase-node-local` op het apparaat worden opgegeven in de sjabloon.
 
#### <a name="spec"></a>Specificatie

Als u een SQL Managed instance wilt maken op uw Azure Stack edge-apparaat, kunt u uw geheugen-en CPU-vereisten opgeven in het gedeelte specificatie van `deployment.yaml` . Elk SQL Managed instance moet mini maal 2 GB geheugen en één CPU-kern aanvragen, zoals wordt weer gegeven in het volgende voor beeld. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

Raadpleeg de details van de [SQL Managed instance](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details)voor meer informatie over het aanpassen van de richt lijnen voor gegevens controllers en 1 SQL Managed instance.

### <a name="run-deployment-template"></a>Implementatie sjabloon uitvoeren

Voer de `deployment.yaml` volgende opdracht uit:

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

Hier volgt een voor beeld van de uitvoer van de volgende opdracht:

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

De `sqlex-0` pod in de voorbeeld uitvoer geeft de status van het beheerde exemplaar van SQL aan.

## <a name="remove-data-controller"></a>Gegevens controller verwijderen

Als u de gegevens controller wilt verwijderen, verwijdert u de eigen naam ruimte waarin u deze hebt geïmplementeerd.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Volgende stappen

- [Implementeer een staatloze toepassing op uw Azure stack Edge Pro](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md).
