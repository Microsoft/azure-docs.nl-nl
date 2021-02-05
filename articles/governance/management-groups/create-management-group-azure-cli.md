---
title: 'Quickstart: Een beheergroep maken met de Azure CLI'
description: In deze quickstart gebruikt u de Azure CLI om een beheergroep te maken om uw resources in een resource-hiërarchie in te delen.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe38882bd3b025635662e228ae919a24b03dee78
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592446"
---
# <a name="quickstart-create-a-management-group-with-the-azure-cli"></a>Quickstart: Een beheergroep maken met de Azure CLI

Managementgroepen zijn containers die u helpen bij het beheren van toegang, beleid en naleving voor meerdere abonnementen. Maak deze containers om een doeltreffende en efficiënte hiërarchie te maken die kan worden gebruikt met [Azure Policy](../policy/overview.md) en [op rollen gebaseerd toegangsbeheer van Azure](../../role-based-access-control/overview.md). Zie [Uw resources indelen met Azure-beheergroepen](overview.md) voor meer informatie over beheergroepen.

Het kan tot vijftien minuten duren voordat de eerste beheergroep die in de map is gemaakt, is voltooid. Er zijn processen die de eerste keer worden uitgevoerd om de service voor beheergroepen in Azure in te stellen voor uw map. U ontvangt een melding wanneer het proces is voltooid. Zie [Eerste configuratie van beheergroepen](./overview.md#initial-setup-of-management-groups) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

- Voor deze quickstart moet u versie 2.0.76 of hoger van Azure CLI uitvoeren om de CLI lokaal te installeren en te gebruiken. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

- Elke Azure AD-gebruiker in de tenant kan een beheergroep maken zonder dat de schrijfmachtiging voor beheergroepen is toegewezen als [Hiërarchie beschermen](./how-to/protect-resource-hierarchy.md#setting---require-authorization) niet is ingeschakeld. Deze nieuwe beheergroep wordt een onderliggend element van de hoofdbeheergroep of de [standaard beheergroep](./how-to/protect-resource-hierarchy.md#setting---default-management-group) en de maker krijgt de roltoewijzing "Eigenaar". Met de beheergroep-service kan deze functie worden toegewezen, zodat roltoewijzingen niet nodig zijn op hoofdmapniveau. Gebruikers hebben geen toegang tot de hoofdbeheergroep wanneer deze wordt gemaakt. Om te voorkomen dat de drempel van het vinden van de Azure AD Global Admins om beheergroepen te kunnen gebruiken te hoog is, wordt het maken van de eerste beheergroepen op hoofdmapniveau toegestaan.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-the-azure-cli"></a>Maken in de Azure CLI

Gebruik voor Azure CLI de [az account management-group create](/cli/azure/account/management-group#az_account_management_group_create)-opdracht om een nieuwe beheergroep te maken. In dit voorbeeld wordt de **naam van de beheergroep** _contoso_.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

De **naam** is een unieke ID die wordt gemaakt. Deze ID wordt gebruikt door andere opdrachten om te verwijzen naar deze groep en kan later niet worden gewijzigd.

Als u wilt dat de beheergroep een andere naam weergeeft in de Azure Portal, voegt u de parameter **display-name** toe. Gebruik bijvoorbeeld de volgende opdracht om een beheergroep te maken met als GroupName Contoso en als weergavenaam "Contoso Group":

```azurecli-interactive
az account management-group create --name 'Contoso' --display-name 'Contoso Group'
```

In de bovenstaande voorbeelden wordt de nieuwe beheergroep gemaakt in de hoofdbeheergroep. Als u een andere beheergroep als het bovenliggend element wilt opgeven, gebruikt u de parameter **bovenliggend element** en geeft u de naam van de bovenliggende groep op.

```azurecli-interactive
az account management-group create --name 'ContosoSubGroup' --parent 'Contoso'
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de eerder gemaakte beheergroep wilt verwijderen, gebruikt u de opdracht [az account management-group delete](/cli/azure/account/management-group#az_account_management_group_delete):

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een beheergroep gemaakt om uw resource-hiërarchie te organiseren. De beheergroep kan abonnementen of andere beheergroepen bevatten.

Ga verder met het volgende voor meer informatie over beheergroepen en het beheren van uw resource-hiërarchie:

> [!div class="nextstepaction"]
> [Uw resources beheren met beheergroepen](./manage.md)