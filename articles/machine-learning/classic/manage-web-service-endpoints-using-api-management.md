---
title: 'ML Studio (klassiek): webservices beheren met API Management-Azure'
description: Een hand leiding waarin wordt getoond hoe u AzureML-webservices beheert met behulp van API Management. Beheer uw REST API-eind punten door gebruikers toegang, gebruiks beperking en dashboard bewaking te definiëren.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: 293643ab5354c51142baaf281b1845cfc9fa6f23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518025"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Azure Machine Learning Studio (klassieke) webservices beheren met API Management

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


## <a name="overview"></a>Overzicht
In deze hand leiding wordt beschreven hoe u snel aan de slag kunt met API Management voor het beheren van uw Azure Machine Learning Studio (klassieke) webservices.

## <a name="what-is-azure-api-management"></a>Wat is Azure API Management?
Azure API Management is een Azure-service waarmee u uw REST API-eind punten kunt beheren door de gebruikers toegang, gebruiks beperking en dashboard bewaking te definiëren. Zie de [Azure API management-site](https://azure.microsoft.com/services/api-management/) voor meer informatie. Zie [de hand leiding voor importeren en publiceren](../../api-management/import-and-publish.md)om aan de slag te gaan met Azure API management. In deze andere hand leiding, waarop deze hand leiding is gebaseerd, zijn meer onderwerpen opgenomen, zoals meldings configuraties, prijs categorieën, de verwerking van reacties, gebruikers verificatie, het maken van producten, ontwikkel abonnementen en het gebruik van Dash boards.

## <a name="prerequisites"></a>Vereisten
Voor het volt ooien van deze hand leiding hebt u het volgende nodig:

* Een Azure-account.
* Een AzureML-account.
* De werk ruimte, service en api_key voor een AzureML-experiment dat is geïmplementeerd als een webservice. Zie de [Snelstartgids van Studio](create-experiment.md)voor meer informatie over het maken van een AzureML-experiment. Voor informatie over het implementeren van een studio-experiment (klassiek) als een webservice raadpleegt u de [Studio-implementatie procedures](deploy-a-machine-learning-web-service.md) voor meer informatie over het implementeren van een AzureML-experiment als een webservice. Bijlage A bevat ook instructies voor het maken en testen van een eenvoudig AzureML-experiment en het implementeren als een webservice.

## <a name="create-an-api-management-instance"></a>Een API Management-exemplaar maken

U kunt uw Azure Machine Learning-webservice beheren met een API Management-exemplaar.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **+ Een resource maken**.
3. Typ ' API Management ' in het zoekvak en selecteer vervolgens de resource ' API Management '.
4. Klik op **Create**.
5. De **naam** waarde wordt gebruikt om een unieke URL te maken (in dit voor beeld wordt ' demoazureml ' gebruikt).
6. Selecteer een **abonnement**, **resource groep** en **locatie** voor uw service-exemplaar.
7. Geef een waarde voor de naam van de **organisatie** op (in dit voor beeld wordt ' demoazureml ' gebruikt).
8. Voer uw **e-mail adres** van de beheerder in. dit e-mail adres wordt gebruikt voor meldingen van het API management systeem.
9. Klik op **Create**.

Het kan tot 30 minuten duren voordat een nieuwe service is gemaakt.

![Scherm afbeelding toont het dialoog venster een P I-beheer service met de vereiste opties voor het maken van een service.](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>De API maken
Zodra het service-exemplaar is gemaakt, is de volgende stap het maken van de API. Een API bestaat uit een reeks bewerkingen die vanuit een clienttoepassing kunnen worden aangeroepen. API-bewerkingen worden geproxied naar bestaande webservices. In deze hand leiding worden Api's gemaakt die zijn gebaseerd op de bestaande AzureML-bron records en BES-webservices.

De API maken:

1. Open in het Azure Portal het service-exemplaar dat u hebt gemaakt.
2. Selecteer in het navigatie deel venster links de optie **api's**.

   ![API-beheer-menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Klik op **API toevoegen**.
2. Voer de **naam** van een web-API in (in dit voor beeld wordt gebruikgemaakt van de AzureML-demo-API).
3. Voer voor **Web service-URL** `https://ussouthcentral.services.azureml.net` .
4. Voer een * * web API-URL-achtervoegsel in. Dit wordt het laatste deel van de URL die klanten gebruiken voor het verzenden van aanvragen naar het service-exemplaar (in dit voor beeld wordt gebruikgemaakt van ' azureml-demo ').
5. Selecteer **https** voor **Web API-URL-schema**.
6. Selecteer voor **producten** **starter**.
7. Klik op **Opslaan**.


## <a name="add-the-operations"></a>De bewerkingen toevoegen

Bewerkingen worden toegevoegd en geconfigureerd voor een API in de Publisher-Portal. Als u toegang wilt krijgen tot de Publisher-Portal, klikt u op de **Publisher-Portal** in het Azure portal voor uw API Management-service, selecteert u **api's**, **bewerkingen** en klikt u vervolgens op **bewerking toevoegen**.

![toevoegen-bewerking](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Het venster **nieuwe bewerking** wordt weer gegeven en het tabblad **hand tekening** wordt standaard geselecteerd.

## <a name="add-rrs-operation"></a>De bewerking bron records toevoegen
Maak eerst een bewerking voor de service voor de AzureML-bron records:

1. Selecteer voor de **HTTP-term** **post**.
2. Typ "" voor de **URL-sjabloon** `/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}` .
3. Voer een **weergave naam** in (in dit voor beeld wordt ' Rr's ' uitgevoerd ' gebruikt).

   ![Scherm afbeelding toont de handtekening pagina waar u een weergave naam kunt invoeren.](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Klik   >  aan de linkerkant op reacties **toevoegen** en selecteer **200 OK**.
5. Klik op **Opslaan** om deze bewerking op te slaan.

   ![Scherm afbeelding toont de pagina bewerking R R S uitvoeren met de knop Opslaan.](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>BES-bewerkingen toevoegen

> [!NOTE]
> Scherm afbeeldingen zijn hier niet opgenomen voor de BES-bewerkingen, omdat ze vergelijkbaar zijn met die voor het toevoegen van de bewerking bron records.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Een batch-uitvoerings taak verzenden (maar niet starten)

1. Klik op **bewerking toevoegen** om een BES-bewerking toe te voegen aan de API.
2. Selecteer voor de **HTTP-term** **post**.
3. Typ "" voor de **URL-sjabloon** `/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}` .
4. Voer een **weergave naam** in (in dit voor beeld wordt ' bes Submit ' gebruikt).
5. Klik   >  aan de linkerkant op reacties **toevoegen** en selecteer **200 OK**.
6. Klik op **Opslaan**.

### <a name="start-a-batch-execution-job"></a>Een batch-uitvoerings taak starten

1. Klik op **bewerking toevoegen** om een BES-bewerking toe te voegen aan de API.
2. Selecteer voor de **HTTP-term** **post**.
3. Voor de **HTTP-term** typt u `/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}` .
4. Voer een **weergave naam** in (in dit voor beeld wordt ' bes start ' gebruikt).
6. Klik   >  aan de linkerkant op reacties **toevoegen** en selecteer **200 OK**.
7. Klik op **Opslaan**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>De status of het resultaat van een batch-uitvoerings taak ophalen

1. Klik op **bewerking toevoegen** om een BES-bewerking toe te voegen aan de API.
2. Selecteer **ophalen** voor de **HTTP-term**.
3. Typ "" voor de **URL-sjabloon** `/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}` .
4. Voer een **weergave naam** in (in dit voor beeld wordt ' bes-status ' gebruikt).
6. Klik   >  aan de linkerkant op reacties **toevoegen** en selecteer **200 OK**.
7. Klik op **Opslaan**.

### <a name="delete-a-batch-execution-job"></a>Een batch-uitvoerings taak verwijderen

1. Klik op **bewerking toevoegen** om een BES-bewerking toe te voegen aan de API.
2. Selecteer **verwijderen** voor de **HTTP-term**.
3. Typ "" voor de **URL-sjabloon** `/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}` .
4. Voer een **weergave naam** in (in dit voor beeld wordt ' bes delete ' gebruikt).
5. Klik   >  aan de linkerkant op reacties **toevoegen** en selecteer **200 OK**.
6. Klik op **Opslaan**.

## <a name="call-an-operation-from-the-developer-portal"></a>Een bewerking aanroepen vanuit de ontwikkelaars Portal

Bewerkingen kunnen rechtstreeks worden aangeroepen vanuit de ontwikkelaars Portal, die een handige manier biedt om de bewerkingen van een API weer te geven en te testen. In deze stap roept u de methode voor het **uitvoeren van bron records** aan die is toegevoegd aan de API voor de **AzureML-demo**. 

1. Klik op **ontwikkelaars Portal**.

   ![Scherm afbeelding toont de koppeling naar de ontwikkelaars Portal.](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Klik op **api's** in het bovenste menu en klik vervolgens op de **AzureML-demo-API** om de beschik bare bewerkingen te bekijken.

   ![Scherm afbeelding toont de Azure M L-demo van een P I-koppeling.](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Selecteer **bron records uitvoeren** voor de bewerking. Klik op **Probeer het opnieuw**.

   ![Scherm afbeelding toont het dialoog venster Azure M L demo van een P I met het selectie vakje voor het uitvoeren van R S en een try-toets.](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. Voor **aanvraag parameters** typt u uw **werk ruimte** en  **service**, typt u "2,0 voor de **apiversion**" en "True" voor de **Details**. U vindt uw **werk ruimte** en **service** in het service Dashboard van de AzureML-webservice (Zie **de webservice testen** in bijlage A).

   Klik voor **aanvraag headers** op **header toevoegen** en typ ' content-type ' en ' application/json '. Klik op **koptekst toevoegen** en typ ' autorisatie ' en ' Bearer *\<your service API-KEY\>* '. U kunt uw API-sleutel vinden in het dash board van de AzureML-webservice (Zie **de webservice testen** in bijlage A).

   Typ voor het **hoofd gedeelte** van de aanvraag `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}` .

   ![Scherm afbeelding toont de Azure M L-demo een P I aanvraag parameters, aanvraag headers, aanvraag tekst en autorisatie.](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Klik op **Verzenden**.

   ![Scherm afbeelding toont de knop verzenden.](./media/manage-web-service-endpoints-using-api-management/send.png)

Nadat een bewerking is aangeroepen, wordt de **aangevraagde URL** van de back-end-service weer gegeven in de ontwikkelaars Portal, de **antwoord status**, de **antwoord headers** en eventuele **antwoord inhoud**.

![Scherm afbeelding toont de ontwikkelaars portal waarin de reactie status, reactie latentie, antwoord headers en antwoord inhoud worden weer gegeven.](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Bijlage A: een eenvoudige AzureML-webservice maken en testen
### <a name="creating-the-experiment"></a>Het experiment maken
Hieronder vindt u de stappen voor het maken van een eenvoudig AzureML-experiment en het implementeren van deze as web service. De webservice gebruikt de invoer van een kolom met wille keurige tekst en retourneert een aantal functies die worden weer gegeven als gehele getallen. Bijvoorbeeld:

| Tekst | Gehashte tekst |
| --- | --- |
| Dit is een goede dag |1 1 2 2 0 2 0 1 |

Ga eerst met een browser naar keuze naar: [https://studio.azureml.net/](https://studio.azureml.net/) en voer uw referenties in om u aan te melden. Maak vervolgens een nieuw, leeg experiment.

![Scherm afbeelding toont een nieuwe pagina waarop een EXPERIMENT is geselecteerd en een zoek opdracht in tekst.](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Wijzig de naam in **SimpleFeatureHashingExperiment**. Vouw **opgeslagen gegevens sets** uit en sleep **boek beoordelingen van Amazon** naar uw experiment.

![In de scherm afbeelding ziet u voor beelden aan de linkerkant en een SimpleFeatureHashingExperiment venster aan de rechter kant met de instructie om items hier naartoe te slepen.](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Vouw **gegevens transformatie** en- **bewerking** uit en sleep de **kolommen in de gegevensset** naar uw experiment. Verbind **boek revisies van Amazon** om de **kolommen in de gegevensset te selecteren**.

![De module boek beoordelingen gegevensset verbinden met een module Project kolommen](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Klik op **kolommen selecteren in gegevensset** en klik vervolgens op **kolom kiezer starten** en selecteer **col2**. Klik op het vinkje om deze wijzigingen toe te passen.

![Kolommen selecteren met kolom namen](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Vouw **Text Analytics** uit en sleep **functie-hashing** naar het experiment. Verbind **select columns in dataset** om **hashing van functies te gebruiken**.

![Scherm afbeelding toont een functie-hash-item dat wordt toegevoegd aan de werk ruimte.](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Type **3** voor de **hash-bitsize**. Hiermee worden kolommen van 8 (23) gemaakt.

![Scherm afbeelding toont eigenschappen waarvoor hashing van functies is geselecteerd en u kunt hash-bitsize invoeren.](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Op dit moment kunt u op **uitvoeren** klikken om het experiment te testen.

![Scherm afbeelding toont een knop uitvoeren.](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Een webservice maken
Maak nu een webservice. Vouw **Web Service** uit en sleep **invoer** naar uw experiment. Verbind **invoer** met **functie-hashing**. Sleep ook **uitvoer** naar uw experiment. Koppel de **uitvoer** aan **functie-hashing**.

![In de scherm opname wordt de werk ruimte weer gegeven na de opgegeven wijzigingen.](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Klik op **webservice publiceren**.

![Scherm afbeelding toont de knop PUBLISH WEB SERVICE.](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Klik op **Ja** om het experiment te publiceren.

![Scherm afbeelding toont een bevestigings bericht en de optie voor publiceren of niet.](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>De webservice testen
Een AzureML-webservice bestaat uit RSS-eind punten (aanvraag/antwoord service) en BES (batch Execution Service). RSS is voor synchrone uitvoering. BES is voor asynchrone taak uitvoering. Als u uw webservice wilt testen met de onderstaande python-bron, moet u mogelijk de Azure SDK voor python downloaden en installeren (zie: [python installeren](/azure/developer/python/azure-sdk-install)).

U hebt ook de **werk ruimte**, de **service** en **api_key** van uw experiment nodig voor de onderstaande voorbeeld bron. U kunt de werk ruimte en service vinden door te klikken op **aanvraag/antwoord** of **batch uitvoering** voor uw experiment in het dash board van de webservice.

![Scherm afbeelding toont het deel venster aanvraag waarin u de werk ruimte en de service waarden kunt vinden.](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

U kunt de **api_key** vinden door te klikken op het experiment in het web service-dash board.

![Scherm afbeelding toont het experiment in het web service-dash board waar u de sleutel P I kunt vinden.](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Eind punt van bron records testen
##### <a name="test-button"></a>Knop Testen
Een eenvoudige manier om het eind punt van de bron records te testen is door te klikken op **testen** op het web service-dash board.

![Scherm afbeelding toont het experiment in het web service-dash board met de knop testen.](./media/manage-web-service-endpoints-using-api-management/test.png)

Typ **Dit is een goede dag** voor **col2**. Klik op het vinkje.

![Scherm afbeelding toont het dialoog venster gegevens invoeren om te voors pellen, waarin u tekst kunt invoeren zoals in het voor beeld dit is een goede dag.](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

U ziet iets als

![Scherm afbeelding toont het resultaat van het experiment, waardoor dit een goede dag is en meerdere cijfers tussen aanhalings tekens.](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Voorbeeldcode
Een andere manier om uw RR'S te testen, is vanuit uw client code. Als u op het dash board op **aanvraag/antwoord** klikt en naar beneden schuift, ziet u voorbeeld code voor C#, python en R. U ziet ook de syntaxis van de aanvraag RR'S, met inbegrip van de aanvraag-URI, headers en hoofd tekst.

Deze hand leiding bevat een werkend python-voor beeld. U moet deze wijzigen met de **werk ruimte**, de **service** en de **api_key** van uw experiment.

```python
import urllib2
import json
workspace = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE WORKSPACE ID>"
service = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE SERVICE ID>"
api_key = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE API KEY>"
data = {
"Inputs": {
    "input1": {
        "ColumnNames": ["Col2"],
        "Values": [ [ "This is a good day" ] ]
    },
},
"GlobalParameters": { }
}
url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
body = str.encode(json.dumps(data))
try:
    req = urllib2.Request(url, body, headers)
    response = urllib2.urlopen(req)
    result = response.read()
    print "result:" + result
        except urllib2.HTTPError, error:
    print("The request failed with status code: " + str(error.code))
    print(error.info())
    print(json.loads(error.read()))
```

#### <a name="test-bes-endpoint"></a>BES-eind punt testen
Klik op **batch uitvoering** op het dash board en schuif naar beneden. U ziet voorbeeld code voor C#, python en R. U ziet ook de syntaxis van de BES-aanvragen voor het verzenden van een taak, het starten van een taak, het ophalen van de status of de resultaten van een taak en het verwijderen van een taak.

Deze hand leiding bevat een werkend python-voor beeld. U moet deze wijzigen met de **werk ruimte**, de **service** en de **api_key** van uw experiment. Daarnaast moet u de naam van het **opslag account**, de **sleutel van het opslag account** en de naam van de **opslag container** wijzigen. Ten slotte moet u de locatie van het **invoer bestand** en de locatie van het **uitvoer bestand** wijzigen.

```python
import urllib2
import json
import time
from azure.storage import *
workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
service = "<REPLACE WITH YOUR SERVICE ID>"
api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
input_blob_name = "mydatablob.csv"
output_blob_name = "myresultsblob.csv"
def printHttpError(httpError):
print("The request failed with status code: " + str(httpError.code))
print(httpError.info())
print(json.loads(httpError.read()))
return
def saveBlobToFile(blobUrl, resultsLabel):
print("Reading the result from " + blobUrl)
try:
    response = urllib2.urlopen(blobUrl)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
with open(output_file, "wb+") as f:
    f.write(response.read())
print(resultsLabel + " have been written to the file " + output_file)
return
def processResults(result):
first = True
results = result["Results"]
for outputName in results:
    result_blob_location = results[outputName]
    sas_token = result_blob_location["SasBlobToken"]
    base_url = result_blob_location["BaseLocation"]
    relative_url = result_blob_location["RelativeLocation"]
    print("The results for " + outputName + " are available at the following Azure Storage location:")
    print("BaseLocation: " + base_url)
    print("RelativeLocation: " + relative_url)
    print("SasBlobToken: " + sas_token)
    if (first):
        first = False
        url3 = base_url + relative_url + sas_token
        saveBlobToFile(url3, "The results for " + outputName)
return

def invokeBatchExecutionService():
url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
print("Uploading the input to blob storage...")
data_to_upload = open(input_file, "r").read()
blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
print "Uploaded the input to blob storage"
input_blob_path = "/" + storage_container_name + "/" + input_blob_name
connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
payload =  {
    "Input": {
        "ConnectionString": connection_string,
        "RelativeLocation": input_blob_path
    },
    "Outputs": {
        "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
    },
    "GlobalParameters": {
    }
}
    body = str.encode(json.dumps(payload))
headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
print("Submitting the job...")
# submit the job
req = urllib2.Request(url + "?api-version=2.0", body, headers)
try:
    response = urllib2.urlopen(req)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
result = response.read()
job_id = result[1:-1] # remove the enclosing double-quotes
print("Job ID: " + job_id)
# start the job
print("Starting the job...")
req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
try:
    response = urllib2.urlopen(req)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
url2 = url + "/" + job_id + "?api-version=2.0"

while True:
    print("Checking the job status...")
    # If you are using Python 3+, replace urllib2 with urllib.request in the following code
    req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = json.loads(response.read())
    status = result["StatusCode"]
    print "status:" + status
    if (status == 0 or status == "NotStarted"):
        print("Job " + job_id + " not yet started...")
    elif (status == 1 or status == "Running"):
        print("Job " + job_id + " running...")
    elif (status == 2 or status == "Failed"):
        print("Job " + job_id + " failed!")
        print("Error details: " + result["Details"])
        break
    elif (status == 3 or status == "Cancelled"):
        print("Job " + job_id + " cancelled!")
        break
    elif (status == 4 or status == "Finished"):
        print("Job " + job_id + " finished!")
        processResults(result)
        break
    time.sleep(1) # wait one second
return
invokeBatchExecutionService()
```