---
title: Overeenkomsten voor handels partners
description: Overeenkomsten tussen handels partners maken en beheren met behulp van Azure Logic Apps en Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: c8cbfb619c9eed325161503f705bf5c4c0746265
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95992933"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Handelspartner-overeenkomsten maken en beheren in Azure Logic Apps

Met een overeenkomst voor [handels partners](../logic-apps/logic-apps-enterprise-integration-partners.md)  
  kunnen organisaties en bedrijven naadloos communiceren met elkaar door het specifieke industrie standaard protocol te definiëren dat moet worden gebruikt bij het uitwisselen van Business-to-Business (B2B)-berichten. Overeenkomsten bieden algemene voor delen, zoals:

* Organisaties in staat stellen om informatie uit te wisselen met een bekende indeling.
* Verbeter de efficiëntie bij het uitvoeren van B2B-trans acties.
* Kunt u eenvoudig oplossingen voor bedrijfs integratie maken, beheren en gebruiken.

In dit artikel wordt beschreven hoe u een AS2-, EDIFACT-of X12-overeenkomst maakt die u kunt gebruiken bij het bouwen van oplossingen voor bedrijfs integratie voor B2B-scenario's met behulp van de [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) en [Azure Logic apps](../logic-apps/logic-apps-overview.md). Nadat u een overeenkomst hebt gemaakt, kunt u de connectors AS2, EDIFACT of X12 gebruiken voor het uitwisselen van B2B-berichten.

Zie [Exchange RosettaNet-berichten](../logic-apps/logic-apps-enterprise-integration-rosettanet.md)voor het maken van overeenkomsten voor het uitwisselen van RosettaNet-berichten.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, [meldt u zich aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) voor het opslaan van uw overeenkomst en andere B2B-artefacten. Dit integratie account moet worden gekoppeld aan uw Azure-abonnement.

* Ten minste twee [handels partners](../logic-apps/logic-apps-enterprise-integration-partners.md) die u al hebt gemaakt in uw integratie account. Een overeenkomst vereist zowel een host-partner als een gast partner. Beide partners moeten dezelfde ' Business Identity '-kwalificatie gebruiken als de overeenkomst die u wilt maken, zoals AS2, X12 of EDIFACT.

* Optioneel: de logische app waarvoor u uw overeenkomst wilt gebruiken en een trigger waarmee de werk stroom van de logische app wordt gestart. Als u alleen uw integratie account en B2B-artefacten wilt maken, hebt u geen logische app nodig. Voordat uw logische app echter de B2B-artefacten in uw integratie account kan gebruiken, moet u uw integratie account koppelen aan uw logische app. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md) en [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Overeenkomsten maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
Selecteer in het hoofd menu van Azure **alle services**. Voer in het zoekvak ' Integration ' in als uw filter. Selecteer in de resultaten deze resource: **integratie accounts**

   ![Uw integratie account zoeken](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. Onder **integratie accounts** selecteert u het integratie account waar u de overeenkomst wilt maken.

   ![Het integratie account selecteren waar de overeenkomst moet worden gemaakt](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. Kies in het rechterdeel venster onder **onderdelen** de tegel **overeenkomsten** .

   ![Kies ' overeenkomsten '](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. Klik onder **overeenkomsten** op **toevoegen**. Geef in het deel venster **toevoegen** informatie op over uw overeenkomst, bijvoorbeeld:

   ![Kies toevoegen](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Naam** | Ja | <*overeenkomst-naam*> | De naam voor uw overeenkomst |
   | **Type overeenkomst** | Yes | **AS2**, **X12** of **EDIFACT** | Het protocol type voor uw overeenkomst. Wanneer u een overeenkomst bestand maakt, moet de inhoud in dat bestand overeenkomen met het overeenkomst type. | |  
   | **Host-partner** | Yes | <*host-partner naam*> | De host-partner vertegenwoordigt de organisatie die de overeenkomst specificeert |
   | **Host-id** | Yes | <*host-partner-id*> | De id van de host-partner |
   | **Gast partner** | Yes | <*gast-partner naam*> | De gast partner vertegenwoordigt de organisatie die zakendoet met de host-partner |
   | **Gast identiteit** | Yes | <*gast-partner-id*> | De id van de gast partner |
   | **Instellingen voor ontvangen** | Varieert | Varieert | Met deze eigenschappen geeft u op hoe de host-partner alle inkomende berichten van de gast partner in de overeenkomst ontvangt. Zie voor meer informatie het betreffende type overeenkomst: <p>- [AS2-bericht instellingen](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT-bericht instellingen](logic-apps-enterprise-integration-edifact.md) <br>- [X12-bericht instellingen](logic-apps-enterprise-integration-x12.md) |
   | **Instellingen verzenden** | Varieert | Varieert | Met deze eigenschappen geeft u op hoe de host-partner alle uitgaande berichten naar de gast partner in de overeenkomst verzendt. Zie voor meer informatie het betreffende type overeenkomst: <p>- [AS2-bericht instellingen](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT-bericht instellingen](logic-apps-enterprise-integration-edifact.md) <br>- [X12-bericht instellingen](logic-apps-enterprise-integration-x12.md) |
   |||||

   > [!IMPORTANT]
   > De oplossing voor een overeenkomst is afhankelijk van de items die zijn gedefinieerd in de partner en het inkomende bericht:
   >
   > * De kwalificatie en id van de afzender
   > * De kwalificatie en id van de ontvanger
   >
   > Als deze waarden voor uw partner veranderen, moet u er ook voor zorgen dat u de overeenkomst bijwerkt.

1. Wanneer u klaar bent met het maken van de overeenkomst, kiest u **OK** op de pagina **toevoegen** en gaat u terug naar uw integratie account.

   In de lijst met **overeenkomsten** wordt nu uw nieuwe overeenkomst weer gegeven.

## <a name="edit-agreements"></a>Overeenkomsten bewerken

1. Selecteer in de [Azure Portal](https://portal.azure.com)in het hoofd menu van Azure **alle services**.

1. Voer in het zoekvak ' Integration ' in als uw filter. Selecteer in de resultaten deze resource: **integratie accounts**

1. Onder **integratie accounts** selecteert u het integratie account met de overeenkomst die u wilt bewerken.

1. Kies in het rechterdeel venster onder **onderdelen** de tegel **overeenkomsten** .

1. Onder **overeenkomsten** selecteert u uw overeenkomst en kiest u **bewerken**.

1. Breng uw wijzigingen aan en sla deze op.

## <a name="delete-agreements"></a>Overeenkomsten verwijderen

1. Selecteer in de [Azure Portal](https://portal.azure.com)in het hoofd menu van Azure **alle services**.

1. Voer in het zoekvak ' Integration ' in als uw filter. Selecteer in de resultaten deze resource: **integratie accounts**

1. Onder **integratie accounts** selecteert u het integratie account met de overeenkomst die u wilt verwijderen.

1. Kies in het rechterdeel venster onder **onderdelen** de tegel **overeenkomsten** .

1. Onder **overeenkomsten** selecteert u uw overeenkomst en kiest u **verwijderen**.

1. Bevestig dat u de geselecteerde overeenkomst wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

* [AS2-berichten uitwisselen](logic-apps-enterprise-integration-as2.md)
* [EDIFACT-berichten uitwisselen](logic-apps-enterprise-integration-edifact.md)
* [X12-berichten uitwisselen](logic-apps-enterprise-integration-x12.md)
