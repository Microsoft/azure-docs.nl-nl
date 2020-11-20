---
title: Informatie over het versie beheer van de app voor uw Azure IoT Central-apps | Microsoft Docs
description: Herhaal uw Apparaatinstellingen door nieuwe versies te maken en zonder uw live verbonden apparaten te beïnvloeden
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 93545c63013c95e3db498b079061da3d9b189efd
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94986441"
---
# <a name="create-a-new-device-template-version"></a>Een nieuwe sjabloon versie voor een apparaat maken

*Dit artikel is van toepassing op oplossingenbouwers en apparaatontwikkelaars.*

Een sjabloon voor een apparaat bevat een schema dat beschrijft hoe een apparaat samenwerkt met IoT Central. Deze interacties zijn onder andere telemetrie, eigenschappen en opdrachten. Zowel het apparaat als de IoT Central-toepassing zijn afhankelijk van een gemeen schappelijke uitleg van dit schema om informatie uit te wisselen. U kunt alleen beperkte wijzigingen aanbrengen in het schema zonder het contract te verbreken. Daarom is het belang rijk dat de meeste schema wijzigingen een nieuwe versie van de sjabloon voor het apparaat vereisen. Door de versie van de sjabloon te bepalen, kunnen oudere apparaten door gaan met de schema versie die ze begrijpen, terwijl nieuwere of bijgewerkte apparaten een latere schema versie gebruiken.

Het schema in een apparaatprofiel wordt gedefinieerd in het model van het apparaat en de bijbehorende interfaces. Device-sjablonen bevatten andere informatie, zoals Cloud eigenschappen, weer gave-aanpassingen en weer gaven. Als u wijzigingen aanbrengt in de onderdelen van de sjabloon die niet bepalen hoe het apparaat gegevens uitwisselt met IoT Central, hoeft u geen versie van de sjabloon te maken.

U moet altijd een bijgewerkte apparaatprofiel publiceren voordat een operator deze kan gebruiken. IoT Central stopt met het publiceren van belang rijke wijzigingen in een apparaatprofiel zonder de sjabloon eerst te hoeven indelen.

> [!NOTE]
> Zie [een sjabloon instellen en beheren](howto-set-up-template.md) voor meer informatie over het maken van een sjabloon voor een apparaat.

## <a name="versioning-rules"></a>Versie beheer regels

