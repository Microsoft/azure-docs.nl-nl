---
title: Verbinding met 81-gegevens verbinden met Azure-Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van verbinding met 81-gegevens met Azure Sentinel.
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
ms.openlocfilehash: e70bfd12b018e785e7b3b91e098c99fea1101c04
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093094"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>De activiteiten logboeken van uw perimeter 81 verbinden met Azure Sentinel

> [!IMPORTANT]
> De perimeter 81-gegevens connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel wordt uitgelegd hoe u het apparaat voor de [activiteiten logboeken van de Perimeter 81](https://www.perimeter81.com/) verbindt met Azure Sentinel. Met de connector voor de activiteiten logboeken van de perimeter 81 kunt u eenvoudig uw perimeter 81-gegevens in azure Sentinel brengen, zodat u deze in werkmappen kunt bekijken, er aangepaste waarschuwingen voor moet maken en deze moet opnemen om het onderzoek te verbeteren.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

- U moet Lees machtigingen hebben voor gedeelde sleutels voor de werk ruimte.

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>De activiteiten logboeken van een perimeter 81 configureren en verbinden

Activiteiten logboeken van de perimeter 81 kunnen Logboeken rechtstreeks integreren en exporteren naar Azure Sentinel.

1. Klik in de Azure-Sentinel Portal op **Data connectors** in het navigatie menu.

1. Selecteer **activiteiten logboeken van de Perimeter 81** in de galerie en klik vervolgens op de knop **connector pagina openen** .

1. Kopieer de **werk ruimte-id** en **primaire sleutel** op de pagina connector 81-activiteiten logboeken en plak ze in de perimeter 81, [zoals hier](https://support.perimeter81.com/hc/en-us/articles/360012680780)wordt beschreven.

1. Nadat u de instructies hebt voltooid, ziet u de verbonden gegevens typen op de Azure Sentinel connector-pagina.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken** onder **CustomLogs**  -  **Perimeter81_CL**.

Het kan Maxi maal 20 minuten duren voordat de logboeken worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u de activiteiten logboeken van een perimeter 81 verbindt met Azure Sentinel. Klik op het tabblad **volgende stappen** op de pagina Data Connector om optimaal gebruik te maken van de mogelijkheden die zijn ingebouwd in deze gegevens connector. Hier vindt u een kant-en-klare werkmap en enkele voor beelden van query's, zodat u meteen nuttige informatie kunt vinden.

Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
