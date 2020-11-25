---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 2474b8920c5387c7896b413f229c2f5b06cdafb1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011132"
---
| Eigenschap | Beschrijving |
|:--- |:---|
|**time** | De UTC-tijd (Universal Time Coordinated) wanneer de aanvraag is ontvangen door de opslag. Bijvoorbeeld: `2018/11/08 21:09:36.6900118`.|
|**resourceId** | De resource-ID van het opslag account. Bijvoorbeeld: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**category** | De categorie van de aangevraagde bewerking. Bijvoorbeeld: `StorageRead` , `StorageWrite` of `StorageDelete` .|
|**operationName** | Het type REST bewerking dat is uitgevoerd. <br> Zie [Opslaganalyse logboeken en status berichten](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)voor een volledige lijst met bewerkingen. |
|**operationVersion** | De versie van de opslag service die tijdens het maken van de aanvraag is opgegeven. Dit is gelijk aan de waarde van de **x-MS-version-** header. Bijvoorbeeld: `2017-04-17`.|
|**schemaVersion** | De schema versie van het logboek. Bijvoorbeeld: `1.0`.|
|**statuscode** | De HTTP-status code voor de aanvraag. Als de aanvraag wordt onderbroken, kan deze waarde worden ingesteld op `Unknown` . <br> Bijvoorbeeld: `206` |
|**statusText** | De status van de aangevraagde bewerking.  Zie [Opslaganalyse het onderwerp geregistreerde bewerkingen en status berichten](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)voor een volledige lijst met status berichten. In versie 2017-04-17 en hoger wordt het status bericht `ClientOtherError` niet gebruikt. In plaats daarvan bevat dit veld een fout code. Bijvoorbeeld: `SASSuccess`  |
|**durationMs** | De totale tijd, uitgedrukt in milliseconden, om de aangevraagde bewerking uit te voeren. Dit omvat de tijd voor het lezen van de inkomende aanvraag en het verzenden van het antwoord naar de aanvrager. Bijvoorbeeld: `12`.|
|**callerIpAddress** | Het IP-adres van de aanvrager, inclusief het poort nummer. Bijvoorbeeld: `192.100.0.102:4362`. |
|**correlationId** | De ID die wordt gebruikt voor het correleren van Logboeken tussen resources. Bijvoorbeeld: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**location** | De locatie van het opslag account. Bijvoorbeeld: `North Europe`. |
|**protocol**|Het protocol dat wordt gebruikt voor de bewerking. Bijvoorbeeld:, `HTTP` , `HTTPS` `SMB` of `NFS`|
| **URI** | Een uniform resource-id die wordt aangevraagd. |