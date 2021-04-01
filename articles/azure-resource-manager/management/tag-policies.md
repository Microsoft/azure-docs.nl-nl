---
title: Beleid voor het labelen van resources
description: Hierin worden de Azure-beleids regels beschreven die u kunt toewijzen om label compatibiliteit te garanderen.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: c6867bc01306ac3c08a9797ece0567a45e060af2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89293729"
---
# <a name="assign-policies-for-tag-compliance"></a>Beleid voor naleving van labels toewijzen

U gebruikt [Azure Policy](../../governance/policy/overview.md) om regels en conventies voor labelen af te dwingen. Door een beleid te maken, vermijdt u het scenario van resources die worden geïmplementeerd in uw abonnement en die niet de verwachte Tags voor uw organisatie hebben. In plaats van labels hand matig toe te passen of te zoeken naar resources die niet compatibel zijn, maakt u een beleid waarmee automatisch de benodigde Tags tijdens de implementatie worden toegepast. Labels kunnen nu ook worden toegepast op bestaande resources met het nieuwe [wijzigings](../../governance/policy/concepts/effects.md#modify) effect en een [herstel taak](../../governance/policy/how-to/remediate-resources.md). In de volgende sectie ziet u voor beelden van beleids regels voor Tags.

## <a name="policies"></a>Beleidsregels

[!INCLUDE [Tag policies](../../../includes/policy/reference/bycat/policies-tags.md)]

## <a name="next-steps"></a>Volgende stappen

* Zie [Tags gebruiken om uw Azure-resources te organiseren](tag-resources.md) voor meer informatie over het taggen van resources.
* Niet alle resource typen ondersteunen Tags. Zie [tag-ondersteuning voor Azure-resources](tag-support.md)om te bepalen of u een tag kunt Toep assen op een resource type.
