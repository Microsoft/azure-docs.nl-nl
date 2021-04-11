---
title: Het Azure Blob-bestandssysteem stuur programma voor Azure Data Lake Storage Gen2
description: Meer informatie over het Azure Blob-bestandssysteem stuur programma (ABFS), een speciaal Azure Storage stuur programma voor Hadoop. Toegang tot gegevens in Azure Data Lake Storage Gen2 met dit stuur programma.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 54c6245ea5290e2cab0efcd52f2208ba874c0848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720440"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Het Azure Blob-bestandssysteem stuur programma (ABFS): een speciaal Azure Storage stuur programma voor Hadoop

Een van de primaire toegangs methoden voor gegevens in Azure Data Lake Storage Gen2 is via het [Hadoop-bestands systeem](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Met Data Lake Storage Gen2 kunnen gebruikers van Azure Blob Storage toegang krijgen tot een nieuw stuur programma, het Azure Blob File System-stuur programma of `ABFS` . ABFS maakt deel uit van Apache Hadoop en is opgenomen in een groot aantal commerciële distributies van Hadoop. Met dit stuur programma kunnen veel toepassingen en frameworks toegang krijgen tot gegevens in Azure Blob Storage zonder enige code die expliciet verwijst naar Data Lake Storage Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Eerdere mogelijkheid: het Windows Azure Storage Blob-stuur programma

De Windows Azure Storage Blob driver of het [WASB-stuur programma](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) bood de oorspronkelijke ondersteuning voor Azure Blob Storage. Dit stuur programma heeft de complexe taak voor het toewijzen van bestandssysteem semantiek (zoals vereist door de Hadoop-bestands systeem Interface) uitgevoerd aan die van de object Store Style interface die door Azure Blob Storage wordt weer gegeven. Dit stuur programma blijft ondersteuning bieden voor dit model en biedt hoge prestaties voor gegevens die zijn opgeslagen in blobs, maar bevat een aanzienlijke hoeveelheid code die deze toewijzing uitvoert, waardoor het lastig is om te onderhouden. Daarnaast moet het stuur programma, zoals [File System. Rename ()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) en [File System. Delete ()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) bij het Toep assen op directory's, een groot aantal bewerkingen uitvoeren (vanwege object archieven zonder ondersteuning voor directory's). Dit leidt vaak tot slechtere prestaties. Het ABFS-stuur programma is ontworpen om de inherente tekortkomingen van WASB te overwinnen.

## <a name="the-azure-blob-file-system-driver"></a>Het Azure Blob File System-stuur programma

De [Azure data Lake Storage rest-interface](/rest/api/storageservices/data-lake-storage-gen2) is ontworpen ter ondersteuning van de semantiek van bestands systemen via Azure Blob Storage. Gezien het Hadoop-bestands systeem is ook ontworpen om dezelfde semantiek te ondersteunen, is er geen vereiste voor een complexe toewijzing in het stuur programma. Het Azure Blob File System-stuur programma (of ABFS) is dus een louter client-Shim voor de REST API.

Er zijn echter enkele functies die het stuur programma nog moet uitvoeren:

### <a name="uri-scheme-to-reference-data"></a>URI-schema om te verwijzen naar gegevens

Consistent met andere bestandssysteem implementaties in Hadoop, het ABFS-stuur programma definieert een eigen URI-schema, zodat resources (directory's en bestanden) kunnen worden verholpen. Het URI-schema wordt beschreven in [de Azure data Lake Storage Gen2-URI gebruiken](./data-lake-storage-introduction-abfs-uri.md). De structuur van de URI is: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Met de bovenstaande URI-indeling kunnen standaard Hadoop-hulpprogram ma's en-frameworks worden gebruikt om te verwijzen naar deze bronnen:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/
```

Intern vertaalt het ABFS-stuur programma de resource (s) die zijn opgegeven in de URI naar bestanden en mappen, en maakt aanroepen van de Azure Data Lake Storage REST API met die verwijzingen.

### <a name="authentication"></a>Verificatie

Het ABFS-stuur programma ondersteunt twee vormen van verificatie, zodat de Hadoop-toepassing veilig toegang kan krijgen tot bronnen in een Data Lake Storage Gen2-account dat kan worden ondersteund. Volledige details van de beschik bare verificatie schema's vindt u in de [Azure Storage beveiligings handleiding](security-recommendations.md). Dit zijn:

- **Gedeelde sleutel:** Hierdoor kunnen gebruikers toegang krijgen tot alle resources in het account. De sleutel is versleuteld en opgeslagen in Hadoop-configuratie.

- **Azure Active Directory OAuth Bearer-token:** Azure AD Bearer-tokens worden verkregen en vernieuwd door het stuur programma met behulp van de identiteit van de eind gebruiker of een geconfigureerde service-principal. Met behulp van dit verificatie model wordt alle toegang per oproep geautoriseerd met behulp van de identiteit die is gekoppeld aan het opgegeven token en wordt geëvalueerd op basis van de toegewezen POSIX-Access Control lijst (ACL).

   > [!NOTE]
   > Azure Data Lake Storage Gen2 ondersteunt alleen Azure AD v 1.0-eind punten.

### <a name="configuration"></a>Configuratie

Alle configuratie voor het ABFS-stuur programma wordt opgeslagen in het <code>core-site.xml</code> configuratie bestand. Bij Hadoop-distributies met [Ambari](https://ambari.apache.org/)kan de configuratie ook worden beheerd met de webportal of Ambari rest API.

Details van alle ondersteunde configuratie vermeldingen zijn opgegeven in de [officiële Hadoop-documentatie](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html).

### <a name="hadoop-documentation"></a>Hadoop-documentatie

Het ABFS-stuur programma is volledig gedocumenteerd in de [officiële Hadoop-documentatie](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)

## <a name="next-steps"></a>Volgende stappen

- [Een Azure Databricks-cluster maken](./data-lake-storage-use-databricks-spark.md)
- [URI van Azure Data Lake Storage Gen2 gebruiken](./data-lake-storage-introduction-abfs-uri.md)