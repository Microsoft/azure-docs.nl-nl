---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004335"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Wat gebeurt er met mijn app tijdens de implementatie?

Met alle officieel ondersteunde implementatie methoden worden wijzigingen aangebracht in de bestanden in de `/home/site/wwwroot` map van uw app. Deze bestanden worden gebruikt om uw app uit te voeren. Daarom kan de implementatie mislukken vanwege vergrendelde bestanden. De app kan ook onvoorspelbaar werken tijdens de implementatie, omdat niet alle bestanden tegelijk worden bijgewerkt. Dit is niet wenselijk voor een klant gerichte app. Er zijn een aantal verschillende manieren om deze problemen te voor komen:

- [Voer uw app rechtstreeks uit vanuit het zip-pakket](../articles/app-service/deploy-run-package.md) zonder dit uit te pakken.
- Stop uw app of schakel offline modus in voor uw app tijdens de implementatie. Zie voor meer informatie [omgaan met vergrendelde bestanden tijdens de implementatie](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Implementeren naar een [staging-sleuf](../articles/app-service/deploy-staging-slots.md) met [automatisch wisselen](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) ingeschakeld. 
