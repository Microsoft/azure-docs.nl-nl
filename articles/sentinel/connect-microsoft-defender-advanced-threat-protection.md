---
title: Micro soft Defender voor eindpunt gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Informatie over het verbinden van gegevens van micro soft Defender voor eind punten (voorheen micro soft Defender ATP) aan Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 0db4e0fe0472c75f1eae392980ae697f53007244
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98623363"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Verbinding maken met waarschuwingen van micro soft Defender voor eind punt (voorheen micro soft Defender ATP)

> [!IMPORTANT]
>
> - **Micro soft Defender voor eind punt** was voorheen bekend als **micro soft Defender Advanced Threat Protection** of **MDATP**.
>
>     Mogelijk ziet u de oude naam die nog steeds wordt gebruikt in het product (inclusief de gegevens connector in azure Sentinel) gedurende een bepaalde periode.

Met de [micro soft Defender for Endpoint](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) -connector kunt u waarschuwingen van micro soft Defender naar het eind punt streamen naar Azure Sentinel. Op die manier kunt u beveiligings gebeurtenissen in uw organisatie uitgebreid analyseren en playbooks bouwen voor een effectief en direct antwoord.

> [!NOTE]
>
> Als u de nieuwe onbewerkte gegevens logboeken wilt opnemen in de [geavanceerde jacht](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview)van micro soft Defender voor het eind punt, gebruikt u de nieuwe connector voor Microsoft 365 Defender (voorheen micro soft Threat Protection, [raadpleegt](./connect-microsoft-365-defender.md)u de documentatie).

## <a name="prerequisites"></a>Vereisten

- U moet een geldige licentie voor micro soft Defender voor eind punt hebben, zoals beschreven in [micro soft Defender voor endpoint-implementatie instellen](/windows/security/threat-protection/microsoft-defender-atp/licensing). 

- U moet een globale beheerder of een beveiligings beheerder zijn in de Azure Sentinel-Tenant.

## <a name="connect-to-microsoft-defender-for-endpoint"></a>Verbinding maken met micro soft Defender voor eind punt

Als micro soft Defender voor het eind punt is geïmplementeerd en uw gegevens worden opgenomen, kunnen de waarschuwingen eenvoudig worden gestreamd naar Azure Sentinel.

1. In azure Sentinel selecteert u **gegevens connectors**, selecteert u **micro soft Defender voor eind punt** (kan nog steeds *micro soft Defender Advanced Threat Protection* worden genoemd) in de galerie en selecteert u de **pagina connector openen**.

1. Klik op **Verbinden**. 

1. Als u een query wilt uitvoeren voor micro soft Defender for Endpoint-waarschuwingen in **Logboeken**, voert u **SecurityAlert** in het query venster in en voegt u een filter toe waarbij de **provider naam** **MDATP** is.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u micro soft Defender voor eind punt verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](./tutorial-detect-threats-built-in.md).