---
title: Overzicht van Azure IoT Hub X. 509 CA-beveiliging | Microsoft Docs
description: 'Overzicht: apparaten verifiëren voor het IoT Hub met behulp van X. 509-certificerings instanties.'
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 455f1f3e1c8181646fbe165d0f1aea6a2fb2fb13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92150708"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Apparaatverificatie met behulp van X. 509 CA-certificaten

In dit artikel wordt beschreven hoe u een X. 509 certificerings instantie (CA)-certificaten gebruikt om apparaten te verifiëren die IoT Hub verbinden.  In dit artikel leert u het volgende:

* Een X. 509 CA-certificaat ophalen
* Het CA-certificaat voor X. 509 registreren bij IoT Hub
* Apparaten ondertekenen met X. 509 CA-certificaten
* De verificatie van apparaten die zijn ondertekend met X. 509 CA

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>Overzicht

Met de functie X. 509 CA kan de verificatie van het apparaat worden IoT Hub met behulp van een certificerings instantie (CA). Het vereenvoudigt het initiële inschrijvings proces voor apparaten en de logistiek van de toeleverings keten tijdens de fabricage van het apparaat. [Meer informatie vindt u in dit scenario artikel over de waarde van het gebruik van X. 509 CA-certificaten](iot-hub-x509ca-concept.md) voor verificatie van apparaten.  U wordt aangeraden dit scenario artikel te lezen voordat u verdergaat, omdat u kunt nagaan waarom de volgende stappen zijn uitgevoerd.

## <a name="prerequisite"></a>Vereiste

Als u de functie X. 509 CA gebruikt, moet u een IoT Hub-account hebben.  [Meer informatie over hoe u een IOT hub-exemplaar maakt](quickstart-send-telemetry-dotnet.md) als u er nog geen hebt.

## <a name="how-to-get-an-x509-ca-certificate"></a>Een X. 509 CA-certificaat ophalen

Het X. 509 CA-certificaat bevindt zich bovenaan de keten van certificaten voor elk van uw apparaten.  U kunt er een kopen of maken, afhankelijk van hoe u deze wilt gebruiken.

Voor productie omgeving wordt u aangeraden een X. 509 CA-certificaat te kopen bij een open bare basis certificerings instantie. Het kopen van een CA-certificaat heeft het voor deel van de basis-CA die fungeert als een vertrouwde derde partij om te kunnen betalen voor de rechtmatigheid van uw apparaten. Overweeg deze optie als u wilt dat uw apparaten deel uitmaken van een open IoT-netwerk waar ze naar verwachting kunnen communiceren met producten of services van derden.

U kunt ook een zelfondertekende X. 509-certificerings instantie maken voor experimenten of voor gebruik in gesloten IoT-netwerken.

Ongeacht hoe u uw X. 509 CA-certificaat aanschaft, moet u ervoor zorgen dat het geheim van de persoonlijke sleutel wordt bewaard en beveiligd blijft.  Dit is nodig om vertrouwens relaties te maken in de X. 509 CA-verificatie.

Meer informatie over het [maken van een ZELFONDERTEKEND CA-certificaat](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md), dat u kunt gebruiken voor experimenten tijdens deze functie beschrijving.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Apparaten ondertekenen in de certificaat vertrouwens keten

De eigenaar van een X. 509 CA-certificaat kan cryptografisch een tussenliggende CA ondertekenen die op zijn beurt een andere tussenliggende CA kan ondertekenen, enzovoort, totdat de laatste tussenliggende CA dit proces beëindigt door een apparaat te ondertekenen. Het resultaat is een trapsgewijs getrapte keten van certificaten, ook wel een certificaat vertrouwens keten genoemd. In Real-Life is dit afspeelt als overdracht van vertrouwens relatie met het ondertekenen van apparaten. Deze delegering is belang rijk omdat hiermee een cryptografische variabele keten wordt gemaakt en het delen van de handtekening sleutels wordt voor komen.

![img-algemeen-CERT-keten-van-vertrouwen](./media/generic-cert-chain-of-trust.png)

