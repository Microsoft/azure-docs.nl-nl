---
title: Problemen met latentie oplossen met behulp van logboeken voor Opslaganalyse
description: U kunt latentie problemen identificeren en oplossen met behulp van Azure Storage analytic-logboeken en de client toepassing optimaliseren.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 1e6033f9a8f4cecd2429eca67a3d58e54d7ae1f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99221105"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Problemen met latentie oplossen met behulp van logboeken voor Opslaganalyse

Diagnose en probleem oplossing is een belang rijke vaardigheid voor het maken en ondersteunen van client toepassingen met Azure Storage.

Als gevolg van de gedistribueerde aard van een Azure-toepassing, is het diagnosticeren en oplossen van fouten en prestatie problemen mogelijk ingewik kelder dan in traditionele omgevingen.

De volgende stappen laten zien hoe u latentie problemen kunt identificeren en oplossen met behulp van Azure Storage analytic-logboeken en de client toepassing kunt optimaliseren.

## <a name="recommended-steps"></a>Aanbevolen stappen

1. Down load de [Opslaganalyse-logboeken](./manage-storage-analytics-logs.md#download-storage-logging-log-data).

2. Gebruik het volgende Power shell-script om de RAW-indelings logboeken te converteren naar tabellaire indeling:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. Met het script wordt een GUI-venster gestart waarin u de informatie op kolommen kunt filteren, zoals hieronder wordt weer gegeven.

   ![Venster opslag analyse logboek parser](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Verfijn de logboek vermeldingen op basis van het ' bewerkings type ' en zoek de logboek vermelding die tijdens de tijds periode van het probleem is gemaakt.

   ![Logboek vermeldingen van het type bewerking](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. Tijdens het tijdstip waarop het probleem is opgetreden, zijn de volgende waarden belang rijk:

   * Bewerking-type = GetBlob
   * aanvraag-status = SASNetworkError
   * End-to-end-latentie-in-MS = 8453
   * Server-latentie-in-MS = 391

   End-to-end-latentie wordt berekend met behulp van de volgende vergelijking:

   * End-to-end latentie = Server-Latency + client latentie

   De latentie van de client berekenen met behulp van de logboek vermelding:

   * Client latentie = end-to-end latentie – Server-Latency

        Voor beeld: 8453 – 391 = 8062ms

   De volgende tabel bevat informatie over de OperationType-en RequestStatus-resultaten met een hoge latentie:

   | BLOB-type |RequestStatus =<br>Geslaagd|RequestStatus =<br>GEBASEERD NetworkError|Aanbeveling|
   |---|---|---|---|
   |GetBlob|Ja|Nee|[**GetBlob-bewerking:** RequestStatus = geslaagd](#getblob-operation-requeststatus--success)|
   |GetBlob|Nee|Ja|[**GetBlob-bewerking:** RequestStatus = (SAS) NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Ja|Nee|[**Put-bewerking:** RequestStatus = geslaagd](#put-operation-requeststatus--success)|
   |PutBlob|Nee|Ja|[**Put-bewerking:** RequestStatus = (SAS) NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Status resultaten

### <a name="getblob-operation-requeststatus--success"></a>GetBlob-bewerking: RequestStatus = geslaagd

Controleer de volgende waarden zoals vermeld in stap 5 van de sectie ' aanbevolen stappen ':

* End-to-end-latentie
* Server-Latency
* Client-Latency

In een **GetBlob-bewerking** met **RequestStatus = geslaagd**, **wordt met** deze waarde aangegeven dat Azure Storage een grote hoeveelheid tijd besteedt aan het schrijven van gegevens naar de client. Deze vertraging duidt op een Client-Side probleem.

**Advies**

* Onderzoek de code in uw client.
* Gebruik wireshark, micro soft Message Analyzer of Tcping voor het onderzoeken van problemen met de netwerk verbinding van de client. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob-bewerking: RequestStatus = (SAS) NetworkError

Controleer de volgende waarden zoals vermeld in stap 5 van de sectie ' aanbevolen stappen ':

* End-to-end-latentie
* Server-Latency
* Client-Latency

In een **GetBlob-bewerking** met **REQUESTSTATUS = (SAS) NetworkError**, als de **maximale tijd** wordt besteed aan **client latentie**, is het meest voorkomende probleem dat de client de verbinding verbreekt voordat een time-out in de opslag service verloopt.

**Advies**

* Onderzoek de code in uw client om te begrijpen waarom en wanneer de client de verbinding met de opslag service verbreekt.
* Gebruik wireshark, micro soft Message Analyzer of Tcping voor het onderzoeken van problemen met de netwerk verbinding van de client. 

### <a name="put-operation-requeststatus--success"></a>Put-bewerking: RequestStatus = geslaagd

Controleer de volgende waarden zoals vermeld in stap 5 van de sectie ' aanbevolen stappen ':

* End-to-end-latentie
* Server-Latency
* Client-Latency

In een **put-bewerking** met **RequestStatus = geslaagd**, als de **maximale tijd** wordt besteed aan de **client latentie**, geeft dit aan dat de client meer tijd neemt om gegevens te verzenden naar de Azure Storage. Deze vertraging duidt op een Client-Side probleem.

**Advies**

* Onderzoek de code in uw client.
* Gebruik wireshark, micro soft Message Analyzer of Tcping voor het onderzoeken van problemen met de netwerk verbinding van de client. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Put-bewerking: RequestStatus = (SAS) NetworkError

Controleer de volgende waarden zoals vermeld in stap 5 van de sectie ' aanbevolen stappen ':

* End-to-end-latentie
* Server-Latency
* Client-Latency

In een **PutBlob-bewerking** met **REQUESTSTATUS = (SAS) NetworkError**, als de **maximale tijd** wordt besteed aan **client latentie**, is het meest voorkomende probleem dat de client de verbinding verbreekt voordat een time-out in de opslag service verloopt.

**Advies**

* Onderzoek de code in uw client om te begrijpen waarom en wanneer de client de verbinding met de opslag service verbreekt.
* Gebruik wireshark, micro soft Message Analyzer of Tcping voor het onderzoeken van problemen met de netwerk verbinding van de client.