---
title: Meer informatie over het versien van apparaatsjabloonversies voor uw Azure IoT Central-apps | Microsoft Docs
description: Uw apparaatsjablonen aanpassen door nieuwe versies te maken zonder dat dit van invloed is op uw live verbonden apparaten
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 03d4b0e43c9f692b90f4ab5d4d136dac92b74de6
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715042"
---
# <a name="create-a-new-device-template-version"></a>Een nieuwe versie van een apparaatsjabloon maken

*Dit artikel is van toepassing op oplossingenbouwers en apparaatontwikkelaars.*

Een apparaatsjabloon bevat een schema dat beschrijft hoe een apparaat communiceert met IoT Central. Deze interacties omvatten telemetrie, eigenschappen en opdrachten. Zowel het apparaat als de toepassing IoT Central vertrouwen op een gedeeld begrip van dit schema om informatie uit te wisselen. U kunt alleen beperkte wijzigingen aanbrengen in het schema zonder het contract te verbreken. Daarom is voor de meeste schemawijzigingen een nieuwe versie van de apparaatsjabloon vereist. Met versiering van de apparaatsjabloon kunnen oudere apparaten doorgaan met de schemaversie die ze begrijpen, terwijl nieuwere of bijgewerkte apparaten een nieuwere schemaversie gebruiken.

Het schema in een apparaatsjabloon wordt gedefinieerd in het apparaatmodel en de interfaces ervan. Apparaatsjablonen bevatten andere informatie, zoals cloudeigenschappen, weergaveaanpassingen en weergaven. Als u de onderdelen van de apparaatsjabloon wijzigt die niet bepalen hoe het apparaat gegevens uitwisselt met IoT Central, hoeft u geen versie van de sjabloon te wijzigen.

U moet altijd een bijgewerkte apparaatsjabloon publiceren voordat een operator deze kan gebruiken. IoT Central voorkomt dat u belangrijke wijzigingen publiceert in een apparaatsjabloon zonder eerst de sjabloon te versiereren.

> [!NOTE]
> Zie Een apparaatsjabloon instellen en beheren voor meer informatie over het maken van [een apparaatsjabloon](howto-set-up-template.md)

## <a name="versioning-rules"></a>Versieregels

Deze sectie bevat een overzicht van de versieregels die van toepassing zijn op apparaatsjablonen. Zowel apparaatmodellen als interfaces hebben versienummers. In het volgende codefragment ziet u het apparaatmodel voor een thermostaatapparaat. Het apparaatmodel heeft één interface. U ziet het versienummer aan het einde van het `@id` veld.

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

Als u deze informatie wilt weergeven in IoT Central gebruikersinterface, selecteert **u Identiteit weergeven** in de apparaatsjablooneditor:

:::image type="content" source="media/howto-version-device-template/view-identity.png" alt-text="De identiteit van een interface weergeven om het versienummer te zien":::

De volgende lijst bevat de regels die bepalen wanneer u een nieuwe versie moet maken:

* Nadat een apparaatmodel is gepubliceerd, kunt u geen interfaces meer verwijderen, zelfs niet in een nieuwe versie van de apparaatsjabloon.
* Nadat een apparaatmodel is gepubliceerd, kunt u een interface toevoegen als u een nieuwe versie van de apparaatsjabloon maakt.
* Nadat een apparaatmodel is gepubliceerd, kunt u een interface vervangen door een nieuwere versie als u een nieuwe versie van de apparaatsjabloon maakt. Als de apparaatsjabloon sensor v1 bijvoorbeeld gebruikmaakt van de interface thermostat v1, kunt u een apparaatsjabloon voor sensor v2 maken die gebruikmaakt van de thermostaat v2-interface.
* Nadat een interface is gepubliceerd, kunt u de inhoud van de interface niet verwijderen, zelfs niet in een nieuwe versie van de apparaatsjabloon.
* Nadat een interface is gepubliceerd, kunt u items toevoegen aan de inhoud van een interface als u een nieuwe versie van de interface en apparaatsjabloon maakt. Items die u aan de interface kunt toevoegen, zijn telemetrie, eigenschappen en opdrachten.
* Nadat een interface is gepubliceerd, kunt u geen schemawijzigingen aanbrengen in bestaande items in de interface als u een nieuwe versie van de interface en apparaatsjabloon maakt. Niet-schemaonderdelen van een interface-item bevatten de weergavenaam en het semantische type. De schemaonderdelen van een interface-item die u niet kunt wijzigen, zijn naam, mogelijkheidstype en schema.

