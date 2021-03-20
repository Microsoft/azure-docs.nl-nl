---
title: Gegevens analyseren in Azure Data Lake Storage Gen1-Power BI
description: Meer informatie over het gebruik van Power BI Desktop voor het analyseren en visualiseren van gegevens die zijn opgeslagen in Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bcc561cd5eea4372d798fff4580362ba0879c3a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91574191"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Gegevens in Azure Data Lake Storage Gen1 analyseren met behulp van Power BI
In dit artikel leert u hoe u Power BI Desktop kunt gebruiken voor het analyseren en visualiseren van gegevens die zijn opgeslagen in Azure Data Lake Storage Gen1.

## <a name="prerequisites"></a>Vereisten
Voordat u met deze zelfstudie begint, moet u het volgende hebben of hebben gedaan:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Data Lake Storage gen1-account**. Volg de instructies in aan [de slag met Azure data Lake Storage gen1 met behulp van de Azure Portal](data-lake-store-get-started-portal.md). In dit artikel wordt ervan uitgegaan dat u al een Data Lake Storage Gen1 account hebt gemaakt met de naam **myadlsg1** en dat er een voorbeeld gegevensbestand (**Drivers.txt**) naar het bestand is geüpload. Dit voorbeeld bestand kan worden gedownload van [Azure data Lake Git-opslag plaats](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. U kunt dit downloaden van het [micro soft Download centrum](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Een rapport maken in Power BI Desktop
1. Start Power BI Desktop op de computer.
2. Klik op het lint **Start** op **gegevens ophalen** en klik vervolgens op meer. Klik in het dialoog venster **gegevens ophalen** op **Azure**, klik op **Azure data Lake Store** en klik vervolgens op **verbinding maken**.
   
    ![Scherm afbeelding van het dialoog venster gegevens ophalen met de optie Azure Data Lake Store gemarkeerd en de optie verbinding maken.](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Verbinding maken met Data Lake Storage Gen1")
3. Als er een dialoog venster wordt weer geven over de connector die zich in een ontwikkelings fase bevinden, kunt u kiezen om door te gaan.
4. Geef in het dialoog venster **Azure data Lake Store** de URL naar uw data Lake Storage gen1-account op en klik vervolgens op **OK**.
   
    ![URL voor Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL voor Data Lake Storage Gen1")
5. Klik in het volgende dialoog venster op **Aanmelden** om u aan te melden bij het data Lake Storage gen1-account. U wordt omgeleid naar de aanmeldings pagina van uw organisatie. Volg de aanwijzingen om u aan te melden bij het account.
   
    ![Aanmelden bij Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Aanmelden bij Data Lake Storage Gen1")
6. Nadat u bent aangemeld, klikt u op **verbinding maken**.
   
    ![Scherm afbeelding van het dialoog venster Azure Data Lake Store met de optie verbinding maken.](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Verbinding maken met Data Lake Storage Gen1")
7. In het volgende dialoog venster ziet u het bestand dat u hebt geüpload naar uw Data Lake Storage Gen1-account. Controleer de gegevens en klik vervolgens op **laden**.
   
    ![Gegevens laden uit Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Gegevens laden uit Data Lake Storage Gen1")
8. Nadat de gegevens in Power BI zijn geladen, worden de volgende velden op het tabblad **velden** weer gegeven.
   
    ![Geïmporteerde velden](./media/data-lake-store-power-bi/imported-fields.png "Geïmporteerde velden")
   
    Als u de gegevens wilt visualiseren en analyseren, hebben we de voor keur dat de gegevens beschikbaar zijn volgens de volgende velden:
   
    ![Gewenste velden](./media/data-lake-store-power-bi/desired-fields.png "Gewenste velden")
   
    In de volgende stappen wordt de query bijgewerkt voor het converteren van de geïmporteerde gegevens in de gewenste indeling.
9. Klik op het lint **Start** op **query's bewerken**.
   
    ![Scherm opname van het lint start met de optie Query's bewerken aangeroepen.](./media/data-lake-store-power-bi/edit-queries.png "Query's bewerken")
10. Klik in de query-editor, onder de kolom **inhoud** , op **binair**.
    
    ![Scherm afbeelding van de query-editor met de inhouds kolom out out.](./media/data-lake-store-power-bi/convert-query1.png "Query's bewerken")
11. U ziet een pictogram met een bestand dat het **Drivers.txt** bestand vertegenwoordigt dat u hebt geüpload. Klik met de rechter muisknop op het bestand en klik op **CSV**.    
    
    ![Scherm opname van de query-editor met de CSV-optie.](./media/data-lake-store-power-bi/convert-query2.png "Query's bewerken")
12. U ziet een uitvoer zoals hieronder wordt weer gegeven. Uw gegevens zijn nu beschikbaar in een indeling die u kunt gebruiken om visualisaties te maken.
    
    ![Scherm afbeelding van de query-editor waarbij de uitvoer wordt weer gegeven zoals verwacht.](./media/data-lake-store-power-bi/convert-query3.png "Query's bewerken")
13. Klik op het lint **Start** op **sluiten en Toep assen** en klik vervolgens op **sluiten en Toep assen**.
    
    ![Scherm opname van het lint start met de optie sluiten en Toep assen aangeroepen.](./media/data-lake-store-power-bi/load-edited-query.png "Query's bewerken")
14. Zodra de query is bijgewerkt, worden de nieuwe velden die beschikbaar zijn voor visualisatie weer gegeven op het tabblad **velden** .
    
    ![Bijgewerkte velden](./media/data-lake-store-power-bi/updated-query-fields.png "Bijgewerkte velden")
15. Laat ons een cirkel diagram maken om de Stuur Programma's in elke plaats voor een bepaald land of bepaalde regio weer te geven. U doet dit door de volgende selecties te maken.
    
    1. Klik op het tabblad visualisaties op het symbool voor een cirkel diagram.
       
        ![Cirkel diagram maken](./media/data-lake-store-power-bi/create-pie-chart.png "Cirkel diagram maken")
    2. De kolommen die we gaan gebruiken, zijn **kolom 4** (naam van de plaats) en **kolom 7** (naam van het land/de regio). Sleep deze kolommen van het tabblad **velden** naar het tabblad **Visualisaties** , zoals hieronder wordt weer gegeven.
       
        ![Visualisaties maken](./media/data-lake-store-power-bi/create-visualizations.png "Visualisaties maken")
    3. Het cirkel diagram moet er nu uitzien zoals hieronder wordt weer gegeven.
       
        ![Cirkeldiagram](./media/data-lake-store-power-bi/pie-chart.png "Visualisaties maken")
16. Als u een bepaald land/regio selecteert in de filters op pagina niveau, kunt u nu het aantal Stuur Programma's weer geven in elke plaats van het geselecteerde land/de regio. Selecteer bijvoorbeeld onder het tabblad **Visualisaties** de optie **Brazilië** onder **filters op pagina niveau**.
    
    ![Een land/regio selecteren](./media/data-lake-store-power-bi/select-country.png "Een land/regio selecteren")
17. Het cirkel diagram wordt automatisch bijgewerkt om de Stuur Programma's in de steden van Brazilië weer te geven.
    
    ![Stuur Programma's in een land/regio](./media/data-lake-store-power-bi/driver-per-country.png "Stuur Programma's per land/regio")
18. Klik in het menu **bestand** op **Opslaan** om de visualisatie op te slaan als een Power bi desktop bestand.

## <a name="publish-report-to-power-bi-service"></a>Rapport publiceren naar Power BI-service
Wanneer u de visualisaties in Power BI Desktop hebt gemaakt, kunt u deze met anderen delen door deze te publiceren naar de Power BI-service. Zie [publiceren vanuit Power bi Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/)voor instructies over hoe u dit doet.

## <a name="see-also"></a>Zie ook
* [Gegevens analyseren in Data Lake Storage Gen1 met behulp van Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

