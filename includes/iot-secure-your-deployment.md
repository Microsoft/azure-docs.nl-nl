---
title: bestand opnemen
description: bestand opnemen
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 08cca67455df4b2d28bba0a7410fccc11446fcdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010692"
---
Dit artikel bevat het volgende detail niveau voor het beveiligen van de IoT-infra structuur (Azure IoT-based Internet of Things). Het bevat koppelingen naar details over het implementatie niveau voor het configureren en implementeren van elk onderdeel. Het biedt ook vergelijkingen en keuzes tussen verschillende concurrerende methoden.

Het beveiligen van de Azure IoT-implementatie kan worden onderverdeeld in de volgende drie beveiligings gebieden:

* **Beveiliging van apparaten**: het IOT-apparaat wordt beveiligd terwijl het in de natuur wordt geïmplementeerd.

* **Verbindings beveiliging**: ervoor zorgen dat alle gegevens die worden verzonden tussen het IOT-apparaat en het IOT Hub vertrouwelijk zijn en een onrecht matige proef.

* **Cloud beveiliging**: biedt een manier om gegevens te beveiligen terwijl deze door lopen, en wordt opgeslagen in de Cloud.

![Drie beveiligings gebieden](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Inrichting en verificatie van het apparaat beveiligen

De IoT-oplossings Accelerators beveiligen IoT-apparaten met behulp van de volgende twee methoden:

* Door een unieke identiteits sleutel (beveiligings tokens) op te geven voor elk apparaat, dat door het apparaat kan worden gebruikt om te communiceren met de IoT Hub.

* Door gebruik te maken van een op apparaat [X. 509-certificaat](https://www.itu.int/rec/T-REC-X.509-201210-S) en een persoonlijke sleutel om het apparaat te verifiëren bij de IOT hub. Deze verificatie methode zorgt ervoor dat de persoonlijke sleutel op het apparaat op elk gewenst moment niet bekend is op het apparaat, waardoor er een hoger beveiligings niveau is.

De beveiligings token methode biedt verificatie voor elke aanroep van het apparaat naar IoT Hub door de symmetrische sleutel aan elke aanroep te koppelen. Met X. 509-verificatie is verificatie van een IoT-apparaat op de fysieke laag mogelijk als onderdeel van de TLS-verbinding tot stand brengen. De methode op basis van het beveiligings token kan worden gebruikt zonder de X. 509-verificatie, een minder veilig patroon. De keuze tussen de twee methoden wordt voornamelijk bepaald door de manier waarop de verificatie van het apparaat beveiligd moet zijn, en beschik baarheid van beveiligde opslag op het apparaat (om de persoonlijke sleutel veilig op te slaan).

## <a name="iot-hub-security-tokens"></a>IoT Hub beveiligings tokens

IoT Hub maakt gebruik van beveiligings tokens om apparaten en services te verifiëren om te voor komen dat sleutels in het netwerk worden verzonden. Daarnaast worden beveiligings tokens beperkt in de geldigheid van de tijd en het bereik. Azure IoT Sdk's genereren automatisch tokens zonder dat hiervoor speciale configuratie is vereist. In sommige gevallen moet de gebruiker echter de beveiligings tokens rechtstreeks genereren en gebruiken. Deze scenario's zijn onder andere het directe gebruik van de MQTT-, AMQP-of HTTP-Opper vlakken, of de implementatie van het token service-patroon.

Meer informatie over de structuur van het beveiligings token en het gebruik ervan vindt u in de volgende artikelen:

* [Structuur van beveiligings token](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [SAS-tokens gebruiken als apparaat](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Elk IoT Hub heeft een [identiteits register](../articles/iot-hub/iot-hub-devguide-identity-registry.md) dat kan worden gebruikt voor het maken van resources per apparaat in de service, zoals een wachtrij met in-Flight Cloud-naar-apparaat-berichten, en om toegang tot de op het apparaat gerichte eind punten toe te staan. Het IoT Hub identiteits register biedt beveiligde opslag van apparaat-id's en beveiligings sleutels voor een oplossing. Individuele identiteiten of groepen met apparaat-id's kunnen worden toegevoegd aan een lijst met toegestane apparaten of aan een blokkerings lijst, waardoor de toegang tot het apparaat volledig kan worden beheerd. De volgende artikelen bevatten meer informatie over de structuur van het identiteits register en de ondersteunde bewerkingen.

[IOT hub ondersteunt protocollen zoals MQTT, AMQP en http](../articles//iot-hub/iot-hub-devguide-security.md). Elk van deze protocollen gebruikt beveiligings tokens van het IoT-apparaat om een andere IoT Hub:

* AMQP: SASL PLAIN en AMQP op claims gebaseerde beveiliging ( `{policyName}@sas.root.{iothubName}` met tokens van IOT-hub, `{deviceId}` met tokens van het bereik van het apparaat).

* MQTT: CONNECT-pakket gebruikt `{deviceId}` als de `{ClientId}` , `{IoThubhostname}/{deviceId}` in het veld **username** en een SAS-token in het veld **wacht woord** .

* HTTP: er bevindt zich een geldig token in de header van de autorisatie aanvraag.

IoT Hub identiteits register kan worden gebruikt voor het configureren van beveiligings referenties per apparaat en toegangs beheer. Als een IoT-oplossing al een aanzienlijke investering in een [aangepast REGI ster voor apparaat-id en/of verificatie schema](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication)heeft, kan deze echter worden geïntegreerd in een bestaande infra structuur met IOT hub door een token service te maken.

### <a name="x509-certificate-based-device-authentication"></a>X. 509-verificatie op basis van een certificaat

Het gebruik van een [op apparaten gebaseerd X. 509-certificaat](../articles/iot-hub/iot-hub-devguide-security.md) en het bijbehorende persoonlijke en open bare sleutel paar maakt extra verificatie op de fysieke laag mogelijk. De persoonlijke sleutel wordt veilig opgeslagen in het apparaat en kan niet worden gedetecteerd buiten het apparaat. Het X. 509-certificaat bevat informatie over het apparaat, zoals de apparaat-ID en andere details van de organisatie. Er wordt een hand tekening van het certificaat gegenereerd met behulp van de persoonlijke sleutel.

Inrichtings stroom op hoog niveau:

* Koppel een id aan een fysiek apparaat: apparaat-id en/of X. 509-certificaat dat is gekoppeld aan het apparaat tijdens de fabricage van het apparaat of de provisie.

* Maak een corresponderende id-vermelding in IoT Hub: apparaat-id en bijbehorende apparaatgegevens in het IoT Hub identiteits register.

* U kunt de vinger afdruk van X. 509-certificaat veilig opslaan in het REGI ster van IoT Hub identiteit.

### <a name="root-certificate-on-device"></a>Basis certificaat op het apparaat

Tijdens het tot stand brengen van een beveiligde TLS-verbinding met IoT Hub, verifieert het IoT-apparaat IoT Hub met behulp van een basis certificaat dat deel uitmaakt van de SDK van het apparaat. Het certificaat bevindt zich in de map \\ c- \\ certificaten onder de hoofdmap van de opslag plaats voor de C client-SDK. Hoewel deze basis certificaten lang worden bewaard, kunnen ze nog steeds verlopen of worden ingetrokken. Als het certificaat op het apparaat niet kan worden bijgewerkt, is het mogelijk dat het apparaat niet meer kan worden verbonden met de IoT Hub (of een andere Cloud service). Een manier om het basis certificaat bij te werken wanneer het IoT-apparaat effectief wordt geïmplementeerd, vermindert dit risico.

## <a name="securing-the-connection"></a>De verbinding beveiligen

Internet verbinding tussen het IoT-apparaat en het IoT Hub wordt beveiligd met de standaard Transport Layer Security (TLS). Azure IoT ondersteunt [tls 1,2](https://tools.ietf.org/html/rfc5246), TLS 1,1 en TLS 1,0, in deze volg orde. Ondersteuning voor TLS 1,0 wordt alleen geboden voor compatibiliteit met eerdere versies. Controleer de [TLS-ondersteuning in IOT hub](../articles/iot-hub/iot-hub-tls-support.md) om te zien hoe u uw hub kunt configureren voor gebruik van TLS 1,2, omdat deze de meeste beveiliging biedt.

## <a name="securing-the-cloud"></a>De Cloud beveiligen

Azure IoT Hub maakt definitie van [beleid voor toegangs beheer](../articles/iot-hub/iot-hub-devguide-security.md) voor elke beveiligings sleutel mogelijk. Hierbij wordt gebruikgemaakt van de volgende set machtigingen om toegang te verlenen aan elk van de eind punten van de IoT Hub. Met behulp van bevoegdheden kunt u toegang tot een IoT Hub beperken op basis van de functionaliteit.

* **RegistryRead**. Hiermee wordt lees toegang verleend aan het identiteits register. Zie [identiteits register](../articles/iot-hub/iot-hub-devguide-identity-registry.md)voor meer informatie.

* **RegistryReadWrite**. Hiermee wordt lees-en schrijf toegang verleend aan het identiteits register. Zie [identiteits register](../articles/iot-hub/iot-hub-devguide-identity-registry.md)voor meer informatie.

* **ServiceConnect**. Verleent toegang tot Cloud service gerichte communicatie en bewakings eindpunten. Hiermee wordt bijvoorbeeld een machtiging verleend aan back-end-Cloud Services om apparaat-naar-Cloud-berichten te ontvangen, Cloud-naar-apparaat-berichten te verzenden en de bijbehorende bezorgings bevestigingen op te halen.

* **DeviceConnect**. Hiermee wordt toegang verleend aan aan het apparaat gerichte eind punten. Het geeft bijvoorbeeld toestemming om apparaat-naar-Cloud-berichten te verzenden en Cloud-naar-apparaat-berichten te ontvangen. Deze machtiging wordt gebruikt door apparaten.

Er zijn twee manieren om **DeviceConnect** -machtigingen te verkrijgen met IOT hub met [beveiligings tokens](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app): met behulp van een apparaat-id-sleutel of een gedeelde toegangs sleutel. Daarnaast is het belang rijk te weten dat alle functies die toegankelijk zijn vanaf apparaten, worden weer gegeven in het ontwerp van eind punten met voor voegsel `/devices/{deviceId}` .

[Service onderdelen kunnen alleen beveiligings tokens genereren](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) met behulp van het beleid voor gedeelde toegang, waarbij de juiste machtigingen worden verleend.

Azure IoT Hub en andere services die deel kunnen uitmaken van de oplossing, kunnen het beheer van gebruikers met behulp van de Azure Active Directory toestaan.

Gegevens die worden opgenomen door Azure IoT Hub kunnen worden gebruikt door diverse services, zoals Azure Stream Analytics en Azure Blob-opslag. Met deze services is toegang tot het beheer mogelijk. Meer informatie over deze services en beschik bare opties:

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): een schaal bare, volledig geïndexeerde database service voor semi-gestructureerde gegevens die meta gegevens beheert voor de apparaten die u voorziet, zoals kenmerken, configuratie en beveiligings eigenschappen. Azure Cosmos DB biedt verwerkings processen met hoge prestaties en hoge door Voer, neutraal indexering van gegevens en een geavanceerde SQL-query interface.

* [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/): realtime stroom verwerking in de Cloud waarmee u snel een goedkope analyse oplossing kunt ontwikkelen en implementeren om realtime inzichten te verkrijgen op basis van apparaten, Sens oren, infra structuur en toepassingen. De gegevens van deze volledig beheerde service kunnen naar elk volume worden geschaald, terwijl toch hoge door Voer, lage latentie en tolerantie wordt bereikt.

* [Azure-app Services](https://azure.microsoft.com/services/app-service/): een Cloud platform voor het bouwen van krachtige web-en mobiele apps die overal verbinding maken met gegevens. in de Cloud of on-premises. Bouw aantrekkelijke mobiele apps voor iOS, Android en Windows. Integreer met uw software als een service (SaaS) en zakelijke toepassingen met out-of-the-box-connectiviteit tot tien tallen Cloud Services en zakelijke toepassingen. Code in uw favoriete taal en IDE (.NET, Node.js, PHP, python of Java) om sneller dan ooit web-apps en Api's te bouwen.

* [Logic apps](https://azure.microsoft.com/services/app-service/logic/): de Logic apps functie van Azure app service helpt uw IOT-oplossing te integreren met uw bestaande line-of-Business-systemen en om werk stroom processen te automatiseren. Met Logic Apps kunnen ontwikkel aars werk stromen ontwerpen die beginnen met een trigger en vervolgens een reeks stappen uitvoeren: regels en acties die gebruikmaken van krachtige connectors om te integreren met uw bedrijfs processen. Logic Apps biedt kant-en-klare connectiviteit met een aanzienlijk ecosysteem van SaaS-, Cloud-en on-premises toepassingen.

* [Azure Blob-opslag](https://azure.microsoft.com/services/storage/): betrouw bare, voordelige Cloud opslag voor de gegevens die uw apparaten naar de Cloud verzenden.

## <a name="conclusion"></a>Conclusie

In dit artikel vindt u een overzicht van de details van het implementatie niveau voor het ontwerpen en implementeren van een IoT-infra structuur met Azure IoT. Het configureren van elk onderdeel dat moet worden beveiligd is essentieel bij het beveiligen van de algemene IoT-infra structuur. De ontwerp mogelijkheden die beschikbaar zijn in azure IoT bieden een zekere mate van flexibiliteit en keuze. elke keuze kan echter gevolgen hebben voor de beveiliging. Het is raadzaam om elk van deze opties te evalueren door middel van een risico/kosten beoordeling.
