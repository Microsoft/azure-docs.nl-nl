---
title: Een werkstroom verzenden met behulp van FASTQ-bestandsinvoeren
titleSuffix: Microsoft Genomics
description: In dit artikel wordt beschreven hoe u een werk stroom verzendt naar de Microsoft Genomics-service als uw invoer bestanden één paar FASTQ-bestanden zijn.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 3806b165e5abb661e53c6a315650d025fd42e17f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "72248543"
---
# <a name="submit-a-workflow-using-fastq-file-inputs-in-microsoft-genomics"></a>Een werkstroom verzenden met behulp van FASTQ-bestandsinvoer in Microsoft Genomics

In dit artikel wordt beschreven hoe u een werk stroom verzendt naar de Microsoft Genomics-service als uw invoer bestanden één paar FASTQ-bestanden zijn. In dit onderwerp wordt ervan uitgegaan dat u de `msgen`-client al hebt geïnstalleerd en uitgevoerd, en dat u bekend bent met het gebruik van Azure Storage. Als u een werk stroom met de opgegeven voorbeeld gegevens hebt verzonden, kunt u door gaan met dit artikel. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>Instellen: uw FASTQ-bestand uploaden naar Azure-opslag
Stel dat u twee bestanden hebt, *reads_1.fq.gz* en *reads_2.fq.gz*, en u ze hebt geüpload naar uw opslagaccount *myaccount* in Azure als **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/inputs/reads_1 <span></span>.fq <span></span>.gz <span></span>** en **https://<span></span>myaccount.blob.core.<span></span>windows <span></span>.net/<span></span>inputs/<span></span>reads_2.fq <span></span>.gz <span></span>**. U hebt de URL van de API en uw toegangssleutel. U wilt de uitvoer in **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/outputs <span></span>** hebben.


## <a name="submit-your-job-to-the-msgen-client"></a>De taak naar de `msgen`-client verzenden 

Dit is de minimale set argumenten die u dient op te geven voor de `msgen`-client; voor de duidelijkheid zijn regeleinden toegevoegd:

Voor Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fq.gz ^
  --input-blob-name-2 reads_2.fq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```

Voor Unix:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fq.gz \
  --input-blob-name-2 reads_2.fq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Als u liever een configuratiebestand gebruikt, is dit wat het zou moeten bevatten:

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz
input_blob_name_2:                reads_2.fq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Verzend het `config.txt`-bestand met deze aanroep: `msgen submit -f config.txt`

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u twee FASTQ-bestanden geüpload naar Azure Storage, en een werkstroom verzonden naar de Microsoft Genomics-service via de `msgen`-client voor Python. Zie onze [Veelgestelde vragen](frequently-asked-questions-genomics.md)voor meer informatie over het verzenden van werk stromen en andere opdrachten die u kunt gebruiken met de Microsoft Genomics-service. 
