---
title: IoT Edge modules op FPGA-apparaat wijzigen om uit te voeren op Azure Stack Edge Pro GPU-apparaat
description: Hierin wordt beschreven welke wijzigingen nodig zijn voor bestaande IoT Edge-modules op bestaande FPGA-apparaten om te worden uitgevoerd op uw Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 660fbf7cc4dd28c800d8f49fd5d990c99f97c4c8
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442992"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>Bestaande IoT Edge-modules uitvoeren vanaf Azure Stack Edge Pro FPGA-apparaten op Azure Stack Edge Pro GPU-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

In dit artikel worden de wijzigingen beschreven die nodig zijn voor een IoT Edge module op basis van docker die wordt uitgevoerd op Azure Stack Edge Pro FPGA, zodat deze kan worden uitgevoerd op een IoT Edge platform op basis van Azure Stack Edge Pro GPU-apparaat. 

## <a name="about-iot-edge-implementation"></a>Over IoT Edge-implementatie 

De IoT Edge-implementatie verschilt op Azure Stack Edge Pro FPGA-apparaten versus op apparaten met Azure Stack Edge Pro GPU. Voor de GPU-apparaten wordt Kubernetes gebruikt als hosting platform voor IoT Edge. De IoT Edge op FPGA-apparaten maakt gebruik van een docker-platform. Het IoT Edge op docker gebaseerd toepassings model wordt automatisch vertaald naar het systeem eigen Kubernetes-toepassings model. Sommige wijzigingen zijn echter mogelijk nog steeds nodig omdat slechts een kleine subset van het Kubernetes-toepassings model wordt ondersteund.

Als u uw workloads migreert van een FPGA-apparaat naar een GPU-apparaat, moet u wijzigingen aanbrengen in de bestaande IoT Edge modules, zodat deze kunnen worden uitgevoerd op het Kubernetes-platform. Mogelijk moet u uw vereisten voor opslag, netwerken, resource gebruik en webproxys verschillend opgeven. 

## <a name="storage"></a>Storage

Houd rekening met de volgende informatie wanneer u opslag opgeeft voor de IoT Edge modules.

- Opslag voor containers op Kubernetes wordt opgegeven met behulp van volume koppelt.
- De implementatie op Kubernetes kan geen bindingen hebben voor het koppelen van permanente opslag of Stationspaden.
    - Gebruik bij type voor permanente opslag `Mounts` `volume` .
    - Voor Stationspaden gebruikt u `Mounts` with type `bind` .
- Voor IoT Edge op Kubernetes, bind ik `Mounts` alleen voor Directory en niet voor bestand.

#### <a name="example---storage-via-volume-mounts"></a>Voor beeld: opslag via volume koppels 

Voor IoT Edge op docker worden hostroutes-bindingen gebruikt om de shares op het apparaat toe te wijzen aan paden in de container. Dit zijn de opties voor het maken van een container die worden gebruikt op FPGA-apparaten:

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
Voor Stationspaden voor IoT Edge op Kubernetes wordt een voor beeld van het gebruik van `Mounts` met-type `bind` weer gegeven:
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

Voor de GPU-apparaten met IoT Edge op Kubernetes, worden volume koppelingen gebruikt om opslag ruimte op te geven. Als u opslag wilt inrichten met shares, is de waarde van de `Mounts.Source` naam van de SMB-of NFS-share die op uw GPU-apparaat is ingericht. Het `/home/input` is het pad waar het volume toegankelijk is in de container. Dit zijn de opties voor het maken van een container op de GPU-apparaten:

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>Netwerk

Houd rekening met de volgende informatie bij het opgeven van netwerken voor de IoT Edge modules. 

- `HostPort` Er is een specificatie vereist om een service zowel binnen als buiten het cluster beschikbaar te maken.
    - Opties voor K8sExperimental om de bloot stelling van service alleen aan het cluster te beperken.
- Voor de communicatie tussen modules is `HostPort` specificatie en verbinding met toegewezen poort vereist (en niet de beschik bare poort van de container).
- Host netwerken werken met `dnsPolicy = ClusterFirstWithHostNet` , waarbij alle containers (met name `edgeHub` ) ook niet in het doelnet werk moeten zijn. <!--Need further clarifications on this one-->
- Het toevoegen van poort toewijzingen voor TCP, UDP in dezelfde aanvraag werkt niet.

#### <a name="example---external-access-to-modules"></a>Voor beeld: externe toegang tot modules 

Voor alle IoT Edge-modules die poort bindingen opgeven, wordt een IP-adres toegewezen met het Kubernetes External service-IP-bereik dat is opgegeven in de lokale gebruikers interface van het apparaat. Er zijn geen wijzigingen in de container voor het maken van opties tussen IoT Edge op docker versus IoT Edge op Kubernetes, zoals in het volgende voor beeld wordt weer gegeven.  

```json 
{
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
        }
    }
}
```

Als u echter een query wilt uitvoeren op het IP-adres dat is toegewezen aan uw module, kunt u het Kubernetes-dash board gebruiken zoals beschreven in [IP-adres voor services of modules ophalen](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules). 

