---
title: Azure Data Lake Storage Gen1 overzicht in HDInsight
description: Overzicht van Data Lake Storage Gen1 in HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 4ac1a48733d104e7674acbc13bfbb1e7a4cf02b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938880"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>Azure Data Lake Storage Gen1 overzicht in HDInsight

Azure Data Lake Storage Gen1 is een bedrijfsbrede grootschalige-opslag plaats voor big data analytische werk belastingen. Met Azure Data Lake kunt u gegevens van elke grootte, type en opname snelheid vastleggen. En op één plek voor operationele en experimentele analyses.

Toegang tot Data Lake Storage Gen1 vanuit Hadoop (beschikbaar met een HDInsight-cluster) met behulp van de WebHDFS-compatibele REST-Api's. Data Lake Storage Gen1 is ontworpen om analyses in te scha kelen voor de opgeslagen gegevens en is afgestemd op de prestaties in scenario's voor gegevens analyse. Gen1 bevat de mogelijkheden die essentieel zijn voor praktijk cases van het bedrijf. Tot deze mogelijkheden behoren beveiliging, beheer baarheid, aanpassing, betrouw baarheid en beschik baarheid.

Zie het gedetailleerde [overzicht van Azure data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md)voor meer informatie over Azure data Lake Storage gen1.

De belangrijkste mogelijkheden van Data Lake Storage Gen1 zijn:

## <a name="compatibility-with-hadoop"></a>Compatibiliteit met Hadoop

Data Lake Storage Gen1 is een Apache Hadoop bestands systeem dat compatibel is met HDFS-en Hadoop-omgeving.  HDInsight-toepassingen of-services die gebruikmaken van de WebHDFS-API kunnen eenvoudig worden geïntegreerd met Data Lake Storage Gen1. Data Lake Storage Gen1 bevat ook een met WebHDFS compatibele REST-interface voor toepassingen.

Gegevens die zijn opgeslagen in Data Lake Storage Gen1 kunnen eenvoudig worden geanalyseerd met behulp van Hadoop analytic-frameworks. Frameworks zoals MapReduce of Hive. Azure HDInsight-clusters kunnen worden ingericht en geconfigureerd om rechtstreeks toegang te krijgen tot gegevens die zijn opgeslagen in Data Lake Storage Gen1.

## <a name="unlimited-storage-petabyte-files"></a>Onbeperkte opslag, bestanden ter grootte van petabytes

Data Lake Storage Gen1 biedt onbeperkte opslag en is geschikt voor het opslaan van verschillende soorten gegevens voor analyse. Er gelden geen limieten voor account grootten of bestands grootten. Of de hoeveelheid gegevens die kan worden opgeslagen in een Data Lake. Afzonderlijke bestanden variëren van grootte van kilo bytes tot PETA bytes, waardoor Data Lake Storage Gen1 een fantastische keuze maakt om elk type gegevens op te slaan. Gegevens worden blijvend opgeslagen door er meerdere kopieën van te maken. En er zijn geen limieten voor hoe lang de gegevens kunnen worden opgeslagen in de data Lake.

## <a name="performance-tuning-for-big-data-analytics"></a>Prestaties afstemmen voor big data Analytics

Data Lake Storage Gen1 is ontworpen voor analyse systemen. Systemen die een enorme door Voer vereisen om grote hoeveel heden gegevens op te vragen en te analyseren. De data Lake breidt delen van een bestand uit op verschillende afzonderlijke opslag servers. Wanneer u gegevens analyseert, wordt de Lees doorvoer door deze setup verbeterd wanneer het bestand parallel wordt gelezen.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Gereed voor Enter prise: Maxi maal beschikbaar en veilig

Data Lake Storage Gen1 biedt beschikbaarheid en betrouwbaarheid conform industriestandaarden. Gegevensassets worden opgeslagen blijvend: de beveiliging van redundante kopieën tegen onverwachte fouten. Ondernemingen kunnen Data Lake Storage Gen1 in hun oplossingen gebruiken als een belang rijk onderdeel van hun bestaande gegevens platform.

