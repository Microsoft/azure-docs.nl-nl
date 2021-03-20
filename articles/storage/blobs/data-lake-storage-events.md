---
title: 'Zelfstudie: Het Data Lake Capture-patroon implementeren om een Azure Databricks Delta-tabel bij te werken | Microsoft Docs'
description: In deze zelfstudie leert u hoe u met een Event Grid-abonnement, een Azure-functie en een Azure Databricks-taak rijen met gegevens kunt invoegen in een tabel die is opgeslagen in Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.custom: devx-track-csharp
ms.openlocfilehash: f5fa4ad357e937fed7df5be24a1fc78409a0259b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516393"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>Zelfstudie: Het Data Lake Capture-patroon implementeren om een Databricks Delta-tabel bij te werken

In deze zelfstudie leert u hoe u gebeurtenissen in een opslagaccount met een hiërarchische naamruimte verwerkt.

U maakt een kleine oplossing waarmee een gebruiker een Databricks Delta-tabel kan invullen door een bestand met door komma's gescheiden waarden (CSV) te uploaden waarin een verkooporder wordt beschreven. U maakt deze oplossing door een Event Grid-abonnement, een Azure-functie en een [taak](/azure/databricks/jobs) in Azure Databricks aan elkaar te koppelen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een Event Grid-abonnement die een Azure-functie aanroept.
> * Maak een Azure-functie die een melding ontvangt van een gebeurtenis en vervolgens de taak uitvoert in Azure Databricks.
> * Maak een Databricks-taak waarmee een klantorder wordt ingevoegd in een Databricks Delta-tabel in het opslagaccount.

U maakt deze oplossing in omgekeerde volgorde, te beginnen met de Azure Databricks-werkruimte.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

* Maak een opslagaccount met een hiërarchische naamruimte (Azure Data Lake Storage Gen2). In deze zelfstudie wordt een opslagaccount met de naam `contosoorders` gebruikt. Zorg ervoor dat aan uw gebruikersaccount de [rol van Gegevensbijdrager voor opslagblob](../common/storage-auth-aad-rbac-portal.md) is toegewezen.

   Zie [Een opslagaccount maken dat met Azure Data Lake Storage Gen2 wordt gebruikt](create-data-lake-storage-account.md).

* Een service-principal maken. Raadpleeg [Uitleg: Gebruik de portal voor het maken van een Azure AD-toepassing en service-principal die toegang hebben tot resources](../../active-directory/develop/howto-create-service-principal-portal.md).

  Er zijn een paar specifieke zaken die u moet doen terwijl u de stappen in het artikel uitvoert.

  :heavy_check_mark: Wanneer u de stappen uitvoert in de sectie [De toepassing toewijzen aan een rol](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) van het artikel, moet u ervoor zorgen dat de rol **Gegevensbijdrager voor opslagblob** is toegewezen aan de service-principal.

  > [!IMPORTANT]
  > Zorg ervoor dat u de rol toewijst in het bereik van het Data Lake Storage Gen2-opslagaccount. U kunt een rol toewijzen aan de bovenliggende resourcegroep of het bovenliggende abonnement, maar u ontvangt machtigingsgerelateerde fouten tot die roltoewijzingen zijn doorgegeven aan het opslagaccount.

  :heavy_check_mark: Tijdens het uitvoeren van de stappen in de sectie [Waarden ophalen voor het aanmelden](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) van het artikel plakt u de waarden van de tenant-id, de app-id en het wachtwoord in een tekstbestand. U hebt deze waarden later nodig.

## <a name="create-a-sales-order"></a>Een verkooporder maken

Maak eerst een CSV-bestand waarin een verkooporder wordt beschreven en upload dat bestand vervolgens naar het opslagaccount. Later gebruikt u de gegevens uit dit bestand om de eerste rij in de Databricks Delta-tabel in te vullen.

1. Open Azure Storage Explorer. Ga vervolgens naar uw opslagaccount en maak in het gedeelte **Blob-containers** een nieuwe container met de naam **data**.

   ![gegevensmap](./media/data-lake-storage-events/data-container.png "gegevensmap")

   Zie [Azure Storage Explorer gebruiken voor het beheren van gegevens in een Azure Data Lake Storage Gen2-account](data-lake-storage-explorer.md) voor meer informatie over het gebruik van Storage Explorer.

2. Maak in de container **data** een map met de naam **input**.

