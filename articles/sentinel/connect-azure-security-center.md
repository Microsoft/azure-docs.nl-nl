---
title: Azure Defender-gegevens verbinden met Azure Sentinel
description: Meer informatie over het verbinden van Azure Defender-waarschuwingen van Azure Security Center en het streamen naar Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: bb188aa79015c2123b9d9d8b6baf277dfadf2f9c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633041"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>Azure Defender-waarschuwings gegevens van Azure Security Center verbinden

Gebruik de Azure Defender-waarschuwings connector om Azure Defender-waarschuwingen van [Azure Security Center](../security-center/security-center-introduction.md) op te nemen en ze in de Azure-Sentinel te streamen. 

## <a name="prerequisites"></a>Vereisten

- Uw gebruiker moet de rol van beveiligings lezer hebben in het abonnement van de logboeken die u streamen.

- U moet Azure Defender inschakelen binnen Azure Security Center. (De laag standaard bestaat niet meer en is niet langer een licentie vereiste.)

## <a name="connect-to-azure-defender"></a>Verbinding maken met Azure Defender

1. Selecteer in azure Sentinel **Data connectors** in het navigatie menu.

1. Selecteer in de galerie met gegevens connectors de optie **Azure Defender-waarschuwingen van ASC** (kan nog steeds worden aangeroepen Azure Security Center) en klik op de knop voor het openen van de **connector pagina** .

1. Klik onder **configuratie** op **verbinden** naast elk abonnement waarvan u de waarschuwingen wilt streamen naar Azure Sentinel. De knop verbinding maken is alleen beschikbaar als u de vereiste machtigingen hebt.

1. U kunt selecteren of u wilt dat de waarschuwingen van Azure Defender automatisch incidenten genereren in azure Sentinel. Schakel onder **incidenten maken** de optie **ingeschakeld** in om de standaard analyse regel in te scha kelen waarmee automatisch incidenten worden gemaakt op basis van waarschuwingen. U kunt deze regel vervolgens onder **analyse** bewerken op het tabblad  **actieve regels** .

1. Als u het relevante schema in Log Analytics voor de Azure Defender-waarschuwingen wilt gebruiken, zoekt u naar **SecurityAlert**.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Azure Defender kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
