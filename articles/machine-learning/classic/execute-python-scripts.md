---
title: 'ML Studio (klassiek): python-scripts uitvoeren-Azure'
description: Meer informatie over het gebruik van de script module python uitvoeren voor het gebruik van python-code in Machine Learning Studio (klassiek) experimenten en webservices.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: devx-track-python, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 374589212b230e6b3ce0abcadbad8aa3eb7271fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519980"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Python machine learning scripts uitvoeren in Azure Machine Learning Studio (klassiek)

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Python is een waardevol hulp middel in de hulp middelen van veel gegevens wetenschappers. Deze wordt gebruikt in elke fase van typische machine learning werk stromen, inclusief gegevens exploratie, extractie van functies, model training en-implementatie.

In dit artikel wordt beschreven hoe u de script module python uitvoeren kunt gebruiken voor het gebruik van python-code in uw Azure Machine Learning Studio (klassieke) experimenten en Web Services.

## <a name="using-the-execute-python-script-module"></a>De script module python uitvoeren gebruiken

De primaire interface naar python in Studio (klassiek) is via de [script][execute-python-script] module voor het uitvoeren van python. Het accepteert Maxi maal drie invoer en produceert Maxi maal twee uitvoer, vergelijkbaar met de script module voor het [uitvoeren van R][execute-r-script] . Python-code wordt in het vak para meter ingevoerd via een speciaal benoemde ingangs punt functie, genaamd `azureml_main` .

