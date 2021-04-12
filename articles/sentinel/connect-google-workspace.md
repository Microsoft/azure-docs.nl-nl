---
title: Gegevens van Google Workspace (G suite) verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over hoe u de data connector van de Google-werk ruimte (G suite) kunt gebruiken om activiteiten voor Google Workspace-activiteit op te nemen in azure Sentinel. Bekijk Google Workspace-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 4ada570502d913283ba9ee4cc4c65b7bdd853935
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101744684"
---
# <a name="connect-your-google-workspace-to-azure-sentinel"></a>Uw Google-werk ruimte verbinden met Azure Sentinel

> [!IMPORTANT]
> De Google Workspace-connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

De Data Connector voor [Google-werk ruimte (voorheen G-suite)](https://workspace.google.com/) biedt de mogelijkheid om activiteiten voor Google Workspace-activiteit op te nemen in azure Sentinel via de rest API. De connector geeft deze [gebeurtenissen](https://developers.google.com/admin-sdk/reports/reference/rest/v1/activities) zichtbaar in uw SOC, waarmee u potentiële beveiligings Risico's kunt onderzoeken, het gebruik van de samen werking van uw team kunt analyseren, configuratie problemen kunt diagnosticeren en bijhouden wie zich aanmeldt en wanneer, beheerders activiteiten analyset, begrijpen hoe gebruikers inhoud maken en delen, en meer gebeurtenissen in uw organisatie kunnen bekijken.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

Als u Google Workspace-gegevens wilt verzamelen, moet u over de volgende machtigingen en referenties beschikken:

- Lees-en schrijf machtigingen voor uw Azure-Sentinel-werk ruimte.

- Lees machtigingen voor gedeelde sleutels voor de werk ruimte. [Meer informatie over werkruimte sleutels](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Lees-en schrijf machtigingen voor Azure Functions om een functie-app te maken. Meer [informatie over Azure functions](../azure-functions/index.yml).

- De **GooglePickleString** -referentie is vereist voor rest API. Meer [informatie over Google rest API](https://developers.google.com/admin-sdk/reports/v1/reference/activities). [Meer informatie over het verkrijgen van referenties](https://developers.google.com/admin-sdk/reports/v1/quickstart/python).

## <a name="configure-and-connect-google-workspace"></a>Google-werk ruimte configureren en verbinden

Google Workspace kan Logboeken rechtstreeks integreren en exporteren naar Azure Sentinel met behulp van een Azure-functie-app.

> [!NOTE]
> Deze connector maakt gebruik van Azure Functions om verbinding te maken met de Google-rapporten-API om activiteiten gebeurtenissen te verzamelen in azure Sentinel. Dit kan leiden tot extra kosten voor gegevens opname. Controleer de pagina met [prijzen voor Azure functions](https://azure.microsoft.com/pricing/details/functions/) voor meer informatie.

1. Klik in de Azure-Sentinel Portal op **Data connectors**. 

1. Selecteer **Google-werk ruimte (G suite) (preview)** in de galerie connectors en selecteer de **pagina connector openen**.

1. Volg de stappen die worden beschreven in de sectie **configuratie** van de pagina connector.

## <a name="validate-connectivity-and-find-your-data"></a>Connectiviteit valideren en uw gegevens zoeken

Het kan Maxi maal 20 minuten duren voordat u opgenomen gegevens in azure Sentinel kunt zien.

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie **aangepaste logboeken** , in de volgende tabellen:
- `GWorkspace_ReportsAPI_admin_CL`
- `GWorkspace_ReportsAPI_calendar_CL`
- `GWorkspace_ReportsAPI_drive_CL`
- `GWorkspace_ReportsAPI_login_CL`
- `GWorkspace_ReportsAPI_mobile_CL`
- `GWorkspace_ReportsAPI_token_CL`
- `GWorkspace_ReportsAPI_user_accounts_CL`

Deze gegevens connector is afhankelijk van een parser op basis van een Kusto-functie die naar verwachting werkt. [Volg deze stappen](https://aka.ms/sentinel-GWorkspaceReportsAPI-parser) om de alias van de **GWorkspaceActivityReports** Kusto-functies te maken.

Zie het tabblad **volgende stappen** op de pagina connector voor een aantal nuttige voorbeeld query's.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u met Google-werk ruimte verbinding maakt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
