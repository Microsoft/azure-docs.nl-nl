---
title: Verbinding maken met een Rigado Cascade 500 in azure IoT Central | Microsoft Docs
description: Meer informatie over hoe u een Rigado trapsgewijs 500-gateway apparaat verbindt met uw IoT Central-toepassing.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 0000e7690ab92f469a7417e82cb375c524e0b343
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014759"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Een Rigado-trapsgewijs 500-gateway apparaat verbinden met uw Azure IoT Central-toepassing

*Dit artikel is van toepassing op oplossingen bouwers.*

In dit artikel wordt beschreven hoe u als opbouw functie voor oplossingen een Rigado Cascade 500-gateway apparaat kunt verbinden met uw Microsoft Azure IoT Central toepassing. 

## <a name="what-is-cascade-500"></a>Wat is trapsgewijs 500?

Cascade 500 IoT-gateway is een hardware-aanbieding van Rigado die deel uitmaakt van hun trapsgewijze Edge-as-a-service-oplossing. Het biedt commerciële IoT-project-en product teams met flexibele Edge computing kracht, een robuuste, container toepassings omgeving en een groot aantal connectiviteits opties voor draadloze apparaten, waaronder Bluetooth 5, LTE, & Wi-Fi.

Cascade 500 is vooraf gecertificeerd voor Azure IoT Plug en Play (preview) zodat onze oplossingen bouwen het apparaat eenvoudig kunnen opheffen in hun end-to-end-oplossingen. Met de trapsgewijze gateway kunt u draadloos verbinding maken met een aantal Sens oren die op het gateway apparaat nabij zijn. Deze Sens oren kunnen worden geboardd naar IoT Central via het gateway apparaat.

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u de volgende resources nodig:

* Een Rigado-trapsgewijs 500-apparaat. Ga naar [Rigado](https://www.rigado.com/)voor meer informatie.
* Een Azure IoT Central-toepassing. Zie voor meer informatie de [nieuwe toepassing maken](./quick-deploy-iot-central.md).

## <a name="add-a-device-template"></a>Een apparaatsjabloon toevoegen

Als u een trapsgewijs 500-gateway apparaat wilt toevoegen aan uw Azure IoT Central-toepassings exemplaar, moet u een bijbehorende apparaatprofiel in uw toepassing configureren.

Een sjabloon voor een trapsgewijs 500-apparaat toevoegen: 

1. Ga naar het tabblad ***Apparaatbeheer** _ in het linkerdeel venster, selecteer _ * + nieuw * *: ![ nieuwe sjabloon voor het apparaat maken](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. De pagina biedt een optie voor het maken van een **aangepaste sjabloon** _ of _ *_een vooraf geconfigureerde apparaatprofiel gebruiken_**
1. Selecteer de sjabloon C500 in de lijst met vooraf geconfigureerde Apparaatinstellingen, zoals hieronder wordt weer gegeven: ![ Selecteer C500 Device Temp late](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Selecteer ***volgende: aanpassen*** om door te gaan naar de volgende stap. 
1. Selecteer in het volgende scherm de optie ***maken*** om de C500-apparaatprofiel voor te bereiden in uw IOT Central-toepassing.

## <a name="retrieve-application-connection-details"></a>Verbindings Details van toepassing ophalen

U moet nu de **scope-id** en de **primaire sleutel** voor uw Azure IOT Central-toepassing ophalen om verbinding te kunnen maken met het trapsgewijs 500-apparaat. 

1. Ga in het linkerdeel venster naar **beheer**  en klik op **verbinding met apparaat**. 
2. Noteer de **bereik-id** voor uw IOT Central-toepassing.
![App-bereik-ID](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Klik nu op **sleutels weer geven** en noteer de primaire sleutel van de **primaire sleutel** 
 ![](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Contact opnemen met Rigado om verbinding te maken met de gateway 

Als u het trapsgewijze 500-apparaat wilt verbinden met uw IoT Central-toepassing, moet u contact opnemen met Rigado en de details van de toepassings verbinding opgeven in de bovenstaande stappen. 

Zodra het apparaat is verbonden met internet, kan Rigado via een beveiligd kanaal een configuratie-update naar het trapsgewijs 500-gateway apparaat pushen. 

Met deze update worden de IoT Central verbindings Details toegepast op het trapsgewijs 500-apparaat en wordt het weer gegeven in de lijst met apparaten. 

![Lijst met apparaten](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

U bent nu klaar om uw C500-apparaat in uw IoT Central-toepassing te gebruiken!

## <a name="next-steps"></a>Volgende stappen

Als u een ontwikkelaar van een apparaat bent, kunt u het volgende doen:

- Meer informatie over [connectiviteit van apparaten in Azure IOT Central](./concepts-get-connected.md)
- Meer informatie over het [controleren van de connectiviteit van apparaten met behulp van Azure cli](./howto-monitor-devices-azure-cli.md)
