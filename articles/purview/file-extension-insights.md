---
title: Bestands extensie rapportage over uw gegevens in azure controle sfeer liggen met behulp van controle sfeer liggen Insights
description: In deze hand leiding wordt beschreven hoe u de rapportage van de controle sfeer liggen-bestands extensie kunt weer geven en gebruiken voor uw gegevens.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: 5cbfb41d50e055f745864e4d5f8bc15a55d925e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668566"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Inzichten over uw gegevens van Azure controle sfeer liggen 

In deze hand leiding wordt beschreven hoe u toegang kunt krijgen tot inzichten over de bestands extensies of bestands typen die in uw gegevens worden gevonden, kunt lezen, bekijken en filteren.

Ondersteunde gegevens bronnen zijn onder andere: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Amazon S3 buckets

In deze hand leiding leert u het volgende:
> [!div class="checklist"]
> * Start uw controle sfeer liggen-account vanuit Azure 
> - Bekijk de bestands extensie inzichten op uw gegevens
> - Inzoomen voor meer informatie over de bestands extensie voor uw gegevens

## <a name="prerequisites"></a>Vereisten 

Voordat u aan de slag gaat met controle sfeer liggen Insights, moet u ervoor zorgen dat u de volgende stappen hebt uitgevoerd:

- Stel uw Azure-resources in en vul de relevante accounts in met test gegevens

- Een scan voor de test gegevens in elke gegevens bron instellen en volt ooien. Zie voor meer informatie [gegevens bronnen beheren in azure controle sfeer liggen (preview)](manage-data-sources.md) en [een set met scan regels maken](create-a-scan-rule-set.md).

- Meld u aan bij controle sfeer liggen met een account met een [gegevens lezer of gegevens curator rol](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles).


Zie [gegevens bronnen beheren in azure controle sfeer liggen (preview)](manage-data-sources.md)voor meer informatie.

## <a name="use-purview-file-extension-insights"></a>Inzichten van controle sfeer liggen-bestands extensie gebruiken

Bij het scannen van uw assets kan Azure controle sfeer liggen de bestands typen die worden gevonden in uw data onroerend goed detecteren en krijgt u meer informatie over elk bestands type. Details bevatten het aantal bestanden van elk type dat u hebt, waar deze bestanden zich bevinden en of ze scannable zijn voor gevoelige gegevens.

> [!NOTE]
> Nadat u uw bron typen hebt gescand, geeft u de **bestands extensie** inzicht in enkele uren om de nieuwe activa weer te geven.

**Inzicht in de bestands extensie weer geven:**

1. Ga naar het scherm van het **Azure controle sfeer liggen** - [exemplaar in het Azure Portal](https://aka.ms/purviewportal) en selecteer uw controle sfeer liggen-account.

1. Selecteer op de pagina **overzicht** in de sectie **aan de slag** de tegel **controle sfeer liggen-account starten** .

1. Selecteer in controle sfeer liggen het menu-item **Insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: aan de linkerkant om toegang te krijgen tot uw **inzichten** -gebied.
    
1. Selecteer in **inzichten** het tabblad **Bestands extensies** .

    In het rapport wordt een samen vatting weer gegeven van het aantal unieke bestands extensies dat is gevonden, evenals een grafiek met de belangrijkste 10 uitbrei dingen die zijn geselecteerd voor de geselecteerde periode (standaard: 30 dagen).

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="Rapport bestands extensie-overzicht" lightbox="media/file-extension-insights/file-extension-overview.png":::

    Ga op een van de volgende manieren te werk om meer te weten te komen:

    - Selecteer de **tijd** kiezer boven aan het rapport om de tijds Panne te wijzigen waarvoor de bestands extensies zijn gevonden.
    
    - Selecteer **weer gave meer** onder de grafiek om een volledige lijst met gevonden bestands extensies weer te geven. Zie voor meer informatie [bestand extensie Insights DrillDown](#file-extension-insights-drilldown). 

### <a name="file-extension-insights-drilldown"></a>Analyse van bestands extensie inzichten

Wanneer u de gegevens op hoog niveau hebt bekeken over de bestands typen die in uw gegevens bestand zijn gevonden, inzoomen voor meer informatie over waar ze zich bevinden en of ze kunnen worden gescand op gevoelige gegevens.

Bijvoorbeeld:

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="Rapport bestands extensie-DrillDown" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

Het raster bevat Details voor elke gevonden bestands extensie, waaronder:

- **Aantal bestanden**. Het aantal bestanden met de opgegeven extensie.
- **Scannen van inhoud**. Hiermee wordt aangegeven of de bestands extensie wordt ondersteund voor het scannen van gevoelige gegevens.
- **Abonnementen**. Het aantal abonnementen waarvoor de opgegeven extensie is gevonden.
- **Bronnen**. Het aantal bronnen dat met de opgegeven bestands extensie is gevonden.



Gebruik de filters boven het raster om de weer gegeven gegevens te filteren:

|Optie  |Beschrijving  |
|---------|---------|
|**Filteren op tref woord**     |    Voer tekst in het vak **filteren op tref woord**  in om de bestands typen op naam te filteren. Als u bijvoorbeeld alleen Pdf's wilt weer geven, voert u in `PDF` .     |
|**Tijd**        | Selecteer deze optie om te filteren op een specifieke tijds Panne voor het moment waarop uw gegevens zijn gemaakt. <br>**Standaard:** 30 dagen  |
|**Bestandsextensie**     |Selecteer deze optie om het raster te filteren op een of meer bestands typen.        |
|**Bronnen**    |Selecteren om het raster te filteren op basis van de specifieke gegevens bronnen. |
|**Inhoud scannen**     |Selecteer **ondersteund** of **niet ondersteund** om alleen bestands typen weer te geven die verder kunnen worden gescand op gevoelige gegevens of gegevens die niet kunnen worden gescand, zoals **. cert** -of **jpg** -bestanden. |
| | |

Selecteer de optie **kolommen bewerken** boven de filters :::image type="icon" source="media/insights/ico-columns.png" border="false"::: om meer of minder kolommen in het raster weer te geven of om de volg orde te wijzigen. 

Als u het raster wilt sorteren, selecteert u een kolomkop om op die kolom te sorteren.
## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure controle sfeer liggen Insight-rapporten
> [!div class="nextstepaction"]
> [Inzichten in woorden lijst](glossary-insights.md)

> [!div class="nextstepaction"]
> [Inzichten scannen](scan-insights.md)

> [!div class="nextstepaction"]
> [Classificatie inzichten](./classification-insights.md)

> [!div class="nextstepaction"]
> [Gevoeligheids label Insights](sensitivity-insights.md)
