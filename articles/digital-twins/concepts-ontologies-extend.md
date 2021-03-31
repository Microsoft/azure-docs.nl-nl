---
title: Ontologies uitbreiden
titleSuffix: Azure Digital Twins
description: Meer informatie over de redenen en strategieën achter het uitbreiden van een Ontology
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e5973f58887b212919ad739232faafddcf9e735c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100561397"
---
# <a name="extending-ontologies"></a>Ontologies uitbreiden 

Een industrie standaard- [Ontology](concepts-ontologies.md), zoals de [op DTDL gebaseerde RealEstateCore-Ontology voor slimme gebouwen](https://github.com/Azure/opendigitaltwins-building), is een fantastische manier om uw IOT-oplossing te bouwen. Industry Ontologies bieden een uitgebreide set basis interfaces die zijn ontworpen voor uw domein en die is ontwikkeld voor gebruik in het vak in azure IoT-Services, zoals Azure Digital Apparaatdubbels. 

Het is echter mogelijk dat uw oplossing specifieke behoeften heeft die niet onder de branche Ontology vallen. U kunt bijvoorbeeld uw digitale apparaatdubbels koppelen aan 3D-modellen die zijn opgeslagen in een afzonderlijk systeem. In dit geval kunt u een van deze Ontologies uitbreiden om uw eigen mogelijkheden toe te voegen, waarbij u alle voor delen van de oorspronkelijke Ontology behoudt.

In dit artikel wordt gebruikgemaakt van de op DTDL gebaseerde [RealEstateCore](https://www.realestatecore.io/) Ontology als basis voor voor beelden van het uitbreiden van Ontologies met nieuwe DTDL-eigenschappen. De technieken die hier worden beschreven, zijn echter algemeen en kunnen worden toegepast op elk deel van een op DTDL gebaseerde Ontology met een DTDL-mogelijkheid (telemetrie, eigenschap, relatie, onderdeel of opdracht). 

## <a name="realestatecore-space-hierarchy"></a>RealEstateCore-ruimte hiërarchie 

In de op DTDL gebaseerde RealEstateCore Ontology wordt de ruimte hiërarchie gebruikt voor het definiëren van verschillende soorten ruimten: ruimtes, gebouwen, zone enzovoort. De hiërarchie is uitbrei ding van elk van deze modellen om verschillende soorten kamers, gebouwen en zones te definiëren. 

Een deel van de hiërarchie ziet eruit als in het onderstaande diagram. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-original.png" alt-text="Stroom diagram illustreert een deel van de RealEstateCore-ruimte hiërarchie. Op het hoogste niveau is er een element met de naam ruimte. het is verbonden door een ' extends '-pijl omlaag een niveau naar een ruimte. De ruimte is verbonden met twee ' extends '-pijlen omlaag een niveau naar ConferenceRoom en Office."::: 

Zie voor meer informatie over de RealEstateCore-Ontology [*concepten: voor het aannemen van industrie standaard Ontologies*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology).

## <a name="extending-the-realestatecore-space-hierarchy"></a>De RealEstateCore-ruimte hiërarchie uitbreiden 

Soms heeft uw oplossing specifieke behoeften die niet worden gedekt door de branche Ontology. In dit geval kunt u met het uitbreiden van de hiërarchie de branche Ontology blijven gebruiken terwijl u deze aan uw behoeften aanpast. 

In dit artikel bespreken we twee verschillende gevallen waarin het uitbreiden van de hiërarchie van Ontology nuttig is: 

* Nieuwe interfaces toevoegen voor concepten die niet in de branche Ontology zijn. 
* Toevoegen van extra eigenschappen (of relaties, onderdelen, telemetrie en opdrachten) aan bestaande interfaces.

### <a name="add-new-interfaces-for-new-concepts"></a>Nieuwe interfaces toevoegen voor nieuwe concepten 

In dit geval wilt u interfaces toevoegen voor concepten die nodig zijn voor uw oplossing, maar die niet aanwezig zijn in de branche Ontology. Als uw oplossing bijvoorbeeld andere typen kamers heeft die niet worden weer gegeven in de DTDL RealEstateCore Ontology, kunt u ze toevoegen door rechtstreeks vanuit de RealEstateCore-interfaces uit te breiden. 

In het onderstaande voor beeld ziet u een oplossing die ' focus kamers ' moet vertegenwoordigen, die niet aanwezig zijn in de RealEstateCore-Ontology. Een focus ruimte is een kleine ruimte die is ontworpen voor mensen om een aantal uur per keer te richten op een taak. 

Als u de industrie-Ontology met dit nieuwe concept wilt uitbreiden, maakt u een nieuwe interface die [uitbreidt van](concepts-models.md#model-inheritance) de interfaces in de branche Ontology. 

Nadat u de focus room-interface hebt toegevoegd, wordt in de uitgebreide hiërarchie het nieuwe type ruimte weer gegeven. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-1.png" alt-text="Stroom diagram waarin de RealEstateCore-ruimte hiërarchie van boven wordt geïllustreerd, met een nieuwe toevoeging. Op het onderste niveau met ConferenceRoom en Office bevindt zich een nieuw element met de naam FocusRoom (ook via een ' extends '-pijl verbonden vanuit kamer)"::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>Aanvullende mogelijkheden toevoegen aan bestaande interfaces 

In dit geval wilt u meer eigenschappen (of relaties, onderdelen, telemetrie en opdrachten) toevoegen aan interfaces die zich in de branche Ontology bevinden.

In deze sectie ziet u twee voor beelden: 
* Als u een oplossing bouwt waarmee 3D-tekeningen van ruimten worden weer gegeven die u al in een bestaand systeem hebt, kunt u elk digitaal punt koppelen aan de 3D-tekening (op ID) zodat wanneer de oplossing informatie over de ruimte weergeeft, de 3D-tekening ook kan ophalen uit het bestaande systeem. 
* Als uw oplossing de status online/offline van Vergader zalen moet volgen, kunt u de status van de conferentie kamer bijhouden voor gebruik in weer gave of query's. 

Beide voor beelden kunnen worden geïmplementeerd met nieuwe eigenschappen: een `drawingId` eigenschap die de 3D-tekening koppelt aan de digitale twee en een ' online ' eigenschap die aangeeft of de Vergader ruimte online is of niet. 

Normaal gesp roken wilt u de branche Ontology niet rechtstreeks wijzigen omdat u in de toekomst updates kunt opnemen in uw oplossing (waardoor uw toevoegingen worden overschreven). In plaats daarvan kunnen dit soort toevoegingen worden gemaakt in uw eigen interface hiërarchie die wordt uitgebreid van de DTDL RealEstateCore Ontology. Elke interface die u maakt, maakt gebruik van meerdere interface overname om de bovenliggende RealEstateCore-interface en de bovenliggende interface van de uitgebreide interface hiërarchie uit te breiden. Op deze manier kunt u het gebruik van de branche Ontology en uw toevoegingen tegelijk maken. 

Als u de sector Ontology wilt uitbreiden, maakt u uw eigen interfaces die van de interfaces in de branche Ontology uitbrei ding en voegt u de nieuwe mogelijkheden toe aan uw uitgebreide interfaces. Voor elke interface die u wilt uitbreiden, kunt u een nieuwe interface maken. De uitgebreide interfaces zijn geschreven in DTDL (Zie de sectie DTDL voor uitgebreide interfaces verderop in dit document). 

Nadat u het gedeelte van de hierboven weer gegeven hiërarchie hebt uitgebreid, ziet de uitgebreide hiërarchie eruit als in het onderstaande diagram. Hier voegt u de eigenschap Extended Space toe, `drawingId` die een id bevat die de digitale twee koppelt aan de 3D-tekening. Daarnaast voegt de ConferenceRoom-interface een eigenschap ' online ' toe die de online status van de Vergader ruimte bevat. Via overname bevat de ConferenceRoom-interface alle mogelijkheden van de RealEstateCore ConferenceRoom-interface, evenals alle mogelijkheden van de Extended Space-interface. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-2.png" alt-text="Stroom diagram waarin de uitgebreide RealEstateCore-ruimte hiërarchie hierboven wordt geïllustreerd, met meer nieuwe toevoegingen. Room deelt nu het niveau met een ruimte-element, dat verbinding maakt met een ' extends ' pijl omlaag een niveau naar een nieuw room-element naast ConferenceRoom en Office.  De nieuwe elementen zijn verbonden met de bestaande Ontology met meer extends-relaties."::: 

## <a name="using-the-extended-space-hierarchy"></a>De uitgebreide ruimte hiërarchie gebruiken 

Wanneer u digitale apparaatdubbels maakt met behulp van de uitgebreide ruimte hiërarchie, is het model van de digitale twee verschillende modellen van de uitgebreide ruimte hiërarchie (niet de oorspronkelijke branche Ontology) en worden alle mogelijkheden van de branche Ontology en de uitgebreide interfaces, hoewel interface overname, meegenomen.

Elk digitaal dubbel model is een interface uit de uitgebreide hiërarchie die in het onderstaande diagram wordt weer gegeven. 
 
:::image type="content" source="media/concepts-extending-ontologies/ontology-with-models.png" alt-text="Een fragment van de uitgebreide RealEstateCore-ruimte hiërarchie, inclusief ruimte (hoogste niveau), één ruimte (middelste niveau) en ConferenceRoom, Office en FocusRoom (lager niveau). De namen van modellen zijn gekoppeld aan elk-element (er is bijvoorbeeld ruimte verbonden met een model met de naam Room101)."::: 

Bij het uitvoeren van een query voor Digital apparaatdubbels met de model-ID (de `IS_OF_MODEL` operator), moeten de model-id's uit de uitgebreide hiërarchie worden gebruikt. Bijvoorbeeld `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## <a name="contributing-back-to-the-original-ontology"></a>Een bijdrage leveren aan de oorspronkelijke Ontology 

In sommige gevallen breidt u de branche Ontology uit op een manier die veel nuttig is voor de meeste gebruikers van de Ontology. In dit geval kunt u uw uitbrei dingen terugsturen naar de oorspronkelijke Ontology. Elke Ontology heeft een ander proces voor bijdragen, dus controleer de GitHub-opslag plaats van Ontology voor meer informatie over de bijdragen. 

## <a name="dtdl-for-new-interfaces"></a>DTDL voor nieuwe interfaces 

De DTDL voor nieuwe interfaces die rechtstreeks vanuit de Ontology van de branche worden uitgebreid, ziet er als volgt uit. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>DTDL voor uitgebreide interfaces 

De DTDL voor de uitgebreide interfaces, beperkt tot het hierboven beschreven gedeelte, ziet er als volgt uit. 

```json
[
  {
    "@id": "dtmi:com:example:Space;1",
    "@type": "Interface",
    "extends": "dtmi:digitaltwins:rec_3_3:core:Space;1",
    "contents": [
      {
        "@type": "Property",
        "name": "drawingid",
        "schema": "string"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Room;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:core:Room;1",
      "dtmi:com:example:Space;1"
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:ConferenceRoom;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:ConferenceRoom;1",
      "dtmi:com:example:Room;1"
    ],
    "contents": [
      {
        "@type": "Property",
        "name": "online",
        "schema": "boolean"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Office;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:Office;1", 
      "dtmi:com:example:Room;1" 
    ],
    "@context": "dtmi:dtdl:context;2" 
  }, 
  {
    "@id": "dtmi:com:example:FocusRoom;1", 
    "@type": "Interface", 
    "extends": "dtmi:com:example:Office;1", 
    "@context": "dtmi:dtdl:context;2" 
  }
]
``` 

## <a name="next-steps"></a>Volgende stappen

Ga door naar het pad voor het ontwikkelen van modellen op basis van Ontologies: [*het gebruik van Ontology-strategieën in een model ontwikkelings pad*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).