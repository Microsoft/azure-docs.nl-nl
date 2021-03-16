---
title: IoT Edge workload implementeren met behulp van GPU delen op Azure Stack Edge Pro GPU-apparaat
description: Hierin wordt beschreven hoe u een gedeelde GPU-werk belasting kunt implementeren via IoT Edge op uw Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: b52d1e834772a2a6e0e000b3df15d8aa0fa866a9
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564956"
---
# <a name="deploy-an-iot-edge-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>Een IoT Edge workload implementeren met behulp van het delen van GPU op uw Azure Stack Edge Pro

In dit artikel wordt beschreven hoe u met containers de Gpu's kunt delen op uw Azure Stack Edge Pro GPU-apparaat. De aanpak houdt in dat u de service voor meerdere processen (MPS) inschakelt en vervolgens de GPU-workloads opgeeft via een IoT Edge-implementatie. 

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt toegang tot een Azure Stack Edge Pro GPU-apparaat dat is [geactiveerd](azure-stack-edge-gpu-deploy-activate.md) en waarop [Compute is geconfigureerd](azure-stack-edge-gpu-deploy-configure-compute.md). U hebt het [Kubernetes-API-eind punt](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) en u hebt dit eind punt toegevoegd aan het `hosts` bestand op uw client dat toegang heeft tot het apparaat.

1. U hebt toegang tot een client systeem met een [ondersteund besturings systeem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). Als u een Windows-client gebruikt, moet het systeem Power shell 5,0 of hoger uitvoeren om toegang te krijgen tot het apparaat.

1. Sla de volgende implementatie `json` op uw lokale systeem op. U gebruikt de informatie uit dit bestand om de IoT Edge-implementatie uit te voeren. Deze implementatie is gebaseerd op [eenvoudige CUDA-containers](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers) die openbaar beschikbaar zijn via NVIDIA. 

    ```json
    {
        "modulesContent": {
            "$edgeAgent": {
                "properties.desired": {
                    "modules": {
                        "cuda-sample1": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        },
                        "cuda-sample2": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        }
                    },
                    "runtime": {
                        "settings": {
                            "minDockerVersion": "v1.25"
                        },
                        "type": "docker"
                    },
                    "schemaVersion": "1.1",
                    "systemModules": {
                        "edgeAgent": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                                "createOptions": ""
                            },
                            "type": "docker"
                        },
                        "edgeHub": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                            },
                            "type": "docker",
                            "status": "running",
                            "restartPolicy": "always"
                        }
                    }
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    "routes": {
                        "route": "FROM /messages/* INTO $upstream"
                    },
                    "schemaVersion": "1.1",
                    "storeAndForwardConfiguration": {
                        "timeToLiveSecs": 7200
                    }
                }
            },
            "cuda-sample1": {
                "properties.desired": {}
            },
            "cuda-sample2": {
                "properties.desired": {}
            }
        }
    }
    ```

## <a name="verify-gpu-driver-cuda-version"></a>Het GPU-stuur programma controleren, CUDA-versie

De eerste stap is om te controleren of op uw apparaat het vereiste GPU-stuur programma en de CUDA-versies worden uitgevoerd.

