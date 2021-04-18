---
title: Uw eigen ML in uw Azure Sentinel | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u uw eigen algoritmen machine learning gebruiken voor gegevensanalyse in Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: 2164b8ac6e62b8826d5879da07384769c503bfb5
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598598"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>Uw eigen Machine Learning (ML) in Azure Sentinel

Machine Learning (ML) is een van de belangrijkste kenmerken van Azure Sentinel, en een van de belangrijkste kenmerken die dit onderscheidt. Azure Sentinel biedt ML in verschillende ervaringen: ingebouwd [](fusion.md) in de Fusion-correlatie-engine en Jupyter-notebooks en het nieuw beschikbare BYO ML-platform (Build-Your-Own ML). 

ML-detectiemodellen kunnen worden aangepast aan afzonderlijke omgevingen en aan wijzigingen in het gedrag van gebruikers, om fout-positieven te verminderen en bedreigingen te identificeren die niet zouden worden gevonden met een traditionele benadering. Veel beveiligingsorganisaties begrijpen de waarde van ML voor beveiliging, hoewel niet veel van hen de overbodige ervaring hebben van professionals die ervaring hebben met zowel beveiliging als ML. We hebben het hier gepresenteerde framework ontworpen voor beveiligingsorganisaties en professionals om mee te groeien in hun ML-traject. Organisaties die geen ervaring hebben met ML, of zonder de benodigde expertise, kunnen een aanzienlijke beveiligingswaarde krijgen uit Azure Sentinel ingebouwde ML-mogelijkheden van de organisatie.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="machine learning framework":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>Wat is het BYO-ML-platform (Bring Your Own Machine Learning) ?

