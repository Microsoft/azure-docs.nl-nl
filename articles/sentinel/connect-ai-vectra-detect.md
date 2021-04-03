---
title: Verbinding maken met AI Vectra gegevens naar Azure-Sentinel detecteren | Microsoft Docs
description: Meer informatie over het verbinden van AI Vectra gegevens naar Azure Sentinel detecteren.
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
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: c57f4a49ac89f1347fc88f8bacddce3abab7e44e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87038219"
---
# <a name="connect-ai-vectra-detect-to-azure-sentinel"></a>AI Vectra-detectie met Azure-Sentinel verbinden

> [!IMPORTANT]
> De AI Vectra detect Data Connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel wordt uitgelegd hoe u uw [AI-Vectra detecteert](https://www.vectra.ai/product/cognito-detect) met Azure Sentinel. Met de AI Vectra detect Data Connector kunt u eenvoudig uw AI Vectra-gegevens in de Azure-Sentinel detecteren, zodat u deze in werkmappen kunt bekijken, gebruiken om aangepaste waarschuwingen te maken en deze op te nemen om het onderzoek te verbeteren.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-your-ai-vectra-detect-appliance-to-send-cef-messages"></a>Uw AI Vectra detecteren apparaat configureren voor het verzenden van CEF-berichten  

Configureer AI Vectra detect voor het door sturen van met CEF geformatteerde syslog-berichten naar uw Log Analytics-werk ruimte via de syslog-doorstuur server die u hebt ingesteld in [stap 1: de logboek-doorstuur server implementeren](connect-cef-agent.md).

1. Ga in de Vectra-interface naar instellingen > meldingen en kies syslog-configuratie bewerken. Volg de onderstaande instructies om de verbinding in te stellen:

    - Een nieuw doel toevoegen (de hostnaam van de [logboek-doorstuur server](connect-cef-agent.md))
    - Stel de poort in op **514**
    - Het protocol instellen als **UDP**
    - De indeling instellen op **CEF**
    - Logboek typen instellen (alle beschik bare logboek typen selecteren)
    - Klik op **Opslaan**

2. U kunt op de knop **testen** klikken om het verzenden van bepaalde test gebeurtenissen naar de logboek-doorstuur server af te dwingen.

3. Als u het relevante schema in Log Analytics voor de AI Vectra-detectie gebeurtenissen wilt gebruiken, zoekt u naar **CommonSecurityLog**.

4. Ga door naar [stap 3: de verbinding valideren](connect-cef-verify.md).

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u met AI Vectra detecteert apparaten te verbinden met Azure Sentinel. Klik op het tabblad **volgende stappen** op de pagina Data Connector om optimaal gebruik te maken van de mogelijkheden die zijn ingebouwd in deze gegevens connector. Hier vindt u een aantal kant-en-klare voorbeeld query's, zodat u meteen aan de slag kunt met het vinden van nuttige informatie.

Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
