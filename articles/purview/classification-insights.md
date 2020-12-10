---
title: Rapportage over uw gegevens classificeren met behulp van controle sfeer liggen Insights (preview-versie)
description: In deze hand leiding wordt beschreven hoe u controle sfeer liggen Insights-classificatie rapporten kunt weer geven en gebruiken voor uw gegevens.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: bb3c7cc3f51eae90c5b712d224407e639b232fbc
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938882"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>Inzicht in de classificatie van uw gegevens vanuit Azure controle sfeer liggen

In deze hand leiding wordt beschreven hoe u controle sfeer liggen classificatie Insight-rapporten kunt openen, bekijken en filteren voor uw gegevens.

Ondersteunde gegevens bronnen zijn: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Azure Cosmos DB (SQL API), Azure Synapse Analytics (voorheen SQL DW), Azure SQL Database, Azure SQL Managed instance, SQL Server

In deze hand leiding leert u het volgende:

> [!div class="checklist"]
> - Start uw controle sfeer liggen-account vanuit Azure
> - Classificatie inzichten op uw gegevens weer geven
> - Inzoomen voor meer informatie over de classificatie van uw gegevens

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat met controle sfeer liggen Insights, moet u ervoor zorgen dat u de volgende stappen hebt uitgevoerd:

- Stel uw Azure-resources in en vul de relevante accounts in met test gegevens

- Een scan voor de test gegevens in elke gegevens bron instellen en volt ooien 

Zie [gegevens bronnen beheren in azure controle sfeer liggen (preview)](manage-data-sources.md)voor meer informatie.

## <a name="use-purview-classification-insights"></a>Controle sfeer liggen classificatie Insights gebruiken

In azure controle sfeer liggen zijn classificaties vergelijkbaar met de label Tags en worden ze gebruikt om gegevens te markeren en te identificeren van een specifiek type dat is gevonden in uw gegevens voor het scannen.

Controle sfeer liggen maakt gebruik van dezelfde gevoelige informatie typen als Microsoft 365, zodat u uw bestaande beveiligings beleid en-beveiliging kunt uitrekken in uw hele data-erfgoed.

> [!NOTE]
> Nadat u uw bron typen hebt gescand, geeft u een aantal uur de **classificatie** inzicht in de nieuwe activa.

**Classificatie inzichten weer geven:**

