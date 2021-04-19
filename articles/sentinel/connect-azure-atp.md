---
title: Verbinding Microsoft Defender for Identity (voorheen Azure ATP) met Azure Sentinel| Microsoft Docs
description: Meer informatie over het streamen van logboeken van Microsoft Defender for Identity (voorheen Azure Advanced Threat Protection) (ATP) naar Azure Sentinel met één klik.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 7a26091d4985b6fdb17120c6fd70476a750c94a9
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714133"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Verbinding maken met gegevens Microsoft Defender for Identity (voorheen Azure Advanced Threat Protection)

> [!IMPORTANT]
> De Microsoft Defender for Identity-gegevensconnector in Azure Sentinel is momenteel in openbare preview.
> Deze functie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel wordt beschreven hoe u beveiligingswaarschuwingen van [Microsoft Defender for Identity](/azure-advanced-threat-protection/what-is-atp) naar Azure Sentinel. 

Als u naast beveiligingswaarschuwingen ook statuswaarschuwingen wilt doorsturen, integreert Microsoft Defender for Identity met een Syslog-server. Zie de Microsoft Defender for Identity [voor meer informatie.](/defender-for-identity/setting-syslog) 

## <a name="prerequisites"></a>Vereisten

- Gebruiker met globale beheerder- of beveiligingsbeheerdermachtigingen
- U moet een preview-klant van Microsoft Defender for Identity zijn en integratie tussen Microsoft Defender for Identity en Microsoft Cloud App Security. Zie integratie voor [Microsoft Defender for Identity meer informatie.](/cloud-app-security/mdi-integration)

## <a name="connect-to-microsoft-defender-for-identity"></a>Verbinding maken met Microsoft Defender for Identity

Zorg ervoor dat Microsoft Defender for Identity preview-versie is [ingeschakeld op uw netwerk.](/azure-advanced-threat-protection/install-atp-step1)
Als Microsoft Defender for Identity gegevens worden geïmplementeerd en opgenomen, kunnen de verdachte waarschuwingen eenvoudig worden gestreamd naar Azure Sentinel. Het kan tot 24 uur duren voordat de waarschuwingen naar de Azure Sentinel.


1. Als u Microsoft Defender for Identity verbinding wilt Azure Sentinel, moet u eerst integratie tussen Microsoft Defender for Identity en Microsoft Cloud App Security. Zie integratie voor Microsoft Defender for Identity informatie [over hoe Microsoft Defender for Identity doen.](/cloud-app-security/mdi-integration)

1. Selecteer Azure Sentinel **gegevensconnectoren en** klik vervolgens op de **tegel Microsoft Defender for Identity (preview).**

1. U kunt selecteren of u wilt dat de waarschuwingen van Microsoft Defender for Identity automatisch incidenten in Azure Sentinel genereren. Selecteer **onder Incidenten** maken de **optie** Inschakelen om de standaard analyseregel in te stellen waarmee automatisch incidenten worden gemaakt op basis van waarschuwingen die zijn gegenereerd in de verbonden beveiligingsservice. U kunt deze regel vervolgens bewerken onder **Analyse** en vervolgens **Actieve regels.**

1. Klik op **Verbinden**.

1. Als u het relevante schema in Log Analytics wilt gebruiken voor de Microsoft Defender for Identity, zoekt u **naar SecurityAlert.**

> [!NOTE]
> Als de waarschuwingen groter zijn dan 30 kB, Azure Sentinel het veld Entiteiten niet meer weergegeven in de waarschuwingen.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u verbinding maakt Microsoft Defender for Identity met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over [het krijgen van inzicht in uw gegevens en mogelijke bedreigingen.](quickstart-get-visibility.md)
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