1. [Verbinding maken met de Power shell-interface van uw apparaat](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Voer de volgende opdracht uit:

    `Get-HcsGpuNvidiaSmi`

1. In de NVIDIA SMI-uitvoer noteert u de GPU-versie en de CUDA-versie op het apparaat. Als u Azure Stack Edge 2102-software uitvoert, komt deze versie overeen met de volgende versies van Stuur Programma's:

    - Versie van GPU-stuur programma: 460.32.03
    - CUDA-versie: 11,2
    
    Hier volgt een voorbeeld van uitvoer:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Tue Feb 23 10:34:01 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000041F:00:00.0 Off |                    0 |
    | N/A   40C    P8    15W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>  
    ```

1. Zorg ervoor dat deze sessie wordt geopend wanneer u deze gebruikt om de NVIDIA SMI-uitvoer in het hele artikel weer te geven.


## <a name="deploy-without-context-sharing"></a>Implementeren zonder context delen

U kunt nu een toepassing implementeren op uw apparaat wanneer de multi-process-service niet wordt uitgevoerd en er geen context is om te delen. De implementatie is via de Azure Portal in de `iotedge` naam ruimte die op uw apparaat bestaat.

### <a name="create-user-in-iot-edge-namespace"></a>Gebruiker maken in IoT Edge naam ruimte

Eerst maakt u een gebruiker die verbinding moet maken met de `iotedge` naam ruimte. De IoT Edge-modules worden geïmplementeerd in de naam ruimte iotedge. Zie [Kubernetes-naam ruimten op het apparaat](azure-stack-edge-gpu-kubernetes-rbac.md#namespaces-types)voor meer informatie.

Voer de volgende stappen uit om een gebruiker te maken en gebruikers toegang te verlenen tot de `iotedge` naam ruimte. 

1. [Verbinding maken met de Power shell-interface van uw apparaat](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Maak een nieuwe gebruiker in de `iotedge` naam ruimte. Voer de volgende opdracht uit:

    `New-HcsKubernetesUser -UserName <user name>`

    Hier volgt een voorbeeld van uitvoer:

    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName iotedgeuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: 
    ===========================//snipped //======================// snipped //=============================
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: iotedgeuser
      name: iotedgeuser@kubernetes
    current-context: iotedgeuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: iotedgeuser
      user:
        client-certificate-data: 
    ===========================//snipped //======================// snipped //=============================
        client-key-data: 
    ===========================//snipped //======================// snipped ============================
    PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
    ```

1. Kopieer de uitvoer die wordt weer gegeven als tekst zonder opmaak. Sla de uitvoer op als een *configuratie* bestand (zonder extensie) in de `.kube` map van uw gebruikers profiel op uw lokale machine, bijvoorbeeld `C:\Users\<username>\.kube` . 

1. Verleen de gebruiker die u hebt gemaakt, toegang tot de `iotedge` naam ruimte. Voer de volgende opdracht uit:

    `Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName <user name>`    

    Hier volgt een voorbeeld van uitvoer:

    ```python
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName iotedgeuser
    [10.100.10.10]: PS>    
    ```
Zie [verbinding maken met en beheren van een Kubernetes-cluster via kubectl op uw Azure stack Edge Pro GPU-apparaat](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac)voor gedetailleerde instructies.

### <a name="deploy-modules-via-portal"></a>Modules implementeren via de portal

Implementeer IoT Edge modules via de Azure Portal. U implementeert de openbaar beschik bare CUDA-voorbeeld modules met de tekst simulatie van n. Zie voor meer informatie [N-hoofdtekst simulatie](https://physics.princeton.edu//~fpretori/Nbody/intro.htm).

1. Zorg ervoor dat de IoT Edge-service op uw apparaat wordt uitgevoerd.

    ![IoT Edge-service wordt uitgevoerd.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-1.png)

1. Selecteer de tegel IoT Edge in het rechterdeel venster. Ga naar **IoT Edge > eigenschappen**. Selecteer in het rechterdeel venster de IoT Hub bron die aan het apparaat is gekoppeld.

    ![Eigenschappen weer geven.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-2.png)

1. Ga in de IoT Hub resource naar **automatische apparaatbeheer > IOT Edge**. Selecteer in het rechterdeel venster het IoT Edge apparaat dat aan het apparaat is gekoppeld.

    ![Ga naar IoT Edge.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-3.png)

1. Selecteer **Modules instellen**.

    ![Ga naar set-modules.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-4.png)

1. Selecteer **+ > + IOT Edge module toevoegen**.

    ![IoT Edge module toevoegen.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-5.png)

1. Geef op het tabblad **module-instellingen** de naam van de **IOT Edge module** en de URI van de **installatie kopie** op. Stel een **installatie kopie-pull-beleid** in **op maken**.

    ![Module-instellingen.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-6.png)
1. Geef op het tabblad **omgevings variabelen** **NVIDIA_VISIBLE_DEVICES** op als **0**.

    ![Omgevings variabelen.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-7.png)

1. Geef op het tabblad Opties voor het maken van de **container** de volgende opties op:

    ```json
    {
        "Entrypoint": [
            "/bin/sh"
        ],
        "Cmd": [
            "-c",
            "/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done"
        ],
        "HostConfig": {
            "IpcMode": "host",
            "PidMode": "host"
        }
    }    
    ```
    De opties worden als volgt weer gegeven:

    ![Opties voor het maken van containers.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-8.png)

    Selecteer **Toevoegen**.

1. De module die u hebt toegevoegd, moet worden weer gegeven als **actief**. 

    ![Een implementatie bekijken en maken.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-9.png)


1. Herhaal alle stappen om een module toe te voegen die u hebt gevolgd bij het toevoegen van de eerste module. In dit voor beeld geeft u de naam van de module op als `cuda-sample2` . 

    ![Module-instellingen voor 2e module.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-12.png)

    Gebruik dezelfde omgevings variabele als beide modules dezelfde GPU delen.

    ![Omgevings variabele voor 2e module.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-13.png)

    Gebruik dezelfde container Create-opties die u voor de eerste module hebt ingesteld en selecteer **toevoegen**.

    ![Opties voor het maken van een container voor 2e modules.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-14.png)

1. Selecteer op de pagina **modules instellen** de optie **controleren + maken** en selecteer vervolgens **maken**. 

    ![Bekijk en maak een tweede implementatie.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-15.png)

1. De **runtime status** van beide modules moet nu worden weer gegeven als **actief**.  

    ![2e implementatie status.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-16.png)


### <a name="monitor-workload-deployment"></a>Implementatie van werk belasting bewaken

1. Open een nieuwe Power shell-sessie.

1. Geef een lijst weer van de peulen die in de `iotedge` naam ruimte worden uitgevoerd. Voer de volgende opdracht uit:

    `kubectl get pods -n iotedge`   

    Hier volgt een voorbeeld van uitvoer:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-ssng8   2/2     Running   0          5s
    cuda-sample2-6db6d98689-d74kb   2/2     Running   0          4s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          6d21h
    edgehub-d6c764847-l8v4m         2/2     Running   0          24h
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          6d21h
    PS C:\WINDOWS\system32>   
    ```
    Er zijn twee peulen `cuda-sample1-97c494d7f-lnmns` en worden `cuda-sample2-d9f6c4688-2rld9` uitgevoerd op het apparaat.

1. Terwijl beide containers de simulatie van de n-hoofd tekst uitvoeren, Bekijk dan het GPU-gebruik van de NVIDIA SMI-uitvoer. Ga naar de Power shell-interface van het apparaat en voer uit `Get-HcsGpuNvidiaSmi` .

    Hier volgt een voor beeld van een uitvoer, wanneer beide containers de werk simulatie n-Body uitvoeren:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:31:16 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   52C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    188342      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    188413      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
    Zoals u ziet, worden er twee containers uitgevoerd met simulatie van de n-lichaams op GPU 0. U kunt ook het bijbehorende geheugen gebruik weer geven.

1. Zodra de simulatie is voltooid, wordt in de NVIDIA SMI-uitvoer aangegeven dat er geen processen op het apparaat worden uitgevoerd.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:54:48 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
 
1. Nadat de proef van de hoofd tekst is voltooid, bekijkt u de logboeken om inzicht te krijgen in de details van de implementatie en de tijd die nodig is om de simulatie uit te voeren. 

    Hier volgt een voor beeld van de uitvoer van de eerste container:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample1-869989578c-ssng8 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170171.531 ms
    = 98.590 billion interactions per second
    = 1971.801 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```
    Hier volgt een voor beeld van de uitvoer van de tweede container:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-d74kb cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170054.969 ms
    = 98.658 billion interactions per second
    = 1973.152 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```

1. Stop de implementatie van de module. In de IoT Hub resource voor uw apparaat:
    1. Ga naar **automatische implementatie van apparaten > IOT Edge**. Selecteer het IoT Edge apparaat dat overeenkomt met uw apparaat.

    1. Ga naar **modules instellen** en selecteer een module. 

        ![Selecteer module instellen.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-1.png)

    1. Selecteer een module op het tabblad **modules** .
    
        ![Selecteer een module.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-2.png)

    1.  Stel op het tabblad **module-instellingen** de **gewenste status** in op gestopt. Selecteer **Update**.

        ![Wijzig de instellingen van de module.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-3.png)

    1. Herhaal de stappen om de tweede module te stoppen die op het apparaat is geïmplementeerd. Selecteer **Controleren en maken** en selecteer vervolgens **Maken**. Hiermee wordt de implementatie bijgewerkt.

        ![Een bijgewerkte implementatie bekijken en maken.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-6.png)
 
    1. Pagina met **set-modules** meerdere keren vernieuwen. totdat de status van de module **runtime** wordt weer gegeven als **gestopt**.

        ![De implementatie status controleren.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-8.png) 
    

## <a name="deploy-with-context-sharing"></a>Implementeren met context deling

U kunt nu de n-hoofdtekst simulatie implementeren op twee CUDA-containers wanneer MPS op uw apparaat wordt uitgevoerd. Eerst schakelt u MPS in op het apparaat.


1. [Verbinding maken met de Power shell-interface van uw apparaat](azure-stack-edge-gpu-connect-powershell-interface.md).

1. Voer de opdracht uit om MPS op uw apparaat in te scha kelen `Start-HcsGpuMPS` .

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    Set compute mode to EXCLUSIVE_PROCESS for GPU 0000191E:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. Down load de NVIDIA SMI-uitvoer van de Power shell-interface van het apparaat. U kunt zien dat het `nvidia-cuda-mps-server` proces of de MPS-service wordt uitgevoerd op het apparaat.

    Hier volgt een voorbeeld van uitvoer:

    ```yml
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:37:39 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   36C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```

1. Implementeer de modules die u eerder hebt gestopt. Stel de **gewenste status** in op uitvoeren via **set-modules**.

    Dit is de voorbeeld uitvoer:

    ```yml
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-2zxh6   2/2     Running   0          44s
    cuda-sample2-6db6d98689-fn7mx   2/2     Running   0          44s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          5d20h
    edgehub-d6c764847-l8v4m         2/2     Running   0          27m
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          5d20h
    PS C:\WINDOWS\system32>
    ```
    U kunt zien dat de modules op uw apparaat zijn geïmplementeerd en worden uitgevoerd.

1. Wanneer de modules zijn geïmplementeerd, wordt de gesimuleerde n-lichaams simulatie ook uitgevoerd op beide containers. Hier volgt een voor beeld van de uitvoer wanneer de simulatie is voltooid voor de eerste container:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge logs cuda-sample1-869989578c-2zxh6 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155256.062 ms
    = 108.062 billion interactions per second
    = 2161.232 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32> 
    ```
    Hier volgt een voor beeld van de uitvoer wanneer de simulatie is voltooid voor de tweede container:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-fn7mx cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155366.359 ms
    = 107.985 billion interactions per second
    = 2159.697 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>    
    ```      

1. Down load de NVIDIA SMI-uitvoer van de Power shell-interface van het apparaat wanneer beide containers de hoofd simulatie van de n-tekst uitvoeren. Hier volgt een voor beeld van een uitvoer. Er zijn drie processen, het `nvidia-cuda-mps-server` proces (type C) komt overeen met de MPS-service en de `/tmp/nbody` processen (type M + C) komen overeen met de n-lichaams werk belastingen die door de modules worden geïmplementeerd. 

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:59:44 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   54C    P0    69W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A     56832    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A     56900    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```
    
## <a name="next-steps"></a>Volgende stappen

- [Implementeer een gedeelde GPU Kubernetes-werk belasting op uw Azure stack Edge Pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
