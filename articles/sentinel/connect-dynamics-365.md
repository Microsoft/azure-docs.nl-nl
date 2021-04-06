---
title: Dynamics 365-logboeken verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de activiteiten connector voor Dynamics 365 Common Data Service (CDS) voor het meenemen van gegevens over actieve beheerders-, gebruikers-en ondersteunings activiteiten.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: 018368b6284cf39edec01f0a9a943b8ea15c85d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98104180"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>Dynamics 365-activiteiten logboeken verbinden met Azure Sentinel

De activiteiten connector voor [Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) common data service (CDS) biedt inzicht in de activiteiten van beheerders, gebruikers en ondersteuning, evenals logboek registratie gebeurtenissen van micro soft. Door Dynamics 365 CRM-logboeken te verbinden met Azure Sentinel, kunt u deze gegevens in werkmappen weer geven, gebruiken om aangepaste waarschuwingen te maken en deze te benutten om uw onderzoek proces te verbeteren. Deze nieuwe Azure Sentinel connector verzamelt de gegevens van de Dynamics-CDS van de Office Management API. Ga voor meer informatie over de activiteiten van de Dynamics-CD'S die zijn gecontroleerd in het Power platform naar [inschakelen en gebruik activiteiten logboeken](/power-platform/admin/enable-use-comprehensive-auditing).

> [!IMPORTANT]
>
> De Dynamics 365 Common Data Service-activiteiten connector (CDS) is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor uw Azure-Sentinel-werk ruimte.

- U moet een [micro soft Dynamics 365-productie licentie](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description)hebben. Deze connector is niet beschikbaar voor sandbox-omgevingen.
    - Er is een Microsoft 365 Enterprise [E3-of E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) -abonnement vereist om activiteiten te registreren.

- Gegevens ophalen uit de Office Management API:
    - U moet een globale beheerder van uw Tenant zijn.

    - [Office-controle logboek registratie](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) moet zijn ingeschakeld in het [beveiligings-en nalevings centrum van Office](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance).

## <a name="enable-the-dynamics-365-activities-data-connector"></a>De Dynamics 365-gegevens connector voor activiteiten inschakelen

1. Selecteer in het navigatie menu van de Azure-Sentinel **Data connectors**.

1. Selecteer in de galerie **Data connectors** de optie **Dynamics 365 (preview)** en selecteer vervolgens **connector pagina openen** in het voorbeeld venster.

1. Klik op het tabblad **instructies** onder **configuratie** op **verbinden**. 

    Zodra de connector is geactiveerd, duurt het ongeveer 30 minuten totdat u de gegevens die in de grafiek arriveren, kunt zien. 

    Volgens het [controle logboek van het bedrijf in het nalevings centrum](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log)kan het tot 30 minuten duren of tot 24 uur nadat er een gebeurtenis is opgetreden voor de bijbehorende controle logboek record die in de resultaten wordt geretourneerd.

1. De micro soft Dynamics audit logs vindt u in de `Dynamics365Activity` tabel. Zie de [schema verwijzing](/azure/azure-monitor/reference/tables/dynamics365activity)van de tabel.

## <a name="querying-the-data"></a>Query's uitvoeren op de gegevens

1. Selecteer **Logboeken** in het navigatie menu van de Azure-Sentinel.

1. Voer de volgende query uit om te controleren of Logboeken binnenkomen:

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u de gegevens van Dynamics 365-activiteiten verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met het detecteren van bedreigingen met Azure Sentinel, met behulp [van ingebouwde](tutorial-detect-threats-built-in.md) of [aangepaste](tutorial-detect-threats-custom.md) regels.
