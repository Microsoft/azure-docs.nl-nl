---
title: Ontologies converteren
titleSuffix: Azure Digital Twins
description: Meer informatie over het proces van het converteren van industrie standaard modellen naar DTDL voor Azure Digital Apparaatdubbels
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: aa4dde51c077152dd5c8a938ad64ad0a051f89ad
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561501"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Industrie standaard Ontologies converteren naar DTDL voor Azure Digital Apparaatdubbels

De meeste [Ontologies](concepts-ontologies.md) zijn gebaseerd op semantische web-standaarden zoals [Owl](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)en [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Als u een model met Azure Digital Apparaatdubbels wilt gebruiken, moet dit de DTDL-indeling hebben. In deze artikelen worden algemene ontwerp richtlijnen beschreven in de vorm van een **conversie patroon** voor het converteren van op RDF gebaseerde modellen naar DTDL, zodat ze kunnen worden gebruikt met Azure Digital apparaatdubbels. 

Het artikel bevat ook een voor beeld van een [**conversie programma code**](#converter-samples) voor RDF-en Owl-conversie Programma's, die kunnen worden uitgebreid voor andere schema's in de bouw nijverheid.

## <a name="conversion-pattern"></a>Conversie patroon

Er zijn verschillende bibliotheken van derden die kunnen worden gebruikt bij het converteren van op RDF gebaseerde modellen naar DTDL. Met sommige van deze bibliotheken kunt u uw model bestand laden in een grafiek. U kunt door de grafiek kijken naar specifieke RDFS-en OWL-constructies en deze converteren naar DTDL.   

De volgende tabel is een voor beeld van hoe RDFS-en OWL-constructies kunnen worden toegewezen aan DTDL. 

| RDFS/OWL-concept | RDFS/OWL-construct | DTDL-concept | DTDL-constructie |
| --- | --- | --- | --- |
| Klassen | `owl:Class`<br>Achtervoegsel voor IRI<br>``rdfs:label``<br>``rdfs:comment`` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Subklassen | `owl:Class`<br>Achtervoegsel voor IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Eigenschappen van gegevens type | `owl:DatatypeProperty`<br>`rdfs:label` of `INode`<br>`rdfs:label`<br>`rdfs:range` | Interface-eigenschappen | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Object eigenschappen | `owl:ObjectProperty`<br>`rdfs:label` of `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relatie | `type:Relationship`<br>`name`<br>`target` (of als er geen waarde wordt opgegeven `rdfs:range` )<br>`comment`<br>`displayName`<br>

In het volgende code fragment van C# ziet u hoe een RDF-model bestand in een grafiek wordt geladen en wordt geconverteerd naar DTDL, met behulp van de [**dotNetRDF**](https://www.dotnetrdf.org/) -bibliotheek. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Voor beelden van Converter

### <a name="rdf-converter-application"></a>Toepassing voor RDF-Converter 

Er is een voorbeeld toepassing beschikbaar waarmee een op RDF gebaseerd model bestand wordt geconverteerd naar [DTDL (versie 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) voor gebruik door de Azure Digital apparaatdubbels-service. Het is gevalideerd voor het [Brick](https://brickschema.org/ontology/) -schema en kan worden uitgebreid voor andere schema's in de bouw nijverheid (zoals het bouwen van [TOPOLOGIE Ontology (bot)](https://w3c-lbd-cg.github.io/bot/), [semantische sensor netwerk](https://www.w3.org/TR/vocab-ssn/)of [IFC (buildingSmart Industry Foundation Classes)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

Het voor beeld is een .NET core-opdracht regel toepassing met de naam **RdfToDtdlConverter**.

U kunt het voor beeld hier ophalen: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Als u de code wilt downloaden naar uw computer, klikt u op de knop voor het downloaden van de *zip* onder de titel op de pagina voor beeld van land. Hiermee wordt een *zip* -bestand met de naam *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* gedownload, dat u vervolgens kunt uitpakken en verkennen.

U kunt dit voor beeld gebruiken om de conversie patronen in de context te bekijken en als bouw steen voor uw eigen toepassingen model conversies uit te voeren op basis van uw eigen specifieke behoeften.

### <a name="owl2dtdl-converter"></a>OWL2DTDL-conversie programma 

Het [**OWL2DTDL-conversie programma**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) is een voor beeld waarmee een Owl Ontology wordt omgezet in een set DTDL-interface declaraties, die kunnen worden gebruikt met de Azure Digital apparaatdubbels-service. Het werkt ook voor Ontology-netwerken, die zijn gemaakt op basis Ontology, waarbij andere Ontologies worden gebruikt via `owl:imports` declaraties.

Dit conversie programma is gebruikt voor het vertalen van de onroerend- [core-Ontology](https://doc.realestatecore.io/3.1/full.html) naar DTDL en kan worden gebruikt voor Ontology op basis van Owl.

## <a name="next-steps"></a>Volgende stappen 

* Meer informatie over het uitbreiden van de industrie standaard Ontologies om te voldoen aan uw specificaties: [*concepten: uitbrei ding van industrie Ontologies*](concepts-ontologies-extend.md).

* U kunt ook door gaan met het pad voor het ontwikkelen van modellen op basis van Ontologies: [*het gebruik van Ontology-strategieën in een model ontwikkelings pad*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).