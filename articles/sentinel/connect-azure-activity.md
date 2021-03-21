---
title: Azure-activiteit gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Stream Azure-activiteiten meldt zich aan bij Azure Sentinel met één klik. In het activiteiten logboek worden gebeurtenissen op abonnements niveau in azure vastgelegd en weer gegeven.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 8f0bac363e020826e34df3cb0b4e3d3582990302
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595449"
---
# <a name="connect-data-from-azure-activity-log"></a>Gegevens verbinden vanuit Azure-activiteiten logboek

U kunt Logboeken van [Azure-activiteiten logboek](../azure-monitor/essentials/platform-logs-overview.md) streamen naar Azure Sentinel met één klik. Het activiteiten logboek is een abonnements logboek waarmee gebeurtenissen op abonnements niveau worden vastgelegd en weer gegeven in azure, van Azure Resource Manager operationele gegevens naar updates op Service Health-gebeurtenissen. Met het activiteiten logboek kunt u de ' wat, wie en wanneer ' bepalen voor elke schrijf bewerking (PUT, POST, DELETE) die is uitgevoerd op de resources in uw abonnement. U kunt ook de status van de bewerking en andere relevante eigenschappen ontdekken. Het activiteiten logboek bevat geen lees bewerkingen (GET) of bewerkingen voor resources die gebruikmaken van het klassieke/' RDFE-model '. 

## <a name="prerequisites"></a>Vereisten

- Uw gebruiker moet Inzender machtigingen hebben voor de Log Analytics-werk ruimte.
- Uw gebruiker moet Lees machtigingen hebben voor elk abonnement waarvan u de logboeken wilt streamen naar Azure Sentinel.

## <a name="set-up-the-azure-activity-connector"></a>De Azure activity connector instellen

1. Selecteer in het navigatie menu van de Azure-Sentinel **Data connectors**. Klik in de lijst met connectors op **Azure activity** en klik vervolgens op de knop **pagina connector openen** op de rechter benedenhoek.

2. Klik op het tabblad **instructies** op de koppeling **Azure-activiteiten logboeken configureren >** .

3. Selecteer in het deel venster **activiteiten logboek van Azure** de abonnementen waarvan u de logboeken wilt streamen naar Azure Sentinel. 

4. Klik op **verbinding maken** in het deel venster abonnement dat rechts wordt geopend.

5. Als u het relevante schema in Log Analytics voor Azure-activiteiten waarschuwingen wilt gebruiken, typt u `AzureActivity` in het query venster.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure-activiteiten logboek verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met het detecteren van bedreigingen met Azure Sentinel, met behulp [van ingebouwde](tutorial-detect-threats-built-in.md) of [aangepaste](tutorial-detect-threats-custom.md) regels.
