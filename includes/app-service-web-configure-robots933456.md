---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "78255827"
---
## <a name="robots933456-in-logs"></a>robots933456 in logboeken

U ziet mogelijk het volgende bericht in de containerlogboeken:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

U kunt dit bericht veilig negeren. `/robots933456.txt` is een dummy-URL-pad dat App Service gebruikt om te controleren of de container aanvragen kan verwerken. Een 404-antwoord geeft alleen aan dat het pad niet bestaat, maar laat App Service wel weten dat de container in orde is en klaar is om te reageren op aanvragen.