U kunt ook [verbinding maken met de Power shell-interface van het apparaat](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) en de `iotedge` lijst opdracht gebruiken om alle modules weer te geven die op het apparaat worden uitgevoerd. De [uitvoer](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge) van de opdracht geeft ook de externe IP-adressen aan die zijn gekoppeld aan de module.


## <a name="resource-usage"></a>Resourcegebruik 

Met de op Kubernetes gebaseerde IoT Edge-instellingen op GPU-apparaten, worden de resources, zoals hardwareversnelling, geheugen en CPU-vereisten, anders opgegeven dan op de FPGA-apparaten. 

#### <a name="compute-acceleration-usage"></a>Gebruik van Compute Acceleration

Als u modules op FPGA wilt implementeren, gebruikt u de opties voor het maken van een container <!--with Device Bindings--> zoals weer gegeven in de volgende configuratie: <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
Gebruik voor GPU de resource-aanvraag specificaties in plaats van de bindingen van het apparaat, zoals wordt weer gegeven in de volgende minimale configuratie. U vraagt NVIDIA-resources in plaats van Catapult en u niet te opgeven `wireserver` . 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>Geheugen-en CPU-gebruik
 
Als u geheugen-en CPU-gebruik wilt instellen, gebruikt u processor limieten voor modules in de `k8s-experimental` sectie. <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
De specificatie van geheugen en CPU is niet nodig, maar is in het algemeen goed te gebruiken. Als `requests` niet wordt opgegeven, worden de waarden die zijn ingesteld in limieten gebruikt als mini maal vereist. 

Het gebruik van gedeeld geheugen voor modules vereist ook een andere manier. U kunt bijvoorbeeld de host IPC-modus gebruiken voor gedeeld geheugen gebruik tussen live video analyses en oplossingen voor het afnemen van problemen zoals beschreven in [Live video Analytics implementeren op Azure stack Edge](../media-services/live-video-analytics-edge/deploy-azure-stack-edge-how-to.md#deploy-live-video-analytics-edge-module-using-azure-portal).


## <a name="web-proxy"></a>Webproxy 

Houd rekening met de volgende informatie bij het configureren van de webproxy:

Als u een webproxy in uw netwerk hebt geconfigureerd, configureert u de volgende omgevings variabelen voor de `edgeHub` implementatie op uw op docker gebaseerde IOT Edge-installatie op FPGA-apparaten:

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs` (tenzij de webproxy `Amqp` verkeer toestaat)

Voor de op Kubernetes gebaseerde IoT Edge-instellingen op GPU-apparaten moet u deze extra variabele tijdens de implementatie configureren:

- `no_proxy`: localhost

- IoT Edge proxy op het Kubernetes-platform gebruikt poort 35000 en 35001. Zorg ervoor dat de module niet wordt uitgevoerd op deze poorten of dat er poort conflicten optreden. 

## <a name="other-differences"></a>Andere verschillen

- **Implementatie strategie**: u moet mogelijk het implementatie gedrag wijzigen voor alle updates in de module. Het standaard gedrag voor IoT Edge modules is Rolling update. Dit gedrag zorgt ervoor dat de bijgewerkte module niet opnieuw kan worden opgestart als de module resources gebruikt, zoals hardwareversnelling of netwerk poorten. Dit gedrag kan onverwachte effecten hebben, speciaal bij het omgaan met permanente volumes op het Kubernetes-platform voor de GPU-apparaten. Als u dit standaard gedrag wilt overschrijven, kunt u een `Recreate` in de `k8s-experimental` sectie in uw module opgeven.

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- **Modules namen**: module namen moeten voldoen aan de Kubernetes-naamgevings conventies. Mogelijk moet u de naam van de modules die worden uitgevoerd op IoT Edge met docker wijzigen wanneer u deze modules verplaatst naar IoT Edge met Kubernetes. Zie [Kubernetes name Conventions (naamgevings conventies](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/)) voor meer informatie over naamgeving.
- **Andere opties**: 
    - Bepaalde docker-opties voor het maken van FPGA-apparaten werken niet in de Kubernetes-omgeving op uw GPU-apparaten. Bijvoorbeeld:, like – entry point.<!--can we confirm what exactly is required here-->
    - Omgevings variabelen, zoals `:` moeten worden vervangen door `__` .
    - De container voor het **maken** van de status voor een Kubernetes pod leidt naar de **uitstel** -status voor een module op de IOT hub resource. Hoewel er een aantal redenen zijn om de pod in deze status te hebben, is een gemeen schappelijke reden dat een grote container installatie kopie wordt opgehaald via een lage netwerk bandbreedte verbinding. Wanneer de pod zich in deze staat bevindt, wordt de status van de module weer gegeven als **uitstel** in IOT hub, hoewel de module net wordt gestart.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe u [GPU kunt configureren voor het gebruik van een module](azure-stack-edge-j-series-configure-gpu-modules.md).
