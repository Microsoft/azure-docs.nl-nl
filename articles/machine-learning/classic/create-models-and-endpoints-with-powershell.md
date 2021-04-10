---
title: 'ML Studio (klassiek): meerdere modellen maken & eind punten-Azure'
description: Gebruik Power shell om meerdere Machine Learning modellen en web service-eind punten te maken met dezelfde algoritme, maar met verschillende trainings gegevens sets.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: 35b5fe4556f1d557d3fc0420e9069f2fb510eec4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520507"
---
# <a name="create-multiple-web-service-endpoints-from-one-experiment-with-ml-studio-classic-and-powershell"></a>Meerdere webservice-eind punten maken van een experiment met ML Studio (klassiek) en Power shell

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Hier volgt een veelvoorkomend machine learning probleem: u wilt een groot aantal modellen maken die dezelfde werk stroom hebben en dezelfde algoritme gebruiken. Maar u wilt dat ze verschillende trainings gegevens sets als invoer hebben. Dit artikel laat u zien hoe u dit kunt doen op schaal in Azure Machine Learning Studio (klassiek) door slechts één experiment te gebruiken.

Stel dat u bent eigenaar van een wereld wijde franchise in de onderneming. U wilt een regressie model maken om de huur vraag te voors pellen op basis van historische gegevens. U hebt 1.000 verhuur locaties over de hele wereld en u hebt een gegevensset verzameld voor elke locatie. Ze bevatten belang rijke functies zoals datum, tijd, weer en verkeer die specifiek zijn voor elke locatie.

U kunt uw model eenmaal trainen met behulp van een samengevoegde versie van alle gegevens sets op alle locaties. Maar elk van uw locaties heeft een unieke omgeving. Het is dus een betere benadering om uw regressie model afzonderlijk te trainen met behulp van de gegevensset voor elke locatie. Op die manier kan elk getraind model rekening houden met de verschillende opslag grootten, het volume, de geografie, de populatie, de gebruiks vriendelijke verkeers omgeving en nog veel meer.

Dat kan de beste aanpak zijn, maar u wilt geen 1.000-trainings experimenten in Azure Machine Learning Studio (klassiek) maken, met elk een unieke locatie. Naast een overweldigende taak lijkt het ook inefficiënt omdat elk experiment dezelfde onderdelen zou hebben, met uitzonde ring van de trainings gegevensset.

Gelukkig kunt u dit doen met behulp van de API voor opnieuw [trainen van Azure machine learning Studio (klassiek)](./retrain-machine-learning-model.md) en de taak automatiseren met [Azure machine learning Studio (klassiek) Power shell](powershell-module.md).

