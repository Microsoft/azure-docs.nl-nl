---
title: Azure Data Lake Storage Gen2 overzicht in HDInsight
description: Overzicht van Data Lake Storage Gen2 in HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: e69838f18efc08d0f64dd9ea904f502617073a8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938854"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>Azure Data Lake Storage Gen2 overzicht in HDInsight

Azure Data Lake Storage Gen2 maakt kern functies van Azure Data Lake Storage Gen1 en integreert deze in Azure Blob-opslag. Deze functies omvatten een bestands systeem dat compatibel is met Hadoop, Azure Active Directory (Azure AD) en Acl's (toegangs beheer lijsten) op basis van POSIX. Met deze combi natie kunt u profiteren van de prestaties van Azure Data Lake Storage Gen1. Hoewel het beheer van de levens cyclus voor lagen en gegevens van Blob Storage ook gebruikt.

Zie [Inleiding tot Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)voor meer informatie over Azure data Lake Storage Gen2.

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Kern functionaliteit van Azure Data Lake Storage Gen2

* **Toegang die compatibel is met Hadoop:** In Azure Data Lake Storage Gen2 kunt u gegevens beheren en benaderen net zoals u dat zou doen met een Hadoop Distributed File System (HDFS). Het ABFS-stuur programma (Azure Blob File System) is beschikbaar in alle Apache Hadoop omgevingen, waaronder Azure HDInsight en Azure Databricks. Gebruik ABFS om toegang te krijgen tot gegevens die zijn opgeslagen in Data Lake Storage Gen2.

* **Een superset van POSIX-machtigingen:** Het beveiligings model voor Data Lake Gen2 ondersteunt ACL-en POSIX-machtigingen, samen met een extra granulariteit die specifiek is voor Data Lake Storage Gen2. Instellingen kunnen worden geconfigureerd via beheer hulpprogramma's of Frameworks als Apache Hive en Apache Spark.

* **Kosten effectiviteit:** Data Lake Storage Gen2 biedt goedkope opslag capaciteit en-trans acties. De levens cyclus van Azure Blob-opslag helpt de kosten te verlagen door de facturerings tarieven aan te passen naarmate de levens cyclus van gegevens verloopt.

* **Compatibiliteit met hulpprogram ma's, frameworks en apps voor Blob-opslag:** Data Lake Storage Gen2 blijft werken met een breed scala aan hulpprogram ma's, frameworks en toepassingen voor Blob Storage.

* **Geoptimaliseerd stuur programma:** Het ABFS-stuur programma is specifiek geoptimaliseerd voor big data Analytics. De bijbehorende REST-Api's worden opgehaald via het DFS-eind punt (Distributed File System), dfs.core.windows.net.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Wat is er nieuw voor Azure Data Lake Storage gen 2

### <a name="managed-identities-for-secure-file-access"></a>Beheerde identiteiten voor beveiligde bestands toegang

Azure HDInsight maakt gebruik van beheerde identiteiten voor het beveiligen van de cluster toegang tot bestanden in Azure Data Lake Storage Gen2. Beheerde identiteiten zijn een functie van Azure Active Directory die Azure-Services biedt met een set automatisch beheerde referenties. Deze referenties kunnen worden gebruikt om te verifiëren bij elke service die ondersteuning biedt voor Active Directory-verificatie. Voor het gebruik van beheerde identiteiten is het niet nodig om referenties op te slaan in code of configuratie bestanden.

Zie [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie.

### <a name="azure-blob-file-system-driver"></a>Azure Blob File System-stuur programma

Apache Hadoop toepassingen verwachten een systeem eigen gegevens te lezen uit en te schrijven naar de lokale schijf opslag. Met een Hadoop File System-stuur programma zoals ABFS kunnen Hadoop-toepassingen werken met Cloud opslag. Werkt door regel matige Hadoop-bestandssysteem bewerkingen te emuleren. Het stuur programma converteert die opdrachten die zijn ontvangen van de toepassing, naar bewerkingen die het daad werkelijke platform voor Cloud opslag begrijpt.

Voorheen heeft het Hadoop File System-stuur programma alle bestandssysteem bewerkingen geconverteerd naar Azure Storage REST API-aanroepen aan de client zijde. En vervolgens de REST API aangeroepen. Deze conversie aan de client zijde heeft echter geresulteerd in meerdere REST API-aanroepen voor één bestandssysteem bewerking, zoals het wijzigen van de naam van een bestand. ABFS heeft de Hadoop-bestandssysteem logica verplaatst van de client aan de server zijde. De Azure Data Lake Storage Gen2-API wordt nu parallel uitgevoerd met de BLOB API. Deze migratie verbetert de prestaties omdat u nu algemene Hadoop-bestandssysteem bewerkingen kunt uitvoeren met één REST API aanroep.

Zie voor meer informatie [het Azure Blob-bestandssysteem stuur programma (ABFS): een speciaal Azure Storage stuur programma voor Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>URI-schema voor Azure Data Lake Storage gen 2

Azure Data Lake Storage Gen2 gebruikt een nieuw URI-schema voor toegang tot bestanden in Azure Storage van HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Het URI-schema biedt met SSL versleutelde toegang.

`<FILE_SYSTEM_NAME>` Hiermee wordt het pad van het bestands systeem Data Lake Storage Gen2 geïdentificeerd.

`<ACCOUNT_NAME>` Hiermee wordt de naam van het Azure Storage-account aangeduid. Een FQDN (Fully Qualified Domain Name) is vereist.

`<PATH>` is de naam van het bestand of de map HDFS-pad.

Als waarden voor `<FILE_SYSTEM_NAME>` en `<ACCOUNT_NAME>` niet worden opgegeven, wordt het standaard bestandssysteem gebruikt. Voor de bestanden in het standaard bestandssysteem gebruikt u een relatief pad of een absoluut pad. U `hadoop-mapreduce-examples.jar` kunt bijvoorbeeld een van de volgende paden naar het bestand met HDInsight-clusters verwijzen:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> De bestands naam bevindt zich `hadoop-examples.jar` in de HDInsight-versies 2,1 en 1,6. Wanneer u werkt met bestanden buiten HDInsight, zult u zien dat de meeste hulpprogram ma's de ABFS-indeling niet herkennen, maar verwacht u in plaats daarvan een elementaire pad-indeling, zoals `example/jars/hadoop-mapreduce-examples.jar` .

Zie [de Azure data Lake Storage GEN2 URI gebruiken](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Kennismaking met Azure Storage](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage Gen1-overzicht](./overview-data-lake-storage-gen1.md)