In deze sectie vindt u een overzicht van de versie beheer regels die van toepassing zijn op Apparaatinstellingen. De modellen en interfaces van het apparaat hebben versie nummers. Het volgende code fragment toont het model voor een thermo staat-apparaat. Het model van het apparaat heeft één interface. Aan het einde van het veld ziet u het versie nummer `@id` .

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    // ...
  ]
}
```

Als u deze informatie wilt weer geven in de IoT Central-gebruikers interface, selecteert u **identiteit weer geven** in de editor voor Apparaatbeheer:

:::image type="content" source="media/howto-version-device-template/view-identity.png" alt-text="De identiteit van een interface weer geven om het versie nummer te bekijken":::

De volgende lijst bevat de regels die bepalen wanneer u een nieuwe versie moet maken:

* Nadat een model is gepubliceerd, kunt u geen interfaces verwijderen, zelfs niet in een nieuwe versie van de sjabloon.
* Nadat een model is gepubliceerd, kunt u een interface toevoegen als u een nieuwe versie van de sjabloon voor het apparaat maakt.
* Nadat een model is gepubliceerd, kunt u een interface met een nieuwere versie vervangen als u een nieuwe versie van de sjabloon voor het apparaat maakt. Als de sensor v1-Device-sjabloon bijvoorbeeld gebruikmaakt van de Thermo staat v1-interface, kunt u een sensor v2-apparaatprofiel maken die gebruikmaakt van de Thermo staat v2-interface.
* Nadat een interface is gepubliceerd, kunt u de inhoud van de interface niet meer verwijderen, zelfs in een nieuwe versie van de sjabloon.
* Nadat een interface is gepubliceerd, kunt u items toevoegen aan de inhoud van een interface als u een nieuwe versie van de interface en de apparaatprofiel maakt. Items die u aan de interface kunt toevoegen, zijn onder andere telemetrie, eigenschappen en opdrachten.
* Nadat een interface is gepubliceerd, kunt u niet-schema wijzigingen aanbrengen in bestaande items in de interface als u een nieuwe versie van de interface en de apparaatprofiel maakt. Niet-schema onderdelen van een interface-item bevatten de weergave naam en het semantische type. De schema onderdelen van een interface-item dat u niet kunt wijzigen, zijn naam, type capaciteit en schema.

In de volgende secties worden enkele voor beelden gegeven van het wijzigen van apparaatinstellingen in IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>De sjabloon voor het apparaat aanpassen zonder versie beheer

Bepaalde elementen van de mogelijkheden van uw apparaat kunnen worden bewerkt zonder dat ze de sjabloon en interfaces van uw apparaat hoeven te maken. Enkele van deze velden bevatten bijvoorbeeld weergave naam, semantisch type, minimum waarde, maximum waarde, decimaal posities, kleur, eenheid, weergave-eenheid, opmerking en beschrijving. Een van deze aanpassingen toevoegen:

1. Ga naar de pagina met **Apparaatinstellingen** .
1. Selecteer de sjabloon die u wilt aanpassen.
1. Kies het tabblad **aanpassen** .
1. Alle mogelijkheden die zijn gedefinieerd in het model van uw apparaat, worden hier weer gegeven. U kunt al deze velden bewerken, opslaan en gebruiken zonder de nood zaak van de sjabloon voor uw apparaat. Als er velden zijn die u wilt bewerken die alleen-lezen zijn, moet u een versie van uw apparaatprofiel maken om deze te wijzigen. Selecteer een veld dat u wilt bewerken en voer een nieuwe waarde in.
1. Selecteer **Opslaan**. Deze waarden overschrijven nu alles dat in de sjabloon voor het eerst is opgeslagen en die in de toepassing worden gebruikt.

## <a name="version-a-device-template"></a>Een sjabloon voor een apparaat versie

Als u een nieuwe versie van uw apparaatprofiel maakt, maakt u een concept versie van de sjabloon waarin het model van het apparaat kan worden bewerkt. Alle gepubliceerde interfaces blijven gepubliceerd totdat ze afzonderlijk zijn geversied. Als u een gepubliceerde interface wilt wijzigen, maakt u eerst een nieuwe sjabloon versie voor een apparaat.

Alleen de sjabloon van het apparaat wordt gebruikt wanneer u probeert een deel van het model te bewerken dat u niet kunt bewerken in de sectie aanpassingen.

Een sjabloon voor een apparaat versie:

1. Ga naar de pagina met **Apparaatinstellingen** .
1. Selecteer de sjabloon voor het apparaat dat u wilt versieren.
1. Selecteer de knop **versie** boven aan de pagina en geef de sjabloon een nieuwe naam. IoT Central stelt een nieuwe naam voor die u kunt bewerken.
1. Selecteer **Maken**.
1. Uw sjabloon voor het apparaat bevindt zich nu in de concept modus. U kunt zien dat uw interfaces nog steeds zijn vergrendeld. De versie van de interfaces die u wilt wijzigen.

## <a name="version-an-interface"></a>Een interface versie

Met versie beheer van een interface kunt u de mogelijkheden in de interface die u al hebt gemaakt, toevoegen, bijwerken en verwijderen.

Een interface versie:

1. Ga naar de pagina met **Apparaatinstellingen** .
1. Selecteer de Device-sjabloon die u in een concept modus hebt.
1. Selecteer de interface in de gepubliceerde modus die u wilt versie en bewerken.
1. Selecteer de knop **versie** boven aan de pagina interface.
1. Selecteer **Maken**.
1. Uw interface bevindt zich nu in de concept modus. U kunt mogelijkheden aan uw interface toevoegen of deze bewerken zonder bestaande aanpassingen en weer gaven te verbreken.

## <a name="migrate-a-device-across-versions"></a>Een apparaat migreren over versies

U kunt meerdere versies van de sjabloon voor het apparaat maken. In de loop van de tijd hebt u meerdere apparaten die zijn verbonden met behulp van deze Apparaatinstellingen. U kunt apparaten van de ene versie van uw apparaatprofiel naar de andere migreren. In de volgende stappen wordt beschreven hoe u een apparaat migreert:

1. Ga naar de pagina **Apparaten**.
1. Selecteer het apparaat dat u naar een andere versie wilt migreren.
1. Kies **migreren**:  :::image type="content" source="media/howto-version-device-template/migrate-device.png" alt-text="Kies de optie om te beginnen met het migreren van een apparaat":::
1. Selecteer de sjabloon van het-apparaat met het versie nummer waarnaar u het apparaat wilt migreren en selecteer vervolgens **migreren**.

## <a name="next-steps"></a>Volgende stappen

Als u een operator of oplossings bouwer bent, is een voorgestelde volgende stap het [beheren van uw apparaten](./howto-manage-devices.md).

Als u een ontwikkelaar van apparaten bent, is een voorgestelde volgende stap meer informatie over [Azure IOT edge apparaten en Azure IOT Central](./concepts-iot-edge.md).
