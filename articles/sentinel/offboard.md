---
title: Azure-Sentinel verwijderen | Microsoft Docs
description: Uw Azure-Sentinel-exemplaar verwijderen.
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
ms.openlocfilehash: f9c400b55b0da47495db4f1ff4ceb86aa39fe2cc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90885834"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Azure Sentinel verwijderen uit uw werkruimte

Als u Azure Sentinel niet meer wilt gebruiken, wordt in dit artikel uitgelegd hoe u het kunt verwijderen uit uw werk ruimte.

## <a name="how-to-remove-azure-sentinel"></a>Azure Sentinel verwijderen

Volg deze procedure om de Azure-Sentinel te verwijderen uit uw werk ruimte:

1. Ga naar de **Azure-Sentinel**, gevolgd door **instellingen** en selecteer het tabblad **Azure-Sentinel verwijderen**.

1. Voordat u Azure Sentinel verwijdert, moet u de selectie vakjes gebruiken om ons te laten weten waarom u deze verwijdert.

1. Selecteer **Azure-Sentinel verwijderen in uw werk ruimte**.
    
    ![De SecurityInsights-oplossing verwijderen](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Wat gebeurt er achter de schermen?

Wanneer u de oplossing verwijdert, neemt Azure-Sentinel Maxi maal 48 uur de eerste fase van het verwijderings proces te volt ooien.

Nadat de verbinding is vastgesteld, wordt het offboarding-proces gestart.

**De configuratie van deze connectors wordt verwijderd:**
-   Office 365

-   AWS

-   Beveiligings waarschuwingen van micro soft-Services: micro soft Defender for Identity (*voorheen Azure ATP*), Microsoft Cloud App Security inclusief Cloud Discovery Shadow it reporting, Azure AD Identity Protection, micro soft Defender voor eind punt (*voorheen micro soft Defender ATP*), Azure defender-waarschuwingen van Azure Security Center

-   Bedreigingsinformatie

-   Veelvoorkomende beveiligings Logboeken (inclusief op CEF gebaseerde logboeken, Barracuda en syslog) (als u Azure Defender-waarschuwingen van Azure Security Center ontvangt, worden deze logboeken nog steeds verzameld.)

-   Windows-beveiligings gebeurtenissen (als u Azure Defender-waarschuwingen van Azure Security Center ontvangt, worden deze logboeken nog steeds verzameld.)

Binnen de eerste 48 uur zijn de gegevens-en analytische regels (inclusief realtime-automatiserings configuratie) niet langer toegankelijk of kunnen ze niet meer worden opgevraagd in azure Sentinel.

**Na 30 dagen worden deze resources verwijderd:**

-   Incidenten (met inbegrip van onderzoek meta gegevens)

-   Analytische regels

-   Bladwijzers

Uw playbooks, opgeslagen werkmappen, opgeslagen opsporingsquery's en notebooks worden niet verwijderd. **Sommige kunnen worden verbroken vanwege de verwijderde gegevens. U kunt deze hand matig verwijderen.**

Nadat u de service hebt verwijderd, is er een respijt periode van 30 dagen waarin u de oplossing opnieuw kunt inschakelen. uw gegevens en analytische regels worden hersteld, maar de geconfigureerde connectors die zijn losgekoppeld, moeten opnieuw worden verbonden.

> [!NOTE]
> Als u de oplossing verwijdert, is uw abonnement nog steeds geregistreerd bij de Azure Sentinel-resourceprovider. **U kunt het abonnement handmatig verwijderen.**




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u de Azure Sentinel-service kunt verwijderen. Als u van gedachten verandert en deze opnieuw wilt installeren:
- Aan de slag [met on-boarding van Azure-Sentinel](quickstart-onboard.md).
