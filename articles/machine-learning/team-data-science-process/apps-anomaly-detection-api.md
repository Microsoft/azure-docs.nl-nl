---
title: 'Azure Machine Learning anomalie detectie-API: team data Science process'
description: De anomalie detectie-API is een voor beeld dat is gebouwd met Microsoft Azure Machine Learning die afwijkingen in de tijdreeks gegevens detecteert met numerieke waarden die in de loop van de tijd gelijkmatig zijn verdeeld.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: e210c1683d5f14181bc0549e73a892eb91d2e746
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93305692"
---
# <a name="machine-learning-anomaly-detection-api"></a>Machine Learning anomalie detectie-API

> [!NOTE]
> Dit item is onderhouds werkzaamheden. We raden u aan de [afwijkende API-service](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) te gebruiken die wordt ondersteund door een galerie met machine learning-algoritmen onder Azure Cognitive Services om afwijkingen van bedrijfs-, operationele en IOT-gegevens te detecteren.

## <a name="overview"></a>Overzicht
De [anomalie detectie-API](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2) is een voor beeld dat is gebouwd met Azure machine learning die afwijkingen in de tijdreeks gegevens detecteert met numerieke waarden die in de loop van de tijd gelijkmatig zijn verdeeld.

Met deze API kunnen de volgende typen afwijkende patronen worden gedetecteerd in time series-gegevens:

* **Positieve en negatieve trends**: als u bijvoorbeeld het geheugen gebruik bewaken in het berekenen van een opwaartse trend, kan dit van belang zijn, omdat dit mogelijk indicatief is voor een geheugenlek,
* **Wijzigingen in het dynamische waarden bereik**: als u de uitzonde ringen die door een Cloud service worden gegenereerd, bijvoorbeeld bewaken, kunnen eventuele wijzigingen in het dynamische waardebereik duiden op instabiliteit in de status van de service en
* **Pieken en spannings dips**: als u bijvoorbeeld het aantal mislukte aanmeldingen in een service of het aantal afhandelingen in een e-commerce site bewaken, kunnen pieken of spannings dips duiden op abnormaal gedrag.

Deze machine learning detectoren volgen dergelijke wijzigingen in waarden gedurende een bepaalde periode en melden de lopende wijzigingen in hun waarden als afwijkende scores. Deze vereisen geen ad hoc-drempel waarde en hun scores kunnen worden gebruikt voor het beheren van het onjuiste positieve aantal. De anomalie detectie-API is handig in verschillende scenario's, zoals service bewaking, door Kpi's te volgen over een bepaalde periode, de gebruiks bewaking via metrische gegevens, zoals het aantal Zoek opdrachten, aantallen klikken, prestatie bewaking via prestatie meter items, zoals geheugen, CPU, lees bewerkingen van bestanden, enzovoort.

De afwijkings detectie aanbieding wordt geleverd met handige hulp middelen om aan de slag te gaan.

