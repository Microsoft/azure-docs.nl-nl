---
title: Feedback geven voor Azure apps-aanbiedingen-micro soft Commercial Marketplace
description: Feedback voor uw Azure-toepassings aanbieding afhandelen vanuit het Microsoft Azure Marketplace beoordelings team. U krijgt toegang tot feedback in azure DevOps met de referenties van uw partner centrum.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: ede4e5dd781851c781407a8acea640e3e501f259
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436382"
---
# <a name="handling-review-feedback-for-azure-application-offers"></a>Feedback over het verwerken van beoordeling voor Azure-toepassings aanbiedingen

In dit artikel wordt uitgelegd hoe u toegang krijgt tot feedback van het Microsoft Azure Marketplace beoordelings team in [Azure DevOps](https://azure.microsoft.com/services/devops/). Als er in uw Azure-toepassings aanbieding kritieke problemen zijn gevonden tijdens de stap van **micro soft Review** , kunt u zich aanmelden bij dit systeem om gedetailleerde informatie over deze problemen te bekijken (feedback beoordelen). Nadat u alle problemen hebt opgelost, moet u uw aanbieding opnieuw verzenden om deze te kunnen publiceren op Azure Marketplace. In het volgende diagram ziet u hoe dit feedback proces is gekoppeld aan het publicatie proces.

![Feedback proces controleren](./media/review-feedback-process.png)

Normaal gesp roken raadpleegt u problemen als een pull-aanvraag (PR). Elke PR is gekoppeld aan een online Azure DevOps-item dat details bevat over het probleem. In de volgende afbeelding ziet u een voor beeld van de ervaring van het partner centrum als er problemen zijn gevonden tijdens de evaluatie. 

![Publicatiestatus](./media/publishing-status.png)

De PR met specifieke details over de inzending wordt vermeld in de koppeling ' certificerings rapport weer geven '. Voor complexe situaties kunnen de teams met beoordelings-en ondersteunings team u ook een e-mail sturen.

## <a name="azure-devops-access"></a>Toegang tot Azure DevOps

Alle gebruikers met toegang tot de rol ' ontwikkelaar ' in het partner centrum hebben toegang tot het weer geven van de PR-items waarnaar wordt verwezen in de feedback van de beoordeling.

## <a name="reviewing-the-pull-request"></a>De pull-aanvraag controleren

Gebruik de volgende procedure om de problemen te bekijken die worden beschreven in de pull-aanvraag.

1. Selecteer in de sectie **micro soft beoordeling** van het formulier publicatie stappen een PR-koppeling om uw browser te openen en navigeer naar de pagina **overzicht** (Home) voor deze PR. In de volgende afbeelding ziet u een voor beeld van de start pagina van het kritieke probleem voor de aanbieding van de voor beeld-app van contoso. Deze pagina bevat nuttige samenvattings informatie over de beoordelings problemen die in de Azure-app zijn gevonden.

    [![Start pagina pull-aanvraag](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klik op deze afbeelding om uit te breiden.*

1. Beschrijving Selecteer aan de rechter kant van het venster in het sectie **beleid** het probleem bericht (in dit voor beeld: **beleids validatie mislukt**) om de details op laag niveau van het probleem te onderzoeken, inclusief de bijbehorende logboek bestanden. Fouten worden doorgaans weer gegeven aan de onderkant van de logboek bestanden.

1. Selecteer in het menu aan de linkerkant van de start pagina de optie **bestanden** om de lijst bestanden weer te geven waaruit de technische activa voor deze aanbieding bestaan. De micro soft-revisoren moeten opmerkingen hebben toegevoegd waarin de gedetecteerde kritieke problemen worden beschreven. In het volgende voor beeld zijn er twee problemen ontdekt.

    [![Scherm afbeelding waarmee bestanden worden gemarkeerd en de twee problemen die zijn gedetecteerd.](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klik op deze afbeelding om uit te breiden.*

1. Selecteer elk knoop punt met opmerkingen in de linker boom om naar de opmerking in de context van de omringende code te navigeren. Corrigeer de bron code in het project van uw team om het probleem te verhelpen dat wordt beschreven in de opmerking.

>[!Note]
>U kunt de technische activa van uw aanbieding niet bewerken in de Azure DevOps-omgeving van het beoordelings team. Voor uitgevers is dit een alleen-lezen omgeving voor de Inge sloten bron code. U kunt echter reacties op reacties geven op het voor deel van het micro soft Review-team.

   In het volgende voor beeld heeft de uitgever gecontroleerd, gecorrigeerd en gereageerd op het eerste probleem.

   ![Eerste oplossing en antwoord op Opmerking](./media/first-comment-reply.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u de kritieke problemen hebt gecorrigeerd die in de beoordelings PR (s) zijn beschreven, moet u [uw Azure-app-aanbieding opnieuw publiceren](../create-new-azure-apps-offer.md).
