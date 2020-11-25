---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 08458bd170707b28c69fdad1d8aa115a7ad245a5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95554037"
---
> [!NOTE]
> Met de opdracht `az webapp up` worden de volgende acties uitgevoerd:
>
>- Er wordt een standaard[resourcegroep](/cli/azure/group?view=azure-cli-latest#az-group-create) gemaakt.
>
>- Er wordt een standaard[serviceplan voor de app](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) gemaakt.
>
>- Er wordt een [app met de opgegeven naam](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) gemaakt.
>
>- Er worden via zip bestanden van de huidige werkmap naar de app [geïmplementeerd](../articles/app-service/deploy-zip.md).
>