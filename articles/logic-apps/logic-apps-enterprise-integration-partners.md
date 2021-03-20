---
title: Handels partners voor B2B-integraties toevoegen
description: Handels partners in uw integratie account maken voor gebruik met Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 8e3805fae5bf6cc5ad8cf759d3ba75220c6ddbd8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91565068"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Handelspartners toevoegen aan integratieaccounts voor Azure Logic Apps

In [Azure Logic apps](../logic-apps/logic-apps-overview.md)kunt u geautomatiseerde business-to-Business (B2B) integratie werk stromen maken met behulp van een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) met uw Logic apps. Om uw organisatie en anderen weer te geven, maakt en voegt u handels partners als artefacten toe aan uw integratie account. Partners zijn entiteiten die deelnemen aan B2B-transacties en berichten met elkaar uitwisselen.

Voordat u deze partners maakt, moet u de informatie bespreken en delen met uw partners over het identificeren en valideren van de berichten die door de andere worden verzonden. Nadat u deze gegevens hebt geaccepteerd, bent u klaar om partners te maken in uw integratie account.

## <a name="partner-roles-in-integration-accounts"></a>Partner rollen in integratie accounts

Als u de details wilt definiëren van de berichten die worden uitgewisseld met uw partners, maakt en voegt u [overeenkomsten](../logic-apps/logic-apps-enterprise-integration-agreements.md) toe als artefacten aan uw integratie account. Voor overeenkomsten zijn ten minste twee partners in uw integratie account vereist. Uw organisatie is altijd de *host-partner* in de overeenkomst. De organisatie die berichten uitwisselt met uw organisatie is de *gast partner*. Een gast partner kan een ander bedrijf zijn of zelfs een afdeling in uw eigen organisatie. Nadat u deze partners hebt toegevoegd, kunt u een overeenkomst maken.

In een overeenkomst geeft u de details op voor het verwerken van binnenkomende en uitgaande berichten van het perspectief van de hostserver. Voor inkomende berichten geeft de **Receive-instellingen** op hoe de host-partner berichten van de gast partner in de overeenkomst ontvangt. Voor uitgaande berichten geeft de **Verzend instellingen** op hoe de host-partner berichten naar de gast partner verzendt.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, [meldt u zich aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) voor het opslaan van uw partners, overeenkomsten en andere B2B-artefacten. Dit integratie account moet worden gekoppeld aan uw Azure-abonnement.

## <a name="create-partner"></a>Partner maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer in het hoofd menu van Azure **alle services**. Voer in het zoekvak ' integratie ' in en selecteer **integratie accounts**.

   ![Selecteer integratie accounts](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. Onder **integratie accounts** selecteert u het integratie account waaraan u uw partners wilt toevoegen.

   ![Integratie account selecteren](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. Kies de tegel **partners** .

   ![Scherm afbeelding waarin de tegel partners wordt weer gegeven.](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. Klik onder **partners** op **toevoegen**. Onder **partner toevoegen** geeft u de details van de partner op, zoals wordt beschreven in de onderstaande tabel.

   ![Kies toevoegen en geef de partner gegevens op](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam** | Ja | De naam van de partner |
   | **Kwalificatie** | Ja | De verificatie-tekst die unieke zakelijke identiteiten biedt aan organisaties, bijvoorbeeld **D-U-N-S (Dun & Bradstreet)**. <p>Partners kunnen voor een onderling gedefinieerde zakelijke identiteit kiezen. Voor deze scenario's selecteert u **wederzijds gedefinieerd** voor Edifact of **wederzijds gedefinieerd (X12)** voor X12. <p>Selecteer voor RosettaNet alleen **Duns**. Dit is de standaard. |
   | **Waarde** | Ja | Een waarde waarmee de documenten worden geïdentificeerd die door uw Logic apps worden ontvangen. <p>Voor RosettaNet moet deze waarde een getal van negen cijfers zijn dat overeenkomt met het DUNS-nummer. |
   ||||

   > [!NOTE]
   > Voor partners die gebruikmaken van RosettaNet kunt u aanvullende informatie opgeven door deze partners eerst te maken en [deze later te bewerken](#edit-partner).

1. Als u klaar bent, kiest u **Done**.

   Uw nieuwe partner wordt nu weer gegeven in de lijst met **partners** . Daarnaast werkt de tegel **partners** het huidige aantal partners bij.

   ![Nieuwe partner](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>Partner bewerken

1. Zoek en selecteer uw integratie account in de [Azure Portal](https://portal.azure.com).
Kies de tegel **partners** .

   ![De tegel partners kiezen](./media/logic-apps-enterprise-integration-partners/edit.png)

1. Onder **partners** selecteert u de partner die u wilt bewerken en kiest u **bewerken**. Breng onder **bewerken** de gewenste wijzigingen aan.

   ![Uw wijzigingen aanbrengen en opslaan](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   Voor RosettaNet kunt u onder **RosettaNet-partner eigenschappen** de volgende aanvullende gegevens opgeven:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Partner classificatie** | Nee | Het organisatie type van de partner |
   | **Toeleverings keten code** | Nee | De code van de toeleverings keten van de partner, bijvoorbeeld ' informatie technologie ' of ' elektronische onderdelen ' |
   | **Naam van contact persoon** | Nee | De naam van de contact persoon van de partner |
   | **E-mail** | Nee | Het e-mail adres van de partner |
   | **Fax** | Nee | Het faxnummer van de partner |
   | **Telefoon** | Nee | Het telefoon nummer van de partner |
   ||||

1. Wanneer u klaar bent, kiest u **OK** om uw wijzigingen op te slaan.

## <a name="delete-partner"></a>Partner verwijderen

1. Zoek en selecteer uw integratie account in de [Azure Portal](https://portal.azure.com). Kies de tegel **partners** .

   ![Scherm opname van de weer gave partners die u selecteert wanneer u een partner wilt verwijderen.](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. Selecteer onder **partners** de partner die u wilt verwijderen. Kies **Verwijderen**.

   ![Partner verwijderen](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [overeenkomsten](../logic-apps/logic-apps-enterprise-integration-agreements.md)