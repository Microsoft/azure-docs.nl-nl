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
ms.openlocfilehash: d285b48606485fbd7f53511a15be1e2a31791829
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244797"
---
> [!NOTE]
> Met de opdracht `az webapp up` worden de volgende acties uitgevoerd:
>
>- Er wordt een standaard[resourcegroep](/cli/azure/group#az-group-create) gemaakt.
>
>- Er wordt een standaard[serviceplan voor de app](/cli/azure/appservice/plan#az-appservice-plan-create) gemaakt.
>
>- Er wordt een [app met de opgegeven naam](/cli/azure/webapp#az-webapp-create) gemaakt.
>
>- Er worden via zip bestanden van de huidige werkmap naar de app [geïmplementeerd](../articles/app-service/deploy-zip.md).
>