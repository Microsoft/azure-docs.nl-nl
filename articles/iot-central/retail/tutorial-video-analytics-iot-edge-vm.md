---
title: 'Zelfstudie: -een IoT Edge-exemplaar voor videoanalyse maken in Azure IoT Central (Linux-VM)'
description: In deze zelfstudie leert u hoe u een IoT Edge-exemplaar voor videoanalyse kunt maken op een virtuele Linux-machine dat kan worden gebruikt met de toepassingssjabloon voor object- en bewegingsdetectie in videoanalyse.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 10ddbf3dde62380eb79af685ad41b22e4552cea1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832620"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-linux-vm"></a>Zelfstudie: een IoT Edge-exemplaar voor videoanalyse maken in Azure IoT Central (Linux-VM)

Azure IoT Edge is een volledig beheerde service die voorziet in lokale cloudintelligentie door het implementeren en uitvoeren van:

* Aangepaste logica
* Azure-services
* Kunstmatige intelligentie

In IoT Edge worden deze services rechtstreeks uitgevoerd op platformonafhankelijke IoT-apparaten, zodat u uw IoT-oplossing veilig en op schaal kunt uitvoeren in de cloud of offline.

In deze zelfstudie wordt uitgelegd hoe u een IoT Edge-apparaat kunt voorbereiden in een Azure-VM. Met het IoT Edge-exemplaar worden modules voor Live Video Analytics uitgevoerd die worden gebruikt door de Azure IoT Central-toepassingssjabloon voor object- en bewegingsdetectie in videoanalyse.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Azure-VM maken waarop de Azure IoT Edge-runtime is geïnstalleerd
> * De IoT Edge-installatie voorbereiden om de Live Video Analytics-module te hosten en verbinding te maken met IoT Central

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u de vorige zelfstudie [Een toepassing voor videoanalyse maken in Azure IoT Central](./tutorial-video-analytics-create-app-yolo-v3.md) of [Een videoanalyse maken in Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) voltooien.

