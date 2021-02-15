---
title: Overzicht van Azure Data Factory-connector
description: Meer informatie over de ondersteunde connectors in Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: jingwang
ms.openlocfilehash: 9dff36704fc0e3163da0439d55452fa82cb40e55
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381304"
---
# <a name="azure-data-factory-connector-overview"></a>Overzicht van Azure Data Factory-connector

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory ondersteunt de volgende gegevens archieven en-indelingen via kopiëren, gegevens stroom, zoeken, ophalen van meta gegevens en delete-activiteiten. Klik op elk gegevens Archief voor meer informatie over de ondersteunde mogelijkheden en de bijbehorende configuraties.

## <a name="supported-data-stores"></a>Ondersteunde gegevensarchieven

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>Integreren met meer gegevens archieven

Azure Data Factory kunnen een grotere set gegevens archieven bereiken dan de bovenstaande lijst. Als u gegevens wilt verplaatsen naar/van een gegevens archief dat zich niet in de lijst met Azure Data Factory ingebouwde connectors bevindt, kunt u de volgende uitbreid bare opties volgen:
- Voor de data base en het Data Warehouse kunt u meestal een bijbehorend ODBC-stuur programma vinden, waarmee u [algemene ODBC-Connector](connector-odbc.md)kunt gebruiken.
- Voor SaaS-toepassingen:
    - Als er REST-Api's worden geboden, kunt u een [generieke rest-connector](connector-rest.md)gebruiken.
    - Als het OData-feed heeft, kunt u de [algemene OData-connector](connector-odata.md)gebruiken.
    - Als u SOAP-Api's hebt, kunt u de [algemene HTTP-connector](connector-http.md)gebruiken.
    - Als het ODBC-stuur programma heeft, kunt u de [algemene ODBC-Connector](connector-odbc.md)gebruiken.
- Controleer voor anderen of u gegevens kunt laden in of weer geven als een opgeslagen ADF-gegevens archief, bijvoorbeeld Azure Blob/file/FTP/SFTP/etc, en laat de ADF vervolgens van daaruit ophalen. U kunt het mechanisme voor het laden van aangepaste gegevens aanroepen via [Azure function](control-flow-azure-function-activity.md), [aangepaste activiteit](transform-data-using-dotnet-custom-activity.md), [Databricks](transform-data-databricks-notebook.md) / [HDInsight](transform-data-using-hadoop-hive.md), [webactiviteit](control-flow-web-activity.md), enzovoort.

## <a name="supported-file-formats"></a>Ondersteunde bestandsindelingen

Azure Data Factory ondersteunt de volgende bestandsindelingen. Raadpleeg elk artikel voor op indeling gebaseerde instellingen.

- [Avro-indeling](format-avro.md)
- [Binaire indeling](format-binary.md)
- [Common Data Model-indeling](format-common-data-model.md)
- [Tekstindeling met scheidingstekens](format-delimited-text.md)
- [Delta-indeling](format-delta.md)
- [Excel-indeling](format-excel.md)
- [JSON-indeling](format-json.md)
- [ORC-indeling](format-orc.md)
- [Parquet-indeling](format-parquet.md)
- [XML-indeling](format-xml.md)

## <a name="next-steps"></a>Volgende stappen

- [Kopieeractiviteit](copy-activity-overview.md)
- [Toewijzingsgegevensstroom](concepts-data-flow-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit ophalen van metagegevens](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)
