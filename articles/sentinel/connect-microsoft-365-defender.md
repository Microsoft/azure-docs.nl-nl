---
title: Microsoft 365 Defender-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het opnemen van incidenten, waarschuwingen en onbewerkte gebeurtenis gegevens van Microsoft 365 Defender in azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 6500805a4dc7e26f5e1bc601df9ea78279ae17e9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709339"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Gegevens verbinden van Microsoft 365 Defender naar Azure Sentinel

> [!IMPORTANT]
>
> **Microsoft 365 Defender** was voorheen bekend als **micro soft Threat Protection** of **MTP**.
>
> **Micro soft Defender voor eind punt** was voorheen bekend als **micro soft Defender Advanced Threat Protection** of **MDATP**.
>
> U ziet mogelijk dat de oude namen gedurende een bepaalde periode nog steeds worden gebruikt.

## <a name="background"></a>Achtergrond

Met de Azure Sentinel [Microsoft 365 Defender-connector (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) met incident integratie kunt u alle M365D-incidenten en waarschuwingen streamen naar Azure Sentinel en blijven de incidenten gesynchroniseerd tussen beide portals. M365D-incidenten omvatten al hun waarschuwingen, entiteiten en andere relevante informatie, en ze zijn verrijkt en groeperen waarschuwingen van M365D's Component Services **micro soft Defender voor eind punt**, **micro soft Defender voor identiteit**, **micro soft defender voor Office 365** en **Microsoft Cloud app Security**.

Met de connector kunt u ook **geavanceerde jacht** -gebeurtenissen van micro soft Defender naar het eind punt streamen naar Azure Sentinel, zodat u met behulp van MDE-geavanceerde zoek query's kunt kopiëren naar Azure-Sentinel, verrijkt Sentinel-waarschuwingen met MDE-onbewerkte gebeurtenis gegevens om extra inzichten te bieden en de logboeken op te slaan met een verbeterde Bewaar periode in log Analytics.

Zie [Microsoft 365 Defender Integration with Azure Sentinel](microsoft-365-defender-sentinel-integration.md)(Engelstalig) voor meer informatie over incident integratie en geavanceerde jacht-gebeurtenissen verzamelen.

> [!IMPORTANT]
>
> De Microsoft 365 Defender-connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

## <a name="prerequisites"></a>Vereisten

- U moet een geldige licentie voor Microsoft 365 Defender hebben, zoals beschreven in [Microsoft 365 Defender-vereisten](/microsoft-365/security/mtp/prerequisites). 

- U moet een **globale beheerder** of een **beveiligings beheerder** zijn in azure Active Directory.

## <a name="connect-to-microsoft-365-defender"></a>Verbinding maken met Microsoft 365 Defender

1. In azure Sentinel selecteert u **gegevens connectors**, selecteert u **Microsoft 365 Defender (preview)** in de galerie en selecteert u de **pagina connector openen**.

1. Klik onder **configuratie** in de sectie problemen **met incidenten &** op de knop **incidenten met de melding & waarschuwingen** .

1. Om het dupliceren van incidenten te voor komen, is het raadzaam om het selectie vakje in te **scha kelen met alle regels voor het maken van micro soft-incidenten voor deze producten.**

    > [!NOTE]
    > Wanneer u de Microsoft 365 Defender-connector inschakelt, worden alle connectors van de M365D-onderdelen (degene die aan het begin van dit artikel worden genoemd) automatisch op de achtergrond aangesloten. Als u een van de connectors van de onderdelen wilt loskoppelen, moet u eerst de verbinding van de Microsoft 365 Defender-connector verbreken.

1. Als u een query wilt uitvoeren op M365 Defender-incident gegevens, gebruikt u de volgende instructie in het query venster:
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Als u geavanceerde jacht-gebeurtenissen van micro soft Defender voor het eind punt wilt verzamelen, kunnen de volgende typen gebeurtenissen worden verzameld uit de bijbehorende geavanceerde jacht-tabellen.

    1. Schakel de selectie vakjes in van de tabellen met de gebeurtenis typen die u wilt verzamelen:

       | Tabelnaam | Type gebeurtenissen |
       |-|-|
       | DeviceInfo | Computer gegevens (inclusief OS-gegevens) |
       | DeviceNetworkInfo | Netwerk eigenschappen van machines |
       | DeviceProcessEvents | Proces maken en gerelateerde gebeurtenissen |
       | DeviceNetworkEvents | Netwerk verbinding en gerelateerde gebeurtenissen |
       | DeviceFileEvents | Bestanden maken, wijzigen en andere bestandssysteem gebeurtenissen |
       | DeviceRegistryEvents | Register vermeldingen maken en wijzigen |
       | DeviceLogonEvents | Aanmeldingen en andere verificatie gebeurtenissen |
       | DeviceImageLoadEvents | DLL-gebeurtenissen laden |
       | DeviceEvents | Aanvullende typen gebeurtenissen |
       | DeviceFileCertificateInfo | Certificaat gegevens van ondertekende bestanden |
       |

    1. Klik op **wijzigingen Toep assen**.

    1. Als u een query wilt uitvoeren voor de geavanceerde jacht-tabellen in Log Analytics, voert u de naam van de tabel in de bovenstaande lijst in het query venster in.

## <a name="verify-data-ingestion"></a>Gegevens opname controleren

De gegevens grafiek op de connector pagina geeft aan dat u gegevens opneemt. U ziet dat er één regel wordt weer gegeven voor incidenten, waarschuwingen en gebeurtenissen, en de gebeurtenissen regel is een aggregatie van het gebeurtenis volume over alle ingeschakelde tabellen. Wanneer u de connector hebt ingeschakeld, kunt u de volgende KQL-query's gebruiken om meer specifieke grafieken te genereren.

Gebruik de volgende KQL-query voor een grafiek van de binnenkomende M365 Defender-incidenten:

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

Gebruik de volgende KQL-query om een grafiek van gebeurtenis volume te genereren voor één tabel (Wijzig de tabel *DeviceEvents* in de gewenste tabel van uw keuze):

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

Op het tabblad **volgende stappen** vindt u enkele nuttige werkmappen, voorbeeld query's en analyse regel sjablonen die zijn opgenomen. U kunt ze uitvoeren op de plaats of wijzigen en opslaan.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Microsoft 365 Defender-incidenten en geavanceerde jacht-gebeurtenis gegevens van micro soft Defender voor eind punt in azure Sentinel integreert met behulp van de Microsoft 365 Defender connector. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](./tutorial-detect-threats-built-in.md).