U hebt ook een Azure-abonnement nodig. Als u geen Azure-abonnement hebt, kunt u er gratis een maken via de [Azure-aanmeldingspagina](https://aka.ms/createazuresubscription).

## <a name="deploy-azure-iot-edge"></a>Azure IoT Edge implementeren

Selecteer de volgende knop om een Azure-VM te maken waarvoor de meest recente IoT Edge runtime-en Live Video Analytics-modules zijn geïnstalleerd:

[![De knop Implementeren naar Azure voor iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Flive-video-analytics%2Fmaster%2Fref-apps%2Flva-edge-iot-central-gateway%2Fvm_deploy%2FedgeModuleVMDeploy.json)

Gebruik de informatie in de volgende tabel om het formulier **Aangepaste implementatie** te voltooien:

| Veld | Waarde |
| ----- | ----- |
| Abonnement | Selecteer uw Azure-abonnement. |
| Resourcegroep | *lva-rg*: de resourcegroep die u hebt gemaakt in de vorige zelfstudie. |
| Regio       | *VS - oost* |
| Voorvoegsel van DNS-label | Kies een uniek DNS-voorvoegsel voor de VM. Moet bestaan uit alfabetische tekens, zonder cijfers of speciale tekens. |
| Gebruikersnaam van beheerder | *AzureUser* |
| Beheerderswachtwoord | Voer een wachtwoord in. Noteer het wachtwoord in het bestand *scratchpad.txt* want u hebt dit later nodig. |
| Bereik-ID | De **bereik-id** die u in de vorige zelfstudie in het bestand *scratchpad.txt* hebt genoteerd bij het toevoegen van het gatewayapparaat. |
| Apparaat-ID | *gateway-001*: het gatewayapparaat dat u hebt gemaakt in de vorige zelfstudie. |
| Apparaatsleutel | De **primaire sleutel van het apparaat** die u in de vorige zelfstudie in het bestand *scratchpad.txt* hebt genoteerd bij het toevoegen van het gatewayapparaat. |
| Host van IoT Central-app | De **toepassings-URL** die u in de vorige zelfstudie in het bestand *scratchpad.txt* hebt genoteerd. Bijvoorbeeld *traders.azureiotcentral.com*. |
| API-token van Iot Central-app | Het **API-token voor de operator** die u in de vorige zelfstudie in het bestand *scratchpad.txt* hebt genoteerd. |
| Inrichtingssleutel van IoT Central-apparaat | De **primaire sleutel voor de groep SAS-IoT-Devices** die u in de vorige zelfstudie in het bestand *scratchpad.txt* hebt genoteerd. |
| VM-grootte | *Standard_DS1_v2* |
| Besturingssysteemversie van Ubuntu | *18.04-LTS* |
| Locatie | *[resourceGroup().location]* |

Selecteer **Controleren en maken**. Wanneer de validatie is voltooid, selecteert u **Maken**. Het duurt doorgaans ongeveer drie minuten voordat de implementatie is voltooid. Wanneer de implementatie is voltooid, navigeert u naar de resourcegroep **lva-rg** in Azure Portal.

## <a name="ensure-the-iot-edge-runtime-loads-the-modules"></a>Zorgen dat de modules worden geladen met IoT Edge-runtime

Navigeer in Azure Portal naar de resourcegroep **lva-rg** en selecteer de virtuele machine. Selecteer vervolgens **Seriële console** in de sectie **Ondersteuning en probleemoplossing**.

Druk op **Enter** om de prompt voor `login:` weer te geven. Gebruik *AzureUser* als de gebruikersnaam en geef het wachtwoord op dat u hebt gekozen bij het maken van de VM.

Voer de volgende opdracht uit om de versie van IoT Edge-runtime te controleren. Op het moment van schrijven is de versie 1.0.9:

```bash
sudo iotedge --version
```

Geef een lijst met uw IoT Edge-modules weer met behulp van de opdracht:

```bash
sudo iotedge list
```

De implementatie heeft de volgende vijf IoT Edge-modules geconfigureerd voor uitvoeren:

* LvaEdgeGatewayModule
* edgeAgent
* edgeHub
* lvaEdge
* lvaYolov3

De implementatie heeft een aangepaste IoT Edge-omgeving gemaakt met de vereiste modules voor Live Video Analytics. De implementatie heeft de standaardversie van **config.yaml** bijgewerkt om ervoor te zorgen dat IoT Edge-runtime gebruikmaakt van IoT Device Provisioning-Service om verbinding te maken met IoT Central. De implementatie heeft ook een bestand met de naam **state.json** in de map **/data/storage** gemaakt om aanvullende configuratiegegevens aan de modules te verstrekken. Zie de zelfstudie [Een IoT Edge-exemplaar voor videoanalyse maken (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md) voor meer informatie.

Zie [Problemen met uw IoT Edge-apparaat oplossen](../../iot-edge/troubleshoot.md) voor informatie over het oplossen van problemen met het IoT Edge-apparaat

## <a name="use-the-rtsp-simulator"></a>De RTSP-simulator gebruiken

Als u geen echte camera-apparaten kunt verbinden met uw IoT Edge apparaat, kunt u de twee gesimuleerde camera-apparaten in de toepassingssjabloon voor videoanalyse gebruiken. In deze sectie wordt uitgelegd hoe u een gesimuleerde videostroom kunt gebruiken voor uw IoT Edge-apparaat.

Voor deze instructies wordt [Live555 Media Server](http://www.live555.com/mediaServer/) gebruikt als een RTSP-simulator in een Docker-container.

> [!NOTE]
> Verwijzingen naar software van derden in deze opslagplaats zijn alleen ter informatie en voor het gebruiksgemak. Microsoft doet geen aanbevelingen met betrekking tot en verstrekt geen rechten voor software van derden. Zie [Live555 Media Server](http://www.live555.com/mediaServer/) voor meer informatie.

Gebruik de volgende opdracht om het hulpprogramma **rtspvideo** uit te voeren in een Docker-container op uw IoT Edge-VM. Met de Docker-container wordt een RTSP-stroom op de achtergrond gemaakt:

```bash
sudo docker run -d --name live555 --rm -p 554:554 mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2
```

Gebruik de volgende opdracht om een lijst met Docker-containers weer te geven:

```bash
sudo docker ps
```

De lijst bevat een container met de naam **live555**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met de toepassing, kunt u alle resources die u hebt gemaakt, als volgt verwijderen:

1. Ga in de IoT Central-toepassing naar de pagina **Uw toepassing** in de sectie **Beheer**. Selecteer vervolgens **Verwijderen**.
1. Verwijder in Azure Portal de resourcegroep **lva-rg**.
1. Stop op de lokale machine de Docker-container **amp-viewer**.

## <a name="next-steps"></a>Volgende stappen

U hebt nu de IoT Edge runtime, de LVA-modules en de Live555-simulatiestroom geïmplementeerd op een Linux-VM die wordt uitgevoerd in Azure.

Als u de camera's wilt beheren, gaat u verder met de volgende zelfstudie

> [!div class="nextstepaction"]
> [Een toepassingssjabloon voor object- en bewegingsdetectie in videoanalyse bewaken en beheren](./tutorial-video-analytics-manage.md)