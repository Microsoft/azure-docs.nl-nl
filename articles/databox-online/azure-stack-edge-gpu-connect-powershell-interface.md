---
title: Verbinding maken met en beheren van Microsoft Azure Stack Edge Pro GPU-apparaat via de Windows Power shell-interface | Microsoft Docs
description: Hierin wordt beschreven hoe u verbinding maakt met en vervolgens Azure Stack Edge Pro GPU beheert via de Windows Power shell-interface.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 580e5aab7b7ac1edcfee58345291afcb9eb0e977
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562158"
---
# <a name="manage-an-azure-stack-edge-pro-gpu-device-via-windows-powershell"></a>Een Azure Stack Edge Pro GPU-apparaat beheren via Windows Power shell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Met Azure Stack Edge Pro-oplossing kunt u gegevens verwerken en via het netwerk verzenden naar Azure. In dit artikel worden enkele van de configuratie-en beheer taken voor uw Azure Stack Edge Pro-apparaat beschreven. U kunt de Azure Portal, de lokale webgebruikersinterface of de Windows Power shell-interface gebruiken om uw apparaat te beheren.

In dit artikel wordt uitgelegd hoe u verbinding kunt maken met de Power shell-interface van het apparaat en de taken die u met deze interface kunt uitvoeren. 


## <a name="connect-to-the-powershell-interface"></a>Verbinding maken met de PowerShell-interface

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Een ondersteunings pakket maken

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]


## <a name="view-device-information"></a>Apparaatgegevens weer geven
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>Informatie over GPU-stuur programma weer geven

Als de compute-functie op uw apparaat is geconfigureerd, kunt u ook de informatie over het GPU-stuur programma ophalen via de Power shell-interface. 

