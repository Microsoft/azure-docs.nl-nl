---
title: Een nieuwe IoT-simulatie maken - Azure | Microsoft Docs
description: In deze zelfstudie gebruikt u Apparaatsimulatie om een nieuwe simulatie te maken en uit te voeren.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: 3376b3714dc41c7d5ce33756671050d19471a757
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057708"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>Zelfstudie: Een nieuwe IoT-apparaatsimulatie maken en uitvoeren

In deze zelfstudie gebruikt u Apparaatsimulatie om met behulp van een of meer gesimuleerde apparaten een nieuwe IoT-simulatie te maken en uit te voeren.

In deze zelfstudie hebt u:

>[!div class="checklist"]
> * Alle actieve en historische simulaties weergeven
> * Een nieuwe simulatie maken en uitvoeren
> * Metrische gegevens voor een simulatie weergeven
> * Een simulatie stoppen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt volgen, hebt u een geïmplementeerde instantie van Apparaatsimulatie in uw Azure-abonnement nodig.

Als u nog geen apparaatsimulatie hebt geïmplementeerd, raadpleegt u [Apparaatsimulatie implementeren](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) in GitHub.

## <a name="view-simulations"></a>Simulaties weergeven

Selecteer **Simulaties** in de menubalk. De pagina **Simulaties** bevat informatie over alle beschikbare simulaties. Op deze pagina ziet u simulaties die momenteel worden uitgevoerd en die eerder zijn uitgevoerd. Als u een eerdere simulatie opnieuw wilt uitvoeren, klikt u op de tegel voor de simulatie om de details van de simulatie te openen:

![Simulaties](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>Een simulatie maken

Als u een simulatie wilt maken, klikt u op **+ Nieuwe simulatie** in de rechterbovenhoek.

Een simulatie bestaat uit een of meer apparaatmodellen. Het apparaatmodel definieert het gedrag, telemetrie en de indeling van berichten voor het apparaat dat wordt gesimuleerd.

Als u een apparaatmodel wilt toevoegen, klikt u op **+ Een apparaattype toevoegen** en selecteert u het apparaatmodel in de lijst. De simulatie kan meer dan één apparaatmodel bevatten. Elk apparaatmodel kan een ander aantal apparaten en een andere berichtenfrequentie hebben.

Als de vorm van de nieuwe simulatie voltooid is, klikt u op **Simulatie starten** om de simulatie te starten.

Afhankelijk van het aantal gesimuleerde apparaten, kan het wel een paar minuten duren voordat de simulatie is geconfigureerd en wordt gestart:

![Nieuwe simulatie](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>Een simulatie stoppen

Wanneer u op **Simulatie starten** klikt, wordt de pagina met details van de simulatie weergegeven. Deze pagina met details toont live simulatiestatistieken en metrische gegevens van IoT Hub. U kunt ook naar deze detailpagina navigeren door op de tegel voor de simulatie op de pagina **Simulaties** te klikken.

Als u een simulatie wilt stoppen, klikt u op **Simulatie stoppen** in de actiebalk in de rechterbovenhoek.

![Simulatie stoppen](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een simulatie kunt maken, uitvoeren en stoppen. U hebt ook geleerd hoe u de simulatiedetails kunt weergeven. Als u meer te weten wilt komen over het uitvoeren van simulaties, gaat u verder met de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Een aangepast gesimuleerd apparaat maken](iot-accelerators-device-simulation-create-custom-device.md)
