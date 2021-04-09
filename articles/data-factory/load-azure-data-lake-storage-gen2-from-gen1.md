---
title: Gegevens kopiëren van Azure Data Lake Storage Gen1 naar Gen2
description: Azure Data Factory gebruiken om gegevens van Azure Data Lake Storage Gen1 naar Gen2 te kopiëren
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 4a4bc0b55928fcaa40cd1cb03ca0522e611df476
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727716"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Gegevens kopiëren van Azure Data Lake Storage Gen1 naar Gen2 met Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 is een set mogelijkheden die is toegewezen aan big data Analytics die is ingebouwd in [Azure Blob-opslag](../storage/blobs/storage-blobs-introduction.md). U kunt het gebruiken om interactie met uw gegevens te maken met behulp van paradigma's voor zowel het bestandssysteem als de objectopslag.

Als u momenteel Azure Data Lake Storage Gen1 gebruikt, kunt u Azure Data Lake Storage Gen2 evalueren door gegevens te kopiëren van Data Lake Storage Gen1 naar Gen2 met behulp van Azure Data Factory.

Azure Data Factory is een volledig beheerde service voor gegevens integratie in de Cloud. U kunt de service gebruiken om de Lake te vullen met gegevens uit een uitgebreide set on-premises en cloud-gebaseerde gegevens opslag en tijd besparen wanneer u uw analyse oplossingen bouwt. Zie de tabel met [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met ondersteunde connectors.

Azure Data Factory biedt een oplossing voor beheerde gegevens verplaatsing met scale-out. Vanwege de scale-out-architectuur van Data Factory kunnen gegevens worden opgenomen met een hoge door voer. Zie de prestaties van de [Kopieer activiteit](copy-activity-performance.md)voor meer informatie.

In dit artikel leest u hoe u het hulp programma voor Data Factory het kopiëren van gegevens kunt gebruiken om gegevens van Azure Data Lake Storage Gen1 naar Azure Data Lake Storage Gen2 te kopiëren. U kunt vergelijkbare stappen volgen om gegevens te kopiëren vanuit andere typen gegevensarchieven.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Azure Data Lake Storage Gen1 account met gegevens.
* Azure Storage account met Data Lake Storage Gen2 ingeschakeld. Als u geen opslag account hebt, [maakt u een account](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer in het menu links de optie **een resource maken**  >  **gegevens en analyses**  >  **Data Factory**.
   
   ![Data Factory selectie in het nieuwe deel venster](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geef op de pagina **nieuw Data Factory** waarden op voor de velden die worden weer gegeven in de volgende afbeelding: 
      
   ![Pagina nieuwe Data Factory](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Naam**: Voer een wereld wijd unieke naam in voor uw Azure-Data Factory. Als het fout bericht ' Data Factory-naam \" LoadADLSDemo \" is niet beschikbaar ' wordt weer gegeven, voert u een andere naam in voor de Data Factory. Gebruik bijvoorbeeld de naam _**uwnaam**_**ADFTutorialDataFactory**. Maak de data factory opnieuw. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
    * **Abonnement**: Selecteer het Azure-abonnement waarin u de Data Factory wilt maken. 
    * **Resource groep**: Selecteer een bestaande resource groep in de vervolg keuzelijst. U kunt ook de optie **nieuwe maken** selecteren en de naam van een resource groep invoeren. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie. 
    * **Versie**: Selecteer **V2**.
    * **Locatie**: Selecteer de locatie voor de Data Factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven die door de data factory worden gebruikt, kunnen zich op andere locaties of in andere regio's bevinden. 

3. Selecteer **Maken**.
4. Nadat het maken is voltooid, gaat u naar uw data factory. U ziet de **Data Factory** start pagina zoals wordt weer gegeven in de volgende afbeelding: 
   
   ![Startpagina van de gegevensfactory](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Selecteer de tegel **auteur & monitor** om de gegevens integratie toepassing in een afzonderlijk tabblad te openen.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Gegevens laden in Azure Data Lake Storage Gen2

1. Selecteer op de pagina **aan de slag** de **gegevens kopiëren** tegel om het hulp programma Copy data te starten. 

   ![De tegel gegevens kopiëren](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Geef op de pagina **Eigenschappen** **CopyFromADLSGen1ToGen2** op voor het veld **taak naam** . Selecteer **Next**.

    ![De pagina Eigenschappen](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Selecteer op de pagina **brongegevens archief** de optie **+ nieuwe verbinding maken**.

    ![De pagina Brongegevensarchief](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Selecteer **Azure Data Lake Storage Gen1** in de connectorgalerie en selecteer **Doorgaan**.
    
    ![Azure Data Lake Storage Gen1-pagina van brongegevens archief](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Voer de volgende stappen uit op de pagina **Azure data Lake Storage gen1 verbinding opgeven** :

   a. Selecteer uw Data Lake Storage Gen1 voor de account naam en geef of Valideer de **Tenant**.
  
   b. Selecteer **verbinding testen** om de instellingen te valideren. Selecteer vervolgens **Voltooien**.
  
   c. U ziet dat er een nieuwe verbinding is gemaakt. Selecteer **Next**.
   
   > [!IMPORTANT]
   > In deze procedure gebruikt u een beheerde identiteit voor Azure-resources om uw Azure Data Lake Storage Gen1 te verifiëren. Volg [deze instructies](connector-azure-data-lake-store.md#managed-identity)om de beheerde identiteit de juiste machtigingen te geven in azure data Lake Storage gen1.
   
   ![Azure Data Lake Storage Gen1 account opgeven](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Blader op de pagina **het invoer bestand of de map kiezen** naar de map en het bestand waarnaar u wilt kopiëren. Selecteer de map of het bestand en selecteer **kiezen**.

    ![Het invoerbestand of de invoermap kiezen](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Geef het Kopieer gedrag op door de opties **bestanden recursief** en **binair kopiëren** te selecteren. Selecteer **Next**.

    ![Scherm afbeelding toont het Kies het invoer bestand of de map waarin u bestand recursief en binaire kopie kopiëren kunt selecteren.](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Selecteer op de pagina **doel gegevens archief** de optie **+ nieuwe verbinding maken**  >  **Azure data Lake Storage Gen2**  >  **door gaan**.

    ![De pagina Doelgegevensarchief](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Voer de volgende stappen uit op de pagina **Azure data Lake Storage Gen2 verbinding opgeven** :

   a. Selecteer uw Data Lake Storage Gen2 geschikte account in de vervolg keuzelijst **opslag account naam** .
   
   b. Selecteer **Voltooien** om de verbinding te maken. Selecteer vervolgens **Volgende**.
   
   ![Azure Data Lake Storage Gen2 account opgeven](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Voer op de pagina **het uitvoer bestand of de map kiezen** **copyfromadlsgen1** in als de naam van de uitvoermap en selecteer **volgende**. Data Factory maakt het bijbehorende Azure Data Lake Storage Gen2 bestands systeem en submappen tijdens het kopiëren als ze niet bestaan.

    ![Scherm afbeelding toont het mappad dat u invoert.](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Selecteer op de pagina **Instellingen** de optie **Volgende** om de standaardinstellingen te gebruiken.

12. Controleer de instellingen op de pagina **samen vatting** en selecteer **volgende**.

    ![Overzichtspagina](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Selecteer op de **pagina implementatie** de optie **controleren** om de pijp lijn te bewaken.

    ![De pagina Implementatie](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd. De kolom **Acties** bevat onder andere koppelingen om details van de uitvoering van activiteiten te bekijken en de pijplijn opnieuw uit te voeren.

    ![Pijplijnuitvoeringen controleren](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Selecteer de koppeling **uitvoeringen van activiteit weer** geven in de kolom **acties** om de uitvoering van activiteiten te bekijken die zijn gekoppeld aan de pijplijn uitvoering. Omdat er slechts één activiteit (kopieeractiviteit) in de pijplijn is, ziet u slechts één vermelding in de lijst. Als u wilt terugkeren naar de weer gave pijplijn uitvoeringen, selecteert u de koppeling **pijp lijnen** bovenaan. Selecteer **Vernieuwen** om de lijst te vernieuwen. 

    ![Uitvoering van activiteiten controleren](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Als u de uitvoerings Details voor elke Kopieer activiteit wilt bewaken, selecteert u de **Details** koppeling (bril-afbeelding) onder **acties** in de weer gave activiteiten bewaking. U kunt details bekijken, zoals het volume van de gegevens die uit de bron zijn gekopieerd naar de sink, de gegevensdoorvoer, de uitvoeringsstappen met de overeenkomstige duur en de gebruikte configuraties.

    ![Details van uitvoering van activiteit bewaken](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Controleer of de gegevens zijn gekopieerd naar uw Azure Data Lake Storage Gen2-account.

## <a name="best-practices"></a>Aanbevolen procedures

Zie voor meer informatie over het upgraden van Azure Data Lake Storage Gen1 naar Azure Data Lake Storage Gen2 in [het algemeen uw Big Data Analytics-oplossingen upgraden van Azure data Lake Storage gen1 naar Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md). In de volgende secties worden aanbevolen procedures geïntroduceerd voor het gebruik van Data Factory voor een gegevens upgrade van Data Lake Storage Gen1 naar Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Gegevens partitie voor historische gegevens kopie

- Als uw totale gegevens grootte in Data Lake Storage Gen1 minder is dan 30 TB en het aantal bestanden kleiner is dan 1.000.000, kunt u alle gegevens in één exemplaar van de Kopieer activiteit kopiëren.
- Als u een grotere hoeveelheid gegevens hebt om te kopiëren, of als u de flexibiliteit wilt hebben voor het beheren van de gegevens migratie in batches en deze wilt volt ooien binnen een specifiek tijds bestek, partitioneert u de gegevens. Partitioneren vermindert ook het risico van een onverwacht probleem.

Gebruik een proef versie van het concept om de end-to-end-oplossing te controleren en de Kopieer doorvoer in uw omgeving te testen. Belangrijkste stappen voor het testen van concepten: 

1. Maak een Data Factory pijp lijn met één Kopieer activiteit om meerdere TBs van gegevens van Data Lake Storage Gen1 naar Data Lake Storage Gen2 te kopiëren om een basis lijn voor de Kopieer prestaties te verkrijgen. Begin met [DIUs (Data Integration units)](copy-activity-performance-features.md#data-integration-units) als 128. 
2. Op basis van de Kopieer doorvoer die u in stap 1 krijgt, berekent u de geschatte tijd die nodig is voor de volledige gegevens migratie. 
3. Beschrijving Maak een controle tabel en definieer het bestands filter voor het partitioneren van de bestanden die moeten worden gemigreerd. De manier waarop u de bestanden partitioneert, is: 

    - Partitioneren op mapnaam of mapnaam met een Joker teken filter. We raden deze methode aan.
    - Partitioneren op het tijdstip van de laatste wijziging van een bestand.

### <a name="network-bandwidth-and-storage-io"></a>Netwerk bandbreedte en opslag-I/O 

U kunt de gelijktijdigheid van Data Factory Kopieer taken die gegevens van Data Lake Storage Gen1 lezen en gegevens naar Data Lake Storage Gen2 schrijven, beheren. Op deze manier kunt u het gebruik op die opslag-I/O beheren om te voor komen dat dit van invloed is op het normale zakelijke werk op Data Lake Storage Gen1 tijdens de migratie.

### <a name="permissions"></a>Machtigingen 

In Data Factory ondersteunt de [Data Lake Storage gen1-connector](connector-azure-data-lake-store.md) Service-Principal en beheerde identiteit voor Azure-resource verificaties. De [Data Lake Storage Gen2-connector](connector-azure-data-lake-storage.md) ondersteunt account sleutel, Service-Principal en beheerde identiteit voor Azure-resource verificaties. Als u wilt dat Data Factory alle bestanden of Acl's (toegangs beheer lijsten) die u nodig hebt, kunt navigeren en kopiëren, moet u hoge machtigingen verlenen voor het account dat u hebt opgegeven om toegang te krijgen tot, lees of schrijf alle bestanden en stel de Acl's in als u dit wilt doen. Ken tijdens de migratie periode een rol van super gebruiker of eigenaar toe. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Acl's van Data Lake Storage Gen1 behouden

Zie [acl's van data Lake Storage gen1 behouden](connector-azure-data-lake-storage.md#preserve-acls)als u de acl's samen met gegevens bestanden wilt repliceren wanneer u een upgrade uitvoert van Data Lake Storage Gen1 naar Data Lake Storage Gen2. 

### <a name="incremental-copy"></a>Incrementele kopie 

U kunt verschillende benaderingen gebruiken om alleen de nieuwe of bijgewerkte bestanden van Data Lake Storage Gen1 te laden:

- Nieuwe of bijgewerkte bestanden laden op basis van een gepartitioneerde map of bestands naam. Een voor beeld is/2019/05/13/*.
- Nieuwe of bijgewerkte bestanden laden door LastModifiedDate.
- Identificeer nieuwe of bijgewerkte bestanden door een hulp programma of oplossing van derden. Geef vervolgens de naam van het bestand of de map door aan de Data Factory pijp lijn via de para meter of een tabel of bestand. 

De juiste frequentie voor incrementele belasting is afhankelijk van het totale aantal bestanden in Azure Data Lake Storage Gen1 en het volume van nieuwe of bijgewerkte bestanden dat elke keer moet worden geladen. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> Overzicht van de [Kopieer activiteit](copy-activity-overview.md) 
>  [Azure data Lake Storage gen1-connector](connector-azure-data-lake-store.md) 
>  [Azure data Lake Storage Gen2-connector](connector-azure-data-lake-storage.md)