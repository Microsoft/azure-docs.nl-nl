---
title: Verbinding maken met meer dan beSECURE-gegevens voor Azure-Sentinel | Microsoft Docs
description: Meer informatie over hoe u de meer dan Security beSECURE Data Connector kunt gebruiken om beSECURE-logboeken te verzamelen in azure Sentinel. Bekijk beSECURE-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 313f201aeabd470850b27d979dc5253f80e82a55
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541156"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Verbind uw beveiligings beSECURE tot Azure Sentinel

> [!IMPORTANT]
> De buitenste beveiligings beSECURE-connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

Met de meer dan een beSECURE-connector kunt u eenvoudig al uw beSECURE Security-oplossings logboeken verbinden met uw Azure-Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van onderzoek. Integratie tussen beSECURE en Azure Sentinel maakt gebruik van REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-besecure"></a>BeSECURE configureren en verbinden

beSECURE kan worden geïntegreerd met en logboeken rechtstreeks naar Azure Sentinel.

1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer in de galerie met **gegevens connectors** de optie meer **beveiliging beSECURE (preview)** en open vervolgens de **pagina connector**.

1. Volg de onderstaande stappen om uw beSECURE-oplossing te configureren voor het verzenden van scan resultaten, scan status en controle spoor logboeken naar Azure Sentinel.

    **Open het menu integratie:**
    1. Klik op de menu optie meer

    1. Server selecteren

    1. Integratie selecteren

    1. Azure Sentinel inschakelen 

    **Geef beSECURE op met de onderverklikker instellingen van Azure:**

      Kopieer de *werk ruimte-id* en *primaire sleutel* waarden van de Azure Sentinel connector-pagina, plak ze in de beSECURE-configuratie en klik op **wijzigen**.
      
      :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{Werk ruimte-ID en primaire sleutel}":::

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie **CustomLogs** , in een of meer van de volgende tabellen:
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

Als u een query wilt uitvoeren voor de beSECURE-Logboeken in analyse regels, query's, onderzoeken of ergens anders in azure Sentinel, voert u een van de bovenstaande tabel namen boven in het query venster in.

## <a name="validate-connectivity"></a>Connectiviteit valideren
Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u beSECURE kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
