---
title: Gegevens kopiëren van Azure Storage-blobs naar Data Lake Storage Gen1
description: Gebruik het hulp programma AdlCopy om gegevens te kopiëren van Azure Storage-blobs naar Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 84ee65b05af4393f49696875bda41df39e283d5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85980086"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Gegevens kopiëren van Azure Storage-blobs naar Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [DistCp gebruiken](data-lake-store-copy-data-wasb-distcp.md)
> * [AdlCopy gebruiken](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Storage Gen1 biedt een opdracht regel programma, [AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358), voor het kopiëren van gegevens uit de volgende bronnen:

* Van Azure Storage blobs in Data Lake Storage Gen1. U kunt AdlCopy niet gebruiken om gegevens te kopiëren van Data Lake Storage Gen1 naar Azure Storage blobs.
* Tussen twee Data Lake Storage Gen1 accounts.

Daarnaast kunt u het hulp programma AdlCopy in twee verschillende modi gebruiken:

* **Zelfstandig**, waar het hulp programma data Lake Storage gen1-resources gebruikt om de taak uit te voeren.
* **Met een Data Lake Analytics-account**, waarbij de eenheden die aan uw data Lake Analytics-account zijn toegewezen, worden gebruikt om de Kopieer bewerking uit te voeren. U kunt deze optie gebruiken wanneer u de Kopieer taken op een voorspel bare manier wilt uitvoeren.

## <a name="prerequisites"></a>Vereisten

Voordat u dit artikel gaat lezen, moet u beschikken over het volgende:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Storage blobs** -container met enkele gegevens.
* **Een Data Lake Storage gen1-account**. Zie [aan de slag met Azure data Lake Storage gen1](data-lake-store-get-started-portal.md) voor instructies over het maken van een account.
* **Data Lake Analytics-account (optioneel)** : Zie aan de [slag met Azure data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) voor instructies over het maken van een Data Lake Analytics-account.
* **Hulp programma AdlCopy**. Installeer het [AdlCopy-hulp programma](https://www.microsoft.com/download/details.aspx?id=50358).

## <a name="syntax-of-the-adlcopy-tool"></a>De syntaxis van het hulp programma AdlCopy

Gebruik de volgende syntaxis om te werken met het AdlCopy-hulp programma

```console
AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern
```

De para meters in de syntaxis worden hieronder beschreven:

| Optie | Beschrijving |
| --- | --- |
| Bron |Hiermee geeft u de locatie van de bron gegevens in de Azure Storage-blob. De bron kan een BLOB-container, een BLOB of een andere Data Lake Storage Gen1-account zijn. |
| Doel |Hiermee geeft u de Data Lake Storage Gen1 bestemming waarnaar moet worden gekopieerd. |
| SourceKey |Hiermee geeft u de toegangs sleutel voor opslag voor de BLOB-bron van Azure Storage. Dit is alleen vereist als de bron een BLOB-container of een blob is. |
| Account |**Optioneel**. Gebruik deze als u Azure Data Lake Analytics account wilt gebruiken om de Kopieer taak uit te voeren. Als u de optie/account gebruikt in de syntaxis, maar geen Data Lake Analytics account opgeeft, gebruikt AdlCopy een standaard account om de taak uit te voeren. Als u deze optie gebruikt, moet u ook de bron (Azure Storage Blob) en het doel (Azure Data Lake Storage Gen1) toevoegen als gegevens bronnen voor uw Data Lake Analytics-account. |
| Eenheden |Hiermee geeft u het aantal Data Lake Analytics-eenheden op dat voor de Kopieer taak wordt gebruikt. Deze optie is verplicht als u de **/account** -optie gebruikt om het data Lake Analytics-account op te geven. |
| Patroon |Hiermee geeft u een regex-patroon op dat aangeeft welke blobs of bestanden moeten worden gekopieerd. AdlCopy maakt gebruik van hoofdletter gevoelige overeenkomst. Het standaard patroon wanneer er geen patroon is opgegeven, is het kopiëren van alle items. Het opgeven van meerdere bestands patronen wordt niet ondersteund. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>AdlCopy (als zelfstandig) gebruiken om gegevens van een Azure Storage BLOB te kopiëren

1. Open een opdracht prompt en navigeer naar de map waarin AdlCopy is geïnstalleerd `%HOMEPATH%\Documents\adlcopy` .
1. Voer de volgende opdracht uit om een specifieke blob van de bron container naar een Data Lake Storage Gen1-map te kopiëren:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>
    ```

    Bijvoorbeeld:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

    >[!NOTE]
    >Met de syntaxis hierboven geeft u het bestand op dat moet worden gekopieerd naar een map in het Data Lake Storage Gen1-account. Het hulp programma AdlCopy maakt een map als de opgegeven mapnaam niet bestaat.

    U wordt gevraagd om de referenties in te voeren voor het Azure-abonnement waarmee u uw Data Lake Storage Gen1-account hebt. Er wordt een uitvoer weer gegeven die er ongeveer als volgt uitziet:

    ```output
    Initializing Copy.
    Copy Started.
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```

1. U kunt ook alle blobs kopiëren van de ene container naar het Data Lake Storage Gen1-account met behulp van de volgende opdracht:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>  
    ```      

    Bijvoorbeeld:

    ```console
    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

### <a name="performance-considerations"></a>Prestatieoverwegingen

Als u kopieert vanuit een Azure Blob Storage-account, wordt u mogelijk beperkt tijdens het kopiëren van de Blob-opslag. Hierdoor worden de prestaties van uw Kopieer taak verslechterd. Zie Azure Storage limieten bij [Azure-abonnement en service limieten](../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over de limieten van Azure Blob Storage.

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>AdlCopy (als zelfstandig) gebruiken om gegevens te kopiëren van een andere Data Lake Storage Gen1-account

U kunt AdlCopy ook gebruiken om gegevens tussen twee Data Lake Storage Gen1 accounts te kopiëren.

1. Open een opdracht prompt en navigeer naar de map waarin AdlCopy is geïnstalleerd `%HOMEPATH%\Documents\adlcopy` .
1. Voer de volgende opdracht uit om een specifiek bestand te kopiëren van de ene Data Lake Storage Gen1-account naar een andere.

    ```console
    AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/
    ```

    Bijvoorbeeld:

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

   > [!NOTE]
   > Met de bovenstaande syntaxis geeft u het bestand op dat moet worden gekopieerd naar een map in het doel Data Lake Storage Gen1 account. Het hulp programma AdlCopy maakt een map als de opgegeven mapnaam niet bestaat.
   >
   >

    U wordt gevraagd om de referenties in te voeren voor het Azure-abonnement waarmee u uw Data Lake Storage Gen1-account hebt. Er wordt een uitvoer weer gegeven die er ongeveer als volgt uitziet:

    ```output
    Initializing Copy.
    Copy Started.|
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```
1. Met de volgende opdracht worden alle bestanden van een specifieke map in het bron Data Lake Storage Gen1-account gekopieerd naar een map in het doel Data Lake Storage Gen1-account.

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

### <a name="performance-considerations"></a>Prestatieoverwegingen

Wanneer u AdlCopy als een zelfstandig hulp programma gebruikt, wordt de kopie uitgevoerd op gedeelde, door Azure beheerde bronnen. De prestaties die u mogelijk in deze omgeving krijgt, zijn afhankelijk van de systeem belasting en de beschik bare resources. Deze modus wordt het beste gebruikt voor kleine overdrachten op ad hoc basis. Er hoeven geen para meters te worden afgestemd wanneer AdlCopy wordt gebruikt als een zelfstandig hulp programma.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Gebruik AdlCopy (met Data Lake Analytics account) om gegevens te kopiëren

U kunt ook uw Data Lake Analytics-account gebruiken om de AdlCopy-taak uit te voeren om gegevens te kopiëren van Azure Storage-blobs naar Data Lake Storage Gen1. Normaal gesp roken gebruikt u deze optie wanneer de gegevens die moeten worden verplaatst, zich in het bereik van gigabytes en terabyte bevinden en u een betere en voorspel bare prestatie doorvoer wilt.

Als u uw Data Lake Analytics-account met AdlCopy wilt kopiëren uit een Azure Storage Blob, moet u de bron (Azure Storage Blob) toevoegen als gegevens bron voor uw Data Lake Analytics-account. Zie [Data Lake Analytics account gegevens bronnen beheren](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources)voor instructies over het toevoegen van aanvullende gegevens bronnen aan uw data Lake Analytics-account.

> [!NOTE]
> Als u kopieert van een Azure Data Lake Storage Gen1-account als bron met behulp van een Data Lake Analytics-account, hoeft u het Data Lake Storage Gen1-account niet aan het Data Lake Analytics-account te koppelen. De vereiste om het bron archief te koppelen aan het Data Lake Analytics-account is alleen wanneer de bron een Azure Storage-account is.
>
>

Voer de volgende opdracht uit om een Azure Storage-BLOB te kopiëren naar een Data Lake Storage Gen1-account met behulp van Data Lake Analytics account:

```console
AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>
```

Bijvoorbeeld:

```console
AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2
```

U kunt ook de volgende opdracht uitvoeren om alle bestanden van een specifieke map in het bron Data Lake Storage Gen1-account te kopiëren naar een map in het doel Data Lake Storage Gen1 account met behulp van Data Lake Analytics account:

```console
AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2
```

### <a name="performance-considerations"></a>Prestatieoverwegingen

Bij het kopiëren van gegevens in het bereik van terabytes biedt het gebruik van AdlCopy met uw eigen Azure Data Lake Analytics-account betere en voorspel bare prestaties. De para meter die moet worden afgestemd, is het aantal Azure Data Lake Analytics-eenheden dat u voor de Kopieer taak wilt gebruiken. Als u het aantal eenheden verhoogt, worden de prestaties van uw Kopieer taak verbeterd. Elk bestand dat moet worden gekopieerd, mag Maxi maal één eenheid gebruiken. Als u meer eenheden opgeeft dan het aantal bestanden dat wordt gekopieerd, worden de prestaties niet verbeterd.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>AdlCopy gebruiken om gegevens te kopiëren met behulp van patroon overeenkomst

In deze sectie leert u hoe u AdlCopy kunt gebruiken om gegevens uit een bron te kopiëren (in het onderstaande voor beeld gebruiken we Azure Storage Blob) naar een doel Data Lake Storage Gen1 account met behulp van joker tekens. U kunt bijvoorbeeld de volgende stappen gebruiken om alle bestanden met de extensie. CSV van de bron-BLOB naar de bestemming te kopiëren.

1. Open een opdracht prompt en navigeer naar de map waarin AdlCopy is geïnstalleerd `%HOMEPATH%\Documents\adlcopy` .
1. Voer de volgende opdracht uit om alle bestanden met de extensie *. CSV van een specifieke blob van de bron container naar een Data Lake Storage Gen1 map te kopiëren:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv
    ```

    Bijvoorbeeld:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv
    ```

## <a name="billing"></a>Billing

* Als u het AdlCopy-hulp programma als zelfstandig gebruikt, wordt u gefactureerd voor de kosten voor het verplaatsen van gegevens, als de bron Azure Storage account zich niet in dezelfde regio bevindt als het Data Lake Storage Gen1-account.
* Als u het AdlCopy-hulp programma gebruikt met uw Data Lake Analytics-account, zijn de standaard [Data Lake Analytics facturerings tarieven](https://azure.microsoft.com/pricing/details/data-lake-analytics/) van toepassing.

## <a name="considerations-for-using-adlcopy"></a>Overwegingen voor het gebruik van AdlCopy

* AdlCopy (voor versie 1.0.5) biedt ondersteuning voor het kopiëren van gegevens uit bronnen die collectief meer dan duizenden bestanden en mappen bevatten. Als u echter problemen ondervindt met het kopiëren van een grote gegevensset, kunt u de bestanden/mappen in verschillende submappen distribueren en het pad naar die submappen als bron gebruiken.

## <a name="performance-considerations-for-using-adlcopy"></a>Prestatie overwegingen voor het gebruik van AdlCopy

AdlCopy biedt ondersteuning voor het kopiëren van gegevens met duizenden bestanden en mappen. Als u echter problemen ondervindt met het kopiëren van een grote gegevensset, kunt u de bestanden/mappen naar kleinere submappen distribueren. AdlCopy is gebouwd voor ad-hoc kopieën. Als u gegevens op een terugkerende basis probeert te kopiëren, kunt u overwegen [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) te gebruiken die volledig beheer over de Kopieer bewerkingen biedt.

## <a name="release-notes"></a>Opmerkingen bij de release

* 1.0.13: als u gegevens naar hetzelfde Azure Data Lake Storage Gen1-account kopieert over meerdere adlcopy-opdrachten, hoeft u uw referenties niet meer opnieuw in te voeren voor elke uitvoering. Met Adlcopy wordt die informatie nu in een cache opgeslagen over meerdere uitvoeringen.

## <a name="next-steps"></a>Volgende stappen

* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
