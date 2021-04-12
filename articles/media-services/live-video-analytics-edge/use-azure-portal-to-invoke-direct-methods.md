---
title: Azure Portal gebruiken om directe methoden aan te roepen
description: Dit artikel is een overzicht over het gebruik van Azure Portal om direct methoden aan te roepen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 07/24/2020
ms.author: inhenkel
ms.openlocfilehash: ddd6a94015829a0b5dd4053f4d3a2fd6d2650d32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492651"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>Azure Portal gebruiken om directe methoden aan te roepen

IoT Hub biedt u de mogelijkheid om [directe methoden](../../iot-hub/iot-hub-devguide-direct-methods.md#method-invocation-for-iot-edge-modules) op edge-apparaten aan te roepen vanuit de cloud. De module Live Video Analytics in IoT Edge (LVA) biedt verschillende [directe methoden](./direct-methods.md) die kunnen worden gebruikt om verschillende werkstromen te definiëren, implementeren en instantiëren voor het analyseren van live video.

In dit artikel leert u hoe u directe-methodeaanroepen kunt aanroepen in de module Live Video Analytics in IoT Edge (LVA) via Azure Portal.

## <a name="prerequisites"></a>Vereisten

* De module Live Video Analytics in IoT Edge (LVA) wordt op uw edge-apparaat uitgevoerd met behulp van de methoden die beschreven staan in [Snelstart: Live Video Analytics in IoT Edge](./get-started-detect-motion-emit-events-quickstart.md) of met behulp van de [portal.](./deploy-iot-edge-device.md)

* U hebt kennis van [Live Video Analytics](./overview.md) en [het concept mediagraaf](./media-graph-concept.md).

## <a name="invoking-direct-methods-via-azure-portal"></a>Directe methoden aanroepen via Azure Portal

Elk van de [directe methoden](./direct-methods.md) die door de LVA-module beschikbaar worden gemaakt, kunnen via Azure Portal worden aangeroepen. In de onderstaande stappen worden de details voor één directe methode gegeven. U kunt andere via dezelfde stappen directe methoden aanroepen. Elke directe methode vereist echter een specifieke JSON-hoofdtekst.

Gebruik de methodeaanroep `GraphTopologyList` om een lijst op te halen met alle graaftopologieën die momenteel zijn geïmplementeerd op de module Live Video Analytics in IoT Edge (LVA). Gebruik de volgende stappen om deze directe methode aan te roepen:

1. Aanmelden bij Azure Portal
1. Zoek de relevante resourcegroep op de startpagina van de portal om uw IoT Hub te zoeken, of selecteer deze als u uw IoT Hub kent.
    ![resourcegroep op de startpagina van de portal](media/use-azure-portal-to-invoke-directs-methods/portal-rg-home.png)
1. Selecteer op de IoT Hub-pagina, onder Automatische Apparaatbeheer, IoT Edge om de verschillende apparaat-Id's weer te geven. Selecteer de relevante apparaat-id om de modules weer te geven die op het apparaat worden uitgevoerd.
    ![iot hub-pagina](media/use-azure-portal-to-invoke-directs-methods/iot-hub-page.png)
1. Selecteer de module Live Video Analytics in IoT Edge om de configuratiepagina te openen.<br><br>
    ![De module Live Video Analytics in IoT Edge selecteen om de configuratiepagina te openen](media/use-azure-portal-to-invoke-directs-methods/modules.png)
1. Selecteer de menuoptie Directe methode. <br><br>
    ![Klikken op de menuoptie Directe methode](media/use-azure-portal-to-invoke-directs-methods/module-details.png)
    > [!NOTE]
    > Mogelijk moet u een waarde toevoegen in de secties van de verbindingsreeks, zoals u kunt zien op de huidige pagina. U hoeft niets te verbergen of te wijzigen in de sectie Instellingsnaam. U kunt dit openbaar houden.

1. Typ *GraphTopologyList* in het veld **Methodenaam**.
1. Kopieer en plak de JSON hieronder in het veld **Payload**.
    ```json
    {
    "@apiVersion": "2.0"
    }
    ```
1. Selecteer de knop **Methode aanroepen** boven aan de pagina.<br><br>
    ![knop methode aanroepen](media/use-azure-portal-to-invoke-directs-methods/direct-method.png)
1. U ziet het bericht Status 200 in het gedeelte **Resultaat**.<br><br>
    ![time-out van verbinding](media/use-azure-portal-to-invoke-directs-methods/connection-timeout.png)

## <a name="responses"></a>Antwoorden

| Voorwaarde             | Statuscode | Gedetailleerde foutcode |
|-----------------------|-------------|---------------------|
| Geslaagd               | 200         | N.v.t.                 |
| Algemene gebruikersfouten   | 400-bereik   |                     |
| Algemene serverfouten | 500-bereik   |                     |

## <a name="next-steps"></a>Volgende stappen

Meer directe methoden kunt u vinden op de pagina [directe methoden](./direct-methods.md) .

> [!NOTE]
> Er wordt een specifieke topologie gemaakt door een instantie van de graaf. Zorg er dus voor dat u de juiste topologie hebt ingesteld voordat u een instantie van de graaf maakt.

[Snelstart: Detecteren van beweging](./get-started-detect-motion-emit-events-quickstart.md) is een goede referentie om inzicht te krijgen in de exacte volgorde van directe-methodeaanroepen die moeten worden uitgevoerd.
