---
title: 'Quickstart: Gebeurtenissen verzenden en ontvangen met Go - Azure Event Hubs'
description: 'Quickstart: Dit artikel bevat een overzicht voor het maken van een Go-toepassing die gebeurtenissen verzendt vanuit Azure Event Hubs.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: ededd042dadd6059bd7b2d689cff78fe1bd6af0b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311087"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>Quickstart: Gebeurtenissen verzenden of gebeurtenissen ontvangen uit Event Hubs met behulp van Go
Azure Event Hubs is een big data-platform voor het streamen van gegevens en een gebeurtenisopneemservice die miljoenen gebeurtenissen per seconde kan opnemen en verwerken. Event Hubs kan gebeurtenissen, gegevens of telemetrie die wordt geproduceerd door gedistribueerde software en apparaten verwerken en opslaan. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen via een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Zie [Overzicht van Event Hubs](event-hubs-about.md) en [Functies van Event Hubs](event-hubs-features.md) voor een gedetailleerd overzicht van Event Hubs.

In deze zelfstudie wordt beschreven hoe u Go-toepassingen schrijft voor het verzenden van gebeurtenissen naar of het ontvangen van gebeurtenissen van een Event Hub. 

> [!NOTE]
> U kunt deze snelstart als voorbeeld downloaden van de [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), de tekenreeksen `EventHubConnectionString` en `EventHubName` vervangen door uw event hub-waarden en deze uitvoeren. U kunt ook de stappen in deze zelfstudie volgen om uw eigen oplossing te maken.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

- Go is lokaal geïnstalleerd. Volg [deze instructies](https://golang.org/doc/install) indien nodig.
- Een actief Azure-account. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account][] aan voordat u begint.
- **Een Event Hubs-naamruimte en een Event Hub maken**. Gebruik de [Azure-portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken.

## <a name="send-events"></a>Gebeurtenissen verzenden
In deze sectie wordt beschreven hoe u een Go-toepassing maakt voor het verzenden van gebeurtenissen naar een Event Hub. 

### <a name="install-go-package"></a>Go-pakket installeren

Download het Go-pakket voor Event Hubs met `go get` of `dep`. Bijvoorbeeld:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Pakketten in uw codebestand importeren

Als u de Go-pakketten wilt importeren, gebruikt u het volgende codevoorbeeld:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Een service-principal maken

Maak een nieuwe service-principal door de instructies in [Een Azure-service-principal maken met Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Sla de gegeven referenties op in uw omgeving met de volgende namen. Zowel de Azure SDK voor Go als de Event Hubs-pakketten zijn vooraf geconfigureerd om te zoeken naar deze namen van variabelen:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Maak nu een autorisatieprovider voor uw Event Hubs-client die gebruikmaakt van deze referenties:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Event Hubs-client maken

Met de volgende code wordt een Event Hubs-client gemaakt:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Code schrijven om berichten te verzenden

Gebruik in het volgende codefragment (1) om berichten interactief te verzenden vanuit een terminal of (2) om berichten te verzenden binnen uw programma:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Extra's

Haal de id's van de partities op in uw Event Hub:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Voer de toepassing uit om gebeurtenissen naar de Event Hub te verzenden. 

Gefeliciteerd! U hebt nu berichten verzonden naar een Event Hub.

## <a name="receive-events"></a>Gebeurtenissen ontvangen

### <a name="create-a-storage-account-and-container"></a>Een opslagaccount en container maken

De status, zoals leases op partities en controlepunten in de gebeurtenisstroom, wordt gedeeld tussen ontvangers die gebruikmaken van een Azure Storage-container. U kunt een opslagaccount en container maken met de Go-SDK, maar u kunt er ook een maken door de instructies te volgen in [Over Azure-opslagaccounts](../storage/common/storage-account-create.md).

Voorbeelden voor het maken van opslagartefacten met de Go SDK zijn beschikbaar in de [Opslagplaats met Go-voorbeelden](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) en in het voorbeeld dat overeenkomt met deze zelfstudie.

### <a name="go-packages"></a>Go-pakketten

Als u de berichten wilt ontvangen, kunt u de Go-pakketten ophalen voor Event Hubs met `go get` of `dep`:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Pakketten in uw codebestand importeren

Als u de Go-pakketten wilt importeren, gebruikt u het volgende codevoorbeeld:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>Een service-principal maken

Maak een nieuwe service-principal door de instructies in [Een Azure-service-principal maken met Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Sla de gegeven referenties op in uw omgeving met de volgende namen: Zowel Azure SDK voor Go als Event Hubs-pakketten zijn vooraf geconfigureerd om te zoeken naar deze namen van variabelen.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Maak vervolgens een autorisatieprovider voor uw Event Hubs-client die gebruikmaakt van deze referenties:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Struct met metagegevens ophalen

Maak een struct met metagegevens over uw Azure-omgeving met behulp van de Azure Go SDK. Latere bewerkingen gebruiken deze struct om juiste eindpunten te vinden.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Referentiehelper maken 

Maak een referentiehelper die gebruikmaakt van de referenties van de vorige Azure Active Directory (AAD) om een Shared Access Signature (SAS)-referentie voor Storage te maken. De laatste parameter vertelt deze constructor om dezelfde omgevingsvariabelen te gebruiken zoals eerder gebruikt:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>Een controlepunt en een leaser maken 

Maak een **leaser**, die verantwoordelijk is voor het leasen van een partitie aan een bepaalde ontvanger en een **controlepunt**, verantwoordelijk voor het schrijven van controlepunten voor de berichtenstroom, zodat andere ontvangers kunnen beginnen met het lezen van de juiste offset.

Op dit moment is er één **StorageLeaserCheckpointer** beschikbaar die dezelfde opslagcontainer gebruikt voor het beheren van zowel leases als controlepunten. Naast het opslagaccount en de namen van de containers heeft de **StorageLeaserCheckpointer** de referentie die in de vorige stap is gemaakt en de Azure-omgevingsstruct nodig om op de juiste wijze toegang te krijgen tot de container.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>Event Processor Host samenstellen

U hebt nu de volgende onderdelen nodig om een Event Processor Host te maken. Dezelfde **StorageLeaserCheckpointer** wordt gebruikt als een leaser en een controlepunt, zoals eerder is beschreven:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>Handler maken 

Maak nu een handler en registreer deze bij de host van de gebeurtenisprocessor. Wanneer de host wordt gestart, worden deze en andere opgegeven handlers toegepast op binnenkomende berichten:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>Code schrijven om berichten te ontvangen

Als alles is ingesteld, kunt u de EventProcessorHost starten met `Start(context)` zodat deze permanent wordt uitgevoerd, of met `StartNonBlocking(context)` om alleen te worden uitgevoerd zolang er berichten beschikbaar zijn.

Deze zelfstudie wordt gestart en wordt als volgt uitgevoerd: zie het GitHub-voorbeeld voor een voorbeeld met `StartNonBlocking`:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event Hubs](event-hubs-features.md)
- [Veelgestelde vragen over Event Hubs](event-hubs-faq.yml)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[gratis account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
