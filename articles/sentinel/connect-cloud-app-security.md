---
title: Cloud App Security gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Microsoft Cloud App Security-connector (MCAS) voor het streamen van waarschuwingen en Cloud Discovery logboeken van MCAS naar Azure Sentinel.
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
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 3312eed71865508e5e83d37c7ced8cf220f13ca9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97835105"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Verbinding maken met gegevens van Microsoft Cloud App Security 

Met de [Microsoft Cloud app Security](/cloud-app-security/what-is-cloud-app-security) -connector (MCAS) kunt u waarschuwingen streamen en [Cloud Discovery logboeken](/cloud-app-security/tutorial-shadow-it) van MCAS naar Azure Sentinel. Hiermee krijgt u inzicht in uw Cloud-apps, kunt u geavanceerde analyses verkrijgen om Cyber dreigingen te identificeren en te bestrijden en te bepalen hoe uw gegevens worden verplaatst.

## <a name="prerequisites"></a>Vereisten

- Uw gebruiker moet lees-en schrijf machtigingen hebben voor de werk ruimte.
- Uw gebruiker moet over de machtigingen globale beheerder of beveiligings beheerder beschikken voor de Tenant van de werk ruimte.
- Als u Cloud Discovery-Logboeken in azure Sentinel wilt streamen, [schakelt u Azure Sentinel in als uw Siem in Microsoft Cloud app Security](/cloud-app-security/siem-sentinel).

> [!IMPORTANT]
> Opname van Cloud Discovery Logboeken is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
 
## <a name="connect-to-cloud-app-security"></a>Verbinding maken met Cloud App Security

Als u Cloud App Security al hebt, controleert u of deze is [ingeschakeld in uw netwerk](/cloud-app-security/getting-started-with-cloud-app-security).
Als Cloud App Security is geïmplementeerd en opnamet van uw gegevens, kunnen de waarschuwings gegevens eenvoudig worden gestreamd naar Azure Sentinel.


1. Selecteer in het navigatie menu van de Azure-Sentinel **Data connectors**. Klik in de lijst met connectors op de tegel **Microsoft Cloud app Security** en vervolgens op de knop **pagina connector openen** aan de rechter kant.

1. Selecteer de logboeken die u wilt streamen naar Azure Sentinel; u kunt **waarschuwingen** en **Cloud Discovery logboeken** kiezen (preview-versie). 

1. Klik op **wijzigingen Toep assen**.

1. U kunt selecteren of u wilt dat de waarschuwingen van Cloud App Security automatisch incidenten genereren in azure Sentinel. Onder **incidenten maken: aanbevolen!** selecteert u **ingeschakeld** om de standaard analyse regel in te scha kelen waarmee automatisch incidenten worden gemaakt op basis van waarschuwingen. U kunt deze regel vervolgens onder **analyse** bewerken op het tabblad  **actieve regels** .

1. Als u het relevante schema in Log Analytics voor Cloud App Security-waarschuwingen wilt gebruiken, typt u `SecurityAlert` in het query venster. Typ voor het Cloud Discovery logboeken schema `McasShadowItReporting` .

> [!NOTE]
> Cloud Discovery helpt trends te detecteren en te identificeren door de gegevens van de onderliggende gebruikers verbindingen met Cloud-apps samen te voegen.
>
> Omdat Cloud Discovery gegevens per dag worden geaggregeerd, moet u er rekening mee houden dat Maxi maal 24 uur aan de meest recente gegevens niet in azure Sentinel wordt weer gegeven. In het geval dat een onderzoek op laag niveau meer onmiddellijke gegevens vereist, moet dit rechtstreeks worden gedaan in het bron apparaat of de service waar de onbewerkte gegevens zich bevinden.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Microsoft Cloud App Security kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met het detecteren van bedreigingen met Azure Sentinel, met behulp [van ingebouwde](./tutorial-detect-threats-built-in.md) of [aangepaste](tutorial-detect-threats-custom.md) regels.