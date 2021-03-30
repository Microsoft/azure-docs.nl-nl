---
title: Azure Storage-voorbeelden met behulp van Java | Microsoft Docs
description: Lees hier hoe u voorbeeldcode en -toepassingen voor Azure Storage kunt weergeven, downloaden en uitvoeren. Ga aan de slag met voorbeelden voor blobs, wachtrijen, tabellen en bestanden, met behulp van de clientbibliotheken van Storage voor Java.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 10/01/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 9e3cd6be0da7c6d9401945293283de874c7cd866
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92359761"
---
# <a name="azure-storage-samples-using-v12-java-client-libraries"></a>Azure Storage-voorbeelden met behulp van v12 van de Java-clientbibliotheken

De volgende tabel geeft een overzicht van onze opslagplaats met voorbeelden en de scenario's die in elk voorbeeld worden behandeld. Klik op de koppelingen om de bijbehorende voorbeeldcode weer te geven in GitHub.

> [!NOTE]
> In deze voorbeelden wordt de nieuwste Azure Storage Java v12-bibliotheek gebruikt. Zie [Aan de slag met Azure Blob-service in Java](https://github.com/Azure-Samples/storage-blob-java-getting-started) in de GitHub-opslagplaats voor informatie over verouderde v8-code.

## <a name="blob-samples"></a>Blob-voorbeelden

### <a name="authentication"></a>Verificatie

:::row:::
   :::column span="":::
      [Verifiëren met een gedeelde sleutelreferentie](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L38)
   :::column-end:::
   :::column span="":::
      [Verifiëren met Azure Identity](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/AzureIdentityExample.java#L10)
   :::column-end:::
:::row-end:::

### <a name="blob-service"></a>Blob-service

:::row:::
   :::column span="":::
      [Een blob-serviceclient maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L49)
   :::column-end:::
   :::column span="":::
      [Containers weergeven](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/ListContainersExample.java#L10)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Containers verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/ListContainersExample.java#L52)
   :::column-end:::
:::row-end:::

### <a name="batching"></a>Batchverwerking

:::row:::
   :::column span="":::
      [Een blob-batchclient maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob-batch/src/samples/java/com/azure/storage/blob/batch/ReadmeSamples.java#L41)
   :::column-end:::
   :::column span="":::
      [Blobs bulksgewijs verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob-batch/src/samples/java/com/azure/storage/blob/batch/ReadmeSamples.java#L45)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Toegangslaag instellen voor een batch blobs](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob-batch/src/samples/java/com/azure/storage/blob/batch/ReadmeSamples.java#L51)
   :::column-end:::
:::row-end:::

### <a name="container"></a>Container

:::row:::
   :::column span="":::
      [Een containerclient maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L57)
   :::column-end:::
   :::column span="":::
      [Een container maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L64)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Blobs vermelden](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L112)
   :::column-end:::
   :::column span="":::
      [Container verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L123)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Blob

:::row:::
   :::column span="":::
      [Blob uploaden](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L79)
   :::column-end:::
   :::column span="":::
      [Blob downloaden](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L86)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een blob verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/BasicExample.java#L118)
   :::column-end:::
   :::column span="":::
      [Een blob uploaden uit een groot bestand](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/FileTransferExample.java#L95)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Een grote blob downloaden naar een bestand](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/FileTransferExample.java#L100)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Problemen oplossen
:::row:::
   :::column span="2":::
      [Een herstelbare fout activeren met een containerclient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob/StorageErrorHandlingExample.java#L11)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Voorbeelden van Data Lake Storage Gen2

### <a name="data-lake-service"></a>Data Lake-service
:::row:::
   :::column span="":::
      [Een Data Lake-serviceclient maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L48)
   :::column-end:::
   :::column span="":::
      [Een bestandssysteemclient maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L57)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>Bestandssysteem
:::row:::
   :::column span="":::
      [Een bestandssysteem maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L64)
   :::column-end:::
   :::column span="":::
      [Een map maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een bestand en submap maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L73)
   :::column-end:::
   :::column span="":::
      [Een bestandsclient maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L83)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Paden weergeven in een bestandssysteem](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L131)
   :::column-end:::
   :::column span="":::
      [Een bestandssysteem verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L142)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Bestandssystemen weergeven in een Azure-opslagaccount](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/ListFileSystemsExample.java#L10)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Directory

:::row:::
   :::column span="":::
      [Een mapclient maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L31)
   :::column-end:::
   :::column span="":::
      [Een bovenliggende map maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L37)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een onderliggende map maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L44)
   :::column-end:::
   :::column span="":::
      [Een bestand in een onderliggende map maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L52)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Directoryeigenschappen ophalen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L68)
   :::column-end:::
   :::column span="":::
      [Een onderliggende map verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L83)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Een bovenliggende map verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/DirectoryExample.java#L90)
   :::column-end:::
:::row-end:::

### <a name="file"></a>File

:::row:::
   :::column span="":::
      [Een bestand maken met een bestandsclient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L93)
   :::column-end:::
   :::column span="":::
      [Een bestand verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/BasicExample.java#L137)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Toegangsbeheer instellen voor een bestand](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/GetSetAccessControlExample.java#L82)
   :::column-end:::
   :::column span="":::
      [Toegangsbeheer ophalen voor een bestand](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake/GetSetAccessControlExample.java#L104)
   :::column-end:::
:::row-end:::


## <a name="azure-file-samples"></a>Azure File-voorbeelden

### <a name="authentication"></a>Verificatie

:::row:::
   :::column span="2":::
      [Verifiëren met een verbindingsreeks](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L27)
   :::column-end:::
:::row-end:::

### <a name="file-service"></a>Bestandsservice

:::row:::
   :::column span="":::
      [Bestandsshares maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L31)
   :::column-end:::
   :::column span="":::
      [Eigenschappen ophalen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L40)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Shares weergeven](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L49)
   :::column-end:::
   :::column span="":::
      [Shares verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareServiceSample.java#L49)
   :::column-end:::
:::row-end:::

### <a name="file-share"></a>Bestandsshare

:::row:::
   :::column span="":::
      [Een shareclient maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L29)
   :::column-end:::
   :::column span="":::
      [Een share maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L40)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een momentopname van de share maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L55)
   :::column-end:::
   :::column span="":::
      [Een map maken met een shareclient](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L63)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Eigenschappen van een share ophalen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L72)
   :::column-end:::
   :::column span="":::
      [Hoofdmap ophalen en mappen weergeven](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L100)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Een share verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/ShareSample.java#L151)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Directory

:::row:::
   :::column span="":::
      [Een bovenliggende map maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L35)
   :::column-end:::
   :::column span="":::
      [Een onderliggende map maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L42)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een bestand in een onderliggende map maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L50)
   :::column-end:::
   :::column span="":::
      [Mappen en bestanden weergeven](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een onderliggende map verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L90)
   :::column-end:::
   :::column span="":::
      [Een bovenliggende map verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/DirectorySample.java#L97)
   :::column-end:::
:::row-end:::

### <a name="file"></a>File

:::row:::
   :::column span="":::
      [Een bestandsclient maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L45)
   :::column-end:::
   :::column span="":::
      [Bestand uploaden](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een bestand downloaden](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L100)
   :::column-end:::
   :::column span="":::
      [Bestandseigenschappen ophalen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L120)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Een bestand verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share/FileSample.java#L128)
   :::column-end:::
:::row-end:::


## <a name="queue-samples"></a>Wachtrijvoorbeelden

### <a name="authentication"></a>Verificatie

:::row:::
   :::column span="2":::
      [Verifiëren met een SAS-token](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L17)
   :::column-end:::
:::row-end:::

### <a name="queue-service"></a>Queue-service

:::row:::
   :::column span="":::
      [Een wachtrij maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L20)
   :::column-end:::
   :::column span="":::
      [Wachtrijen weergeven](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L22)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Wachtrijen verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/QueueServiceSamples.java#L27)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>Wachtrij

:::row:::
   :::column span="":::
      [Een wachtrijclient maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L25)
   :::column-end:::
   :::column span="":::
      [Berichten aan een wachtrij toevoegen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L27)
   :::column-end:::
:::row-end:::

### <a name="message"></a>Bericht

:::row:::
   :::column span="":::
      [Het aantal berichten ophalen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L34)
   :::column-end:::
   :::column span="":::
      [Berichten bekijken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L37)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Berichten ontvangen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L41)
   :::column-end:::
   :::column span="":::
      [Een bericht bijwerken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L45)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Het eerste bericht verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L50)
   :::column-end:::
   :::column span="":::
      [Alle berichten wissen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L59)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Een wachtrij verwijderen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue/MessageSamples.java#L64)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>Tabelvoorbeelden (v11)

:::row:::
   :::column span="":::
      [Tabel maken](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L50)
   :::column-end:::
   :::column span="":::
      [Entiteit/tabel verwijderen](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L109)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Entiteit invoegen/samenvoegen/vervangen](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L195)
   :::column-end:::
   :::column span="":::
      [Query uitvoeren op entiteiten](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L234)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Query uitvoeren op tabellen](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L262)
   :::column-end:::
   :::column span="":::
      [ACL/eigenschappen van tabel](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java#L49)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Entiteit bijwerken](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java#L76)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Bibliotheken met codevoorbeelden van Azure

Als u de complete bibliotheken met Java-voorbeelden wilt bekijken, gaat u naar:

* [Codevoorbeelden van Azure Blob](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)
* [Codevoorbeelden van Azure Data Lake](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/src/samples/java/com/azure/storage/file/datalake)
* [Codevoorbeelden van Azure Files](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-share/src/samples/java/com/azure/storage/file/share)
* [Codevoorbeelden van Azure Queue](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue)

U kunt de GitHub-opslagplaats voor elke bibliotheek bekijken en klonen.

## <a name="getting-started-guides"></a>Introductiehandleidingen

Bekijk de volgende handleidingen als u op zoek bent naar instructies voor het installeren van de Azure Storage-clientbibliotheken en voor instructies om aan de slag te gaan.

* [Aan de slag met Azure Blob-service in Java](../blobs/storage-quickstart-blobs-java.md)
* [Aan de slag met Azure Queue-service in Java](../queues/storage-quickstart-queues-java.md)
* [Aan de slag met de Azure-tabelservice in Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Aan de slag met Azure File-service in Java](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>Volgende stappen

Voor informatie over voorbeelden voor andere talen:

* .NET: [Azure Storage-voorbeelden met behulp van .NET](storage-samples-dotnet.md)
* Python: [Azure Storage-voorbeelden met behulp van Python](storage-samples-python.md)
* JavaScript/Node.js: [Azure Storage-voorbeelden met behulp van JavaScript](storage-samples-javascript.md)
* C++: [Azure Storage-voorbeelden met behulp van C++](storage-samples-c-plus-plus.md)
* Alle andere talen: [Voorbeelden van Azure Storage](storage-samples.md)