Data Lake Storage Gen1 biedt ook beveiliging op ondernemings niveau voor opgeslagen gegevens. Zie voor meer informatie [Gegevens beveiligen in Azure Data Lake Storage Gen1](#data-security-in-data-lake-storage-gen1).

## <a name="flexible-data-structures"></a>Flexibele gegevens structuren

Data Lake Storage Gen1 kunnen alle gegevens in de oorspronkelijke indeling opslaan, op voor waarde dat dit zo is, zonder dat hiervoor eerdere trans formaties zijn vereist. Data Lake Storage Gen1 hoeft geen schema te worden gedefinieerd voordat de gegevens worden geladen. Het afzonderlijke analytische Framework interpreteert de gegevens en definieert een schema op het moment van de analyse. Data Lake Storage Gen1 kan gestructureerde gegevens verwerken. En semigestructureerde en ongestructureerde gegevens.

Containers voor gegevens van Data Lake Storage Gen1 zijn eigenlijk mappen en bestanden. U werkt met de opgeslagen gegevens met behulp van Sdk's, het Azure Portal en Azure PowerShell. Gegevens die in de Store worden geplaatst met deze interfaces en containers, kunnen elk gegevens type opslaan. Data Lake Storage Gen1 voert geen speciale verwerking van gegevens uit op basis van het gegevens type.

## <a name="data-security-in-data-lake-storage-gen1"></a>Gegevens beveiliging in Data Lake Storage Gen1

Data Lake Storage Gen1 gebruikt Azure Active Directory voor verificatie en maakt gebruik van toegangs beheer lijsten (Acl's) voor het beheren van de toegang tot uw gegevens.

| **Functie** | **Beschrijving** |
| --- | --- |
| Verificatie |Data Lake Storage Gen1 integreert met Azure Active Directory (Azure AD) voor identiteits-en toegangs beheer voor alle gegevens die zijn opgeslagen in Data Lake Storage Gen1. Vanwege de integratie Data Lake Storage Gen1 de voor delen van alle Azure AD-functies. Deze functies omvatten: multi-factor Authentication, voorwaardelijke toegang en op rollen gebaseerd toegangs beheer op basis van Azure. Ook bewaking van toepassings gebruik, beveiligings bewaking en waarschuwingen, enzovoort. Data Lake Storage Gen1 ondersteunt het OAuth 2.0-protocol voor verificatie in de REST-interface. Bekijk [verificatie binnen Azure data Lake Storage gen1 met behulp van Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Toegangsbeheer |Data Lake Storage Gen1 biedt toegangs beheer door de machtigingen voor POSIX-stijlen te ondersteunen die door het WebHDFS-protocol worden weer gegeven. ACL's kunnen worden ingeschakeld voor de hoofdmap, submappen en afzonderlijke bestanden. Zie [toegangs beheer in data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md)voor meer informatie over de werking van acl's in de context van data Lake Storage gen1. |
| Versleuteling |Data Lake Storage Gen1 biedt ook versleuteling voor gegevens die in het account zijn opgeslagen. U geeft de versleutelingsinstellingen op tijdens het maken van een Data Lake Storage Gen1-account. U kunt ervoor kiezen de gegevens te versleutelen of niet te versleutelen. Zie [Versleuteling in Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md) voor meer informatie. Zie [aan de slag met Azure data Lake Storage gen1 met behulp van de Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md)voor instructies over het bieden van een configuratie met betrekking tot versleuteling. |

Zie [gegevens beveiligen die zijn opgeslagen in azure data Lake Storage gen1](../data-lake-store/data-lake-store-secure-data.md)voor meer informatie over het beveiligen van gegevens in data Lake Storage gen1.

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Toepassingen die compatibel zijn met Data Lake Storage Gen1

Data Lake Storage Gen1 is compatibel met de meeste open source-onderdelen in de Hadoop-omgeving. Het kan ook goed worden geïntegreerd in andere Azure-services.  Volg de onderstaande koppelingen voor meer informatie over hoe Data Lake Storage Gen1 kan worden gebruikt met open source-onderdelen en andere Azure-Services.

* Zie [open-source Big Data-toepassingen die samen werken met Azure data Lake Storage gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md).
* Zie [Azure data Lake Storage gen1 integreren met andere Azure-Services](../data-lake-store/data-lake-store-integrate-with-other-services.md) om te begrijpen hoe u data Lake Storage gen1 met andere Azure-Services kunt gebruiken om een breder scala aan scenario's mogelijk te maken.
* Zie [Azure data Lake Storage gen1 gebruiken voor Big Data vereisten](../data-lake-store/data-lake-store-data-scenarios.md).

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1-bestands systeem (adl://)

In Hadoop-omgevingen kunt u toegang krijgen tot Data Lake Storage Gen1 via het nieuwe bestands systeem, de AzureDataLakeFilesystem (adl://). De prestaties van toepassingen en services die worden gebruikt, `adl://` kunnen worden geoptimaliseerd op manieren die momenteel niet beschikbaar zijn in WebHDFS. Als gevolg hiervan beschikt u over de flexibiliteit om de beste prestaties te behalen met behulp van de aanbevolen adl://. Of u kunt bestaande code hand haven door de WebHDFS-API rechtstreeks te gebruiken. Azure HDInsight maakt optimaal gebruik van de AzureDataLakeFilesystem om de beste prestaties op Data Lake Storage Gen1 te bieden.

Toegang tot uw gegevens in Data Lake Storage Gen1 met behulp van de volgende URI:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Zie [acties die beschikbaar zijn voor de opgeslagen gegevens](../data-lake-store/data-lake-store-get-started-portal.md#properties)voor meer informatie over het openen van de gegevens in data Lake Storage gen1.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Kennismaking met Azure Storage](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage Gen2-overzicht](./overview-data-lake-storage-gen2.md)