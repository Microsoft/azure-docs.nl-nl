---
title: Wat is OPC Twin - Azure | Microsoft Docs
description: In dit artikel vindt u een overzicht van OPC Twin. OPC Twin biedt detectie, registratie en extern beheer van industriële apparaten via REST-API's.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9825e5e85c8da27b0f3fc2b9d78fcc9ca3513389
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646719"
---
# <a name="what-is-opc-twin"></a>Wat is OPC Twin?

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

OPC Twin bestaat uit microservices die gebruikmaken van Azure IoT Edge en IoT Hub om verbinding te maken met de cloud en het fabrieksnetwerk. OPC Twin biedt detectie, registratie en extern beheer van industriële apparaten via REST-API's. Voor OPC Twin is geen OPC Unified Architecture-SDK (OPC UA) vereist. Het is neutraal voor programmeertalen en kan worden opgenomen in een serverloze werkstroom. In dit artikel worden verschillende toepassingsscenario’s voor OPC Twin beschreven.

## <a name="discovery-and-control"></a>Detectie en beheer
U kunt OPC Twin gebruiken voor eenvoudig detectie en registratie.

### <a name="simple-discovery-and-registration"></a>Eenvoudige detectie en registratie
Met OPC Twin kunnen fabrieksmedewerkers het fabrieksnetwerk scannen, zodat OPC UA-servers kunnen worden gedetecteerd en geregistreerd. Als alternatief kunnen fabrieksmedewerkers ook handmatig OPC UA-apparaten registreren met behulp van een bekende detectie-URL. De fabrieksmedewerker kan bijvoorbeeld op afstand een scan van het netwerk activeren en de OPC UA-servers visueel weergeven om verbinding te maken met alle OPC UA-apparaten nadat de IoT Edge-gateway met een OPC Twin-module in de fabriek is geïnstalleerd. 

### <a name="simple-control"></a>Eenvoudig beheer
Met OPC Twin kunnen fabrieksmedewerkers reageren op gebeurtenissen en hun machines in de fabriek automatisch of handmatig opnieuw on the fly configureren vanuit de cloud. OPC Twin biedt REST API's om services op de OPC UA-server aan te roepen, door de adresruimte te bladeren, variabelen te lezen/schrijven en methoden uit te voeren. Stel dat een ketel een temperatuur-KPI gebruikt om de productielijn aan te sturen. De temperatuursensor publiceert de wijziging in gegevens met behulp van OPC Publisher. De fabrieksmedewerker ontvangt de waarschuwing dat de temperatuur de drempelwaarde heeft bereikt. De productielijn wordt automatisch afgekoeld via OPC Twin. De fabrieksmedewerker wordt op de hoogte gesteld van het afkoelen.

## <a name="authentication"></a>Verificatie
U kunt OPC Twin gebruiken voor eenvoudige verificatie en voor een eenvoudige ontwikkelaarservaring.

### <a name="simple-authentication"></a>Eenvoudige verificatie 
OPC Twin maakt gebruik van verificatie en controles van begin tot eind met behulp van Azure Active Directory (AAD). Met OPC Twin kan de toepassing bijvoorbeeld boven op OPC Twin worden ontwikkeld om vast te stellen wat een operator op een computer heeft uitgevoerd. Aan de kant van de machine wordt OPC UA-auditing gebruikt. Aan de cloudzijde worden een onveranderlijk logboek voor clientaudits en AAD-verificatie op de REST API gebruikt.

### <a name="simple-developer-experience"></a>Eenvoudige ontwikkelervaring 
OPC Twin kan worden gebruikt met toepassingen die in elke mogelijke programmeertaal zijn geschreven via REST API's. Omdat ontwikkelaars een OPC UA-client in een oplossing integreren, is kennis van de OPC UA-SDK niet nodig. OPC Twin kan naadloos worden geïntegreerd in stateless, serverloze architecturen. Zo kan een volledige stack webontwikkelaar die een toepassing voor een alarm- en een gebeurtenisdashboard ontwikkelt met behulp van OPC Twin de logica schrijven om te reageren op gebeurtenissen in JavaScript of TypeScript zonder kennis te hebben van C, C# of de volledige OPC UA-stackimplementatie. 

## <a name="next-steps"></a>Volgende stappen

Nu u over OPC Twin en het gebruik ervan hebt geleerd, is dit de voorgestelde volgende stap:

[Wat is OPC Vault?](overview-opc-vault.md)