---
title: 'Zelfstudie: een pijplijn maken met de wizard Kopiëren '
description: In deze zelfstudie maakt u een Azure Data Factory-pijplijn met een kopieeractiviteit. Hiervoor gebruikt u de wizard Kopiëren die wordt ondersteund door Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 60a575fc211c512c8657bffd567c96f98cc3d69a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377003"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-data-factory-copy-wizard"></a>Zelfstudie: een pijplijn maken met de kopieeractiviteit middels de Data Factory-wizard Kopiëren
> [!div class="op_single_selector"]
> * [Overzicht en vereisten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [De wizard Kopiëren](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sjabloon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [zelfstudie over kopieeractiviteiten](../quickstart-create-data-factory-dot-net.md). 


Deze zelfstudie laat zien hoe u de **Wizard Kopiëren** kunt gebruiken om gegevens uit een Azure Blob-opslag te kopiëren naar Azure SQL Database. 

Met de **Wizard Kopiëren** van Azure Data Factory maakt u snel een gegevenspijplijn waarmee u gegevens uit een ondersteund brongegevensarchief kunt kopiëren naar een doelgegevensarchief. Daarom wordt u aangeraden de wizard te gebruiken als eerste stap bij het maken van een voorbeeldpijplijn voor uw gegevensverplaatsingsscenario. Zie [ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor een lijst met gegevensarchieven die worden ondersteund als bron en als doel.  

Deze zelfstudie laat zien hoe u een Azure-gegevensfactory kunt maken, de wizard Kopiëren kunt starten en een reeks stappen kunt uitvoeren om informatie over uw scenario voor gegevensopname/-verplaatsing op te geven. Nadat u de stappen in de wizard hebt voltooid, maakt de wizard automatisch een pijplijn met een kopieeractiviteit om gegevens te kopiëren uit een Azure Blob-opslag naar Azure SQL Database. Zie het artikel [Activiteiten voor gegevensverplaatsing](data-factory-data-movement-activities.md) voor meer informatie over kopieeractiviteiten.

## <a name="prerequisites"></a>Vereisten
U dient eerst te voldoen aan de vereisten in het artikel [Overzicht van de zelfstudie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voordat u deze zelfstudie volgt.

## <a name="create-data-factory"></a>Een gegevensfactory maken
In deze stap gebruikt u Azure Portal om een Azure Data Factory met de naam **ADFTutorialDataFactory** te maken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik in het menu in de linkerbovenhoek op **Een resource maken**, klik op **Gegevens en analyses** en **Data Factory**. 
   
   ![Nieuw -> DataFactory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)
2. In de blade **Nieuwe gegevensfactory**:
   
   1. Voer **ADFTutorialDataFactory** in als **naam**.
       De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als dit foutbericht wordt geretourneerd: `Data factory name "ADFTutorialDataFactory" is not available`, wijzigt u de naam van de gegevensfactory (bijvoorbeeld uwnaamADFTutorialDataFactoryDDMMJJJJ) en maakt een nieuwe. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.  
      
       ![Naam van gegevensfactory niet beschikbaar](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)    
   2. Selecteer uw Azure-**abonnement**.
   3. Voer een van de volgende stappen uit voor de resourcegroep: 
      
      - Selecteer **Bestaande gebruiken** om een bestaande resourcegroep te selecteren.
      - Selecteer **Nieuwe maken** als u een naam voor een resourcegroep wilt typen.
          
        Bij sommige van de stappen in deze zelfstudie wordt ervan uitgegaan dat u de naam **ADFTutorialResourceGroup** gebruikt voor de resourcegroep. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../../azure-resource-manager/management/overview.md) voor meer informatie.
   4. Selecteer een **locatie** voor de gegevensfactory.
   5. Selecteer het selectievakje **Vastmaken aan dashboard** onderaan de blade.  
   6. Klik op **Create**.
      
       ![Blade voor een nieuwe gegevensfactory](media/data-factory-copy-data-wizard-tutorial/new-data-factory-blade.png)            
3. Wanneer het aanmaken is voltooid, ziet u de blade **Gegevensfactory** zoals op de volgende afbeelding wordt weergegeven:
   
   ![Startpagina van de gegevensfactory](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## <a name="launch-copy-wizard"></a>De wizard Kopiëren starten
1. Klik op de blade Data Factory op **Gegevens kopiëren** om de **wizard Kopiëren** te starten. 
   
   > [!NOTE]
   > Als u ziet dat de webbrowser is vastgelopen bij Autoriseren..., schakelt u **Cookies van derden en sitegegevens blokkeren** in de browserinstellingen uit. U kunt deze instelling ook ingeschakeld laten en een uitzondering maken voor **login.microsoftonline.com**. Open de wizard vervolgens opnieuw.
2. Op de pagina **Eigenschappen**:
   
   1. Voer **CopyFromBlobToAzureSql** in als **taaknaam**
   2. Voer de **beschrijving** in (optioneel).
   3. Wijzig de **Startdatum en -tijd** en de **Einddatum en -tijd** zodat de einddatum is ingesteld op vandaag en de startdatum op vijf dagen eerder.  
   4. Klik op **Volgende**.  
      
      ![Hulpprogramma voor kopiëren - pagina Eigenschappen](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. Op de pagina **Brongegevensarchief** klikt u op de tegel **Azure Blob Storage**. U gebruikt deze pagina om het brongegevensarchief op te geven voor de kopieertaak. 
   
    ![Hulpprogramma voor kopiëren - pagina van brongegevensarchief](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
4. Op de pagina **Het Azure Blob Storage-account opgeven**:
   
   1. Voer **AzureStorageLinkedService** in als **naam van de gekoppelde service**.
   2. Controleer of de optie **Van Azure-abonnementen** is geselecteerd als **accountselectiemethode**.
   3. Selecteer uw Azure-**abonnement**.  
   4. Selecteer een **Azure-opslagaccount** uit de lijst met Azure-opslagaccounts die beschikbaar is voor het abonnement dat u hebt geselecteerd. U kunt er ook voor kiezen om de opslagaccountinstellingen handmatig op te geven. Selecteer daarvoor de optie **Handmatig invoeren** als **accountselectiemethode** en klik vervolgens op **Volgende**. 
      
      ![Hulpprogramma voor kopiëren - Het Azure Blob Storage-account opgeven](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
5. Op de pagina **Het invoerbestand of de invoermap kiezen**:
   
   1. Dubbelklik op **adftutorial** (map).
   2. Selecteer **emp.txt** en klik op **Kiezen**
      
      ![Schermafbeelding met de optie Kiezen voor uw invoerbestand.](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
6. Klik op de pagina **Het invoerbestand of de invoermap kiezen** op **Volgende** Selecteer niet **Binaire kopie**. 
   
    ![Schermafbeelding met de optie Binaire kopie voor uw invoer.](./media/data-factory-copy-data-wizard-tutorial/chose-input-file-folder.png) 
7. Op de pagina **Bestandsinstellingen** ziet u de scheidingstekens en het schema dat automatisch is gedetecteerd door de wizard tijdens het parseren van het bestand. U kunt de scheidingstekens ook handmatig invoeren zodat de wizard Kopiëren stopt met automatisch detecteren, of als u wilt dat gegevens worden overschreven. Klik op **Volgende** nadat u de scheidingstekens hebt gecontroleerd en een voorbeeld van de gegevens hebt bekeken. 
   
    ![Hulpprogramma voor kopiëren - Bestandsindelingsinstellingen](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. Op de pagina Doelgegevensarchief selecteert u **Azure SQL Database** en klikt u op **Volgende**.
   
    ![Hulpprogramma voor kopiëren - Doelarchief kiezen](./media/data-factory-copy-data-wizard-tutorial/choose-destination-store.png)
9. Op de pagina **De Azure SQL-database opgeven**:
   
   1. Typ **AzureSqlLinkedService** in het veld **Verbindingsnaam**.
   2. Controleer of de optie **Van Azure-abonnementen** is geselecteerd als **Server-/databaseselectiemethode**.
   3. Selecteer uw Azure-**abonnement**.  
   4. Selecteer de **servernaam** en **database**.
   5. Voer de **gebruikersnaam** en het **wachtwoord** in.
   6. Klik op **Volgende**.  
      
      ![Hulpprogramma voor kopiëren - Azure SQL Database opgeven](./media/data-factory-copy-data-wizard-tutorial/specify-azure-sql-database.png)
10. Op de pagina **Tabeltoewijzing** selecteert u uit de vervolgkeuzelijst **emp** in het veld **Bestemming** en klikt u op de **pijl naar beneden** (optioneel) om het schema en een voorbeeld van de gegevens te bekijken.
    
     ![Hulpprogramma voor kopiëren - Tabeltoewijzing](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
11. Op de pagina **Schematoewijzing** klikt u op **Volgende**.
    
    ![Hulpprogramma voor kopiëren - Schematoewijzing](./media/data-factory-copy-data-wizard-tutorial/schema-mapping-page.png)
12. Op de pagina **Prestatie-instellingen** klikt u op **Volgende**. 
    
    ![Schermafbeelding van de pagina Prestatie-instellingen, waar u Volgende kunt selecteren.](./media/data-factory-copy-data-wizard-tutorial/performance-settings.png)
13. Lees de informatie op de pagina **Samenvatting** en klik op **Voltooien**. De wizard maakt twee gekoppelde services, twee gegevenssets (invoer en uitvoer) en één pijplijn in de gegevensfactory (van waaruit u de wizard Kopiëren hebt gestart). 
    
    ![Schermafbeelding van de pagina Samenvatting, waar u Volgende kunt selecteren.](./media/data-factory-copy-data-wizard-tutorial/summary-page.png)

## <a name="launch-monitor-and-manage-application"></a>Monitor starten en toepassing beheren
1. Klik op de pagina **Implementatie** op de koppeling: `Click here to monitor copy pipeline`.
   
   ![Hulpprogramma voor kopiëren - Implementeren voltooid](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
2. De bewakingstoepassing wordt gestart op een afzonderlijk tabblad in uw webbrowser.   
   
   ![App voor bewaking](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)   
3. Klik op de knop **Vernieuwen** in de lijst **ACTIVITEITSVENSTERS** onderaan om de meest recente status van uursegmenten te zien. U ziet vijf activiteitsvensters voor vijf dagen tussen de start- en eindtijd voor de pijplijn. De lijst wordt niet automatisch vernieuwd. Het kan dus zijn dat u een paar keer op Vernieuwen moet klikken om alle activiteitsvensters weer te geven met de status Gereed. 
4. Selecteer een activiteitsvenster in de lijst. Bekijk de details hierover deze in de **Activiteitsvensterverkenner** aan de rechterkant.

    ![Details van activiteitsvenster](media/data-factory-copy-data-wizard-tutorial/activity-window-details.png)    

    Let op: de datums 11, 12, 13, 14 en 15 hebben een groene kleur. Dit betekent dat de dagelijkse uitvoersegmenten voor deze datums al zijn geproduceerd. U ziet deze kleurcodering ook bij de pijplijn en de uitvoergegevensset in de diagramweergave. U ziet (op basis van de kleurcodering) dat in de vorige stap al twee segmenten zijn gemaakt, dat één segment momenteel wordt verwerkt en dat de andere twee segmenten wachten op verwerking. 

    Zie het artikel [Pijplijnen bewaken en beheren met behulp van de app voor bewaking](data-factory-monitor-manage-app.md) voor meer informatie over het gebruik van deze toepassing.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u voor een kopieerbewerking een Azure Blob-opslag gebruikt als brongegevensarchief en Azure SQL Database als doelgegevensarchief. De volgende tabel bevat een lijst met gegevensarchieven die worden ondersteund als bron en doel voor de kopieeractiviteit: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Klik voor details over velden/eigenschappen die u ziet in de wizard Kopiëren voor een gegevensarchief, in de tabel op de koppeling voor dit gegevensarchief. 