---
title: Bron bestanden maken en gebruiken
description: Meer informatie over het maken van batch-resource bestanden uit verschillende invoer bronnen. In dit artikel worden enkele algemene methoden beschreven voor het maken en plaatsen van deze op een virtuele machine.
ms.date: 03/18/2020
ms.topic: how-to
ms.openlocfilehash: 84a5e9780b4fa0abfec5b736e04d385f14716873
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109286"
---
# <a name="creating-and-using-resource-files"></a>Bron bestanden maken en gebruiken

Een Azure Batch taak vereist vaak een deel van de gegevens die moeten worden verwerkt. Bron bestanden zijn de manier om deze gegevens via een taak aan uw batch virtuele machine (VM) te leveren. Alle soorten taken ondersteunen bron bestanden: taken, start taken, taak voorbereidings taken, taak release taken, enzovoort. In dit artikel worden enkele veelvoorkomende methoden beschreven voor het maken van bron bestanden en het plaatsen ervan op een virtuele machine.  

Bron bestanden plaatsen gegevens op een virtuele machine in batch, maar het type gegevens en de manier waarop het wordt gebruikt, is flexibel. Er zijn echter enkele veelvoorkomende use cases:

1. Algemene bestanden op elke VM inrichten met bron bestanden op basis van een begin taak
1. Invoer gegevens inrichten voor verwerking door taken

Algemene bestanden kunnen bijvoorbeeld bestanden op een begin taak zijn die worden gebruikt voor het installeren van toepassingen die door uw taken worden uitgevoerd. Invoer gegevens kunnen onbewerkte afbeeldings-of video gegevens zijn, of gegevens die door batch moeten worden verwerkt.

## <a name="types-of-resource-files"></a>Typen bron bestanden

Er zijn een aantal verschillende opties beschikbaar voor het genereren van bron bestanden. Het proces voor het maken van bron bestanden is afhankelijk van waar de oorspronkelijke gegevens zijn opgeslagen.

Opties voor het maken van een bron bestand:

