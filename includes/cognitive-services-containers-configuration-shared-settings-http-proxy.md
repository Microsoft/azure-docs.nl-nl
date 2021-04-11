---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7f51b7fe95445cbd3a8aff530f89ce55b5abfb1e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630151"
---
Als u een HTTP-proxy wilt configureren voor het maken van uitgaande aanvragen, gebruikt u deze twee argumenten:

| Name | Gegevenstype | Beschrijving |
|--|--|--|
|HTTP_PROXY|tekenreeks|De proxy die moet worden gebruikt, bijvoorbeeld `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|tekenreeks|Alle referenties die nodig zijn voor verificatie bij de proxy, bijvoorbeeld `username:password` . Deze waarde **moet in kleine letters worden gereduceerd**. |
|`<proxy-user>`|tekenreeks|De gebruiker voor de proxy.|
|`<proxy-password>`|tekenreeks|Het wacht woord dat is gekoppeld aan `<proxy-user>` de proxy.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
