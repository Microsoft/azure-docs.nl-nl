---
title: De tweelinggrafiek met relaties beheren
titleSuffix: Azure Digital Twins
description: Zie een grafiek met digitale apparaatdubbels beheren door deze te verbinden met relaties.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fde473453aa79e0078765df394acdeb54b3c7fe9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102433315"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Een grafiek van digitale apparaatdubbels beheren met behulp van relaties

De kern van Azure Digital Apparaatdubbels is de [dubbele grafiek](concepts-twins-graph.md) die uw hele omgeving weergeeft. Het dubbele diagram is gemaakt van individuele digitale apparaatdubbels die zijn verbonden via **relaties**. 

Zodra u een werkend [Azure Digital apparaatdubbels-exemplaar](how-to-set-up-instance-portal.md) hebt en [verificatie](how-to-authenticate-client.md) code hebt ingesteld in uw client-app, kunt u de [**DigitalTwins-api's**](/rest/api/digital-twins/dataplane/twins) gebruiken voor het maken, wijzigen en verwijderen van digitale Apparaatdubbels en hun relaties in een Azure Digital apparaatdubbels-instantie. U kunt ook de [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)of de [Azure Digital apparaatdubbels cli](how-to-use-cli.md)gebruiken.

Dit artikel richt zich op het beheren van relaties en de hele grafiek; Zie [*How-to: Manage Digital apparaatdubbels*](how-to-manage-twin.md)(Engelstalig) als u wilt werken met afzonderlijke digitale apparaatdubbels.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>Manieren om grafieken te beheren

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

U kunt ook wijzigingen aanbrengen in uw grafiek met behulp van het Azure Digital Apparaatdubbels Explorer-voor beeld, waarmee u uw apparaatdubbels en Graph kunt visualiseren en het gebruik van de SDK achter de schermen. In de volgende sectie wordt dit voor beeld uitvoerig beschreven.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Relaties maken

Relaties beschrijven hoe verschillende digitale apparaatdubbels met elkaar zijn verbonden, die de basis vormen van het dubbele diagram.

Relaties worden gemaakt met behulp van de `CreateOrReplaceRelationshipAsync()` aanroep. 

Als u een relatie wilt maken, moet u het volgende opgeven:
* De bron-dubbele ID ( `srcId` in het onderstaande code voorbeeld): de id van de dubbele locatie van de relatie.
* De dubbele ID van het doel ( `targetId` in het onderstaande code voorbeeld): de id van de dubbele locatie van de relatie.
* Een relatie naam ( `relName` in het onderstaande code voorbeeld): het algemene type relatie, zoals _contains_.
* Een relatie-ID ( `relId` in het onderstaande code voorbeeld): de specifieke naam voor deze relatie, wat lijkt op _Relationship1_.

De relatie-ID moet uniek zijn binnen de opgegeven bron. Het hoeft niet wereld wijd uniek te zijn.
Voor de dubbele *Foo* moet elke specifieke relatie-id bijvoorbeeld uniek zijn. Een andere dubbele *balk* kan echter een uitgaande relatie hebben die overeenkomt met dezelfde id van een *Foo* -relatie.

In het volgende code voorbeeld ziet u hoe u een relatie maakt in uw Azure Digital Apparaatdubbels-exemplaar. De SDK-aanroep (gemarkeerd) wordt gebruikt in een aangepaste methode die kan worden weer gegeven in de context van een groter programma.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod" highlight="13":::

Deze aangepaste functie kan nu worden aangeroepen om een _contains_ -relatie te maken, zoals: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

Als u meerdere relaties wilt maken, kunt u aanroepen naar dezelfde methode herhalen, waarbij verschillende relatie typen in het argument worden door gegeven. 

`BasicRelationship`Zie [*How-to: use the Azure Digital apparaatdubbels Api's and sdk's*](how-to-use-apis-sdks.md#serialization-helpers)(Engelstalig) voor meer informatie over de helper-klasse.

### <a name="create-multiple-relationships-between-twins"></a>Meerdere relaties maken tussen apparaatdubbels

Relaties kunnen worden geclassificeerd als een van de volgende: 

* Uitgaande relaties: relaties die deel uitmaken van dit dubbele punt om deze te verbinden met andere apparaatdubbels. De `GetRelationshipsAsync()` methode wordt gebruikt voor het ophalen van uitgaande relaties van een twee.
* Binnenkomende relaties: relaties die deel uitmaken van andere apparaatdubbels die naar dit dubbele punt verwijzen om een ' binnenkomende ' koppeling te maken. De `GetIncomingRelationshipsAsync()` methode wordt gebruikt voor het ophalen van binnenkomende relaties van een twee.

Er is geen beperking voor het aantal relaties dat u tussen twee apparaatdubbels kunt hebben: u kunt zo veel relaties tussen apparaatdubbels hebben als u wilt. 

Dit betekent dat u verschillende typen relaties tussen twee apparaatdubbels tegelijk kunt uitdrukken. *Dubbele a* kan bijvoorbeeld zowel een *opgeslagen* relatie als een *vervaardigde* relatie met *dubbele B* hebben.

U kunt zelfs meerdere exemplaren van hetzelfde type relatie maken tussen dezelfde twee apparaatdubbels, indien gewenst. In dit voor beeld kunnen *dubbele a* twee verschillende *opgeslagen* relaties hebben met *dubbele B*, zolang de relaties verschillende relatie-id's hebben.

## <a name="list-relationships"></a>Lijst met relaties

Als u toegang wilt krijgen tot de lijst met **uitgaande** relaties voor een bepaalde dubbele in de grafiek, kunt u de `GetRelationships()` methode als volgt gebruiken:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

Dit retourneert een `Azure.Pageable<T>` of `Azure.AsyncPageable<T>` , afhankelijk van of u de synchrone of asynchrone versie van de aanroep gebruikt.

Hier volgt een voor beeld waarin een lijst met relaties wordt opgehaald. De SDK-aanroep (gemarkeerd) wordt gebruikt in een aangepaste methode die kan worden weer gegeven in de context van een groter programma.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod" highlight="8":::

U kunt deze aangepaste methode nu aanroepen om de uitgaande relaties van het apparaatdubbels als volgt te bekijken:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

U kunt de opgehaalde relaties gebruiken om naar andere apparaatdubbels in uw grafiek te navigeren. U doet dit door het veld te lezen `target` in de relatie die wordt geretourneerd en dit te gebruiken als de id voor uw volgende oproep naar `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Inkomende relaties zoeken naar een digitaal, twee

Azure Digital Apparaatdubbels heeft ook een API voor het zoeken van alle **inkomende** relaties naar een opgegeven dubbele. Dit is vaak handig voor omgekeerde navigatie of bij het verwijderen van een dubbele.

>[!NOTE]
> `IncomingRelationship` oproepen retour neren niet de volledige hoofd tekst van de relatie. Zie de documentatie van het document voor meer informatie over de `IncomingRelationship` -klasse. [](/dotnet/api/azure.digitaltwins.core.incomingrelationship)

Het code voorbeeld in de vorige sectie is gericht op het vinden van uitgaande relaties van een dubbele. Het volgende voor beeld is op dezelfde manier gestructureerd, maar detecteert *inkomende* relaties met de dubbele plaats. In dit voor beeld wordt ook de SDK-aanroep (gemarkeerd) gebruikt in een aangepaste methode die kan worden weer gegeven in de context van een groter programma.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod" highlight="8":::

U kunt deze aangepaste methode nu aanroepen om de binnenkomende relaties van het apparaatdubbels als volgt te bekijken:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>Alle dubbele eigenschappen en relaties weer geven

Met behulp van de bovenstaande methoden voor het weer geven van uitgaande en inkomende relaties naar een dubbele, kunt u een methode maken waarmee de volledige dubbele gegevens worden afgedrukt, met inbegrip van de eigenschappen van de twee en beide typen relaties. Hier volgt een voor beeld van een aangepaste methode voor het combi neren van de bovenstaande aangepaste methoden voor dit doel einde.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

U kunt deze aangepaste functie nu als volgt aanroepen: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="update-relationships"></a>Relaties bijwerken

Relaties worden bijgewerkt met behulp van de- `UpdateRelationship` methode. 

>[!NOTE]
>Deze methode is voor het bijwerken van de **Eigenschappen** van een relatie. Als u de bron dubbel of doel van de relatie wilt wijzigen, moet u [de relatie verwijderen](#delete-relationships) en [er een opnieuw maken](#create-relationships) met behulp van de nieuwe apparaatdubbels.

De vereiste para meters voor de client aanroep zijn de ID van de bron, twee (de dubbele locatie van de relatie), de ID van de relatie die moet worden bijgewerkt en een [JSON-patch](http://jsonpatch.com/) document met de eigenschappen en nieuwe waarden die u wilt bijwerken.

Hier volgt een voorbeeld code waarin wordt getoond hoe u deze methode gebruikt. In dit voor beeld wordt de SDK-aanroep (gemarkeerd) gebruikt in een aangepaste methode die kan worden weer gegeven in de context van een groter programma.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UpdateRelationshipMethod" highlight="6":::

Hier volgt een voor beeld van een aanroep van deze aangepaste methode, waarbij een JSON-patch-document wordt door gegeven met de informatie voor het bijwerken van een eigenschap.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseUpdateRelationship":::

## <a name="delete-relationships"></a>Relaties verwijderen

Met de eerste para meter geeft u de bron op, twee (de dubbele locatie van de relatie). De andere para meter is de relatie-ID. U hebt zowel de dubbele ID als de relatie-ID nodig, omdat relatie-Id's alleen uniek zijn binnen het bereik van een dubbele.

Hier volgt een voorbeeld code waarin wordt getoond hoe u deze methode gebruikt. In dit voor beeld wordt de SDK-aanroep (gemarkeerd) gebruikt in een aangepaste methode die kan worden weer gegeven in de context van een groter programma.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod" highlight="5":::

U kunt deze aangepaste methode nu aanroepen om een relatie als volgt te verwijderen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="runnable-twin-graph-sample"></a>Uitvoer bare dubbele grafiek voorbeeld

Het volgende uitvoer bare code fragment maakt gebruik van de relatie bewerkingen uit dit artikel om een dubbele grafiek te maken van digitale apparaatdubbels en relaties.

### <a name="set-up-the-runnable-sample"></a>Het uitvoer bare-voor beeld instellen

Het fragment maakt gebruik van de [*Room.jsop*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) en [*Floor.jsop*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) model definities uit de [*zelf studie: Verken Azure Digital apparaatdubbels met een voor beeld-client-app*](tutorial-command-line-app.md). U kunt deze koppelingen gebruiken om rechtstreeks naar de bestanden te gaan of ze als onderdeel van het volledige end-to-end- [voorbeeld project te](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)downloaden. 

Ga als volgt te werk voordat u het voor beeld uitvoert:
1. Down load de model bestanden, plaats deze in uw project en vervang de `<path-to>` tijdelijke aanduidingen in de onderstaande code om uw programma te laten weten waar ze zich bevinden.
2. Vervang de tijdelijke aanduiding door de `<your-instance-hostname>` hostnaam van uw Azure Digital apparaatdubbels-exemplaar.
3. Voeg twee afhankelijkheden toe aan uw project die nodig zijn om met Azure Digital Apparaatdubbels te werken. De eerste bestaat uit het pakket voor [Azure Digital Twins SDK voor .NET](/dotnet/api/overview/azure/digitaltwins/client), de tweede biedt hulpmiddelen voor de verificatie met Azure.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

U moet ook lokale referenties instellen als u het voor beeld rechtstreeks wilt uitvoeren. In de volgende sectie wordt dit uitgelegd.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Nadat u de bovenstaande stappen hebt voltooid, kunt u de volgende voorbeeld code rechtstreeks uitvoeren.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

Hier volgt de console-uitvoer van het bovenstaande programma: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Console-uitvoer met de dubbele Details, binnenkomende en uitgaande relaties van de apparaatdubbels." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Het dubbele diagram is een concept van het maken van relaties tussen apparaatdubbels. Als u de visuele weer gave van de dubbele grafiek wilt bekijken, raadpleegt u de sectie [*visualisatie*](how-to-manage-graph.md#visualization) van dit artikel. 

## <a name="create-graph-from-a-csv-file"></a>Een grafiek maken op basis van een CSV-bestand

In praktische use cases worden vaak dubbele hiërarchieën gemaakt op basis van gegevens die zijn opgeslagen in een andere data base, of mogelijk in een spread sheet of CSV-bestand. In deze sectie ziet u hoe u gegevens uit een CSV-bestand kunt lezen en hoe u er een dubbele grafiek van kunt maken.

Bekijk de volgende gegevens tabel, met een beschrijving van een set digitale apparaatdubbels en relaties.

|  Model-id    | Dubbele ID (moet uniek zijn) | Naam van relatie  | Doel-dubbele ID  | Dubbele init-gegevens |
| --- | --- | --- | --- | --- |
| dtmi: voor beeld: Floor; 1    | Floor1 | bevat | Room1 | |
| dtmi: voor beeld: Floor; 1    | Floor0 | bevat | Room0 | |
| dtmi: voor beeld: room; 1    | Room1 | | | {"Tempe ratuur": 80} |
| dtmi: voor beeld: room; 1    | Room0 | | | {"Tempe ratuur": 70} |

Een manier om deze gegevens op te halen in azure Digital Apparaatdubbels is door de tabel te converteren naar een CSV-bestand en code te schrijven om het bestand te interpreteren in opdrachten voor het maken van apparaatdubbels en relaties. In het volgende code voorbeeld ziet u hoe u de gegevens uit het CSV-bestand leest en een dubbele grafiek maakt in azure Digital Apparaatdubbels.

In de onderstaande code wordt het CSV-bestand *data.csv* genoemd en is er een tijdelijke aanduiding voor de **hostnaam** van uw Azure Digital apparaatdubbels-exemplaar. In het voor beeld wordt ook gebruikgemaakt van verschillende pakketten die u aan uw project kunt toevoegen om u te helpen bij dit proces.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het uitvoeren van query's op een Azure Digital Apparaatdubbels-grafiek:
* [*Concepten: query taal*](concepts-query-language.md)
* [*Instructies: een query uitvoeren op de dubbele grafiek*](how-to-query-graph.md)