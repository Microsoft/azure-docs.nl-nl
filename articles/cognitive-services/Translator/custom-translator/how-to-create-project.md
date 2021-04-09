---
title: Een project-aangepaste vertaler maken
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u een project maakt en beheert in de aangepaste Translator van Azure Cognitive Services.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 9e3aa52323f44e6c1407fe2a542e40ee06370043
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895795"
---
# <a name="create-a-project"></a>Een project maken

Een project is een container voor een model, documenten en tests. Elk project bevat automatisch alle documenten die naar die werk ruimte worden geüpload die het juiste taal paar hebben.

Het maken van een project is de eerste stap voor het bouwen van een model.

## <a name="create-a-project"></a>Een project maken:

1.  Klik in de portal voor [aangepaste vertalers](https://portal.customtranslator.azure.ai) op project maken.

    ![Project maken](media/how-to/how-to-create-project.png)

2.  Voer de volgende gegevens over uw project in het dialoog venster in:

    a.  Project naam (vereist): Geef uw project een unieke, herken bare naam. Het is niet nodig om de talen in de titel te vermelden.

    b.  Beschrijving: een korte samen vatting van het project. Deze beschrijving heeft geen invloed op het gedrag van de aangepaste vertaler of uw resulterende aangepaste systeem, maar kan u helpen om onderscheid te maken tussen verschillende projecten.

    c.  Taal paar (vereist): Selecteer de taal die u wilt vertalen van en naar.

    d.  Categorie (vereist): Selecteer de categorie die het meest geschikt is voor uw project. De categorie beschrijft de terminologie en de stijl van de documenten die u wilt vertalen.

    e.  Categorie Beschrijving: gebruik dit veld om het specifieke veld of de branche waarin u werkt beter te beschrijven. Als uw categorie bijvoorbeeld medicijnen is, kunt u een bepaald document, zoals een chirurgische of Pediatrics, toevoegen. De beschrijving heeft geen invloed op het gedrag van de aangepaste vertaler of het resulterende aangepaste systeem.

    f.  Project label: het [project label](workspace-and-project.md#project-labels) onderscheidt tussen projecten met hetzelfde taal paar en dezelfde-categorie. Gebruik als best practice een label *alleen* als u van plan bent om meerdere projecten te bouwen voor hetzelfde taal paar en dezelfde categorie, en u toegang wilt krijgen tot deze projecten met een andere CategoryID. Gebruik dit veld niet als u systemen alleen voor één categorie wilt maken. Een project label is niet vereist en niet handig om onderscheid te maken tussen taal paren. U kunt hetzelfde label voor meerdere projecten gebruiken.

    ![Dialoog venster project maken](media/how-to/how-to-create-project-dialog.png)

3.  Klik op Maken

## <a name="view-project-details"></a>Project details weer geven

De landings pagina van het aangepaste conversie programma toont de eerste 10 projecten in uw werk ruimte. De project naam, taal paar, categorie, status en BLEU-Score worden weer gegeven.

Nadat u een project hebt geselecteerd, ziet u het volgende op de pagina project:

- CategoryID: er wordt een categorie nummer gemaakt door het samen voegen van het WorkspaceID, het project label en de categorie code. U gebruikt de CategoryID met de Text Translator API om aangepaste vertalingen op te halen. Klik op het pictogram kopiëren om het te kopiëren.

- Trein knop: gebruik deze knop om een [trainings model](how-to-train-model.md)te starten.

- Knop documenten toevoegen: gebruik deze knop om [documenten te uploaden](how-to-upload-document.md).

- Knop documenten filteren: met deze knop kunt u een specifiek (e) document (en) filteren en zoeken.

    ![Project details weer geven](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over het zoeken, bewerken en verwijderen van het project](how-to-search-edit-delete-projects.md).
- Meer informatie [over het uploaden van documenten](how-to-upload-document.md) om Vertaal modellen te maken.
