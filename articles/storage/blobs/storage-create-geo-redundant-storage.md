---
title: 'Zelfstudie: een maximaal beschikbare toepassing bouwen met Blob-opslag'
titleSuffix: Azure Storage
description: Geografisch zone-redundante opslag (RA-GZRS) met leestoegang gebruiken om uw toepassingsgegevens maximaal beschikbaar te maken.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc, devx-track-python, devx-track-js, devx-track-csharp
ms.subservice: blobs
ms.openlocfilehash: 0d597f0742cfc43f1c7fb38568b2a2bbda352beb
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049335"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Zelfstudie: Een maximaal beschikbare toepassing bouwen met Blob-opslag

Deze zelfstudie is deel één van een serie. In deze zelfstudie leert u hoe u uw toepassingsgegevens maximaal beschikbaar maakt in Azure.

Wanneer u deze zelfstudie hebt afgerond, hebt u een consoletoepassing die een blob uploadt en ophaalt uit een [geografisch zone-redundant](../common/storage-redundancy.md) opslagaccount met leestoegang (RA-GZRS).

Met geo-redundantie in Azure Storage worden transacties asynchroon gerepliceerd van een primaire regio naar een secundaire regio die zich op honderden kilometers afstand bevindt. Dit replicatieproces zorgt ervoor dat de gegevens in de secundaire regio uiteindelijk consistent zijn. De consoletoepassing gebruikt het [circuitonderbreker](/azure/architecture/patterns/circuit-breaker)patroon om te bepalen met welk eindpunt er verbinding moet worden gemaakt, waarbij er automatisch wordt overgeschakeld tussen eindpunten terwijl er fouten en herstelbewerkingen worden gesimuleerd.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

In deel 1 van de reeks leert u het volgende:

> [!div class="checklist"]
> * Create a storage account
> * De verbindingsreeks instellen
> * De consoletoepassing uitvoeren

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

