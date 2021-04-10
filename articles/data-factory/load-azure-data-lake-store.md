---
title: Gegevens laden in Azure Data Lake Storage Gen1
description: Azure Data Factory gebruiken om gegevens te kopiëren naar Azure Data Lake Storage Gen1
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 1e75fc65b4afea9dc895580e4711e0050cb7c64f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101716411"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Gegevens in Azure Data Lake Storage Gen1 laden met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md) (voorheen bekend als Azure data Lake Store) is een bedrijfsbrede opslag ruimte voor Big Data analytische werk belastingen. Met Data Lake Storage Gen1 kunt u gegevens van elke grootte, type en opname snelheid vastleggen. De gegevens worden op één plek vastgelegd voor operationele en experimentele analyses.

Azure Data Factory is een volledig beheerde service voor gegevens integratie in de Cloud. U kunt de service gebruiken om het Lake data van uw bestaande systeem te vullen en tijd te besparen bij het bouwen van uw analyse oplossingen.

Azure Data Factory biedt de volgende voor delen voor het laden van gegevens in Data Lake Storage Gen1:

* **Eenvoudig in te stellen**: een intuïtieve 5-stap wizard zonder scripting vereist.
* **Ondersteuning voor uitgebreide gegevens opslag**: ingebouwde ondersteuning voor een uitgebreide set on-premises en cloud-gebaseerde gegevens archieven. Zie de tabel met [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een gedetailleerde lijst.
* **Beveiligd en compatibel**: gegevens worden overgebracht via https of ExpressRoute. De wereld wijde service aanwezigheid zorgt ervoor dat uw gegevens de geografische grens nooit verlaten.
* **Hoge prestaties**: tot 1 GB/s gegevens laden in data Lake Storage gen1. Zie [prestaties van Kopieer activiteit](copy-activity-performance.md)voor meer informatie.

Dit artikel laat u zien hoe u met het hulp programma Data Factory Gegevens kopiëren _gegevens van Amazon S3 in data Lake Storage gen1 kunt laden_. U kunt vergelijkbare stappen volgen om gegevens te kopiëren vanuit andere typen gegevensarchieven.

> [!NOTE]
> Zie [gegevens kopiëren naar of van data Lake Storage gen1 met behulp van Azure Data Factory](connector-azure-data-lake-store.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Data Lake Storage Gen1 account: als u geen Data Lake Storage Gen1 account hebt, raadpleegt u de instructies in [een Data Lake Storage gen1-account maken](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3: in dit artikel wordt beschreven hoe u gegevens kopieert van Amazon S3. U kunt andere gegevens archieven gebruiken door vergelijk bare stappen te volgen.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer in het linkermenu **Een resource maken** > **Analyse** > **Data Factory**:
   
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geef op de pagina **nieuw Data Factory** waarden op voor de velden die worden weer gegeven in de volgende afbeelding: 
      
   ![De pagina Nieuwe data factory](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Naam**: Voer een wereld wijd unieke naam in voor uw Azure-Data Factory. Als het fout bericht ' Data Factory-naam \" LoadADLSG1Demo \" is niet beschikbaar ' wordt weer gegeven, voert u een andere naam in voor de Data Factory. U kunt bijvoorbeeld _**de naam**_**ADFTutorialDataFactory**. Probeer de data factory opnieuw te maken. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
    * **Abonnement**: Selecteer het Azure-abonnement waarin u de Data Factory wilt maken. 
    * **Resource groep**: Selecteer een bestaande resource groep in de vervolg keuzelijst of selecteer de optie **nieuwe maken** en voer de naam van een resource groep in. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
    * **Versie**: Selecteer **V2**.
    * **Locatie**: Selecteer de locatie voor de Data Factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevens archieven die door data factory worden gebruikt, kunnen zich op andere locaties en regio's bevinden. Deze gegevens archieven bevatten Azure Data Lake Storage Gen1, Azure Storage, Azure SQL Database, enzovoort.

3. Selecteer **Maken**.
4. Nadat het maken is voltooid, gaat u naar uw data factory. U ziet de **Data Factory** start pagina zoals wordt weer gegeven in de volgende afbeelding: 
   
   ![Startpagina van de gegevensfactory](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Klik op de tegel **Maken en bewaken** om de toepassing Gegevensintegratie in een afzonderlijk tabblad te openen.

## <a name="load-data-into-data-lake-storage-gen1"></a>Gegevens laden in Data Lake Storage Gen1

1. Selecteer op de pagina **aan de slag** de **gegevens kopiëren** tegel om het gegevens kopiëren-hulp programma te starten: 

   ![De tegel Copy Data-hulpprogramma](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Geef op de pagina **Eigenschappen** **CopyFromAmazonS3ToADLS** op voor het veld **taak naam** en selecteer **volgende**:

    ![De pagina Eigenschappen](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Klik op de pagina **brongegevens archief** op **+ nieuwe verbinding maken**:

    ![De pagina Brongegevensarchief](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Selecteer **Amazon S3** en selecteer **door gaan**
    
    ![S3-pagina van brongegevens archief](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Voer op de pagina **Amazon S3-verbinding opgeven** de volgende stappen uit: 
   1. Geef de waarde voor de **toegangs sleutel-id** op.
   2. Geef de waarde voor de **geheime toegangs sleutel** op.
   3. Selecteer **Finish**.
   
      ![Scherm afbeelding toont het deel venster met de nieuwe gekoppelde service, waarin u waarden kunt invoeren.](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Er wordt een nieuwe verbinding weer geven. Selecteer **Next**.
   
   ![Scherm afbeelding toont de nieuwe verbinding.](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. Blader op de pagina **Het invoerbestand of de invoermap kiezen** naar de map en het bestand dat u wilt kopiëren. Selecteer de map/het bestand, selecteer **kiezen** en selecteer vervolgens **volgende**:

    ![Het invoerbestand of de invoermap kiezen](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Kies het Kopieer gedrag door de opties **bestanden recursief** en **binaire kopie** kopiëren (bestanden kopiëren als-is) te selecteren. Selecteer **volgende**:

    ![Scherm afbeelding toont het Kies het invoer bestand of de map waarin u bestand recursief en binaire kopie kopiëren kunt selecteren.](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. Klik op de pagina **doel gegevens archief** op **+ nieuwe verbinding maken** en selecteer vervolgens **Azure data Lake Storage gen1** en selecteer **door gaan**:

    ![De pagina Doelgegevensarchief](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Voer de volgende stappen uit op de pagina **nieuwe gekoppelde service (Azure data Lake Storage gen1)** : 

   1. Selecteer uw Data Lake Storage Gen1 account voor de **naam** van het data Lake Store-account.
   2. Geef de **Tenant** op en selecteer volt ooien.
   3. Selecteer **Next**.
   
   > [!IMPORTANT]
   > In dit scenario gebruikt u een beheerde identiteit voor Azure-resources om uw Data Lake Storage Gen1-account te verifiëren. Zorg ervoor dat u het MSI-bestand de juiste machtigingen geeft in Data Lake Storage Gen1 door [deze instructies](connector-azure-data-lake-store.md#managed-identity)te volgen.
   
   ![Data Lake Storage Gen1 account opgeven](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Voer op de pagina **het uitvoer bestand of de map kiezen** **copyfroms3** in als de naam van de uitvoermap en selecteer **volgende**: 

    ![Scherm afbeelding toont het mappad dat u invoert.](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Selecteer op de pagina **instellingen** de optie **volgende**:

    ![Pagina Instellingen](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Controleer de instellingen op de pagina **samen vatting** en selecteer **volgende**:

    ![Overzichtspagina](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Selecteer op de **pagina implementatie** de optie **controleren** om de pijp lijn te bewaken (taak):

    ![De pagina Implementatie](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd. De kolom **acties** bevat koppelingen om de details van de activiteit weer te geven en de pijp lijn opnieuw uit te voeren:

    ![Pijplijnuitvoeringen controleren](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Selecteer de koppeling **uitvoeringen van activiteit weer** geven in de kolom **acties** om de uitvoering van activiteiten te bekijken die zijn gekoppeld aan de pijplijn uitvoering. Omdat er slechts één activiteit (kopieeractiviteit) in de pijplijn is, ziet u slechts één vermelding in de lijst. Als u wilt terugkeren naar de weer gave pijplijn uitvoeringen, selecteert u de koppeling **pijp lijnen** bovenaan. Selecteer **Vernieuwen** om de lijst te vernieuwen. 

    ![Uitvoering van activiteiten controleren](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Als u de uitvoerings Details voor elke Kopieer activiteit wilt bewaken, selecteert u de koppeling **Details** onder **acties** in de weer gave activiteiten bewaking. U kunt details, zoals het volume van de gegevens die uit de bron zijn gekopieerd, bewaken naar de sink, gegevens doorvoer, uitvoerings stappen met de overeenkomstige duur en de gebruikte configuraties:

    ![Details van uitvoering van activiteit bewaken](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Controleer of de gegevens zijn gekopieerd naar uw Data Lake Storage Gen1-account: 

    ![Data Lake Storage Gen1 uitvoer controleren](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over de ondersteuning van Data Lake Storage Gen1: 

> [!div class="nextstepaction"]
>[Azure Data Lake Storage Gen1-connector](connector-azure-data-lake-store.md)
