---
title: 'ML Studio (klassiek): gegevens importeren/exporteren in Web Services-Azure'
description: Meer informatie over het gebruik van de modules gegevens importeren en gegevens exporteren voor het verzenden en ontvangen van gegevens van een webservice.
services: machine-learning
author: likebupt
ms.author: keli19
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
ms.date: 03/28/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 4824b7a4233bc65d521e1c6ded7d1ea276b2a929
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520558"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Azure Machine Learning Studio (klassieke) webservices implementeren die gebruikmaken van modules voor het importeren en exporteren van gegevens

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Wanneer u een voorspellend experiment maakt, voegt u doorgaans een invoer en uitvoer van een webservice toe. Wanneer u het experiment implementeert, kunnen gebruikers gegevens van de webservice verzenden en ontvangen via de invoer en uitvoer. Voor sommige toepassingen is het mogelijk dat de gegevens van een consument beschikbaar zijn via een gegevensfeed of al aanwezig zijn in een externe gegevens bron, zoals Azure Blob Storage. In deze gevallen hebben ze geen lees-en schrijf gegevens met behulp van de invoer en uitvoer van webservices. Ze kunnen in plaats daarvan de batch Execution Service (BES) gebruiken voor het lezen van gegevens uit de gegevens bron met behulp van een import data-module en de Score resultaten naar een andere gegevens locatie schrijven met behulp van een module export data.

De modules gegevens importeren en gegevens exporteren kunnen lezen uit en schrijven naar verschillende gegevens locaties, zoals een web-URL via HTTP, een Hive-query, een data base in Azure SQL Database, Azure Table Storage, Azure Blob Storage, een gegevensfeed of een SQL Server-Data Base.

In dit onderwerp wordt het voor beeld 5: trainen, testen, evalueren voor binaire classificatie: volwassene gegevensset gebruikt en wordt ervan uitgegaan dat de gegevensset al is geladen in een Azure SQL-tabel met de naam censusdata.

## <a name="create-the-training-experiment"></a>Het trainings experiment maken
Wanneer u het voor beeld 5: trainen, testen, evalueren voor binaire classificatie: voor beeld van de volwassene-gegevensset gebruikt, wordt het voor beeld van een volwassene van de verschuldigde gegevens van de volgroeide-waarde voor de meeste Het experiment in het canvas ziet er ongeveer uit zoals in de volgende afbeelding:

