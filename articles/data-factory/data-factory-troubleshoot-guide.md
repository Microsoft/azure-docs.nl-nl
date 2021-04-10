---
title: Problemen oplossen Azure Data Factory | Microsoft Docs
description: Meer informatie over het oplossen van problemen met externe controle activiteiten in Azure Data Factory.
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/30/2020
ms.author: abnarain
ms.openlocfilehash: 101e55188b8021040e2fd6bd573c1c6330241e72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382800"
---
# <a name="troubleshoot-azure-data-factory"></a>Problemen met Azure Data Factory oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene probleemoplossings methoden besproken voor externe controle activiteiten in Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Connector- en kopieeractiviteiten

Raadpleeg [problemen met Azure Data Factory connectors oplossen](connector-troubleshoot-guide.md)voor problemen met de connector, zoals een probleem met de Kopieer activiteit.

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>Fout code: 3200

- **Bericht**: fout 403.

- **Oorzaak**: `The Databricks access token has expired.`

- **Aanbeveling**: standaard is het toegangs token van Azure Databricks geldig gedurende 90 dagen. Maak een nieuw token en werk de gekoppelde service bij.

### <a name="error-code-3201"></a>Fout code: 3201

- **Bericht**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **Oorzaak**: `Bad authoring: Notebook path not specified correctly.`

- **Aanbeveling**: Geef het pad naar de notebook op in de Databricks-activiteit.

<br/> 

- **Bericht**: `Cluster... does not exist.`

- **Oorzaak**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Aanbeveling**: Controleer of het Databricks-cluster bestaat.

<br/> 

- **Bericht**: `Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Oorzaak**: `Bad authoring.`

- **Aanbeveling**: Geef een absoluut pad op voor werk ruimte-adresserings schema's of `dbfs:/folder/subfolder/foo.py` voor bestanden die zijn opgeslagen in de Databricks-BESTANDS systeem (DFS).

<br/> 

- **Bericht**: `{0} LinkedService should have domain and accessToken as required properties.`

- **Oorzaak**: `Bad authoring.`

- **Aanbeveling**: Controleer de [definitie van de gekoppelde service](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Bericht**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Oorzaak**: `Bad authoring.`

- **Aanbeveling**: Controleer de [definitie van de gekoppelde service](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Bericht**: `Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **Oorzaak**: `Bad authoring.`

- **Aanbeveling**: Raadpleeg het fout bericht.

<br/>

### <a name="error-code-3202"></a>Fout code: 3202

- **Bericht**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **Oorzaak**: `Too many Databricks runs in an hour.`

- **Aanbeveling**: alle pijp lijnen controleren die gebruikmaken van deze Databricks-werk ruimte voor het maken van een taak. Als pijp lijnen te veel Databricks-runs worden uitgevoerd, migreert u enkele pijp lijnen naar een nieuwe werk ruimte.

<br/> 

- **Bericht**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Oorzaak**: `Authoring error: No value provided for the parameter.`

- **Aanbeveling**: Inspecteer de JSON van de pijp lijn en zorg ervoor dat alle para meters in de baseParameters-notebook een niet-lege waarde opgeven.

<br/> 

- **Bericht**: `User: ` SimpleUserContext {gebruikersID =..., naam = user@company.com , orgId =...}` is not authorized to access cluster.`

- **Oorzaak**: de gebruiker die het toegangs token heeft gegenereerd, heeft geen toegang tot het Databricks-cluster dat is opgegeven in de gekoppelde service.

- **Aanbeveling**: Zorg ervoor dat de gebruiker over de vereiste machtigingen beschikt in de werk ruimte.

### <a name="error-code-3203"></a>Fout code: 3203

- **Bericht**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Oorzaak**: het cluster is beëindigd. Voor interactieve clusters kan dit een race voorwaarde zijn.

- **Aanbeveling**: gebruik taak clusters om deze fout te voor komen.

### <a name="error-code-3204"></a>Fout code: 3204

- **Bericht**: `Job execution failed.`

- **Oorzaak**: fout berichten geven verschillende problemen aan, zoals een onverwachte cluster status of een specifieke activiteit. Vaak wordt er geen fout bericht weer gegeven.

- **Aanbeveling**: n.v.t.

### <a name="error-code-3208"></a>Fout code: 3208

- **Bericht**: `An error occurred while sending the request.`

- **Oorzaak**: de netwerk verbinding met de Databricks-service is onderbroken.

- **Aanbeveling**: als u een zelf-hostende Integration runtime gebruikt, zorg er dan voor dat de netwerk verbinding betrouwbaar is van de knoop punten voor Integration runtime. Als u Azure Integration runtime gebruikt, werkt normaal gesp roken opnieuw.
 
## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

De volgende tabel is van toepassing op U-SQL.
 
### <a name="error-code-2709"></a>Fout code: 2709

- **Bericht**: `The access token is from the wrong tenant.`

- **Oorzaak**: onjuiste Azure Active Directory-Tenant (Azure AD).

- **Aanbeveling**: onjuiste Azure Active Directory-Tenant (Azure AD).

<br/>

