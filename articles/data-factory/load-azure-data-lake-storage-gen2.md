---
title: Gegevens laden in Azure Data Lake Storage Gen2
description: Azure Data Factory gebruiken om gegevens te kopiëren naar Azure Data Lake Storage Gen2
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 075778e2650d3b1f67447078eb6cb130849bbb35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593744"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Gegevens laden in Azure Data Lake Storage Gen2 met Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 is een set mogelijkheden die wordt toegewezen aan big data Analytics, ingebouwd in [Azure Blob-opslag](../storage/blobs/storage-blobs-introduction.md). Hiermee kunt u een interface met uw gegevens maken met behulp van zowel bestands systeem-als object opslag-modellen.

Azure Data Factory (ADF) is een volledig beheerde service voor gegevens integratie in de Cloud. U kunt de service gebruiken om de Lake te vullen met gegevens uit een uitgebreide set on-premises en cloud-gebaseerde gegevens opslag en tijd besparen bij het bouwen van uw analyse oplossingen. Zie de tabel met [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een gedetailleerde lijst met ondersteunde connectors.

Azure Data Factory biedt een oplossing voor beheerde gegevens verplaatsing met scale-out. Als gevolg van de scale-out-architectuur van ADF kunnen gegevens worden opgenomen met een hoge door voer. Zie [prestaties van Kopieer activiteit](copy-activity-performance.md)voor meer informatie.

Dit artikel laat u zien hoe u met het hulp programma Data Factory Gegevens kopiëren gegevens kunt laden van _Amazon Web Services S3-service_ in _Azure data Lake Storage Gen2_. U kunt vergelijkbare stappen volgen om gegevens te kopiëren vanuit andere typen gegevensarchieven.

>[!TIP]
>Raadpleeg [deze specifieke stapsgewijze instructies voor het](load-azure-data-lake-storage-gen2-from-gen1.md)kopiëren van gegevens van Azure data Lake Storage gen1 naar Gen2.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Azure Storage account met Data Lake Storage Gen2 ingeschakeld: als u geen opslag account hebt, [maakt u een account](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* AWS-account met een S3-Bucket die gegevens bevat: in dit artikel wordt beschreven hoe u gegevens kopieert vanuit Amazon S3. U kunt andere gegevens archieven gebruiken door vergelijk bare stappen te volgen.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer in het linkermenu **Een resource maken** > **Integratie** > **Data Factory**:
   
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Geef op de pagina **nieuw Data Factory** waarden op voor de volgende velden:
 
    * **Naam**: Voer een wereld wijd unieke naam in voor uw Azure-Data Factory. Als u de fout melding ' Data Factory-naam *naamvanuwgegevensfactory* is niet beschikbaar ' ontvangt, voert u een andere naam in voor de Data Factory. U kunt bijvoorbeeld _**de naam**_**ADFTutorialDataFactory**. Probeer de data factory opnieuw te maken. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
    * **Abonnement**: Selecteer het Azure-abonnement waarin u de Data Factory wilt maken. 
    * **Resource groep**: Selecteer een bestaande resource groep in de vervolg keuzelijst of selecteer de optie **nieuwe maken** en voer de naam van een resource groep in. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
    * **Versie**: Selecteer **V2**.
    * **Locatie**: Selecteer de locatie voor de Data Factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevens archieven die door data factory worden gebruikt, kunnen zich op andere locaties en regio's bevinden. 

3. Selecteer **Maken**.

4. Nadat het maken is voltooid, gaat u naar uw data factory. U ziet de **Data Factory** start pagina zoals wordt weer gegeven in de volgende afbeelding: 
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Start pagina voor de Azure Data Factory, met de tegel Author & monitor.":::

   Klik op de tegel **Maken en bewaken** om de toepassing Gegevensintegratie in een afzonderlijk tabblad te openen.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Gegevens laden in Azure Data Lake Storage Gen2

1. Selecteer op de pagina **Aan de slag** de tegel **Gegevens kopiëren** om het hulpprogramma Gegevens kopiëren te openen.

2. Geef op de pagina **Eigenschappen** **CopyFromAmazonS3ToADLS** op voor het veld **taak naam** en selecteer **volgende**.

    ![De pagina Eigenschappen](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Klik op de pagina **Brongegevensarchief** op **+ Nieuwe verbinding maken**. Selecteer **Amazon S3** in de galerie connector en selecteer **door gaan**.
    
    ![S3-pagina van brongegevens archief](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Voer de volgende stappen uit op de pagina **nieuwe gekoppelde service (Amazon S3)** :

   1. Geef de waarde voor de **toegangs sleutel-id** op.
   2. Geef de waarde voor de **geheime toegangs sleutel** op.
   3. Klik op **Verbinding testen** om de instellingen te valideren en selecteer vervolgens **Maken**.

      ![Amazon S3-account opgeven](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   4. U ziet dat er een nieuwe AmazonS3-verbinding wordt gemaakt. Selecteer **Next**. 

5. Blader op de pagina **Het invoerbestand of de invoermap kiezen** naar de map en het bestand dat u wilt kopiëren. Selecteer de map/het bestand en selecteer vervolgens **kiezen**.

    ![Het invoerbestand of de invoermap kiezen](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Geef het Kopieer gedrag op door de **recursief** en **binaire Kopieer** opties te controleren. Selecteer **Next**.

    ![Scherm afbeelding toont het Kies het invoer bestand of de map waarin u binaire kopie en recursief kunt selecteren.](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Klik op de pagina **doel gegevens archief** op **+ nieuwe verbinding maken** en selecteer vervolgens **Azure data Lake Storage Gen2** en selecteer **door gaan**.

    ![De pagina Doelgegevensarchief](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Voer de volgende stappen uit op de pagina **nieuwe gekoppelde service (Azure data Lake Storage Gen2)** :

   1. Selecteer het account dat geschikt is voor Data Lake Storage Gen2 in de vervolgkeuzelijst Opslagaccountnaam.
   2. Selecteer **maken** om de verbinding te maken. Selecteer vervolgens **Volgende**.   

        ![Azure Data Lake Storage Gen2 account opgeven](./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png)

9. Voer op de pagina **het uitvoer bestand of de map kiezen** **copyfroms3** in als de naam van de uitvoermap en selecteer **volgende**. Bij het maken van de ADF worden de bijbehorende ADLS Gen2 bestands systeem en submappen tijdens het kopiëren gemaakt als deze niet bestaat.

    ![Scherm afbeelding toont het mappad dat u invoert.](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Selecteer op de pagina **Instellingen** de optie **Volgende** om de standaardinstellingen te gebruiken.

    ![Pagina Instellingen](./media/load-azure-data-lake-storage-gen2/copy-settings.png)

11. Controleer de instellingen op de pagina **samen vatting** en selecteer **volgende**.

    ![Overzichtspagina](./media/load-azure-data-lake-storage-gen2/copy-summary.png)

12. Selecteer op de pagina **Implementatie** de optie **Controleren** om de pijplijn of taak te controleren. 
 
13. Wanneer de uitvoering van de pijp lijn is voltooid, ziet u een pijplijn uitvoering die wordt geactiveerd door een hand matige trigger. U kunt via koppelingen in de kolom **NAAM PIJPLIJN** details van activiteiten bekijken en de pijplijn opnieuw uitvoeren.

    ![Pijplijnuitvoeringen controleren](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Als u de uitvoering van activiteiten wilt zien die zijn gekoppeld aan de pijplijn uitvoering, selecteert u de koppeling **CopyFromAmazonS3ToADLS** onder de kolom pijplijn naam. Selecteer de koppeling **Details** (pictogram van een bril) in de kolom NAAM ACTIVITEIT om details van de kopieerbewerking te zien. U kunt details, zoals het volume van de gegevens die uit de bron zijn gekopieerd, bewaken naar de sink, gegevens doorvoer, uitvoerings stappen met de overeenkomstige duur en de gebruikte configuratie.
 
    ![Uitvoering van activiteiten controleren](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)
    
    ![Details van uitvoering van activiteit bewaken](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

15. Selecteer Vernieuwen om de weergave te vernieuwen. Selecteer **Alle pijplijnuitvoeringen** bovenaan om terug te gaan naar de weergave Pijplijnuitvoeringen.

16. Controleer of de gegevens zijn gekopieerd naar uw Data Lake Storage Gen2-account.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2-connector](connector-azure-data-lake-storage.md)
