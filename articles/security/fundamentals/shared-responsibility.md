---
title: Gedeelde verantwoordelijkheid in de Cloud-Microsoft Azure
description: Begrijp het gedeelde verantwoordelijkheids model en welke beveiligings taken worden verwerkt door de Cloud provider en welke taken door u worden afgehandeld.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: terrylan
ms.openlocfilehash: 5d9d3878c34bd203b6c14b45e1196245ef8524f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99548946"
---
# <a name="shared-responsibility-in-the-cloud"></a>Gedeelde verantwoordelijkheid in de cloud

Als u de open bare Cloud Services overweegt en evalueert, is het belang rijk om het gedeelde verantwoordelijkheids model te begrijpen en welke beveiligings taken worden verwerkt door de Cloud provider en welke taken door u worden uitgevoerd. De werkbelasting verantwoordelijkheden variëren, afhankelijk van of de werk belasting wordt gehost op Software as a Service (SaaS), platform as a Service (PaaS), Infrastructure as a Service (IaaS) of in een on-premises Data Center

## <a name="division-of-responsibility"></a>Verantwoordelijkheids divisie
In een on-premises Data Center bent u eigenaar van de hele stack. Wanneer u overstapt naar de Cloud, worden sommige verantwoordelijkheden overgedragen naar micro soft. In het volgende diagram ziet u de verantwoordelijkheids gebieden tussen u en micro soft, afhankelijk van het type implementatie van de stack.

![Verantwoordelijkheids zones](./media/shared-responsibility/shared-responsibility.png)

Voor elk type cloudimplementatie bent u eigenaar van uw gegevens en identiteiten. U bent verantwoordelijk voor het beveiligen van de beveiliging van uw gegevens en identiteiten, on-premises resources en de Cloud onderdelen die u beheert (afhankelijk van het Service type).

Ongeacht het type implementatie, worden de volgende verantwoordelijkheden altijd door u bewaard:

- Gegevens
- Eindpunten
- Account
- Toegangsbeheer

## <a name="cloud-security-advantages"></a>Voor delen van Cloud beveiliging
De Cloud biedt grote voor delen voor het oplossen van lange permanente informatie over beveiligings problemen. In een on-premises omgeving hebben organisaties waarschijnlijk unmet verantwoordelijkheden en beperkte bronnen die beschikbaar zijn om te investeren in beveiliging, waardoor een omgeving wordt gemaakt waarin aanvallers zwakke plekken op alle lagen kunnen exploiteren.

In het volgende diagram ziet u een traditionele benadering waarbij veel beveiligings verantwoordelijkheden worden unmet vanwege beperkte resources. In de Cloud-aanpak kunt u de beveiligings verantwoordelijkheden van de dag tot de dag naar uw Cloud provider verschuiven en uw resources opnieuw toewijzen.

![Beveiligings voordelen van Cloud era](./media/shared-responsibility/cloud-enabled-security.png)

In de Cloud-aanpak kunt u ook gebruikmaken van cloud-gebaseerde beveiligings mogelijkheden voor meer effectiviteit en Cloud Intelligence gebruiken om de detectie en reactie tijd van bedreigingen te verbeteren. Door verantwoordelijkheden te verplaatsen naar de Cloud provider, kunnen organisaties meer beveiligings dekking krijgen, waardoor ze beveiligings bronnen en budget opnieuw aan andere zakelijke prioriteiten kan toewijzen.

## <a name="next-steps"></a>Volgende stappen
Zie [gedeelde verantwoordelijkheden voor Cloud Computing](https://azure.microsoft.com/resources/shared-responsibility-for-cloud-computing/)voor meer informatie over het delen van de verantwoordelijkheid tussen u en micro soft in een SaaS-, Paas-en IaaS-implementatie.
