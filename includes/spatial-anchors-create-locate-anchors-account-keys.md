---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 7210b05566f5cd6f3c56792bce0904b3c9b645da
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993113"
---
## <a name="set-up-authentication"></a>Verificatie instellen

Voor toegang tot de service moet u beschikken over een accountsleutel, een toegangstoken of een Azure Active Directory-beveiligingstoken. U kunt hierover meer lezen op de [conceptpagina Verificatie](../articles/spatial-anchors/concepts/authentication.md).

### <a name="account-keys"></a>Accountsleutels

Accountsleutels zijn een referentie waarmee uw toepassing kan worden geverifieerd bij de service Azure Spatial Anchors. Het beoogde doel van accountsleutels is om snel aan de slag te kunnen. Met name tijdens de ontwikkelingsfase van de integratie van uw toepassing met Azure Spatial Anchors. Zo kunt u accountsleutels gebruiken door ze tijdens het ontwikkelen in uw clienttoepassingen in te sluiten. Als het ontwikkelen klaar is, wordt u sterk aangeraden over te stappen op een verificatiemechanisme op productieniveau, ondersteund door toegangstokens of Azure Active Directory-gebruikersverificatie. Als u een accountsleutel voor ontwikkeling wilt ophalen, gaat u in uw Azure Spatial Anchors-account en naar het tabblad Sleutels.