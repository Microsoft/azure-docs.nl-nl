---
title: Fout tolerantie in Azure Data Factory Kopieer activiteit toevoegen door incompatibele rijen over te slaan
description: Meer informatie over het toevoegen van fout tolerantie in Azure Data Factory Kopieer activiteit door het overs laan van niet-compatibele rijen tijdens het kopiëren
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 10e4bedae5b7c429152a3503fff2cb2769d66eb5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377173"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>Fout tolerantie in Kopieer activiteit toevoegen door incompatibele rijen over te slaan

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](data-factory-copy-activity-fault-tolerance.md)
> * [Versie 2 (huidige versie)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [fout tolerantie in de Kopieer activiteit van Data Factory](../copy-activity-fault-tolerance.md).

Met Azure Data Factory [Kopieer activiteit](data-factory-data-movement-activities.md) kunt u op twee manieren incompatibele rijen verwerken bij het kopiëren van gegevens tussen de bron-en Sink-gegevens opslag:

- U kunt de Kopieer activiteit afbreken en mislukken wanneer incompatibele gegevens worden aangetroffen (standaard gedrag).
- U kunt door gaan met het kopiëren van alle gegevens door fout tolerantie toe te voegen en incompatibele gegevens rijen over te slaan. Daarnaast kunt u de niet-compatibele rijen in Azure Blob-opslag registreren. U kunt vervolgens het logboek controleren om de oorzaak van de fout te achterhalen, de gegevens op de gegevens bron op te lossen en de Kopieer activiteit opnieuw uit te voeren.

## <a name="supported-scenarios"></a>Ondersteunde scenario's
De Kopieer activiteit ondersteunt drie scenario's voor het detecteren, overs Laan en het vastleggen van incompatibele gegevens:

- **Incompatibiliteit tussen het gegevenstype van de bron en het systeemeigen gegevenstype van de sink**

    Bijvoorbeeld: gegevens uit een CSV-bestand in de Blob-opslag kopiëren naar een SQL database met een schema definitie die drie typen kolommen van het type **int** bevat. De rijen met het CSV-bestand die numerieke gegevens bevatten, zoals `123,456,789` worden gekopieerd naar het sink-archief. De rijen met niet-numerieke waarden, zoals `123,456,abc` gedetecteerd als incompatibel, worden echter overgeslagen.

- **Het aantal kolommen tussen de bron en de sink komt niet overeen**

    Bijvoorbeeld: gegevens uit een CSV-bestand in Blob Storage kopiëren naar een SQL database met een schema definitie die zes kolommen bevat. De rijen met het CSV-bestand die zes kolommen bevatten, worden naar de Sink-Store gekopieerd. De rijen met het CSV-bestand met meer of minder dan zes kolommen worden gedetecteerd als incompatibel en worden overgeslagen.

- **Schending van primaire sleutel bij het schrijven naar SQL Server/Azure SQL Database/Azure Cosmos DB**

    Bijvoorbeeld: gegevens kopiëren van een SQL-Server naar een SQL database. Er wordt een primaire sleutel gedefinieerd in de Sink-SQL database, maar er is geen dergelijke primaire sleutel gedefinieerd in de SQL-bron server. De dubbele rijen die aanwezig zijn in de bron, kunnen niet naar de Sink worden gekopieerd. Met de Kopieer activiteit wordt alleen de eerste rij van de bron gegevens naar de Sink gekopieerd. De volgende bron rijen die de dubbele primaire-sleutel waarde bevatten, worden gedetecteerd als incompatibel en worden overgeslagen.

>[!NOTE]
>Deze functie is niet van toepassing wanneer Kopieer activiteit is geconfigureerd voor het aanroepen van het mechanisme voor het laden van externe gegevens, waaronder [Azure Synapse Analytics poly base](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-synapse-analytics) of [Amazon Redshift Unload](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift). Voor het laden van gegevens in azure Synapse Analytics met poly Base, gebruikt u ondersteuning voor systeem eigen fout tolerantie van poly Base door '[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)' op te geven in de Kopieer activiteit.

## <a name="configuration"></a>Configuratie
In het volgende voor beeld wordt een JSON-definitie geboden voor het overs laan van de niet-compatibele rijen in een Kopieer activiteit:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Eigenschap | Beschrijving | Toegestane waarden | Vereist |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | Het overs laan van incompatibele rijen tijdens het kopiëren inschakelen of niet. | Waar<br/>False (standaard) | No |
| **redirectIncompatibleRowSettings** | Een groep eigenschappen die kan worden opgegeven wanneer u de niet-compatibele rijen wilt vastleggen. | &nbsp; | No |
| **linkedServiceName** | De gekoppelde service van Azure Storage om het logboek op te slaan dat de overgeslagen rijen bevat. | De naam van een gekoppelde [opslag](data-factory-azure-blob-connector.md#azure-storage-linked-service) -of [azurestoragesas zijn](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) -service, die verwijst naar het opslag exemplaar dat u wilt gebruiken om het logboek bestand op te slaan. | No |
| **path** | Het pad naar het logboek bestand dat de overgeslagen rijen bevat. | Geef het pad op naar de Blob-opslag die u wilt gebruiken om de niet-compatibele gegevens te registreren. Als u geen pad opgeeft, maakt de service een container voor u. | No |

## <a name="monitoring"></a>Bewaking
Nadat het uitvoeren van de Kopieer activiteit is voltooid, kunt u het aantal overgeslagen rijen bekijken in de sectie bewaking:

![Monitor heeft incompatibele rijen overgeslagen](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Als u configureert om de niet-compatibele rijen te registreren, kunt u het logboek bestand vinden op dit pad: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` in het logboek bestand ziet u de rijen die zijn overgeslagen en de hoofd oorzaak van de incompatibiliteit.

Zowel de oorspronkelijke gegevens als de bijbehorende fout worden geregistreerd in het bestand. Een voor beeld van de inhoud van het logboek bestand is als volgt:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Volgende stappen
Zie [gegevens verplaatsen met behulp van Kopieer activiteit](data-factory-data-movement-activities.md)voor meer informatie over Azure Data Factory Kopieer activiteit.