1. [Verbinding maken met de Power shell-interface](#connect-to-the-powershell-interface).
2. Gebruik de `Get-HcsGpuNvidiaSmi` om de informatie over het GPU-stuur programma op te halen voor uw apparaat.

    In het volgende voor beeld ziet u het gebruik van deze cmdlet:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Noteer de stuur programma-informatie van de voorbeeld uitvoer van deze cmdlet.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Service voor meerdere processen inschakelen (MPS)

Een MPS (multi-process service) op NVIDIA-Gpu's biedt een mechanisme waarbij Gpu's kunnen worden gedeeld door meerdere taken, waarbij elke taak een percentage van de resources van de GPU toegewezen heeft. MPS is een preview-functie op uw Azure Stack Edge Pro GPU-apparaat. Voer de volgende stappen uit om MPS in te scha kelen op uw apparaat:

[!INCLUDE [Enable MPS](../../includes/azure-stack-edge-gateway-enable-mps.md)]


## <a name="reset-your-device"></a>Het apparaat opnieuw instellen

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Reken logboeken ophalen

Als de compute-functie op uw apparaat is geconfigureerd, kunt u de reken logboeken ook ophalen via de Power shell-interface.

1. [Verbinding maken met de Power shell-interface](#connect-to-the-powershell-interface).
2. Gebruik de `Get-AzureDataBoxEdgeComputeRoleLogs` om de reken logboeken voor uw apparaat op te halen.

    In het volgende voor beeld ziet u het gebruik van deze cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Hier volgt een beschrijving van de para meters die worden gebruikt voor de cmdlet:
    - `Path`: Geef een netwerkpad op naar de share waar u het Compute-pakket wilt maken.
    - `Credential`: Geef de gebruikers naam op voor de netwerk share. Wanneer u deze cmdlet uitvoert, moet u het wacht woord voor delen opgeven.
    - `FullLogCollection`: Met deze para meter zorgt u ervoor dat in het logboek pakket alle reken logboeken worden opgenomen. Het logboek pakket bevat standaard slechts een subset van de logboeken.


## <a name="change-kubernetes-pod-and-service-subnets"></a>Subnetten voor Kubernetes-pods en -services wijzigen

Kubernetes op uw Azure Stack edge-apparaat gebruikt standaard subnetten 172.27.0.0/16 en 172.28.0.0/16 voor respectievelijk pod en service. Als deze subnetten al worden gebruikt in uw netwerk, kunt u de `Set-HcsKubeClusterNetworkInfo` cmdlet uitvoeren om deze subnetten te wijzigen.

U wilt deze configuratie uitvoeren voordat u Compute configureert vanaf de Azure Portal, terwijl het Kubernetes-cluster in deze stap wordt gemaakt.

1. [Maak verbinding met de Power shell-interface van het apparaat](#connect-to-the-powershell-interface).
1. Voer de volgende handelingen uit vanuit de Power shell-interface van het apparaat:

    `Set-HcsKubeClusterNetworkInfo -PodSubnet <subnet details> -ServiceSubnet <subnet details>`

    Vervang de <subnet details> door het subnet-bereik dat u wilt gebruiken. 

1. Wanneer u deze opdracht hebt uitgevoerd, kunt u de `Get-HcsKubeClusterNetworkInfo` opdracht gebruiken om te controleren of de Pod en de service-subnetten zijn gewijzigd.

Hier volgt een voor beeld van de uitvoer van deze opdracht.

```powershell
[10.100.10.10]: PS>Set-HcsKubeClusterNetworkInfo -PodSubnet 10.96.0.1/16 -ServiceSubnet 10.97.0.1/16
[10.100.10.10]: PS>Get-HcsKubeClusterNetworkInfo

Id                                   PodSubnet    ServiceSubnet
--                                   ---------    -------------
6dbf23c3-f146-4d57-bdfc-76cad714cfd1 10.96.0.1/16 10.97.0.1/16
[10.100.10.10]: PS>
```

## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Fout opsporing voor Kubernetes-problemen met betrekking tot IoT Edge

Voordat u begint, hebt u het volgende nodig:

- Er is een compute-netwerk geconfigureerd. Zie [zelf studie: netwerk configureren voor Azure stack Edge Pro met GPU](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
- Reken functie die op uw apparaat is geconfigureerd.
    
Op een Azure Stack Edge Pro-apparaat waarop de compute-rol is geconfigureerd, kunt u problemen oplossen of het apparaat bewaken met twee verschillende sets opdrachten.

- `iotedge`Opdrachten gebruiken. Deze opdrachten zijn beschikbaar voor basis bewerkingen voor uw apparaat.
- `kubectl`Opdrachten gebruiken. Deze opdrachten zijn beschikbaar voor een uitgebreide set bewerkingen voor uw apparaat.

Als u een van de bovenstaande opdrachten wilt uitvoeren, moet u [verbinding maken met de Power shell-interface](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>`iotedge`Opdrachten gebruiken

[Maak verbinding met de Power shell-interface](#connect-to-the-powershell-interface) en gebruik de functie om een lijst met beschik bare opdrachten weer te geven `iotedge` .

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                           
Usage: iotedge COMMAND

Commands:
   list
   logs
   restart

[10.100.10.10]: PS>
```

De volgende tabel bevat een korte beschrijving van de opdrachten die beschikbaar zijn voor `iotedge` :

|command  |Beschrijving |
|---------|---------|
|`list`     | Modules in lijst weergeven         |
|`logs`     | De logboeken van een module ophalen        |
|`restart`     | Een module stoppen en opnieuw starten         |

#### <a name="list-all-iot-edge-modules"></a>Alle IoT Edge modules weer geven

Gebruik de opdracht om een lijst weer te geven met alle modules die op het apparaat worden uitgevoerd `iotedge list` .

Hier volgt een voor beeld van de uitvoer van deze opdracht. Met deze opdracht worden alle modules, de bijbehorende configuratie en de externe IP-adressen weer gegeven die aan de modules zijn gekoppeld. U kunt bijvoorbeeld toegang krijgen tot de **webserver** -app op `https://10.128.44.244` . 


```powershell
[10.100.10.10]: PS>iotedge list

NAME                   STATUS  DESCRIPTION CONFIG                                             EXTERNAL-IP
----                   ------  ----------- ------                                             -----
gettingstartedwithgpus Running Up 10 days  mcr.microsoft.com/intelligentedge/solutions:latest
iotedged               Running Up 10 days  azureiotedge/azureiotedge-iotedged:0.1.0-beta10    <none>
edgehub                Running Up 10 days  mcr.microsoft.com/azureiotedge-hub:1.0             10.128.44.243
edgeagent              Running Up 10 days  azureiotedge/azureiotedge-agent:0.1.0-beta10
webserverapp           Running Up 10 days  nginx:stable                                       10.128.44.244

[10.100.10.10]: PS>
```
#### <a name="restart-modules"></a>Modules opnieuw opstarten

U kunt de `list` opdracht gebruiken om een lijst weer te geven met alle modules die op het apparaat worden uitgevoerd. Bepaal vervolgens de naam van de module die u opnieuw wilt opstarten en gebruik deze met de `restart` opdracht.

Hier volgt een voor beeld van de uitvoer van een module. Op basis van de beschrijving van hoe lang de module wordt uitgevoerd, kunt u zien dat deze `cuda-sample1` opnieuw is opgestart.

```powershell
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  ----------- ------                                          ----------- -------
edgehub      Running Up 5 days   mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:308
                                                                                             81/TCP,8883:31753/TCP
iotedged     Running Up 7 days   azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days   nvidia/samples:nbody
edgeagent    Running Up 7 days   azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 1 days   nvidia/samples:nbody

[10.100.10.10]: PS>iotedge restart cuda-sample1
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION  CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  -----------  ------                                          ----------- -------
edgehub      Running Up 5 days    mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:30
                                                                                              881/TCP,8883:31753/TC
                                                                                              P
iotedged     Running Up 7 days    azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days    nvidia/samples:nbody
edgeagent    Running Up 7 days    azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 4 minutes nvidia/samples:nbody

[10.100.10.10]: PS>

```

#### <a name="get-module-logs"></a>Module Logboeken ophalen

Gebruik de `logs` opdracht om logboeken op te halen voor elke IOT Edge module die op uw apparaat wordt uitgevoerd. 

Als er een fout is opgetreden bij het maken van de container installatie kopie of tijdens het ophalen van de installatie kopie, voert u uit `logs edgeagent` . `edgeagent` is de IoT Edge runtime-container die verantwoordelijk is voor het inrichten van andere containers. Omdat `logs edgeagent` alle logboeken worden gedumpt, is het gebruik van de optie 0 een goede manier om de recente fouten te bekijken `--tail ` . 

Hier volgt een voorbeeld uitvoer.

```powershell
[10.100.10.10]: PS>iotedge logs cuda-sample2 --tail 10
[10.100.10.10]: PS>iotedge logs edgeagent --tail 10
<6> 2021-02-25 00:52:54.828 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:52:54.829 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Plan execution ended for deployment 11
[10.100.10.10]: PS>
```

### <a name="use-kubectl-commands"></a>Kubectl-opdrachten gebruiken

Op een Azure Stack Edge Pro-apparaat waarop de compute-rol is geconfigureerd, `kubectl` zijn alle opdrachten beschikbaar voor het controleren of oplossen van problemen met modules. Voer `kubectl --help` uit vanuit het opdracht venster om een lijst met beschik bare opdrachten weer te geven.

```PowerShell
C:\Users\myuser>kubectl --help

kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

Voor een uitgebreide lijst met `kubectl` opdrachten gaat u naar [ `kubectl` materiaal](https://kubernetes.io/docs/reference/kubectl/cheatsheet/).


#### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Het IP-adres van de service of module die buiten het Kubernetes-cluster wordt weer gegeven, ophalen

Voer de volgende opdracht uit om het IP-adres te verkrijgen van een taakverdelings service of modules die buiten het Kubernetes worden weer gegeven:

`kubectl get svc -n iotedge`

Hieronder ziet u een voor beeld van de uitvoer van alle services of modules die buiten het Kubernetes-cluster worden weer gegeven. 


```powershell
[10.100.10.10]: PS>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

[10.100.10.10]: PS>
```
Het IP-adres in de externe IP-kolom komt overeen met het externe eind punt voor de service of de module. U kunt ook [het externe IP-adres in het Kubernetes-dash board ophalen](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules).


#### <a name="to-check-if-module-deployed-successfully"></a>Controleren of de module is geïmplementeerd

Compute-modules zijn containers waarvoor een bedrijfs logica is geïmplementeerd. Er kunnen meerdere containers worden uitgevoerd op een Kubernetes-pod. 

Als u wilt controleren of een compute-module is geïmplementeerd, maakt u verbinding met de Power shell-interface van het apparaat.
Voer de `get pods` opdracht uit en controleer of de container (die overeenkomt met de module Compute) wordt uitgevoerd.

Voer de volgende opdracht uit om de lijst met alle bereiken in een specifieke naam ruimte weer te geven:

`get pods -n <namespace>`

Voer de volgende opdracht uit om de modules te controleren die via IoT Edge zijn geïmplementeerd:

`get pods -n iotedge`

Hieronder ziet u een voor beeld van een uitvoer van alle peulen die in de `iotedge` naam ruimte worden uitgevoerd.

```
[10.100.10.10]: PS>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

[10.100.10.10]: PS>
```

De status **status geeft aan dat** alle peulen in de naam ruimte worden uitgevoerd en de kant-en- **klare** is het aantal containers dat is geïmplementeerd in een pod. In het voor gaande voor beeld wordt alle peulen uitgevoerd en zijn alle modules die in elk van de peulen zijn geïmplementeerd, actief. 

Als u de modules wilt controleren die zijn geïmplementeerd via Azure Arc, voert u de volgende opdracht uit:

`get pods -n azure-arc`

U kunt ook [verbinding maken met het Kubernetes-dash board om IOT Edge of Azure Arc-implementaties te bekijken](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#view-module-status).


Voor een uitgebreidere uitvoer van een specifieke pod voor een bepaalde naam ruimte kunt u de volgende opdracht uitvoeren:

`kubectl describe pod <pod name> -n <namespace>` 

De voorbeeld uitvoer wordt hier weer gegeven.

```
[10.100.10.10]: PS>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


[10.100.10.10]: PS>
```

#### <a name="to-get-container-logs"></a>Container logboeken ophalen

Als u de logboeken voor een module wilt ophalen, voert u de volgende opdracht uit vanuit de Power shell-interface van het apparaat:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Omdat met de `all-containers` vlag alle logboeken voor alle containers worden gedumpt, is het gebruik van de optie een goede manier om de recente fouten te bekijken `--tail 10` .

Hier volgt een voor beeld van een uitvoer. 

```
[10.100.10.10]: PS>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

[10.100.10.10]: PS>
```

### <a name="change-memory-processor-limits-for-kubernetes-worker-node"></a>Geheugen wijzigen, processor limieten voor Kubernetes worker-knoop punt

Voer de volgende stappen uit om de geheugen-of processor limieten voor het worker-knoop punt Kubernetes te wijzigen:

1. [Maak verbinding met de Power shell-interface van het apparaat](#connect-to-the-powershell-interface).
1. Als u de huidige resources voor het worker-knoop punt en de functie opties wilt ophalen, voert u de volgende opdracht uit:

    `Get-AzureDataBoxEdgeRole`

    Hier volgt een voorbeeld uitvoer. Noteer de waarden voor `Name` en `Compute` onder `Resources` sectie. `MemoryInBytes` en `ProcessorCount` duiden de momenteel toegewezen waarden geheugen en processor aantal aan voor het Kubernetes worker-knoop punt.  

    ```powershell
    [10.100.10.10]: PS>Get-AzureDataBoxEdgeRole
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:12
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True    
    [10.100.10.10]: PS>
    ```
    
1. Als u de waarden van geheugen en processors voor het worker-knoop punt wilt wijzigen, voert u de volgende opdracht uit:

    Set-AzureDataBoxEdgeRoleCompute naam <Name value from the output of Get-AzureDataBoxEdgeRole> /geheugen <Value in Bytes> -ProcessorCount <Nee. > van kern geheugens

    Hier volgt een voorbeeld uitvoer. 
    
    ```powershell
    [10.100.10.10]: PS>Set-AzureDataBoxEdgeRoleCompute -Name IotRole -MemoryInBytes 32GB -ProcessorCount 16
    
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:16
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
    
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True
    
    [10.100.10.10]: PS>    
    ```

Volg de volgende richt lijnen bij het wijzigen van het geheugen-en processor gebruik.

- Het standaard geheugen is 25% van de apparaats specificatie.
- Het standaard aantal processors is 30% van de apparaats specificatie.
- Wanneer u de waarden voor geheugen en processor aantallen wijzigt, raden we u aan om de waarden te variëren tussen 15% en 60% van het geheugen van het apparaat en het aantal processors. 
- We raden een bovengrens van 60% aan, zodat er voldoende bronnen zijn voor systeem onderdelen. 

## <a name="connect-to-bmc"></a>Verbinding maken met BMC

Base Board management controller (BMC) wordt gebruikt om uw apparaat op afstand te controleren en te beheren. In deze sectie worden de cmdlets beschreven die kunnen worden gebruikt voor het beheren van BMC-configuratie. Voordat u een van deze cmdlets uitvoert, [maakt u verbinding met de Power shell-interface van het apparaat](#connect-to-the-powershell-interface).

- `Get-HcsNetBmcInterface`: Gebruik deze cmdlet om de eigenschappen van de netwerk configuratie van de BMC op te halen, bijvoorbeeld,,, `IPv4Address` `IPv4Gateway` `IPv4SubnetMask` , `DhcpEnabled` . 
    
    Hier volgt een voorbeeld van uitvoer:
    
    ```powershell
    [10.100.10.10]: PS>Get-HcsNetBmcInterface
    IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
    -----------   ----------- -------------- -----------
    10.128.53.186 10.128.52.1 255.255.252.0        False
    [10.100.10.10]: PS>
    ```
- `Set-HcsNetBmcInterface`: U kunt deze cmdlet op de volgende twee manieren gebruiken.

    - Gebruik de cmdlet om de DHCP-configuratie voor BMC in of uit te scha kelen met behulp van de juiste waarde voor de `UseDhcp` para meter. 

        ```powershell
        Set-HcsNetBmcInterface -UseDhcp $true
        ```

        Hier volgt een voorbeeld van uitvoer: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -UseDhcp $true
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address IPv4Gateway IPv4SubnetMask DhcpEnabled
        ----------- ----------- -------------- -----------
        10.128.54.8 10.128.52.1 255.255.252.0         True
        [10.100.10.10]: PS>
        ```

    - Gebruik deze cmdlet om de statische configuratie voor de BMC te configureren. U kunt de waarden opgeven voor `IPv4Address` , `IPv4Gateway` , en `IPv4SubnetMask` . 
    
        ```powershell
        Set-HcsNetBmcInterface -IPv4Address "<IPv4 address of the device>" -IPv4Gateway "<IPv4 address of the gateway>" -IPv4SubnetMask "<IPv4 address for the subnet mask>"
        ```        
        
        Hier volgt een voorbeeld van uitvoer: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -IPv4Address 10.128.53.186 -IPv4Gateway 10.128.52.1 -IPv4SubnetMask 255.255.252.0
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
        -----------   ----------- -------------- -----------
        10.128.53.186 10.128.52.1 255.255.252.0        False
        [10.100.10.10]: PS>
        ```    

- `Set-HcsBmcPassword`: Gebruik deze cmdlet om het BMC-wacht woord voor te wijzigen `EdgeUser` . De gebruikers naam- `EdgeUser` -is hoofdletter gevoelig.

    Hier volgt een voorbeeld van uitvoer: 

    ```powershell
    [10.100.10.10]: PS> Set-HcsBmcPassword -NewPassword "Password1"
    [10.100.10.10]: PS>
    ```

## <a name="exit-the-remote-session"></a>De externe sessie afsluiten

Als u de externe Power shell-sessie wilt afsluiten, sluit u het Power shell-venster.

## <a name="next-steps"></a>Volgende stappen

- [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) in de Azure-portal implementeren.