---
title: .NET gebruiken voor Apache Spark
description: Meer informatie over het gebruik van .NET en Apache Spark voor batchverwerking, realtime streaming, machine learning en het schrijven van ad-hocquery's in Azure Synapse Analytics notebooks.
author: luisquintanilla
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: luquinta
ms.reviewer: jrasnick
ms.openlocfilehash: 8d045c1ec96bb7b31a710a28e30e3d428922b65e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378547"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>.NET voor Apache Spark gebruiken met Azure Synapse Analytics

[.NET voor Apache Spark](https://dot.net/spark) biedt [gratis, opensource-](https://github.com/dotnet/spark)en platformoverschrijdende .NET-ondersteuning voor Spark. 

Het biedt .NET-bindingen voor Spark, waarmee u toegang hebt tot Spark-API's via C# en F#. Met .NET voor Apache Spark kunt u ook door de gebruiker gedefinieerde functies schrijven en uitvoeren voor Spark geschreven in .NET. Met de .NET-API's voor Spark hebt u toegang tot alle aspecten van Spark DataFrames waarmee u uw gegevens kunt analyseren, waaronder Spark SQL, Delta Lake en Structured Streaming.

U kunt gegevens analyseren met .NET voor Apache Spark batch-taakdefinities van Spark of met interactieve Azure Synapse Analytics notebooks. In dit artikel leert u hoe u .NET kunt gebruiken voor Apache Spark met Azure Synapse beide technieken.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Batchtaken verzenden met behulp van de Spark-taakdefinitie

Ga naar de zelfstudie voor informatie over het gebruik van Azure Synapse Analytics om Apache Spark voor [Synapse Spark-pools te maken.](apache-spark-job-definitions.md) Als u uw app niet hebt verpakt om te verzenden naar Azure Synapse, moet u de volgende stappen voltooien.

1. Voer de volgende opdrachten uit om uw app te publiceren. Zorg ervoor dat u *mySparkApp vervangt* door het pad naar uw app.
   
   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.18.04-x64
   ```

2. Zip de inhoud van de publicatiemap, bijvoorbeeld die is gemaakt als `publish.zip` gevolg van stap 1. Alle assemblies moeten zich in de eerste laag van het ZIP-bestand en er mag geen tussenliggende maplaag zijn. Dit betekent dat wanneer u uitpakken `publish.zip` , alle assemblies worden geëxtraheerd in uw huidige map.

    **In Windows:**

    Gebruik een extractieprogramma, [zoals 7-Zip](https://www.7-zip.org/) of [WinZip,](https://www.winzip.com/)om het bestand uit te extraheren in de bin-map met alle gepubliceerde binaire bestanden.

    **In Linux:**

    Open een bash-shell en cd in de bin-map met alle gepubliceerde binaire bestanden en voer de volgende opdracht uit.

    ```bash
    zip -r publish.zip
    ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET voor Apache Spark in Azure Synapse Analytics notebooks 

Notebooks zijn een uitstekende optie voor het maken van prototypen van uw .NET voor Apache Spark en scenario's. U kunt uw gegevens snel en efficiënt gaan gebruiken, begrijpen, filteren, weergeven en visualiseren. 

Data engineers, gegevenswetenschappers, bedrijfsanalisten en machine learning engineers kunnen samenwerken via een gedeeld, interactief document. U ziet direct resultaten van gegevensverkenning en u kunt uw gegevens visualiseren in hetzelfde notebook.

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>.NET gebruiken voor Apache Spark notebooks

Wanneer u een nieuw notebook maakt, kiest u een taalkernel die u uw bedrijfslogica wilt uitdrukken. Kernelondersteuning is beschikbaar voor verschillende talen, waaronder C#.

Als u .NET wilt gebruiken voor Apache Spark in uw Azure Synapse Analytics-notebook, selecteert u **.NET Spark (C#)** als uw kernel en koppelt u het notebook aan een bestaande serverloze Apache Spark-pool.

De .NET Spark-notebook is gebaseerd op de [interactieve .NET-ervaringen](https://github.com/dotnet/interactive) en biedt interactieve C#-ervaringen met de mogelijkheid om .NET voor Spark out-of-the-box te gebruiken met de Spark-sessievariabele die al vooraf is `spark` gedefinieerd.

### <a name="install-nuget-packages-in-notebooks"></a>NuGet-pakketten installeren in notebooks

U kunt NuGet-pakketten van uw keuze installeren in uw notebook met behulp van de magic-opdracht vóór de `#r nuget` naam van het NuGet-pakket. In het volgende diagram ziet u een voorbeeld:

![Schermopname van het gebruik #r voor het installeren van een Spark .NET Notebook NuGet-pakket](./media/apache-spark-development-using-notebooks/synapse-spark-dotnet-notebook-nuget.png)

Zie de interactieve documentatie van .NET voor meer informatie over het werken met NuGet-pakketten in [notebooks.](https://github.com/dotnet/interactive/blob/main/docs/nuget-overview.md)

### <a name="net-for-apache-spark-c-kernel-features"></a>.NET voor Apache Spark C#-kernelfuncties

De volgende functies zijn beschikbaar wanneer u .NET gebruikt voor Apache Spark in Azure Synapse Analytics notebook:

* Declaratieve HTML: genereer uitvoer van uw cellen met behulp van HTML-syntaxis, zoals kopteksten, lijsten met opsommingstekens en zelfs het weergeven van afbeeldingen.
* Eenvoudige C#-instructies (zoals toewijzingen, afdrukken naar console, uitzonderingen, etc.).
* C#-codeblokken met meerdere regels (zoals if-instructies, foreach-lussen, klassedefinities, etc.).
* Toegang tot de standaard C#-bibliotheek (zoals System, LINQ, Enumerables, etc.).
* Ondersteuning voor C# 8.0-taalfuncties.
* `spark` als een vooraf gedefinieerde variabele om u toegang te geven tot Apache Spark sessie.
* Ondersteuning voor het definiëren [van door de gebruiker gedefinieerde .NET-functies die kunnen worden uitgevoerd binnen Apache Spark](/dotnet/spark/how-to-guides/udf-guide). We raden UF's schrijven en aanroepen [in .NET](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue) aan voor Apache Spark Interactive-omgevingen om te leren hoe u UF's in .NET gebruikt voor Apache Spark interactieve ervaringen.
* Ondersteuning voor het visualiseren van uitvoer van uw Spark-taken met behulp van verschillende grafieken (zoals lijn, staaf of histogram) en lay-outs (zoals één, overlay, bijvoorbeeld) met behulp van de `XPlot.Plotly` bibliotheek.
* De mogelijkheid om NuGet-pakketten op te nemen in uw C#-notebook.

## <a name="next-steps"></a>Volgende stappen

* [Documentatie voor .NET voor Apache Spark](/dotnet/spark/)
* [.NET voor Apache Spark Interactieve handleidingen](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue)
* [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/)
* [.NET Interactive](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
