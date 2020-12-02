---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: c9b4e0fdf95c2bb4888a3a11820f1785d8545bd4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993038"
---
### <a name="access-tokens"></a>Toegangstokens

Toegangstokens zijn een meer robuuste methode om verificatie uit te voeren bij Azure Spatial Anchors. Met name wanneer u de toepassing voorbereidt voor een productie-implementatie. Samengevat: stel een back-endservice in waarmee uw clienttoepassing veilig kan worden geverifieerd. Uw back-endservice communiceert tijdens runtime met AAD en met de beveiligingstokenservice van Azure Spatial Anchors om een toegangstoken aan te vragen. Dit token wordt vervolgens afgeleverd aan de clienttoepassing en wordt in de SDK gebruikt om verificatie bij Azure Spatial Anchors uit te voeren.
