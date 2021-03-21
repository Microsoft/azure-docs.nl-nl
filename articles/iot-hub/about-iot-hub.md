---
title: Inleiding tot Azure IoT Hub | Microsoft Docs
description: Lees hier alles over Azure IoT Hub. Deze IoT-service is gebouwd voor schaalbare gegevensopname, apparaatbeheer en beveiliging.
author: nberdy
ms.author: nberdy
ms.date: 08/08/2019
ms.topic: overview
ms.custom:
- mvc
- amqp
- mqtt
- 'role: Direction'
- 'role: System Architecture'
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 86a373844b370cc9f9ce31dc65b2039a81279803
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102454767"
---
# <a name="what-is-azure-iot-hub"></a>Wat is Azure IoT Hub?

IoT Hub is een beheerde service die wordt gehost in de cloud en die fungeert als een centrale berichtenhub voor bidirectionele communicatie tussen uw IoT-toepassing en de apparaten die hiermee worden beheerd. Met Azure IoT Hub kunt u IoT-oplossingen bouwen met betrouwbare en veilige communicatie tussen miljoenen IoT-apparaten en een back-end die in de cloud wordt gehost. U kunt vrijwel elk apparaat verbinden met IoT Hub.

IoT Hub biedt ondersteuning voor communicatie van het apparaat naar de cloud en omgekeerd. IoT Hub ondersteunt meerdere berichtenpatronen, zoals telemetrie voor apparaat-naar-cloud, bestandsupload vanaf apparaten en request-reply-methoden voor het beheren van uw apparaten vanuit de cloud. Controle met behulp van IoT Hub helpt u bij het onderhouden van uw oplossing door het traceren van gebeurtenissen, zoals het maken van apparaten, apparaatstoringen en apparaatverbindingen.

De mogelijkheden van IoT Hub helpen u bij het bouwen van schaalbare, complete IoT-oplossingen, bijvoorbeeld voor het beheren van industriële apparatuur die worden gebruikt in productieomgevingen, het volgen van waardevolle items in de gezondheidszorg en het monitoren van het gebruik van kantoorgebouwen.

## <a name="scale-your-solution"></a>Uw oplossing schalen