* [Visual Studio 2019](https://www.visualstudio.com/downloads/) installeren met de **Azure-ontwikkelworkload**.

  ![Azure-ontwikkeling (onder Web en Cloud)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="python-v12"></a>[Python-V12](#tab/python)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

* [Python](https://www.python.org/downloads/) installeren
* [Azure Storage-SDK voor Python](https://github.com/Azure/azure-storage-python) downloaden en installeren

# <a name="nodejs-v12"></a>[Node.js V12](#tab/nodejs)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="nodejs-v11"></a>[Node.js V11](#tab/nodejs11)

* [Node.js](https://nodejs.org) installeren.

---

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Create a storage account

Een opslagaccount biedt een unieke naamruimte voor het opslaan en openen van uw Azure Storage-gegevensobjecten.

Volg deze stappen om een account voor geografisch zone-redundante (RA-GZRS) opslag met leestoegang te maken:

1. Selecteer de knop **Een resource maken** in Azure Portal.
2. Selecteer **Opslagaccount: blob, bestand, tabel, wachtrij** op de pagina **Nieuw**.
4. Vul het formulier voor het opslagaccount in met de informatie uit de volgende afbeelding en selecteer **Maken**:

   | Instelling       | Voorbeeldwaarde | Beschrijving |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Abonnement** | *Mijn abonnement* | Zie [Abonnementen](https://account.azure.com/Subscriptions) voor meer informatie over uw abonnementen. |
   | **ResourceGroup** | *myResourceGroup* | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige resourcegroepnamen. |
   | **Naam** | *mystorageaccount* | Een unieke naam in voor uw opslagaccount. |
   | **Locatie** | *VS - oost* | Kies een locatie. |
   | **Prestaties** | *Standard* | Prestaties van het type Standard zijn een goede optie voor het voorbeeldscenario. |
   | **Type account** | *StorageV2* | Het gebruik van een v2-opslagaccount voor algemeen gebruik wordt aanbevolen. Zie [Overzicht van opslagaccounts](../common/storage-account-overview.md) voor meer informatie over typen Azure-opslagaccounts. |
   | **Replicatie**| *Leestoegang tot geografische zone-redundante opslag (RA-GZRS)* | De primaire regio is zone-redundant en wordt gerepliceerd naar een secundaire regio, waarbij leestoegang tot de secundaire regio is ingeschakeld. |
   | **Toegangslaag**| *Dynamisch* | Gebruik de dynamische laag voor gegevens die vaak worden gebruikt. |

    ![opslagaccount maken](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Het voorbeeld downloaden

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

[Download het voorbeeldproject](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) en pak (unzip) het bestand storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip uit. U kunt ook [git](https://git-scm.com/) gebruiken om een kopie van de toepassing te downloaden naar uw ontwikkelomgeving. Het voorbeeldproject bevat een consoletoepassing.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="python-v12"></a>[Python-V12](#tab/python)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

[Download het voorbeeldproject](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) en pak (unzip) het bestand storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip uit. U kunt ook [git](https://git-scm.com/) gebruiken om een kopie van de toepassing te downloaden naar uw ontwikkelomgeving. Het voorbeeldproject bevat een basis Python-toepassing.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejs-v12"></a>[Node.js V12](#tab/nodejs)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="nodejs-v11"></a>[Node.js V11](#tab/nodejs11)

[Download het voorbeeldproject](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) en pak het bestand uit. U kunt ook [git](https://git-scm.com/) gebruiken om een kopie van de toepassing te downloaden naar uw ontwikkelomgeving. Het voorbeeldproject bevat een eenvoudige Node.js-toepassing.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>Voorbeeld configureren

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

In de toepassing moet u de verbindingsreeks voor uw opslagaccount opgeven. U kunt deze verbindingsreeks opslaan in een omgevingsvariabele op de lokale computer waarop de toepassing wordt uitgevoerd. Volg een van de onderstaande voorbeelden afhankelijk van uw besturingssysteem voor het maken van de omgevingsvariabele.

Ga in Azure Portal naar uw opslagaccount. Selecteer bij **Instellingen** in uw opslagaccount de optie **Toegangssleutels**. Kopieer de **verbindingsreeks** uit de primaire of secundaire sleutel. Voer een van de volgende opdrachten uit afhankelijk van uw besturingssysteem waarbij u \<yourconnectionstring\> vervangt door uw werkelijke verbindingsreeks. Met deze opdracht slaat u een omgevingsvariabele naar de lokale machine op. In Windows is de omgevingsvariabele pas beschikbaar wanneer u de **opdrachtprompt** of shell die u gebruikt opnieuw laadt.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="python-v12"></a>[Python-V12](#tab/python)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

In de toepassing moet u de referenties voor uw opslagaccount opgeven. U kunt deze gegevens opslaan in omgevingsvariabelen op de lokale computer waarop de toepassing wordt uitgevoerd. Volg afhankelijk van uw besturingssysteem een van de onderstaande voorbeelden voor het maken van de omgevingsvariabelen.

Ga in Azure Portal naar uw opslagaccount. Selecteer bij **Instellingen** in uw opslagaccount de optie **Toegangssleutels**. Plak de **naam van het opslagaccount** en de waarden voor de **sleutel** in de volgende opdrachten, waarbij u de tijdelijke aanduidingen \<youraccountname\> en \<youraccountkey\> vervangt. Met deze opdracht slaat u de omgevingsvariabelen op de lokale machine op. In Windows is de omgevingsvariabele pas beschikbaar wanneer u de **opdrachtprompt** of shell die u gebruikt opnieuw laadt.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejs-v12"></a>[Node.js V12](#tab/nodejs)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="nodejs-v11"></a>[Node.js V11](#tab/nodejs11)

Als u dit voorbeeld wilt uitvoeren, moet u de referenties van uw opslagaccount toevoegen aan het `.env.example`-bestand en de naam vervolgens wijzigen in `.env`.

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

U kunt deze informatie vinden in de Azure Portal door te navigeren naar uw opslagaccount en **Toegangssleutels** te selecteren in de sectie **Instellingen**.

Installeer de vereiste afhankelijkheden. Hiertoe opent u een opdrachtprompt, navigeert u naar de voorbeeldmap en voert u `npm install` in.

---

## <a name="run-the-console-application"></a>De consoletoepassing uitvoeren

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Druk in Visual Studio op **F5** of selecteer **Start** om foutopsporing van de toepassing te starten. Visual Studio herstelt automatisch ontbrekende NuGet-pakketten als dit zo is geconfigureerd. Ga naar [Installing and reinstalling packages with package restore](/nuget/consume-packages/package-restore#package-restore-overview) (Pakketten installeren en opnieuw installeren met pakketherstel) voor meer informatie.

Er wordt een consolevenster geopend en de toepassing wordt gestart. De toepassing uploadt de afbeelding **HelloWorld.png** vanuit de oplossing naar het opslagaccount. De toepassing controleert of de afbeelding is gerepliceerd naar het secundaire RA-GZRS-eindpunt. Daarna wordt de afbeelding maximaal 999 keer gedownload. Elke leesbewerking wordt voorgesteld door een **P** of een **S**. **P** staat voor het primaire eindpunt en **S** voor het secundaire eindpunt.

![Console-app die wordt uitgevoerd](media/storage-create-geo-redundant-storage/figure3.png)

In de voorbeeldcode wordt de taak `RunCircuitBreakerAsync` in het bestand `Program.cs` gebruikt om een afbeelding met behulp van de [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync)-methode te downloaden vanuit het opslagaccount. Voorafgaand aan de download is een [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext) gedefinieerd. De bewerkingscontext definieert de gebeurtenis-handlers. Deze worden geactiveerd wanneer een download is voltooid of wanneer een download is mislukt en opnieuw wordt uitgevoerd.

# <a name="python-v12"></a>[Python-V12](#tab/python)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

Als u de toepassing wilt uitvoeren op een terminal of opdrachtprompt, gaat u naar de map **circuitbreaker.py** en voert u `python circuitbreaker.py` in. De toepassing uploadt de afbeelding **HelloWorld.png** vanuit de oplossing naar het opslagaccount. De toepassing controleert of de afbeelding is gerepliceerd naar het secundaire RA-GZRS-eindpunt. Daarna wordt de afbeelding maximaal 999 keer gedownload. Elke leesbewerking wordt voorgesteld door een **P** of een **S**. **P** staat voor het primaire eindpunt en **S** voor het secundaire eindpunt.

![Console-app die wordt uitgevoerd](media/storage-create-geo-redundant-storage/figure3.png)

In de voorbeeldcode wordt de methode `run_circuit_breaker` in het bestand `circuitbreaker.py` gebruikt om een afbeelding met behulp van de [get_blob_to_path](/python/api/azure-storage-blob/azure.storage.blob.baseblobservice.baseblobservice#get-blob-to-path-container-name--blob-name--file-path--open-mode--wb---snapshot-none--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--lease-id-none--if-modified-since-none--if-unmodified-since-none--if-match-none--if-none-match-none--timeout-none-)-methode te downloaden vanuit het opslagaccount.

De functie Opnieuw van het opslagobject is ingesteld op een lineair beleid voor nieuwe pogingen. De functie Opnieuw bepaalt of een aanvraag opnieuw moet worden geprobeerd en geeft het aantal seconden op dat moet worden gewacht voordat opnieuw wordt geprobeerd de aanvraag uit te voeren. Stel de waarde van **retry\_to\_secondary** in op True als de aanvraag bij een volgende poging worden uitgevoerd naar het secundaire eindpunt als de eerste aanvraag naar het primaire eindpunt is mislukt. In de voorbeeldtoepassing is een aangepast beleid voor nieuwe pogingen gedefinieerd in de functie `retry_callback` van het opslagobject.

Vóór het downloaden zijn de functies [retry_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient) en [response_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient) van het serviceobject gedefinieerd. Deze functies definiëren gebeurtenis-handlers die worden geactiveerd wanneer een download is voltooid of wanneer een download is mislukt en opnieuw wordt uitgevoerd.

# <a name="nodejs-v12"></a>[Node.js V12](#tab/nodejs)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="nodejs-v11"></a>[Node.js V11](#tab/nodejs11)

Als u het voorbeeld wilt uitvoeren, opent u een opdrachtprompt, navigeert u naar de voorbeeldmap en voert u `node index.js` in.

In het voorbeeld wordt een container gemaakt in uw Blob-opslagaccount, wordt **HelloWorld. png** in de container geüpload en wordt herhaaldelijk gecontroleerd of de container en de installatiekopie naar de secundaire regio zijn gerepliceerd. Na de replicatie wordt u gevraagd **D** of **Q** (gevolgd door Enter) in te voeren om te downloaden of af te sluiten. Uw uitvoer moet er ongeveer uitzien als de uitvoer in het volgende voorbeeld:

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>De voorbeeldcode begrijpen

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

### <a name="retry-event-handler"></a>Gebeurtenis-handler opnieuw proberen

De gebeurtenis-handler `OperationContextRetrying` wordt aangeroepen wanneer het downloaden van de afbeelding is mislukt en is ingesteld op Opnieuw proberen. Als het maximale aantal nieuwe pogingen dat in de toepassing is gedefinieerd, is bereikt, verandert de [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) van de aanvraag in `SecondaryOnly`. Deze instelling zorgt ervoor dat de toepassing de afbeelding probeert te downloaden van het secundaire eindpunt. Deze configuratie vermindert de tijd die nodig is om de afbeelding op te vragen omdat het primaire eindpunt niet oneindig opnieuw wordt geprobeerd.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Gebeurtenis-handler Aanvraag voltooid

De gebeurtenis-handler `OperationContextRequestCompleted` wordt aangeroepen wanneer het downloaden van de afbeelding is geslaagd. Als de toepassing het secundaire eindpunt gebruikt, blijft de toepassing dit eindpunt maximaal 20 keer gebruiken. Na 20 keer stelt de toepassing de [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) weer in op `PrimaryThenSecondary` en wordt het primaire eindpunt weer geprobeerd. Als een aanvraag is geslaagd, blijft de toepassing van het primaire eindpunt lezen.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

# <a name="python-v12"></a>[Python-V12](#tab/python)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="python-v21"></a>[Python v 2.1](#tab/python2)

### <a name="retry-event-handler"></a>Gebeurtenis-handler opnieuw proberen

De gebeurtenis-handler `retry_callback` wordt aangeroepen wanneer het downloaden van de afbeelding is mislukt en is ingesteld op Opnieuw proberen. Als het maximale aantal nieuwe pogingen dat in de toepassing is gedefinieerd, is bereikt, verandert de [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode) van de aanvraag in `SECONDARY`. Deze instelling zorgt ervoor dat de toepassing de afbeelding probeert te downloaden van het secundaire eindpunt. Deze configuratie vermindert de tijd die nodig is om de afbeelding op te vragen omdat het primaire eindpunt niet oneindig opnieuw wordt geprobeerd.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Gebeurtenis-handler Aanvraag voltooid

De gebeurtenis-handler `response_callback` wordt aangeroepen wanneer het downloaden van de afbeelding is geslaagd. Als de toepassing het secundaire eindpunt gebruikt, blijft de toepassing dit eindpunt maximaal 20 keer gebruiken. Na 20 keer stelt de toepassing de [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode) weer in op `PRIMARY` en wordt het primaire eindpunt weer geprobeerd. Als een aanvraag is geslaagd, blijft de toepassing van het primaire eindpunt lezen.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

# <a name="nodejs-v12"></a>[Node.js V12](#tab/nodejs)

We werken momenteel om code fragmenten te maken die overeenkomen met versie 12. x van de Azure Storage-client bibliotheken. Zie [de Azure Storage V12-client bibliotheken aankondigen](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)voor meer informatie.

# <a name="nodejs-v11"></a>[Node.js V11](#tab/nodejs11)

Met de Node.js V10 SDK zijn callbackhandlers overbodig. In plaats daarvan wordt in het voorbeeld een pijplijn gemaakt die is geconfigureerd met opties voor opnieuw proberen en een secundair eindpunt. Hierdoor kan de toepassing automatisch overschakelen naar de secundaire pijplijn als deze de gegevens niet kan bereiken via de primaire pijplijn.

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>Volgende stappen

In deel een van de reeks hebt u geleerd hoe u een toepassing maximaal beschikbaar maakt met RA-GZRS-opslagaccounts.

Ga naar deel twee van de serie voor informatie over hoe u een fout simuleert en uw toepassing dwingt om het secundaire RA-GZRS-eindpunt te gebruiken.

> [!div class="nextstepaction"]
> [Een fout simuleren bij lezen uit de primaire regio](simulate-primary-region-failure.md)