In de volgende secties vindt u enkele voorbeelden van het wijzigen van apparaatsjablonen in IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>De apparaatsjabloon aanpassen zonder versie-instellingen

Bepaalde elementen van de mogelijkheden van uw apparaat kunnen worden bewerkt zonder dat u versie-versie van uw apparaatsjabloon en interfaces hoeft te gebruiken. Sommige van deze velden omvatten bijvoorbeeld weergavenaam, semantisch type, minimumwaarde, maximumwaarde, decimale waarden, kleur, eenheid, weergave-eenheid, opmerking en beschrijving. Een van deze aanpassingen toevoegen:

1. Ga naar de **pagina Apparaatsjablonen.**
1. Selecteer de apparaatsjabloon die u wilt aanpassen.
1. Kies het **tabblad** Aanpassen.
1. Alle mogelijkheden die in uw apparaatmodel zijn gedefinieerd, worden hier vermeld. U kunt al deze velden bewerken, opslaan en gebruiken zonder dat u de versie van uw apparaatsjabloon hoeft te wijzigen. Als er velden zijn die u wilt bewerken en alleen-lezen zijn, moet u de versie van uw apparaatsjabloon wijzigen. Selecteer een veld dat u wilt bewerken en voer nieuwe waarden in.
1. Selecteer **Opslaan**. Deze waarden overschrijven nu alles wat in eerste instantie is opgeslagen in uw apparaatsjabloon en worden gebruikt in de toepassing.

## <a name="version-a-device-template"></a>Versie van een apparaatsjabloon

Als u een nieuwe versie van uw apparaatsjabloon maakt, wordt er een conceptversie van de sjabloon gemaakt waarin het apparaatmodel kan worden bewerkt. Gepubliceerde interfaces blijven gepubliceerd totdat ze afzonderlijk worden gepubliceerd. Als u een gepubliceerde interface wilt wijzigen, maakt u eerst een nieuwe versie van de apparaatsjabloon.

Pas versie van de apparaatsjabloon aan wanneer u probeert een deel van het apparaatmodel te bewerken dat u niet kunt bewerken in de sectie Aanpassingen.

Een versie van een apparaatsjabloon wijzigen:

1. Ga naar de **pagina Apparaatsjablonen.**
1. Selecteer de apparaatsjabloon die u wilt versien.
1. Selecteer de **knop** Versie bovenaan de pagina en geef de sjabloon een nieuwe naam. IoT Central stelt een nieuwe naam voor die u kunt bewerken.
1. Selecteer **Maken**.
1. Uw apparaatsjabloon is nu in de conceptmodus. U kunt zien dat uw interfaces nog steeds zijn vergrendeld. Versie van de interfaces die u wilt wijzigen.

## <a name="version-an-interface"></a>Versie van een interface

Met versie-versies van een interface kunt u mogelijkheden toevoegen en bijwerken binnen de interface die u al hebt gemaakt.

Een interface versien:

1. Ga naar de **pagina Apparaatsjablonen.**
1. Selecteer de apparaatsjabloon die u in de conceptmodus hebt.
1. Selecteer de interface in de gepubliceerde modus die u wilt versien en bewerken.
1. Selecteer de **knop** Versie bovenaan de interfacepagina.
1. Selecteer **Maken**.
1. Uw interface is nu in de conceptmodus. U kunt mogelijkheden aan uw interface toevoegen of bewerken zonder bestaande aanpassingen en weergaven te verbreken.

## <a name="migrate-a-device-across-versions"></a>Een apparaat migreren tussen versies

U kunt meerdere versies van de apparaatsjabloon maken. Na een periode hebt u meerdere verbonden apparaten met behulp van deze apparaatsjablonen. U kunt apparaten migreren van de ene versie van uw apparaatsjabloon naar een andere. In de volgende stappen wordt beschreven hoe u een apparaat migreert:

1. Ga naar de pagina **Apparaten**.
1. Selecteer het apparaat dat u naar een andere versie wilt migreren.
1. Kies **Migreren:**  :::image type="content" source="media/howto-version-device-template/migrate-device.png" alt-text="kies de optie om te beginnen met het migreren van een apparaat":::
1. Selecteer de apparaatsjabloon met het versienummer waar u het apparaat naar wilt migreren en selecteer **Migreren.**

## <a name="next-steps"></a>Volgende stappen

Als u een operator of bouwer van oplossingen bent, is een voorgestelde volgende stap om te leren [hoe u uw apparaten beheert.](./howto-manage-devices.md)

Als u een apparaatontwikkelaar bent, is een voorgestelde volgende stap het lezen van Azure IoT Edge [apparaten en Azure IoT Central.](./concepts-iot-edge.md)
