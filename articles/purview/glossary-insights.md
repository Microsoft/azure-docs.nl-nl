---
title: Een woordenlijst rapport over uw gegevens met behulp van controle sfeer liggen Insights
description: In deze hand leiding wordt beschreven hoe u controle sfeer liggen Insights-woorden lijst rapporten kunt weer geven en gebruiken voor uw gegevens.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: eb1d59ae41b04be60dec90aaee4b2305b6d39ca6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095847"
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

De **woorden lijst Insights** biedt u als zakelijke gebruiker waardevolle informatie om een goed gedefinieerde woorden lijst voor uw organisatie te onderhouden.

1. Het rapport begint met **kpi's op hoog niveau** waarin de **_totale termen_*_ in uw controle sfeer liggen-account* worden weer gegeven, met _goedgekeurde voor waarden zonder activa_*_ en _*_verlopen voor waarden met activa_**. Elk van deze waarden helpt u bij het identificeren van de status van uw woorden lijst.

   :::image type="content" source="./media/glossary-insights/glossary-kpi.png" alt-text="KPI van woorden lijst Insights weer geven"::: 


2. De sectie **moment opname van de voor waarden** (weer gegeven hierboven) toont u de term status als **_concept_*_, _*_goedgekeurde_*_, _*_waarschuwing_*_ en _*_verlopen_** voor termen met activa en voor waarden zonder activa.

3. Klik op **weer gave meer** om de naam van de term met de verschillende status te zien en meer informatie over de **experts *_ en _*_deskundigen_**. 

   :::image type="content" source="./media/glossary-insights/glossary-view-more.png" alt-text="Moment opname van de voor waarden met en zonder assets":::  

4. Wanneer u op ' meer weer geven ' klikt voor ***goedgekeurde voor waarden met activa** _, kunt u met inzichten navigeren naar de pagina met de termen voor _ *woorden lijst**, van waaruit u verder kunt navigeren naar de lijst met assets met de gekoppelde voor waarden. 

   :::image type="content" source="./media/glossary-insights/navigate-to-glossary-detail.png" alt-text="Inzichten in woorden lijst"::: 

4. Bekijk op de pagina terminologie Insights een distributie van **onvolledige termen** op gegevens type ontbreekt. In de grafiek wordt het aantal voor waarden weer gegeven met **_ontbrekende definitie_*_, _*_ontbrekende expert_*_, _*_ontbrekende_-*en* ontbrekende _meerdere_** velden.

1. Klik op ***meer weer geven** _ van _ * onvolledige voor waarden * * om de voor waarden weer te geven waarvoor informatie ontbreekt. U kunt naar de detail pagina van de woordenlijst term navigeren om de ontbrekende gegevens in te voeren en ervoor te zorgen dat de woordenlijst term is voltooid.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het maken van een woordenlijst term via een [woorden lijst](./how-to-create-import-export-glossary.md)