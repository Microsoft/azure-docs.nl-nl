---
title: Project-aangepaste vertaler zoeken, bewerken en verwijderen
titleSuffix: Azure Cognitive Services
description: Aangepaste Translator biedt verschillende manieren om uw projecten op efficiënte wijze te beheren. U kunt meerdere projecten maken, zoeken op basis van uw criteria, uw projecten bewerken. Het is ook mogelijk om een project te verwijderen in Custom Translator.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 308ca25d35011c67ded7300177149cd590462952
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896440"
---
# <a name="search-edit-and-delete-projects"></a>Projecten zoeken, bewerken en verwijderen

Aangepaste Translator biedt meerdere manieren om uw projecten op efficiënte wijze te beheren. U kunt veel projecten maken, zoeken op basis van uw criteria en uw projecten bewerken. Het is ook mogelijk om een project te verwijderen in Custom Translator.  

## <a name="search-and-filter-projects"></a>Projecten zoeken en filteren

Met het filter tool kunt u projecten doorzoeken op verschillende filter voorwaarden. Het filtert zoals de project naam, de status, de bron-en doel taal en de categorie van het project.

1. Klik op de knop Filter.

    ![Project zoeken](media/how-to/how-to-search-project.png)

2. U kunt filteren op elke (of alle) van de volgende velden: project naam, bron taal, doel taal, categorie en project beschikbaarheid.

3. Klik op Toep assen.

    ![Filter opties voor zoek projecten](media/how-to/how-to-search-project-filters.png)

4. Schakel het filter uit om al uw projecten weer te geven door op wissen te tikken.

## <a name="edit-a-project"></a>Een project bewerken

Met het aangepaste conversie programma kunt u de naam en beschrijving van een project bewerken. Andere meta gegevens van het project, zoals de categorie, de bron taal en de doel taal, zijn niet beschikbaar om te bewerken. In de onderstaande stappen wordt beschreven hoe u een project bewerkt.

1. Klik op het potlood pictogram dat wordt weer gegeven wanneer u de muis aanwijzer op een project houdt.

    ![Project bewerken](media/how-to/how-to-edit-project.png)

2. In het dialoog venster kunt u de project naam, de beschrijving van het project, de categorie beschrijving en het project label wijzigen als er geen model is geïmplementeerd. U kunt de categorie of het taal paar niet wijzigen wanneer het project is gemaakt.

    ![Dialoog venster project bewerken](media/how-to/how-to-edit-project-dialog.png)

3. Klik op de knop Opslaan.

## <a name="delete-a-project"></a>Een project verwijderen

U kunt een project verwijderen wanneer u het niet meer nodig hebt. Zorg ervoor dat het project geen modellen heeft in de actieve status, zoals geïmplementeerd, training verzonden, gegevens verwerking, implementeren, enzovoort, anders mislukt de verwijderings bewerking. In de onderstaande stappen wordt beschreven hoe u een project verwijdert.

1. Plaats de muis aanwijzer op een project record en klik op het prullenbak pictogram.

   ![Project verwijderen](media/how-to/how-to-delete-project.png)

2. Verwijdering bevestigen. Als u een project verwijdert, worden alle modellen die in dat project zijn gemaakt, verwijderd. Het verwijderen van het project heeft geen invloed op uw documenten.

   ![Bevestigings dialoogvenster verwijderen](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>Volgende stappen

- [Upload documenten](how-to-upload-document.md) om te beginnen met het bouwen van uw aangepaste Vertaal model.
