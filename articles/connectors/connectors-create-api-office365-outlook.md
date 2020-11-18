---
title: Integreren met Office 365 Outlook
description: Taken en werk stromen automatiseren waarmee e-mail, contact personen en agenda's in Office 365 Outlook worden beheerd met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/13/2020
tags: connectors
ms.openlocfilehash: 9caf69a7f78c7872f0a5f8a2ed07bdc749a29023
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682992"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>E-mail, contactpersonen en agenda's beheren in Office 365 Outlook met behulp van Azure Logic Apps

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de [Office 365 Outlook-Connector](/connectors/office365connector/)kunt u geautomatiseerde taken en werk stromen maken voor het beheren van uw werk-of school account door Logic apps te bouwen. U kunt bijvoorbeeld de volgende taken automatiseren:

* E-mail ontvangen, verzenden en beantwoorden.
* Plan vergaderingen in uw agenda.
* Contact personen toevoegen en bewerken.

U kunt elke trigger gebruiken om uw werk stroom te starten, bijvoorbeeld wanneer er een nieuwe e-mail binnenkomt, wanneer een agenda-item wordt bijgewerkt of wanneer er een gebeurtenis optreedt in een diff-service, zoals Sales Force. U kunt acties gebruiken die reageren op de trigger gebeurtenis, bijvoorbeeld een e-mail bericht verzenden of een nieuwe agenda gebeurtenis maken.

## <a name="prerequisites"></a>Vereisten

* Een Outlook-account waar u zich aanmeldt met een [werk-of school account](https://www.office.com/). Als u een @outlook.com account hebt @hotmail.com , gebruikt u in plaats daarvan de [Outlook.com-connector](../connectors/connectors-create-api-outlook.md) . Zie [verbinding maken met andere accounts](#connect-using-other-accounts)om verbinding te maken met Outlook met een ander gebruikers account, zoals een service account.

* Een Azure-account en -abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* De logische app waartoe u toegang wilt krijgen tot uw Outlook-account. Als u uw werk stroom wilt starten met een Office 365 Outlook-trigger, moet u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)hebben. Als u een Office 365 Outlook-actie wilt toevoegen aan uw werk stroom, moet uw logische app al een trigger hebben.

## <a name="add-a-trigger"></a>Een trigger toevoegen

Een [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) is een gebeurtenis waarmee de werk stroom in uw logische app wordt gestart. In deze voor beeld van een logische app wordt een ' polling-trigger ' gebruikt waarmee wordt gecontroleerd op een bijgewerkte agenda gebeurtenis in uw e-mail account, op basis van de opgegeven interval en frequentie.

