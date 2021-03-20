---
title: Resources toevoegen aan integratieserviceomgevingen
description: Logische apps, integratie accounts, aangepaste connectors en beheerde connectors toevoegen aan uw integratie service omgeving (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 147247c663311cfb3e05a986c6fb2bffbb41158b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92675202"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Resources toevoegen aan uw integratie service omgeving (ISE) in Azure Logic Apps

Nadat u een [ISE (Integration service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)hebt gemaakt, voegt u resources zoals Logic apps, integratie accounts en connectors toe zodat ze toegang hebben tot de resources in uw virtuele Azure-netwerk. Zo worden beheerde ISE-connectors die beschikbaar zijn nadat u uw ISE hebt gemaakt, niet automatisch weer gegeven in de ontwerp functie voor logische apps. Voordat u deze ISE-connectors kunt gebruiken, moet u [deze connectors hand matig toevoegen en implementeren voor uw ISE](#add-ise-connectors-environment) , zodat ze in de ontwerp functie voor logische apps worden weer gegeven.

> [!IMPORTANT]
> Voor logische apps en integratie accounts die in een ISE samen werken, moeten beide *dezelfde ISE* gebruiken als hun locatie.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De ISE die u hebt gemaakt om uw Logic apps uit te voeren. Als u nog geen ISE hebt, [maakt u eerst een ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Als u resources wilt maken, toevoegen of bijwerken die zijn geïmplementeerd in een ISE, moet u de rol eigenaar of Inzender toewijzen aan die ISE, of u hebt machtigingen overgenomen via het Azure-abonnement of de Azure-resource groep die is gekoppeld aan de ISE. Voor personen die geen eigenaar, bijdrager of overgenomen machtigingen hebben, kunnen ze de rol Integratieserviceomgeving Inzender of de rol van Integratieserviceomgeving ontwikkelaar toewijzen. Zie [Wat is Azure Role-based Access Control (Azure RBAC)](../role-based-access-control/overview.md)? voor meer informatie.

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Logische apps maken

Voer de volgende stappen uit om Logic apps te bouwen die worden uitgevoerd in uw integratie service omgeving (ISE):

1. Zoek en open uw ISE als deze nog niet is geopend. Selecteer in het menu ISE onder **instellingen** de optie **Logic apps**  >  **toevoegen**.

   ![Nieuwe logische app toevoegen aan ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Geef informatie op over de logische app die u wilt maken, bijvoorbeeld:

   ![Scherm opname van het venster ' Logic app ' maken ', waarin voorbeeld gegevens zijn ingevoerd.](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam** | Ja | De naam van de logische app die moet worden gemaakt |
   | **Abonnement** | Ja | De naam voor het te gebruiken Azure-abonnement |
   | **Resourcegroep** | Ja | De naam voor de Azure-resource groep (nieuw of bestaand) die moet worden gebruikt |
   | **Locatie** | Ja | Onder **integratie service omgevingen** selecteert u het te gebruiken ISE, als dit nog niet is geselecteerd. <p><p> **Belang rijk**: als u uw Logic Apps wilt gebruiken met een integratie account, moeten beide dezelfde ISE gebruiken. |
   ||||

1. Selecteer **Maken** als u klaar bent.

1. Blijf [op de gebruikelijke manier door gaan met het maken van uw logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Zie [geïsoleerd versus multi tenant in het overzicht van ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)voor meer informatie over hoe triggers en acties werken en hoe ze worden gelabeld wanneer u een ISE gebruikt vergeleken met de multi tenant-Logic apps service.

1. Zie [uw integratie service omgeving beheren](../logic-apps/ise-manage-integration-service-environment.md)als u logische apps en API-verbindingen in uw ISE wilt beheren.

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Integratie accounts maken

Op basis van de [ISE-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) die u hebt geselecteerd bij het maken, bevat uw ISE geen extra kosten voor het gebruik van een bepaald integratie account. Logic apps die zich in een Integration service Environment (ISE) bevinden, kunnen verwijzen naar alleen integratie accounts die zich in dezelfde ISE bevinden. Voor een integratie account om te werken met Logic apps in een ISE, moeten zowel het integratie account als de logische apps *dezelfde omgeving* gebruiken als hun locatie. Zie [integratie accounts met ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)voor meer informatie over integratie accounts en ISEs.

Voer de volgende stappen uit om een integratie account te maken dat gebruikmaakt van een ISE:

1. Zoek en open uw ISE als deze nog niet is geopend. Selecteer in het menu ISE onder **instellingen** de optie **integratie accounts**  >  **toevoegen**.

   ![Nieuw integratie account toevoegen aan ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Geef informatie op over de logische app die u wilt maken, bijvoorbeeld:

   ![Integratie service omgeving selecteren](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam** | Ja | De naam voor het integratie account dat u wilt maken |
   | **Abonnement** | Ja | De naam voor het Azure-abonnement dat u wilt gebruiken |
   | **Resourcegroep** | Ja | De naam voor de Azure-resource groep (nieuw of bestaand) die moet worden gebruikt |
   | **Prijscategorie** | Ja | De prijs categorie die moet worden gebruikt voor het integratie account |
   | **Locatie** | Ja | Onder **integratie service omgevingen** selecteert u dezelfde ISE als uw Logic apps, als deze nog niet is geselecteerd. <p><p> **Belang rijk**: als u uw integratie account wilt gebruiken met Logic apps, moeten beide dezelfde ISE gebruiken. |
   ||||

1. Selecteer **Maken** als u klaar bent.

1. [Koppel uw logische app op de gebruikelijke manier aan uw integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Ga door met het toevoegen van resources aan uw integratie account, zoals [handels partners](../logic-apps/logic-apps-enterprise-integration-partners.md) en [overeenkomsten](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Zie [uw integratie service omgeving beheren](../logic-apps/ise-manage-integration-service-environment.md)voor informatie over het beheren van integratie accounts in uw ISE.

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>ISE-connectors toevoegen

Nadat u uw ISE hebt gemaakt, worden beheerde ISE-connectors niet automatisch weer gegeven in de connector kiezer van de Logic app Designer. Voordat u deze ISE-connectors kunt gebruiken, moet u deze connectors hand matig toevoegen en implementeren voor uw ISE, zodat ze in de ontwerp functie voor logische apps worden weer gegeven.

> [!IMPORTANT]
> Beheerde ISE-connectors ondersteunen momenteel geen [Tags](../azure-resource-manager/management/tag-support.md). Als u een beleid instelt dat labels afdwingt, kan het toevoegen van ISE-connectors mislukken met een fout die vergelijkbaar is met dit voor beeld:
> 
> ```json
> {
>    "error": { 
>       "code": "IntergrationServiceEnvironmentManagedApiDefinitionTagsNotSupported", 
>       "message": "The tags are not supported in the managed API 'azureblob'."
>    }
> }
> ```
> 
> Als u ISE-connectors wilt toevoegen, moet u het beleid uitschakelen of verwijderen. 

1. Selecteer in het menu ISE, onder **instellingen**, **beheerde connectors**. Selecteer **toevoegen** op de werk balk.

   ![Beheerde connectors weer geven](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. Open in het deel venster **een nieuwe beheerde connector toevoegen** de lijst **connector zoeken** . Selecteer de ISE-connector die u wilt gebruiken, maar die nog niet is geïmplementeerd in uw ISE. Selecteer **Maken**.

   ![Selecteer de ISE-connector die u wilt implementeren in uw ISE](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Alleen ISE-connectors die in aanmerking komen maar nog niet zijn geïmplementeerd in uw ISE, kunnen worden geselecteerd. Connectors die al in uw ISE zijn geïmplementeerd, kunnen niet worden geselecteerd.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Aangepaste connectors maken

Als u aangepaste connectors in uw ISE wilt gebruiken, maakt u die aangepaste connectors rechtstreeks in uw ISE.

1. Zoek en open uw ISE als deze nog niet is geopend. Selecteer in het menu ISE onder **instellingen** de optie **aangepaste connectors**  >  **toevoegen**.

   ![Een aangepaste connector maken](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Geef de naam, het Azure-abonnement en de Azure-resource groep (nieuw of bestaand) op die u voor uw aangepaste connector wilt gebruiken.

1. Selecteer in de lijst **locatie** , onder de sectie **integratie service omgevingen** , dezelfde ISE die uw Logic apps gebruiken en selecteer **maken**, bijvoorbeeld:

   ![Scherm opname van het venster ' maken Logic Apps aangepaste connector ' met voorbeeld informatie geselecteerd.](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Selecteer de nieuwe aangepaste connector en selecteer vervolgens **bewerken**, bijvoorbeeld:

   ![Aangepaste connector selecteren en bewerken](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Ga door met het maken van de connector op de gebruikelijke manier vanuit een [OpenAPI-definitie](/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) of [SOAP](/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Zie [uw integratie service omgeving beheren](../logic-apps/ise-manage-integration-service-environment.md)voor het beheren van aangepaste connectors in uw ISE.

## <a name="next-steps"></a>Volgende stappen

* [Integratieserviceomgevingen beheren](../logic-apps/ise-manage-integration-service-environment.md)
