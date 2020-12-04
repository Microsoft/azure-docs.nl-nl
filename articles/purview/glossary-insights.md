---
title: Een woordenlijst rapport over uw gegevens met behulp van controle sfeer liggen Insights
description: In deze hand leiding wordt beschreven hoe u controle sfeer liggen Insights-woorden lijst rapporten kunt weer geven en gebruiken voor uw gegevens.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: f61d99a61cb50886d70489b586d948bfa751e196
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576771"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Woorden lijst inzichten over uw gegevens in azure controle sfeer liggen

In deze hand leiding wordt beschreven hoe u met controle sfeer liggen terminologie rapporten voor uw gegevens kunt openen, bekijken en filteren.

In deze hand leiding leert u het volgende:

> [!div class="checklist"]
> - Ga naar inzichten vanuit uw controle sfeer liggen-account
> - Een weer gave van uw gegevens in vogel vlucht ophalen

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat met controle sfeer liggen Insights, moet u ervoor zorgen dat u de volgende stappen hebt uitgevoerd:

- Stel uw Azure-resources in en vul het account in met gegevens

- Een scan voor het bron type instellen en volt ooien

- Een woorden lijst instellen en assets koppelen aan termen van de woorden lijst

Zie [gegevens bronnen beheren in azure controle sfeer liggen (preview)](manage-data-sources.md)voor meer informatie.

## <a name="use-purview-glossary-insights"></a>Controle sfeer liggen Glossary Insights gebruiken

In azure controle sfeer liggen kunt u voor waarden voor de woorden lijst maken en deze koppelen aan activa. Later kunt u de terminologie van de woorden lijst bekijken in woorden lijst inzichten. Dit geeft u de status van uw woorden lijst aan op basis van de voor waarden die zijn gekoppeld aan activa. Er wordt ook een overzicht gegeven van de status en de distributie van rollen door het aantal gebruikers.

**Voor het weer geven van woorden lijst Insights:**

1. Ga naar het scherm van het **Azure controle sfeer liggen** - [exemplaar in het Azure Portal](https://aka.ms/purviewportal) en selecteer uw controle sfeer liggen-account.

1. Selecteer op de pagina **overzicht** in de sectie **aan de slag** de tegel **controle sfeer liggen** -account starten.

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="Controle sfeer liggen starten vanuit de Azure Portal":::

1. Op de **Start** pagina van controle sfeer liggen selecteert u de tegel **inzichten weer geven** om toegang te krijgen tot uw **inzichten** - :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: gebied.

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Bekijk uw inzichten in de Azure Portal":::

1. Selecteer in het gebied **inzichten** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: de optie **woorden lijst** om het rapport Insights van de controle sfeer liggen **woorden** lijst weer te geven.

Op de pagina met de **woorden lijst Insights** worden de volgende gebieden weer gegeven:
1. **Kpi's op hoog niveau** om woordenlijst termen en catalogus gebruikers weer te geven

2. De **belangrijkste termen in de woorden lijst en het aantal assets** toont de vijf termen van de woorden lijst met de hieraan gekoppelde activa. Alle andere assets worden verwerkt in de categorie ' overige ' in de grafiek.

3. In termen van de **woorden lijst op term status** ziet u de distributie van termen in de woorden lijst op basis van de status concept, goedgekeurd, waarschuwing en verlopen. 

1. Houd de muis aanwijzer of klik op het segment van de grafiek met een status en noteer het aantal voor waarden met die status.

1. **Distributie van rollen per aantal gebruikers** toont de distributie van rollen op basis van het aantal gebruikers per rol in controle sfeer liggen.

   :::image type="content" source="./media/glossary-insights/glossary-insights1.png" alt-text="Woorden lijst Insights weer geven":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure controle sfeer liggen Insight-rapporten via [Asset Insights](./asset-insights.md)