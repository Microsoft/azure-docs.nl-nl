---
title: Een beheergroep maken met de portal
description: In deze quickstart gebruikt u de Azure-portal om een beheergroep te maken om uw resources in een resource-hiërarchie in te delen.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: 2fa9b667079104fe9728ab41ecd87685c35de1b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024683"
---
# <a name="quickstart-create-a-management-group"></a>Quickstart: Een beheergroep maken

Managementgroepen zijn containers die u helpen bij het beheren van toegang, beleid en naleving voor meerdere abonnementen. Maak deze containers om een doeltreffende en efficiënte hiërarchie te maken die kan worden gebruikt met [Azure Policy](../policy/overview.md) en [op rollen gebaseerd toegangsbeheer van Azure](../../role-based-access-control/overview.md). Zie [Uw resources indelen met Azure-beheergroepen](overview.md) voor meer informatie over beheergroepen.

Het kan tot vijftien minuten duren voordat de eerste beheergroep die in de map is gemaakt, is voltooid. Er zijn processen die de eerste keer worden uitgevoerd om de service voor beheergroepen in Azure in te stellen voor uw map. U ontvangt een melding wanneer het proces is voltooid. Zie [Eerste configuratie van beheergroepen](./overview.md#initial-setup-of-management-groups) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

- Elke Azure AD-gebruiker in de tenant kan een beheergroep maken zonder dat de schrijfmachtiging voor beheergroepen is toegewezen als [Hiërarchie beschermen](./how-to/protect-resource-hierarchy.md#setting---require-authorization) niet is ingeschakeld. Deze nieuwe beheergroep wordt een onderliggend element van de hoofdbeheergroep of de [standaard beheergroep](./how-to/protect-resource-hierarchy.md#setting---default-management-group) en de maker krijgt de roltoewijzing "Eigenaar". Met de beheergroep-service kan deze functie worden toegewezen, zodat roltoewijzingen niet nodig zijn op hoofdmapniveau. Gebruikers hebben geen toegang tot de hoofdbeheergroep wanneer deze wordt gemaakt. Om te voorkomen dat de drempel van het vinden van de Azure AD Global Admins om beheergroepen te kunnen gebruiken te hoog is, wordt het maken van de eerste beheergroepen op hoofdmapniveau toegestaan.

### <a name="create-in-portal"></a>Maken in portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **Alle services** > **Beheer en governance**.

1. Selecteer **Beheergroepen**.

1. Selecteer **+ Beheergroep toevoegen**.

   :::image type="content" source="./media/main.png" alt-text="Schermopname van de pagina Beheergroepen, met onderliggende beheergroepen en abonnementen.":::

1. Houd **Nieuwe maken** ingedrukt en vul het veld Beheergroep-ID in.

   - De **beheergroep-ID** is de unieke ID van de map die wordt gebruikt voor het verzenden van opdrachten in deze beheergroep. Deze ID kan niet worden bewerkt nadat deze is gemaakt, omdat deze in het Azure-systeem wordt gebruikt om deze groep te identificeren. De [hoofdbeheergroep](./overview.md#root-management-group-for-each-directory) wordt automatisch gemaakt met een ID die de Azure Active Directory-ID is. Wijs voor alle andere beheergroepen een unieke ID toe.
   - Het veld Weergavenaam is de naam die wordt weergegeven in de Azure-portal. Een afzonderlijke weergavenaam is een optioneel veld bij het maken van de beheergroep en kan op elk gewenst moment worden gewijzigd.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Schermopname van de opties Beheergroep toevoegen, voor het maken van een nieuwe beheergroep.":::

1. Selecteer **Opslaan**.

## <a name="clean-up-resources"></a>Resources opschonen

Voer de volgende stappen uit om de gemaakte beheergroep te verwijderen:

1. Selecteer **Alle services** > **Beheer en governance**.

1. Selecteer **Beheergroepen**.

1. Zoek de eerder gemaakte beheergroep, selecteer deze en selecteer vervolgens **Details** naast de naam.
   Selecteer vervolgens **Verwijderen** en klik op OK.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een beheergroep gemaakt om uw resource-hiërarchie te organiseren. De beheergroep kan abonnementen of andere beheergroepen bevatten.

Ga verder met het volgende voor meer informatie over beheergroepen en het beheren van uw resource-hiërarchie:

> [!div class="nextstepaction"]
> [Uw resources beheren met beheergroepen](./manage.md)