> [!NOTE]
> Als u het voor beeld sneller wilt uitvoeren, kunt u het aantal locaties van 1.000 tot 10 verlagen. Dezelfde principes en procedures zijn echter van toepassing op de 1.000-locaties. Als u echter wilt trainen van 1.000-gegevens sets, wilt u mogelijk de volgende Power shell-scripts parallel uitvoeren. Hoe u dit doet, valt buiten het bereik van dit artikel, maar u vindt hier ook voor beelden van Power shell-multi threading op internet.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Het trainings experiment instellen
Gebruik het voorbeeld [experiment](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) in de [Cortana Intelligence Gallery](https://gallery.azure.ai). Open dit experiment in de werk ruimte [Azure machine learning Studio (klassiek)](https://studio.azureml.net) .

> [!NOTE]
> Als u dit voor beeld wilt volgen, kunt u een standaard werkruimte gebruiken in plaats van een gratis werk ruimte. U maakt één eind punt voor elke klant, voor een totaal van 10 eind punten. hiervoor is een Standard-werk ruimte vereist, omdat een gratis werk ruimte is beperkt tot drie eind punten.
> 
> 

Het experiment maakt gebruik van een **import gegevens** module voor het importeren van de trainings gegevensset *customer001.csv* van een Azure-opslag account. We gaan ervan uit dat u trainings gegevens sets hebt verzameld van alle uithuur locaties van fietsen en ze hebt opgeslagen op dezelfde Blob-opslag locatie met bestands namen, variërend van *rentalloc001.csv* naar *rentalloc10.csv*.

![In de lezers module worden gegevens uit een Azure-Blob geïmporteerd](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Houd er rekening mee dat een webservice-module voor het **uitvoeren van webservices** is toegevoegd aan de module **Train model** .
Wanneer dit experiment wordt geïmplementeerd als een webservice, retourneert het eind punt dat is gekoppeld aan de uitvoer, het getrainde model in de indeling van een. ilearner-bestand.

U kunt ook een webservice-para meter instellen waarmee de URL wordt gedefinieerd die wordt gebruikt in de module **import data** . Zo kunt u de para meter gebruiken om afzonderlijke trainings gegevens sets op te geven voor het trainen van het model voor elke locatie.
U kunt dit op andere manieren doen. U kunt een SQL-query met een webservice-para meter gebruiken om gegevens op te halen uit een data base in Azure SQL Database. U kunt ook een module voor het  **invoeren van webservices** gebruiken om in een gegevensset door te geven aan de webservice.

![Een getrainde model module wordt uitgevoerd in een webservice-module voor het uitvoeren van webservices](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Nu gaan we dit trainings experiment uitvoeren met de standaard waarde *rental001.csv* als trainings gegevensset. Als u de uitvoer van de module **Evaluate** bekijkt (Klik op de uitvoer en selecteert u **visualiseren**), ziet u een goede prestaties van *AUC* = 0,91. Op dit moment bent u klaar om een webservice te implementeren uit dit trainings experiment.

## <a name="deploy-the-training-and-scoring-web-services"></a>De training-en Score-webservices implementeren
Als u de training-webservice wilt implementeren, klikt u op de knop **webservice instellen** onder het canvas op het experiment en selecteert u **Web service implementeren**. Deze webservice aanroepen ' fiets huren oefenen '.

Nu moet u de Score-webservice implementeren.
Hiertoe klikt u op **webservice instellen** onder het canvas en selecteert u **Predictive web service**. Hiermee maakt u een score experiment.
U moet enkele kleine aanpassingen aanbrengen om de service als een webservice te kunnen gebruiken. De label kolom "Cnt" uit de invoer gegevens verwijderen en de uitvoer beperken tot alleen de exemplaar-id en de bijbehorende voorspelde waarde.

Als u uw werk wilt opslaan, kunt u het [voorspellende experiment](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) openen in de galerie die al is voor bereid.

Als u de webservice wilt implementeren, voert u het voorspellende experiment uit en klikt u op de knop **webservice implementeren** onder het canvas. Geef een naam op voor de Score-webservice voor het afhuren van fietsen.

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>10 identieke webservice-eind punten maken met Power shell
Deze webservice wordt geleverd met een standaard eindpunt. Maar u bent niet zo geïnteresseerd in het standaard eindpunt omdat het niet kan worden bijgewerkt. Wat u moet doen, is om tien extra eind punten te maken, één voor elke locatie. U kunt dit doen met Power shell.

Eerst stelt u de Power shell-omgeving in:

```powershell
Import-Module .\AzureMLPS.dll
# Assume the default configuration file exists and is properly set to point to the valid Workspace.
$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'
```

Voer vervolgens de volgende Power shell-opdracht uit:

```powershell
# Create 10 endpoints on the scoring web service.
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    Write-Host ('adding endpoint ' + $endpointName + '...')
    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
}
```

U hebt nu 10 eind punten gemaakt en deze bevatten allemaal hetzelfde getrainde model dat is getraind op *customer001.csv*. U kunt deze weer geven in de Azure Portal.

![De lijst met getrainde modellen weer geven in de portal](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>De eind punten bijwerken om afzonderlijke trainings gegevens sets te gebruiken met Power shell
De volgende stap is het bijwerken van de eind punten met modellen die uniek zijn getraind voor elke afzonderlijke gegevens van de klant. Maar eerst moet u deze modellen maken op basis van de trainings-webservice voor de **fiets** . We gaan terug naar de web-service voor de **huur training** van de fiets. U moet het BES-eind punt 10 keer aanroepen met tien verschillende trainings gegevens sets om 10 verschillende modellen te kunnen produceren. Gebruik hiervoor de Power shell-cmdlet **InovkeAmlWebServiceBESEndpoint** .

U moet ook referenties voor uw Blob Storage-account opgeven in `$configContent` . Dat wil zeggen, in de velden `AccountName` , `AccountKey` en `RelativeLocation` . Dit `AccountName` kan een van uw account namen zijn, zoals wordt weer gegeven op het tabblad **Azure Portal** (*opslag* ). Nadat u op een opslag account hebt geklikt, kunt u het `AccountKey` vinden door op de knop **toegangs sleutels beheren** onderaan te drukken en de *primaire toegangs sleutel* te kopiëren. Het `RelativeLocation` is het pad dat relatief is ten opzichte van uw opslag locatie waar een nieuw model wordt opgeslagen. Zo verwijst het pad `hai/retrain/bike_rental/` in het volgende script naar een container met de naam `hai` en `/retrain/bike_rental/` submappen zijn. Op dit moment kunt u geen submappen maken via de portal-gebruikers interface, maar er zijn [verschillende Azure Storage Explorers](../../storage/common/storage-explorers.md) waarmee u dit kunt doen. Het is raadzaam om een nieuwe container in uw opslag ruimte te maken om de nieuwe getrainde modellen (iLearner-bestanden) als volgt op te slaan: Klik op de knop **toevoegen** onder aan de pagina opslag en geef deze de naam `retrain` . In samen vatting zijn de benodigde wijzigingen in het volgende script van toepassing op `AccountName` , `AccountKey` en `RelativeLocation` (: `"retrain/model' + $seq + '.ilearner"` ).

```powershell
# Invoke the retraining API 10 times
# This is the default (and the only) endpoint on the training web service
$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
$apiKey = $trainingSvcEp.PrimaryKey;
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
}
```

> [!NOTE]
> Het BES-eind punt is de enige ondersteunde modus voor deze bewerking. Bron records kunnen niet worden gebruikt voor het produceren van getrainde modellen.
> 
> 

Zoals hierboven wordt weer geven, kunt u in plaats van tien verschillende BES-taak configuratie json-bestanden maken. in plaats daarvan wordt de configuratie teken reeks dynamisch gemaakt. Voer de invoer vervolgens door naar de para meter *jobConfigString* van de cmdlet **InvokeAmlWebServceBESEndpoint** . Het is niet nodig om een kopie op de schijf op te slaan.

Als alles goed gaat, moet u na een tijdje 10. iLearner-bestanden van *model001. iLearner* naar *model010. ILearner* in uw Azure Storage-account zien. Nu bent u klaar om de 10 scores-webservice-eind punten bij te werken met deze modellen met behulp van de Power shell **-cmdlet patch-AmlWebServiceEndpoint** . Vergeet niet dat u alleen de niet-standaard-eind punten kunt bijwerken die u eerder programmatisch hebt gemaakt.

```powershell
# Patch the 10 endpoints with respective .ilearner models
$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
$sasToken = '<my_blob_sas_token>'
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
    Write-Host ('Patching endpoint ' + $endpointName + '...');
    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
}
```

Dit moet redelijk snel worden uitgevoerd. Wanneer de uitvoering is voltooid, hebt u 10 voorspellende webservice-eind punten gemaakt. Elk van deze bevat een getraind model dat speciaal is getraind op de gegevensset die specifiek is voor een huur locatie, allemaal uit één Oefen experiment. Als u dit wilt controleren, kunt u deze eind punten aanroepen met behulp van de **InvokeAmlWebServiceRRSEndpoint** -cmdlet, zodat ze dezelfde invoer gegevens leveren. U moet rekening houden met verschillende Voorspellings resultaten omdat de modellen zijn getraind met verschillende trainings sets.

## <a name="full-powershell-script"></a>Volledige Power shell-script
Hier volgt een overzicht van de volledige bron code:

```powershell
Import-Module .\AzureMLPS.dll
# Assume the default configuration file exists and properly set to point to the valid workspace.
$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

# Create 10 endpoints on the scoring web service
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    Write-Host ('adding endpoint ' + $endpontName + '...')
    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
}

# Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
$apiKey = $trainingSvcEp.PrimaryKey;
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
}

# Patch the 10 endpoints with respective .ilearner models
$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
$sasToken = '?test'
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
    Write-Host ('Patching endpoint ' + $endpointName + '...');
    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
}
```