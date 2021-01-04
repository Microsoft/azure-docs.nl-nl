---
title: Inleiding tot IoT-oplossingsversnellers - Azure | Microsoft Docs
description: Hier vindt u informatie over Azure IoT-oplossingsversnellers. IoT-oplossingsversnellers zijn volledige, end-to-end, kant-en-klare IoT-oplossingen.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 5012383e64a85ee025273f5339b828f5338e1d4f
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629065"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Wat zijn Azure IoT-oplossingsversnellers?

Een IoT-cloudoplossing maakt doorgaans gebruik van aangepaste code en cloudservices om connectiviteit, gegevensverwerking en analyses en presentatie van apparaten te beheren.

De IoT-oplossingsverbeteringen zijn complete, eenvoudig te implementeren IoT-oplossingen waarmee veelvoorkomende IoT-scenario's kunnen worden geïmplementeerd. De scenario's omvatten een verbonden factory en apparaatsimulatie. Wanneer u een oplossingsversneller implementeert, bevat de implementatie alle vereiste cloudservices samen met de vereiste toepassingscode.

De oplossingsversnellers vormen het startpunt voor uw eigen IoT-oplossingen. De broncode voor alle oplossingsversnellers is open-source en beschikbaar in GitHub. U wordt aangeraden om de oplossingsverbeteringen te downloaden en aan te passen zodat ze voldoen aan uw vereisten.

U kunt de oplossingsversnellers ook gebruiken als leermiddelen voordat u helemaal uw eigen IoT-oplossing gaat maken. De oplossingsversnellers implementeren bewezen procedures voor IoT-cloudoplossingen die u kunt volgen.

De toepassingscode in elke oplossingsverbetering bevat een web-app waarmee u de oplossingsverbetering beheren.

> [!NOTE]
> De oplossingen voor controle op afstand en voorspellend onderhoud zijn verwijderd van de site [Azure IoT-oplossingsversnellers](https://www.azureiotsolutions.com/Accelerators). Zie [Wat zijn Azure IoT-oplossingsversnellers? (vorige versie)](/previous-versions/azure/iot-accelerators/about-iot-accelerators) voor meer informatie.

## <a name="supported-iot-scenarios"></a>Ondersteunde IoT-scenario's

Er zijn momenteel twee oplossingsversnellers beschikbaar die u kunt implementeren:

### <a name="connected-factory"></a>Verbonden factory

Gebruik de [Connected Factory-oplossingsversneller](iot-accelerators-connected-factory-features.md) voor het verzamelen van telemetriegegevens van industriële activa met een [OPC Unified Architecture](https://opcfoundation.org/about/opc-technologies/opc-ua/)-interface en om deze activa te beheren. Industriële activa kunnen assembly- en teststations in een productielijn bevatten.

U kunt de verbonden factory gebruiken om industriële apparaten te controleren en te beheren:

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="Schermopname van het dashboard van de oplossing voor verbonden factory." lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="device-simulation"></a>Apparaatsimulatie

Gebruik de [Device Simulation-oplossingsversneller](iot-accelerators-device-simulation-overview.md) voor het laten draaien van gesimuleerde apparaten die realistische telemetriegegevens genereren. U kunt deze oplossingsversneller gebruiken voor het testen van het gedrag van de andere oplossingsversnellers of voor het testen van uw eigen aangepaste IoT-oplossingen.

U kunt de web-app voor apparaatsimulatie gebruiken om simulaties te configureren en uit te voeren:

:::image type="content" source="./media/about-iot-accelerators/ds-dashboard-inline.png" alt-text="Schermopname van het dashboard voor de oplossing voor apparaatsimulatie." lightbox="./media/about-iot-accelerators/ds-dashboard-expanded.png":::

## <a name="design-principles"></a>Ontwerpprincipes

Alle oplossingsversnellers volgen dezelfde ontwerpprincipes en -doelen. Het ontwerp ervan is:

* **Schaalbaar**, zodat u verbinding kunt maken met miljoenen verbonden apparaten en deze kunt beheren.
* **Uitbreidbaar**, zodat u ze kunt aanpassen aan uw behoeften.
* **Begrijpelijk**, zodat u precies weet hoe ze werken en hoe ze worden geïmplementeerd.
* **Modulair**, waardoor u services voor alternatieven kunt verwisselen.
* **Veilig**, vanwege de combinatie van Azure-beveiliging met ingebouwde functies voor connectiviteit en apparaatbeveiliging.

## <a name="architectures-and-languages"></a>Architecturen en talen

De oorspronkelijke oplossingsversnellers werden geschreven met behulp van .NET en een model-view-controller (MVC)-architectuur. Microsoft werkt de oplossingsversnellers bij met een nieuwe architectuur op basis van microservices. In de volgende tabel wordt de huidige status van de oplossingsverbeteringen weergegeven met koppelingen naar de GitHub-opslagplaatsen:

| Oplossingsverbetering   | Architectuur  | Talen     |
| ---------------------- | ------------- | ------------- |
| Verbonden factory      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Apparaatsimulatie      | Microservices | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

Zie [Inleiding tot de Azure IoT-referentiearchitectuur](/azure/architecture/reference-architectures/iot/) voor meer informatie over de microservicearchitectuur.

## <a name="deployment-options"></a>Implementatieopties

U kunt de oplossingsverbeteringen implementeren op de site [Microsoft Azure IoT-oplossingsverbeteringen](https://www.azureiotsolutions.com/Accelerators#) of door gebruik te maken van de opdrachtregel.

De kosten voor het uitvoeren van een oplossingsversneller zijn de gecombineerde [kosten van de onderliggende Azure-services](https://azure.microsoft.com/pricing). U ziet details van de gebruikte Azure-services wanneer u uw implementatieopties kiest.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de quickstart [Een oplossing voor verbonden factory uitproberen](quickstart-connected-factory-deploy.md) om een van de oplossingsverbeteringen uit te proberen.
