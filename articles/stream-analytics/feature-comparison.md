---
title: Vergelijking van Azure Stream Analytics-functies
description: In dit artikel worden de functies vergeleken die worden ondersteund voor Azure Stream Analytics Cloud-en IoT Edge-taken in de Azure Portal, Visual Studio en Visual Studio code.
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 037bd8bc823cd8c77241d0ca25174e29d25149b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020533"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Vergelijking van Azure Stream Analytics-functies

Met Azure Stream Analytics kunt u streaming-oplossingen maken in de Cloud en op het IoT Edge met behulp van [Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)en [Visual Studio code](quick-create-visual-studio-code.md). In de tabellen in dit artikel ziet u welke functies door elk platform worden ondersteund voor beide taak typen.

> [!NOTE]
> De hulpprogramma's Visual Studio en Visual Studio Code bieden geen ondersteuning voor taken in de regio's China - oost, China - noord, Duitsland - centraal en Duitsland - noordoost.

## <a name="cloud-job-features"></a>Cloud taak functies


|Functie  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Kruis platform     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Ontwerpen van scripts     |Ja         |Ja         |Ja         |
|Script IntelliSense     |Syntaxis markering         |Syntaxis markering</br>Code volt ooien</br>Fout markering         |Syntaxis markering</br>Code volt ooien</br>Fout markering         |
|Alle typen invoer, uitvoer en taak configuraties definiëren     |Ja         |Ja         |Ja         |
|Broncodebeheer     |Nee         |Ja         |Ja         |
|Ondersteuning voor CI/CD     |Gedeeltelijk         |Ja         |Ja         |
|Invoer en uitvoer over meerdere query's delen     |Nee         |Ja         |Ja         |
|Query's testen met een voorbeeld bestand     |Ja         |Ja        |Ja         |
|Lokale tests voor dynamische gegevens     |Nee         |Ja       |Ja      |
|Taken weer geven en taak entiteiten bekijken     |Ja         |Ja        |Ja         |
|Een taak exporteren naar een lokaal project     |Nee         |Ja         |Ja         |
|Taken verzenden, starten en stoppen     |Ja         |Ja         |Ja         |
|Metrische gegevens van de taak en het diagram weer geven     |Ja         |Ja         |Openen in portal         |
|Runtime fouten van taken weer geven     |Ja         |Ja         |Nee         |
|Resourcelogboeken     |Ja         |Nee         |Nee         |
|Aangepaste bericht eigenschappen     |Ja         |Ja         |Nee       |
|Aangepaste C#-code functie en deserializer|Alleen-lezen modus|Ja|Nee|
|Java script UDF en UDA     |Ja         |Ja         |Alleen in Windows         |
|Machine Learning Service     |Ja        |Ja         |Nee         |
|Azure Machine Learning Studio (klassiek)|Ja, maar de query kan niet worden getest        |Ja |Nee         |
|Compatibiliteitsniveau     |1.0</br>1.1</br>1,2 (standaard)         |1.0</br>1.1</br>1,2 (standaard)           |1.0</br>1.1</br>1,2 (standaard)           |
|Ingebouwde op ML gebaseerde anomalie detectie functies     |Ja         |Ja         |Ja         |
|Ingebouwde georuimtelijke functies     |Ja         |Ja         |Ja         |



## <a name="iot-edge-job-features"></a>IoT Edge taak functies

|Functie  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Ontwerpen van taken     |Ja         |Ja         |Nee         |
|Broncodebeheer     |Nee         |Ja         |Nee         |
|Een taak exporteren naar een lokaal project     |Nee         |Ja         |Nee         |
|Query's testen met een voorbeeld bestand     |Ja         |Ja         |Nee         |
|Invoer en uitvoer over meerdere query's delen     |Nee         |Ja         |Nee         |
|C#-UDF     |Nee         |Ja         |Nee         |
|Taken verzenden     |Ja         |Ja         |Nee         |
|Taken weer geven en taak entiteiten bekijken     |Ja         |Ja         |Nee         |
|Metrische gegevens van de taak en het diagram weer geven     |Ja         |Gedeeltelijk         |Nee         |
|Runtime fouten van taken weer geven     |Ja         |Gedeeltelijk         |Nee         |
|Ondersteuning voor CI/CD     |Nee         |Nee         |Nee         |


## <a name="next-steps"></a>Volgende stappen

* [Azure Stream Analytics op IoT Edge](stream-analytics-edge.md)
* [Zelf studie: een door de gebruiker gedefinieerde C#-functie schrijven voor Azure Stream Analytics IoT Edge-taak (preview-versie)](stream-analytics-edge-csharp-udf.md)
* [Stream Analytics IoT Edge taken ontwikkelen met behulp van Visual Studio-hulpprogram ma's](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven](stream-analytics-vs-tools.md)
* [Azure Stream Analytics verkennen met Visual Studio code (preview)](visual-studio-code-explore-jobs.md)


