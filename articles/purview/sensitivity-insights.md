---
title: Gevoeligheid label rapportage over uw gegevens in azure controle sfeer liggen met behulp van controle sfeer liggen Insights
description: In deze hand leiding wordt beschreven hoe u de controle sfeer liggen-gevoeligheids label rapportage voor uw gegevens kunt weer geven en gebruiken.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: bb8ac82b2e59ec86db89c7eba0ce607fcfc0ac2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676555"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>Gevoeligheid label inzichten over uw gegevens in azure controle sfeer liggen

In deze hand leiding wordt beschreven hoe u beveiligings inzichten kunt openen, weer geven en filteren op basis van gevoeligheids labels die op uw gegevens zijn toegepast.

Ondersteunde gegevens bronnen zijn: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, SQL Server, Azure SQL Database, Azure SQL Managed instance, Amazon S3-buckets

In deze hand leiding leert u het volgende:

> [!div class="checklist"]
> - Start uw controle sfeer liggen-account vanuit Azure.
> - Gevoeligheid van inzichten op uw gegevens weer geven
> - Inzoomen voor meer gevoeligheid voor het labelen van Details over uw gegevens

> [!NOTE]
> Gevoeligheids labels gevonden op [Power bi assets](register-scan-power-bi-tenant.md) die worden gescand door controle sfeer liggen, worden momenteel niet weer gegeven in het rapport gevoeligheid labeling Insights. 
>
> Als u de gevoeligheids labels op Power BI activa wilt weer geven, bekijkt u de asset in het [controle sfeer liggen Data Catalog](how-to-search-catalog.md).
> 
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat met controle sfeer liggen Insights, moet u ervoor zorgen dat u de volgende stappen hebt uitgevoerd:

- Stel uw Azure-resources in en vul de relevante accounts in met test gegevens

- [Uitgebreide Microsoft 365 gevoeligheids labels naar assets in azure controle sfeer liggen](create-sensitivity-label.md), en u hebt de labels gemaakt of geselecteerd die u wilt Toep assen op uw gegevens.

- Een scan voor de test gegevens in elke gegevens bron instellen en volt ooien. Zie voor meer informatie [gegevens bronnen beheren in azure controle sfeer liggen (preview)](manage-data-sources.md) en [een set met scan regels maken](create-a-scan-rule-set.md).

- Meld u aan bij controle sfeer liggen met een account met een [gegevens lezer of gegevens curator rol](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles).

Zie [gegevens bronnen beheren in azure controle sfeer liggen (preview)](manage-data-sources.md) en [automatisch labelen voor uw gegevens in azure controle sfeer liggen](create-sensitivity-label.md)voor meer informatie.

## <a name="use-purview-sensitivity-labeling-insights"></a>Controle sfeer liggen-gevoeligheid gebruiken voor het labelen van inzichten

In controle sfeer liggen zijn classificaties vergelijkbaar met de labels van tags en worden ze gebruikt om gegevens te markeren en te identificeren van een specifiek type dat is gevonden in uw data-erfgoed tijdens het scannen.

Met gevoeligheids labels kunt u aangeven hoe gevoelige bepaalde gegevens zich in uw organisatie bevindt. Een specifieke project naam kan bijvoorbeeld zeer vertrouwelijk zijn binnen uw organisatie, terwijl dezelfde term niet vertrouwelijk is voor andere organisaties. 

Classificaties worden rechtstreeks overeenkomen, zoals een sofi-nummer, dat een classificatie heeft van het **sofi-nummer**. 

In contrast worden labels van gevoeligheid toegepast wanneer een of meer classificaties en voor waarden samen worden gevonden. In deze context verwijzen de [voor waarden](/microsoft-365/compliance/apply-sensitivity-label-automatically) naar alle para meters die u kunt definiëren voor ongestructureerde gegevens, zoals **nabijheid van een andere classificatie** en **% betrouw baarheid**. 

Controle sfeer liggen maakt gebruik van dezelfde classificaties, ook wel aangeduid als [gevoelige informatie typen](/microsoft-365/compliance/sensitive-information-type-entity-definitions), als Microsoft 365. Op deze manier kunt u uw bestaande gevoelige labels uitbreiden over uw Azure controle sfeer liggen-assets.

> [!NOTE]
> Nadat u uw bron typen hebt gescand, geeft u met de **gevoeligheid** een aantal uur inzicht in de nieuwe activa.

**Voor het weer geven van gevoeligheid labeling Insights:**

1. Ga naar de start pagina van **Azure controle sfeer liggen** .

1. Selecteer op de pagina **overzicht** in de sectie **aan de slag** de tegel **controle sfeer liggen-account starten** .

1. Selecteer in controle sfeer liggen het menu-item **Insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: aan de linkerkant om toegang te krijgen tot uw **inzichten** -gebied.

