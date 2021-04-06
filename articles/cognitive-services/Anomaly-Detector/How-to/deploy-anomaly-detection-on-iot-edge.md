---
title: Anomalie detectie uitvoeren op IoT Edge
titleSuffix: Azure Cognitive Services
description: Implementeer de afwijkings detector module voor IoT Edge.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: b4153b07b153a9ee0b16dc032ab5e7810e236d7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98936273"
---
# <a name="deploy-an-anomaly-detector-module-to-iot-edge"></a>Een anomalie detectie module implementeren voor het IoT Edge

Meer informatie over het implementeren van de Cognitive Services [anomaliey detector](../anomaly-detector-container-howto.md) -module op een IOT edge apparaat. Wanneer de module is geïmplementeerd in IoT Edge, wordt de-functie in IoT Edge samen met andere modules uitgevoerd als container exemplaren. Het stelt precies dezelfde Api's beschikbaar als een anomalie detectie container instantie die wordt uitgevoerd in een standaard docker-container omgeving. 

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement gebruiken. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.
* Installeer de [Azure CLI](/cli/azure/install-azure-cli).
* Een [IOT hub](../../../iot-hub/iot-hub-create-through-portal.md) en een [IOT Edge](../../../iot-edge/quickstart-linux.md) apparaat.

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>De module anomalie detectie op de rand implementeren

1. Voer in het Azure Portal **anomalie detectie in IOT Edge** in de zoek opdracht in en open het resultaat van de Azure Marketplace.
2. Hiermee gaat u naar de doel apparaten van de Azure Portal [voor IOT Edge module pagina](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector). Geef de volgende vereiste informatie op.

    1. Selecteer uw abonnement.

    1. Selecteer uw IoT Hub.

    1. Selecteer **apparaat zoeken** en een IOT edge apparaat zoeken.

3. Selecteer de knop **Create** (Maken).

4. Selecteer de **AnomalyDetectoronIoTEdge** -module.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="Afbeelding van IoT Edge modules gebruikers interface met AnomalyDetectoronIoTEdge-koppeling gemarkeerd met een rood vak om aan te geven dat dit het item is die moet worden geselecteerd.":::

5. Navigeer naar **Omgevingsvariabelen** en geef de volgende informatie op.

    1.  Behoud de waarde accepteren voor **EULA**.

    1. Vul uw Cognitive Services-eindpunt in **Facturering** in.

    1. Vul bij uw sleutel van de Cognitive Services-API **ApiKey** in.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="Omgevings variabelen met een rode vak rond de gebieden waarvoor waarden moeten worden ingevuld voor het eind punt en de API-sleutel":::

6. Selecteer **Update**.

7. Selecteer **volgende: routes** om uw route te definiëren. U geeft op dat alle berichten van alle modules naar Azure IoT Hub moeten worden gestuurd.

8. Selecteer **Volgende: Controleren en maken**. U kunt een preview bekijken van het JSON-bestand waarmee alle modules worden gedefinieerd die naar uw IoT Edge-apparaat worden geïmplementeerd.
    
9. Selecteer **Maken** om de module-implementatie te starten.

10. Nadat u de module-implementatie hebt voltooid, gaat u terug naar de pagina IoT Edge van uw IoT-hub. Selecteer uw apparaat in de lijst met IoT Edge-apparaten om de details weer te geven.

11. Schuif omlaag en bekijk de modules die worden weergegeven. Controleer of de runtime status wordt uitgevoerd voor uw nieuwe module. 

Raadpleeg de [gids voor probleem oplossing](../../../iot-edge/troubleshoot.md) om de runtime status van uw IOT edge-apparaat op te lossen

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>Anomalie detectie op een IoT Edge apparaat testen

U maakt een HTTP-aanroep naar het Azure IoT Edge-apparaat waarop de Azure Cognitive Services-container wordt uitgevoerd. De container bevat op REST gebaseerde eind punt-Api's. Gebruik de host, `http://<your-edge-device-ipaddress>:5000` , voor module-api's.

Als uw edge-apparaat nog geen binnenkomende communicatie op poort 5000 toestaat, moet u een nieuwe **regel voor binnenkomende poort** maken. 

Voor een **virtuele** Azure-machine kan dit worden ingesteld onder instellingen voor de inkomende poort  >    >  regel voor **netwerk verbinding** voor binnenkomende poorten  >    >  **toevoegen**.

Er zijn verschillende manieren om te controleren of de module actief is. Zoek het *externe IP-* adres en de weer gegeven poort van het apparaat in kwestie en open uw favoriete webbrowser. Gebruik de onderstaande aanvraag-Url's om te controleren of de container wordt uitgevoerd. De onderstaande voorbeeld aanvraag-Url's zijn `http://<your-edge-device-ipaddress:5000` , maar uw specifieke container kan variëren. Houd er rekening mee dat u het *externe IP-* adres van uw edge-apparaat moet gebruiken.

| Aanvraag-URL | Doel |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | De container bevat een startpagina. |
| `http://<your-edge-device-ipaddress>:5000/status` | Daarnaast wordt met GET gevraagd of de API-sleutel die wordt gebruikt om de container te starten, geldig is zonder dat dit een eindpunt query veroorzaakt. Deze aanvraag kan worden gebruikt voor Kubernetes- [en gereedheids tests](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/). |
| `http://<your-edge-device-ipaddress>:5000/swagger` | De container bevat een volledige set met documentatie voor de eindpunten en een functie **Uitproberen**. Met deze functie kunt u uw instellingen invoeren in een HTML-formulier op het web en de query maken zonder dat u code hoeft te schrijven. Nadat de query is geretourneerd, wordt een voor beeld van een krul opdracht weer gegeven om te demonstreren welke HTTP-headers en hoofdtekst indeling vereist zijn. |

![Start pagina van container](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>Volgende stappen

* Controleer de [installatie-en uitvoer containers](../anomaly-detector-container-configuration.md) voor het ophalen van de container installatie kopie en voer de container uit
* Controleer [Containers configureren](../anomaly-detector-container-configuration.md) voor configuratie-instellingen
* [Meer informatie over de API-service voor anomalie detectie](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
