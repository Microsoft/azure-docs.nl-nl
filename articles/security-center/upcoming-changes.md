---
title: Belangrijke wijzigingen afkomstig van Azure Security Center
description: Aanstaande wijzigingen in Azure Security Center waarvan u mogelijk op de hoogte moet zijn en waarmee u mogelijk rekening moet houden
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: memildin
ms.openlocfilehash: df863372cbf7abfb6fee145b7d13bb00d8deb074
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380161"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Belangrijke aanstaande wijzigingen aan Azure Security Center

> [!IMPORTANT]
> De informatie op deze pagina heeft betrekking op producten of functies van voorlopige versies, die aanzienlijk kunnen worden gewijzigd voordat ze commercieel worden vrijgegeven, als dat ooit al gebeurt. Microsoft biedt geen verplichtingen of garanties, uitdrukkelijk of impliciet, met betrekking tot de informatie die hier wordt verstrekt.

Op deze pagina vindt u informatie over de wijzigingen die zijn gepland voor Security Center. Hierin worden de geplande wijzigingen in het product beschreven die van invloed kunnen zijn op zaken als uw beveiligde score of werkstromen.

Als u op zoek bent naar de nieuwste opmerkingen bij de release, vindt u deze in [Wat is er nieuw in Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Geplande wijzigingen

### <a name="system-updates-should-be-installed-on-your-machines-recommendation-getting-sub-recommendations"></a>De aanbeveling 'Er moeten systeemupdates worden geïnstalleerd op uw computers' krijg subaanbevelingen

#### <a name="summary"></a>Samenvatting

| Aspect | Details |
|---------|---------|
|Aankondigingsdatum | 9 november 2020  |
|Datum voor deze wijziging  |  Tweede helft van november 2020 |
|Impact     | Tijdens de overgang van de huidige versie van deze aanbeveling naar de vervanging kan uw beveiligingsscore veranderen. |
|  |  |

Er wordt een verbeterde versie uitgebracht van de aanbeveling **Er moeten systeemupdates worden geïnstalleerd op uw computers**. Met de nieuwe versie wordt de huidige versie in het beveiligingsbeheer voor het toepassen van systeemupdates *vervangen* en worden de volgende verbeteringen aangebracht:

- Subaanbevelingen voor elke ontbrekende update
- Een opnieuw ontworpen ervaring op de Azure Security Center-pagina's van Azure Portal
- Uitgebreide gegevens voor de aanbeveling van Azure Resource Graph

#### <a name="transition-period"></a>Overgangsperiode

Er is een overgangsperiode van 36 uur (bij benadering). Om mogelijke onderbrekingen te minimaliseren, is het de planning om de update plaats te laten vinden gedurende een weekend. De overgang kan van invloed zijn op uw beveiligingsscores.

#### <a name="redesigned-portal-experience"></a>Opnieuw ontworpen portalervaring

De pagina met aanbevelingsinformatie voor **Er moeten systeemupdates worden geïnstalleerd op uw computer** bevat de lijst met resultaten zoals hieronder wordt weergegeven. Wanneer u één resultaat selecteert, wordt het deelvenster Details geopend met een koppeling naar de herstelgegevens en een lijst met betrokken resources.

:::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Een van de subaanbevelingen in de portalervaring openen voor de bijgewerkte aanbeveling":::


#### <a name="richer-data-from-azure-resource-graph"></a>Uitgebreidere gegevens van Azure Resource Graph

Azure Resource Graph is een service in Azure die is ontworpen om efficiënt resources te verkennen. U kunt ARG gebruiken om op schaal een query uit te voeren in een bepaalde set abonnementen, zodat u uw omgeving effectief kunt beheren. 

Voor Azure Security Center kunt u gebruikmaken van ARG en de [Kusto Query Language (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) om query's uit te voeren op een breed scala aan postuurgegevens.

Als u een query uitvoert op de huidige versie van **Er moeten systeemupdates worden geïnstalleerd op uw computer**, is de enige informatie die beschikbaar is van ARG dat de aanbeveling op een computer moet worden hersteld. Wanneer de bijgewerkte versie wordt uitgebracht, worden alle ontbrekende systeemupdates gegroepeerd op machine, geretourneerd met de volgende query.

```kusto
securityresources
| where type =~ "microsoft.security/assessments/subassessments"
| where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
| where properties.status.code == "Unhealthy"
```

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is er nieuw in Azure Security Center?](release-notes.md) voor alle recente wijzigingen aan het product.