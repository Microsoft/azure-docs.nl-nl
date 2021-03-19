---
title: XML valideren voor B2B Enter prise Integration
description: XML valideren met behulp van schema's in Azure Logic Apps met Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74792165"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>XML voor B2B Enterprise-integratie valideren in Azure Logic Apps met Enterprise Integration Pack

In het geval van B2B-scenario's moeten de handels partners in een overeenkomst er zeker van zijn dat de berichten die worden uitgewisseld geldig zijn voordat gegevens verwerking kan worden gestart. U kunt documenten valideren op basis van een vooraf gedefinieerd schema met behulp van de XML-validatie actie, die beschikbaar is in de Enterprise Integration Pack.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, kunt u [zich aanmelden voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een lege of bestaande logische app waarvoor u de XML-validatie actie wilt gebruiken. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md) en [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dat is gekoppeld aan uw Azure-abonnement, is gekoppeld aan de logische app waar u de XML-validatie actie wilt gebruiken en bevat het schema dat u wilt gebruiken voor het valideren van XML-inhoud. De logische app en het integratie account moeten zich op dezelfde locatie of Azure-regio bevinden.

## <a name="add-xml-validation-action"></a>XML-validatie actie toevoegen

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Als u een lege logische app hebt, voert u in de ontwerp functie voor logische apps in het zoekvak in `HTTP request` als uw filter en selecteert u de trigger **Wanneer een HTTP-aanvraag is ontvangen** . Anders gaat u verder met de volgende stap.

1. Selecteer **nieuwe stap** onder de laatste stap in de werk stroom.

   Als u een actie wilt toevoegen tussen de bestaande stappen, plaatst u de muis aanwijzer op de pijl die de stappen verbindt zodat het plus teken ( **+** ) wordt weer gegeven. Selecteer het plus teken en selecteer vervolgens **een actie toevoegen**.

1. Selecteer onder **Kies een actie** de optie **Ingebouwd**. Voer in het zoekvak `xml validation` als uw filter in. Selecteer **XML-validatie** in de lijst acties.

   ![Zoek en selecteer de actie XML-validatie](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Als u de XML-inhoud wilt opgeven die u wilt valideren, klikt u in het vak **inhoud** zodat de lijst met dynamische inhoud wordt weer gegeven.

   ![Lijst met dynamische inhoud openen](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   De lijst met dynamische inhoud bevat eigenschappen tokens die de uitvoer van de vorige stappen in de werk stroom vertegenwoordigen. Als in de lijst niet de verwachte eigenschap wordt weer gegeven, controleert u de kop van de trigger of actie, of u **meer weer geven** kunt selecteren.

1. Selecteer in de lijst met dynamische inhoud de eigenschap met de inhoud die u wilt valideren.

   In dit voor beeld wordt de uitvoer van de **hoofd tekst** van de trigger geselecteerd.

   ![Te valideren inhoud selecteren](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Als u het schema wilt opgeven dat u voor validatie wilt gebruiken, opent u de lijst **schema naam** en selecteert u het validatie schema dat u hebt toegevoegd aan het gekoppelde integratie account.

   ![Schema selecteren dat moet worden gebruikt voor validatie](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Sla uw logische app op.

   U bent nu klaar met het instellen van de validatie. In een echte wereld-app wilt u mogelijk de gevalideerde gegevens opslaan in een LOB-app (line-of-Business) zoals Sales Force. Als u de gevalideerde uitvoer naar Sales Force wilt verzenden, voegt u een actie toe.

1. Als u uw validatie actie wilt testen, kunt u een aanvraag verzenden om de werk stroom van uw logische app te activeren.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)