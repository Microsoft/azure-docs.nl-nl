---
title: OPC Twin-architectuur - Azure | Microsoft Docs
description: In dit artikel vindt u een overzicht van de architectuur van OPC Twin. Hierin wordt het detecteren, activeren, bladeren en bewaken van de server beschreven.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 602d2f77564709294d28b797ff3f03f12b3b32d8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646735"
---
# <a name="opc-twin-architecture"></a>OPC Twin-architectuur

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

De volgende diagrammen illustreren de OPC Twin-architectuur.

## <a name="discover-and-activate"></a>Detecteren en activeren

1. De operator schakelt een netwerkscan in voor de module of voert een eenmalige detectie uit met behulp van een detectie-URL. De gedetecteerde eindpunten en toepassingsgegevens worden via telemetrie verzonden naar de onboarding-agent voor verwerking.  De onboarding-agent voor het OPC UA-apparaat verwerkt OPC UA-serverdetectiegebeurtenissen die worden verzonden door de methode OPC Twin IoT Edge-module als deze actief is in de detectie- of scanmodus. De detectiegebeurtenissen resulteren in het registreren van toepassingen en updates in het register van het OPC UA-apparaat.

   ![Diagram waarin de OPC Twin-architectuur wordt weergegeven met de OPC Twin IoT Edge-module in de detectie- of scanmodus.](media/overview-opc-twin-architecture/opc-twin1.png)

1. De operator inspecteert het certificaat van het gedetecteerde eindpunt en activeert het geregistreerde eindpunt voor toegang. 

   ![Diagram waarin de OPC Twin-architectuur wordt weergegeven met de IoT Edge 'Twin identity'.](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Bladeren en bewaken

1. Na de activering kan de operator de REST API van de Twin-service gebruiken om te bladeren of het serverinformatiemodel te inspecteren, objectvariabelen te lezen/schrijven en methoden aan te roepen.  De gebruiker gebruikt een vereenvoudigd OPC UA-API, die volledig wordt weergegeven in HTTP en JSON.

   ![Diagram waarin de configuratie van de OPC Twin-architectuur wordt weergegeven voor het bladeren door en het controleren van het servergegevensmodel.](media/overview-opc-twin-architecture/opc-twin3.png)

1. De REST-interface van de Twin-service kan ook worden gebruikt voor het maken van bewaakte items en abonnementen in de OPC Publisher. Met de OPC Publisher kunnen telemetrie worden verzonden vanuit OPC UA-serversystemen naar IoT Hub. Zie [wat is OPC Publisher](overview-opc-publisher.md)voor meer informatie over de OPC Publisher.

   ![De werking van OPC Twin](media/overview-opc-twin-architecture/opc-twin4.png)
