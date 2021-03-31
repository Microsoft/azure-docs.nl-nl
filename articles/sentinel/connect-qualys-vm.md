---
title: Gegevens van Qualys-beveiligings probleem koppelen aan Azure-Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Qualys-gegevens voor beveiligings problemen met Azure Sentinel.
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
ms.openlocfilehash: 8e11c4182520e143007b46c8a7907b5e71bfb27d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097582"
---
# <a name="connect-your-qualys-vm-to-azure-sentinel-with-azure-function"></a>Uw Qualys-VM verbinden met Azure Sentinel met Azure function

> [!IMPORTANT]
> De Qualys VM-gegevens connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de Qualys-connector voor beveiligings problemen kunt u eenvoudig al uw [QUALYS VM](https://www.qualys.com/apps/vulnerability-management/) -beveiligings oplossings logboeken verbinden met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van onderzoek. Integratie tussen Qualys VM en Azure Sentinel maakt gebruik van Azure Functions om logboek gegevens te verzamelen met behulp van REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-qualys-vm"></a>Qualys VM configureren en verbinden

Azure Functions kunnen gebeurtenissen en logboeken rechtstreeks van Qualys-VM integreren en verzamelen en deze door sturen naar Azure Sentinel.

1. Klik in de Azure-Sentinel-Portal op **Data connectors** en selecteer Qualys-connector voor **beveiligings beheer** .

1. Selecteer de **pagina connector openen**.

1. Volg de instructies op de pagina **Qualys-beveiligings problemen beheer** .

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in Log Analytics onder de tabel **QualysHostDetection_CL** .

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Qualys VM verbindt met Azure Sentinel met behulp van Azure function-apps. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
