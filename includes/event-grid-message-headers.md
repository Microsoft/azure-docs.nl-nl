---
title: bestand opnemen
description: bestand opnemen
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c37e2c28eb6aee9edf4bdd97066ce5f15e7447c1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005624"
---
## <a name="message-headers"></a>Bericht koppen
Dit zijn de eigenschappen die u ontvangt in de bericht koppen:

| Naam van eigenschap | Description |
| ------------- | ----------- | 
| AEG-abonnements naam | De naam van het gebeurtenis abonnement. |
| AEG-levering | Het aantal pogingen dat is gedaan voor de gebeurtenis. |
| AEG-gebeurtenis-type | <p>Het type gebeurtenis.</p><p>Dit kan een van de volgende waarden zijn:</p><ul><li>SubscriptionValidation</li><li>Melding</li><li>SubscriptionDeletion</li></ul> | 
| AEG-meta gegevens-versie | <p>De meta gegevens versie van de gebeurtenis.<p> Voor **Event grid-gebeurtenis schema** vertegenwoordigt deze eigenschap de versie van de meta gegevens en voor het **Cloud-gebeurtenis schema**, het vertegenwoordigt de **specificatie versie**. </p>|
| AEG-gegevens versie | <p>De gegevens versie van de gebeurtenis.</p><p>Voor **Event grid-gebeurtenis schema** vertegenwoordigt deze eigenschap de gegevens versie en voor het **Cloud-gebeurtenis schema**, maar dit is niet van toepassing.</p> |
| AEG-uitvoer-gebeurtenis-id | ID van de Event Grid gebeurtenis. |


