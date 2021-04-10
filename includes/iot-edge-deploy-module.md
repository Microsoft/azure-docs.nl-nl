---
title: bestand opnemen
description: bestand opnemen
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 6b9ec2017ffa5d4e4148b441aa23ed2eca6ee8b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99628900"
---
Een van de belangrijkste mogelijkheden van Azure IoT Edge is het implementeren van code naar uw IoT Edge apparaten vanuit de Cloud. *IoT Edge-modules* zijn uitvoerbare pakketten die zijn geïmplementeerd als containers. In deze sectie implementeert u een vooraf ontwikkelde module vanuit het [gedeelte IOT Edge modules van Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) rechtstreeks vanuit Azure IOT hub.

De module die u in deze sectie implementeert, simuleert een sensor en verzendt gegenereerde gegevens. Deze module is een handig stukje code wanneer u aan de slag gaat met IoT Edge, omdat u de gesimuleerde gegevens kunt gebruiken voor ontwikkel- en testdoeleinden. Als u precies wilt zien wat deze module doet, kunt u de [broncode van de gesimuleerde temperatuursensor bekijken](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Volg deze stappen om uw eerste module te implementeren vanuit Azure Marketplace.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en ga naar uw IOT-hub.

1. Selecteer in het menu aan de linkerkant onder **automatische Apparaatbeheer** de optie **IOT Edge**.

1. Selecteer de apparaat-ID van het doel apparaat in de lijst met apparaten.

1. Selecteer op de bovenste balk **Modules instellen**.

   ![Scherm opname van het selecteren van modules instellen.](./media/iot-edge-deploy-module/select-set-modules.png)

1. Open onder **IOT Edge modules** de vervolg keuzelijst **toevoegen** en selecteer vervolgens **Marketplace-module**.

   ![Scherm opname van de vervolg keuzelijst toevoegen.](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. Zoek en selecteer de module in de **Marketplace van IOT Edge-module** `Simulated Temperature Sensor` .

   De module wordt toegevoegd aan de sectie IoT Edge modules met de gewenste **uitvoerings** status.

1. Selecteer **Volgende: Routes** om door te gaan naar de volgende stap in de wizard.

   ![Scherm opname die laat zien hoe de volgende stap gaat nadat de module is toegevoegd.](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. Op het tabblad **routes** verwijdert u de standaard route, **route** en selecteert u **volgende: controleren + maken** om door te gaan naar de volgende stap van de wizard.

   >[!Note]
   >Routes worden gemaakt met behulp van naam-en waardeparen. Op deze pagina ziet u twee routes. De standaard route, **route**, verzendt alle berichten naar IOT hub (dat wordt aangeroepen `$upstream` ). Er is automatisch een tweede route, **SimulatedTemperatureSensorToIoTHub**, gemaakt toen u de module van Azure Marketplace hebt toegevoegd. Deze route verzendt alle berichten van de gesimuleerde temperatuur module naar IoT Hub. U kunt de standaardroute verwijderen, omdat deze in dit geval overbodig is.

   ![Scherm opname van het verwijderen van de standaard route en de volgende stap wordt verplaatst.](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. Controleer het JSON-bestand en selecteer vervolgens **maken**. Het JSON-bestand definieert alle modules die u implementeert op uw IoT Edge-apparaat. U ziet de **SimulatedTemperatureSensor** -module en de twee runtime modules, **edgeAgent** en **edgeHub**.

   >[!Note]
   >Wanneer u een nieuwe implementatie bij een IoT Edge-apparaat indient, wordt er niets naar uw apparaat gepusht. In plaats daarvan voert het apparaat regelmatig query’s uit naar eventuele nieuwe instructies. Als het apparaat een manifest van een bijgewerkte implementatie vindt, wordt de informatie over de nieuwe implementatie gebruikt om installatiekopieën van de module op te halen uit de cloud en wordt een lokale uitvoering van de modules gestart. Dit proces kan enkele minuten duren.

1. Nadat u de informatie over de implementatie van de module hebt gemaakt, gaat de wizard terug naar de pagina met apparaatdetails. Bekijk de implementatie status op het tabblad **modules** .

   U ziet drie modules: **$edgeAgent**, **$edgeHub** en **SimulatedTemperatureSensor**. Als een of meer van de modules **Ja** zijn onder **opgegeven in de implementatie** , maar niet onder **gerapporteerd door het apparaat**, wordt het IOT edge apparaat nog steeds gestart. Wacht enkele minuten en vernieuw vervolgens de pagina.

   ![Scherm opname van de gesimuleerde temperatuur sensor in de lijst met geïmplementeerde modules.](./media/iot-edge-deploy-module/view-deployed-modules.png)