Het certificaat van het apparaat (ook wel een blad certificaat genoemd) moet de *object naam* hebben ingesteld op de **apparaat-id** ( `CN=deviceId` ) die is gebruikt bij het registreren van het IOT-apparaat in de Azure IOT hub. Deze instelling is vereist voor verificatie.

Hier vindt u informatie over het [maken van een certificaat keten](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) als gereed voor het ondertekenen van apparaten.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Het CA-certificaat voor X. 509 registreren bij IoT Hub

Registreer uw X. 509 CA-certificaat om te IoT Hub waar het wordt gebruikt voor de verificatie van uw apparaten tijdens de registratie en de verbinding.  Het registreren van het X. 509-CA-certificaat is een proces dat uit twee stappen bestaat en dat het certificaat bestand uploadt en bewijs van bezit is.

Het upload proces omvat het uploaden van een bestand dat uw certificaat bevat.  Dit bestand mag nooit persoonlijke sleutels bevatten.

De stap bewijs van de verwerkings fase omvat een cryptografisch vraag-en reactie proces tussen u en IoT Hub.  Als de inhoud van een digitaal certificaat openbaar is en daarom vatbaar is voor inbreuk, moet IoT Hub controleren of u echt eigenaar bent van het CA-certificaat.  Dit doet u door een wille keurige uitdaging te genereren die u moet ondertekenen met de bijbehorende persoonlijke sleutel van het CA-certificaat.  Als u het geheim van de persoonlijke sleutel hebt behouden en u hebt beveiligd zoals eerder aanbevolen, hebt u alleen de kennis over het uitvoeren van deze stap. Geheim houding van persoonlijke sleutels is de bron van vertrouwen in deze methode.  Nadat u de uitdaging hebt ondertekend, moet u deze stap volt ooien door een bestand met de resultaten te uploaden.

Meer informatie over hoe u [uw CA-certificaat kunt registreren](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)

## <a name="how-to-create-a-device-on-iot-hub"></a>Een apparaat maken op IoT Hub

Als u wilt voor komen dat apparaten worden geïmiteerd, IoT Hub moet u ervoor zorgen dat u weet welke apparaten u kunt verwachten.  Hiervoor maakt u een apparaat in het apparaat-REGI ster van de IoT Hub.  Dit proces wordt geautomatiseerd wanneer IoT Hub [Device Provisioning Service](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/)wordt gebruikt. 

Hier vindt u informatie over het [hand matig maken van een apparaat in IOT hub](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub).

Een X. 509-apparaat maken voor uw IoT-hub

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Apparaten verifiëren die zijn ondertekend met X. 509 CA-certificaten

Als X. 509 CA-certificaat is geregistreerd en apparaten die zijn aangemeld bij een certificaat keten van vertrouwen, wordt de verificatie van het apparaat gehandhaafd wanneer het apparaat verbinding maakt, zelfs voor de eerste keer.  Wanneer een X. 509 CA-ondertekend apparaat verbinding maakt, wordt de certificaat keten geüpload voor validatie. De keten bevat alle tussenliggende CA-en apparaat certificaten.  Met deze informatie wordt het apparaat in een proces met twee stappen IoT Hub geverifieerd.  IoT Hub cryptografische validatie van de certificaat keten voor interne consistentie en vervolgens een bewijs van een gebezite uitdaging voor het apparaat.  IoT Hub declareert het apparaat authentiek op basis van een geslaagde reactie van het apparaat.  Deze verklaring gaat ervan uit dat de persoonlijke sleutel van het apparaat is beveiligd en dat alleen het apparaat kan reageren op deze uitdaging.  U wordt aangeraden beveiligde chips als HSM (hardware Secure modules) te gebruiken in apparaten om persoonlijke sleutels te beveiligen.

Een geslaagde apparaat-verbinding met IoT Hub het verificatie proces is voltooid en is ook de juiste installatie.

Hier vindt u informatie over het [volt ooien van deze stap voor de verbinding](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)met het apparaat.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [de waarde van X. 509 ca-verificatie](iot-hub-x509ca-concept.md) in IOT.

Aan de slag met IoT Hub [Device Provisioning Service](../iot-dps/index.yml).