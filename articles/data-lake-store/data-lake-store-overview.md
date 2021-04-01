---
title: Wat is Azure Data Lake Storage Gen1 | Microsoft Docs
description: Overzicht van Data Lake Storage Gen1 (voorheen bekend als Azure Data Lake Store) en wat de meerwaarde is ten opzichte van andere gegevensarchieven
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: overview
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 52c00359fb4e759d9517dee958e31c00feb2b33e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92149243"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Wat is Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 is een ondernemingsbrede opslagplaats op hyperschaal voor analytische workloads van big data. Met Azure Data Lake kunt u gegevens van elke grootte, type en opnamesnelheid vastleggen op één enkele locatie voor operationele en experimentele analyses.

Data Lake Storage Gen1 kan worden geopend via Hadoop (beschikbaar met HDInsight-cluster) met behulp van de met WebHDFS compatibele REST-API's. Het is ontworpen om de opgeslagen gegevens te kunnen analyseren en het is afgestemd op prestaties voor scenario's met gegevensanalyses. Data Lake Storage Gen1 bevat alle mogelijkheden op bedrijfsniveau: beveiliging, beheerbaarheid, schaalbaarheid, betrouwbaarheid en beschikbaarheid.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

De belangrijkste mogelijkheden van Data Lake Storage Gen1 zijn onder andere de volgende.

### <a name="built-for-hadoop"></a>Gebouwd voor Hadoop

Data Lake Storage Gen1 is een Apache Hadoop-bestandssysteem dat compatibel is met Hadoop Distributed File System (HDFS) en samenwerkt met het Hadoop-ecosysteem. Uw bestaande toepassingen of services van HDInsight die gebruikmaken van de API WebHDFS kunnen eenvoudig worden geïntegreerd met Data Lake Storage Gen1. Data Lake Storage Gen1 bevat ook een met WebHDFS compatibele REST-interface voor toepassingen.

Gegevens die zijn opgeslagen in Data Lake Storage Gen1 kunt u eenvoudig analyseren met analytische frameworks van Hadoop zoals MapReduce of Hive. U kunt Azure HDInsight-clusters inrichten en configureren voor directe toegang tot gegevens die zijn opgeslagen in Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Onbeperkte opslag, bestanden ter grootte van petabytes

Data Lake Storage Gen1 biedt onbeperkte opslag en is geschikt voor het opslaan van een verscheidenheid aan gegevens voor analyses. Het legt geen limieten op voor de grootte van accounts of bestanden, of de hoeveelheid gegevens die kunnen worden opgeslagen in een data lake. Afzonderlijke bestanden kunnen in grootte variëren van kilobytes tot petabytes. Gegevens worden blijvend opgeslagen door er meerdere kopieën van te maken. Er is geen limiet voor de tijdsduur waarin de gegevens kunnen worden opgeslagen in de data lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Prestaties zijn afgestemd op big data-analyses

Data Lake Storage Gen1 is gebouwd voor het uitvoeren van grootschalige analytische systemen waarvoor grote doorvoer is vereist om query's op grote hoeveelheden gegevens uit te voeren en deze te analyseren. De Data Lake verspreidt delen van een bestand over een aantal afzonderlijke opslagservers. Hiermee verbetert u de doorvoer wanneer het bestand in parallel wordt gelezen voor het uitvoeren van gegevensanalyse.

### <a name="enterprise-ready-highly-available-and-secure"></a>Geschikt voor gebruik door bedrijven: Hoge beschikbaarheid en beveiligd

Data Lake Storage Gen1 biedt beschikbaarheid en betrouwbaarheid conform industriestandaarden. Uw gegevensassets worden blijvend opgeslagen door het maken van redundante exemplaren ter bescherming tegen onverwachte fouten.

