---
title: Tekst visualisaties Azure Monitor werkmap
description: Meer informatie over alle Azure Monitor werkmap tekst visualisaties.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: e8f2d9495484b781b26962c2946b5bada6c38b4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100609764"
---
# <a name="text-visualizations"></a>Tekstvisualisaties

Met werkmappen kunnen auteurs tekst blokken in hun werkmappen bevatten. De tekst kan een menselijke analyse zijn van telemetrie, informatie om gebruikers te helpen bij het interpreteren van uw gegevens, sectie koppen, enzovoort.

[![Scherm opname van Apdex-tabel met tekst](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

Tekst wordt toegevoegd via een besturings element voor een prijs opwaardering, waarmee het besturings element voor volledige opmaak wordt geboden. Dit zijn onder andere kop-en teken stijlen, hyper links, tabellen, enzovoort.

Bewerkings modus:

![Scherm opname van een tekst stap in de bewerkings modus.](./media/workbooks-text-visualizations/text-edit-mode.png)

Preview-modus:

![Scherm opname van een tekst stap in de bewerkings modus op het tabblad voor beeld.](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>Een besturings element tekst toevoegen

1. Schakel de werkmap over naar de bewerkings modus door te klikken op het item werkbalk opdracht **bewerken** .
2. Gebruik de koppeling **tekst toevoegen** om een besturings element tekst toe te voegen aan de werkmap.
3. Prijs verlaging toevoegen in het veld editor.
4. Gebruik de *tekst stijl* optie om te scha kelen tussen de automatische prijs verlaging en de verpakte prijs met de Azure Portal standaard info/waarschuwing/geslaagd/fout opmaak.
5. Gebruik het tabblad **voor beeld** om te zien hoe uw inhoud eruit ziet. Tijdens het bewerken wordt in het voor beeld de inhoud in een schuif balk gebied weer gegeven om de grootte te beperken; tijdens runtime wordt de inhoud van de prijs verlaging echter uitgebreid om alle benodigde ruimte te vullen, zonder schuif balken.
6. Selecteer de knop **klaar met bewerken** om het bewerken van de stap te volt ooien.

> [!TIP]
> Gebruik dit [Cheat-blad voor prijs verlaging](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) om meer te weten te komen over verschillende opmaak opties.

## <a name="text-styles"></a>Tekst stijlen

De volgende tekst stijlen zijn beschikbaar voor de tekst stap:

| Stijl     | Uitleg                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | Er wordt geen extra opmaak toegepast.                                                      |
| `info`    | De "info"-stijl van de portal, met een  `ℹ` of een vergelijkbaar pictogram en een achtergrond die in het algemeen blauw is.      |
| `error`   | De "fout stijl" van de portal, met een `❌` of meer pictogrammen en een algemeen rode achtergrond.     |
| `success` | De stijl "geslaagd" van de portal, met een `✔` of meer pictogram en normaal gesp roken groene achtergrond.  |
| `upsell`  | De verkoop stijl ' Upsell ' van de portal, met een `🚀` of meer vergelijk bare pictogrammen en meestal paarse achtergrond. |
| `warning` | De "waarschuwing"-stijl van de portal, met een `⚠` of meer vergelijkbaar pictogram en een blauw achtergrond.   |

In plaats van een specifieke stijl te kiezen, kunt u ook een tekst parameter als de bron van de stijl kiezen. De parameter waarde moet een van de bovenstaande tekst waarden zijn. Het ontbreken van een waarde of een niet-herkende waarde wordt als `plain` stijl beschouwd.

Info stijl voor beeld:

![Scherm opname van de stijl van de info.](./media/workbooks-text-visualizations/text-preview-info-style.png)

Voor beeld van waarschuwings stijl:

![Scherm opname van de stijl van de waarschuwing.](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken [van een grafiek in werkmappen](workbooks-chart-visualizations.md).
* Meer informatie over het maken [van een raster in werkmappen](workbooks-grid-visualizations.md).
