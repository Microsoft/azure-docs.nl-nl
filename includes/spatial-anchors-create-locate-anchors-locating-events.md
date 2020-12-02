---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 5360cbb7bdfbdc59e87366e73a891b5c2583672e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993033"
---
Nadat de watcher is gemaakt, wordt de `AnchorLocated`-gebeurtenis voor elk aangevraagd anker geactiveerd. Deze gebeurtenis wordt geactiveerd wanneer er een anker wordt gelokaliseerd of als het anker niet kan worden gelokaliseerd. Als deze situatie zich voordoet, wordt de reden aangegeven in de status. Nadat alle ankers voor een watcher zijn verwerkt, gevonden of niet gevonden, wordt de `LocateAnchorsCompleted`-gebeurtenis geactiveerd. Er geldt een limiet van 35 id's per watcher. 