1. Ga naar het scherm van het **Azure controle sfeer liggen** - [exemplaar in het Azure Portal](https://aka.ms/purviewportal) en selecteer uw controle sfeer liggen-account.

1. Selecteer op de pagina **overzicht** in de sectie **aan de slag** de tegel **controle sfeer liggen-account starten** .

1. Selecteer in controle sfeer liggen het menu-item **Insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: aan de linkerkant om toegang te krijgen tot uw **inzichten** -gebied.

1. Selecteer in het gebied **inzichten** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: de optie **classificatie** om het rapport controle sfeer liggen **classificatie Insights** weer te geven.

   :::image type="content" source="./media/insights/select-classification-labeling-small.png" alt-text="Rapport over classificatie inzichten" lightbox="media/insights/select-classification-labeling.png":::

   Op de pagina belangrijkste **classificatie Insights** worden de volgende gebieden weer gegeven:

   |Gebied  |Beschrijving  |
   |---------|---------|
   |**Overzicht van bronnen met classificaties**     |Geeft tegels weer die het volgende bieden: <br>-Het aantal abonnementen dat in uw gegevens is gevonden <br>-Het aantal unieke classificaties dat in uw gegevens is gevonden <br>-Het aantal geclassificeerde bronnen gevonden <br>-Het aantal geclassificeerde bestanden dat is gevonden <br>-Het aantal geclassificeerde tabellen gevonden         |
   |**Belangrijkste bronnen met geclassificeerde gegevens (afgelopen 30 dagen)**     |Toont de trend, in de afgelopen 30 dagen, van het aantal bronnen dat is gevonden met geclassificeerde gegevens.            |
   |**Belangrijkste classificatie categorieën per bron**     |Hier wordt het aantal bronnen weer gegeven dat is gevonden per classificatie categorie, zoals **financieel** of **Government**.      |
   |**Belangrijkste classificaties voor bestanden**     |Hier worden de belangrijkste classificaties weer gegeven die zijn toegepast op bestanden in uw gegevens, zoals creditcard nummers of nationale identificatie nummers.         |
   |**Belangrijkste classificaties voor tabellen**     | Hier worden de belangrijkste classificaties weer gegeven die zijn toegepast op tabellen in uw gegevens, zoals persoonlijke identiteits gegevens. |   
   |  **Classificatie activiteit** <br>(bestanden en tabellen) |  Geeft afzonderlijke grafieken weer voor bestanden en tabellen, elk met het aantal bestanden of tabellen dat over de geselecteerde periode is geclassificeerd. <br>**Standaard**: 30 dagen<br>Selecteer de **tijd** filter boven de grafieken om een ander tijds bestek te selecteren dat u wilt weer geven.    |
   |    |    |

## <a name="classification-insights-drilldown"></a>Analyse van classificatie Insights

Selecteer in een van de volgende **classificatie Insights** -grafieken de koppeling **meer weer geven** om in te zoomen voor meer informatie:

- **Belangrijkste classificatie categorieën per bron**
- **Belangrijkste classificaties voor bestanden**
- **Belangrijkste classificaties voor tabellen**
- **Classificatie-activiteit > classificatie gegevens**

Bijvoorbeeld:

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="Alle classificaties weer geven" lightbox="media/insights/view-classifications.png":::

Ga op een van de volgende manieren te werk om meer te weten te komen:

|Optie  |Beschrijving  |
|---------|---------|
|**Uw gegevens filteren**     |  Gebruik de filters boven het raster om de weer gegeven gegevens te filteren, met inbegrip van de classificatie naam, de naam van het abonnement of het bron type. <br><br>Als u niet zeker weet wat de naam is van de classificatie, kunt u de naam van een deel of alle typen opgeven in het vak **filteren op tref woord** .       |
|**Het raster sorteren** |Selecteer een kolomkop om het raster op die kolom te sorteren. | 
|**Kolommen bewerken**     |  Als u meer of minder kolommen in het raster wilt weer geven, selecteert u **kolommen bewerken** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: en selecteert u vervolgens de kolommen die u wilt weer geven of de volg orde wijzigen.   |
|**Meer inzoomen**     | Als u wilt inzoomen op een specifieke classificatie, selecteert u een naam in de kolom **classificatie** om de **classificatie per bron** rapport weer te geven. <br><br>In dit rapport worden gegevens weer gegeven voor de geselecteerde classificatie, met inbegrip van de bron naam, het bron type, de abonnements-ID en het aantal geclassificeerde bestanden en tabellen.      |
|**Door assets bladeren**     |  Als u wilt bladeren door de activa die met een specifieke classificatie of bron zijn gevonden, selecteert u een classificatie of bron, afhankelijk van het rapport dat u bekijkt  en selecteert u vervolgens in :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: de filters bladeren. <br><br>In de zoek resultaten worden alle geclassificeerde assets weer gegeven die voor het geselecteerde filter zijn gevonden.  Zie [Search the Azure controle sfeer liggen Data Catalog](how-to-search-catalog.md)voor meer informatie.       |
| | |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure controle sfeer liggen Insight-rapporten
> [!div class="nextstepaction"]
> [Inzichten in woorden lijst](glossary-insights.md)

> [!div class="nextstepaction"]
> [Inzichten scannen](scan-insights.md)

> [!div class="nextstepaction"]
> [Gevoeligheid voor het labelen van inzichten](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Inzichten voor bestands extensies](file-extension-insights.md)