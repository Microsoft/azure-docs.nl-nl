---
title: Melding voor automatisch bijwerken en verlopen van zelf-hostende IR
description: Meer informatie over de zelf-hostende functie voor automatisch bijwerken en verlopen van Integration runtime
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 972015f0f42a8a869de0edcc8f0e921ae7278cd1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376255"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Melding voor automatisch bijwerken en verlopen van zelf-hostende IR

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u zelf-hostende Integration runtime automatisch kunt bijwerken naar de nieuwste versie en hoe ADF de versies van zelf-hostende Integration runtime beheert.

## <a name="self-hosted-integration-runtime-auto-update"></a>Zelf-hostende Integration Runtime automatisch bijwerken
Wanneer u een zelf-hostende Integration runtime installeert op uw lokale machine of een Azure-VM, hebt u in het algemeen twee opties voor het beheren van de versie van zelf-hostende Integration runtime: automatisch bijwerken of hand matig onderhouden. Normaal gesp roken brengt ADF twee nieuwe versies van zelf-hostende integratie-runtime uit elke maand, waarin nieuwe functies worden uitgebracht, een probleem oplossing of een uitbrei ding van fouten. Daarom raden we gebruikers aan om bij te werken naar de meest recente versie om de nieuwste functie en uitbrei ding te verkrijgen.

De meest geschikte manier is om automatisch bijwerken in te scha kelen wanneer u een zelf-hostende Integration runtime maakt of bewerkt. Vervolgens wordt deze automatisch bijgewerkt naar de nieuwste versie. U kunt de update ook volgens de meest geschikte tijds periode plannen.

![Automatisch bijwerken inschakelen](media/create-self-hosted-integration-runtime/shir-auto-update.png)

U kunt de datum/tijd van de laatste update controleren in uw zelf-hostende Integration runtime-client.

![Scherm afbeelding van het controleren van de tijd van de update](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> Om de stabiliteit van zelf-hostende Integration runtime te garanderen, hoewel we twee versies hebben uitgebracht, worden deze alleen elke maand automatisch bijgewerkt. Soms zult u merken dat de automatisch bijgewerkte versie de vorige versie van de meest recente versie is. Als u de meest recente versie wilt downloaden, gaat u naar het [Download centrum](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="self-hosted-integration-runtime-expire-notification"></a>Zelf-hostende melding Integration Runtime verloop datum
Als u hand matig wilt bepalen welke versie van de zelf-hostende Integration runtime, kunt u de instelling van automatische updates uitschakelen en deze hand matig installeren. Elke versie van zelf-hostende Integration runtime is in één jaar verlopen. Het verlopende bericht wordt weer gegeven in de ADF-Portal en de zelf-hostende Integration runtime-client **90 dagen** vóór de verval datum.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [concepten van de integratie-runtime in azure Data Factory](./concepts-integration-runtime.md).

- Meer informatie over [het maken van een zelf-hostende Integration runtime in de Azure Portal](./create-self-hosted-integration-runtime.md).
