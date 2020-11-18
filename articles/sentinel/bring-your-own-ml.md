---
title: Breng uw eigen ML in azure Sentinel | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u uw eigen machine learning-algoritmen maakt en gebruikt voor gegevens analyse in azure Sentinel.
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
ms.openlocfilehash: 6a891a301d5869603a7d90d28bb9063d7d5bdb1d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660573"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>Breng uw eigen Machine Learning (ML) over naar Azure Sentinel

Machine Learning (ML) is een van de belangrijkste basissen van Azure Sentinel en een van de belangrijkste kenmerken die het bevat. Azure Sentinel voorziet in verschillende ervaringen: ingebouwd in de [fusie](fusion.md) correlatie-engine en Jupyter-notebooks, en het pas beschik bare bouwen van uw eigen ml-platform (BYO ml). 

ML detectie modellen kunnen worden aangepast aan afzonderlijke omgevingen en aan wijzigingen in het gedrag van gebruikers, om fout-positieven te verminderen en bedreigingen te identificeren die niet met een traditionele benadering worden gevonden. Veel beveiligings organisaties begrijpen de waarde van ML voor beveiliging, maar niet veel hiervan hebben het luxe mede werkers met expertise in zowel de beveiliging als de ML. We hebben het Framework ontworpen dat hier wordt gepresenteerd voor beveiligings organisaties en-professionals om in hun ML traject mee te groeien. Organisaties die nieuw zijn in MILLILITERs of zonder de vereiste deskundigheid, kunnen aanzienlijke beschermings waarde krijgen ten opzichte van de ingebouwde ML-mogelijkheden van Azure Sentinel.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="machine learning Framework":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>Wat is het gebruik van uw eigen Machine Learning-platform (BYO-ML)?

