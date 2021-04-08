---
title: 'Converteren naar CSV: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module converteren naar CSV in Azure Machine Learning Designer om een gegevensset te converteren naar een CSV-bestand dat later opnieuw kan worden gebruikt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: cc58689e30e9b03e490c0871f3decd50372371fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421920"
---
# <a name="convert-to-csv-module"></a>Converteren naar CSV-module

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om een gegevensset te converteren naar een CSV-indeling die kan worden gedownload, geëxporteerd of worden gedeeld met R-of python-script modules.

### <a name="more-about-the-csv-format"></a>Meer informatie over de CSV-indeling 

De CSV-indeling, die staat voor ' door komma's gescheiden waarden ', is een bestands indeling die wordt gebruikt door veel externe machine learning-hulpprogram ma's. CSV is een gemeen schappelijke Interchange-indeling bij het werken met open-source talen, zoals R of python.

Zelfs als u het meren deel van uw werk in Azure Machine Learning hebt, zijn er situaties waarin het handig kan zijn om uw gegevensset om te zetten in CSV om te gebruiken in externe hulpprogram ma's. Bijvoorbeeld:

+ Down load het CSV-bestand om het te openen met Excel of importeer het in een relationele data base.  
+ Sla het CSV-bestand op in de Cloud opslag en maak er verbinding mee vanuit Power BI om visualisaties te maken.  
+ Gebruik de CSV-indeling om gegevens voor te bereiden voor gebruik in R en python. 

Wanneer u een gegevensset converteert naar CSV, wordt het CSV-bestand opgeslagen in uw Azure ML-werk ruimte. U kunt een Azure-opslag programma gebruiken om het bestand rechtstreeks te openen en te gebruiken. U kunt ook het CSV-bestand in de ontwerp functie openen door de module **converteren naar CSV** te selecteren en vervolgens het histogram pictogram onder het tabblad **uitvoer** in het rechterdeel venster te selecteren om de uitvoer weer te geven. U kunt het CSV-bestand downloaden van de map met resultaten naar een lokale map.  

## <a name="how-to-configure-convert-to-csv"></a>Conversie naar CSV configureren


1.  Voeg de module Convert to CSV toe aan de pijp lijn. U kunt deze module vinden in de **gegevens transformatie** groep in de ontwerp functie. 

2. Verbind het met een wille keurige module die een gegevensset uitvoert.   
  
3.  Verzend de pijp lijn.

### <a name="results"></a>Resultaten
  

Selecteer het tabblad **uitvoer** in het rechterdeel venster van **converteren naar CSV** en selecteer een van deze pictogrammen onder de **poort uitvoer**.  

+ **Gegevensset registreren**: Selecteer het pictogram en sla het CSV-bestand weer op in de Azure ml-werk ruimte als afzonderlijke gegevensset. U kunt de gegevensset vinden als een module in de module structuur onder de sectie **mijn gegevens sets** .

 + **Uitvoer weer geven**: Selecteer het oogpictogram en volg de instructies om door de **Results_dataset** map te bladeren en het data.csv bestand te downloaden.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 