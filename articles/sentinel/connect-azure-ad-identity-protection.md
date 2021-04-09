---
title: Azure AD Identity Protection gegevens verbinden met Azure Sentinel
description: Meer informatie over het streamen van Logboeken en waarschuwingen van Azure AD Identity Protection naar Azure Sentinel voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van het onderzoek.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: f420e80f10072c440f5401c042d6370a061b1a7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632238"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>Verbinding maken met gegevens van Azure Active Directory (Azure AD) identiteits beveiliging

U kunt Logboeken van [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) naar Azure Sentinel streamen om waarschuwingen te streamen naar Azure Sentinel om Dash boards weer te geven, aangepaste waarschuwingen te maken en het onderzoek te verbeteren. Azure Active Directory Identity Protection biedt een geconsolideerde weer gave van risico gebruikers, risico detecties en beveiligings problemen, met de mogelijkheid om het risico onmiddellijk op te lossen en beleids regels in te stellen om toekomstige gebeurtenissen automatisch te herstellen. De service is gebouwd op basis van de ervaring van micro soft met het beschermen van consumenten identiteiten en het verbeteren van een enorme nauw keurigheid van het signaal van meer dan 13.000.000.000 logboeken per dag. 

## <a name="prerequisites"></a>Vereisten

- U moet een [Azure AD Premium P2-abonnement](https://azure.microsoft.com/pricing/details/active-directory/)hebben.
- U moet een gebruiker hebben met de machtigingen globale beheerder of beveiligings beheerder.

## <a name="connect-to-azure-ad-identity-protection"></a>Verbinding maken met Azure AD Identity Protection

Als u een Azure AD Premium P2-abonnement hebt, wordt Azure AD Identity Protection opgenomen. Als er [beleid is ingeschakeld](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) en er waarschuwingen worden gegenereerd, kunnen de waarschuwings gegevens eenvoudig worden gestreamd naar Azure Sentinel.

1. Selecteer in azure Sentinel **Data connectors** en klik vervolgens op de tegel **Azure AD Identity Protection** .

1. Klik op **verbinding maken** om Azure AD Identity Protection gebeurtenissen te streamen naar Azure Sentinel.

1. Als u het relevante schema in Log Analytics voor de Azure AD Identity Protection waarschuwingen wilt gebruiken, zoekt u naar **SecurityAlert**.

Als u de connector wilt testen, kunt u [detecties simuleren](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md) om voorbeeld waarschuwingen te genereren die worden gestreamd naar Azure Sentinel.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Azure AD Identity Protection kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