IoT Hub kan worden opgeschaald naar miljoenen gelijktijdig verbonden apparaten en miljoenen gebeurtenissen per seconde om zo uw IoT-workloads te ondersteunen. Zie [IoT Hub schalen](iot-hub-scaling.md) voor meer informatie over het schalen van uw IoT-hub. Bekijk de [pagina met prijzen](https://azure.microsoft.com/pricing/details/iot-hub/) voor meer informatie over de verschillende lagen service die door IoT Hub wordt geboden en hoe u het best kunt voldoen aan uw schaalbaarheidsbehoeften.

## <a name="secure-your-communications"></a>Uw communicatie beveiligen

Met IoT Hub beschikt u over een beveiligd communicatiekanaal dat uw apparaten kunnen gebruiken om gegevens te verzenden.

* Verificatie per apparaat zorgt ervoor dat elk apparaat een veilige verbinding kan opzetten met IoT Hub en dat elk apparaat veilig kan worden beheerd.

* U hebt volledige controle over de toegang van apparaten en u kunt de verbindingen beheren op apparaatniveau.

* De [IoT Hub Device Provisioning Service](../iot-dps/index.yml) zorgt ervoor dat apparaten automatisch worden ingericht met de juiste IoT-hub wanneer het apparaat voor het eerst wordt opgestart.

* Verschillende verificatietypen ondersteunen allerlei mogelijkheden voor apparaten:

  * Verificatie aan de hand van een SAS-token betekent dat u snel aan de slag kunt met uw IoT-oplossing.

  * Verificatie via afzonderlijke X.509-certificaten zorgt voor een veilige, op standaarden gebaseerde verificatie.

  * X.509-CA-verificatie is beschikbaar voor eenvoudige, op standaarden gebaseerde inschrijving.

## <a name="route-device-data"></a>Apparaatgegevens routeren

De ingebouwde functionaliteit voor het routeren van berichten biedt u de flexibiliteit voor het instellen van automatische berichtendistributie op basis van regels:

* Gebruik [berichtroutering](iot-hub-devguide-messages-d2c.md) om te bepalen naar welke locatie de hub telemetriegegevens van apparaten verstuurt.

* Er zijn geen extra kosten verbonden aan het routeren van berichten naar meerdere eindpunten.

* Routeringsregels zonder code hebben de plaats ingenomen van aangepaste code voor het verzenden van berichten.

## <a name="integrate-with-other-services"></a>Integreren met andere services

U kunt IoT Hub integreren met andere Azure-services om zo complete, end-to-end-oplossingen te bouwen. Enkele voorbeelden:

* Gebruik [Azure Event Grid](../event-grid/index.yml) om op een betrouwbare, veilige en schaalbare manier snel te reageren op kritieke gebeurtenissen.

* Gebruik [Azure Logic Apps](../logic-apps/index.yml) voor het automatiseren van bedrijfsprocessen.

* Gebruik [Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md) om machine learning en AI-modellen toe te voegen aan uw oplossing.

* Gebruik [Azure Stream Analytics](../stream-analytics/index.yml) voor het in realtime uitvoeren van analytische berekeningen op de gegevens die door uw apparaten worden gestreamd.

## <a name="configure-and-control-your-devices"></a>Uw apparaten configureren en beheren

U hebt de beschikking over allerlei ingebouwde functies om de apparaten te beheren die zijn verbonden met IoT Hub.

* Metagegevens en statusinformatie van al uw apparaten opslaan, synchroniseren en opvragen.

* Apparaatstatus instellen per apparaat of op basis van algemene eigenschappen van apparaten.

* Automatisch reageren op een door het apparaat gerapporteerde statuswijziging met geïntegreerde berichtroutering.

## <a name="make-your-solution-highly-available"></a>Uw oplossing maximaal beschikbaar maken

Er geldt een [SLA (Service Level Agreement) voor IoT-Hub](https://azure.microsoft.com/support/legal/sla/iot-hub/) van 99,9%. In de volledige [Azure SLA](https://azure.microsoft.com/support/legal/sla/) wordt de gegarandeerde beschikbaarheid van Azure als geheel uitgelegd.

## <a name="connect-your-devices"></a>Uw apparaten verbinden

Gebruik de bibliotheken uit de [apparaat-SDK van Azure IoT](./iot-hub-devguide-sdks.md) om toepassingen te bouwen die worden uitgevoerd op uw apparaten en die communiceren met IoT Hub. Voorbeelden van ondersteunde platforms zijn diverse Linux-distributies, Windows en realtime-besturingssystemen. Enkele ondersteunde talen:

* C
* Embedded C
* C#
* Java
* Python
* Node.js.

IoT Hub en de apparaat-SDK's ondersteunen de volgende protocollen voor het verbinden van apparaten:

* HTTPS
* AMQP
* AMQP via WebSockets
* MQTT
* MQTT via WebSockets

IoT Hub en de Sdk's van het apparaat ondersteunen de [Azure IoT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md) -conventies voor het verbinden van apparaten. IoT Plug en Play-apparaten gebruiken een model voor het adverteren van hun mogelijkheden aan IoT-Plug en Play-toepassingen. Met het model voor apparaten kunnen bouwers van oplossingen smart-apparaten integreren met hun oplossingen zonder hand matige configuratie.

Als uw oplossing de apparaatbibliotheken niet kan gebruiken, kunnen apparaten het protocol MQTT v3.1.1, HTTPS 1.1 of AMQP 1.0 gebruiken om op systeemeigen wijze verbinding te maken met uw hub.

Als uw oplossing geen van de ondersteunde protocollen kan gebruiken, kunt u IoT Hub uitbreiden voor ondersteuning van aangepaste protocollen:

* Gebruik [Azure IoT Edge](../iot-edge/index.yml) om een veldgateway te maken waarmee u aan de rand protocolomzetting kunt uitvoeren.

* Pas de [protocolgateway van Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) aan om protocolomzetting uit te voeren in de cloud.

## <a name="quotas-and-limits"></a>Quota en limieten

Voor elk Azure-abonnement gelden standaardquotalimieten ter voorkoming van misbruik van de service. Deze limieten kunnen invloed hebben op het bereik van uw IoT-oplossing. De huidige limiet voor een abonnementsvariant is 50 IoT-hubs per abonnement. U kunt een verzoek voor een groter quota indienen door contact op te nemen met de ondersteuning. Zie [Quota en beperkingen voor IoT Hub](iot-hub-devguide-quotas-throttling.md) voor meer informatie. Voor meer informatie over quotabeperkingen bekijkt u een van de volgende artikelen:

* [Limieten, quota en beperkingen van Azure-abonnementen en -services](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [IoT Hub throttling and you](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="iot-hub-on-azure-stack-hub-preview"></a>IoT Hub in Azure Stack Hub (preview)

Met IoT Hub op Azure Stack Hub (preview) kunt u hybride IoT-oplossingen maken. IoT Hub is een beheerde service die fungeert als een centrale berichtenhub voor bidirectionele communicatie tussen uw IoT-toepassing en de apparaten die hiermee worden beheerd. Met IoT Hub kunt u in Azure Stack Hub IoT-oplossingen bouwen met betrouwbare en veilige communicatie tussen IoT-apparaten en uw on-premises oplossingen.

IoT Hub op Azure Stack Hub is gratis beschikbaar tijdens de openbare preview. Zie voor meer informatie het [overzicht voor IoT Hub in Azure Stack Hub](/azure-stack/operator/iot-hub-rp-overview).

## <a name="next-steps"></a>Volgende stappen

Bekijk de snelstartgidsen voor IoT Hub als u een end-to-end IoT-oplossing wilt uitproberen:

* [Snelstart: Telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-node.md)

Voor meer informatie over de manieren waarop u IoT-oplossingen kunt bouwen en implementeren met Azure IoT, gaat u naar:

* [Basisprincipes: Azure IoT-technologieën en -oplossingen](../iot-fundamentals/iot-services-and-technologies.md).