Voor organisaties die ML-resources hebben en aangepaste ML-modellen willen bouwen voor hun unieke zakelijke behoeften, bieden we het **BYO-ML-platform.** Het platform maakt gebruik van de [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) / [Apache Spark](http://spark.apache.org/) en Jupyter Notebooks om de ML-omgeving te produceren. Deze bevat de volgende onderdelen:

- een BYO-ML-pakket, dat bibliotheken bevat om u te helpen bij het openen van gegevens en het terug pushen van de resultaten naar Log Analytics (LA), zodat u de resultaten kunt integreren met uw detectie, onderzoek en opsporing. 

- ML-algoritmesjablonen die u kunt aanpassen aan specifieke beveiligingsproblemen in uw organisatie. 

- voorbeeldnotenotenotes om het model te trainen en de score van het model te plannen. 

Bovendien kunt u uw eigen ML-modellen en/of uw eigen Spark-omgeving gebruiken om te integreren met Azure Sentinel.

Met het BYO-ML-platform kunt u snel aan de slag met het bouwen van uw eigen ML-modellen: 

- Het notebook met voorbeeldgegevens helpt u om end-to-end een praktijkervaring te krijgen, zonder dat u zich zorgen hoeft te maken over de verwerking van productiegegevens.

- Het pakket dat is geïntegreerd met de Spark-omgeving vermindert de uitdagingen en frictie bij het beheren van de infrastructuur.

- De bibliotheken ondersteunen gegevensverloop. Trainings- en scoring-notebooks demonstreren de end-to-end-ervaring en fungeren als sjabloon om u aan te passen aan uw omgeving.

### <a name="use-cases"></a>Gebruiksvoorbeelden
 
Het BYO-ML-platform en -pakket verminderen de tijd en moeite die u nodig hebt om uw eigen ML-detecties te bouwen, en ze bieden de mogelijkheid om specifieke beveiligingsproblemen in uw Azure Sentinel. Het platform ondersteunt de volgende gebruiksgevallen:

**Een ML-algoritme trainen om een aangepast model op te halen:** U kunt een bestaand ML-algoritme gebruiken (gedeeld door Microsoft of de gebruikerswereld) en het eenvoudig trainen op uw eigen gegevens om een aangepast ML-model te krijgen dat beter past bij uw gegevens en omgeving.

**Een ML-algoritmesjabloon wijzigen om een aangepast model op te halen:** U kunt een ML-algoritmesjabloon wijzigen (gedeeld door Microsoft of de gebruikerswereld) en het gewijzigde algoritme trainen op uw eigen gegevens om een aangepast model af te leiden dat past bij uw specifieke probleem.

**Uw eigen model maken:** Maak uw eigen model met behulp Azure Sentinel byo-ML-platform en hulpprogramma's van uw bedrijf.

**Uw Databricks-/Spark-omgeving integreren:** Integreer uw bestaande Databricks/Spark-omgeving in Azure Sentinel en gebruik BYO-ML-bibliotheken en -sjablonen om ML-modellen te bouwen voor hun unieke situaties.

**Importeer uw eigen ML-model:** U kunt uw eigen ML-modellen importeren en het BYO-ML-platform en hulpprogramma's gebruiken om ze te integreren met Azure Sentinel.

**Een ML-algoritme delen:** Deel een ML-algoritme om de community te laten over adopteren en zich aan te passen.

**Gebruik ML om SecOps mogelijk** te maken: gebruik uw eigen aangepaste ML-model en resultaten voor opsporing, detectie, onderzoek en reactie.

In dit artikel worden de onderdelen van het BYO-ML-platform beschreven en wordt beschreven hoe u het platform en het algoritme Voor afwijkende resourcetoegang kunt gebruiken om een aangepaste ML-detectie te bieden met Azure Sentinel.

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark-omgeving

[Apache Spark™](http://spark.apache.org/) een stap vooruit gemaakt bij het vereenvoudigen van big data door een uniform framework te bieden voor het bouwen van gegevenspijplijnen. Azure Databricks gaat hiermee verder door een cloudplatform zonder beheer te bieden dat is gebouwd rond Spark. We raden u aan Databricks te gebruiken voor uw BYO-ML-platform, zodat u zich kunt richten op het vinden van antwoorden die direct van invloed zijn op uw bedrijf, in plaats van de problemen met gegevenspijplijnen en platformen op te lossen.
Als u al een Databricks- of een andere Spark-omgeving hebt en de bestaande installatie liever wilt gebruiken, werkt het BYO-ML-pakket ook goed op deze omgevingen. 

## <a name="byo-ml-package"></a>BYO-ML-pakket

Het BYO ML-pakket bevat de best practices en onderzoek van Microsoft aan de front-end van de ML voor beveiliging. In dit pakket bieden we de volgende lijst met hulpprogramma's, notebooks en algoritmesjablonen voor beveiligingsproblemen.

| Bestandsnaam | Description |
| --------- | ----------- |
| azure_sentinel_utilities.whl | Bevat hulpprogramma's voor het lezen van blobs uit Azure en het schrijven naar Log Analytics. |
| AnomalousRASampleData | Notebook demonstreert het gebruik van het anomalous Resource Access-model in Azure Sentinel gegenereerde trainings- en testvoorbeeldgegevens. |
| AnomalousRATraining.ipynb | Notebook om het algoritme te trainen, de modellen te bouwen en op te slaan. |
| AnomalousRAScoring.ipynb | Notebook voor het plannen van de uitvoering van het model, het visualiseren van het resultaat en het terugschrijven van de score Azure Sentinel. |
|

De eerste ml-algoritmesjabloon die we aanbieden, is voor de detectie [van afwijkende resourcetoegang.](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML) Het is gebaseerd op een filteralgoritme voor samenwerking en is getraind met toegangslogboeken voor Windows-bestands delen (beveiligingsgebeurtenissen met gebeurtenis-id 5140). De belangrijkste informatie die u nodig hebt voor dit model in het logboek is het koppelen van gebruikers en bronnen die worden gebruikt. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Voorbeeld van walkthrough: Afwijkende toegangsdetectie voor bestands share 

Nu u bekend bent met de belangrijkste onderdelen van het BYO-ML-platform, is hier een voorbeeld dat u laat zien hoe u het platform en de onderdelen kunt gebruiken om een aangepaste ML-detectie te leveren.

### <a name="setup-the-databricksspark-environment"></a>De Databricks-/Spark-omgeving instellen

U moet uw eigen Databricks-omgeving instellen als u er nog geen hebt. Raadpleeg het [snelstartdocument voor Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal) voor instructies.

### <a name="auto-export-instruction"></a>Instructie voor automatisch exporteren

Als u aangepaste ML-modellen wilt bouwen op basis van uw eigen gegevens in Azure Sentinel, moet u uw gegevens exporteren van Log Analytics naar een Blob Storage- of Event Hub-resource, zodat het ML-model er toegang toe heeft vanuit Databricks. Meer informatie over [het opnemen van gegevens in Azure Sentinel](connect-data-sources.md).

Voor dit voorbeeld moet u uw trainingsgegevens voor het toegangslogboek voor bestands delen in de Azure Blob-opslag hebben. De indeling van de gegevens wordt beschreven in het notebook en de bibliotheken.

U kunt uw gegevens automatisch exporteren vanuit Log Analytics met behulp van [de Azure CLI (Command Line Interface).](/cli/azure/monitor/log-analytics) 

De rol Inzender  moet aan u zijn toegewezen in uw Log Analytics-werkruimte, uw opslagaccount en uw EventHub-resource om de opdrachten uit te voeren. 

Hier is een voorbeeld van een reeks opdrachten voor het instellen van automatisch exporteren:

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>Aangepaste gegevens exporteren

Voor aangepaste gegevens die niet worden ondersteund door de automatische export van Log Analytics, kunt u logische apps of andere oplossingen gebruiken om uw gegevens te verplaatsen. Raadpleeg de blog en het script [Exporting Log Analytics Data to Blob Store (Log Analytics-gegevens exporteren](https://techcommunity.microsoft.com/t5/azure-monitor/log-analytics-data-export-preview/ba-p/1783530) naar Blob Store).

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Correleren met gegevens buiten Azure Sentinel

U kunt ook gegevens van buiten de Azure Sentinel naar de blobopslag of Event Hub en deze correleren met de Azure Sentinel om uw ML-modellen te bouwen. 
 
### <a name="copy-and-install-the-related-packages"></a>De gerelateerde pakketten kopiëren en installeren

Kopieer het BYO-ML-pakket uit de github Azure Sentinel opslagplaats die hierboven wordt vermeld, naar uw Databricks-omgeving. Open vervolgens de notebooks en volg de instructies in het notebook om de vereiste bibliotheken op uw clusters te installeren.

### <a name="model-training-and-scoring"></a>Modeltraining en scoren

Volg de instructies in de twee notebooks om de configuraties te wijzigen op basis van uw eigen omgeving en resources, volg de stappen om uw model te trainen en te bouwen, en plan vervolgens het model om de toegangslogboeken voor binnenkomende bestands share te scoren.

### <a name="write-results-to-log-analytics"></a>Resultaten schrijven naar Log Analytics

Zodra de score is gepland, kunt u de module in het scoring-notebook gebruiken om de scoreresultaten te schrijven naar de Log Analytics-werkruimte die is gekoppeld aan uw Azure Sentinel-instantie.

### <a name="check-results-in-azure-sentinel"></a>Resultaten controleren in Azure Sentinel

Als u uw scoreresultaten samen met gerelateerde logboekgegevens wilt bekijken, gaat u terug naar Azure Sentinel portal. In **Logboeken** > logboeken ziet u de resultaten in de **AnomalousResourceAccessResult_CL** tabel (of de naam van uw eigen aangepaste tabel). U kunt deze resultaten gebruiken om uw onderzoeks- en opsporingservaringen te verbeteren.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="afwijkende logboeken voor resourcetoegang":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>Een aangepaste analyseregel maken met ML-resultaten

Zodra u hebt bevestigd dat de ML-resultaten in de aangepaste logboekentabel staan en u tevreden bent met de betrouwbaarheid van de scores, kunt u een detectie maken op basis van de resultaten. Ga naar **Analytics** vanuit Azure Sentinel portal en [maak een nieuwe detectieregel.](tutorial-detect-threats-custom.md) Hieronder vindt u een voorbeeld van de query die wordt gebruikt om de detectie te maken.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="aangepaste analyseregel maken voor B Y O M L-detecties":::

### <a name="view-and-respond-to-incidents"></a>Incidenten weergeven en hierop reageren
Zodra u de analyseregel hebt ingesteld op basis van de ML-resultaten, wordt er een incident gegenereerd en  weergegeven op de pagina Incidenten op Azure Sentinel. 

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u het BYO-ML-platform van Azure Sentinel kunt gebruiken voor het maken of importeren van uw eigen machine learning-algoritmen om gegevens te analyseren en bedreigingen te detecteren.

- Zie berichten over machine learning en tal van andere relevante onderwerpen in [Azure Sentinel Blog](https://aka.ms/azuresentinelblog).
