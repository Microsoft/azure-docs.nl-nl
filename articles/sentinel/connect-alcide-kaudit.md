---
title: Alcide kAudit-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over hoe u Alcide kAudit-gegevens verbindt met Azure Sentinel.
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
ms.openlocfilehash: 6d1314b79f09f1bed2de5d6964b0a6224b5962ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096868"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Uw Alcide kAudit verbinden met Azure Sentinel

> [!IMPORTANT]
> De Alcide kAudit-gegevens connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) helpt u afwijkende Kubernetes-gedrag te identificeren en te focussen op Kubernetes schendingen en incidenten, terwijl de detectie tijd wordt verminderd. In dit artikel wordt uitgelegd hoe u uw Alcide kAudit-oplossing verbindt met Azure Sentinel. Met de Alcide kAudit Data Connector kunt u eenvoudig uw kAudit-logboek gegevens in azure-Sentinel brengen, zodat u deze in werkmappen kunt bekijken, gebruiken om aangepaste waarschuwingen te maken en deze op te nemen om het onderzoek te verbeteren. Integratie tussen Alcide kAudit en Azure Sentinel maakt gebruik van REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

- U moet Lees machtigingen hebben voor gedeelde sleutels voor de werk ruimte.

## <a name="configure-and-connect-alcide-kaudit"></a>Alcide kAudit configureren en verbinden

Met Alcide kAudit kunnen Logboeken rechtstreeks naar Azure Sentinel worden geëxporteerd.

1. Klik in de Azure-Sentinel Portal op **Data connectors** in het navigatie menu.

1. Selecteer **Alcide kAudit** in de galerie en klik vervolgens op de knop **connector pagina openen** .

1. Volg de stapsgewijze instructies in de [installatie handleiding van de Alcide kAudit](https://get.alcide.io/hubfs/Azure%20Sentinel%20Integration%20with%20kAudit.pdf).

1. Wanneer u wordt gevraagd om de werk ruimte-ID en de primaire sleutel, kunt u deze kopiëren van de Alcide kAudit Data Connector-pagina.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="Werk ruimte-ID en primaire sleutel":::

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken** onder de volgende gegevens typen in **CustomLogs**:

- detectie van **alcide_kaudit_detections_1_CL** -Alcide kaudit 
- **alcide_kaudit_activity_1_CL** -Alcide kaudit-activiteiten logboeken
- aantallen **alcide_kaudit_selections_count_1_CL** -Alcide kaudit-activiteiten
- Alcide kAudit-activiteit Details **alcide_kaudit_selections_details_1_CL**

Als u het relevante schema in Logboeken voor de Alcide kAudit wilt gebruiken, zoekt u naar de hierboven vermelde gegevens typen.

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Alcide kAudit verbindt met Azure Sentinel. Klik op het tabblad **volgende stappen** op de pagina Data Connector om optimaal gebruik te maken van de mogelijkheden die zijn ingebouwd in deze gegevens connector. Hier vindt u een aantal kant-en-klare voorbeeld query's, zodat u meteen aan de slag kunt met het vinden van nuttige informatie.

Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
