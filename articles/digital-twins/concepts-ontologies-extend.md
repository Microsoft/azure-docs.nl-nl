---
title: Uitbreiding van ontologieën
titleSuffix: Azure Digital Twins
description: Meer informatie over de redenen en strategieën voor het uitbreiden van een ontologie
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b38b4910773c433ed63fd2082c5cbefce81e0e9e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480203"
---
# <a name="extending-ontologies"></a>Uitbreiding van ontologieën 

Een ontologie die industriestandaard [is,](concepts-ontologies.md)zoals de [op DTDL gebaseerde RealEstateCore-ontologie](https://github.com/Azure/opendigitaltwins-building)voor slimme gebouwen, is een uitstekende manier om uw IoT-oplossing te bouwen. Branche ontologieën bieden een uitgebreide set basisinterfaces die zijn ontworpen voor uw domein en zijn ontworpen om direct te werken in Azure IoT-services, zoals Azure Digital Twins. 

Het is echter mogelijk dat uw oplossing specifieke behoeften heeft die niet onder de ontologie van de branche vallen. U kunt bijvoorbeeld uw digitale tweelingen koppelen aan 3D-modellen die zijn opgeslagen in een afzonderlijk systeem. In dit geval kunt u een van deze ontologieën uitbreiden om uw eigen mogelijkheden toe te voegen met behoud van alle voordelen van de oorspronkelijke ontologie.

In dit artikel wordt de op DTDL gebaseerde [RealEstateCore-ontologie](https://www.realestatecore.io/) gebruikt als basis voor voorbeelden van het uitbreiden van ontologieën met nieuwe DTDL-eigenschappen. De technieken die hier worden beschreven, zijn echter algemeen en kunnen worden toegepast op elk deel van een ontologie op basis van DTDL met elke DTDL-mogelijkheid (telemetrie, eigenschap, relatie, onderdeel of opdracht). 

## <a name="realestatecore-space-hierarchy"></a>RealEstateCore-ruimtehiërarchie 

In de op DTDL gebaseerde RealEstateCore-ontologie wordt de hiërarchie Ruimte gebruikt om verschillende soorten ruimten te definiëren: ruimten, gebouwen, zone, enzovoort. De hiërarchie is een uitbreiding van elk van deze modellen om verschillende soorten ruimten, gebouwen en zones te definiëren. 

Een deel van de hiërarchie ziet eruit als in het onderstaande diagram. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-original.png" alt-text="Stroomdiagram ter illustratie van een deel van de RealEstateCore-ruimtehiërarchie. Op het hoogste niveau is er een element met de naam Ruimte; het is verbonden door een pijl naar beneden uit te breiden naar Room; Ruimte is verbonden door twee pijlen naar beneden op een niveau uit te breiden naar ConferenceRoom en Office."::: 

Zie [*Concepts: Adopting industry-standard ontologies*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology)voor meer informatie over de RealEstateCore-ontologie.

## <a name="extending-the-realestatecore-space-hierarchy"></a>De RealEstateCore-ruimtehiërarchie uitbreiden 

Soms heeft uw oplossing specifieke behoeften die niet worden gedekt door de ontologie van de branche. In dit geval kunt u door de hiërarchie uit te breiden de ontologie van de branche blijven gebruiken en deze aanpassen aan uw behoeften. 

In dit artikel bespreken we twee verschillende gevallen waarbij het uitbreiden van de ontologiehiërarchie nuttig is: 

* Nieuwe interfaces toevoegen voor concepten die niet in de ontologie van de branche worden gebruikt. 
* Aanvullende eigenschappen (of relaties, onderdelen, telemetrie of opdrachten) toevoegen aan bestaande interfaces.

### <a name="add-new-interfaces-for-new-concepts"></a>Nieuwe interfaces toevoegen voor nieuwe concepten 

In dit geval wilt u interfaces toevoegen voor concepten die nodig zijn voor uw oplossing, maar die niet aanwezig zijn in de ontologie van de branche. Als uw oplossing bijvoorbeeld andere typen ruimten heeft die niet worden weergegeven in de realestateCore-ontologie op basis van DTDL, kunt u deze toevoegen door ze rechtstreeks uit te breiden vanuit de RealEstateCore-interfaces. 

In het onderstaande voorbeeld wordt een oplossing weergegeven die 'focusruimten' moet vertegenwoordigen, die niet aanwezig zijn in de ontologie RealEstateCore. Een focusruimte is een kleine ruimte die is ontworpen om mensen zich een paar uur per keer op een taak te richten. 

Als u de ontologie van de branche wilt [](concepts-models.md#model-inheritance) uitbreiden met dit nieuwe concept, maakt u een nieuwe interface die is uitgebreid van de interfaces in de ontologie van de branche. 

Nadat de interface van de focusruimte is toegevoegd, toont de uitgebreide hiërarchie het nieuwe kamertype. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-1.png" alt-text="Stroomdiagram waarin de realEstateCore-ruimtehiërarchie van hierboven wordt weergegeven, met een nieuwe optelling. Op het onderste niveau met ConferenceRoom en Office is er een nieuw element met de naam FocusRoom (ook verbonden via een pijl'extends' van Room)"::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>Aanvullende mogelijkheden toevoegen aan bestaande interfaces 

In dit geval wilt u meer eigenschappen (of relaties, onderdelen, telemetrie of opdrachten) toevoegen aan interfaces in de ontologie van de branche.

In deze sectie ziet u twee voorbeelden: 
* Als u een oplossing bouwt waarin 3D-tekeningen worden weergegeven van spaties die u al in een bestaand systeem hebt, kunt u elke digitale tweeling koppelen aan de 3D-tekening (op id), zodat wanneer de oplossing informatie over de ruimte we geeft, deze ook de 3D-tekening van het bestaande systeem kan ophalen. 
* Als uw oplossing de online/offlinestatus van vergaderruimten moet bijhouden, kunt u de status van de vergaderruimte bijhouden voor weergave of query's. 

Beide voorbeelden kunnen worden geïmplementeerd met nieuwe eigenschappen: een eigenschap die de 3D-tekening koppelt aan de digitale tweeling en een online-eigenschap die aangeeft of de vergaderruimte `drawingId` online is of niet. 

Normaal gesproken wilt u de ontologie van de branche niet rechtstreeks wijzigen, omdat u updates in uw oplossing in de toekomst wilt kunnen opnemen (waardoor uw toevoegingen worden overschreven). In plaats daarvan kunnen dit soort toevoegingen worden aangebracht in uw eigen interfacehiërarchie die zich uitbreidt van de op DTDL gebaseerde RealEstateCore-ontologie. Elke interface die u maakt, maakt gebruik van meerdere interface-overnames om de bovenliggende RealEstateCore-interface en de bovenliggende interface uit te breiden vanuit uw uitgebreide interfacehiërarchie. Met deze aanpak kunt u gebruikmaken van de ontologie in de branche en uw toevoegingen. 

Als u de ontologie van de branche wilt uitbreiden, maakt u uw eigen interfaces die zijn uitgebreid van de interfaces in de ontologie van de branche en voegt u de nieuwe mogelijkheden toe aan uw uitgebreide interfaces. Voor elke interface die u wilt uitbreiden, maakt u een nieuwe interface. De uitgebreide interfaces zijn geschreven in DTDL (zie de sectie DTDL voor uitgebreide interfaces verder in dit document). 

Na het uitbreiden van het gedeelte van de hiërarchie dat hierboven wordt weergegeven, ziet de uitgebreide hiërarchie eruit zoals in het onderstaande diagram. Hier voegt de uitgebreide space-interface de eigenschap toe die een id bevat die de digitale tweeling koppelt `drawingId` aan de 3D-tekening. Daarnaast voegt de interface ConferenceRoom een online-eigenschap toe die de onlinestatus van de vergaderruimte bevat. Via overname bevat de interface ConferenceRoom alle mogelijkheden van de RealEstateCore ConferenceRoom-interface, evenals alle mogelijkheden van de uitgebreide space-interface. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-2.png" alt-text="Stroomdiagram met de uitgebreide hiërarchie van de RealEstateCore-ruimte hierboven, met meer nieuwe toevoegingen. Room deelt nu het niveau met een Spatie-element, dat is verbonden met een pijl-omlaag naar een nieuw Room-element naast ConferenceRoom en Office.  De nieuwe elementen zijn verbonden met de bestaande ontologie met meer 'extends'-relaties."::: 

## <a name="using-the-extended-space-hierarchy"></a>De uitgebreide ruimtehiërarchie gebruiken 

Wanneer u digitale tweelingen maakt met behulp van de uitgebreide ruimtehiërarchie, is het model van elke digitale tweeling één van de uitgebreide ruimtehiërarchie (niet de oorspronkelijke ontologie in de branche) en bevat het alle mogelijkheden van de ontologie in de branche en de uitgebreide interfaces via interface-overname.

Het model van elke digitale tweeling is een interface uit de uitgebreide hiërarchie, zoals wordt weergegeven in het onderstaande diagram. 
 
:::image type="content" source="media/concepts-ontologies-extend/ontology-with-models.png" alt-text="Een fragment van de uitgebreide hiërarchie van RealEstateCore-ruimte, met inbegrip van Ruimte (hoogste niveau), één ruimte (middelste niveau) en ConferenceRoom, Office en FocusRoom (lager niveau). Namen van modellen zijn verbonden met elk element (room is bijvoorbeeld verbonden met een model met de naam Room101)."::: 

Bij het uitvoeren van query's op digitale tweelingen met behulp van de model-id (de operator), moeten de model-id's van de uitgebreide hiërarchie `IS_OF_MODEL` worden gebruikt. Bijvoorbeeld `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## <a name="contributing-back-to-the-original-ontology"></a>Bijdragen aan de oorspronkelijke ontologie 

In sommige gevallen breidt u de ontologie van de branche uit op een manier die breed nuttig is voor de meeste gebruikers van de ontologie. In dit geval kunt u overwegen om uw extensies terug te dragen aan de oorspronkelijke ontologie. Elke ontologie heeft een ander proces om bij te dragen. Controleer daarom de GitHub-opslagplaats van de ontology op bijdragedetails. 

## <a name="dtdl-for-new-interfaces"></a>DTDL voor nieuwe interfaces 

De DTDL voor nieuwe interfaces die rechtstreeks vanuit de branche ontology uitbreiden, zou er als deze uitzien. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>DTDL voor uitgebreide interfaces 

De DTDL voor de uitgebreide interfaces, beperkt tot het hierboven beschreven gedeelte, zou er als dit uitzien. 

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

Ga verder met het ontwikkelen van modellen op basis van ontologieën: [*Ontologiestrategieën gebruiken in een modelontwikkelingspad.*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)