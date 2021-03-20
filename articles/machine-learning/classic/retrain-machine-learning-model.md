---
title: 'ML Studio (klassiek): een webservice opnieuw trainen-Azure'
description: Meer informatie over het bijwerken van een webservice voor het gebruik van een pas getraind machine learning model in Azure Machine Learning Studio (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: a4fe9e54e5e03a8dbf2a727b22f784c36d6c65f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517583"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Een machine learning model opnieuw trainen en implementeren

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Retraining is een manier om ervoor te zorgen dat machine learning modellen nauw keurig blijven en op basis van de meest relevante gegevens die beschikbaar zijn. In dit artikel wordt beschreven hoe u een machine learning model opnieuw traint en implementeert als nieuwe webservice in Studio (klassiek). Als u een klassieke webservice opnieuw wilt trainen, [raadpleegt u dit artikel met instructies.](retrain-classic-web-service.md)

In dit artikel wordt ervan uitgegaan dat u al een voorspellende webservice hebt geïmplementeerd. Als u nog geen voorspellende webservice hebt, leest u [hier hoe u hier een studio-webservice (Classic) implementeert.](deploy-a-machine-learning-web-service.md)

U kunt deze stappen volgen om een machine learning nieuwe webservice opnieuw te trainen en te implementeren:

1. Een **retraining-webservice** implementeren
1. Een nieuw model trainen met behulp van uw **retraining-webservice**
1. Werk uw bestaande **voorspellende experiment** bij om het nieuwe model te gebruiken

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>De webservice voor retraining implementeren

Met een Retrain web service kunt u uw model opnieuw trainen met een nieuwe set para meters, zoals nieuwe gegevens, en deze op een later tijdstip opslaan. Wanneer u een **webservice-uitvoer**  verbindt met een **Train-model**, voert het trainings experiment een nieuw model uit dat u wilt gebruiken.

Gebruik de volgende stappen voor het implementeren van een retraining-webservice:

1. Verbind een module voor het **invoeren van webservices** met uw gegevens invoer. Normaal gesp roken wilt u er zeker van zijn dat uw invoer gegevens op dezelfde manier worden verwerkt als de oorspronkelijke trainings gegevens.
1. Verbind een **webservice-uitvoer** module met de uitvoer van uw Train- **model**.
1. Als u een module voor het evalueren van een **model** hebt, kunt u een **webservice-uitvoer** module verbinden om de evaluatie resultaten uit te voeren
1. Voer uw experiment uit.

    Na het uitvoeren van het experiment moet de resulterende werk stroom lijken op de volgende afbeelding:

    ![Resulterende werk stroom](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Nu kunt u het trainings experiment implementeren als een retraining-webservice die een getraind model en de evaluatie resultaten van modellen uitvoert.

1. Klik aan de onderkant van het canvas op **set web service** .
1. Selecteer **webservice implementeren [Nieuw]**. De Azure Machine Learning Web Services-portal wordt geopend op de pagina **Web service implementeren** .
1. Typ een naam voor de webservice en kies een betalings plan.
1. Selecteer **Implementeren**.

## <a name="retrain-the-model"></a>Het model opnieuw trainen

Voor dit voor beeld gebruiken we C# om de toepassing voor opnieuw trainen te maken. U kunt ook python-of R-voorbeeld code gebruiken om deze taak uit te voeren.

Gebruik de volgende stappen voor het aanroepen van de Api's voor retraining:

1. Een C#-console toepassing maken in Visual Studio: **Nieuw**  >  **project**  >  **Visual C#**  >  **Windows klassieke bureau blad**  >  **console-app (.NET Framework)**.
1. Meld u aan bij de Machine Learning Web Services-portal.
1. Klik op de webservice waarmee u wilt werken.
1. Klik op **verbruik**.
1. Klik onder aan de pagina **verbruik** in het gedeelte **voorbeeld code** op **batch**.
1. Kopieer de C#-voorbeeld code voor batch uitvoering en plak deze in het bestand Program. cs. Zorg ervoor dat de naam ruimte intact blijft.

Voeg het NuGet-pakket micro soft. AspNet. WebApi. client toe, zoals is opgegeven in de opmerkingen. Als u de verwijzing naar Microsoft.WindowsAzure.Storage.dll wilt toevoegen, moet u mogelijk de [client bibliotheek voor Azure Storage-services](https://www.nuget.org/packages/WindowsAzure.Storage)installeren.

Op de volgende scherm afbeelding ziet u de pagina **verbruik** in de Azure machine learning webservices-Portal.

![Pagina verbruiken](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>De apikey-declaratie bijwerken

Zoek de **apikey** -declaratie:

```csharp
const string apiKey = "abc123"; // Replace this with the API key for the web service
```

Zoek in de sectie **basis informatie** over het gebruik van de pagina **verbruik** de primaire sleutel en kopieer deze naar de **apikey** -declaratie.

### <a name="update-the-azure-storage-information"></a>De Azure Storage gegevens bijwerken

De voorbeeld code BES uploadt een bestand van een lokaal station (bijvoorbeeld ' C:\temp\CensusInput.csv ') naar Azure Storage, verwerkt het en schrijft de resultaten terug naar Azure Storage.

1. Aanmelden bij Azure Portal
1. Klik in de linker navigatie kolom op **meer services**, zoek naar **opslag accounts** en selecteer deze.
1. Selecteer in de lijst met opslag accounts een om het opnieuw getrainde model op te slaan.
1. Klik in de linker navigatie kolom op **toegangs toetsen**.
1. Kopieer de **primaire toegangs sleutel** en sla deze op.
1. Klik in de linker navigatie kolom op **blobs**.
1. Selecteer een bestaande container, of maak een nieuwe, en sla de naam op.

Zoek de *StorageAccountName*-, *StorageAccountKey*-en *StorageContainerName* -declaraties en werk de waarden bij die u hebt opgeslagen in de portal.

```csharp
const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name
```

U moet er ook voor zorgen dat het invoer bestand beschikbaar is op de locatie die u opgeeft in de code.

### <a name="specify-the-output-location"></a>De uitvoer locatie opgeven

Wanneer u de uitvoer locatie in de aanvraag lading opgeeft, moet de extensie van het bestand dat is opgegeven in *RelativeLocation* worden opgegeven als `ilearner` .

```csharp
Outputs = new Dictionary<string, AzureBlobDataReference>() {
    {
        "output1",
        new AzureBlobDataReference()
        {
            ConnectionString = storageConnectionString,
            RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
        }
    },
```

Hier volgt een voor beeld van het opnieuw trainen van uitvoer:

![Uitvoer opnieuw trainen](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>De resultaten van het opnieuw trainen evalueren

Wanneer u de toepassing uitvoert, bevat de uitvoer de token van de URL en Shared Access signatures die nodig zijn voor toegang tot de evaluatie resultaten.

U kunt de prestatie resultaten van het opnieuw getrainde model weer geven door de *BaseLocation*, *RelativeLocation* en *SasBlobToken* van de uitvoer resultaten voor *OUTPUT2* te combi neren en de volledige URL te plakken in de adres balk van de browser.

Bekijk de resultaten om te bepalen of het nieuwe getrainde model beter presteert dan het bestaande.

Sla de *BaseLocation*-, *RelativeLocation*-en *SasBlobToken* op uit de uitvoer resultaten.

## <a name="update-the-predictive-experiment"></a>Het voorspellende experiment bijwerken

### <a name="sign-in-to-azure-resource-manager"></a>Aanmelden bij Azure Resource Manager

Meld u eerst aan bij uw Azure-account vanuit de Power shell-omgeving met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

### <a name="get-the-web-service-definition-object"></a>Het definitie object van de webservice ophalen

Ga vervolgens naar het web service-definitie object door de cmdlet [Get-AzMlWebService](/powershell/module/az.machinelearning/get-azmlwebservice) aan te roepen.

```azurepowershell
$wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

Als u de naam van de resource groep van een bestaande webservice wilt bepalen, voert u de Get-AzMlWebService cmdlet zonder para meters uit om de webservices in uw abonnement weer te geven. Zoek de webservice en controleer de webservice-ID. De naam van de resource groep is het vierde element in de ID, net na het *resourceGroups* -element. In het volgende voor beeld is de naam van de resource groep standaard-MachineLearning-SouthCentralUS.

```azurepowershell
Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
Name : RetrainSamplePre.2016.8.17.0.3.51.237
Location : South Central US
Type : Microsoft.MachineLearning/webServices
Tags : {}
```

Als u de naam van de resource groep van een bestaande webservice wilt bepalen, meldt u zich aan bij de Azure Machine Learning Web Services-portal. Selecteer de webservice. De naam van de resource groep is het vijfde element van de URL van de webservice, net na het *resourceGroups* -element. In het volgende voor beeld is de naam van de resource groep standaard-MachineLearning-SouthCentralUS.

`https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237`

### <a name="export-the-web-service-definition-object-as-json"></a>Het web service-definitie object exporteren als JSON

Als u de definitie van het getrainde model voor het gebruik van het pas getrainde model wilt wijzigen, moet u eerst de cmdlet [export-AzMlWebService](/powershell/module/az.machinelearning/export-azmlwebservice) gebruiken om het te exporteren naar een bestand met JSON-indeling.

```azurepowershell
Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-reference-to-the-ilearner-blob"></a>De verwijzing naar de ilearner-BLOB bijwerken

Zoek in de activa het [getrainde model], werk de *URI* -waarde in het *locationInfo* -knoop punt bij met de URI van de ilearner-blob. De URI wordt gegenereerd door het combi neren van de *BaseLocation* en de *RelativeLocation* van de uitvoer van de aanroep van de BES-training.

```json
"asset3": {
    "name": "Retrain Sample [trained model]",
    "type": "Resource",
    "locationInfo": {
        "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
    },
    "outputPorts": {
        "Results dataset": {
            "type": "Dataset"
        }
    }
},
```

### <a name="import-the-json-into-a-web-service-definition-object"></a>De JSON importeren in een web service definition-object

Gebruik de cmdlet [import-AzMlWebService](/powershell/module/az.machinelearning/import-azmlwebservice) om het gewijzigde JSON-bestand terug te converteren naar een web service-definitie object dat u kunt gebruiken om het predikaat bij te werken.

```azurepowershell
$wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-web-service"></a>De webservice bijwerken

Gebruik ten slotte de cmdlet [Update-AzMlWebService](/powershell/module/az.machinelearning/update-azmlwebservice) om het voorspellende experiment bij te werken.

```azurepowershell
Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het beheren van webservices of het bijhouden van meerdere experimenten.

* [De Web Services-portal verkennen](manage-new-webservice.md)
* [Experimentherhalingen beheren](manage-experiment-iterations.md)