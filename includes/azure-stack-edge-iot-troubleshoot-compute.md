---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 03/23/2021
ms.openlocfilehash: 0d912d0ac3f0fcf4c52116e67909038a1973304b
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105260"
---
Gebruik de IoT Edge runtime-antwoorden van agent om problemen met betrekking tot berekeningen op te lossen. Hier volgt een lijst met mogelijke reacties:

* 200-OK
* 400-de implementatie configuratie is onjuist of ongeldig.
* 417-er is geen implementatie configuratie ingesteld op het apparaat.
* 412-de schema versie in de implementatie configuratie is ongeldig.
* 406-het IoT Edge apparaat is offline of stuurt geen status rapporten.
* 500-er is een fout opgetreden in de IoT Edge-runtime.

Zie [IOT Edge-agent](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent)voor meer informatie.

De volgende fout is gerelateerd aan de IoT Edge-service op uw Azure Stack Edge Pro-apparaat.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Reken modules hebben een onbekende status en kunnen niet worden gebruikt

#### <a name="error-description"></a>Foutbeschrijving

Alle modules op het apparaat tonen de onbekende status en kunnen niet worden gebruikt. De onbekende status blijft behouden tijdens het opnieuw opstarten.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Voorgestelde oplossing

Verwijder de IoT Edge-service en implementeer de module (s) vervolgens opnieuw. Zie [Remove IOT Edge service](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#remove-iot-edge-service)(Engelstalig) voor meer informatie.


### <a name="modules-show-as-running-but-are-not-working"></a>Modules worden weer gegeven als actief, maar werken niet

#### <a name="error-description"></a>Foutbeschrijving

De runtime status van de module wordt weer gegeven als actief, maar de verwachte resultaten worden niet weer gegeven. 

Dit kan zijn vanwege een probleem met de configuratie van de module route die niet werkt of waarvoor `edgehub` geen berichten worden doorgestuurd zoals verwacht. U kunt de `edgehub` Logboeken controleren. Als u ziet dat er fouten zijn zoals het mislukken van verbinding met de IoT Hub-service, is de meest voorkomende reden voor de verbindings problemen. De verbindings problemen kunnen zich voordoen omdat de AMPQ-poort die als standaard poort wordt gebruikt door IoT Hub service voor communicatie wordt geblokkeerd of de webproxyserver deze berichten blokkeert.

#### <a name="suggested-solution"></a>Voorgestelde oplossing

Voer de volgende stappen uit:
1. Als u de fout wilt oplossen, gaat u naar de IoT Hub-resource voor uw apparaat en selecteert u vervolgens uw edge-apparaat. 
1. Ga naar **modules instellen > runtime-instellingen**. 
1. Voeg de `Upstream protocol` omgevings variabele toe en wijs hieraan een waarde toe `AMQPWS` . De berichten die in dit geval worden geconfigureerd, worden via de websockets via poort 443 verzonden.

### <a name="modules-show-as-running-but-do-not-have-an-ip-assigned"></a>Modules worden weer gegeven als actief, maar er is geen toegewezen IP-adres

#### <a name="error-description"></a>Foutbeschrijving

De runtime status van de module wordt weer gegeven als actief, maar er is geen IP-adres toegewezen aan de app met containers. 

Dit komt doordat het bereik van IP-adressen die u hebt opgegeven voor Kubernetes External service Ip's niet voldoende is. U dient dit bereik uit te breiden om ervoor te zorgen dat elke container of VM die u hebt geïmplementeerd, wordt gedekt.

#### <a name="suggested-solution"></a>Voorgestelde oplossing

Voer de volgende stappen uit in de lokale web-UI van uw apparaat:
1. Ga naar de **Compute** -pagina. Selecteer de poort waarvoor u het Compute-netwerk hebt ingeschakeld. 
1. Voer een statisch, aaneengesloten bereik van IP-adressen in voor **Kubernetes externe service ip's**. U hebt 1 IP-adres nodig voor de `edgehub` service. Daarnaast hebt u één IP-adres nodig voor elke IoT Edge module en voor elke VM die u gaat implementeren. 
1. Selecteer **Toepassen**. Het gewijzigde IP-bereik moet direct van kracht worden.

Zie [externe service ip's voor containers wijzigen](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#change-external-service-ips-for-containers)voor meer informatie.

### <a name="configure-static-ips-for-iot-edge-modules"></a>Statische IP-adressen configureren voor IoT Edge modules

#### <a name="problem-description"></a>Beschrijving van het probleem

Kubernetes wijst dynamische IP-adressen toe aan elke IoT Edge module op uw Azure Stack Edge Pro GPU-apparaat. Er is een methode nodig om statische IP-adressen voor de modules te configureren.

#### <a name="suggested-solution"></a>Voorgestelde oplossing

U kunt vaste IP-adressen voor uw IoT Edge modules opgeven via de sectie K8s-experimentele, zoals hieronder wordt beschreven: 

```yaml
{
  "k8s-experimental": {
    "serviceOptions" : {
      "loadBalancerIP" : "100.23.201.78",
      "type" : "LoadBalancer"
    }
  }
}
```
### <a name="expose-kubernetes-service-as-cluster-ip-service-for-internal-communication"></a>Kubernetes-service beschikbaar maken als IP-adres van de cluster voor interne communicatie

#### <a name="problem-description"></a>Beschrijving van het probleem

Het IoT-Service type is standaard van het type load balancer en wijst externe IP-adressen toe. U wilt mogelijk geen extern IP-adres voor uw toepassing. Mogelijk moet u het Peul in het KUbernetes-cluster zichtbaar maken voor toegang als andere peulen en niet als een extern beschik bare load balancer service. 

#### <a name="suggested-solution"></a>Voorgestelde oplossing

U kunt de opties voor maken gebruiken via de sectie K8s-experimentele. De volgende service optie moet werken met poort bindingen.

```yaml
{
"k8s-experimental": {
  "serviceOptions" : {
    "type" : "ClusterIP"
    }
  }
}
```