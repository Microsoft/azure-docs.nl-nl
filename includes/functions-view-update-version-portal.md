---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: aa9a715fdafc143a116458691965087b016dec1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83343351"
---
Gebruik de volgende procedure om de runtime-versie weer te geven en bij te werken die momenteel wordt gebruikt door een functie-app.

1. Blader in de [Azure-portal](https://portal.azure.com) naar uw functie-app.

1. Kies onder **instellingen** de optie **configuratie**. Ga naar het tabblad **instellingen voor runtime** en zoek de **runtime versie**. Noteer de specifieke runtime versie. In onderstaand voorbeeld is de versie ingesteld op `~3`.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-view-runtime-version.png" alt-text="De runtime versie weer geven." border="true":::

1. Als u uw functie-app wilt vastmaken aan de runtime met versie 1.x, kiest u **~ 1** onder **Runtime-versie**. Deze schakeloptie is uitgeschakeld als uw app functies bevat.

1. Wanneer u de runtime-versie wijzigt, gaat u terug naar het tabblad **Overzicht** en kiest u **Opnieuw opstarten** om de app opnieuw op te starten.  De functie-app wordt opnieuw opgestart met versie 1.x van de runtime en de sjablonen van versie 1.x worden gebruikt wanneer u functies maakt.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-restart-function-app.png" alt-text="Start de functie-app opnieuw." border="true":::
