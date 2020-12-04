---
title: Actief leren gebruiken met Knowledge Base-QnA Maker
description: Meer informatie over het verbeteren van de kwaliteit van uw Knowledge Base met actief leren. Beoordeling, accepteren of afwijzen, toevoegen zonder bestaande vragen te verwijderen of te wijzigen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 1ac5e32b454cfc1adafb1f54b01d2a1a302908a4
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600432"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Actief leren gebruiken om uw Knowledge Base te verbeteren

Met [actief leren](../Concepts/active-learning-suggestions.md) kunt u de kwaliteit van uw kennis basis verbeteren door alternatieve vragen te stellen. Gebruikers-inzendingen worden in aanmerking genomen en worden als suggesties weer gegeven in de lijst met alternatieve vragen. U hebt de flexibiliteit om deze suggesties als alternatieve vragen toe te voegen of af te wijzen.

Uw kennis database wordt niet automatisch gewijzigd. Als u een wijziging wilt door voeren, moet u de suggesties accepteren. Deze suggesties Voeg vragen toe, maar u kunt geen bestaande vragen wijzigen of verwijderen.


## <a name="upgrade-runtime-version-to-use-active-learning"></a>Runtime versie bijwerken om actief leren te gebruiken

Actief leren wordt ondersteund in runtime versie 4.4.0 en hoger. Als uw Knowledge Base is gemaakt in een eerdere versie, moet u [de runtime upgraden](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) om deze functie te gebruiken.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Actief leren inschakelen voor alternatieve vragen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

Actief leren is standaard uitgeschakeld. Schakel deze in om voorgestelde vragen te bekijken. Nadat u actief leren hebt ingeschakeld, moet u gegevens van de client-app naar QnA Maker verzenden. Zie [de architectuur stroom voor het gebruik van GenerateAnswer en Train api's van een bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)voor meer informatie.

1. Selecteer **publiceren** om de Knowledge Base te publiceren. Actieve leer query's worden alleen verzameld van het GenerateAnswer API prediction-eind punt. De query's naar het test venster in de QnA Maker Portal hebben geen invloed op actief leren.

1. Als u actief leren wilt inschakelen in de QnA Maker Portal, gaat u naar de rechter bovenhoek en selecteert u uw **naam**. Ga naar [**Service-instellingen**](https://www.qnamaker.ai/UserSettings).

    ![Schakel de voorgestelde vraag van het actieve leer proces in op de pagina Service-instellingen. Selecteer uw gebruikers naam in het menu rechtsboven en selecteer vervolgens Service-instellingen.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Zoek de QnA Maker-service en schakel vervolgens **actief leren** in.

    > [!div class="mx-imgBorder"]
    > [![Schakel op de pagina Service-instellingen de optie actief leren in. Als u de functie niet kunt in-of uitschakelen, moet u mogelijk een upgrade van uw service uitvoeren.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > De exacte versie van de voor gaande afbeelding wordt alleen weer gegeven als voor beeld. Uw versie kan afwijken.

    Zodra **actief leren** is ingeschakeld, worden met de Knowledge Base regel matig nieuwe vragen voorgesteld op basis van door de gebruiker ingediende vragen. U kunt **actief leren** uitschakelen door de instelling opnieuw in te scha kelen.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

Actief leren bevindt zich **standaard in QnA Maker** Managed (preview). Als u de voorgestelde alternatieve vragen wilt zien, [gebruikt u weergave opties](../How-To/improve-knowledge-base.md#view-suggested-questions) op de pagina bewerken.

---

## <a name="review-suggested-alternate-questions"></a>Voorgestelde alternatieve vragen bekijken

[Bekijk alternatieve voorgestelde vragen](improve-knowledge-base.md) op de pagina **bewerken** van elke kennis database.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een kennisdatabase maken](./manage-knowledge-bases.md)
