---
title: Vereisten voor query's
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: 777e3261bd6f842861ed6622b78589eedaaf65d7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043192"
---
Gebruik de parameter `@StartTime` in de query om metrische gegevens voor een enkele tijdstempel op te halen. Met Metrics Advisor wordt de parameter vervangen door een tekenreeks `yyyy-MM-ddTHH:mm:ss` wanneer de query wordt uitgevoerd.

> [!IMPORTANT]
> Met de query wordt maximaal één record per dimensiecombinatie geretourneerd voor elke tijdstempel. En alle records die zijn geretourneerd met de query, moeten dezelfde tijdstempels hebben. In Metrics Advisor wordt deze query uitgevoerd voor elke tijdstempel om uw gegevens op te nemen. Raadpleeg de sectie [Veelgestelde vragen over query's](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) voor meer informatie en voorbeelden. 