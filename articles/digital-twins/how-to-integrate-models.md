---
title: In de industrie gangbare modellen integreren
titleSuffix: Azure Digital Twins
description: Meer informatie over het integreren van industrie standaard modellen in DTDL voor Azure Digital Apparaatdubbels, hetzij met behulp van speciale DTDL Ontologies of het converteren van bestaande Ontologies
author: baanders
ms.author: baanders
ms.date: 11/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3376f5d5e207a33ad39cd7506998e2ee90e084ad
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051544"
---
# <a name="integrate-industry-standard-models-with-dtdl-for-azure-digital-twins"></a>Industrie standaard modellen integreren met DTDL voor Azure Digital Apparaatdubbels

Het gebruik van modellen die zijn gebaseerd op industrie normen of het gebruik van de standaard Ontology-vertegenwoordiging, zoals RDF of OWL, biedt een rijk begin punt bij het ontwerpen van uw Azure Digital Apparaatdubbels-modellen. Het gebruik van industrie modellen helpt ook bij standaardisatie en het delen van gegevens.

Als u wilt gebruiken met Azure Digital Apparaatdubbels, moet een model worden weer gegeven in de op JSON-LD gebaseerde [**Digital Apparaatdubbels Definition Language (DTDL)**](concepts-models.md). In dit artikel wordt daarom beschreven hoe u uw industrie standaard modellen in DTDL aanduidt, de bestaande principes van de industrie integreert met DTDL semantiek zodat Azure Digital Apparaatdubbels deze kan gebruiken. Het DTDL-model fungeert vervolgens als de bron van waarheid voor het model in azure Digital Apparaatdubbels.

Er zijn drie mogelijke paden voor het integreren van industrie standaard modellen met DTDL:
* **Aannemen**: u kunt uw oplossing starten met een open-source DTDL-Ontology dat is gebouwd op veel aangenomen industrie normen. 
* **Converteren**: als u al bestaande modellen hebt, moet u deze converteren naar DTDL.
* **Auteur**: u kunt altijd uw eigen aangepaste DTDL-modellen ontwikkelen, zoals beschreven in [*instructies: aangepaste modellen beheren*](how-to-manage-model.md).

## <a name="adopt-an-open-source-dtdl-ontology"></a>Een open-source DTDL-Ontology aannemen

Het is vaak gemakkelijker om te beginnen met een open-source DTDL-Ontology dan vanaf een lege pagina. 

Smart-gebouwen oplossingen kunnen bijvoorbeeld gebruikmaken van de open-source [**DTDL RealEstateCore Ontology**](https://github.com/Azure/opendigitaltwins-building), die een gemeen schappelijke grond vormt voor het model leren van intelligente gebouwen en die de industrie normen benutten om Reinvention te voor komen. 

Deze open-source DTDL Ontologies bieden ook aanbevolen procedures voor het gebruik en de juiste uitbrei ding van de modellen. 

## <a name="convert-existing-models-to-dtdl"></a>Bestaande modellen converteren naar DTDL

De meeste branche modellen (ook wel **Ontologies** genoemd) zijn gebaseerd op semantische web-standaarden zoals [Owl](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)en [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Als u een model met Azure Digital Apparaatdubbels wilt gebruiken, moet dit de DTDL-indeling hebben. In deze sectie worden algemene ontwerp richtlijnen beschreven in de vorm van een **conversie patroon** voor het converteren van op RDF gebaseerde modellen naar DTDL, zodat ze kunnen worden gebruikt met Azure Digital apparaatdubbels. 

Het bevat ook [een voor beeld van een **conversie programma code** voor een RDF-Converter](#sample-converter-application), dat is gevalideerd voor het [Brick](https://brickschema.org/ontology/) -schema en kan worden uitgebreid voor andere schema's in de bouw nijverheid.

### <a name="conversion-pattern"></a>Conversie patroon

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

### <a name="sample-converter-application"></a>Voor beeld-Converter toepassing 

Er is een voorbeeld toepassing beschikbaar waarmee een op RDF gebaseerd model bestand wordt geconverteerd naar [DTDL (versie 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) voor gebruik door de Azure Digital apparaatdubbels-service. Het is gevalideerd voor het [Brick](https://brickschema.org/ontology/) -schema en kan worden uitgebreid voor andere schema's in de bouw nijverheid (zoals het bouwen van [TOPOLOGIE Ontology (bot)](https://w3c-lbd-cg.github.io/bot/), [semantische sensor netwerk](https://www.w3.org/TR/vocab-ssn/)of [IFC (buildingSmart Industry Foundation Classes)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

Het voor beeld is een .NET core-opdracht regel toepassing met de naam **RdfToDtdlConverter**.

U kunt het voor beeld hier ophalen: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Als u de code wilt downloaden naar uw computer, klikt u op de knop voor het downloaden van de *zip* onder de titel op de pagina voor beeld van land. Hiermee wordt een *zip* -bestand met de naam *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* gedownload, dat u vervolgens kunt uitpakken en verkennen.

U kunt dit voor beeld gebruiken om de conversie patronen in de context te bekijken en als bouw steen voor uw eigen toepassingen model conversies uit te voeren op basis van uw eigen specifieke behoeften.

## <a name="validate-and-upload-dtdl-models"></a>Valideer en upload DTDL-modellen

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

Zodra een model is geconverteerd en gevalideerd, kunt u **het uploaden naar uw Azure Digital apparaatdubbels-exemplaar**. Zie voor meer informatie over dit proces het gedeelte [*modellen uploaden*](how-to-manage-model.md#upload-models) van *instructies: aangepaste modellen beheren*.

## <a name="next-steps"></a>Volgende stappen 

Meer informatie over het ontwerpen en beheren van digitale twee modellen:
 
* [*Concepten: Aangepaste modellen*](concepts-models.md)
* [*Instructies: Aangepaste modellen beheren*](how-to-manage-model.md)
* [*Instructies: modellen parseren en valideren*](how-to-parse-models.md)