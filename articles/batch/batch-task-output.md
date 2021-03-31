---
title: Taak-en taak uitvoer persistent maken naar een gegevens archief
description: Meer informatie over de verschillende opties voor het persistent maken van uitvoer gegevens uit batch taken en-taken. U kunt gegevens persistent maken in Azure Storage of op een ander gegevens archief.
ms.topic: how-to
ms.date: 11/14/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4ebe0b6d57225eff9f3f1251d5e491c95e9b7ffc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85965107"
---
# <a name="persist-job-and-task-output"></a>Taken persistent maken

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Enkele veelvoorkomende voor beelden van taak uitvoer zijn:

- Bestanden die worden gemaakt wanneer de taak invoer gegevens verwerkt.
- Logboek bestanden die zijn gekoppeld aan de uitvoering van de taak.

In dit artikel worden verschillende opties beschreven voor het persistent maken van taak uitvoer.

## <a name="options-for-persisting-output"></a>Opties voor persistente uitvoer

Afhankelijk van uw scenario kunt u een aantal verschillende benaderingen nemen om de taak uitvoer te behouden:

- [Gebruik de batch-Service-API](batch-task-output-files.md).  
- [Gebruik de Conventie bibliotheek voor batch bestanden voor .net](batch-task-output-file-conventions.md).  
- Implementeer de conventies van het batch-bestand Standard in uw toepassing.
- Implementeer een oplossing voor aangepaste bestands verplaatsing.

De volgende secties geven een korte beschrijving van elke benadering en algemene ontwerp overwegingen voor persistente uitvoer.

### <a name="use-the-batch-service-api"></a>De API voor de batch-service gebruiken

