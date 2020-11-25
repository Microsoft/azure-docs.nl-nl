---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000539"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Meerdere containers op dezelfde host uitvoeren

Als u van plan bent om meerdere containers met blootgestelde poorten uit te voeren, moet u ervoor zorgen dat elke container wordt uitgevoerd met een andere beschik bare poort. Voer bijvoorbeeld de eerste container uit op poort 5000 en de tweede container op poort 5001.

U kunt deze container en een andere Azure Cognitive Services-container die samen op de HOST wordt uitgevoerd. U kunt ook meerdere containers van hetzelfde Cognitive Services-container uitvoeren.
