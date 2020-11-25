---
title: Uw Azure Data Lake Analytics code testen
description: Meer informatie over het toevoegen van test cases voor de U-SQL-en uitgebreide C#-code voor Azure Data Lake Analytics.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: 7310c67ef20a4134d4f613ea969c96802958bf62
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015227"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Uw Azure Data Lake Analytics-code testen

Azure Data Lake biedt de [U-SQL-](data-lake-analytics-u-sql-get-started.md) taal. U-SQL combineert declaratieve SQL met dwingende C# om gegevens op elke schaal te verwerken. In dit document leert u hoe u test cases kunt maken voor de U-SQL-en Extended C# UDO-code (door de gebruiker gedefinieerde operator).

## <a name="test-u-sql-scripts"></a>U-SQL-scripts testen

Het U-SQL-script wordt gecompileerd en geoptimaliseerd voor uitvoer bare code die wordt uitgevoerd in azure of op uw lokale computer. Het compilatie-en optimalisatie proces behandelt het hele U-SQL-script als geheel. U kunt geen traditionele eenheids test uitvoeren voor elke instructie. Met de U-SQL test-SDK en de lokale run SDK kunt u echter testen op script niveau uitvoeren.

### <a name="create-test-cases-for-u-sql-script"></a>Test cases voor U-SQL-script maken

Met Azure Data Lake-Hulpprogram Ma's voor Visual Studio kunt u test cases voor U-SQL-scripts maken.

1. Klik met de rechter muisknop op een U-SQL-script in Solution Explorer en selecteer vervolgens **eenheid maken**.