* De [Webtoepassing](https://anomalydetection-aml.azurewebsites.net/) helpt u bij het evalueren en visualiseren van de resultaten van anomalie detectie-api's voor uw gegevens.

> [!NOTE]
> Probeer het met [deze API](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2) **afwijkende Insights-oplossing**
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>API-implementatie
Als u de API wilt gebruiken, moet u deze implementeren in uw Azure-abonnement waar deze wordt gehost als een Azure Machine Learning-webservice.  U kunt dit doen vanuit de [Azure AI Gallery](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2).  Hiermee worden twee Azure Machine Learning Studio (Classic) webservices (en hun gerelateerde resources) geïmplementeerd voor uw Azure-abonnement, een voor detectie van anomalieën met seizoensgebonden detectie en één zonder detectie van de seizoensgebondenheid.  Zodra de implementatie is voltooid, kunt u uw Api's beheren via de pagina met [webservices van Azure machine learning Studio (klassiek)](https://services.azureml.net/webservices/) .  Op deze pagina kunt u de eindpunt locaties en API-sleutels vinden, evenals voorbeeld code voor het aanroepen van de API.  Meer gedetailleerde instructies zijn [hier](../classic/manage-new-webservice.md)beschikbaar.

## <a name="scaling-the-api"></a>De API schalen
Standaard heeft uw implementatie een gratis facturerings plan voor ontwikkelen en testen met 1.000 trans acties/maand en 2 reken uren/maand.  U kunt een upgrade uitvoeren naar een ander abonnement volgens uw behoeften.  Meer informatie over de prijzen van verschillende abonnementen vindt u [hier](https://azure.microsoft.com/pricing/details/machine-learning/) onder ' productie Web API-prijzen '.

## <a name="managing-aml-plans"></a>AML-abonnementen beheren
U kunt uw facturerings plan [hier](https://services.azureml.net/plans/)beheren.  De naam van het abonnement is gebaseerd op de naam van de resource groep die u hebt gekozen bij het implementeren van de API, plus een teken reeks die uniek is voor uw abonnement.  Instructies voor het uitvoeren van een upgrade van uw [abonnement zijn te vinden in de](../classic/manage-new-webservice.md) sectie ' Betaal plannen beheren '.

## <a name="api-definition"></a>API-definitie
De webservice biedt een op REST gebaseerde API via HTTPS die op verschillende manieren kan worden gebruikt, zoals een web-of mobiele toepassing, R, Python, Excel, enzovoort.  U verzendt uw time series-gegevens naar deze service via een REST API-aanroep en voert een combi natie van de drie typen afwijkingen uit die hieronder worden beschreven.

## <a name="calling-the-api"></a>De API aanroepen
Als u de API wilt aanroepen, moet u weten wat de locatie van het eind punt en de API-sleutel zijn.  Deze twee vereisten, samen met voorbeeld code voor het aanroepen van de API, zijn beschikbaar op de pagina [Azure machine learning Studio (Classic) Web Services](https://services.azureml.net/webservices/) .  Ga naar de gewenste API en klik vervolgens op het tabblad verbruik om ze te zoeken.  U kunt de API aanroepen als een Swagger API (dat wil zeggen, met de URL-para meter `format=swagger` ) of als een niet-Swagger-API (dat wil zeggen, zonder de `format` URL-para meter).  De voorbeeld code maakt gebruik van de Swagger-indeling.  Hieronder vindt u een voorbeeld aanvraag en-antwoord in de niet-Swagger-indeling.  Deze voor beelden zijn het eind punt van de seizoensgebondenheid.  Het eind punt van de niet-seizoensgebondenheid is vergelijkbaar.

### <a name="sample-request-body"></a>Voorbeeld aanvraag tekst
De aanvraag bevat twee objecten: `Inputs` en `GlobalParameters` .  In het onderstaande voor beeld wordt een aantal para meters expliciet verzonden en andere niet (Schuif omlaag voor een volledige lijst met para meters voor elk eind punt).  In para meters die niet expliciet in de aanvraag worden verzonden, worden de volgende standaard waarden gebruikt.

```json
{
            "Inputs": {
                    "input1": {
                            "ColumnNames": ["Time", "Data"],
                            "Values": [
                                    ["5/30/2010 18:07:00", "1"],
                                    ["5/30/2010 18:08:00", "1.4"],
                                    ["5/30/2010 18:09:00", "1.1"]
                            ]
                    }
            },
    "GlobalParameters": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "bileveldetector.sensitivity": "3.25",
        "detectors.spikesdips": "Both"
    }
}
```

### <a name="sample-response"></a>Voorbeeldreactie
Als u het veld wilt zien `ColumnNames` , moet u `details=true` als een URL-para meter in uw aanvraag toevoegen.  Zie de onderstaande tabellen voor de betekenis achter elk van deze velden.

```json
{
    "Results": {
        "output1": {
            "type": "table",
            "value": {
                "Values": [
                    ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                    ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                    ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                ],
                "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
            }
        }
    }
}
```


## <a name="score-api"></a>Score-API
De Score-API wordt gebruikt voor het uitvoeren van een afwijkings detectie voor gegevens in een tijd reeks die geen seizoen seizoensgebonden zijn. De API voert een aantal anomalie detectoren uit op de gegevens en retourneert hun anomalie cijfers.
In de afbeelding hieronder ziet u een voor beeld van afwijkingen die de Score-API kan detecteren. Deze tijd reeks heeft twee verschillende niveau wijzigingen en drie pieken. De rode stippen geven de tijd weer waarop de niveau wijziging wordt gedetecteerd, terwijl de zwarte stippen de gedetecteerde pieken weer geven.
![Score-API][1]

### <a name="detectors"></a>Detectoren
De anomalie detectie-API ondersteunt detectoren in drie algemene categorieën. Meer informatie over specifieke invoer parameters en uitvoer voor elke detector vindt u in de volgende tabel.

| Detector categorie | Detectie | Description | Invoerparameters | Uitvoerwaarden |
| --- | --- | --- | --- | --- |
| Piek detectoren |TSpike detector |Spoor pieken en spannings dips op basis van de waarden uit het eerste en derde kwartiel |*tspikedetector. sensitivity:* Hiermee wordt een geheel getal in het bereik 1-10, standaard waarde: 3, gebruikt. Bij hogere waarden worden er meer extreme waarden onderschept waardoor deze minder gevoelig zijn |TSpike: binaire waarden – ' 1 ' als een Prikker/DIP wordt gedetecteerd, ' 0 ' anders |
| Piek detectoren | ZSpike detector |Pieken en spannings dips detecteren op basis van de betekenis van de data Points |*zspikedetector. sensitivity:* waarde voor geheel getal in het bereik 1-10 opgeven, standaard: 3; Bij hogere waarden worden er meer extreme waarden onderschept waardoor deze minder gevoelig zijn |ZSpike: binaire waarden – ' 1 ' als een Prikker/DIP wordt gedetecteerd, ' 0 ' anders |
| Trage trend detector |Trage trend detector |Langzame positieve trend detecteren conform de ingestelde gevoeligheid |*trenddetector. sensitivity:* drempel op de detector Score (standaard: 3,25, 3,25 – 5 is een redelijk bereik waaruit u kunt kiezen. Hoe hoger de minder gevoelige) |tscore: een zwevend getal dat de afwijkings score voor trend weergeeft |
| Detecties van niveau wijzigingen | Wijzigings detectie op bidirectionele niveau |De wijziging van zowel opwaarts-als lagere niveaus detecteren volgens de ingestelde gevoeligheid |*bileveldetector. sensitivity:* drempel op de detector Score (standaard: 3,25, 3,25 – 5 is een redelijk bereik waaruit u kunt kiezen. Hoe hoger de minder gevoelige) |rpscore: een zwevend getal dat een afwijkings Score weergeeft op een opwaartse of lagere niveau wijziging |

### <a name="parameters"></a>Parameters
Meer gedetailleerde informatie over deze invoer parameters vindt u in de volgende tabel:

| Invoerparameters | Description | Standaard instelling | Type | Geldig bereik | Voorgesteld bereik |
| --- | --- | --- | --- | --- | --- |
| detectors. historywindow |Geschiedenis (in aantal gegevens punten) die wordt gebruikt voor de berekening van de afwijkings Score |500 |geheel getal |10-2000 |Time-Series afhankelijk |
| detectors. spikesdips | Hiermee wordt aangegeven of alleen pieken, alleen spannings dips of beide moeten worden gedetecteerd |Beide |geïnventariseerd |Beide, pieken, spannings dips |Beide |
| bileveldetector. sensitivity |Gevoeligheid voor het op bidirectionele niveau wijzigen van de detectie. |3.25 |double |Geen |3,25-5 (lagere waarden betekenen meer gevoelig) |
| trenddetector. sensitivity |Gevoeligheid voor positieve trend detector. |3.25 |double |Geen |3,25-5 (lagere waarden betekenen meer gevoelig) |
| tspikedetector. sensitivity |Gevoeligheid voor TSpike detector |3 |geheel getal |1-10 |3-5 (lagere waarden betekenen meer gevoelige) |
| zspikedetector. sensitivity |Gevoeligheid voor ZSpike detector |3 |geheel getal |1-10 |3-5 (lagere waarden betekenen meer gevoelige) |
| postprocess.tailRows |Aantal van de meest recente gegevens punten dat in de uitvoer resultaten moet worden bewaard |0 |geheel getal |0 (alle gegevens punten blijven) of geef het aantal punten op dat in de resultaten moet worden bewaard |N.v.t. |

### <a name="output"></a>Uitvoer
De API voert alle detectoren uit op uw tijdreeks gegevens en retourneert afwijkende scores en binaire piek indicatoren voor elk tijdstip. De volgende tabel bevat de uitvoer van de API.

| Uitvoerwaarden | Description |
| --- | --- |
| Tijd |Tijds tempels van onbewerkte gegevens, of geaggregeerd (en/of) toegerekende gegevens als aggregatie (en/of) ontbrekende gegevens toerekening is toegepast |
| Gegevens |Waarden van onbewerkte gegevens, of geaggregeerd (en/of) toegerekende gegevens als aggregatie (en/of) ontbrekende gegevens toerekening is toegepast |
| TSpike |Binaire indicator die aangeeft of een Prikker door TSpike detector wordt gedetecteerd |
| ZSpike |Binaire indicator die aangeeft of een Prikker door ZSpike detector wordt gedetecteerd |
| rpscore |Een drijvende-komma waarde voor een afwijkings Score op bidirectionele niveau wijzigen |
| rpalert |1/0 waarde die aangeeft dat er een onregelmatigheid op bidirectionele niveau is op basis van de invoer gevoeligheid |
| tscore |Een zwevend getal dat de afwijkende score voor positieve trend aangeeft |
| talert |1/0 waarde die aangeeft dat er een positieve trend afwijkt op basis van de invoer gevoeligheid |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality-API
De ScoreWithSeasonality-API wordt gebruikt voor het uitvoeren van anomalie detectie op tijd reeksen die seizoensgebonden patronen hebben. Deze API is handig voor het detecteren van afwijkingen in seizoen patronen.
In de volgende afbeelding ziet u een voor beeld van afwijkingen die zijn gedetecteerd in een seizoen tijd reeks. De tijd reeks heeft één Prikker (de eerste zwarte stip), twee spannings dips (de tweede zwarte stip en één aan het einde) en één niveau wijziging (rode punt). Zowel het dip in het midden van de tijd reeks als de niveau wijziging kan alleen worden onderscheiden nadat seizoen onderdelen uit de serie zijn verwijderd.
![Seizoensgebonden API][2]

### <a name="detectors"></a>Detectoren
De detectoren in het eind punt van de seizoensgebondenheid zijn vergelijkbaar met die in het eind punt van de niet-seizoensgebondenie, maar met iets andere parameter namen (zie hieronder).

### <a name="parameters"></a>Parameters

Meer gedetailleerde informatie over deze invoer parameters vindt u in de volgende tabel:

| Invoerparameters | Description | Standaard instelling | Type | Geldig bereik | Voorgesteld bereik |
| --- | --- | --- | --- | --- | --- |
| preproces. aggregationInterval |Aggregatie interval in seconden voor het samen voegen van de invoer tijd reeks |0 (er is geen aggregatie uitgevoerd) |geheel getal |0: aggregatie overs Laan, > 0 anders |5 minuten tot 1 dag, afhankelijk van de tijd reeks |
| preproces. aggregationFunc |De functie die wordt gebruikt voor het samen voegen van gegevens in de opgegeven AggregationInterval |gemiddeld |geïnventariseerd |gemiddelde, Sum, length |N.v.t. |
| preproces. replaceMissing |Waarden die worden gebruikt voor het toezeggen van ontbrekende gegevens |LKV (laatste bekende waarde) |geïnventariseerd |nul, LKV, gemiddelde |N.v.t. |
| detectors. historywindow |Geschiedenis (in aantal gegevens punten) die wordt gebruikt voor de berekening van de afwijkings Score |500 |geheel getal |10-2000 |Time-Series afhankelijk |
| detectors. spikesdips | Hiermee wordt aangegeven of alleen pieken, alleen spannings dips of beide moeten worden gedetecteerd |Beide |geïnventariseerd |Beide, pieken, spannings dips |Beide |
| bileveldetector. sensitivity |Gevoeligheid voor het op bidirectionele niveau wijzigen van de detectie. |3.25 |double |Geen |3,25-5 (lagere waarden betekenen meer gevoelig) |
| postrenddetector. sensitivity |Gevoeligheid voor positieve trend detector. |3.25 |double |Geen |3,25-5 (lagere waarden betekenen meer gevoelig) |
| negtrenddetector. sensitivity |Gevoeligheid voor negatieve trend detector. |3.25 |double |Geen |3,25-5 (lagere waarden betekenen meer gevoelig) |
| tspikedetector. sensitivity |Gevoeligheid voor TSpike detector |3 |geheel getal |1-10 |3-5 (lagere waarden betekenen meer gevoelige) |
| zspikedetector. sensitivity |Gevoeligheid voor ZSpike detector |3 |geheel getal |1-10 |3-5 (lagere waarden betekenen meer gevoelige) |
| seizoensgebonden. inschakelen |Of de beoordeling van de seizoensgebondenheid moet worden uitgevoerd |true |booleaans |de waarde True, false |Time-Series afhankelijk |
| seizoensgebonden. numSeasonality |Maximum aantal periodieke cycli dat moet worden gedetecteerd |1 |geheel getal |1, 2 |1-2 |
| seizoensgebonden. transform |Of seizoen (en) trend onderdelen moeten worden verwijderd voordat anomalie detectie wordt toegepast |seizoengezuiverd |geïnventariseerd |geen, onseizoen, deseasontrend |N.v.t. |
| postprocess.tailRows |Aantal van de meest recente gegevens punten dat in de uitvoer resultaten moet worden bewaard |0 |geheel getal |0 (alle gegevens punten blijven) of geef het aantal punten op dat in de resultaten moet worden bewaard |N.v.t. |

### <a name="output"></a>Uitvoer
De API voert alle detectoren uit op uw tijdreeks gegevens en retourneert afwijkende scores en binaire piek indicatoren voor elk tijdstip. De volgende tabel bevat de uitvoer van de API.

| Uitvoerwaarden | Description |
| --- | --- |
| Tijd |Tijds tempels van onbewerkte gegevens, of geaggregeerd (en/of) toegerekende gegevens als aggregatie (en/of) ontbrekende gegevens toerekening is toegepast |
| OriginalData |Waarden van onbewerkte gegevens, of geaggregeerd (en/of) toegerekende gegevens als aggregatie (en/of) ontbrekende gegevens toerekening is toegepast |
| ProcessedData |Een van de volgende opties: <ul><li>Seizoen gezuiverde tijd reeks als er aanzienlijke seizoensgebondenheid is gedetecteerd en de optie voor het selecteren van een seizoen is geselecteerd;</li><li>seizoen gezuiverde en getrendde tijd reeks als er aanzienlijke seizoensgebondenheid is gedetecteerd en de optie deseasontrend geselecteerd</li><li>anders is deze optie hetzelfde als OriginalData</li> |
| TSpike |Binaire indicator die aangeeft of een Prikker door TSpike detector wordt gedetecteerd |
| ZSpike |Binaire indicator die aangeeft of een Prikker door ZSpike detector wordt gedetecteerd |
| BiLevelChangeScore |Een drijvende-komma waarde voor een afwijkings Score op niveau wijziging |
| BiLevelChangeAlert |1/0 waarde die aangeeft dat er een niveau wijziging afwijkt op basis van de invoer gevoeligheid |
| PosTrendScore |Een zwevend getal dat de afwijkende score voor positieve trend aangeeft |
| PosTrendAlert |1/0 waarde die aangeeft dat er een positieve trend afwijkt op basis van de invoer gevoeligheid |
| NegTrendScore |Een drijvende-komma waarde voor een afwijkende score voor negatieve trend |
| NegTrendAlert |1/0 waarde die aangeeft dat er een negatieve trend afwijkt op basis van de invoer gevoeligheid |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png