1. Open in de [Azure Portal](https://portal.azure.com)uw lege logische app in de ontwerp functie voor logische apps.

1. Voer in het zoekvak `office 365 outlook` als uw filter in. In dit voor beeld **wordt geselecteerd wanneer een geplande gebeurtenis binnenkort wordt gestart**.
   
   ![Selecteer trigger om uw logische app te starten](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Als u geen actieve verbinding met uw Outlook-account hebt, wordt u gevraagd u aan te melden en deze verbinding te maken. Zie [verbinding maken met andere accounts](#connect-using-other-accounts)om verbinding te maken met Outlook met een ander gebruikers account, zoals een service account. Geef anders de informatie op voor de eigenschappen van de trigger.

   > [!NOTE]
   > Uw verbinding verloopt pas nadat u de aanmeldings referenties hebt gewijzigd. Zie [Configureer bare token levensduur in azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md)voor meer informatie.

   In dit voor beeld wordt de agenda geselecteerd die door de trigger wordt gecontroleerd, bijvoorbeeld:

   ![De eigenschappen van de trigger configureren](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. Stel in de trigger de **frequentie** -en **interval** waarden in. Als u andere beschik bare trigger eigenschappen, zoals **tijd zone**, wilt toevoegen, selecteert u de eigenschappen in de lijst **nieuwe para meter toevoegen** .

   Als u bijvoorbeeld wilt dat de trigger de kalender elke 15 minuten controleert, stelt u de **frequentie** in op **minuut** en stelt u **interval** in op `15` . 

   ![Stel de frequentie en het interval voor de trigger in](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Voeg nu een actie toe die wordt uitgevoerd nadat de trigger wordt geactiveerd. U kunt bijvoorbeeld de Twilio-bericht actie voor **verzenden** toevoegen, waarmee een tekst wordt verzonden wanneer een agenda gebeurtenis in 15 minuten wordt gestart.

## <a name="add-an-action"></a>Een actie toevoegen

Een [actie](../logic-apps/logic-apps-overview.md#logic-app-concepts) is een bewerking die door de werk stroom in uw logische app wordt uitgevoerd. In dit voor beeld van een logische app wordt een nieuwe contact persoon gemaakt in Office 365 Outlook. U kunt de uitvoer van een andere trigger of actie gebruiken om de contact persoon te maken. Stel bijvoorbeeld dat uw logische app de trigger Dynamics 365 gebruikt **Wanneer een record wordt gemaakt**. U kunt de actie Office 365 Outlook- **contact persoon maken** toevoegen en de uitvoer van de Sales Force-trigger gebruiken om de nieuwe contact persoon te maken.

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **nieuwe stap** om een actie toe te voegen als de laatste stap in uw werk stroom. 

   Als u een actie tussen de stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen deze stappen. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak `office 365 outlook` als uw filter in. In dit voor beeld wordt **contact maken** geselecteerd.

   ![Selecteer de actie die moet worden uitgevoerd in uw logische app](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Als u geen actieve verbinding met uw Outlook-account hebt, wordt u gevraagd u aan te melden en deze verbinding te maken. Zie [verbinding maken met andere accounts](#connect-using-other-accounts)om verbinding te maken met Outlook met een ander gebruikers account, zoals een service account. Geef anders de informatie op voor de eigenschappen van de actie.

   > [!NOTE]
   > Uw verbinding verloopt pas nadat u de aanmeldings referenties hebt gewijzigd. Zie [Configureer bare token levensduur in azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md)voor meer informatie.

   In dit voor beeld wordt de map Contacts geselecteerd waarin de actie de nieuwe contact persoon maakt, bijvoorbeeld:

   ![De eigenschappen van de actie configureren](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Als u andere beschik bare actie-eigenschappen wilt toevoegen, selecteert u deze eigenschappen in de lijst **nieuwe para meter toevoegen** .

1. Selecteer **Opslaan** op de werkbalk van de ontwerper.

<a name="connect-using-other-accounts"></a>

## <a name="connect-using-other-accounts"></a>Verbinding maken met andere accounts

Als u verbinding met Outlook probeert te maken met behulp van een ander account dan dat momenteel is aangemeld bij Azure, kunt u [eenmalige aanmelding (SSO)-](../active-directory/manage-apps/what-is-single-sign-on.md) fouten krijgen. Dit probleem treedt op wanneer u zich met één account aanmeldt bij de Azure Portal, maar u kunt een ander account gebruiken om de verbinding te maken. De ontwerp functie voor logische apps verwacht het account dat is aangemeld bij Azure te gebruiken. U kunt dit probleem oplossen met de volgende opties:

* Stel het andere account in als een **bijdrager** aan de resource groep van uw logische app.

  1. Selecteer **toegangs beheer (IAM)** in het menu van de resource groep van uw logische app. Stel het andere account in met de rol **Inzender** . Zie [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure-portal](../role-based-access-control/role-assignments-portal.md) voor meer informatie.

  1. Als u bent aangemeld bij de Azure Portal met uw werk-of school account, meldt u zich af en meldt u zich weer aan met uw andere account. U kunt nu een verbinding met Outlook maken met behulp van het andere account.

* Stel het andere account zo in dat uw werk-of school account machtigingen voor verzenden als heeft.

   Als u beheerders machtigingen hebt, stelt u in het postvak van het service account uw werk-of school account in met behulp van **verzenden als** of **Verzenden namens** een machtiging. Zie voor meer informatie [postvak machtigingen toewijzen aan een andere gebruiker-beheerder Help](/microsoft-365/admin/add-users/give-mailbox-permissions-to-another-user). U kunt vervolgens de verbinding maken met behulp van uw werk-of school account. Nu kunt u in triggers of acties waar u de afzender kunt opgeven, het e-mail adres van het service account gebruiken.

   De actie **een e-mail bericht verzenden** heeft bijvoorbeeld een optionele para meter, **van (verzenden als)**, die u aan de actie kunt toevoegen en het e-mail adres van uw service account gebruiken als afzender. Voer de volgende stappen uit om deze para meter toe te voegen:

   1. Open in de actie **een E-mail verzenden** de lijst **een para meter toevoegen** en selecteer de para meter **van (verzenden als)** .

   1. Wanneer de para meter voor de actie wordt weer gegeven, voert u het e-mail adres van het service account in.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentie pagina van de connector](/connectors/office365/)voor technische informatie over deze connector, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)