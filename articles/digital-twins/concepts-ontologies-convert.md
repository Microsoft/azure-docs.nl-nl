---
title: Ontologieën converteren
titleSuffix: Azure Digital Twins
description: Inzicht in het proces van het converteren van standaardmodellen naar DTDL voor Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 22b41fce59bf7dbe9db1186036c5ed44f07a4aad
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484473"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Industriestandaard ontologieën converteren naar DTDL voor Azure Digital Twins

De [meeste ontologieën](concepts-ontologies.md) zijn gebaseerd op semantische webstandaarden zoals [TIJDENS,](https://www.w3.org/OWL/) [RDF](https://www.w3.org/2001/sw/wiki/RDF)en [RDFS.](https://www.w3.org/2001/sw/wiki/RDFS) 

Als u een model wilt gebruiken Azure Digital Twins, moet het een DTDL-indeling hebben. In deze artikelen worden algemene ontwerp  richtlijnen beschreven in de vorm van een conversiepatroon voor het converteren van op RDF gebaseerde modellen naar DTDL, zodat ze kunnen worden gebruikt met Azure Digital Twins. 

Het artikel bevat ook [**voorbeeldconvertercode**](#converter-samples) voor RDF- en TIJDENSFS-conversieprogramma's, die kan worden uitgebreid voor andere schema's in de bouw.

## <a name="conversion-pattern"></a>Conversiepatroon

Er zijn verschillende bibliotheken van derden die kunnen worden gebruikt bij het converteren van RDF-modellen naar DTDL. Met sommige van deze bibliotheken kunt u uw modelbestand in een grafiek laden. U kunt door de graaf lopen op zoek naar specifieke RDFS- en TIJDENSFS-constructies en deze converteren naar DTDL.   

De volgende tabel is een voorbeeld van hoe RDFS- en TIJDENSFS-constructies kunnen worden gebruikt voor DTDL. 

| RDFS/CONCEPT VAN HET CONCEPT | RDFS/CONSTRU-constructie | DTDL-concept | DTDL-constructie |
| --- | --- | --- | --- |
| Klassen | `owl:Class`<br>IRI-achtervoegsel<br>``rdfs:label``<br>``rdfs:comment`` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Subklassen | `owl:Class`<br>IRI-achtervoegsel<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Eigenschappen van gegevenstype | `owl:DatatypeProperty`<br>`rdfs:label` of `INode`<br>`rdfs:label`<br>`rdfs:range` | Interface-eigenschappen | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Objecteigenschappen | `owl:ObjectProperty`<br>`rdfs:label` of `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relatie | `type:Relationship`<br>`name`<br>`target` (of weggelaten als er geen `rdfs:range` )<br>`comment`<br>`displayName`<br>

Het volgende C#-codefragment laat zien hoe een RDF-modelbestand wordt geladen in een grafiek en wordt geconverteerd naar DTDL met behulp van de [**dotNetRDF-bibliotheek.**](https://www.dotnetrdf.org/) 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Voorbeelden van converteren

### <a name="rdf-converter-application"></a>RDF-convertertoepassing 

Er is een voorbeeldtoepassing beschikbaar die een op RDF gebaseerd modelbestand converteert naar [DTDL (versie 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) voor gebruik door de Azure Digital Twins service. Het is gevalideerd voor het [fysieke](https://brickschema.org/ontology/) schema en kan worden uitgebreid voor andere schema's in de bouw (zoals [Building Topology Ontology (BOT)](https://w3c-lbd-cg.github.io/bot/), [Semantic Sensor Network](https://www.w3.org/TR/vocab-ssn/)of [buildingSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

Het voorbeeld is een .NET Core-opdrachtregeltoepassing met de **naam RdfToDtdlConverter.**

U kunt het voorbeeld hier vinden: [**RdfToDtdlConverter.**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/) 

Als u de code naar uw computer wilt downloaden, selecteert u de knop Bladeren in **code** onder de titel op de voorbeeldpagina, waarmee u naar de GitHub-opslagplaats voor het voorbeeld gaat. Selecteer de **knop Code** en ZIP **downloaden om** het voorbeeld als een te *downloaden. Zip-bestand* met *de naamRdfToDtdlConverter-main.zip*. Vervolgens kunt u het bestand uitseken en de code verkennen.

:::image type="content" source="media/concepts-ontologies-convert/download-repo-zip.png" alt-text="Schermopname van de opslagplaats RdfToDtdlConverter op GitHub. De knop Code is geselecteerd, wat een klein dialoogvenster produceert waarin de knop ZIP downloaden is gemarkeerd." lightbox="media/concepts-ontologies-convert/download-repo-zip.png":::

U kunt dit voorbeeld gebruiken om de conversiepatronen in context te zien en om als bouwsteen te hebben voor uw eigen toepassingen die modelconversies uitvoeren op basis van uw eigen specifieke behoeften.

### <a name="owl2dtdl-converter"></a>CONVERTEREN2DTDL 

De [**ZETTEN2DTDL Converter**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) is een voorbeeld waarmee een ONTOLOGIE-ontologie wordt omgezet in een set DTDL-interfacedeclaraties, die kunnen worden gebruikt met de Azure Digital Twins service. Het werkt ook voor ontologienetwerken, die zijn gemaakt van één hoofd ontologie die andere ontologieën hergebruikt via `owl:imports` declaraties.

Deze converter is gebruikt voor het vertalen van [de Real Estate Core Ontology](https://doc.realestatecore.io/3.1/full.html) naar DTDL en kan worden gebruikt voor elke ONTOLOGIE op basis van EEN ONTOLOGY.

## <a name="next-steps"></a>Volgende stappen 

* Meer informatie over het uitbreiden van ontologieën volgens de industriestandaard om te voldoen aan uw specificaties: [*Concepten: Uitbreiding van branche ontologieën.*](concepts-ontologies-extend.md)

* U kunt ook doorgaan met het ontwikkelen van modellen op basis van ontologieën: [*Ontologiestrategieën gebruiken in een modelontwikkelingspad.*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)