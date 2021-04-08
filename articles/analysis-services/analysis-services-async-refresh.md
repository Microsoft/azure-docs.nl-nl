---
title: Asynchroon vernieuwen voor Azure Analysis Services modellen | Microsoft Docs
description: Hierin wordt beschreven hoe u de Azure Analysis Services REST API gebruikt voor het coderen van asynchrone vernieuwing van model gegevens.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: e9fd20fd42e9fe1eb0e98766798e5c759c974c97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92013896"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Asynchroon vernieuwen met de REST API

Door gebruik te maken van elke programmeer taal die REST-aanroepen ondersteunt, kunt u asynchrone bewerkingen voor het vernieuwen van gegevens uitvoeren op uw Azure Analysis Services modellen in tabel vorm. Dit omvat synchronisatie van alleen-lezen replica's voor het uitbreiden van de query. 

Het vernieuwen van gegevens kan enige tijd in beslag nemen, afhankelijk van een aantal factoren, zoals het gegevens volume, het optimalisatie niveau met behulp van partities, enzovoort. Deze bewerkingen zijn traditioneel aangeroepen met bestaande methoden, zoals het gebruik van [Tom](/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (Tabellair object model), [Power shell](/analysis-services/powershell/analysis-services-powershell-reference) -cmdlets of [TMSL](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (tabellaire model script taal). Deze methoden kunnen echter vaak onbetrouwbare, langlopende HTTP-verbindingen vereisen.

Met de REST API voor Azure Analysis Services kunnen bewerkingen voor gegevens vernieuwing asynchroon worden uitgevoerd. Met behulp van de REST API kunnen langlopende HTTP-verbindingen van client toepassingen niet nodig zijn. Er zijn ook andere ingebouwde functies voor betrouw baarheid, zoals automatische nieuwe pogingen en batch doorvoer.

## <a name="base-url"></a>Basis-URL

De basis-URL heeft de volgende indeling:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Denk bijvoorbeeld aan een model met de naam AdventureWorks op een server `myserver` met de naam, die zich bevindt in de Azure-regio West vs. De server naam is:

```
asazure://westus.asazure.windows.net/myserver 
```

De basis-URL voor deze server naam is:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Met behulp van de basis-URL kunnen resources en bewerkingen worden toegevoegd op basis van de volgende para meters: 

![Asynchroon vernieuwen](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Alles wat in **s** eindigt, is een verzameling.
- Alles wat eindigt met **()** is een functie.
- Iets anders is een resource/object.

U kunt bijvoorbeeld de bewerking POST in de verzameling vernieuwen gebruiken om een vernieuwings bewerking uit te voeren:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Verificatie

Alle aanroepen moeten worden geverifieerd met een geldig Azure Active Directory (OAuth 2)-token in de autorisatie-header en moeten voldoen aan de volgende vereisten:

- Het token moet een gebruikers token of een service-principal voor de toepassing zijn.
- Voor het token moet de juiste doel groep zijn ingesteld op `https://*.asazure.windows.net` .
- De gebruiker of toepassing moet voldoende machtigingen hebben op de server of het model om de aangevraagde aanroep te kunnen uitvoeren. Het machtigings niveau wordt bepaald door rollen in het model of de groep Administrators op de server.

    > [!IMPORTANT]
    > Op dit moment zijn er machtigingen voor de **Server** beheerdersrol vereist.

## <a name="post-refreshes"></a>/Refreshes plaatsen

Als u een vernieuwings bewerking wilt uitvoeren, gebruikt u de bewerking POST in de verzameling/refreshes om een nieuw vernieuwings item toe te voegen aan de verzameling. De locatie header in het antwoord bevat de vernieuwings-ID. De client toepassing kan de verbinding verbreken en de status later indien nodig controleren, omdat deze asynchroon is.

Er wordt slechts één vernieuwings bewerking geaccepteerd per keer voor een model. Als er een actieve vernieuwings bewerking wordt uitgevoerd en er een andere wordt verzonden, wordt de HTTP-status code van 409-conflicten geretourneerd.

De hoofd tekst kan er als volgt uitzien:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Parameters

Het opgeven van para meters is niet vereist. De standaard waarde wordt toegepast.

| Naam             | Type  | Description  |Standaard  |
|------------------|-------|--------------|---------|
| `Type`           | Enum  | Het type verwerking dat moet worden uitgevoerd. De typen zijn afgestemd op de TMSL- [vernieuwings opdracht](/analysis-services/tmsl/refresh-command-tmsl) typen: Full, clearValues, Calculate, dataOnly, Automatic en defragmenteren. Het type toevoegen wordt niet ondersteund.      |   automatisch      |
| `CommitMode`     | Enum  | Bepaalt of objecten worden doorgevoerd in batches of alleen wanneer dit is voltooid. Voor beelden zijn: standaard, transactioneel, partialBatch.  |  transactionele       |
| `MaxParallelism` | Int   | Deze waarde bepaalt het maximum aantal threads waarop verwerkings opdrachten parallel moeten worden uitgevoerd. Deze waarde is afgestemd op de eigenschap MaxParallelism die kan worden ingesteld in de [opdracht TMSL sequence](/analysis-services/tmsl/sequence-command-tmsl) of met behulp van andere methoden.       | 10        |
| `RetryCount`     | Int   | Hiermee wordt het aantal keren aangegeven dat de bewerking opnieuw wordt uitgevoerd voordat er een fout optreedt.      |     0    |
| `Objects`        | Matrix | Een matrix met objecten die moeten worden verwerkt. Elk object bevat: ' tabel ' bij het verwerken van de volledige tabel of ' tabel ' en ' partitie ' bij het verwerken van een partitie. Als er geen objecten zijn opgegeven, wordt het hele model vernieuwd. |   Het volledige model verwerken      |

CommitMode is gelijk aan partialBatch. Dit wordt gebruikt bij het uitvoeren van een initiële belasting van grote gegevens sets die uren kunnen duren. Als de vernieuwings bewerking mislukt nadat een of meer batches zijn doorgevoerd, blijven de doorgevoerde batches doorgevoerd (de doorgevoerde batches kunnen niet worden hersteld).

> [!NOTE]
> De Batch grootte is op het moment van schrijven de MaxParallelism-waarde, maar deze waarde zou kunnen veranderen.

### <a name="status-values"></a>Status waarden

|Statuswaarde  |Description  |
|---------|---------|
|`notStarted`    |   De bewerking is nog niet gestart.      |
|`inProgress`     |   De bewerking wordt uitgevoerd.      |
|`timedOut`     |    Er is een time-out opgetreden voor de bewerking op basis van de opgegeven time     |
|`cancelled`     |   De bewerking is geannuleerd door de gebruiker of het systeem.      |
|`failed`     |   De bewerking is mislukt.      |
|`succeeded`      |   De bewerking is voltooid.      |

## <a name="get-refreshesrefreshid"></a>/Refreshes/ophalen\<refreshId>

Als u de status van een vernieuwings bewerking wilt controleren, gebruikt u de bewerking GET bij de vernieuwings-ID. Hier volgt een voor beeld van de hoofd tekst van het antwoord. Als de bewerking wordt uitgevoerd, `inProgress` wordt de status geretourneerd.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>/Refreshes ophalen

Als u een lijst met historische vernieuwings bewerkingen voor een model wilt weer geven, gebruikt u de bewerking GET in de/refreshes-verzameling. Hier volgt een voor beeld van de hoofd tekst van het antwoord. 

> [!NOTE]
> Op het moment van schrijven, worden de laatste 30 dagen van de vernieuwings bewerkingen opgeslagen en geretourneerd, maar dit aantal kan veranderen.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-07T02:06:57.1838734Z",
        "endTime": "2017-12-07T02:07:00.4929675Z",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T01:05:54.157324Z",
        "endTime": "2017-12-07T01:05:57.353371Z",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>/Refreshes/verwijderen\<refreshId>

Als u een vernieuwings bewerking in voortgang wilt annuleren, gebruikt u de bewerking DELETE bij de ID vernieuwen.

## <a name="post-sync"></a>/Sync plaatsen

Als u een vernieuwings bewerking hebt uitgevoerd, kan het nodig zijn om de nieuwe gegevens te synchroniseren met replica's voor het uitbreiden van de query. Als u een synchronisatie bewerking voor een model wilt uitvoeren, gebruikt u de bewerking POST in de functie/Sync. De locatie header in het antwoord bevat de synchronisatie bewerkings-ID.

## <a name="get-sync-status"></a>/Sync-status ophalen

Als u de status van een synchronisatie bewerking wilt controleren, gebruikt u de bewerking GET die de bewerkings-ID door geven als een para meter. Hier volgt een voor beeld van de hoofd tekst van de reactie:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Waarden voor `syncstate` :

- 0: repliceren. Database bestanden worden gerepliceerd naar een doelmap.
- 1: reactiveren. De data base wordt opnieuw gehydrateerd op alleen-lezen Server exemplaar (en).
- 2: voltooid. De synchronisatie bewerking is voltooid.
- 3: mislukt. De synchronisatie bewerking is mislukt.
- 4: volt ooien. De synchronisatie bewerking is voltooid, maar de stappen voor het opschonen worden uitgevoerd.

## <a name="code-sample"></a>Codevoorbeeld

Hier volgt een voor beeld van een C#-code om aan de slag te gaan, [RestApiSample op github](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Het code voorbeeld gebruiken

1.    Kloon of down load de opslag plaats. Open de RestApiSample-oplossing.
2.    Zoek de regel- **client. BaseAddress =...** en geef uw [basis-URL](#base-url)op.

Het code voorbeeld maakt gebruik van [Service-Principal](#service-principal) -verificatie.

### <a name="service-principal"></a>Service-principal

Zie [Service-Principal maken-Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md) en [een Service-Principal toevoegen aan de rol Server beheerder](analysis-services-addservprinc-admins.md) voor meer informatie over het instellen van een Service-Principal en het toewijzen van de benodigde machtigingen in azure als. Nadat u de stappen hebt voltooid, voert u de volgende aanvullende stappen uit:

1.    Zoek in het code voorbeeld naar **String Authority =...**, vervang **common** door de Tenant-id van uw organisatie.
2.    Opmerking/Opmerking opheffen, zodat de klasse ClientCredential wordt gebruikt om het cred-object te instantiëren. Zorg ervoor dat de \<App ID> en- \<App Key> waarden op een veilige manier toegankelijk zijn of gebruik verificatie op basis van certificaten voor service-principals.
3.    Voet het voorbeeld uit.


## <a name="see-also"></a>Zie ook

[Voor beelden](analysis-services-samples.md)   
[REST API](/rest/api/analysisservices/servers)