3. Plak de volgende tekst in een teksteditor.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. Sla dit bestand op uw lokale computer op en geef het de naam **data.csv**.

5. Upload dit bestand in Storage Explorer naar de map **input**.  

## <a name="create-a-job-in-azure-databricks"></a>Een taak maken in Azure Databricks

In deze sectie voert u de volgende taken uit:

* Een Azure Databricks-werkruimte maken.
* Maak een notebook.
* Een Databricks Delta-tabel maken en invullen.
* Code toevoegen waarmee rijen in de Databricks Delta-tabel worden ingevoegd.
* Een taak maken.

### <a name="create-an-azure-databricks-workspace"></a>Een Azure Databricks-werkruimte maken

In deze sectie gaat u een Azure Databricks-werkruimte maken met behulp van Azure Portal.

1. Selecteer in Azure Portal **Een resource maken** > **Analyse** > **Azure Databricks**.

    ![Databricks in de Azure-portal](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks in de Azure-portal")

2. Geef bij **Azure Databricks Service** de waarden op voor het maken van een Databricks-werkruimte.

    ![Een Azure Databricks-werkruimte maken](./media/data-lake-storage-events/new-databricks-service.png "Een Azure Databricks-werkruimte maken")

    Het maken van de werkruimte duurt enkele minuten. Bekijk de voortgangsbalk bovenaan om de bewerkingsstatus te volgen.

### <a name="create-a-spark-cluster-in-databricks"></a>Een Spark-cluster maken in Databricks

1. Ga in [Azure Portal](https://portal.azure.com) naar de Azure Databricks-werkruimte die u hebt gemaakt en selecteer vervolgens **Werkruimte starten**.

2. U wordt omgeleid naar de Azure Databricks-portal. Selecteer in de portal **Nieuw** > **Cluster**.

    ![Databricks in Azure](./media/data-lake-storage-events/databricks-on-azure.png "Databricks in Azure")

3. Op de pagina **Nieuw cluster** geeft u de waarden op waarmee een nieuw cluster wordt gemaakt.

    ![Een Databricks Spark-cluster maken in Azure](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Een Databricks Spark-cluster maken in Azure")

    Accepteer alle andere standaardwaarden, anders dan de volgende:

    * Voer een naam in voor het cluster.
    * Zorg ervoor dat u het selectievakje **Beëindigen na 120 minuten van inactiviteit** inschakelt. Geef een duur (in minuten) op waarna het cluster moet worden beëindigd als het niet wordt gebruikt.

4. Selecteer **Cluster maken**. Zodra het cluster wordt uitgevoerd, kunt u notitieblokken koppelen aan het cluster en Spark-taken uitvoeren.

Zie [Een Spark-cluster maken in Azure Databricks](/azure/databricks/clusters/create) voor meer informatie over het maken van clusters.

### <a name="create-a-notebook"></a>Een notebook maken

1. Selecteer **Werkruimte** in het linkerdeelvenster. Selecteer in de **Werkruimte**-vervolgkeuzelijst, **Notitieblok** > **maken**.

    ![Notebook maken in Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Notitieblok maken in Databricks")

2. Voer in het dialoogvenster **Notitieblok maken** een naam voor het notitieblok in. Selecteer **Python** als taal en selecteer vervolgens het Apache Spark-cluster dat u eerder hebt gemaakt.

    ![Schermopname met het dialoogvenster Notebook maken en waar u Python als taal moet selecteren.](./media/data-lake-storage-events/new-databricks-notebook.png "Notitieblok maken in Databricks")

    Selecteer **Maken**.

### <a name="create-and-populate-a-databricks-delta-table"></a>Een Databricks Delta-tabel maken en invullen

1. Kopieer en plak het volgende codeblok in de eerste cel van de notebook die u hebt gemaakt, maar voer deze code nog niet uit.  

   Vervang de plaatsaanduidingen `appId`, `password` en `tenant` in het codeblok door de waarden die u hebt verzameld bij het uitvoeren van de vereiste stappen voor deze zelfstudie.

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId>")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    Met deze code wordt een widget met de naam **source_file** gemaakt. Later gaat u een Azure-functie maken die deze code aanroept en een bestandspad aan die widget doorgeeft.  Deze code verifieert ook uw service-principal met het opslagaccount en maakt enkele variabelen die u in andere cellen gebruikt.

    > [!NOTE]
    > In een productieomgeving kunt u de verificatiesleutel eventueel in Azure Databricks opslaan. Vervolgens voegt u een opzoeksleutel toe aan uw codeblok in plaats van de verificatiesleutel. <br><br>In plaats van deze regel met code te gebruiken (`spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")`) gebruikt u bijvoorbeeld de regel met code `spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))`. <br><br>Nadat u deze zelfstudie hebt afgerond, raadpleegt u het artikel [Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) op de website van Azure Databricks voor voorbeelden van deze methode.

2. Druk op de toetsen **Shift + Enter** om de code in dit blok uit te voeren.

3. Kopieer en plak het volgende codeblok in een andere cel en druk op de toetsen **SHIFT + ENTER** om de code in dit blok uit te voeren.

   ```Python
   from pyspark.sql.types import StructType, StructField, DoubleType, IntegerType, StringType


   inputSchema = StructType([
   StructField("InvoiceNo", IntegerType(), True),
   StructField("StockCode", StringType(), True),
   StructField("Description", StringType(), True),
   StructField("Quantity", IntegerType(), True),
   StructField("InvoiceDate", StringType(), True),
   StructField("UnitPrice", DoubleType(), True),
   StructField("CustomerID", IntegerType(), True),
   StructField("Country", StringType(), True)
   ])

   rawDataDF = (spark.read
    .option("header", "true")
    .schema(inputSchema)
    .csv(adlsPath + 'input')
   )

   (rawDataDF.write
     .mode("overwrite")
     .format("delta")
     .saveAsTable("customer_data", path=customerTablePath))
   ```

   Deze code maakt de Databricks Delta-tabel in uw opslagaccount en laadt vervolgens enkele initiële gegevens uit het CSV-bestand dat u eerder hebt geüpload.

4. Nadat het codeblok is uitgevoerd, verwijdert u het uit de notebook.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Code toevoegen waarmee rijen worden ingevoegd in de Databricks Delta-tabel

1. Kopieer en plak het volgende codeblok in een andere cel, maar voer deze cel nog niet uit.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   Met deze code worden gegevens in een tijdelijke tabelweergave ingevoegd met behulp van gegevens uit een CSV-bestand. Het pad naar dat CSV-bestand is afkomstig van de invoerwidget die u in een eerdere stap hebt gemaakt.

2. Voeg de volgende code toe om de inhoud van de tijdelijke tabelweergave samen te voegen met de Databricks Delta-tabel.

   ```
   %sql
   MERGE INTO customer_data cd
   USING customer_data_to_upsert cu
   ON cd.CustomerID = cu.CustomerID
   WHEN MATCHED THEN
     UPDATE SET
       cd.StockCode = cu.StockCode,
       cd.Description = cu.Description,
       cd.InvoiceNo = cu.InvoiceNo,
       cd.Quantity = cu.Quantity,
       cd.InvoiceDate = cu.InvoiceDate,
       cd.UnitPrice = cu.UnitPrice,
       cd.Country = cu.Country
   WHEN NOT MATCHED
     THEN INSERT (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
     VALUES (
       cu.InvoiceNo,
       cu.StockCode,
       cu.Description,
       cu.Quantity,
       cu.InvoiceDate,
       cu.UnitPrice,
       cu.CustomerID,
       cu.Country)
   ```

### <a name="create-a-job"></a>Een taak maken

Maak een taak die de notebook uitvoert die u eerder hebt gemaakt. Later maakt u een Azure-functie waarmee deze taak wordt uitgevoerd wanneer een gebeurtenis wordt gegenereerd.

1. Klik op **Taken**.

2. Klik op de pagina **Taken** op **Taak maken**.

3. Geef de taak een naam en kies vervolgens de werkmap `upsert-order-data`.

   ![Een taak maken](./media/data-lake-storage-events/create-spark-job.png "Een taak maken")

## <a name="create-an-azure-function"></a>Een Azure-functie maken

Maak een Azure-functie waarmee de taak wordt uitgevoerd.

1. Kies in de linkerbovenhoek van de Databricks-werkruimte het pictogram Personen en kies vervolgens **Gebruikersinstellingen**.

   ![Account beheren](./media/data-lake-storage-events/generate-token.png "Gebruikersinstellingen")

2. Klik op de knop **Nieuwe token genereren** en klik vervolgens op de knop **Genereren**.

   Zorg ervoor dat u het token naar een veilige locatie kopieert. Uw Azure-functie heeft dit token nodig om te verifiëren met Databricks, zodat de taak kan worden uitgevoerd.
  
3. Selecteer de knop **Een resource maken** in de linkerbovenhoek van Azure Portal. Selecteer vervolgens **Compute > Functie-app**.

   ![Een Azure-functie maken](./media/data-lake-storage-events/function-app-create-flow.png "Azure-functie maken")

4. Selecteer op de pagina **Maken** van de functie-app de **.NET Core** voor de runtime-stack en configureer een Application Insights-instantie.

   ![De functie-app configureren](./media/data-lake-storage-events/new-function-app.png "De functie-app configureren")

5. Klik op de pagina **Overzicht** van de functie-app op **Configuratie**.

   ![Schermopname waarin de optie Configuratie onder Geconfigureerde functies is gemarkeerd.](./media/data-lake-storage-events/configure-function-app.png "De functie-app configureren")

6. Kies op de pagina **Toepassingsinstellingen** de knop **Nieuwe toepassingsinstelling** om elke instelling toe te voegen.

   ![Configuratie-instelling toevoegen](./media/data-lake-storage-events/add-application-setting.png "Configuratie-instelling toevoegen")

   Voeg de volgende instellingen toe:

   |Naam van de instelling | Waarde |
   |----|----|
   |**DBX_INSTANCE**| De regio van uw Databricks-werkruimte. Bijvoorbeeld: `westus2.azuredatabricks.net`|
   |**DBX_PAT**| Het persoonlijke toegangstoken dat u eerder hebt gegenereerd. |
   |**DBX_JOB_ID**|De id van de taak die wordt uitgevoerd. In dit geval is de waarde `1`.|
7. Klik op de overzichtspagina van de functie-app op de knop **Nieuwe functie**.

   ![Nieuwe functie](./media/data-lake-storage-events/new-function.png "Nieuwe functie")

8. Kies **Azure Event Grid-trigger**.

   Installeer de extensie **Microsoft.Azure.WebJobs.Extensions.EventGrid** als u dit wordt gevraagd. Als u deze moet installeren, kiest u opnieuw **Azure Event Grid-trigger** om de functie te maken.

   Het deelvenster **Nieuwe functie** wordt weergegeven.

9. Geef in het deelvenster **Nieuwe functie** de functie de naam **UpsertOrder** en klik vervolgens op de knop **Maken**.

10. Vervang de inhoud van het codebestand door deze code en klik vervolgens op de knop **Opslaan**:

    ```cs
    using "Microsoft.Azure.EventGrid"
    using "Newtonsoft.Json"
    using Microsoft.Azure.EventGrid.Models;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    private static HttpClient httpClient = new HttpClient();

    public static async Task Run(EventGridEvent eventGridEvent, ILogger log)
    {
        log.LogInformation("Event Subject: " + eventGridEvent.Subject);
        log.LogInformation("Event Topic: " + eventGridEvent.Topic);
        log.LogInformation("Event Type: " + eventGridEvent.EventType);
        log.LogInformation(eventGridEvent.Data.ToString());

        if (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated" | | eventGridEvent.EventType == "Microsoft.Storage.FileRenamed") {
            var fileData = ((JObject)(eventGridEvent.Data)).ToObject<StorageBlobCreatedEventData>();
            if (fileData.Api == "FlushWithClose") {
                log.LogInformation("Triggering Databricks Job for file: " + fileData.Url);
                var fileUrl = new Uri(fileData.Url);
                var httpRequestMessage = new HttpRequestMessage {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri(String.Format("https://{0}/api/2.0/jobs/run-now", System.Environment.GetEnvironmentVariable("DBX_INSTANCE", EnvironmentVariableTarget.Process))),
                    Headers = {
                        { System.Net.HttpRequestHeader.Authorization.ToString(), "Bearer " +  System.Environment.GetEnvironmentVariable ("DBX_PAT", EnvironmentVariableTarget.Process)},
                        { System.Net.HttpRequestHeader.ContentType.ToString (), "application/json" }
                    },
                    Content = new StringContent(JsonConvert.SerializeObject(new {
                        job_id = System.Environment.GetEnvironmentVariable ("DBX_JOB_ID", EnvironmentVariableTarget.Process) ,
                        notebook_params = new {
                            source_file = String.Join("", fileUrl.Segments.Skip(2))
                        }
                    }))
                 };
                var response = await httpClient.SendAsync(httpRequestMessage);
                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

   Deze code parseert informatie over de opslaggebeurtenis die is gegenereerd en maakt vervolgens een aanvraagbericht met een URL van het bestand dat de gebeurtenis heeft geactiveerd. Als onderdeel van het bericht geeft de functie een waarde door aan de widget **source_file** die u eerder hebt gemaakt. De functiecode verzendt het bericht naar de Databricks-taak en gebruikt het token dat u eerder hebt verkregen als verificatie.

## <a name="create-an-event-grid-subscription"></a>Een Event Grid-abonnement maken

In deze sectie maakt u een Event Grid-abonnement dat de Azure-functie aanroept wanneer bestanden naar het opslagaccount worden geüpload.

1. Klik op de pagina van de functiecode op de knop **Event Grid-abonnement toevoegen**.

   ![Schermopname waarin de knop Event Grid-abonnement toevoegen is gemarkeerd.](./media/data-lake-storage-events/new-event-subscription.png "Nieuw gebeurtenisabonnement")

2. Geef het abonnement een naam op de pagina **Gebeurtenisabonnement maken** en gebruik vervolgens de velden op de pagina om uw opslagaccount te selecteren.

   ![Nieuw gebeurtenisabonnement](./media/data-lake-storage-events/new-event-subscription-2.png "Nieuw gebeurtenisabonnement")

3. Selecteer in de vervolgkeuzelijst **Filteren op gebeurtenistypen** de gebeurtenissen **Blob gemaakt** en **Blob verwijderd** en klik vervolgens op de knop **Maken**.

## <a name="test-the-event-grid-subscription"></a>Het Event Grid-abonnement testen

1. Maak een bestand met de naam `customer-order.csv`, plak de volgende gegevens in dat bestand en sla het op uw lokale computer op.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. Upload dit bestand in Storage Explorer naar de map **input** van uw opslagaccount.

   Als u een bestand uploadt, wordt de gebeurtenis **Microsoft.Storage.BlobCreated** gegenereerd. Event Grid waarschuwt alle abonnees voor die gebeurtenis. In ons geval is de Azure-functie de enige abonnee. De Azure-functie parseert de gebeurtenisparameters om te bepalen welke gebeurtenis heeft plaatsgevonden. Vervolgens wordt de URL van het bestand doorgegeven aan de Databricks-taak. De Databricks-taak leest het bestand en voegt een rij toe aan de Databricks Delta-tabel in uw opslagaccount.

3. Als u wilt controleren of de taak is voltooid, opent u de Databricks-werkruimte, klikt u op de knop **Taken** en opent u vervolgens uw taak.

4. Selecteer de taak om de taakpagina te openen.

   ![Apache Spark-taak](./media/data-lake-storage-events/spark-job.png "Apache Spark-taak")

   Wanneer de taak is voltooid, ziet u dat de status voltooid is.

   ![Taak is voltooid](./media/data-lake-storage-events/spark-job-completed.png "Taak is voltooid")

5. Voer in een nieuwe werkbladcel deze query uit om de bijgewerkte Delta-tabel weer te geven.

   ```
   %sql select * from customer_data
   ```

   De geretourneerde tabel bevat de meest recente record.

   ![De meest recente record wordt weergegeven in de tabel](./media/data-lake-storage-events/final_query.png "De meest recente record wordt weergegeven in de tabel")

6. Als u deze record wilt bijwerken, maakt u een bestand met de naam `customer-order-update.csv`, plakt u de volgende gegevens in dat bestand en slaat u het op uw lokale computer op.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   Dit CSV-bestand is bijna identiek aan het vorige, maar de hoeveelheid van de order is gewijzigd van `228` in `22`.

7. Upload dit bestand in Storage Explorer naar de map **input** van uw opslagaccount.

8. Voer de query `select` opnieuw uit om de bijgewerkte Delta-tabel weer te geven.

   ```
   %sql select * from customer_data
   ```

   De geretourneerde tabel toont de bijgewerkte record.

   ![De bijgewerkte record wordt weergegeven in de tabel](./media/data-lake-storage-events/final_query-2.png "De bijgewerkte record wordt weergegeven in de tabel")

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep en alle gerelateerde resources, wanneer u deze niet meer nodig hebt. Hiervoor selecteert u de resourcegroep voor het opslagaccount en selecteert u **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Reageren op gebeurtenissen van Blob Storage](storage-blob-event-overview.md)