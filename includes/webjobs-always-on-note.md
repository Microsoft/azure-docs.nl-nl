---
title: bestand opnemen
description: Include-bestand
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c255be53a1809bf5dd3fc6b184852767dfec9c66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009671"
---
> [!NOTE]
> Na 20 minuten inactiviteit treedt een time-out op in de webtoepassing. en alleen aanvragen aan de daadwerkelijke webtoepassing zorgen ervoor dat de timer opnieuw wordt ingesteld. De timer wordt niet gereset als de configuratie van de toepassing in de Azure-portal wordt bekeken of als aanvragen worden ingediend op de pagina met geavanceerde hulpmiddelen (`https://<app_name>.scm.azurewebsites.net`). Als u uw webtoepassing zodanig instelt dat deze doorlopend of gepland (timer-trigger) webjobs uitvoert, schakelt u de optie **Altijd aan** op de Azure-pagina **Configuratie** van uw webtoepassing in om ervoor te zorgen dat de webjobs betrouwbaar worden uitgevoerd. Deze functie is alleen beschikbaar in de [prijscategorieën](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Basic, Standard en Premium.
