---
title: QnA Maker-app beheren-QnA Maker
description: Met QnA Maker kunnen meerdere personen samen werken aan een Knowledge Base. QnA Maker biedt een mogelijkheid om de kwaliteit van uw kennis basis te verbeteren met actief onderwijs. De ene kan controleren, accepteren of afwijzen en toevoegen zonder bestaande vragen te verwijderen of te wijzigen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e4f0e229488093067b231a5c92334238ca216234
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550552"
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

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een kennisdatabase maken](./manage-knowledge-bases.md)
