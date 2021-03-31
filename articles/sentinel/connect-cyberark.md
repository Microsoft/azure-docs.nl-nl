---
title: CyberArk EPV-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de CyberArk Enter prise Password kluis (EPV) Data Connector om de logboeken van Azure-Sentinel te halen. Bekijk CyberArk EPV-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: aa57963ce369e4c8f84f4aae5f99fe343181ff6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100530498"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>CyberArk Enter prise-wachtwoord kluis (EPV) verbinden met Azure Sentinel

> [!IMPORTANT]
> De CyberArk Enter prise Password kluis (EPV)-gegevens connector in azure Sentinel is momenteel beschikbaar als open bare preview. Deze functie wordt zonder service level agreement gegeven. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de CyberArk syslog-connector kunt u eenvoudig al uw CyberArk Security-oplossings logboeken koppelen aan uw Azure-Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van onderzoek. Integratie tussen CyberArk en Azure Sentinel maakt gebruik van de CEF-gegevens connector om CyberArk syslog-berichten correct te parseren en weer te geven.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-cyberark-epv"></a>CyberArk EPV configureren en verbinden

CyberArk EPV-logboeken worden verzonden vanuit de kluis naar een op Linux gebaseerde logboek server (met rsyslog of syslog-ng) waarop de Log Analytics-agent is geïnstalleerd, die de logboeken naar Azure Sentinel exporteert. Als u een dergelijke server voor het door sturen van logboeken niet hebt, raadpleegt u [deze instructies](connect-cef-agent.md) om er een te maken en uit te voeren.

1. Klik in de Azure Sentinel-Portal op **Data connectors**, selecteer **CyberArk Enter prise Password kluis (EPV) Events (preview)** en open vervolgens de **pagina connector**.

1. Volg de [instructies in de CYBERARK EPV](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) voor het configureren van het verzenden van syslog-gegevens naar de server voor logboek forwarding.

1. Valideer uw verbinding en controleer de gegevens opname met behulp van [deze instructies](connect-cef-verify.md). Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie voor het **Sentinel van Azure** in de tabel *CommonSecurityLog* .

Als u een query wilt uitvoeren voor de CyberArk EPV-Logboeken in Log Analytics, typt u `CommonSecurityLog` boven in het query venster.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u CyberArk Enter prise-wachtwoord kluis logboeken verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