1. Maak een nieuw test project of Voeg de test case in een bestaand test project in.

   ![Data Lake-Hulpprogram Ma's voor Visual Studio: een U-SQL test project configuratie maken](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>De test gegevens bron beheren

Wanneer u u-SQL-scripts test, moet u invoer bestanden testen. Als u de test gegevens wilt beheren, klikt u in **Solution Explorer** met de rechter muisknop op het U-SQL-project en selecteert u **Eigenschappen**. U kunt een bron invoeren in de **test gegevens bron**.

![Data Lake-Hulpprogram Ma's voor Visual Studio--test gegevens bron project configureren](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

Wanneer u de interface aanroept `Initialize()` in de U-SQL test-SDK, wordt er een tijdelijke lokale hoofdmap voor gegevens gemaakt in de werkmap van het test project. Alle bestanden en mappen in de bronmap test gegevens worden gekopieerd naar de tijdelijke lokale data root-map voordat u de U-SQL-script test uitvoert. U kunt meer mappen voor de gegevens bron testen toevoegen door het pad naar de map met test gegevens te splitsen met een punt komma.

### <a name="manage-the-database-environment-for-testing"></a>De database omgeving voor het testen beheren

Als uw U-SQL-scripts gebruikmaken van of query's uitvoeren met U-SQL database-objecten, moet u de database omgeving initialiseren voordat u U-SQL-test cases uitvoert. Deze methode kan nodig zijn wanneer u opgeslagen procedures aanroept. De `Initialize()` interface in de u-SQL test-SDK helpt u bij het implementeren van alle data bases waarnaar wordt verwezen door het U-SQL-project naar de map met tijdelijke lokale data-hoofdmap in de werkmap van het test project.

Zie voor meer informatie over het beheren van U-SQL database project verwijzingen voor een U-SQL-project [verwijzen naar een u-SQL database-project](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Test resultaten controleren

De `Run()` interface retourneert een resultaat van de taak uitvoering. *0* betekent geslaagd, en *1* betekent fout. U kunt ook C# assert-functies gebruiken om de uitvoer te controleren.

### <a name="run-test-cases-in-visual-studio"></a>Test cases uitvoeren in Visual Studio

Een U-SQL-script test project is gebouwd op basis van een C#-eenheids test-framework. Nadat u het project hebt gemaakt, **Test** selecteert u test Verkenner van  >  **Windows** testen  >  **Test Explorer**. U kunt test cases uitvoeren vanuit **test Explorer**. U kunt ook met de rechter muisknop op het. CS-bestand in de eenheids test klikken en **tests uitvoeren** selecteren.

## <a name="test-c-udos"></a>C# Udo's testen

### <a name="create-test-cases-for-c-udos"></a>Test cases voor C#-Udo's maken

U kunt een test raamwerk voor C#-eenheden gebruiken om uw door de gebruiker gedefinieerde C#-Opera tors (Udo's) te testen. Bij het testen van Udo's moet u bijbehorende **IRowset** -objecten voorbereiden als invoer.

Er zijn twee manieren om een **IRowset** -object te maken:

- Gegevens uit een bestand laden om **IRowset** te maken:

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Gegevens uit een gegevens verzameling gebruiken om **IRowset** te maken:

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Test resultaten controleren

Nadat u de UDO-functies hebt aangeroepen, kunt u de resultaten controleren via de verificatie van het schema en de waarde van de rijenset met behulp van C# assert-functies. U kunt een **u-SQL C# Udo-eenheids test project** toevoegen aan uw oplossing. Als u dit wilt doen, selecteert u **bestand > nieuw > project** in Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Test cases uitvoeren in Visual Studio

Nadat u het project hebt gemaakt, **Test** selecteert u test Verkenner van  >  **Windows** testen  >  **Test Explorer**. U kunt test cases uitvoeren vanuit **test Explorer**. U kunt ook met de rechter muisknop op het. CS-bestand in de eenheids test klikken en **tests uitvoeren** selecteren.

## <a name="run-test-cases-in-azure-pipelines"></a>Test cases uitvoeren in azure-pijp lijnen<a name="run-test-cases-in-azure-devops"></a>

Zowel **U-SQL-script test projecten** als **c# Udo test-projecten** nemen c#-eenheids test projecten over. Met de [Visual Studio-test taak](/azure/devops/pipelines/test/getting-started-with-continuous-testing) in azure-pijp lijnen kunnen deze test cases worden uitgevoerd.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>U-SQL-test cases uitvoeren in azure-pijp lijnen

Zorg ervoor dat u voor een U-SQL-test `CPPSDK` de belasting op de computer bouwt en geef het `CPPSDK` pad door aan `USqlScriptTestRunner(cppSdkFolderFullPath: @"")` .

#### <a name="what-is-cppsdk"></a>Wat is CPPSDK?

CPPSDK is een pakket dat micro soft Visual C++ 14 en Windows SDK 10.0.10240.0 bevat. Dit pakket bevat de omgeving die nodig is voor de U-SQL-runtime. U kunt dit pakket verkrijgen onder de Azure Data Lake-Hulpprogram Ma's voor Visual Studio-installatiemap:

- Voor Visual Studio 2015 is het onder `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Voor Visual Studio 2017 is het onder `C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- Voor Visual Studio 2019 is het onder `C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>CPPSDK voorbereiden in de Azure pipelines build-agent

De meest voorkomende manier om de CPPSDK-afhankelijkheid in azure-pijp lijnen voor te bereiden is als volgt:

1. Zip-map die de CPPSDK-bibliotheken bevat.

1. Check het zip-bestand in op uw broncode beheer systeem. Het zip-bestand zorgt ervoor dat u alle bibliotheken in de map CPPSDK incheckt, zodat bestanden niet worden genegeerd vanwege een `.gitignore` bestand.

1. Pak het zip-bestand uit in de build-pijp lijn.

1. Wijs `USqlScriptTestRunner` naar deze uitgepakte map op de computer bouwen.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>C# UDO-test cases uitvoeren in azure-pijp lijnen

Voor een C# UDO-test moet u verwijzen naar de volgende assembly's, die nodig zijn voor Udo's.

- Micro soft. Analytics. interfaces
- Micro soft. Analytics. types
- Micro soft. Analytics. UnitTest

Als u hiernaar verwijst via [het Nuget-pakket micro soft. Azure. DataLake. USQL. interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), moet u een herstel taak voor Nuget toevoegen in uw build-pijp lijn.

## <a name="next-steps"></a>Volgende stappen

- [CI/CD-pijp lijn instellen voor Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [U-SQL-script uitvoeren op uw lokale computer](data-lake-analytics-data-lake-tools-local-run.md)
- [U-SQL database project gebruiken om U-SQL database te ontwikkelen](data-lake-analytics-data-lake-tools-develop-usql-database.md)