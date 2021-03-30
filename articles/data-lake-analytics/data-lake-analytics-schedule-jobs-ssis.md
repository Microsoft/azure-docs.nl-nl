---
title: Azure Data Lake Analytics U-SQL-taken plannen met SSIS
description: Meer informatie over het gebruik van SQL Server Integration Services om U-SQL-taken te plannen met inline-script of van U-SQL-query bestanden.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/17/2018
ms.openlocfilehash: b080b433f5af49e970faba02003fb68e21a08365
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221448"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>U-SQL-taken plannen met behulp van SQL Server Integration Services (SSIS)

In dit document leert u hoe u-SQL-taken kunt organiseren en maken met behulp van de SQL Server Integration service (SSIS). 

## <a name="prerequisites"></a>Vereisten

[Azure Feature Pack voor integratie Services](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#scenario-managing-data-in-the-cloud) bevat de [Azure data Lake Analytics taak](/sql/integration-services/control-flow/azure-data-lake-analytics-task) en de [Azure data Lake Analytics verbindings beheer](/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager) waarmee u verbinding kunt maken met Azure data Lake Analytics service. Als u deze taak wilt gebruiken, zorg er dan voor dat u:

- [Down load en Installeer SQL Server Data Tools (SSDT) voor Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt)
- [Azure Feature Pack voor Integration Services (SSIS) installeren](/sql/integration-services/azure-feature-pack-for-integration-services-ssis)

## <a name="azure-data-lake-analytics-task"></a>Azure Data Lake Analytics taak

Met de Azure Data Lake Analytics taak kunnen gebruikers U-SQL-taken naar het Azure Data Lake Analytics-account verzenden. 

[Meer informatie over het configureren van Azure data Lake Analytics taak](/sql/integration-services/control-flow/azure-data-lake-analytics-task).

![Azure Data Lake Analytics taak in SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

U kunt het U-SQL-script van verschillende locaties verkrijgen door de ingebouwde functies en taken van SSIS te gebruiken. Hieronder ziet u hoe u de U-SQL-scripts voor verschillende gebruikers cases kunt configureren.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Scenario 1: inline-script aanroep tvfs en opgeslagen procs gebruiken

Configureer in Azure Data Lake Analytics taak editor **Source type** als **DirectInput** en plaats de U-SQL-instructies in **USQLStatement**.

Als u eenvoudig onderhoud en code beheer wilt, plaatst u alleen een kort U-SQL-script als inline-scripts. u kunt bestaande functies voor tabel waarden en opgeslagen procedures in uw U-SQL-data bases aanroepen. 

![Inline U-SQL-script in SSIS-taak bewerken](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Verwant artikel: [para meter door geven aan opgeslagen procedures](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Scenario 2: U-SQL-bestanden gebruiken in Azure Data Lake Store

U kunt ook U-SQL-bestanden in de Azure Data Lake Store gebruiken met behulp van **Azure data Lake Store-bestandsbeheer taak** in azure Feature Pack. Met deze aanpak kunt u de scripts gebruiken die zijn opgeslagen in de Cloud.

Volg de onderstaande stappen om de verbinding tussen Azure Data Lake Store bestandssysteem taak en Azure Data Lake Analytics taak in te stellen.

### <a name="set-task-control-flow"></a>Controle stroom voor taak instellen

Voeg in de ontwerp weergave SSIS-pakket een **Azure data Lake Store-bestandsbeheer taak**, een **foreach-lus-container** en een **Azure data Lake Analytics taak** in de foreach-lus-container toe. De Azure Data Lake Store-bestandsbeheer taak helpt U bij het downloaden van U-SQL-bestanden in uw ADLS-account naar een tijdelijke map. De foreach-lus-container en de Azure Data Lake Analytics taak helpen u bij het indienen van elk U-SQL-bestand onder de tijdelijke map naar het Azure Data Lake Analytics-account als U-SQL-taak.

![Diagram waarin een Azure Data Lake Store-bestandsbeheer taak wordt weer gegeven die wordt toegevoegd aan een foreach-lus-container.](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Azure Data Lake Store-bestandsbeheer taak configureren

1. Stel de **bewerking** in op **CopyFromADLS**.
2. **AzureDataLakeConnection** instellen, meer informatie over [Azure data Lake Store verbindings beheer](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).
3. Stel **AzureDataLakeDirectory** in. Ga naar de map waarin U uw U-SQL-scripts opslaat. Gebruik relatief pad dat relatief is ten opzichte van de hoofdmap van het Azure Data Lake Store-account.
4. Stel **doel** in op een map die de gedownloade U-SQL-scripts in de cache opslaat. Dit mappad wordt gebruikt in de foreach-lus-container voor U-SQL-taak verzending. 

![Azure Data Lake Store-bestandsbeheer taak configureren](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Meer informatie over Azure data Lake Store-bestandsbeheer taak](/sql/integration-services/control-flow/azure-data-lake-store-file-system-task).

### <a name="configure-foreach-loop-container"></a>Foreach-lus-container configureren

1. Stel op de pagina **verzameling** de **Enumerator** in op de bestand- **Enumerator van foreach**.

2. Stel **map** onder **configuratie groep enumerator** in op de tijdelijke map met de gedownloade U-SQL-scripts.

3. Stel **bestanden** onder **configuratie van enumerator** in op `*.usql` zodat de lus-container alleen de bestanden onderschept die eindigen op `.usql` .

    ![Scherm afbeelding waarin de foreach-lus-editor met de verzameling ' Collection ' wordt weer gegeven en de configuratie secties enumerator en enumerator zijn gemarkeerd.](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. Voeg op de pagina **variabelen toewijzingen** een door de gebruiker gedefinieerde variabele toe om de bestands naam voor elk U-SQL-bestand op te halen. Stel de **index** in op 0 om de bestands naam op te halen. In dit voor beeld definieert u een variabele met de naam `User::FileName` . Deze variabele wordt gebruikt voor het dynamisch ophalen van een U-SQL-script bestand verbinding en het instellen van een U-SQL-taak naam in Azure Data Lake Analytics taak.

    ![Foreach-lus-container configureren om bestands naam op te halen](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Azure Data Lake Analytics taak configureren 

1. Stel **Source type** in op **FileConnection**.

2. Stel **FileConnection** in op de bestands verbinding die verwijst naar de bestands objecten die worden geretourneerd vanuit de foreach-lus-container.
    
    Deze bestands verbinding maken:

   1. Kies de **\<New Connection...>** instelling in FileConnection.
   2. Stel het **gebruiks type** in op het **bestaande bestand** en stel het **bestand** in op het bestandspad van een bestaand bestand.

       ![Scherm opname van de editor voor bestands verbindings beheer met het ' bestaande bestand ' dat is geselecteerd voor het type gebruik.](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. Klik in de weer gave **verbindings beheer** met de rechter muisknop op de bestands verbinding die nu zojuist is gemaakt en kies **Eigenschappen**.

   4. Vouw in het venster **Eigenschappen** de optie **expressies** uit en stel **Connections Tring** in op de variabele die is gedefinieerd in de foreach-lus-container, bijvoorbeeld `@[User::FileName]` .

       ![Foreach-lus-container configureren](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Stel **AzureDataLakeAnalyticsConnection** in op het Azure data Lake Analytics-account waarnaar u taken wilt verzenden. Meer informatie over [Azure data Lake Analytics verbindings beheer](/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager).

4. Andere taak configuraties instellen. [Meer informatie](/sql/integration-services/control-flow/azure-data-lake-analytics-task).

5. **Expressies** gebruiken om u-SQL-taak naam dynamisch in te stellen:

    1. Voeg op de pagina **expressies** een nieuw expressie sleutel-waardepaar voor **JobName** toe.
    2. Stel de waarde voor JobName in op de variabele die is gedefinieerd in de foreach-lus-container, bijvoorbeeld `@[User::FileName]` .
    
        ![SSIS-expressie configureren voor de U-SQL-taak naam](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Scenario 3: U-SQL-bestanden gebruiken in Azure Blob Storage

U kunt u-SQL-bestanden in Azure Blob Storage gebruiken met behulp van de **Azure Blob-Download taak** in azure Feature Pack. Met deze aanpak kunt u de scripts in de Cloud gebruiken.

De stappen zijn vergelijkbaar met [scenario 2: u-SQL-bestanden gebruiken in azure data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Wijzig de Download taak voor de Azure Data Lake Store-bestands systeem in Azure Blob. Meer [informatie over de Download taak voor Azure Blob](/sql/integration-services/control-flow/azure-blob-download-task).

De controle stroom ziet er als volgt uit.

![U-SQL-bestanden gebruiken in Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Scenario 4: U-SQL-bestanden op de lokale computer gebruiken

Naast het gebruik van U-SQL-bestanden die zijn opgeslagen in de Cloud, kunt u ook bestanden gebruiken op de lokale computer of bestanden die zijn geïmplementeerd met uw SSIS-pakketten.

1. Klik met de rechter muisknop op **verbindings** beheer in SSIS-project en kies **Nieuw verbindings beheer**.

2. Selecteer **Bestands** type en klik op **toevoegen...**.

3. Stel het **gebruiks type** in op het **bestaande bestand** en stel het **bestand** in op het bestand op de lokale computer.

    ![Bestands verbinding toevoegen aan het lokale bestand](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. **Azure data Lake Analytics** taak toevoegen en:
    1. Stel **Source type** in op **FileConnection**.
    2. Stel **FileConnection** in op de bestands verbinding die nu is gemaakt.

5. Andere configuraties voor Azure Data Lake Analytics taak volt ooien.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Scenario 5-U-SQL-instructie in SSIS-variabele gebruiken

In sommige gevallen moet u mogelijk de U-SQL-instructies dynamisch genereren. U kunt **SSIS-variabele** gebruiken met **SSIS-expressie** en andere SSIS-taken, zoals script taak, om U te helpen de u-SQL-instructie dynamisch te genereren.

1. Open het venster Varia bles via **SSIS > Varia bles** op het bovenste niveau menu.

2. Voeg een SSIS-variabele toe en stel de waarde direct in of gebruik de **expressie** om de waarde te genereren.

3. **Azure data Lake Analytics taak** toevoegen en:
    1. Stel **Source type** in op **Variable**.
    2. Stel **SourceVariable** in op de SSIS-variabele die nu is gemaakt.

4. Andere configuraties voor Azure Data Lake Analytics taak volt ooien.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Scenario 6: para meters door geven aan U-SQL-script

In sommige gevallen wilt u de waarde voor de U-SQL-variabele dynamisch instellen in het U-SQL-script. De functie voor het **toewijzen van para meters** in azure data Lake Analytics taak Help bij dit scenario. Er zijn meestal twee typische gebruikers cases:

- Stel de variabelen voor het invoer-en uitvoer bestand dynamisch in op basis van de huidige datum en tijd.
- Stel de para meter in voor opgeslagen procedures.

Meer [informatie over het instellen van para meters voor het U-SQL-script](/sql/integration-services/control-flow/azure-data-lake-analytics-task#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Volgende stappen

- [SSIS-pakketten uitvoeren in Azure](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)
- [Azure Feature Pack voor Integration Services (SSIS)](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#scenario-managing-data-in-the-cloud)
- [U-SQL-taken plannen met behulp van Azure Data Factory](../data-factory/transform-data-using-data-lake-analytics.md)