![Eerste configuratie van het experiment.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

De gegevens uit de Azure SQL-tabel lezen:

1. Verwijder de module gegevensset.
2. Typ importeren in het zoekvak onderdelen.
3. Voeg in de lijst met resultaten een module *import gegevens* toe aan het canvas voor het experiment.
4. Verbinding maken met de uitvoer van de module *import data* de invoer van de module *clean Missing Data* .
5. Selecteer in het deel venster Eigenschappen **Azure SQL database** in de vervolg keuzelijst **gegevens bron** .
6. In de velden **database server naam**, **database naam**, **gebruikers naam** en **wacht woord** voert u de juiste informatie in voor uw data base.
7. Voer in het veld database query de volgende query in.

    ```tsql
     select [age],
        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     from dbo.censusdata;
    ```
8. Klik onder aan het canvas op **uitvoeren**.

## <a name="create-the-predictive-experiment"></a>Het voorspellende experiment maken
Vervolgens stelt u het voorspellende experiment in van waaruit u de-webservice implementeert.

1. Klik aan de onderkant van het canvas op **webservice instellen** en selecteer **Predictive web service (aanbevolen)**.
2. Verwijder de webservices- *invoer* en *webservice-uitvoer modules* uit het voorspellende experiment.
3. Typ exporteren in het zoekvak onderdelen.
4. Voeg in de lijst met resultaten een module *export gegevens* toe aan het canvas van het experiment.
5. Verbinding maken met de uitvoer van de module *score model* de invoer van de module *gegevens exporteren* .
6. Selecteer in het deel venster Eigenschappen **Azure SQL database** in de vervolg keuzelijst gegevens bestemming.
7. Voer in de velden **database server naam**, **database naam**, **Server gebruikers account naam** en **wacht woord voor Server gebruikers account** de juiste informatie in voor uw data base.
8. In het veld **door komma's gescheiden lijst met kolommen die moeten worden opgeslagen** , typt u labels met scores.
9. Typ dbo in het **veld naam van gegevens tabel**. ScoredLabels. Als de tabel niet bestaat, wordt deze gemaakt wanneer het experiment wordt uitgevoerd of de webservice wordt aangeroepen.
10. Typ ScoredLabels in het veld **door komma's gescheiden lijst met DataTable-kolommen** .

Wanneer u een toepassing schrijft die de uiteindelijke webservice aanroept, wilt u mogelijk een andere invoer query of doel tabel opgeven tijdens de uitvoering. Als u deze invoer en uitvoer wilt configureren, gebruikt u de functie webservice-para meters om de eigenschap *gegevens bron* van de gegevens module *importeren* en de gegevens doel eigenschap *export gegevens* modus in te stellen.  Zie voor meer informatie over para meters voor de webservice de [para meters](/archive/blogs/machinelearning/azureml-web-service-parameters) voor de Azure machine learning Studio-webservice in de blog Cortana Intelligence en machine learning.

De webservice-para meters voor de query import en de doel tabel configureren:

1. Klik in het deel venster Eigenschappen voor de module *gegevens importeren* op het pictogram in de rechter bovenhoek van het veld **database query** en selecteer **set as web service para meter**.
2. Klik in het deel venster met eigenschappen voor de module *gegevens exporteren* op het pictogram rechtsboven in het veld **naam van gegevens tabel** en selecteer **set as web service para meter**.
3. Klik onder aan het deel venster Eigenschappen van module *gegevens exporteren* in het gedeelte **para meters** van de webservice op database query en wijzig de naam van de query.
4. Klik op **naam van gegevens tabel** en wijzig deze **tabel**.

Wanneer u klaar bent, moet uw experiment er ongeveer uitzien als de volgende afbeelding:

![Laatste uiterlijk van het experiment.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

U kunt het experiment nu implementeren als een webservice.

## <a name="deploy-the-web-service"></a>De webservice implementeren
U kunt implementeren in een klassieke of nieuwe webservice.

### <a name="deploy-a-classic-web-service"></a>Een klassieke webservice implementeren
Als u de service als een klassieke webservice wilt implementeren en een toepassing wilt maken om deze te gebruiken:

1. Klik onder aan het canvas op uitvoeren.
2. Wanneer de uitvoering is voltooid, klikt u op **webservice implementeren** en selecteert u **Web-service implementeren [klassiek]**.
3. Zoek uw API-sleutel op in het dash board van de webservice. Kopieer de app en sla deze op om later te gebruiken.
4. Klik in de tabel **standaard eindpunt** op de koppeling voor **batch uitvoering** om de API Help-pagina te openen.
5. Maak in Visual Studio een C#-console toepassing: **Nieuw**  >  **project**  >  **Visual C#**  >  **Windows klassieke bureau blad**  >  **console-app (.NET Framework)**.
6. Op de API Help-pagina, onder aan de pagina, gaat u naar de sectie **voorbeeld code** .
7. Kopieer de C#-voorbeeld code en plak deze in uw Program.cs-bestand en verwijder alle verwijzingen naar de Blob-opslag.
8. Werk de waarde van de variabele *apiKey* bij met de API-sleutel die u eerder hebt opgeslagen.
9. Zoek de aanvraag declaratie en werk de waarden bij van de para meters van de webservice die worden door gegeven aan de modules *gegevens importeren* en *exporteren* . In dit geval gebruikt u de oorspronkelijke query, maar definieert u een nieuwe tabel naam.

    ```csharp
    var request = new BatchExecutionRequest()
    {
        GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable2" },
        }
    };
    ```
10. Voer de toepassing uit.

Als de uitvoering is voltooid, wordt er een nieuwe tabel toegevoegd aan de data base met de Score resultaten.

### <a name="deploy-a-new-web-service"></a>Een nieuwe webservice implementeren

> [!NOTE]
> Als u een nieuwe webservice wilt implementeren, moet u voldoende machtigingen hebben in het abonnement waarvoor u de webservice implementeert. Zie [Manage a web service using the Azure machine learning Web Services portal](manage-new-webservice.md)(Engelstalig) voor meer informatie.

Als u een nieuwe webservice wilt implementeren en een toepassing wilt maken om deze te gebruiken:

1. Klik onder aan het canvas op **uitvoeren**.
2. Wanneer de uitvoering is voltooid, klikt u op **webservice implementeren** en selecteert u **Web service implementeren [New]**.
3. Voer op de pagina experiment implementeren een naam in voor uw webservice, selecteer een prijs plan en klik vervolgens op **implementeren**.
4. Klik op de pagina **Quick** start op **verbruikt**.
5. Klik in de sectie **voorbeeld code** op **batch**.
6. Maak in Visual Studio een C#-console toepassing: **Nieuw**  >  **project**  >  **Visual C#**  >  **Windows klassieke bureau blad**  >  **console-app (.NET Framework)**.
7. Kopieer de C#-voorbeeld code en plak deze in uw Program.cs-bestand.
8. Werk de waarde van de variabele *apiKey* bij met de **primaire sleutel** in de sectie **basis informatie over verbruik** .
9. Zoek de *scoreRequest* -declaratie en werk de waarden bij van de webservice-para meters die worden door gegeven aan de modules *gegevens importeren* en *exporteren* . In dit geval gebruikt u de oorspronkelijke query, maar definieert u een nieuwe tabel naam.

    ```csharp
    var scoreRequest = new
    {
        Inputs = new Dictionary<string, StringTable>()
        {
        },
        GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable3" },
        }
    };
    ```
10. Voer de toepassing uit.