De batch-service ondersteunt het opgeven van uitvoer bestanden in Azure Storage voor taak gegevens wanneer u [een taak aan een taak toevoegt](/rest/api/batchservice/add-a-task-to-a-job) of [een verzameling taken toevoegt aan een taak](/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Zie voor meer informatie over het persistent maken van taak uitvoer met de batch-Service-API [permanente taak gegevens om te Azure Storage met de API van de batch-service](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>De conventies bibliotheek voor batch bestanden voor .NET gebruiken

Batch definieert een optionele set conventies voor het benoemen van uitvoer bestanden voor taken in Azure Storage. In de [conventies voor batch file Standard](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) worden deze conventies beschreven. De bestands conventies standaard bepalen de namen van de doel container en het BLOB-pad in Azure Storage voor een bepaald uitvoer bestand op basis van de namen van de taak en de taak.

Het is aan u of u besluit de bestands conventies standaard te gebruiken voor het benoemen van uw uitvoer gegevens bestanden. U kunt ook de bestemmings-en Blob een naam laten hebben. Als u de bestands conventies standaard gebruikt voor de naamgeving van uitvoer bestanden, zijn uw uitvoer bestanden beschikbaar voor weer gave in de [Azure Portal][portal].

Ontwikkel aars die batch-oplossingen maken met C# en .NET, kunnen de [Bestands conventies bibliotheek voor .net][nuget_package] gebruiken om taak gegevens te behouden in een Azure Storage-account, volgens de [conventies van het batch-bestand standaard](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions). De bestands conventies bibliotheek verwerkt de verplaatsing van uitvoer bestanden naar Azure Storage en benoemt de bestemmings containers en blobs op een goed bekende manier.

Voor meer informatie over het persistent maken van taak uitvoer met de bestands conventies bibliotheek voor .NET raadpleegt [u taak-en taak gegevens persistent maken voor Azure Storage met de conventies bibliotheek voor batch bestanden voor .net](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>De conventies van het batch-bestand implementeren Standard

Als u een andere taal dan .NET gebruikt, kunt u de [conventies voor batch-bestands Conventers](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) implementeren in uw eigen toepassing.

U kunt de bestands conventies standaard zelf een naam geven als u een bewezen beproefd naam schema wilt of als u de uitvoer van de taak wilt bekijken in de Azure Portal.

### <a name="implement-a-custom-file-movement-solution"></a>Een oplossing voor aangepaste bestands verplaatsing implementeren

U kunt ook uw eigen volledige oplossing voor bestands verplaatsing implementeren. Gebruik deze methode wanneer:

- U wilt taak gegevens persistent maken voor een ander gegevens archief dan Azure Storage. Als u bestanden wilt uploaden naar een gegevens archief, zoals Azure SQL of Azure DataLake, kunt u een aangepast script of een uitvoerbaar bestand maken om te uploaden naar die locatie. U kunt deze vervolgens aanroepen op de opdracht regel nadat u het primaire uitvoer bare bestand hebt uitgevoerd. Op een Windows-knoop punt kunt u bijvoorbeeld de volgende twee opdrachten aanroepen: `doMyWork.exe && uploadMyFilesToSql.exe`
- U wilt de oorspronkelijke resultaten op basis van de controle of het begin van het uploaden uitvoeren.
- U wilt nauw keurige controle over de fout afhandeling behouden. U kunt bijvoorbeeld uw eigen oplossing implementeren als u taak afhankelijke acties wilt gebruiken om bepaalde upload acties uit te voeren op basis van de afsluit codes van specifieke taken. Zie [taak afhankelijkheden maken om taken uit te voeren die afhankelijk zijn van andere taken](batch-task-dependencies.md)voor meer informatie over taak afhankelijkheden.

## <a name="design-considerations-for-persisting-output"></a>Ontwerp overwegingen voor persistente uitvoer

Bij het ontwerpen van uw batch-oplossing moet u rekening houden met de volgende factoren die betrekking hebben op taak-en taak uitvoer.

- **Levens duur reken knooppunt**: reken knooppunten zijn vaak tijdelijk, met name bij Pools met automatisch schalen. Uitvoer van een taak die wordt uitgevoerd op een knoop punt is alleen beschikbaar wanneer het knoop punt bestaat en alleen binnen de Bewaar periode voor bestanden die u hebt ingesteld voor de taak. Als een taak uitvoer produceert die mogelijk nodig is nadat de taak is voltooid, moet de taak de uitvoer bestanden uploaden naar een duurzame Store zoals Azure Storage.

- **Uitvoer opslag**: Azure Storage wordt aanbevolen als een gegevens Archief voor taak uitvoer, maar u kunt ook een duurzame opslag gebruiken. Het schrijven van de taak uitvoer naar Azure Storage is geïntegreerd in de API voor de batch-service. Als u een andere vorm van duurzame opslag gebruikt, moet u de toepassings logica schrijven om zelf de taak uitvoer te behouden.

- **Uitvoer ophalen**: u kunt taak uitvoer rechtstreeks ophalen van de reken knooppunten in uw pool of van Azure Storage of een ander gegevens archief als u permanente taak uitvoer hebt. Als u de uitvoer van een taak rechtstreeks vanuit een reken knooppunt wilt ophalen, moet u de bestands naam en de uitvoer locatie ervan op het knoop punt hebben. Als u de uitvoer van de taak naar Azure Storage persistent wilt maken, hebt u het volledige pad naar het bestand in Azure Storage nodig om de uitvoer bestanden te downloaden met de SDK van Azure Storage.

- **Uitvoer weer geven**: wanneer u navigeert naar een batch-taak in het Azure Portal en **bestanden op het knoop punt** selecteert, worden alle bestanden die zijn gekoppeld aan de taak weer gegeven, niet alleen de uitvoer bestanden waarin u bent geïnteresseerd. De bestanden op Compute-knoop punten zijn alleen beschikbaar wanneer het knoop punt bestaat en alleen binnen de Bewaar tijd van het bestand dat u voor de taak hebt ingesteld. Als u de taak uitvoer wilt weer geven die u hebt behouden Azure Storage, kunt u de Azure Portal of een Azure Storage-client toepassing gebruiken, zoals de [Azure Storage Explorer][storage_explorer]. Als u de uitvoer gegevens in Azure Storage wilt weer geven met de portal of een ander hulp programma, moet u de locatie van het bestand weten en rechtstreeks naar de site navigeren.

## <a name="next-steps"></a>Volgende stappen

- Bekijk met behulp van de nieuwe functies in de batch-Service-API om taak gegevens in [permanente taak gegevens te bewaren om te Azure Storage met de API van de batch-service](batch-task-output-files.md).
- Meer informatie over het gebruik van de batch-bestands conventies bibliotheek voor .NET in [persistente taak-en taak gegevens om te Azure Storage met de conventies bibliotheek voor batch bestanden voor .net](batch-task-output-file-conventions.md).
- Zie het [PersistOutputs][github_persistoutputs] -voorbeeld project op github, waarin wordt gedemonstreerd hoe u zowel de batch-client bibliotheek voor .net als de bestands conventies bibliotheek voor .net gebruikt om de taak uitvoer naar duurzame opslag te behouden.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
