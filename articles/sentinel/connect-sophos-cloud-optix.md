---
title: Sophos Cloud Optix-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Sophos Cloud Optix-connector om <PRODUCT NAME> Logboeken in te trekken bij Azure Sentinel. Bekijk <PRODUCT NAME> gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.date: 01/26/2021
ms.author: yelevin
ms.openlocfilehash: c66205ffd9bd5a742d645cbf2f9251a44329a16e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700871"
---
# <a name="connect-your-sophos-cloud-optix-to-azure-sentinel"></a>Uw Sophos Cloud Optix verbinden met Azure Sentinel

> [!IMPORTANT]
> De Sophos Cloud Optix-connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

Met Sophos Cloud Optix connector kunt u eenvoudig al uw Sophos Cloud Optix Security-oplossings logboeken met uw Azure-Sentinel verbinden, om Dash boards weer te geven, aangepaste waarschuwingen te maken en het onderzoek te verbeteren.  Deze mogelijkheid biedt u meer inzicht in de Cloud beveiliging en nalevings postuur van uw organisatie en verbetert de mogelijkheden van uw Cloud beveiligings bewerking. Integratie tussen Sophos Cloud Optix en Azure Sentinel maakt gebruik van REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-sophos-cloud-optix"></a>Sophos Cloud Optix configureren en verbinden

Sophos Cloud Optix kan Logboeken rechtstreeks integreren en exporteren naar Azure Sentinel.

1. Klik in de Azure Sentinel-Portal op **Data connectors** en selecteer **Sophos Cloud Optix (preview)**.

1. Selecteer de **pagina connector openen**.

1. Kopieer de **werk ruimte-id** en de **primaire sleutel** van de connector pagina en sla deze op.

1. Volg de instructies van Sophos om te [integreren met Microsoft Azure Sentinel](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html) (vanaf de derde stap).

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken** onder de sectie **aangepaste logboeken** in de tabel *SophosCloudOptix_CL* .

Als u een query wilt uitvoeren op Sophos Cloud Optix-gegevens, voert u `SophosCloudOptix_CL` in het query venster in. Zie het tabblad **volgende stappen** op de pagina connector en de [Sophos-documentatie](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html)voor een aantal nuttige query voorbeelden.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Sophos Cloud Optix kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
