---
title: Versies beheren-LUIS
titleSuffix: Azure Cognitive Services
description: Met versies kunt u verschillende modellen bouwen en publiceren. Het is een goed idee om het huidige actieve model te klonen naar een andere versie van de app voordat u wijzigingen aanbrengt in het model.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: b93e9fd4047ee3731ef6fe4652bb92ce5145987d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018817"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Versies gebruiken om te bewerken en te testen zonder dat dit van invloed is op staging-of productie-apps

Met versies kunt u verschillende modellen bouwen en publiceren. Het is een goed idee om het huidige actieve model te klonen naar een andere [versie](./luis-concept-app-iteration.md) van de app voordat u wijzigingen aanbrengt in het model.

De actieve versie is de versie die u bewerkt in de LUIS-portal voor het **maken** van intenties, entiteiten, functies en patronen. Wanneer u de ontwerp-Api's gebruikt, hoeft u de actieve versie niet in te stellen, omdat de versie-specifieke REST API-aanroepen de versie in de route bevatten.

Als u met versies wilt werken, opent u uw app door de naam ervan te selecteren op **mijn apps** pagina en vervolgens **beheren** te selecteren in de bovenste balk en vervolgens **versies** in de linkernavigatiebalk te selecteren.

De lijst met versies toont welke versies er zijn gepubliceerd, waar ze zijn gepubliceerd en welke versie momenteel actief is.

## <a name="clone-a-version"></a>Een versie klonen

1. Selecteer de versie die u wilt klonen en selecteer vervolgens **klonen** in de werk balk.

2. Typ in het dialoog venster **versie klonen** een naam voor de nieuwe versie, bijvoorbeeld ' 0,2 '.

   ![Het dialoog venster versie klonen](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)

     > [!NOTE]
     > De versie-ID mag alleen uit tekens, cijfers of '. ' bestaan en mag niet langer zijn dan 10 tekens.

   Er wordt een nieuwe versie met de opgegeven naam gemaakt en deze wordt ingesteld als de actieve versie.

## <a name="set-active-version"></a>Actieve versie instellen

Selecteer een versie in de lijst en selecteer vervolgens **activeren** in de werk balk.

## <a name="import-version"></a>Versie importeren

U kunt een `.json` of een `.lu` versie van uw toepassing importeren.

1. Selecteer **importeren** in de werk balk en selecteer vervolgens de indeling.

2. Voer in het pop-upvenster **nieuwe versie importeren** de nieuwe versie naam van tien tekens in. U hoeft alleen een versie-ID in te stellen als de versie in het bestand al aanwezig is in de app.

    ![Sectie beheren, versies pagina, nieuwe versie importeren](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Zodra u een versie hebt geïmporteerd, wordt de nieuwe versie de actieve versie.

### <a name="import-errors"></a>Fouten importeren

* Tokenizer-fouten: als er een **tokenizer-fout optreedt** tijdens het importeren, probeert u een versie te importeren die gebruikmaakt van een andere [tokenizer](luis-language-support.md#custom-tokenizer-versions) dan die momenteel wordt gebruikt door de app. Zie voor het oplossen van dit probleem [migreren tussen tokenizer-versies](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Overige acties

* Als u een versie wilt **verwijderen** , selecteert u een versie in de lijst en selecteert u vervolgens **verwijderen** op de werk balk. Selecteer **OK**.
* Als u de naam van een versie wilt **wijzigen** , selecteert u een versie in de lijst en selecteert u vervolgens **naam wijzigen** in de werk balk. Voer een nieuwe naam in en selecteer **gereed**.
* Als u een versie wilt **exporteren** , selecteert u een versie in de lijst en selecteert u vervolgens **app exporteren** in de werk balk. Kies JSON of LU om te exporteren voor een back-up of om op te slaan in broncode beheer, kies **exporteren voor container** om [deze app te gebruiken in een Luis-container](luis-container-howto.md).