- [Opslag container-URL](#storage-container-url): Hiermee wordt een bron bestand uit een opslag container in azure gegenereerd
- [Naam van opslag container](#storage-container-name): genereert een bron bestand van de naam van een container in een Azure Storage-account dat is gekoppeld aan batch
- [Web-eind punt](#web-endpoint): genereert een bron bestand van een geldige http-URL

### <a name="storage-container-url"></a>URL van de opslag container

Als u een opslag container-URL gebruikt, hebt u met de juiste machtigingen toegang tot bestanden in een opslag container in Azure. 

In dit C#-voor beeld zijn de bestanden al geüpload naar een Azure storage-container als Blob-opslag. Om toegang te krijgen tot de gegevens die nodig zijn om een bron bestand te maken, moeten we eerst toegang krijgen tot de opslag container.

Maak een SAS-URI (Shared Access Signature) met de juiste machtigingen voor toegang tot de opslag container. Stel de verloop tijd en machtigingen voor de SAS in. In dit geval wordt geen start tijd opgegeven, zodat de SA'S onmiddellijk geldig zijn en na het genereren twee uur verlopen.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Voor toegang tot de container moet u zowel `Read` als `List` machtigingen hebben, terwijl BLOB-toegang u alleen toestemming nodig heeft `Read` .

Nadat de machtigingen zijn geconfigureerd, maakt u het SAS-token en formatteert u de SAS-URL voor toegang tot de opslag container. Genereer met de opgemaakte SAS-URL voor de opslag container een bron bestand met [`FromStorageContainerUrl`](/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl) .

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Een alternatief voor het genereren van een SAS-URL is het inschakelen van anonieme, open bare Lees toegang tot een container en de bijbehorende blobs in Azure Blob-opslag. Door dit te doen, kunt u alleen-lezen toegang verlenen aan deze resources zonder uw account sleutel te delen en zonder een SAS te hoeven gebruiken. Open bare Lees toegang wordt doorgaans gebruikt voor scenario's waarin u wilt dat bepaalde blobs altijd beschikbaar zijn voor anonieme lees toegang. Als dit scenario tegemoetkomt aan uw oplossing, raadpleegt u het artikel [anonieme toegang tot blobs](../storage/blobs/anonymous-read-access-configure.md) voor meer informatie over het beheren van toegang tot uw BLOB-gegevens.

### <a name="storage-container-name"></a>Naam van opslag container

In plaats van een SAS-URL te configureren en te maken, kunt u de naam van uw Azure-opslag container gebruiken om toegang te krijgen tot uw BLOB-gegevens. De opslag container die u gebruikt, moet zich in het Azure Storage-account bevinden dat is gekoppeld aan uw batch-account. Dit opslag account wordt het autoopslag-account genoemd. Met de autoopslag-container kunt u het configureren en maken van een SAS-URL voor toegang tot een opslag container overs Laan.

In dit voor beeld wordt ervan uitgegaan dat de gegevens die moeten worden gebruikt voor het maken van resources, al bestaan in een Azure Storage account dat aan uw batch-account is gekoppeld. Als u geen autoopslag-account hebt, raadpleegt u de stappen in [een batch-account maken](batch-account-create-portal.md) voor meer informatie over het maken en koppelen van een account.

Als u een gekoppeld opslag account gebruikt, hoeft u geen SAS-URL te maken en configureren voor een opslag container. Geef in plaats daarvan de naam op van de opslag container in het gekoppelde opslag account.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Web-eind punt

Gegevens die niet naar Azure Storage worden geüpload, kunnen nog steeds worden gebruikt voor het maken van bron bestanden. U kunt een geldige HTTP-URL opgeven die uw invoer gegevens bevat. De URL wordt aan de batch-API door gegeven, waarna de gegevens worden gebruikt om een bron bestand te maken.

In het volgende C#-voor beeld wordt de invoer gegevens gehost op een fictieve GitHub-eind punt. De API haalt het bestand op uit het geldige web-eind punt en genereert een bron bestand dat door uw taak moet worden verbruikt. Er zijn geen referenties nodig voor dit scenario.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Tips en suggesties

Elke Azure Batch-taak gebruikt bestanden anders. Dit is de reden waarom batch opties beschikbaar heeft voor het beheren van bestanden op taken. De volgende scenario's zijn niet uitgebreid, maar in plaats daarvan worden enkele veelvoorkomende situaties behandeld en worden aanbevelingen geboden.

### <a name="many-resource-files"></a>Veel bron bestanden

Uw batch-taak bevat mogelijk meerdere taken die allemaal dezelfde, algemene bestanden gebruiken. Als algemene taak bestanden worden gedeeld door veel taken, is het gebruik van een toepassings pakket voor het opnemen van de bestanden in plaats van het gebruik van bron bestanden een betere optie. Toepassings pakketten bieden optimalisatie voor de download snelheid. De gegevens in toepassings pakketten worden ook opgeslagen in de cache tussen taken, dus als uw taak bestanden niet vaak worden gewijzigd, zijn de toepassings pakketten mogelijk geschikt voor uw oplossing. Met toepassings pakketten hoeft u niet hand matig verschillende bron bestanden te beheren of SAS-Url's te genereren om toegang te krijgen tot de bestanden in Azure Storage. Batch werkt op de achtergrond met Azure Storage om toepassings pakketten op te slaan en te implementeren op reken knooppunten.

Als elke taak veel bestanden bevat die uniek zijn voor deze taak, zijn bron bestanden de beste optie, omdat taken die gebruikmaken van unieke bestanden vaak moeten worden bijgewerkt of vervangen. Dit is niet zo eenvoudig te doen met de inhoud van toepassings pakketten. Bron bestanden bieden extra flexibiliteit voor het bijwerken, toevoegen of bewerken van afzonderlijke bestanden.

### <a name="number-of-resource-files-per-task"></a>Aantal resource bestanden per taak

Als er meerdere honderd bron bestanden op een taak zijn opgegeven, kan de taak door batch worden geweigerd omdat deze te groot is. Het is raadzaam om uw taken klein te blijven door het aantal resource bestanden voor de taak zelf te minimaliseren.

Als er geen manier is om het aantal bestanden dat uw taak nodig heeft te minimaliseren, kunt u de taak optimaliseren door één bron bestand te maken dat verwijst naar een opslag container van bron bestanden. Als u dit wilt doen, plaatst u de bron bestanden in een Azure Storage-container en gebruikt u de verschillende container [methoden](/dotnet/api/microsoft.azure.batch.resourcefile#methods) voor bron bestanden. Gebruik de opties voor het voor voegsel van de blob om verzamelingen bestanden op te geven die moeten worden gedownload voor uw taken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [toepassings pakketten](batch-application-packages.md) als alternatief voor bron bestanden.
- Zie [container workloads](batch-docker-container-workloads.md)voor meer informatie over het gebruik van containers voor bron bestanden.
- Zie voor meer informatie over het verzamelen en opslaan van de uitvoer gegevens uit uw taken [persistente taak en taak uitvoer](batch-task-output.md).
- Meer informatie over de [Batch-API's en -hulpprogramma's](batch-apis-tools.md) die beschikbaar zijn voor het bouwen van Batch-oplossingen.