![Python-script module uitvoeren](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Voor beeld van python-code in het vak module parameter](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Invoer parameters

Invoer voor de python-module worden weer gegeven als Panda DataFrames. De `azureml_main` functie accepteert Maxi maal twee optionele Panda DataFrames als para meters.

De toewijzing tussen invoer poorten en functie parameters is positioneel:

- De eerste verbonden invoer poort wordt toegewezen aan de eerste para meter van de functie.
- De tweede invoer (indien verbonden) wordt toegewezen aan de tweede para meter van de functie.
- De derde invoer wordt gebruikt voor het [importeren van aanvullende python-modules](#import-modules).

Meer gedetailleerde semantiek van de manier waarop de invoer poorten worden toegewezen aan para meters van de `azureml_main` functie, worden hieronder weer gegeven.

![Tabel met invoer poort configuraties en resulterende python-hand tekening](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Uitvoer retour waarden

De `azureml_main` functie moet een enkele Panda-data frame retour neren die is verpakt in een python- [reeks](https://docs.python.org/2/c-api/sequence.html) , zoals een tuple-, lijst-of numpy-matrix. Het eerste element van deze reeks wordt geretourneerd naar de eerste uitvoer poort van de module. De tweede uitvoer poort van de module wordt gebruikt voor [Visualisaties](#visualizations) en vereist geen retour waarde. Dit schema wordt hieronder weer gegeven.

![Invoer poorten toewijzen aan para meters en retour waarde naar uitvoer poort](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Omzetting van gegevens typen voor invoer en uitvoer

Studio-gegevens sets zijn niet hetzelfde als Panda DataFrames. Als gevolg hiervan worden invoer gegevens sets in Studio (klassiek) geconverteerd naar Panda data frame en worden uitvoer DataFrames weer geconverteerd naar Studio-gegevens sets (klassiek). Tijdens dit conversie proces worden ook de volgende vertalingen uitgevoerd:

 **Python-gegevens type** | **Studio-Vertaal procedure** |
| --- | --- |
| Teken reeksen en cijfers| Vertaald als is |
| Panda ' N.V.T. ' | Vertaald als ontbrekende waarde |
| Index vectoren | Niet-ondersteunde |
| Niet-teken reeks kolom namen | Aanroepen `str` van kolom namen |
| Dubbele kolom namen | Voeg een numeriek achtervoegsel toe: (1), (2), (3), enzovoort.

**Alle invoer gegevens frames in de python-functie hebben altijd een 64-bits numerieke index van 0 tot het aantal rijen min 1*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>Bestaande python-script modules importeren

De back-end die wordt gebruikt om python uit te voeren, is gebaseerd op [Anaconda](https://www.anaconda.com/distribution/), een veelgebruikte weten schappelijke python-distributie. Het wordt geleverd met dicht bij 200 van de meest voorkomende Python-pakketten die worden gebruikt in gegevens gerichte werk belastingen. Studio (klassiek) biedt momenteel geen ondersteuning voor het gebruik van pakket beheer systemen zoals PIP of Conda voor het installeren en beheren van externe bibliotheken.  Als u meer bibliotheken nodig hebt, moet u het volgende scenario gebruiken als richt lijn.

Een veelvoorkomend gebruik van cases is het opnemen van bestaande python-scripts in Studio-experimenten (klassiek). De [script module python uitvoeren][execute-python-script] accepteert een zip-bestand met python-modules op de derde invoer poort. Het bestand wordt tijdens runtime uitgepakt door het uitvoerings raamwerk en de inhoud wordt toegevoegd aan het bibliotheekpad van de Python-interpreter. Met de `azureml_main` functie toegangs punt kunnen deze modules vervolgens rechtstreeks worden geïmporteerd. 

Bekijk een voor beeld van het bestand Hello.py dat een eenvoudige ' Hello, World '-functie bevat.

![Door de gebruiker gedefinieerde functie in het Hello.py-bestand](./media/execute-python-scripts/figure4.png)

Vervolgens maken we een bestand Hello.zip dat Hello.py bevat:

![Zip-bestand met door de gebruiker gedefinieerde python-code](./media/execute-python-scripts/figure5.png)

Upload het zip-bestand als een gegevensset in Studio (klassiek). Maak vervolgens een experiment dat gebruikmaakt van de python-code in het Hello.zip bestand door deze toe te voegen aan de derde invoer poort van de script module voor het **uitvoeren van python** , zoals weer gegeven in de volgende afbeelding.

![Voorbeeld experiment met Hello.zip als invoer voor een script module voor het uitvoeren van een python](./media/execute-python-scripts/figure6a.png)

![Door de gebruiker gedefinieerde python-code die is geüpload als een zip-bestand](./media/execute-python-scripts/figure6b.png)

In de module-uitvoer ziet u dat het zip-bestand is uitgepakt en dat de functie `print_hello` is uitgevoerd.

![Module-uitvoer met door de gebruiker gedefinieerde functie](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Toegang tot Azure Storage blobs

Met de volgende stappen kunt u toegang krijgen tot gegevens die zijn opgeslagen in een Azure Blob Storage-account:

1. Down load het [Azure Blob Storage-pakket voor python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) lokaal.
1. Upload het zip-bestand naar uw studio-werk ruimte (klassiek) als een gegevensset.
1. Uw BlobService-object maken met `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Schakel **beveiligde overdracht** uit die is vereist op het tabblad instellingen van opslag **configuratie**

![Schakel beveiligde overdracht uit die vereist is voor de Azure Portal](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Inzet python-scripts

Alle [script][execute-python-script] modules voor het uitvoeren van python die worden gebruikt in een score experiment, worden aangeroepen wanneer deze worden gepubliceerd als een webservice. In de onderstaande afbeelding ziet u bijvoorbeeld een score experiment met de code voor het evalueren van één python-expressie.

![Studio-werk ruimte voor een webservice](./media/execute-python-scripts/figure3a.png)

![Python-Panda-expressie](./media/execute-python-scripts/python-script-with-python-pandas.png)

Een webservice die is gemaakt op basis van dit experiment, onderneemt de volgende acties:

1. Een python-expressie maken als invoer (als een teken reeks)
1. De python-expressie verzenden naar de Python-interpreter
1. Retourneert een tabel die zowel de expressie als het geëvalueerde resultaat bevat.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>Werken met visualisaties

Grafieken die zijn gemaakt met MatplotLib kunnen worden geretourneerd door het [script python uitvoeren][execute-python-script]. Grafieken worden echter niet automatisch omgeleid naar afbeeldingen wanneer u R gebruikt. De gebruiker moet dus expliciet elke wille keurige grafiek opslaan in PNG-bestanden.

Als u installatie kopieën van MatplotLib wilt genereren, moet u de volgende stappen uitvoeren:

1. Schakel de back-end over op AGG in de standaard weer gave op basis van qt.
1. Maak een nieuw afbeeldings object.
1. De as ophalen en alle uitstaande grafieken genereren.
1. Sla de afbeelding op in een PNG-bestand.

Dit proces wordt geïllustreerd in de volgende afbeeldingen die een spreidings plot matrix maken met behulp van de functie scatter_matrix in Pandas.

![Code voor het opslaan van MatplotLib-cijfers in afbeeldingen](./media/execute-python-scripts/figure-v1-8.png)

![Klik op visualiseren in de script module voor het uitvoeren van een python om de cijfers weer te geven](./media/execute-python-scripts/figure-v2-9a.png)

![Grafieken visualiseren voor een voor beeld van een experiment met behulp van python-code](./media/execute-python-scripts/figure-v2-9b.png)

Het is mogelijk om meerdere cijfers te retour neren door ze op te slaan in verschillende installatie kopieën. Studio (klassiek) runtime haalt alle installatie kopieën op en voegt deze samen voor visualisatie.

## <a name="advanced-examples"></a>Geavanceerde voor beelden

De Anaconda-omgeving die in Studio (klassiek) is geïnstalleerd, bevat algemene pakketten zoals NumPy, SciPy en scikits-leer. Deze pakketten kunnen effectief worden gebruikt voor de verwerking van gegevens in een machine learning-pijp lijn.

Het volgende experiment en script illustreren bijvoorbeeld het gebruik van ensemble-informatie in Scikits-Learn om urgentie scores van functies te berekenen voor een gegevensset. De scores kunnen worden gebruikt om de functie selectie onder Super visie uit te voeren voordat deze in een ander model wordt ingevoerd.

Hier volgt de python-functie die wordt gebruikt voor het berekenen van de prioriteits scores en het rangschikken van de functies op basis van de scores:

![Functie voor het rangschikken van functies op scores](./media/execute-python-scripts/figure8.png)

Het volgende experiment berekent en retourneert de prioriteits scores van functies in de gegevensset ' Pima Indiase diabetes ' in Azure Machine Learning Studio (klassiek):

![Experimenteer met het rangschikken van functies in de Pima Indiase diabetes-gegevensset met behulp van python](./media/execute-python-scripts/figure9a.png)

![Visualisatie van de uitvoer van de script module voor het uitvoeren van python](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Beperkingen

De [script][execute-python-script] module voor het uitvoeren van python heeft de volgende beperkingen:

### <a name="sandboxed-execution"></a>Sandbox-uitvoering

De python-runtime is momenteel in de sandbox opgeslagen en staat geen toegang tot het netwerk of het lokale bestands systeem op een blijvende manier toe. Alle bestanden die lokaal zijn opgeslagen, worden geïsoleerd en verwijderd zodra de module is voltooid. De python-code heeft geen toegang tot de meeste mappen op de machine waarop deze wordt uitgevoerd. de uitzonde ring is de huidige map en de bijbehorende submappen.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Geen geavanceerde ondersteuning voor ontwikkeling en fout opsporing

De python-module biedt momenteel geen ondersteuning voor IDE-functies, zoals IntelliSense en fout opsporing. Als de module tijdens runtime mislukt, is de volledige python-Stack tracering beschikbaar. Maar dit moet worden weer gegeven in het uitvoer logboek voor de module. Het wordt momenteel aangeraden python-scripts te ontwikkelen en op te sporen in een omgeving zoals IPython en de code vervolgens te importeren in de module.

### <a name="single-data-frame-output"></a>Uitvoer van één gegevens frame

Het python-invoer punt is alleen toegestaan om één gegevens frame als uitvoer te retour neren. Het is momenteel niet mogelijk om wille keurige python-objecten, zoals getrainde modellen, rechtstreeks terug te sturen naar de Studio-Runtime (klassiek). Net als bij het uitvoeren van een [R-script][execute-r-script], dat dezelfde beperking heeft, is het mogelijk dat in veel gevallen objecten worden ingedrukt in een byte matrix en dat deze vervolgens in een gegevens frame worden geretourneerd.

### <a name="inability-to-customize-python-installation"></a>Kan de python-installatie niet aanpassen

Op dit moment is de enige manier om aangepaste python-modules toe te voegen via het zip-bestand dat eerder is beschreven. Hoewel dit haalbaar is voor kleine modules, is het lastig voor grote modules (met name voor modules met systeem eigen Dll's) of een groot aantal modules.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg het [Python Developer Center](https://azure.microsoft.com/develop/python/) voor meer informatie.

<!-- Module References -->
[execute-python-script]: /azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script