- **Bericht**: `We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **Oorzaak**: deze fout wordt veroorzaakt door het beperken van data Lake Analytics.

- **Aanbeveling**: Verminder het aantal verzonden taken dat moet worden data Lake Analytics. Wijzig Data Factory triggers en gelijktijdigheids instellingen voor activiteiten of verg root de limieten voor Data Lake Analytics.

<br/> 

- **Bericht**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Oorzaak**: deze fout wordt veroorzaakt door het beperken van data Lake Analytics.

- **Aanbeveling**: Verminder het aantal verzonden taken dat moet worden data Lake Analytics. Wijzig Data Factory triggers en gelijktijdigheids instellingen voor activiteiten of verg root de limieten voor Data Lake Analytics.

### <a name="error-code-2705"></a>Fout code: 2705

- **Bericht**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Oorzaak**: de service-principal of het certificaat heeft geen toegang tot het bestand in de opslag.

- **Aanbeveling**: Controleer of de service-principal of het certificaat dat de gebruiker voor data Lake Analytics taken heeft, toegang heeft tot het data Lake Analytics account en de standaard data Lake Storage instantie van de hoofdmap.

### <a name="error-code-2711"></a>Fout code: 2711

- **Bericht**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Oorzaak**: de service-principal of het certificaat heeft geen toegang tot het bestand in de opslag.

- **Aanbeveling**: Controleer of de service-principal of het certificaat dat de gebruiker voor data Lake Analytics taken heeft, toegang heeft tot het data Lake Analytics account en de standaard data Lake Storage instantie van de hoofdmap.

<br/> 

- **Bericht**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Oorzaak**: het pad naar het U-SQL-bestand is onjuist of de referenties van de gekoppelde service hebben geen toegang.

- **Aanbeveling**: Controleer het pad en de referenties die zijn opgenomen in de gekoppelde service.

### <a name="error-code-2704"></a>Fout code: 2704

- **Bericht**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Oorzaak**: de service-principal of het certificaat heeft geen toegang tot het bestand in de opslag.

- **Aanbeveling**: Controleer of de service-principal of het certificaat dat de gebruiker voor data Lake Analytics taken heeft, toegang heeft tot het data Lake Analytics account en de standaard data Lake Storage instantie van de hoofdmap.

### <a name="error-code-2707"></a>Fout code: 2707

- **Bericht**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **Oorzaak**: het data Lake Analytics-account in de gekoppelde service is onjuist.

- **Aanbeveling**: Controleer of het juiste account is ingesteld.

### <a name="error-code-2703"></a>Fout code: 2703

- **Bericht**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **Oorzaak**: de fout is van data Lake Analytics.

- **Aanbeveling**: de taak is verzonden naar Data Lake Analytics en het script daar, beide mislukt. Onderzoek in Data Lake Analytics. Ga in de portal naar het Data Lake Analytics-account en zoek de taak met behulp van de Data Factory activiteit run ID (gebruik niet de ID van de pijplijn uitvoering). De taak bevat meer informatie over de fout en helpt u bij het oplossen van problemen.

   Als de oplossing niet duidelijk is, neemt u contact op met het Data Lake Analytics-ondersteunings team en geeft u de URL (Universal Resource Locator) van de taak op, waaronder uw account naam en de taak-ID.
 
## <a name="azure-functions"></a>Azure Functions

### <a name="error-code-3602"></a>Fout code: 3602

- **Bericht**: `Invalid HttpMethod: '%method;'.`

- **Oorzaak**: de Httpmethod die in de nettolading van de activiteit is opgegeven, wordt niet ondersteund door de Azure function-activiteit.

- **Aanbeveling**: de ondersteunde Httpmethods zijn: plaatsen, posten, ophalen, verwijderen, opties, kop en tracering.

### <a name="error-code-3603"></a>Fout code: 3603

- **Bericht**: `Response Content is not a valid JObject.`

- **Oorzaak**: de Azure-functie die is aangeroepen, heeft niet een JSON-nettolading geretourneerd in het antwoord. Azure Data Factory (ADF) Azure function-activiteit ondersteunt alleen inhoud van een JSON-antwoord.

- **Aanbeveling**: werk de Azure-functie bij om een geldige JSON-nettolading te retour neren, zoals een C#-functie, retour neren mogelijk `(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");`

### <a name="error-code-3606"></a>Fout code: 3606

- **Bericht**: functie sleutel ontbreekt in azure functions-activiteit.

- **Oorzaak**: de definitie van de Azure function-activiteit is niet voltooid.

- **Aanbeveling**: Controleer of de JSON-definitie van de Azure function activity-activiteit een eigenschap heeft met de naam `functionKey` .

### <a name="error-code-3607"></a>Fout code: 3607

- **Bericht**: `Azure function activity missing function name.`

- **Oorzaak**: de definitie van de Azure function-activiteit is niet voltooid.

- **Aanbeveling**: Controleer of de JSON-definitie van de Azure function activity-activiteit een eigenschap heeft met de naam `functionName` .

### <a name="error-code-3608"></a>Fout code: 3608

- **Bericht**: `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Oorzaak**: de details van de Azure-functie in de definitie van de activiteit zijn mogelijk onjuist.

- **Aanbeveling**: Corrigeer de Azure-functie gegevens en probeer het opnieuw.

### <a name="error-code-3609"></a>Fout code: 3609

- **Bericht**: `Azure function activity missing functionAppUrl.`

- **Oorzaak**: de definitie van de Azure function-activiteit is niet voltooid.

- **Aanbeveling**: Controleer of de JSON-definitie van de Azure function activity-activiteit een eigenschap heeft met de naam `functionAppUrl` .

### <a name="error-code-3610"></a>Fout code: 3610

- **Bericht**: `There was an error while calling endpoint.`

- **Oorzaak**: de functie-URL is mogelijk onjuist.

- **Aanbeveling**: Controleer of de waarde voor `functionAppUrl` in de JSON van de activiteit juist is en probeer het opnieuw.

### <a name="error-code-3611"></a>Fout code: 3611

- **Bericht**: `Azure function activity missing Method in JSON.`

- **Oorzaak**: de definitie van de Azure function-activiteit is niet voltooid.

- **Aanbeveling**: Controleer of de JSON-definitie van de Azure function activity-activiteit een eigenschap heeft met de naam `method` .

### <a name="error-code-3612"></a>Fout code: 3612

- **Bericht**: `Azure function activity missing LinkedService definition in JSON.`

- **Oorzaak**: de definitie van de Azure function-activiteit is niet voltooid.

- **Aanbeveling**: Controleer of de JSON-definitie van de Azure function-activiteit is gekoppeld aan details van de service.

## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code-4101"></a>Fout code: 4101

- **Bericht**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Oorzaak**: onjuiste indeling of ontbrekende definitie van eigenschap `%propertyName;` .

- **Aanbeveling**: Controleer of de `%activityName;` eigenschap `%propertyName;` met de juiste gegevens is gedefinieerd voor de activiteit.

### <a name="error-code-4110"></a>Fout code: 4110

- **Bericht**: `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Oorzaak**: de definitie van de AzureMLExecutePipeline-activiteit is niet voltooid.

- **Aanbeveling**: Controleer of de JSON-definitie van de invoer AzureMLExecutePipeline-activiteit correct gekoppelde service details heeft.

### <a name="error-code-4111"></a>Fout code: 4111

- **Bericht**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Oorzaak**: onjuiste definitie van de activiteit.

- **Aanbeveling**: Controleer of de JSON-definitie van de invoer AzureMLExecutePipeline-activiteit correct gekoppelde service details heeft.

### <a name="error-code-4112"></a>Fout code: 4112

- **Bericht**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Oorzaak**: onjuiste indeling of ontbrekende definitie van eigenschap% PropertyName;.

- **Aanbeveling**: Controleer of de eigenschap `%propertyName;` met de juiste gegevens is gedefinieerd voor de gekoppelde service.

### <a name="error-code-4121"></a>Fout code: 4121

- **Bericht**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Oorzaak**: de referentie die wordt gebruikt voor toegang tot Azure machine learning is verlopen.

- **Aanbeveling**: Controleer of de referentie geldig is en probeer het opnieuw.

### <a name="error-code-4122"></a>Fout code: 4122

- **Bericht**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Oorzaak**: de referentie die in azure machine learning gekoppelde service is opgenomen, is ongeldig of heeft geen machtiging voor de bewerking.

- **Aanbeveling**: Controleer of de referentie in de gekoppelde service geldig is en de machtiging heeft om toegang te krijgen tot Azure machine learning.

### <a name="error-code-4123"></a>Fout code: 4123

- **Bericht**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Oorzaak**: de eigenschappen van de activiteit, zoals `pipelineParameters` zijn ongeldig voor de pijp lijn Azure machine learning (ml).

- **Aanbeveling**: Controleer of de waarde van de activiteit eigenschappen overeenkomt met de verwachte payload van de gepubliceerde Azure ml-pijp lijn die is opgegeven in de gekoppelde service.

### <a name="error-code-4124"></a>Fout code: 4124

- **Bericht**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Oorzaak**: het gepubliceerde Azure ml pijp lijn-eind punt bestaat niet.

- **Aanbeveling**: Controleer of het gepubliceerde Azure machine learning pijplijn-eind punt dat is opgegeven in de gekoppelde service voor komt in azure machine learning.

### <a name="error-code-4125"></a>Fout code: 4125

- **Bericht**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Oorzaak**: er is een server fout opgetreden in azure machine learning.

- **Aanbeveling**: Probeer het later opnieuw. Neem contact op met het Azure Machine Learning team als het probleem zich blijft voordoen.

### <a name="error-code-4126"></a>Fout code: 4126

- **Bericht**: `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Oorzaak**: de uitvoering van de Azure ml-pijp lijn is mislukt.

- **Aanbeveling**: Raadpleeg Azure machine learning voor meer fout logboeken en Los vervolgens de ml-pijp lijn op.

## <a name="common"></a>Algemeen

### <a name="error-code-2103"></a>Fout code: 2103

- **Bericht**: `Please provide value for the required property '%propertyName;'.`

- **Oorzaak**: de vereiste waarde voor de eigenschap is niet opgegeven.

- **Aanbeveling**: Geef de waarde op uit het bericht en probeer het opnieuw.

### <a name="error-code-2104"></a>Fout code: 2104

- **Bericht**: `The type of the property '%propertyName;' is incorrect.`

- **Oorzaak**: het gegeven eigenschaps type is niet juist.

- **Aanbeveling**: Los het type van de eigenschap op en probeer het opnieuw.

### <a name="error-code-2105"></a>Fout code: 2105

- **Bericht**: `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Oorzaak**: de waarde voor de eigenschap is ongeldig of heeft niet de verwachte indeling.

- **Aanbeveling**: Raadpleeg de documentatie voor de eigenschap en controleer of de waarde die u hebt gegeven de juiste notatie en type bevat.

### <a name="error-code-2106"></a>Fout code: 2106

- **Bericht**: `The storage connection string is invalid. %errorMessage;`

- **Oorzaak**: de Connection String voor de opslag is ongeldig of heeft een onjuiste indeling.

- **Aanbeveling**: Ga naar de Azure Portal en zoek uw opslag, kopieer en plak de Connection String in de gekoppelde service en probeer het opnieuw.

### <a name="error-code-2108"></a>Fout code: 2108

- **Bericht**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Oorzaak**: de aanvraag is mislukt vanwege een onderliggend probleem, zoals een netwerk verbinding, een DNS-fout, een validatie van het server certificaat of een time-out.

- **Aanbeveling**: gebruik Fiddler/postman om de aanvraag te valideren.

### <a name="error-code-2110"></a>Fout code: 2110

- **Bericht**: `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Oorzaak**: de gekoppelde service die in de activiteit is opgegeven, is onjuist.

- **Aanbeveling**: Controleer of het gekoppelde service type een van de ondersteunde typen voor de activiteit is. Het gekoppelde service type voor HDI-activiteiten kan bijvoorbeeld HDInsight of HDInsightOnDemand zijn.

### <a name="error-code-2111"></a>Fout code: 2111

- **Bericht**: `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Oorzaak**: het type van de gegeven eigenschap is niet juist.

- **Aanbeveling**: Los het type eigenschap op en probeer het opnieuw.

### <a name="error-code-2112"></a>Fout code: 2112

- **Bericht**: `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Oorzaak**: het Cloud type wordt niet ondersteund of kan niet worden vastgesteld voor opslag van de EndpointSuffix.

- **Aanbeveling**: gebruik opslag in een andere Cloud en probeer het opnieuw.

### <a name="error-code-2128"></a>Fout code: 2128

- **Bericht**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Oorzaak**: netwerk connectiviteit, DNS-fout, validatie van server certificaat of time-out.

- **Aanbeveling**: Controleer of het eind punt dat u probeert te raken reageert op aanvragen. U kunt hulpprogram ma's zoals Fiddler/postman gebruiken.

## <a name="custom"></a>Aangepast

De volgende tabel is van toepassing op Azure Batch.
 
### <a name="error-code-2500"></a>Fout code: 2500

- **Bericht**: `Hit unexpected exception and execution failed.`

- **Oorzaak**: `Can't launch command, or the program returned an error code.`

- **Aanbeveling**: Controleer of het uitvoer bare bestand bestaat. Als het programma is gestart, controleert u of *stdout.txt* en *stderr.txt* zijn geüpload naar het opslag account. Het is een goed idee om Logboeken in uw code op te neemen voor fout opsporing.

### <a name="error-code-2501"></a>Fout code: 2501

- **Bericht**: `Cannot access user batch account; please check batch account settings.`

- **Oorzaak**: onjuiste batch-toegangs sleutel of groeps naam.

- **Aanbeveling**: Controleer de naam van de groep en de sleutel voor batch toegang in de gekoppelde service.

### <a name="error-code-2502"></a>Fout code: 2502

- **Bericht**: `Cannot access user storage account; please check storage account settings.`

- **Oorzaak**: de naam van het opslag account of de toegangs sleutel is onjuist.

- **Aanbeveling**: Controleer de naam van het opslag account en de toegangs sleutel in de gekoppelde service.

### <a name="error-code-2504"></a>Fout code: 2504

- **Bericht**: `Operation returned an invalid status code 'BadRequest'.`

- **Oorzaak**: te veel bestanden in de `folderPath` van de aangepaste activiteit. De totale grootte van `resourceFiles` mag niet langer zijn dan 32.768 tekens.

- **Aanbeveling**: Verwijder overbodige bestanden of zip en voeg een unzip-opdracht toe om deze uit te pakken.
   
   Gebruik bijvoorbeeld `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2505"></a>Fout code: 2505

- **Bericht**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Oorzaak**: aangepaste activiteiten bieden alleen ondersteuning voor opslag accounts die gebruikmaken van een toegangs sleutel.

- **Aanbeveling**: Raadpleeg de beschrijving van de fout.

### <a name="error-code-2507"></a>Fout code: 2507

- **Bericht**: `The folder path does not exist or is empty: ...`

- **Oorzaak**: er zijn geen bestanden in het opslag account op het opgegeven pad.

- **Aanbeveling**: het mappad moet de uitvoer bare bestanden bevatten die u wilt uitvoeren.

### <a name="error-code-2508"></a>Fout code: 2508

- **Bericht**: `There are duplicate files in the resource folder.`

- **Oorzaak**: meerdere bestanden met dezelfde naam bevinden zich in verschillende submappen van folderPath.

- **Aanbeveling**: aangepaste activiteiten de mapstructuur afvlakken onder folderPath. Als u de mappen structuur wilt behouden, moet u de bestanden zip en uitpakken in Azure Batch met behulp van een unzip opdracht.
   
   Gebruik bijvoorbeeld `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2509"></a>Fout code: 2509

- **Bericht**: `Batch url ... is invalid; it must be in Uri format.`

- **Oorzaak**: batch-url's moeten vergelijkbaar zijn met `https://mybatchaccount.eastus.batch.azure.com`

- **Aanbeveling**: Raadpleeg de beschrijving van de fout.

### <a name="error-code-2510"></a>Fout code: 2510

- **Bericht**: `An error occurred while sending the request.`

- **Oorzaak**: de batch-URL is ongeldig.

- **Aanbeveling**: Controleer de batch-URL.
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-206"></a>Fout code: 206

- **Bericht**: `The batch ID for Spark job is invalid. Please retry your job.`

- **Oorzaak**: er is een intern probleem opgetreden met de service die deze fout heeft veroorzaakt.

- **Aanbeveling**: dit probleem kan tijdelijk zijn. Voer de taak na enige tijd opnieuw uit.

### <a name="error-code-207"></a>Fout code: 207

- **Bericht**: `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI.`

- **Oorzaak**: er is een interne fout opgetreden bij het bepalen van de regio van het primaire opslag account.

- **Aanbeveling**: Probeer een andere opslag. 

### <a name="error-code-208"></a>Fout code: 208

- **Bericht**: `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again.`

- **Oorzaak**: er is een interne fout opgetreden tijdens het lezen van de service-principal of het instantiëren van de MSI-verificatie.

- **Aanbeveling**: overweeg het leveren van een service-principal die machtigingen heeft voor het maken van een HDInsight-cluster in het opgegeven abonnement en probeer het opnieuw. Controleer of de [Managed Identities correct zijn ingesteld](../hdinsight/hdinsight-managed-identities.md).

### <a name="error-code-2300"></a>Fout code: 2300

- **Bericht**: `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **Oorzaak**: het fout bericht bevat een bericht dat lijkt op `The remote name could not be resolved.` . De gegeven URI van het cluster is mogelijk ongeldig.

- **Aanbeveling**: Controleer of het cluster niet is verwijderd en of de gegeven URI juist is. Wanneer u de URI in een browser opent, ziet u de Ambari-gebruikers interface. Als het cluster zich in een virtueel netwerk bevindt, moet de URI de persoonlijke URI zijn. Gebruik een virtuele machine (VM) die deel uitmaakt van hetzelfde virtuele netwerk om deze te openen.

   Zie [Direct Connect to Apache Hadoop Services](../hdinsight/hdinsight-plan-virtual-network-deployment.md#directly-connect-to-apache-hadoop-services)(Engelstalig) voor meer informatie.
 
 </br>

- **Oorzaak**: als het fout bericht een bericht bevat dat lijkt op `A task was canceled.` , is er een time-out opgetreden voor de taak verzending.

- **Aanbeveling**: het probleem kan algemeen HDInsight-connectiviteit of een netwerk verbinding zijn. Controleer eerst of de gebruikers interface van HDInsight Ambari beschikbaar is vanuit elke browser. Controleer vervolgens of uw referenties nog geldig zijn.
   
   Als u een zelf-hosted Integrated Runtime (IR) gebruikt, voert u deze stap uit vanaf de virtuele machine of computer waarop de zelf-hostende IR is geïnstalleerd. Probeer de taak vervolgens opnieuw uit Data Factory te verzenden.

   Lees [Ambari Web UI](../hdinsight/hdinsight-hadoop-manage-ambari.md#ambari-web-ui)voor meer informatie.

 </br>

- **Oorzaak**: wanneer het fout bericht een bericht bevat dat vergelijkbaar is met `User admin is locked out in Ambari` of `Unauthorized: Ambari user name or password is incorrect` , zijn de referenties voor HDInsight onjuist of verlopen.

- **Aanbeveling**: Corrigeer de referenties en implementeer de gekoppelde service opnieuw. Controleer eerst of de referenties werken op HDInsight door de cluster-URI in een browser te openen en u aan te melden. Als de referenties niet werken, kunt u ze opnieuw instellen op de Azure Portal.

   Stel het wacht woord voor het ESP-cluster opnieuw in via [selfservice voor wacht woord opnieuw instellen](../active-directory/user-help/active-directory-passwords-update-your-own-password.md).

 </br>

- **Oorzaak**: wanneer het fout bericht een bericht bevat dat vergelijkbaar `502 - Web server received an invalid response while acting as a gateway or proxy server` is met, wordt deze fout geretourneerd door de HDInsight-service.

- **Aanbeveling**: er treedt vaak een 502-fout op wanneer het proces van de Ambari-server is afgesloten. U kunt de Ambari-services opnieuw starten door het hoofd knooppunt opnieuw op te starten.

    1. Verbinding maken met een van uw knoop punten op HDInsight met behulp van SSH.
    1. Zoek de host van het actieve hoofd knooppunt door uit te voeren `ping headnodehost` .
    1. Maak met SSH verbinding met het actieve hoofd knooppunt als Ambari server. 
    1. Start het actieve hoofd knooppunt opnieuw op.

       Bekijk de documentatie voor het oplossen van problemen met Azure HDInsight voor meer informatie. Bijvoorbeeld:

       * [502-fout in de Ambari-gebruikersinterface](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)
       * [RpcTimeoutException voor Apache Spark Thrift-server](../hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception.md)
       * [Problemen met ongeldige gateway fouten oplossen in Application Gateway](../application-gateway/application-gateway-troubleshooting-502.md).

 </br>

- **Oorzaak**: wanneer het fout bericht een bericht bevat dat vergelijkbaar is met `Unable to service the submit job request as templeton service is busy with too many submit job requests` of `Queue root.joblauncher already has 500 applications, cannot accept submission of application` , worden er op hetzelfde moment te veel taken verzonden naar HDInsight.

- **Aanbeveling**: Beperk het aantal gelijktijdige taken dat is verzonden naar HDInsight. Raadpleeg Data Factory gelijktijdige activiteit als de taken worden verzonden door dezelfde activiteit. Wijzig de triggers zodat de gelijktijdige pijplijn uitvoeringen in de loop van de tijd worden verdeeld.

   Raadpleeg de [HDInsight-documentatie](../hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors.md) om `templeton.parallellism.job.submit` de fout te corrigeren.

### <a name="error-code-2301"></a>Fout code: 2301

- **Bericht**: `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Oorzaak**: HDInsight-cluster of-service heeft problemen.

- **Aanbeveling**: deze fout treedt op wanneer ADF geen antwoord ontvangt van HDInsight-cluster wanneer wordt geprobeerd om de status van de actieve taak aan te vragen. Dit probleem kan zich voordoen op het cluster zelf of de HDInsight-service kan een storing hebben.

   Raadpleeg de documentatie voor het oplossen van problemen met HDInsight op https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide of neem contact op met de ondersteuning voor verdere ondersteuning.

### <a name="error-code-2302"></a>Fout code: 2302

- **Bericht**: `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Oorzaak**: de taak is verzonden naar het HDI-cluster en is daar mislukt.

- **Aanbeveling**: 

 1. Controleer de Ambari-gebruikers interface:
    1. Zorg ervoor dat alle services nog worden uitgevoerd.
    1. Ga in de Ambari-gebruikers interface naar het gedeelte waarschuwing in het dash board.
       1. Zie [een cluster beheren en bewaken](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)voor meer informatie over waarschuwingen en oplossingen voor waarschuwingen.
    1. Bekijk uw garen geheugen. Als uw garen geheugen hoog is, kan de verwerking van uw taken worden vertraagd. Als er onvoldoende bronnen zijn voor uw Spark-toepassing/-taak, schaalt u het cluster zodanig dat het cluster voldoende geheugen en kernen heeft. 
 1. Een voor beeld van een test taak uitvoeren.
    1. Als u dezelfde taak uitvoert op HDInsight-back-end, controleert u of deze is geslaagd. Zie voor voor beelden van voorbeeld uitvoeringen [de MapReduce-voor beelden in HDInsight uitvoeren](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) 
 1. Als de taak nog steeds is mislukt op HDInsight, controleert u de logboeken en informatie over de toepassing, die u kunt bieden om ondersteuning te geven voor:
    1. Controleer of de taak is verzonden naar GARENs. Als de taak niet is verzonden naar garens, gebruikt u `--master yarn` .
    1. Als de toepassing is uitgevoerd, verzamelt u de begin tijd en eind tijd van de toepassing voor GARENs. Als de toepassing de uitvoering niet heeft voltooid, verzamelt u start tijd/start tijd.
    1. Controleer en verzamel het toepassings logboek met `yarn logs -applicationId <Insert_Your_Application_ID>` .
    1. Controleer en verzamel de logboeken van de garens-Resource Manager in de `/var/log/hadoop-yarn/yarn` Directory.
    1. Als deze stappen niet voldoende zijn om het probleem op te lossen, neemt u contact op met het Azure HDInsight-team voor ondersteuning en geeft u de bovenstaande logboeken en tijds tempels op.

### <a name="error-code-2303"></a>Fout code: 2303

- **Bericht**: `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Oorzaak**: de taak is verzonden naar het HDI-cluster en is daar mislukt.

- **Aanbeveling**: 

 1. Controleer de Ambari-gebruikers interface:
    1. Zorg ervoor dat alle services nog worden uitgevoerd.
    1. Ga in de Ambari-gebruikers interface naar het gedeelte waarschuwing in het dash board.
       1. Zie [een cluster beheren en bewaken](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)voor meer informatie over waarschuwingen en oplossingen voor waarschuwingen.
    1. Bekijk uw garen geheugen. Als uw garen geheugen hoog is, kan de verwerking van uw taken worden vertraagd. Als er onvoldoende bronnen zijn voor uw Spark-toepassing/-taak, schaalt u het cluster zodanig dat het cluster voldoende geheugen en kernen heeft. 
 1. Een voor beeld van een test taak uitvoeren.
    1. Als u dezelfde taak uitvoert op HDInsight-back-end, controleert u of deze is geslaagd. Zie voor voor beelden van voorbeeld uitvoeringen [de MapReduce-voor beelden in HDInsight uitvoeren](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) 
 1. Als de taak nog steeds is mislukt op HDInsight, controleert u de logboeken en informatie over de toepassing, die u kunt bieden om ondersteuning te geven voor:
    1. Controleer of de taak is verzonden naar GARENs. Als de taak niet is verzonden naar garens, gebruikt u `--master yarn` .
    1. Als de toepassing is uitgevoerd, verzamelt u de begin tijd en eind tijd van de toepassing voor GARENs. Als de toepassing de uitvoering niet heeft voltooid, verzamelt u start tijd/start tijd.
    1. Controleer en verzamel het toepassings logboek met `yarn logs -applicationId <Insert_Your_Application_ID>` .
    1. Controleer en verzamel de logboeken van de garens-Resource Manager in de `/var/log/hadoop-yarn/yarn` Directory.
    1. Als deze stappen niet voldoende zijn om het probleem op te lossen, neemt u contact op met het Azure HDInsight-team voor ondersteuning en geeft u de bovenstaande logboeken en tijds tempels op.

### <a name="error-code-2304"></a>Fout code: 2304

- **Bericht**: `MSI authentication is not supported on storages for HDI activities.`

- **Oorzaak**: de gekoppelde opslag services die worden gebruikt in de HDI-of HDI-activiteit van HDInsight, worden geconfigureerd met een MSI-verificatie die niet wordt ondersteund.

- **Aanbeveling**: geef volledige verbindings reeksen op voor opslag accounts die worden gebruikt in de HDI-gekoppelde service of de HDI-activiteit.

### <a name="error-code-2305"></a>Fout code: 2305

- **Bericht**: `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Oorzaak**: de verbindings gegevens voor het HDI-cluster zijn onjuist, de opgegeven gebruiker heeft geen machtigingen om de vereiste actie uit te voeren of de HDInsight-service heeft problemen met het reageren op aanvragen van ADF.

- **Aanbeveling**: Controleer of de gebruikers gegevens juist zijn en of de Ambari-gebruikers interface voor het HDI-cluster kan worden geopend in een browser van de VM waarop de IR is geïnstalleerd (voor een zelf-hostende IR) of kan worden geopend vanaf elke computer (voor Azure IR).

### <a name="error-code-2306"></a>Fout code: 2306

- **Bericht**: `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Oorzaak**: de JSON die voor de script actie is gegeven, is ongeldig.

- **Aanbeveling**: het fout bericht zou het probleem moeten kunnen identificeren. Los de JSON-configuratie op en probeer het opnieuw.

   Controleer de [gekoppelde Azure HDInsight-service op aanvraag](./compute-linked-services.md#azure-hdinsight-on-demand-linked-service) voor meer informatie.

### <a name="error-code-2310"></a>Fout code: 2310

- **Bericht**: `Failed to submit Spark job. Error: '%message;'`

- **Oorzaak**: ADF heeft geprobeerd een batch te maken op een Spark-cluster met behulp van de LIVY-API (livy/batch), maar er is een fout opgetreden.

- **Aanbeveling**: Volg het fout bericht om het probleem op te lossen. Als er niet voldoende informatie is om het probleem op te lossen, neemt u contact op met het HDI-team en geeft u de batch-ID en taak-ID op die u kunt vinden op de pagina activiteit uitvoer uitvoeren op de ADF-bewaking. Verzamel het volledige logboek van de batch-taak om het probleem verder op te lossen.

   Zie [het volledige logboek van een batch-taak ophalen](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)voor meer informatie over het verzamelen van het volledige logboek.

### <a name="error-code-2312"></a>Fout code: 2312

- **Bericht**: `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Oorzaak**: de taak is mislukt op het HDInsight Spark-cluster.

- **Aanbeveling**: Volg de koppelingen in de uitvoer van de activiteit uitvoeren in de ADF-bewakings pagina om de uitvoering van het uitvoeren van een HDInsight Spark-cluster op te lossen. Neem contact op met het HDInsight-ondersteunings team voor verdere ondersteuning.

   Zie [het volledige logboek van een batch-taak ophalen](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)voor meer informatie over het verzamelen van het volledige logboek.

### <a name="error-code-2313"></a>Fout code: 2313

- **Bericht**: `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Oorzaak**: de batch is verwijderd uit het HDInsight Spark-cluster.

- **Aanbeveling**: problemen met batches op het HDInsight Spark-cluster oplossen. Neem contact op met HDInsight-ondersteuning voor verdere ondersteuning. 

   Voor meer informatie over hoe u het volledige logboek kunt verzamelen, raadpleegt u [het volledige logboek van een batch-taak ophalen](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)en deelt u het volledige logboek met HDInsight-ondersteuning voor verdere ondersteuning.

### <a name="error-code-2328"></a>Fout code: 2328

- **Bericht**: `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Oorzaak**: in het fout bericht worden de details weer gegeven van wat er verkeerd is gegaan.

- **Aanbeveling**: het fout bericht moet u helpen het probleem op te lossen.

### <a name="error-code-2329"></a>Fout code: 2329

- **Bericht**: `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Oorzaak**: in het fout bericht worden de details weer gegeven van wat er verkeerd is gegaan.

- **Aanbeveling**: het fout bericht moet u helpen het probleem op te lossen.

### <a name="error-code-2331"></a>Fout code: 2331

- **Bericht**: `The file path should not be null or empty.`

- **Oorzaak**: het opgegeven bestandspad is leeg.

- **Aanbeveling**: Geef een pad op voor een bestand dat bestaat.

### <a name="error-code-2340"></a>Fout code: 2340

- **Bericht**: `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Oorzaak**: de gekoppelde HDInsightOnDemand-service biedt geen ondersteuning voor uitvoering via SelfHosted IR.

- **Aanbeveling**: selecteer een Azure IR en probeer het opnieuw.

### <a name="error-code-2341"></a>Fout code: 2341

- **Bericht**: `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Oorzaak**: de gegeven URL heeft niet de juiste indeling.

- **Aanbeveling**: Los de cluster-URL op en probeer het opnieuw.

### <a name="error-code-2342"></a>Fout code: 2342

- **Bericht**: `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Oorzaak**: de verstrekte referenties zijn onjuist voor het cluster, of er is een netwerk configuratie of verbindings probleem opgetreden of de IR ondervindt problemen bij het maken van verbinding met het cluster.

- **Aanbeveling**: 
    1. Controleer of de referenties juist zijn door de Ambari-gebruikers interface van het HDInsight-cluster in een browser te openen.
    1. Als het cluster zich in Virtual Network (VNet) bevindt en er een zelf-hostende IR wordt gebruikt, moet de HDI-URL de privé-URL in VNets zijn en moet '-int ' worden weer gegeven na de cluster naam.
    
       Wijzig bijvoorbeeld `https://mycluster.azurehdinsight.net/` in `https://mycluster-int.azurehdinsight.net/` . Let `-int` op de na `mycluster` , maar vóór `.azurehdinsight.net`
    1. Als het cluster zich in VNet bevindt, wordt de zelf-hostende IR gebruikt en wordt de persoonlijke URL gebruikt. de verbinding is echter nog steeds mislukt. vervolgens wordt de virtuele machine waarop de IR is geïnstalleerd problemen ondervonden met het verbinding maken met de HDI. 
    
       Maak verbinding met de virtuele machine waarop de IR is geïnstalleerd en open de Ambari-gebruikers interface in een browser. Gebruik de persoonlijke URL voor het cluster. Deze verbinding moet werken vanuit de browser. Als dat niet het geval is, neemt u contact op met het HDInsight-ondersteunings team.
    1. Als zelf-hostende IR niet wordt gebruikt, moet het HDI-cluster openbaar toegankelijk zijn. Open de Ambari-gebruikers interface in een browser en controleer of deze wordt geopend. Als er problemen zijn met het cluster of de services erop, neemt u contact op met het HDInsight-ondersteunings team voor hulp.

       De HDI-cluster-URL die wordt gebruikt in de aan ADF gekoppelde service moet toegankelijk zijn voor ADF IR (zelf-hostend of Azure), zodat de test verbinding kan worden door gegeven en voor uitvoeringen werkt. Deze status kan worden gecontroleerd door de URL te openen vanuit een browser van een virtuele machine of vanaf een open bare computer.

### <a name="error-code-2343"></a>Fout code: 2343

- **Bericht**: `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Oorzaak**: de gebruikers naam of het wacht woord is leeg.

- **Aanbeveling**: Geef de juiste referenties op om verbinding te maken met HDI en probeer het opnieuw.

### <a name="error-code-2345"></a>Fout code: 2345

- **Bericht**: `Failed to read the content of the hive script. Error: '%message;'`

- **Oorzaak**: het script bestand bestaat niet of de ADF kan geen verbinding maken met de locatie van het script.

- **Aanbeveling**: Controleer of het script bestaat en of de bijbehorende gekoppelde service de juiste referenties voor een verbinding heeft.

### <a name="error-code-2346"></a>Fout code: 2346

- **Bericht**: `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Oorzaak**: ADF heeft geprobeerd een Open Database Connectivity (ODBC)-verbinding te maken met het HDI-cluster en is mislukt met een fout.

- **Aanbeveling**: 

   1. Bevestig dat u uw verbinding met ODBC/Java-database connectiviteit (JDBC) correct hebt ingesteld.
      1. Als u gebruikmaakt van hetzelfde virtuele netwerk, kunt u voor JDBC het volgende doen:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Om ervoor te zorgen dat u de juiste JDBC hebt ingesteld, raadpleegt u [Query Apache Hive via het JDBC-stuur programma in HDInsight](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. Zie voor open data base (ODB) [zelf studie: Query Apache Hive met ODBC en Power shell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) om te controleren of u de juiste instelling hebt. 
   1. Controleer of Hiveserver2, Hive meta Store en Hiveserver2 Interactive actief en werk zijn. 
   1. Controleer de Ambari-gebruikers interface (UI):
      1. Zorg ervoor dat alle services nog worden uitgevoerd.
      1. Ga in de Ambari-gebruikers interface naar het gedeelte waarschuwing in het dash board.
         1. Zie [een cluster beheren en bewaken ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)voor meer informatie over waarschuwingen en oplossingen voor waarschuwingen.
   1. Als deze stappen niet voldoende zijn om het probleem op te lossen, neemt u contact op met het Azure HDInsight-team.

### <a name="error-code-2347"></a>Fout code: 2347

- **Bericht**: `Hive execution through ODBC failed with error message '%message;'.`

- **Oorzaak**: de ADF heeft het Hive-script verzonden voor uitvoering naar het HDI-cluster via een ODBC-verbinding en het script is mislukt op HDI.

- **Aanbeveling**: 

   1. Bevestig dat u uw verbinding met ODBC/Java-database connectiviteit (JDBC) correct hebt ingesteld.
      1. Als u gebruikmaakt van hetzelfde virtuele netwerk, kunt u voor JDBC het volgende doen:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Om ervoor te zorgen dat u de juiste JDBC hebt ingesteld, raadpleegt u [Query Apache Hive via het JDBC-stuur programma in HDInsight](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. Zie voor open data base (ODB) [zelf studie: Query Apache Hive met ODBC en Power shell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) om te controleren of u de juiste instelling hebt. 
   1. Controleer of Hiveserver2, Hive meta Store en Hiveserver2 Interactive actief en werk zijn. 
   1. Controleer de Ambari-gebruikers interface (UI):
      1. Zorg ervoor dat alle services nog worden uitgevoerd.
      1. Ga in de Ambari-gebruikers interface naar het gedeelte waarschuwing in het dash board.
         1. Zie [een cluster beheren en bewaken ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)voor meer informatie over waarschuwingen en oplossingen voor waarschuwingen.
   1. Als deze stappen niet voldoende zijn om het probleem op te lossen, neemt u contact op met het Azure HDInsight-team.

### <a name="error-code-2348"></a>Fout code: 2348

- **Bericht**: `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Oorzaak**: de eigenschappen van de gekoppelde opslag service zijn niet juist ingesteld.

- **Aanbeveling**: alleen volledige verbindings reeksen worden ondersteund in de hoofd opslag gekoppelde service voor HDI-activiteiten. Controleer of u geen gebruik maakt van MSI-autorisaties of-toepassingen.

### <a name="error-code-2350"></a>Fout code: 2350

- **Bericht**: `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Oorzaak**: de referenties die zijn gegeven om verbinding te maken met de opslag waar de bestanden zich bevinden, zijn onjuist of de bestanden bestaan er niet.

- **Aanbeveling**: deze fout treedt op wanneer ADF voor HDI-activiteiten voorbereidt en probeert bestanden te kopiëren naar de hoofd opslag voordat de taak wordt verzonden naar HDI. Controleer of de bestanden op de gegeven locatie bestaan en of de opslag verbinding juist is. Aangezien ADF HDI-activiteiten geen ondersteuning bieden voor MSI-verificatie voor opslag accounts die zijn gerelateerd aan HDI-activiteiten, moet u controleren of deze gekoppelde services volledige sleutels hebben of Azure Key Vault gebruiken.

### <a name="error-code-2351"></a>Fout code: 2351

- **Bericht**: `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Oorzaak**: het bestand bestaat niet op het opgegeven pad.

- **Aanbeveling**: Controleer of het bestand daad werkelijk bestaat en of de gekoppelde service met verbindings informatie die verwijst naar dit bestand, de juiste referenties heeft.

### <a name="error-code-2352"></a>Fout code: 2352

- **Bericht**: `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Oorzaak**: de eigenschappen van de gekoppelde service van het bestands archief zijn niet juist ingesteld.

- **Aanbeveling**: Controleer of de eigenschappen van de gekoppelde File Storage-service correct zijn geconfigureerd.

### <a name="error-code-2353"></a>Fout code: 2353

- **Bericht**: `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Oorzaak**: de eigenschappen van de gekoppelde service van de script opslag zijn niet juist ingesteld.

- **Aanbeveling**: Controleer of de eigenschappen van de gekoppelde script opslag-service correct zijn geconfigureerd.

### <a name="error-code-2354"></a>Fout code: 2354

- **Bericht**: `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Oorzaak**: het gekoppelde opslag service type wordt niet ondersteund door de activiteit.

- **Aanbeveling**: Controleer of de geselecteerde gekoppelde service een van de ondersteunde typen voor de activiteit heeft. HDI-activiteiten bieden ondersteuning voor AzureBlobStorage-en AzureBlobFSStorage gekoppelde services.

   Lees voor meer informatie [opslag opties vergelijken voor gebruik met Azure HDInsight-clusters](../hdinsight/hdinsight-hadoop-compare-storage-options.md)

### <a name="error-code-2355"></a>Fout code: 2355

- **Bericht**: `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Oorzaak**: de gegeven waarde voor `commandEnvironment` is onjuist.

- **Aanbeveling**: Controleer of de gegeven waarde er ongeveer als volgt uitziet:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Controleer ook of elke variabele slechts eenmaal wordt weer gegeven in de lijst.

### <a name="error-code-2356"></a>Fout code: 2356

- **Bericht**: `The commandEnvironment already contains a variable named '%variableName;'.`

- **Oorzaak**: de gegeven waarde voor `commandEnvironment` is onjuist.

- **Aanbeveling**: Controleer of de gegeven waarde er ongeveer als volgt uitziet:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Controleer ook of elke variabele slechts eenmaal wordt weer gegeven in de lijst.

### <a name="error-code-2357"></a>Fout code: 2357

- **Bericht**: `The certificate or password is wrong for ADLS Gen 1 storage.`

- **Oorzaak**: de gegeven referenties zijn onjuist.

- **Aanbeveling**: Controleer of de verbindings gegevens in ADLS gen 1 zijn gekoppeld aan de service en controleer of de test verbinding slaagt.

### <a name="error-code-2358"></a>Fout code: 2358

- **Bericht**: `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Oorzaak**: de opgegeven waarde voor de eigenschap Required `TimeToLive` heeft een ongeldige indeling. 

- **Aanbeveling**: werk de waarde bij naar het voorgestelde bereik en probeer het opnieuw.

### <a name="error-code-2359"></a>Fout code: 2359

- **Bericht**: `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Oorzaak**: de gegeven waarde voor de eigenschap `roles` is ongeldig.

- **Aanbeveling**: werk de waarde bij naar een van de suggesties en probeer het opnieuw.

### <a name="error-code-2360"></a>Fout code: 2360

- **Bericht**: `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Oorzaak**: de ingevoerde Connection String voor de `HCatalogLinkedService` is ongeldig.

- **Aanbeveling**: werk de waarde bij naar een juiste Azure SQL-Connection String en probeer het opnieuw.

### <a name="error-code-2361"></a>Fout code: 2361

- **Bericht**: `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Oorzaak**: het maken van het cluster is mislukt en de ADF heeft geen fout weer gegeven van de HDInsight-service.

- **Aanbeveling**: Open de Azure Portal en probeer de HDI-resource met de gegeven naam te vinden en controleer vervolgens de inrichtings status. Neem contact op met het HDInsight-ondersteunings team voor verdere ondersteuning.

### <a name="error-code-2362"></a>Fout code: 2362

- **Bericht**: `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Oorzaak**: de gegeven extra opslag is geen Azure Blob-opslag.

- **Aanbeveling**: Geef een Azure Blob-opslag account op als extra opslag ruimte voor de gekoppelde service op aanvraag van HDInsight.

### <a name="ssl-error-when-adf-linked-service-using-hdinsight-esp-cluster"></a>SSL-fout bij het koppelen van een ADF-service met het HDInsight ESP-cluster

- **Bericht**: `Failed to connect to HDInsight cluster: 'ERROR [HY000] [Microsoft][DriverSupport] (1100) SSL certificate verification failed because the certificate is missing or incorrect.`

- **Oorzaak**: het probleem heeft waarschijnlijk te maken met het vertrouwens archief van het systeem.

- **Oplossing**: u kunt navigeren naar het pad **micro soft Integration RUNTIME\4.0\SHARED\ODBC Drivers\Microsoft Hive ODBC Driver\lib** en DriverConfiguration64.exe openen om de instelling te wijzigen.

    ![Schakel het selectie vakje systeem vertrouwens opslag gebruiken uit](./media/connector-troubleshoot-guide/system-trust-store-setting.png)

## <a name="web-activity"></a>Web Activity

### <a name="error-code-2128"></a>Fout code: 2128

- **Bericht**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Oorzaak**: dit probleem wordt veroorzaakt door een netwerk verbinding, een DNS-fout, een validatie van het server certificaat of een time-out.

- **Aanbeveling**: Controleer of het eind punt dat u probeert te raken reageert op aanvragen. U kunt hulpprogram ma's zoals **Fiddler/postman** gebruiken.

### <a name="error-code-2108"></a>Fout code: 2108

- **Bericht**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Oorzaak**: de aanvraag is mislukt vanwege een onderliggend probleem, zoals een netwerk verbinding, een DNS-fout, een validatie van het server certificaat of een time-out.

- **Aanbeveling**: gebruik Fiddler/postman om de aanvraag te valideren.

#### <a name="more-details"></a>Meer informatie
**Fiddler** gebruiken om een HTTP-sessie van de bewaakte webtoepassing te maken:

1. Down load, installeer en open [Fiddler](https://www.telerik.com/download/fiddler).

1. Als uw webtoepassing gebruikmaakt van HTTPS, gaat u naar **hulpprogram ma's**  >  **Fiddler opties**  >  **https**.

   1. Selecteer op het tabblad HTTPS beide **vastleg ging-https maakt verbinding** en **VERsleutelt HTTPS-verkeer**.

      ![Opties voor Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Als uw toepassing gebruikmaakt van TLS/SSL-certificaten, voegt u het Fiddler-certificaat toe aan uw apparaat.

   Ga naar: **extra**  >  **Fiddler opties**  >  **https**  >  **acties**  >  **basis certificaat exporteren naar het bureau blad**.

1. Vastleggen uitschakelen door te gaan naar het **bestand**  >  **Capture-verkeer**. Of druk op **F12**.

1. Wis de cache van uw browser zodat alle items in de cache worden verwijderd en opnieuw moet worden gedownload.

1. Een aanvraag maken:

1. Selecteer het tabblad **Composer** .

   1. Stel de HTTP-methode en URL in.
 
   1. Voeg, indien nodig, kopteksten en een aanvraag tekst toe.

   1. Selecteer **Uitvoeren**.

1. Schakel het vastleggen van verkeer in en voltooi de problematische trans actie op de pagina.

1. Ga naar: **bestanden**  >    >  **alle sessies** opslaan.

Zie [aan de slag met Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)voor meer informatie.

## <a name="general"></a>Algemeen

### <a name="activity-stuck-issue"></a>Activiteit is vastgelopen

Wanneer u ziet dat de activiteit veel langer wordt uitgevoerd dan uw normale uitvoeringen zonder enige voortgang, kan het gebeuren dat deze vastloopt. U kunt het annuleren en opnieuw proberen om te zien of het helpt. Als het een Kopieer activiteit is, kunt u meer te weten komen over de prestatie bewaking en het oplossen van problemen met de prestaties van de [Kopieer activiteit](copy-activity-performance-troubleshooting.md). Als het een gegevens stroom is, kunt u leren van het [toewijzen van gegevens stromen prestaties](concepts-data-flow-performance.md) en afstemmings handleiding.

### <a name="payload-is-too-large"></a>De nettolading is te groot

**Fout bericht:**`The payload including configurations on activity/dataSet/linked service is too large. Please check if you have settings with very large value and try to reduce its size.`

**Oorzaak:** De payload voor elke uitvoering van de activiteit omvat de configuratie van de activiteit, de bijbehorende gegevensset (s) en de gekoppelde service (s) configuraties, indien van toepassing, en een klein gedeelte van de systeem eigenschappen die per type activiteit worden gegenereerd. De maximale grootte van deze nettolading is 896 KB, zoals vermeld in de sectie [Data Factory limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) .

**Aanbeveling:** U bereikt waarschijnlijk deze limiet omdat u een of meer grote parameter waarden doorgeeft vanuit de upstream-activiteit of extern, met name als u werkelijke gegevens doorgeeft over activiteiten in de controle stroom. Controleer of u de grootte van grote parameter waarden kunt beperken of uw pijplijn logica wilt afstemmen om te voor komen dat dergelijke waarden worden door gegeven aan activiteiten in plaats daarvan in de activiteit.

## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:

* [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
* [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
* [Azure-video's](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&A-vragenpagina](/answers/topics/azure-data-factory.html)
