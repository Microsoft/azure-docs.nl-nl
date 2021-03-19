---
title: Illusive-aanvals systeem gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van illusive-systeem gegevens van het aanvals beleid met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: ea7953be25473357f7ed572fa8b76076edc6f75a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87038114"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Uw illusive-aanvals beheer systeem verbinden met Azure Sentinel

> [!IMPORTANT]
> De illusive-aanvals beheer systeem gegevens connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel wordt uitgelegd hoe u uw [illusive-aanvals beheer systeem](https://www.illusivenetworks.com/technology/platform/attack-detection-system) verbindt met Azure Sentinel. Met de illusive-systeem gegevens connector voor aanvallen kunt u de gegevens van het aanvals-en incident logboek van illusive delen met Azure Sentinel en deze informatie weer geven in specifieke Dash boards die inzicht bieden in de aanvals Risico's van uw organisatie (ASM-dash board) en de ongeoorloofde verplaatsingen volgen in het netwerk van uw organisatie (ADS-dash board).

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Systeem logboeken van illusive-aanval door sturen naar de syslog-agent  

Configureer een aanvals systeem om syslog-berichten in de CEF-indeling door te sturen naar uw Azure-werk ruimte via de syslog-agent.

1. Meld u aan bij de illusive-console en navigeer naar instellingen->rapportage.

1. Syslog-servers zoeken.

1. Geef de volgende informatie op:
   - Hostnaam: IP-adres of FQDN-naam van de Linux-syslog-agent
   - Poort: 514
   - Protocol: TCP
   - Controle berichten: controle berichten verzenden naar server

1. Klik op toevoegen om de syslog-server toe te voegen.

1. Als u het relevante schema in **Logboeken** voor het illusive-aanvals beheer systeem wilt gebruiken, zoekt u naar **CommonSecurityLog**.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u illusive-aanvals beheer systeem verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
