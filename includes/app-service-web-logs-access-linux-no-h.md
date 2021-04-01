---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: df71f0804b62eb4b17ff8d2f652b076b5c64c959
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "92743786"
---
U hebt vanuit de container toegang tot de consolelogboeken.

Schakel eerst logboekregistratie voor containers in door de volgende opdracht uit te voeren:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Vervang `<app-name>` en `<resource-group-name>` door de namen die van toepassing zijn op uw web-app.

Zodra logboekregistratie is ingeschakeld, voert u de volgende opdracht uit om de logboekstream te zien:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Als u de consolelogboeken niet meteen ziet, probeert u het opnieuw na 30 seconden.

U kunt op elk gewenst moment stoppen met logboekstreaming door **Ctrl**+**C** te typen.

U kunt ook de logboekbestanden in een browser inspecteren op `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
