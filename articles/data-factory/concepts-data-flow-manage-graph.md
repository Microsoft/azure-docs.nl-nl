---
title: De grafiek toewijzings gegevens stroom beheren
description: De stroom grafiek voor het toewijzen van gegevens effectief beheren en bewerken
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2020
ms.openlocfilehash: f55364fe0eabae4bb1698bd655cde8d3e01b127a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590457"
---
# <a name="managing-the-mapping-data-flow-graph"></a>De grafiek toewijzings gegevens stroom beheren

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Het koppelen van gegevens stromen wordt gemaakt met behulp van een ontwerp oppervlak dat de gegevens stroom grafiek kan weer gegeven. In de grafiek is de transformatie logica van links naar rechts gemaakt en worden extra gegevens stromen boven aan de pagina toegevoegd. Als u een nieuwe trans formatie wilt toevoegen, selecteert u het plus teken aan de rechter benedenhoek van een bestaande trans formatie.

![Canvas](media/data-flow/canvas-2.png)

Als uw gegevens stromen complex worden, kunt u de volgende mechanismen gebruiken om de gegevens stroom grafiek effectief te navigeren en te beheren. 

## <a name="moving-transformations"></a>Trans formaties verplaatsen

Bij het toewijzen van gegevens stromen wordt een reeks verbonden transformatie logica een **stroom** genoemd. Het veld voor de **inkomende stroom** bepaalt welke gegevens stroom de huidige trans formatie bedient. Elke trans formatie heeft een of twee inkomende streams, afhankelijk van de functie en duidt een uitvoer stroom aan. Het uitvoer schema van de binnenkomende stromen bepaalt op welke kolom meta gegevens kunnen worden verwezen door de huidige trans formatie.

![Knoop punt verplaatsen](media/data-flow/move-nodes.png "knoop punt verplaatsen")

In tegens telling tot het pijp lijn-canvas, worden gegevensstroom transformaties niet bewerkt met behulp van slepen en neerzetten. Als u een trans formatie wilt wijzigen in de binnenkomende stroom of verplaatsen, kiest u een andere waarde in de vervolg keuzelijst **inkomende stream** . Wanneer u dit doet, worden alle downstream-trans formaties naast de bewerkte trans formatie verplaatst. De grafiek wordt automatisch bijgewerkt om de nieuwe logische stroom weer te geven. Als u de inkomende stroom wijzigt in een trans formatie die al downstream-trans formatie heeft, wordt er een nieuwe vertakking of parallelle gegevens stroom gemaakt. Meer informatie over [nieuwe vertakkingen in het toewijzen van gegevens stroom](data-flow-new-branch.md).

## <a name="hide-graph-and-show-graph"></a>Grafiek verbergen en grafiek weer geven

Wanneer u uw trans formatie bewerkt, kunt u het configuratie paneel uitvouwen om het hele canvas te verg Roten, zodat de grafiek wordt verborgen. Klik op de omhoog gerichte dubbele punt haak aan de rechter kant van het canvas.

![Grafiek verbergen](media/data-flow/hide-graph.png "grafiek verbergen")

Wanneer de grafiek is verborgen, kunt u tussen trans formaties binnen een stroom scha kelen door op **volgende** of **vorige** te klikken. Klik op de omlaag gerichte dubbele punt haak om de grafiek weer te geven.

![Grafiek weer geven](media/data-flow/show-graph.png "grafiek weer geven")

## <a name="searching-for-transformations"></a>Trans formaties zoeken

Als u snel een trans formatie in uw grafiek wilt vinden, klikt u op het **Zoek** pictogram boven de zoom instelling.

![Scherm afbeelding toont de knop zoeken.](media/data-flow/search-1.png "Zoeken in grafiek")

U kunt zoeken op transformatie naam of beschrijving om een trans formatie te vinden.

![Scherm afbeelding toont het zoekvak.](media/data-flow/search-2.png "Zoeken in grafiek")

## <a name="hide-reference-nodes"></a>Verwijzings knooppunten verbergen

Als uw gegevens stroom een koppelings-, opzoek-, exists-of Union-trans formaties bevat, toont de gegevens stroom referentie knooppunten naar alle inkomende streams. Als u de hoeveelheid benodigde verticale ruimte wilt minimaliseren, kunt u de referentie knooppunten minimaliseren. Hiertoe klikt u met de rechter muisknop op het canvas en selecteert u **referentie knooppunten verbergen**.

![Verwijzings knooppunten verbergen](media/data-flow/hide-reference-nodes.png "Verwijzings knooppunten verbergen")

## <a name="next-steps"></a>Volgende stappen

Nadat u de logica voor de gegevens stroom hebt voltooid, schakelt u de [foutopsporingsmodus](concepts-data-flow-debug-mode.md) in en test u deze in een voor beeld van de gegevens.