1. Selecteer in het gebied **inzichten** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: de optie **gevoeligheids labels** om het rapport controle sfeer liggen **Sensitivity labeling Insights** weer te geven.

    > [!NOTE]
    > Als dit rapport leeg is, hebt u mogelijk uw gevoeligheids labels niet uitgebreid naar Azure controle sfeer liggen. Zie [uw gegevens automatisch labelen in azure controle sfeer liggen](create-sensitivity-label.md)voor meer informatie.

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="Gevoeligheid voor het labelen van inzichten" lightbox="media/insights/sensitivity-labeling-insights.png":::

   De belangrijkste Insights-pagina met **gevoeligheids labels** bevat de volgende gebieden:

   |Gebied  |Beschrijving  |
   |---------|---------|
   |**Overzicht van bronnen met gevoeligheids labels**     |Geeft tegels weer die het volgende bieden: <br>-Het aantal abonnementen dat is gevonden in uw gegevens. <br>-Het aantal unieke gevoeligheids labels dat op uw gegevens is toegepast <br>-Het aantal bronnen waarvoor de gevoeligheids labels zijn toegepast <br>-Het aantal bestanden en tabellen waarop de gevoeligheids labels zijn toegepast|
   |**Belangrijkste bronnen met gelabelde gegevens (afgelopen 30 dagen)**     | Hier ziet u de trend, in de afgelopen 30 dagen, van het aantal bronnen waarvoor de gevoeligheids labels zijn toegepast.       |
   |**Belangrijkste labels toegepast in verschillende bronnen**     |Toont de labels op het hoogste niveau dat in al uw controle sfeer liggen-gegevens resources wordt toegepast. |
   |**Labels op het hoogste niveau toegepast op bestanden**     |Hier worden de belangrijkste labels weer gegeven die worden toegepast op bestanden in uw gegevens.          |
   |**Labels op het hoogste niveau toegepast op tabellen**     | Hier worden de belangrijkste labels weer gegeven die worden toegepast op database tabellen in uw gegevens. |   
   |  **Activiteit labelen**  |  Geeft afzonderlijke grafieken weer voor bestanden en tabellen, elk met het aantal bestanden of tabellen die zijn gemarkeerd in het geselecteerde tijds bestek. <br>**Standaard**: 30 dagen<br>Selecteer de **tijd** filter boven de grafieken om een ander tijds bestek te selecteren dat u wilt weer geven.    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>Gevoeligheid labelen Insights DrillDown

Selecteer in een van de volgende **gevoeligheid labels Insights** -grafieken de koppeling **meer weer geven** om in te zoomen voor meer informatie:

- **Belangrijkste labels toegepast in verschillende bronnen**
- **Labels op het hoogste niveau toegepast op bestanden**
- **Labels op het hoogste niveau toegepast op tabellen**
- **Activiteit labelen > gelabelde gegevens**

Bijvoorbeeld:

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="Analyse label gevoeligheid" lightbox="media/insights/sensitivity-label-drilldown.png":::

Ga op een van de volgende manieren te werk om meer te weten te komen:

|Optie  |Beschrijving  |
|---------|---------|
|**Uw gegevens filteren**     |  Gebruik de filters boven het raster om de weer gegeven gegevens te filteren, met inbegrip van de naam van het label, de naam van het abonnement of het bron type. <br><br>Als u niet zeker weet wat de naam van het label is, kunt u de naam van een deel of alle typen opgeven in het vak **filteren op tref woord** .       |
|**Het raster sorteren** |Selecteer een kolomkop om het raster op die kolom te sorteren. | 
|**Kolommen bewerken**     |  Als u meer of minder kolommen in het raster wilt weer geven, selecteert u **kolommen bewerken** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: en selecteert u vervolgens de kolommen die u wilt weer geven of de volg orde wijzigen.    <br><br>Selecteer een kolomkop om het raster op die kolom te sorteren.   |
|**Meer inzoomen**     | Als u wilt inzoomen op een specifiek label, selecteert u een naam in de kolom **gevoeligheids label** om het **label op bron** rapport weer te geven. <br><br>In dit rapport worden gegevens weer gegeven voor het geselecteerde label, met inbegrip van de bron naam, het bron type, de abonnements-ID en het aantal geclassificeerde bestanden en tabellen.      |
|**Door assets bladeren**     |  Selecteer een of meer labels of bronnen, afhankelijk van het rapport dat u bekijkt, om door de activa te bladeren die zijn gevonden met een specifiek label of een specifieke bron  en selecteer vervolgens in :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: de filters bladeren. <br><br>In de zoek resultaten worden alle gelabelde activa weer gegeven die voor het geselecteerde filter zijn gevonden.  Zie [Search the Azure controle sfeer liggen Data Catalog](how-to-search-catalog.md)voor meer informatie.       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>Integratie van gevoeligheids label met Microsoft 365 naleving

Sluit integratie met de [micro soft-Information Protection](/microsoft-365/compliance/information-protection) die in Microsoft 365 worden aangeboden, betekent dat controle sfeer liggen direct manieren om inzicht in uw gegevens te kunnen uitbreiden en uw gegevens kan classificeren en labelen.

Als u wilt dat uw Microsoft 365 gevoeligheids labels worden uitgebreid naar uw assets in azure controle sfeer liggen, moet u Information Protection voor Azure controle sfeer liggen actief inschakelen in het Microsoft 365 compliance Center.

Zie [uw gegevens automatisch labelen in azure controle sfeer liggen](create-sensitivity-label.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over deze Azure controle sfeer liggen Insight-rapporten:

- [Inzichten in woorden lijst](glossary-insights.md)
- [Inzichten scannen](scan-insights.md)
- [Classificatie inzichten](./classification-insights.md)
- [Inzichten voor bestands extensies](file-extension-insights.md)
