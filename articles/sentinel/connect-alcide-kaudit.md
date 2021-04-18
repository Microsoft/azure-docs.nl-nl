---
title: Alcide kAudit-gegevens verbinden met Azure Sentinel| Microsoft Docs
description: Meer informatie over het verbinden van Alcide kAudit-gegevens met Azure Sentinel.
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
ms.openlocfilehash: b146e228de13109975a76b0e4c6c9fd183fd362d
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600401"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Uw Alcide kAudit verbinden met Azure Sentinel

> [!IMPORTANT]
> De Alcide kAudit-gegevensconnector in Azure Sentinel is momenteel in openbare preview.
> Deze functie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

[Met Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) kunt u afwijkende Kubernetes-gedragingen identificeren en u richten op Kubernetes-schendingen en -incidenten, terwijl de detectietijd wordt verkort. In dit artikel wordt uitgelegd hoe u uw Alcide kAudit-oplossing verbindt met Azure Sentinel. Met de Alcide kAudit-gegevensconnector kunt u eenvoudig uw kAudit-logboekgegevens naar Azure Sentinel brengen, zodat u deze kunt weergeven in werkmappen, deze kunt gebruiken om aangepaste waarschuwingen te maken en deze kunt opnemen om het onderzoek te verbeteren. Integratie tussen Alcide kAudit en Azure Sentinel maakt gebruik van REST API.

> [!NOTE]
> Gegevens worden opgeslagen op de geografische locatie van de werkruimte waarop u de Azure Sentinel.

## <a name="prerequisites"></a>Vereisten

- U moet lees- en schrijfmachtigingen hebben voor de Azure Sentinel werkruimte.

- U moet leesmachtigingen hebben voor gedeelde sleutels voor de werkruimte.

## <a name="configure-and-connect-alcide-kaudit"></a>Alcide kAudit configureren en verbinden

Met Alcide kAudit kunt u logboeken rechtstreeks naar Azure Sentinel.

1. Klik in Azure Sentinel portal op **Gegevensconnectoren** in het navigatiemenu.

1. Selecteer **Alcide kAudit in** de galerie en klik vervolgens op de **knop Connectorpagina** openen.

1. Volg de stapsgewijste instructies in de [Installatiehandleiding voor Alcide kAudit.](https://awesomeopensource.com/project/alcideio/kaudit?categoryPage=29#before-installing-alcide-kaudit)

1. Wanneer u wordt gevraagd om de werkruimte-id en de primaire sleutel, kunt u deze kopiëren vanaf de pagina Alcide kAudit-gegevensconnector.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="Werkruimte-id en primaire sleutel":::

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat er een verbinding tot stand is gebracht, worden de gegevens weergegeven in **Logboeken** onder de volgende gegevenstypen in **CustomLogs:**

- **alcide_kaudit_detections_1_CL** - Alcide kAudit-detecties 
- **alcide_kaudit_activity_1_CL-** Activiteitenlogboeken van Alcide kAudit
- **alcide_kaudit_selections_count_1_CL-** Aantal alcide kAudit-activiteiten
- **alcide_kaudit_selections_details_1_CL** - Details van alcide kAudit-activiteit

Als u het relevante schema in Logboeken voor de Alcide kAudit wilt gebruiken, zoekt u naar de hierboven genoemde gegevenstypen.

Het kan tot 20 minuten duren voordat uw logboeken worden weergegeven in Log Analytics.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Alcide kAudit verbindt met Azure Sentinel. Als u optimaal wilt profiteren van de mogelijkheden die zijn ingebouwd in deze gegevensconnector, klikt u op het tabblad **Volgende stappen** op de pagina gegevensconnector. Hier vindt u enkele kant-en-klaar voorbeeldquery's, zodat u aan de slag kunt gaan met het vinden van nuttige informatie.

Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen om](tutorial-monitor-your-data.md) uw gegevens te bewaken.
