---
title: Onbewerkte gegevens van Microsoft 365 Defender verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het opnemen van onbewerkte gebeurtenis gegevens van Microsoft 365 Defender in azure Sentinel.
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
ms.openlocfilehash: 756c245fe06ae81545a125dd98f30fb27fdff2dd
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655576"
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

Met de nieuwe [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) -connector kunt u **geavanceerde jacht** -logboeken streamen-een type onbewerkte gebeurtenis gegevens-van Microsoft 365 Defender naar Azure Sentinel. 

Met de integratie van [micro soft Defender voor eind punt (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) in het beveiligings paraplu van Microsoft 365 Defender kunt u nu uw micro soft Defender voor [geavanceerde jacht](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) -gebeurtenissen verzamelen met behulp van de Microsoft 365 Defender connector, en deze direct in een nieuwe, door het doel gemaakte tabellen in uw Azure Sentinel-werk ruimte. Deze tabellen zijn gebaseerd op hetzelfde schema dat wordt gebruikt in de Microsoft 365 Defender-Portal, zodat u volledige toegang hebt tot de volledige set geavanceerde jacht-logboeken, zodat u het volgende kunt doen:

- U kunt eenvoudig uw bestaande micro soft Defender ATP-query's voor geavanceerde jacht kopiëren naar Azure-Sentinel.

- Gebruik de onbewerkte gebeurtenis Logboeken om extra inzichten te bieden voor uw waarschuwingen, jacht en onderzoek en het correleren van gebeurtenissen met gegevens uit extra gegevens bronnen in azure Sentinel.

- Sla de logboeken op met een verbeterde Bewaar periode van meer dan micro soft Defender voor eind punt of de standaard retentie van Microsoft 365 Defender van 30 dagen. U kunt dit doen door de Bewaar periode van uw werk ruimte te configureren of door de Bewaar periode per tabel in Log Analytics te configureren.

> [!IMPORTANT]
>
> De Microsoft 365 Defender-connector bevindt zich momenteel in de open bare preview. Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- U moet een geldige licentie voor micro soft Defender voor eind punt hebben, zoals beschreven in [micro soft Defender voor endpoint-implementatie instellen](/windows/security/threat-protection/microsoft-defender-atp/licensing). 

- Aan uw gebruiker moet de rol van globale beheerder zijn toegewezen voor de Tenant (in Azure Active Directory).

## <a name="connect-to-microsoft-365-defender"></a>Verbinding maken met Microsoft 365 Defender

Als micro soft Defender voor het eind punt is geïmplementeerd en uw gegevens worden opgenomen, kunnen de gebeurtenis Logboeken eenvoudig worden gestreamd naar Azure Sentinel.

1. In azure Sentinel selecteert u **gegevens connectors**, selecteert u **Microsoft 365 Defender (preview)** in de galerie en selecteert u de **pagina connector openen**.

1. De volgende typen gebeurtenissen kunnen worden verzameld uit de bijbehorende geavanceerde jacht-tabellen. Schakel de selectie vakjes in van de gebeurtenis typen die u wilt verzamelen:

    | Type gebeurtenissen | Tabelnaam |
    |-|-|
    | Computer gegevens (inclusief OS-gegevens) | DeviceInfo |
    | Netwerk eigenschappen van machines | DeviceNetworkInfo |
    | Proces maken en gerelateerde gebeurtenissen | DeviceProcessEvents |
    | Netwerk verbinding en gerelateerde gebeurtenissen | DeviceNetworkEvents |
    | Bestanden maken, wijzigen en andere bestandssysteem gebeurtenissen | DeviceFileEvents |
    | Register vermeldingen maken en wijzigen | DeviceRegistryEvents |
    | Aanmeldingen en andere verificatie gebeurtenissen | DeviceLogonEvents |
    | DLL-gebeurtenissen laden | DeviceImageLoadEvents |
    | Aanvullende typen gebeurtenissen | DeviceEvents |
    |

1. Klik op **wijzigingen Toep assen**. 

1. Als u een query wilt uitvoeren voor de geavanceerde jacht-tabellen in Log Analytics, voert u de naam van de tabel in de bovenstaande lijst in het query venster in.

## <a name="verify-data-ingestion"></a>Gegevens opname controleren

De gegevens grafiek op de connector pagina geeft aan dat u gegevens opneemt. U ziet dat er één regel wordt weer gegeven waarin gebeurtenis volumes worden geaggregeerd voor alle ingeschakelde tabellen. Wanneer u de connector hebt ingeschakeld, kunt u de volgende KQL-query gebruiken om een vergelijk bare grafiek van het gebeurtenis volume te genereren voor één tabel (Wijzig de tabel *DeviceEvents* in de gewenste tabel van uw keuze):

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

Op het tabblad **volgende stappen** ziet u een aantal voor beelden van query's die zijn opgenomen. U kunt ze uitvoeren op de plaats of wijzigen en opslaan.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u onbewerkte gebeurtenis gegevens van micro soft Defender voor eind punt in azure Sentinel kunt ophalen met behulp van de Microsoft 365 Defender connector. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](./tutorial-detect-threats-built-in.md).