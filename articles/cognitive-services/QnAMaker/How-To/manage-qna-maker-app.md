---
title: QnA Maker-app beheren-QnA Maker
description: Met QnA Maker kunnen meerdere personen samen werken aan een Knowledge Base. QnA Maker biedt een mogelijkheid om de kwaliteit van uw kennis basis te verbeteren met actief onderwijs. De ene kan controleren, accepteren of afwijzen en toevoegen zonder bestaande vragen te verwijderen of te wijzigen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 9c042d044f5ceba5a64d6bd7dfefa34bbc69b107
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353201"
---
# <a name="manage-qna-maker-app"></a>QnA Maker-app beheren

Met QnA Maker kunt u samen werken met verschillende auteurs en inhouds editors door een mogelijkheid te bieden om de toegang van mede werkers te beperken op basis van de rol van de mede werker.
Meer informatie over de [concepten van QnA Maker-samen werker-authenticatie](../Concepts/role-based-access-control.md).

U kunt de kwaliteit van uw Knowledge Base ook verbeteren door alternatieve vragen te stellen via [actief leren](../Concepts/active-learning-suggestions.md). Gebruikers-inzendingen worden in aanmerking genomen en worden als suggesties weer gegeven in de lijst met alternatieve vragen. U hebt de flexibiliteit om deze suggesties als alternatieve vragen toe te voegen of af te wijzen.

Uw kennis database wordt niet automatisch gewijzigd. Als u een wijziging wilt door voeren, moet u de suggesties accepteren. Deze suggesties Voeg vragen toe, maar u kunt geen bestaande vragen wijzigen of verwijderen.

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC) toevoegen

Met QnA Maker kunnen meerdere mensen samen werken aan alle kennis grondslagen in dezelfde QnA Maker resource. Deze functie wordt meegeleverd met [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../../../role-based-access-control/role-assignments-portal.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Toegang op het QnA Maker resource niveau

U kunt een bepaalde Knowledge Base niet delen in een QnA Maker-service. Als u meer nauw keuriger toegangs beheer wilt, kunt u uw kennis grondslagen distribueren over verschillende QnA Maker resources en vervolgens functies toevoegen aan elke resource.

## <a name="add-a-role-to-a-resource"></a>Een rol toevoegen aan een resource

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Een gebruikers account toevoegen aan de QnA Maker-resource

De volgende stappen gebruiken de rol samen werken, maar een van de [rollen](../reference-role-based-access-control.md) kan worden toegevoegd met behulp van deze stappen

1. Meld u aan bij [Azure](https://portal.azure.com/) Portal en ga naar uw QnA Maker-resource.

    ![QnA Maker Resource lijst](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Ga naar het tabblad **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Selecteer **Toevoegen**.

    ![QnA Maker IAM toevoegen](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Selecteer een rol in de volgende lijst:

    |Rol|
    |--|
    |Eigenaar|
    |Inzender|
    |QnA Maker lezer Cognitive Services|
    |Cognitive Services QnA Maker editor|
    |Cognitive Services gebruiker|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker IAM-rol toevoegen.":::

1. Voer het e-mail adres van de gebruiker in en druk op **Opslaan**.

    ![QnA Maker IAM add e-mail](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>QnA Maker Knowledge bases weer geven

Wanneer de persoon met wie u uw QnA Maker-service hebt gedeeld, zich aanmeldt in de [QnA Maker Portal](https://qnamaker.ai), kunnen ze alle kennissen in die service zien op basis van hun rol.

Wanneer ze een kennis database selecteren, is hun huidige rol op die QnA Maker resource zichtbaar naast de naam van de Knowledge Base.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Scherm opname van de kennis database in de bewerkings modus met de rolnaam tussen haakjes naast de naam van de Knowledge Base in de linkerbovenhoek van de webpagina.":::

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Runtime versie bijwerken om actief leren te gebruiken

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

Actief leren wordt ondersteund in runtime versie 4.4.0 en hoger. Als uw Knowledge Base is gemaakt in een eerdere versie, moet u [de runtime upgraden](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) om deze functie te gebruiken.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

In QnA Maker Managed (preview), omdat de runtime wordt gehost door de QnA Maker-service zelf, hoeft u de runtime niet hand matig bij te werken.

---

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