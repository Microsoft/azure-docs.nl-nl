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
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997902"
---
Als u toegang wilt tot de consolelogboeken die worden gegenereerd binnen uw toepassingscode in de App Service, schakelt u diagnostische logboekregistratie in door de volgende opdracht in de [Cloud Shell](https://shell.azure.com) uit te voeren:

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Mogelijk waarden voor `--level` zijn: `Error`, `Warning`, `Info` en `Verbose`. Elk hoger niveau omvat het vorige niveau. Bijvoorbeeld: `Error` omvat alleen foutberichten en `Verbose` omvat alle berichten.

Nadat diagnostische logboekregistratie is ingeschakeld, voert u de volgende opdracht uit om de logboekstream te zien:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Als u de consolelogboeken niet meteen ziet, probeert u het opnieuw na 30 seconden.

> [!NOTE]
> U kunt ook de logboekbestanden van de browser inspecteren op `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

U kunt op elk gewenst moment `Ctrl`+`C` typen om te stoppen met logboekstreaming.
