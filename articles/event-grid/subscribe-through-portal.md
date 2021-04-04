---
title: Abonnementen Azure Event Grid via de portal
description: In dit artikel wordt beschreven hoe u Event Grid-abonnementen maakt voor de ondersteunde bronnen, zoals Azure Blob Storage, door gebruik te maken van de Azure Portal.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e80e2243c93ab38187646256f567d6ab73c40100
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995060"
---
# <a name="subscribe-to-events-through-portal"></a>Abonneren op gebeurtenissen via de portal

In dit artikel wordt beschreven hoe u Event Grid-abonnementen maakt via de portal.

## <a name="create-event-subscriptions"></a>Gebeurtenis abonnementen maken

Als u een Event Grid-abonnement wilt maken voor een van de ondersteunde [gebeurtenis bronnen](overview.md#event-sources), gebruikt u de volgende stappen. In dit artikel wordt beschreven hoe u een Event Grid-abonnement maakt voor een Azure-abonnement.

1. Selecteer **Alle services**.

   ![Alle services selecteren](./media/subscribe-through-portal/select-all-services.png)

1. Zoek naar **Event grid-abonnementen** en selecteer deze in de beschik bare opties.

   ![Scherm opname toont de zoek opdracht in de Azure Portal met Event Grid abonnementen die zijn geselecteerd.](./media/subscribe-through-portal/search.png)

1. Selecteer **+ Gebeurtenisabonnement**.

   ![Abonnement toevoegen](./media/subscribe-through-portal/add-subscription.png)

1. Selecteer het type abonnement dat u wilt maken. Als u zich bijvoorbeeld wilt abonneren op gebeurtenissen voor uw Azure-abonnement, selecteert u **Azure-abonnementen** en het doel abonnement.

   ![Azure-abonnement selecteren](./media/subscribe-through-portal/azure-subscription.png)

1. Als u zich wilt abonneren op alle gebeurtenis typen voor deze gebeurtenis bron, laat u de optie **Abonneren op alle gebeurtenis typen** ingeschakeld. Anders selecteert u de gebeurtenis typen voor dit abonnement.

   ![Gebeurtenis typen selecteren](./media/subscribe-through-portal/select-event-types.png)

1. Geef aanvullende informatie op over het gebeurtenis abonnement, zoals het eind punt voor het afhandelen van gebeurtenissen en de naam van een abonnement.

   ![Scherm opname van de secties ' Details van eind punt ' en ' Details van gebeurtenis abonnement ' met een opgegeven abonnements naam waarde.](./media/subscribe-through-portal/provide-subscription-details.png)

1. Als u onbestelbare berichten wilt inschakelen en beleid voor opnieuw proberen wilt aanpassen, selecteert u **extra functies**.

   ![Extra functies selecteren](./media/subscribe-through-portal/select-additional-features.png)

1. Selecteer een container die moet worden gebruikt voor het opslaan van gebeurtenissen die niet worden geleverd en stel in hoe nieuwe pogingen worden verzonden.

   ![Dead-berichten inschakelen en opnieuw proberen](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Wanneer u klaar bent, selecteert u **Maken**.

## <a name="create-subscription-on-resource"></a>Abonnement maken op resource

Bepaalde gebeurtenis bronnen bieden ondersteuning voor het maken van een gebeurtenis abonnement via de portal-interface voor die bron. Selecteer de bron van de gebeurtenis en zoek naar **gebeurtenissen** in het linkerdeel venster.

![Abonnements gegevens opgeven](./media/subscribe-through-portal/resource-events.png)

De portal geeft u de opties voor het maken van een gebeurtenis abonnement dat relevant is voor die bron.

## <a name="next-steps"></a>Volgende stappen

* [Event grid aflevering van berichten en probeer het opnieuw](delivery-and-retry.md).
* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [aangepaste gebeurtenissen maken en routeren met Azure Event grid](custom-event-quickstart.md)om snel aan de slag te gaan met Event grid.
