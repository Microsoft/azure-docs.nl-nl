---
title: Cache opschonen in azure front deur Standard/Premium (preview)
description: In dit artikel wordt uitgelegd hoe u de cache van een Azure front-deur standaard/Premium kunt leegmaken.
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: aacbf2ceab8580727b1885bf6533cd74a7c4e60a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099060"
---
# <a name="cache-purging-in-azure-front-door-standardpremium-preview"></a>Cache opschonen in azure front deur Standard/Premium (preview)

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

Azure front deur Standard/Premium slaat assets in cache op tot de TTL (time-to-Live) van het activum verloopt. Wanneer een client een Asset aanvraagt met verlopen TTL, haalt de Azure front-deur omgeving een nieuwe bijgewerkte kopie van de Asset op om de aanvraag te leveren en slaat vervolgens de vernieuwde cache op.

De aanbevolen procedure is om ervoor te zorgen dat uw gebruikers altijd de nieuwste kopie van uw assets verkrijgen. De manier om dat te doen, is om uw assets voor elke update te maken en ze als nieuwe Url's te publiceren. Azure front deur Standard/Premium haalt onmiddellijk de nieuwe assets op voor de volgende client aanvragen. Soms wilt u in de cache opgeslagen inhoud verwijderen uit alle Edge-knoop punten en alle nieuwe bijgewerkte assets afdwingen. De reden dat u de inhoud in de cache wilt verwijderen, is omdat u nieuwe updates hebt aangebracht in uw toepassing of activa wilt bijwerken die onjuiste informatie bevatten.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Controleer de [cache van Azure front-deur](concept-caching.md) om te begrijpen hoe caching werkt.

## <a name="configure-cache-purge"></a>Cache opschonen configureren

1. Ga naar de pagina overzicht van het Azure front deur-profiel met de activa die u wilt verwijderen en selecteer vervolgens **cache leegmaken**.

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-1.png" alt-text="Scherm opname van cache opschonen op de overzichts pagina.":::

1. Selecteer het eind punt en het domein dat u wilt verwijderen uit de Edge-knoop punten. *(U kunt meer dan één domein selecteren)*

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-2.png" alt-text="Scherm opname van de pagina voor het opschonen van de cache.":::

1. Als u alle assets wilt wissen, selecteert u **alle assets voor de geselecteerde domeinen opschonen**. Als dat niet het geval is, voert u in **paden** het pad in van elk activum dat u wilt leegmaken.

Deze indelingen worden ondersteund in de lijsten met te verwijderen paden:

* **Eén pad leegmaken**: afzonderlijke assets opschonen door het volledige pad van de asset (zonder het protocol en domein) op te geven, met de bestands extensie, bijvoorbeeld/pictures/strasbourg.png.
* **Basis domein opschonen**: de hoofdmap van het eind punt met/* in het pad opschonen.

Cache-leegmaak aanvragen op de Azure front deur standaard/Preium zijn niet hoofdletter gevoelig. Daarnaast zijn ze query teken reeks neutraal, wat betekent dat als u een URL opschoont, alle wijzigingen in de query teken reeks worden verwijderd. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een voor deur standaard/Premium](create-front-door-portal.md).
