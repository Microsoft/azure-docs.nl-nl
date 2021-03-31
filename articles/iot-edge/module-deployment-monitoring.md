---
title: Automatische implementatie voor apparaatgroepen-Azure IoT Edge | Microsoft Docs
description: Automatische implementaties in Azure IoT Edge gebruiken om groepen apparaten te beheren op basis van gedeelde labels
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3989ec4ca2b5c9d7385841604678791b20c1d102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489979"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Meer informatie over IoT Edge automatische implementaties voor afzonderlijke apparaten of op schaal

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Automatische implementaties en gelaagde implementaties helpen u bij het beheren en configureren van modules op grote aantallen IoT Edge apparaten.

Azure IoT Edge biedt twee manieren om de modules te configureren die op IoT Edge apparaten kunnen worden uitgevoerd. De eerste methode is om modules per apparaat te implementeren. U maakt een implementatie manifest en past dit vervolgens op naam toe op een bepaald apparaat. Bij de tweede methode worden modules automatisch geïmplementeerd op alle geregistreerde apparaten die voldoen aan een set gedefinieerde voor waarden. U maakt een implementatie manifest en definieert vervolgens op basis van [Tags](../iot-edge/how-to-deploy-at-scale.md#identify-devices-using-tags) op het apparaat dubbele.

Dit artikel richt zich op het configureren en bewaken van wagentjes van apparaten, gezamenlijk aangeduid als *IOT Edge automatische implementaties*. De eenvoudige implementaties tappen zijn als volgt:

1. Een operator definieert een implementatie die een set modules en de doel apparaten beschrijft. Elke implementatie heeft een implementatie manifest dat deze informatie weergeeft.
2. De IoT Hub-service communiceert met alle doel apparaten om ze te configureren met de gedeclareerde modules.
3. De IoT Hub-service haalt status op van de IoT Edge apparaten en maakt ze beschikbaar voor de operator.  Een operator kan bijvoorbeeld zien wanneer een edge-apparaat niet is geconfigureerd of dat een module tijdens runtime mislukt.
4. Op elk gewenst moment worden nieuwe IoT Edge-apparaten die voldoen aan de doel voorwaarden, geconfigureerd voor de implementatie.

In dit artikel wordt elk onderdeel beschreven dat betrokken is bij het configureren en bewaken van een implementatie. Zie [IOT Edge modules op schaal implementeren en bewaken](how-to-deploy-at-scale.md)voor een overzicht van het maken en bijwerken van een implementatie.

## <a name="deployment"></a>Implementatie

Met een IoT Edge automatische implementatie worden installatie kopieën van IoT Edge module toegewezen om als exemplaren uit te voeren op een doel groep van IoT Edge apparaten. Het werkt door een IoT Edge implementatie manifest te configureren om een lijst met modules op te geven met de bijbehorende initialisatie parameters. Een implementatie kan worden toegewezen aan één apparaat (op basis van apparaat-ID) of aan een groep apparaten (op basis van labels). Nadat een IoT Edge-apparaat een implementatie manifest heeft ontvangen, worden de container installatie kopieën uit de respectieve container opslagplaatsen gedownload en geïnstalleerd en worden ze dienovereenkomstig geconfigureerd. Zodra een implementatie is gemaakt, kan een operator de implementatie status controleren om te zien of de doel apparaten correct zijn geconfigureerd.

Alleen IoT Edge apparaten kunnen worden geconfigureerd met een implementatie. De volgende vereisten moeten op het apparaat staan voordat de implementatie kan worden ontvangen:

* Het basis besturingssysteem
* Een container beheersysteem, zoals Moby of docker
* Inrichting van de IoT Edge-runtime

### <a name="deployment-manifest"></a>Distributiemanifest

Een implementatie manifest is een JSON-document waarin de modules worden beschreven die moeten worden geconfigureerd op de beoogde IoT Edge apparaten. Het bevat de meta gegevens van de configuratie voor alle modules, met inbegrip van de vereiste systeem modules (met name de IoT Edge agent en IoT Edge hub).  

De meta gegevens van de configuratie van elke module zijn onder andere:

* Versie
* Type
* Status (bijvoorbeeld actief of gestopt)
* Beleid voor opnieuw starten
* Installatie kopie en container register
* Routes voor gegevens invoer en-uitvoer

Als de module installatie kopie wordt opgeslagen in een persoonlijk container register, bevat de IoT Edge agent de register referenties.

### <a name="target-condition"></a>Doel voorwaarde

De doel voorwaarde wordt continu geëvalueerd gedurende de levens duur van de implementatie. Nieuwe apparaten die aan de vereisten voldoen, worden opgenomen en alle bestaande apparaten die niet meer worden verwijderd. De implementatie wordt opnieuw geactiveerd als de service een wijziging in de doel voorwaarde detecteert.

U hebt bijvoorbeeld een implementatie met een doel voorwaarde label. Environment = ' Prod '. Wanneer u de implementatie hebt gestart, zijn er 10 productie apparaten. De modules zijn in deze 10 apparaten geïnstalleerd. In de status van de IoT Edge-agent worden 10 apparaten weer gegeven, 10 geslaagde reacties, 0 mislukte antwoorden en 0 Reacties in behandeling. Nu voegt u nog vijf apparaten toe met tags. Environment = ' Prod '. De service detecteert de wijziging en de status van de IoT Edge-agent wordt 15 totaal aantal apparaten, 10 geslaagde reacties, 0 mislukte antwoorden en vijf in behandeling zijnde antwoorden tijdens de implementatie van de vijf nieuwe apparaten.

Gebruik een Booleaanse voor waarde op dubbele Tags van het apparaat, dubbele gerapporteerde eigenschappen of deviceId om de doel apparaten te selecteren. Als u de voor waarde met tags wilt gebruiken, moet u ' Tags ' toevoegen: {} sectie op het apparaat dubbele onder hetzelfde niveau als eigenschappen. [Meer informatie over tags op het apparaat, dubbele](../iot-hub/iot-hub-devguide-device-twins.md)

Voor beelden van doel voorwaarden:

* deviceId = ' linuxprod1 '
* Tags. Environment = ' Prod '
* Tags. Environment = ' Prod ' en tags. Location = ' westelijke '
* Tags. Environment = ' Prod ' of tags. Location = ' westelijke '
* Tags. operator = ' John ' en tags. Environment = ' Prod ' en niet deviceId = ' linuxprod1 '
* Properties. gerapporteerd. devicemodel = ' 4000x '

Houd rekening met deze beperkingen wanneer u een doel voorwaarde bouwt:

* In Device-dubbele kunt u alleen een doel voorwaarde bouwen met behulp van tags, gerapporteerde eigenschappen of deviceId.
* Dubbele aanhalings tekens zijn niet toegestaan in een deel van de doel voorwaarde. Gebruik enkele aanhalings tekens.
* Enkele aanhalings tekens vertegenwoordigen de waarden van de doel voorwaarde. Daarom moet u de enkele aanhalings tekens met een andere enkele aanhalings tekens wegmaken als deze deel uitmaakt van de apparaatnaam. Bijvoorbeeld, om een apparaat met de naam `operator'sDevice` schrijven te richten `deviceId='operator''sDevice'` .
* Cijfers, letters en de volgende tekens zijn toegestaan in doel voorwaarde waarden: `-:.+%_#*?!(),=@;$` .

### <a name="priority"></a>Prioriteit

Een prioriteit geeft aan of een implementatie moet worden toegepast op een doel apparaat ten opzichte van andere implementaties. Een implementatie prioriteit is een positief geheel getal, met grotere getallen die een hogere prioriteit identificeren. Als een IoT Edge-apparaat is gericht op meer dan één implementatie, is de implementatie met de hoogste prioriteit van toepassing.  Implementaties met lagere prioriteiten worden niet toegepast en worden niet samengevoegd.  Als een apparaat is gericht op twee of meer implementaties met een gelijke prioriteit, is de meest recent gemaakte implementatie (bepaald door de tijds tempel voor maken) van toepassing.

### <a name="labels"></a>Labels

Labels zijn teken reeks sleutel-waardeparen die u kunt gebruiken voor het filteren en groeperen van implementaties. Een implementatie kan meerdere labels hebben. Labels zijn optioneel en zijn niet van invloed op de werkelijke configuratie van IoT Edge apparaten.

### <a name="metrics"></a>Metrische gegevens

Standaard worden alle implementaties gerapporteerd op basis van vier metrische gegevens:

* **Doel** geeft de IOT edge apparaten weer die overeenkomen met de voor waarde voor de implementatie doelstelling.
* **Toegepast** toont de doel IOT edge apparaten die niet zijn gericht op een andere implementatie van een hogere prioriteit.
* Bij **geslaagde rapportage** worden de IOT edge-apparaten weer gegeven die hebben gerapporteerd dat de modules zijn geïmplementeerd.
* **Rapportage fout** toont de IOT edge apparaten die hebben gerapporteerd dat een of meer modules niet zijn geïmplementeerd. Als u de fout nader wilt onderzoeken, maakt u extern verbinding met die apparaten en bekijkt u de logboek bestanden.

Daarnaast kunt u uw eigen aangepaste metrische gegevens definiëren om de implementatie te bewaken en beheren.

Metrische gegevens geven samen vattingen van de verschillende statussen die apparaten kunnen terugsturen als gevolg van het Toep assen van een implementatie configuratie. Met metrische gegevens kunt u een query uitvoeren op [dubbele gerapporteerde eigenschappen van edgeHub-module](module-edgeagent-edgehub.md#edgehub-reported-properties), zoals *lastDesiredStatus* of *lastConnectTime*. Bijvoorbeeld:

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

Het toevoegen van uw eigen metrische gegevens is optioneel en heeft geen invloed op de werkelijke configuratie van IoT Edge apparaten.

## <a name="layered-deployment"></a>Gelaagde implementatie

Gelaagde implementaties zijn automatische implementaties die samen kunnen worden gecombineerd om het aantal unieke implementaties te verminderen dat moet worden gemaakt. Gelaagde implementaties zijn handig in scenario's waarin dezelfde modules opnieuw worden gebruikt in verschillende combi Naties in veel automatische implementaties.

Gelaagde implementaties hebben dezelfde basis onderdelen als automatische implementatie. Ze zijn gericht op apparaten op basis van tags in de apparaatdubbels van het apparaat en bieden dezelfde functionaliteit voor labels, metrische gegevens en status rapportage. Aan gelaagde implementaties zijn ook prioriteiten toegewezen, maar in plaats van de prioriteit te gebruiken om te bepalen welke implementatie op een apparaat wordt toegepast, bepaalt de prioriteit hoe meerdere implementaties op een apparaat worden gerangschikt. Als twee gelaagde implementaties bijvoorbeeld een module of een route met dezelfde naam hebben, wordt de gelaagde implementatie met de hogere prioriteit toegepast terwijl de lagere prioriteit wordt overschreven.

De System runtime modules, edgeAgent en edgeHub, zijn niet geconfigureerd als onderdeel van een gelaagde implementatie. Op alle IoT Edge apparaten waarop een gelaagde implementatie is gericht, moet eerst een standaard automatische implementatie worden toegepast. De automatische implementatie biedt de basis waarop gelaagde implementaties kunnen worden toegevoegd.

Een IoT Edge apparaat kan één en slechts één standaard automatische implementatie Toep assen, maar kan meerdere gelaagde automatische implementaties Toep assen. Gelaagde implementaties die zijn gericht op een apparaat, moeten een hogere prioriteit hebben dan de automatische implementatie voor dat apparaat.

Denk bijvoorbeeld aan het volgende scenario van een bedrijf dat gebouwen beheert. Ze hebben IoT Edge modules ontwikkeld voor het verzamelen van gegevens uit beveiligings camera's, bewegings Sens oren en liften. Niet al hun gebouwen kunnen echter alle drie de modules gebruiken. Met standaard automatische implementaties moet het bedrijf afzonderlijke implementaties maken voor alle module combinaties die hun gebouwen nodig hebben.

![Standaard automatische implementaties moeten elke module combinatie bevatten](./media/module-deployment-monitoring/standard-deployment.png)

Zodra het bedrijf overschakelt naar gelaagde automatische implementaties, kunnen ze echter dezelfde module combinaties voor hun gebouwen maken met minder implementaties om te beheren. Elke module heeft een eigen gelaagde implementatie en de labels van apparaten bepalen welke modules aan elk gebouw worden toegevoegd.

![Gelaagde automatische implementaties vereenvoudigen scenario's waarbij dezelfde modules op verschillende manieren worden gecombineerd](./media/module-deployment-monitoring/layered-deployment.png)

### <a name="module-twin-configuration"></a>Dubbele configuratie van module

Wanneer u werkt met gelaagde implementaties, kunt u op een of andere manier twee implementaties met dezelfde module richten als een apparaat. In dergelijke gevallen kunt u beslissen of de implementatie met een hogere prioriteit de module moet overschrijven of toevoegen. U hebt bijvoorbeeld een implementatie met dezelfde module op 100 verschillende apparaten. 10 van die apparaten bevinden zich echter in beveiligde faciliteiten en hebben extra configuratie nodig om te communiceren via proxy servers. U kunt een gelaagde implementatie gebruiken om module dubbele eigenschappen toe te voegen die ervoor zorgen dat deze 10 apparaten veilig kunnen communiceren zonder de bestaande module dubbele informatie van de basis implementatie te overschrijven.

U kunt module dubbele gewenste eigenschappen toevoegen in het implementatie manifest. Wanneer u in een standaard implementatie eigenschappen zou toevoegen in de **Eigenschappen. gewenste** sectie van de module dubbele, in een gelaagde implementatie kunt u een nieuwe subset van gewenste eigenschappen declareren.

In een standaard implementatie kunt u bijvoorbeeld de gesimuleerde temperatuur sensor module toevoegen met de volgende gewenste eigenschappen die aangeven dat de gegevens binnen vijf seconden moeten worden verzonden:

```json
"SimulatedTemperatureSensor": {
  "properties.desired": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

In een gelaagde implementatie die gericht is op sommige of alle van dezelfde apparaten, kunt u een eigenschap toevoegen die aangeeft dat de gesimuleerde sensor 1000 berichten verzendt en vervolgens stopt. U wilt de bestaande eigenschappen niet overschrijven, dus u maakt een nieuwe sectie in de gewenste eigenschappen `layeredProperties` , die de nieuwe eigenschap bevat:

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "StopAfterCount": 1000
  }
}
```

Op een apparaat waarop zowel implementaties zijn toegepast, worden de volgende eigenschappen weer gegeven in de module twee voor de gesimuleerde temperatuur sensor:

```json
"properties": {
  "desired": {
    "SendData": true,
    "SendInterval": 5,
    "layeredProperties": {
      "StopAfterCount": 1000
    }
  }
}
```

Als u het `properties.desired` veld van de module dubbele in een gelaagde implementatie instelt, worden de gewenste eigenschappen voor die module in een lagere prioriteits implementatie overschreven.

## <a name="phased-rollout"></a>Gefaseerde implementatie

Een gefaseerde implementatie is een algemeen proces waarbij een operator wijzigingen implementeert in een uitbrei ding van IoT Edge apparaten. Het doel is om wijzigingen geleidelijk aan te brengen om het risico te verkleinen dat er grote veranderingen in de omvang ontstaan. Automatische implementaties helpen gefaseerde implementaties te beheren in een vloot van IoT Edge apparaten.

Een gefaseerde implementatie wordt uitgevoerd in de volgende fasen en stappen:

1. Stel een test omgeving van IoT Edge apparaten in door deze in te richten en een apparaat-dubbele tag in te stellen zoals `tag.environment='test'` . In de test omgeving moet de productie omgeving worden gespiegeld die de implementatie uiteindelijk gaat richten.
2. Maak een implementatie met inbegrip van de gewenste modules en configuraties. De doel voorwaarde moet gericht zijn op de test IoT Edge Device environment.
3. Valideer de configuratie van de nieuwe module in de test omgeving.
4. Werk de implementatie bij om een subset van productie IoT Edge apparaten op te nemen door een nieuwe tag toe te voegen aan de doel voorwaarde. Zorg er ook voor dat de prioriteit voor de implementatie hoger is dan andere implementaties die momenteel zijn gericht op die apparaten
5. Controleer of de implementatie is geslaagd op de doel IoT-apparaten door de implementatie status weer te geven.
6. Werk de implementatie bij om alle resterende productie IoT Edge-apparaten te richten.

## <a name="rollback"></a>Terugdraaiactie

Implementaties kunnen worden teruggedraaid als u fouten of onjuiste configuraties ontvangt. Omdat een implementatie de absolute module configuratie voor een IoT Edge apparaat definieert, moet een extra implementatie ook met een lagere prioriteit op hetzelfde apparaat worden gericht, zelfs als het doel is om alle modules te verwijderen.  

Als u een implementatie verwijdert, worden de modules niet verwijderd van de doel apparaten. Er moet een andere implementatie zijn die een nieuwe configuratie voor de apparaten definieert, zelfs als het een lege implementatie is.

Terugdraai acties uitvoeren in de volgende volg orde:

1. Controleer of een tweede implementatie ook gericht is op dezelfde apparaatset. Als het doel van het terugdraaien alle modules verwijdert, mag de tweede implementatie geen enkele module bevatten.
2. Wijzig of verwijder de doel voorwaarde-expressie van de implementatie die u wilt terugdraaien, zodat de apparaten niet meer voldoen aan de doel voorwaarde.
3. Controleer of het terugdraaien is geslaagd door de implementatie status weer te geven.
   * De gerollte-back-implementatie mag niet langer de status weer geven voor de apparaten die zijn teruggedraaid.
   * De tweede implementatie moet nu een implementatie status bevatten voor de apparaten die zijn teruggedraaid.

## <a name="next-steps"></a>Volgende stappen

* Door loop de stappen om een implementatie te maken, bij te werken of te verwijderen in [IOT Edge modules implementeren en bewaken op schaal](how-to-deploy-at-scale.md).
* Meer informatie over andere IoT Edge concepten, zoals de modules [IOT Edge runtime](iot-edge-runtime.md) en [IOT Edge](iot-edge-modules.md).