Data Lake Storage Gen1 biedt ook beveiliging op bedrijfsniveau voor de opgeslagen gegevens. Zie voor meer informatie [Gegevens beveiligen in Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Alle gegevens

In Data Lake Storage Gen1 kunnen alle gegevens worden opgeslagen in de systeemeigen indeling in de huidige vorm, en het is niet nodig om de gegevens eerst om te zetten. In Data Lake Storage Gen1 hoeft geen schema te worden gedefinieerd voordat de gegevens worden geladen. Daardoor kan het afzonderlijke analytische framework de gegevens interpreteren en een schema definiëren op het moment van de analyse. Doordat bestanden van verschillende groottes en indelingen kunnen worden opgeslagen, kan Data Lake Storage Gen1 gestructureerde, semi-gestructureerde en ongestructureerde gegevens verwerken.

Containers voor gegevens van Data Lake Storage Gen1 zijn eigenlijk mappen en bestanden. U werkt met de opgeslagen gegevens met behulp van SDK's, Azure Portal en Azure Powershell. Als u uw gegevens in het archief plaats via deze interfaces en de juiste containers gebruikt, kunt u allerlei soorten gegevens opslaan. Data Lake Storage Gen1 voert geen speciale verwerking van gegevens uit op basis van het type gegevens dat wordt opgeslagen.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Gegevens beveiligen

Data Lake Storage Gen1 maakt gebruik van Azure Active Directory (Azure AD) voor verificatie en toegangsbeheerlijsten (ACL’s) om toegang tot uw gegevens te beheren.

| Functie | Beschrijving |
| --- | --- |
| Verificatie |Data Lake Storage Gen1 integreert met Azure AD voor identiteits- en toegangsbeheer voor alle gegevens die zijn opgeslagen in Data Lake Storage Gen1. Door die integratie profiteert Data Lake Storage Gen1 van alle Azure AD-functies, zoals meervoudige verificatie, voorwaardelijke toegang, op rollen gebaseerd toegangsbeheer van Azure, bewaking van het gebruik van toepassingen, beveiligingsbewaking en waarschuwingen enzovoort. Data Lake Storage Gen1 ondersteunt het OAuth 2.0-protocol voor verificatie in de REST-interface. Zie [Data Lake Storage Gen1-verificatie](data-lakes-store-authentication-using-azure-active-directory.md).|
| Toegangsbeheer |Data Lake Storage Gen1 biedt toegangsbeheer door ondersteuning te bieden voor POSIX-machtigingen die beschikbaar worden gemaakt door het protocol WebHDFS. U kunt ACL's inschakelen voor de hoofdmap, submappen en afzonderlijke bestanden. Zie voor meer informatie over de werking van ACL's in de context van Data Lake Storage Gen1 [Toegangsbeheer in Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Versleuteling |Data Lake Storage Gen1 biedt ook versleuteling voor gegevens die zijn opgeslagen in het account. U geeft de versleutelingsinstellingen op tijdens het maken van een Data Lake Storage Gen1-account. U kunt ervoor kiezen de gegevens te versleutelen of niet te versleutelen. Zie [Versleuteling in Data Lake Storage Gen1](data-lake-store-encryption.md) voor meer informatie. Voor instructies voor het bieden van versleuteling-gerelateerde configuratie raadpleegt u [Aan de slag met Data Lake Storage Gen1 via Azure Portal](data-lake-store-get-started-portal.md). |

Zie [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md) voor instructies over het beveiligen van gegevens in Data Lake Storage Gen1.

## <a name="application-compatibility"></a>Compatibiliteit van toepassingen

Data Lake Storage Gen1 is compatibel met de meeste open source-onderdelen van het Hadoop-ecosysteem. Het kan ook goed worden geïntegreerd in andere Azure-services. Gebruik de volgende koppelingen voor meer informatie over hoe u Data Lake Storage Gen1 kunt gebruiken met open source-onderdelen en andere Azure-Services:

- Zie [Toepassingen en services die compatibel zijn met Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md) voor een lijst met open-sourcetoepassingen die compatibel zijn met Data Lake Storage Gen1.
- Zie [Integreren met andere Azure-services](data-lake-store-integrate-with-other-services.md) om te begrijpen hoe Data Lake Storage Gen1 samen met andere Azure-services kan worden gebruikt voor een breed scala aan scenario's.
- Zie [Scenario's voor het gebruik van Data Lake Storage Gen1](data-lake-store-data-scenarios.md) voor informatie over het gebruik van Data Lake Storage Gen1 in scenario's zoals het opnemen, verwerken, downloaden en visualiseren van gegevens.

## <a name="data-lake-storage-gen1-file-system"></a>Data Lake Storage Gen1-bestandssysteem

Data Lake Storage Gen1 is toegankelijk via het nieuwe bestandssysteem AzureDataLakeFilesystem (adl://) in Hadoop-omgevingen (beschikbaar met HDInsight-cluster). Toepassingen en services die gebruikmaken van adl:// kunnen profiteren van verdere optimalisatie van prestaties die op dit moment niet beschikbaar zijn in WebHDFS. Als gevolg hiervan geeft Data Lake Storage Gen1 u de flexibiliteit om de beste prestaties te behalen met de aanbevolen optie adl:// of bestaande code te beheren door de API van WebHDFS rechtstreeks te blijven gebruiken. Azure HDInsight maakt volledig gebruik van AzureDataLakeFilesystem om de beste prestaties te leveren op Data Lake Storage Gen1.

U kunt toegang krijgen tot uw gegevens in Data Lake Storage Gen1 met behulp van `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Zie [Eigenschappen van de opgeslagen gegevens weergeven](data-lake-store-get-started-portal.md#properties) voor meer informatie over toegang krijgen tot de gegevens in Data Lake Storage Gen1.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met Data Lake Storage Gen1 met behulp van Azure Portal](data-lake-store-get-started-portal.md)
- [Aan de slag met Data Lake Storage Gen1 met behulp van .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)