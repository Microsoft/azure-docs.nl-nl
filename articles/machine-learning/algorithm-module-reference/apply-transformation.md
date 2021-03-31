---
title: 'Trans formatie Toep assen: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module trans formatie Toep assen in Azure Machine Learning om een invoer gegevensset te wijzigen op basis van een eerder berekende trans formatie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/26/2020
ms.openlocfilehash: a5db3935ae445ee7dcf8129eb1d4c75fcb64302f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96003902"
---
# <a name="apply-transformation-module"></a>Transformatie module Toep assen

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Gebruik deze module om een invoer gegevensset te wijzigen op basis van een eerder berekende trans formatie. Deze module is nodig in als u trans formaties wilt bijwerken in ingrijpende pijp lijnen.

Als u bijvoorbeeld z-scores hebt gebruikt voor het normaliseren van uw trainings gegevens met behulp van de module **normaliseren gegevens** , zou u ook de z-Score waarde willen gebruiken die tijdens de Score fase is berekend voor training. In Azure Machine Learning kunt u de normalisatie methode opslaan als een trans formatie en vervolgens **trans formatie Toep assen** gebruiken om de z-Score toe te passen op de invoer gegevens vóór de score.

## <a name="how-to-save-transformations"></a>Trans formaties opslaan

Met de ontwerp functie kunt u gegevens transformaties opslaan als gegevens **sets** , zodat u ze kunt gebruiken in andere pijp lijnen.

1. Selecteer een module voor gegevens transformatie die met succes is uitgevoerd.

1. Selecteer het tabblad **uitvoer en logboeken** .

1. Zoek de trans formatie-uitvoer en selecteer de **gegevensset registreren** om deze op te slaan als module onder **gegevens sets** in het module palet.

## <a name="how-to-use-apply-transformation"></a>Trans formatie Toep assen gebruiken  
  
1. Voeg de module **trans formatie Toep assen** toe aan uw pijp lijn. U kunt deze module vinden in het gedeelte beoordeling van de **model score &** van het palet van de module. 
  
1. Zoek de opgeslagen trans formatie die u wilt gebruiken onder **gegevens sets** in het module palet.

1. Verbind de uitvoer van de opgeslagen trans formatie met de linker invoer poort van de module **Apply trans formatie** .

    De gegevensset moet exact hetzelfde schema (aantal kolommen, kolom namen, gegevens typen) hebben als de gegevensset waarvoor de trans formatie voor het eerst is ontworpen.  
  
1. Verbind de gegevensset-uitvoer van de gewenste module met de juiste invoer poort van de module **Apply trans formatie** .
  
1. Als u een trans formatie wilt Toep assen op de nieuwe gegevensset, verzendt u de pijp lijn.  

> [!IMPORTANT]
> Om ervoor te zorgen dat de bijgewerkte trans formatie in trainings pijplijnen ook kan worden uitgevoerd bij het afleiden van pijp lijnen, moet u de onderstaande stappen volgen wanneer er bijgewerkte trans formatie is in de trainings pijplijn:
> 1. Registreer in de trainings pijplijn de uitvoer van de [trans formatie select columns](select-columns-transform.md) als een gegevensset.
> ![Gegevensset van module-uitvoer registreren](media/module/select-columns-transform-register-dataset.png)
> 1. Verwijder in de pijp lijn voor de deinterferentie de **TD-** module en vervang deze door de geregistreerde gegevensset in de vorige stap.
> ![TD-module vervangen](media/module/replace-tranformation-directory.png)

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 