Voor organisaties met een capaciteit van meer dan het bouwen van aangepaste ML-modellen voor hun unieke bedrijfs behoeften bieden we het **BYO-ml platform**. Het platform maakt gebruik van de [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) / [Apache Spark](http://spark.apache.org/) omgeving en Jupyter-notebooks voor het produceren van de ml-omgeving. Deze bevat de volgende onderdelen:

- een BYO-ML-pakket, dat bibliotheken bevat die u helpen bij het openen van gegevens en het terugbrengen van de resultaten naar Log Analytics (LA), zodat u de resultaten kunt integreren met uw detectie, onderzoek en jacht. 

- ML-algoritme sjablonen die u kunt aanpassen om specifieke beveiligings problemen in uw organisatie aan te passen. 

- voor beelden van notebooks om het model te trainen en het model score te plannen. 

Daarnaast kunt u uw eigen ML-modellen en/of uw eigen Spark-omgeving meenemen om te integreren met Azure Sentinel.

Met het BYO-ML platform kunt u aan de slag gaan met het bouwen van uw eigen ML-modellen: 

- Het notitie blok met voorbeeld gegevens helpt u om aan de slag te gaan met een end-to-end-ervaring, zonder dat u zich zorgen hoeft te maken over het verwerken van productie gegevens.

- Het pakket dat is geïntegreerd met de Spark-omgeving, vermindert de uitdagingen en wrijvingen bij het beheer van de infra structuur.

- De bibliotheken ondersteunen gegevens verplaatsingen. Training-en Score notitieblokken demonstreren de end-to-end-ervaring en fungeren als een sjabloon die u kunt aanpassen aan uw omgeving.

### <a name="use-cases"></a>Gebruiksvoorbeelden
 
Het BYO-ML platform en pakket nemen aanzienlijk minder tijd en moeite bij het bouwen van uw eigen ML-detecties en de mogelijkheid om specifieke beveiligings problemen in azure Sentinel te verhelpen. Het platform ondersteunt de volgende use-cases:

**Train een ml-algoritme voor het verkrijgen van een aangepast model:** U kunt een bestaande ML-algoritme (gedeeld door micro soft of door de gebruikers community) nemen en deze eenvoudig trainen op uw eigen gegevens om een aangepast ML-model te krijgen dat beter aansluit bij uw gegevens en omgeving.

**Een ml-algoritme sjabloon wijzigen om het aangepaste model op te halen:** U kunt een sjabloon voor ML-algoritme (gedeeld door micro soft of door de gebruikers community) wijzigen en het gewijzigde algoritme op uw eigen gegevens trainen om een aangepast model af te stemmen op uw specifieke probleem.

**Maak uw eigen model:** Maak zelf een nieuw model met behulp van het BYO-ML-platform en-hulpprogram ma's van Azure Sentinel.

**Uw Databricks/Spark-omgeving integreren:** Integreer uw bestaande Databricks/Spark-omgeving in azure Sentinel en gebruik BYO-ML-bibliotheken en-sjablonen om ML-modellen te bouwen voor hun unieke situaties.

**Importeer uw eigen ml-model:** U kunt uw eigen ML-modellen importeren en het BYO-ML platform en de hulpprogram ma's gebruiken om ze te integreren met Azure Sentinel.

**Een ml-algoritme delen:** Deel een ML-algoritme voor de community om aan te nemen en aan te passen.

**Gebruik ml om SecOps te stimuleren:** gebruik uw eigen aangepaste ml-model en de resultaten voor jacht, detecties, onderzoek en reacties.

In dit artikel wordt beschreven hoe u de onderdelen van het platform BYO-ML kunt gebruiken en hoe u het platform en het algoritme voor afwijkend bronnen toegang gebruikt om een aangepaste ML detectie met Azure Sentinel te leveren.

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark-omgeving

[Apache Spark™](http://spark.apache.org/) een stap vooruit in het vereenvoudigen van Big data door een uniform Framework te bieden voor het bouwen van gegevens pijplijnen. Azure Databricks gaat dit verder door een Cloud platform voor Zero-beheer te bieden dat is gebouwd rond Spark. We raden u aan Databricks te gebruiken voor uw BYO-ML platform, zodat u zich kunt richten op het vinden van antwoorden die een onmiddellijke invloed op uw bedrijf hebben, in plaats van de gegevens pijplijnen en platform problemen te aanpakken.
Als u al Databricks of een andere Spark-omgeving hebt en u de bestaande instellingen liever wilt gebruiken, zal het pakket met de BYO-ML ook worden gebruikt. 

## <a name="byo-ml-package"></a>BYO-ML-pakket

Het BYO ML-pakket bevat de aanbevolen procedures en onderzoek van micro soft aan de front-end van de ML voor beveiliging. In dit pakket bieden we de volgende lijst met hulpprogram ma's, notitie blokken en algoritme sjablonen voor beveiligings problemen.

| Bestandsnaam | Beschrijving |
| --------- | ----------- |
| azure_sentinel_utilities. WHL | Bevat hulpprogram ma's voor het lezen van blobs van Azure en het schrijven naar Log Analytics. |
| AnomalousRASampleData | Notebook toont het gebruik van afwijkend bron toegangs model in Sentinel met gegenereerde training en test voorbeeld gegevens. |
| AnomalousRATraining. ipynb | Notebook om het algoritme te trainen, de modellen te bouwen en op te slaan. |
| AnomalousRAScoring. ipynb | Notitie blok om het model te plannen dat moet worden uitgevoerd, visualiseer het resultaat en schrijf de Score terug naar Azure Sentinel. |
|

De eerste ML-algoritme sjabloon die wij bieden, is voor [afwijkende detectie van bron toegang](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML). Het is gebaseerd op een filter algoritme voor samen werking en is getraind met toegangs logboeken van de Windows-bestands share (beveiligings gebeurtenissen met gebeurtenis-ID 5140). De belangrijkste informatie die u nodig hebt voor dit model in het logboek is het koppelen van gebruikers en bronnen waartoe toegang wordt verkregen. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Voor beeld van een walkthrough: afwijkende detectie van toegang tot bestands share 

Nu u bekend bent met de belangrijkste onderdelen van het BYO-ML platform, is hier een voor beeld om te laten zien hoe u het platform en de onderdelen kunt gebruiken om een aangepaste ML detectie te leveren.

### <a name="setup-the-databricksspark-environment"></a>De Databricks/Spark-omgeving instellen

Als u er nog geen hebt, moet u uw eigen Databricks-omgeving instellen. Raadpleeg het [Databricks Snelstartgids](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal) -document voor instructies.

### <a name="auto-export-instruction"></a>Instructie voor automatisch exporteren

Als u aangepaste ML-modellen wilt maken op basis van uw eigen gegevens in Sentinel, moet u uw gegevens exporteren van Log Analytics naar een Blob Storage-of event hub-bron, zodat het ML-model er toegang toe heeft vanuit Databricks. Meer informatie over het [opnemen van gegevens in azure Sentinel](connect-data-sources.md).

Voor dit voor beeld moet u uw trainings gegevens voor het logboek bestands share toegang hebben in de Azure Blob-opslag. De indeling van de gegevens wordt gedocumenteerd in het notitie blok en de bibliotheken.

U kunt uw gegevens automatisch exporteren vanuit Log Analytics met behulp van de [Azure-opdracht regel interface (CLI)](/cli/azure/monitor/log-analytics). 

U moet de rol **Inzender** in uw log Analytics-werk ruimte, uw opslag account en uw EventHub-bron toewijzen om de opdrachten uit te voeren. 

Hier volgt een voor beeld van een set opdrachten voor het instellen van automatisch exporteren:

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

Voor aangepaste gegevens die niet worden ondersteund door Log Analytics automatisch exporteren, kunt u logische apps of andere oplossingen gebruiken om uw gegevens te verplaatsen. U kunt verwijzen naar het [exporteren van log Analytics gegevens naar](https://www.borninthecloud.com/exporting-log-analytics-data-to-blob-store/?preview=true) het blog van de BLOB Store en het script.

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Correleren met gegevens buiten Azure Sentinel

U kunt ook gegevens van buiten Azure-Sentinel naar de Blob-opslag of event hub halen en deze correleren met de verklikker gegevens om uw ML-modellen te bouwen. 
 
### <a name="copy-and-install-the-related-packages"></a>De gerelateerde pakketten kopiëren en installeren

Kopieer het BYO-ML-pakket van de Azure Sentinel GitHub-opslag plaats die hierboven wordt vermeld, naar uw Databricks-omgeving. Open vervolgens de notitie blokken en volg de instructies in het notitie blok om de vereiste bibliotheken in uw clusters te installeren.

### <a name="model-training-and-scoring"></a>Model training en Score

Volg de instructies in de twee notitie blokken om de configuraties te wijzigen op basis van uw eigen omgeving en resources, volg de stappen om uw model te trainen en te bouwen en plan vervolgens het model om de binnenkomende logboeken voor bestands share toegang te geven.

### <a name="write-results-to-log-analytics"></a>Resultaten naar Log Analytics schrijven

Zodra u het Score item hebt gepland, kunt u de module in het Score notitieblok gebruiken om de Score resultaten te schrijven naar de Log Analytics werk ruimte die aan uw Azure Sentinel-exemplaar is gekoppeld.

### <a name="check-results-in-azure-sentinel"></a>Resultaten controleren in azure Sentinel

Ga terug naar uw Azure-Sentinel-Portal om uw gescoorde resultaten te bekijken met gerelateerde logboek gegevens. In **logboeken** > aangepaste logboeken ziet u de resultaten in de **AnomalousResourceAccessResult_CL** tabel (of uw eigen aangepaste tabel naam). U kunt deze resultaten gebruiken om uw onderzoek en jacht te verbeteren.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="afwijkende bron toegangs logboeken":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>Aangepaste analyse regel bouwen met ML-resultaten

Zodra u de resultaten van de ML hebt bevestigd in de tabel met aangepaste logboeken en u tevreden bent met de betrouw baarheid van de scores, kunt u een detectie maken op basis van de resultaten. Ga naar **Analytics** vanuit de Azure-Sentinel-Portal en [Maak een nieuwe detectie regel](tutorial-detect-threats-custom.md). Hieronder ziet u een voor beeld waarin de query wordt gebruikt om de detectie te maken.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="aangepaste analyse regel maken voor B-O M L detecties":::

### <a name="view-and-respond-to-incidents"></a>Incidenten weer geven en erop reageren
Zodra u de analyse regel hebt ingesteld op basis van de MILLILITERs resultaten, wordt er een incident gegenereerd en weer gegeven op de pagina **incidenten** in azure Sentinel als er resultaten boven de drempel waarde vallen die u hebt ingesteld in de query. 

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u het BYO-ML-platform van Azure Sentinel gebruikt voor het maken of importeren van uw eigen machine learning algoritmen voor het analyseren van gegevens en het detecteren van bedreigingen.

- Bekijk berichten over machine learning en veel andere relevante onderwerpen in de [Azure Sentinel-blog](https://aka.ms/azuresentinelblog).