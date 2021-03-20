---
title: Azure Storage resources met C++-client bibliotheek weer geven
description: Meer informatie over het gebruik van de vermelding-Api's in Microsoft Azure Storage-client bibliotheek voor C++ om containers, blobs, wacht rijen, tabellen en entiteiten op te sommen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/23/2017
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 8c8e2d2ddf6899e62bc95bc1e52c84eccdc3a91e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92784095"
---
# <a name="list-azure-storage-resources-in-c"></a>Azure Storage-resources in C++ weergeven

De bewerkings bewerkingen zijn essentieel voor een groot aantal ontwikkel scenario's met Azure Storage. In dit artikel wordt beschreven hoe u de meeste efficiënte objecten in Azure Storage kunt opsommen met behulp van de weer gegeven Api's in de Microsoft Azure Storage-client bibliotheek voor C++.

> [!NOTE]
> Deze hand leiding is gericht op de Azure Storage-client bibliotheek voor C++ versie 2. x, die beschikbaar is via [NuGet](https://www.nuget.org/packages/wastorage) of [github](https://github.com/Azure/azure-storage-cpp).

De Storage-client bibliotheek biedt diverse methoden voor het weer geven of doorzoeken van objecten in Azure Storage. Dit artikel heeft betrekking op de volgende scenario's:

* Containers in een account weer geven
* Blobs in een container of virtuele BLOB-map weer geven
* Wacht rijen in een account weer geven
* Tabellen in een account weer geven
* Entiteiten in een tabel opvragen

Elk van deze methoden wordt weer gegeven met verschillende overbelastingen voor verschillende scenario's.

## <a name="asynchronous-versus-synchronous"></a>Asynchroon versus synchroon

Omdat de Storage-client bibliotheek voor C++ is gebouwd op basis van de [C++ rest-bibliotheek](https://github.com/Microsoft/cpprestsdk), ondersteunen we asynchrone bewerkingen nu met behulp van [pplx:: taak](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Bijvoorbeeld:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Met synchrone bewerkingen worden de bijbehorende asynchrone bewerkingen gepakt:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Als u met meerdere threading-toepassingen of-Services werkt, raden we u aan om de async-Api's rechtstreeks te gebruiken in plaats van een thread te maken om de synchronisatie-Api's aan te roepen, waardoor de prestaties aanzienlijk worden beïnvloed.

## <a name="segmented-listing"></a>Gesegmenteerde vermelding

Voor de schaal van Cloud opslag is gesegmenteerde vermeldingen vereist. U kunt bijvoorbeeld meer dan een miljoen blobs in een Azure Blob-container of meer dan een miljard entiteiten in een Azure-tabel hebben. Dit zijn niet theoretische getallen, maar werkelijke gebruiks gevallen van klanten.

Daarom is het niet praktisch om alle objecten in één antwoord weer te geven. In plaats daarvan kunt u objecten weer geven met behulp van paginering. Elk van de vermeldings-Api's heeft een *gesegmenteerde* overbelasting.

De reactie voor een gesegmenteerde vermelding omvat het volgende:

* *_segment*, dat de set resultaten bevat die voor één aanroep naar de listing-API wordt geretourneerd.
* *continuation_token*, dat wordt door gegeven aan de volgende aanroep om de volgende pagina met resultaten op te halen. Wanneer er geen resultaten meer zijn om te retour neren, is het vervolg token null.

Zo kan een typische aanroep voor het weer geven van een lijst met alle blobs in een container eruitzien zoals in het volgende code fragment. De code is beschikbaar in onze voor [beelden](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted.cpp):

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Houd er rekening mee dat het aantal geretourneerde resultaten op een pagina kan worden bepaald door de para meter *max_results* in de overbelasting van elke API, bijvoorbeeld:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Als u de para meter *max_results* niet opgeeft, wordt de standaard waarde van maxi maal 5000 resultaten geretourneerd op één pagina.

Het is ook mogelijk dat een query op Azure Table Storage geen records retourneert, of dat er minder records zijn dan de waarde van de para meter *max_results* die u hebt opgegeven, zelfs als het vervolg token niet leeg is. Een mogelijke reden is dat de query niet binnen vijf seconden kan worden voltooid. Zolang het vervolg token niet leeg is, moet de query worden voortgezet en mag de code niet de grootte van de segment resultaten aannemen.

Het aanbevolen coderings patroon voor de meeste scenario's is een gesegmenteerde vermelding, die een expliciete voortgang van de vermelding of het uitvoeren van query's biedt, en hoe de service op elke aanvraag reageert. Met name voor C++-toepassingen of-services kan het geheugen en de prestaties worden beheerd door de controle op lagere niveaus van de voortgang van de vermelding.

## <a name="greedy-listing"></a>Greedy-aanbieding

Eerdere versies van de Storage-client bibliotheek voor C++ (versies 0.5.0 Preview en eerder) bevatten niet-gesegmenteerde vermelding-Api's voor tabellen en wacht rijen, zoals in het volgende voor beeld:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Deze methoden zijn geïmplementeerd als wrappers van gesegmenteerde Api's. Voor elke reactie van een gesegmenteerde vermelding voegt de code de resultaten toe aan een vector en worden alle resultaten geretourneerd nadat de volledige containers zijn gescand.

Deze benadering kan worden gebruikt als het opslag account of de tabel een klein aantal objecten bevat. Met een toename van het aantal objecten kan het vereiste geheugen echter zonder limiet worden verhoogd, omdat alle resultaten in het geheugen aanwezig zijn. Eén vermelding kan enige tijd in beslag nemen, waarbij de aanroeper geen informatie over de voortgang heeft.

Deze Greedy-weer gegeven Api's in de SDK bestaan niet in C#, Java of de Java script-Node.js omgeving. We hebben deze in versie 0.6.0 Preview verwijderd om te voor komen dat er problemen zijn met het gebruik van deze Greedy-Api's.

Als uw code deze Greedy-Api's aanroept:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Vervolgens moet u uw code wijzigen voor het gebruik van de gesegmenteerde vermelding-Api's:

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

Door de para meter *max_results* van het segment op te geven, kunt u balanceren tussen het aantal aanvragen en het geheugen gebruik om te voldoen aan de prestatie overwegingen voor uw toepassing.

Als u gebruikmaakt van gesegmenteerde vermeldings-Api's, maar de gegevens in een lokale verzameling opslaat in een ' Greedy-stijl, raden we u ook ten zeerste aan uw code te herwaarderen om het opslaan van gegevens in een lokale verzameling zorgvuldig op schaal te verwerken.

## <a name="lazy-listing"></a>Luie vermelding

Hoewel Greedy een lijst met mogelijke problemen heeft gegenereerd, is het handig als er niet te veel objecten in de container staan.

Als u ook gebruikmaakt van C#-of Oracle Java-Sdk's, moet u bekend zijn met het inkomend programmeer model, dat een lijst met luie stijlen biedt, waarbij de gegevens bij een bepaalde offset alleen worden opgehaald als dat vereist is. In C++ biedt de op iterator gebaseerde sjabloon ook een soort gelijke benadering.

Een typische API voor een luie lijst met **list_blobs** als voor beeld ziet er als volgt uit:

```cpp
list_blob_item_iterator list_blobs() const;
```

Een typisch code fragment dat gebruikmaakt van het vertraagde vermelding patroon kan er als volgt uitzien:

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

Houd er rekening mee dat Lazy list alleen beschikbaar is in de synchrone modus.

Vergeleken met de vermelding Greedy haalt de luie lijst alleen gegevens op als dat nodig is. Onder de kaften worden gegevens uit Azure Storage alleen opgehaald wanneer de volgende iterator wordt verplaatst naar het volgende segment. Het geheugen gebruik wordt daarom geregeld met een beperkte grootte en de bewerking is snel.

In de Storage-client bibliotheek voor C++ in versie 2.2.0 worden de API voor Lazy-vermeldingen opgenomen.

## <a name="conclusion"></a>Conclusie

In dit artikel hebben we verschillende Overloads besproken voor het weer geven van Api's voor verschillende objecten in de Storage-client bibliotheek voor C++. Samenvatting:

* Asynchrone Api's worden sterk aanbevolen onder meerdere threading-scenario's.
* Een gesegmenteerde vermelding wordt aanbevolen voor de meeste scenario's.
* De vertraagde vermelding is in de bibliotheek opgenomen als een handige wrapper in synchrone scenario's.
* De Greedy-aanbieding wordt niet aanbevolen en is uit de bibliotheek verwijderd.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over Azure Storage en de client bibliotheek voor C++.

* [Blob Storage gebruiken vanuit C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Table Storage gebruiken vanuit C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Queue Storage gebruiken met C++](../queues/storage-c-plus-plus-how-to-use-queues.md)
* [Azure Storage-client bibliotheek voor C++ API-documentatie.](https://azure.github.io/azure-storage-cpp/)
* [Blog van het Azure Storage-team](/archive/blogs/windowsazurestorage/)
* [Documentatie over Azure Storage](https://azure.microsoft.com/documentation/services/storage/)