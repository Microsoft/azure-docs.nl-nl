---
title: Stappen voor het toewijzen van een Azure-rol - Azure RBAC
description: Meer informatie over de stappen voor het toewijzen van Azure-rollen aan gebruikers, groepen, service-principals of beheerde identiteiten met behulp van op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 04/14/2021
ms.author: rolyon
ms.openlocfilehash: 40a17da6383fb1f368c74a82fefa71991cdc1b19
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517671"
---
# <a name="steps-to-assign-an-azure-role"></a>Stappen voor het toewijzen van een Azure-rol

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] In dit artikel worden de stappen op hoog niveau beschreven voor het toewijzen van Azure-rollen met behulp van [Azure Portal](role-assignments-portal.md), [Azure PowerShell](role-assignments-powershell.md), [Azure CLI](role-assignments-cli.md)of de [REST API](role-assignments-rest.md).

## <a name="step-1-determine-who-needs-access"></a>Stap 1: bepalen wie toegang nodig heeft

U moet eerst bepalen wie toegang nodig heeft. U kunt een rol toewijzen aan een gebruiker, groep, service-principal of beheerde identiteit. Dit wordt ook wel een *beveiligingsprincipaal genoemd.*

![Beveiligings-principal voor een roltoewijzing](./media/shared/rbac-security-principal.png)

- Gebruiker: een persoon een profiel heeft in Azure Active Directory. U kunt ook rollen toewijzen aan gebruikers in andere tenants. Zie [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md) voor meer informatie over gebruikers in andere organisaties.
- Groep: een aantal gebruikers dat in Azure Active Directory is gemaakt. Wanneer u een rol aan een groep toewijst, hebben alle gebruikers binnen die groep die rol. 
- Service-principal: een beveiligings-id die wordt gebruikt door toepassingen of services om toegang tot specifieke Azure-resources te krijgen. U kunt het zien als een *gebruikersidentiteit* (gebruikersnaam en wachtwoord of certificaat) voor een toepassing.
- Beheerde identiteit - een identiteit in Azure Active Directory die automatisch wordt beheerd door Azure. [Beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md) worden doorgaans gebruikt bij het ontwikkelen van cloudtoepassingen voor het beheren van de referenties voor verificatie bij Azure-services.

## <a name="step-2-select-the-appropriate-role"></a>Stap 2: selecteer de juiste rol

Machtigingen worden gegroepeerd in een *roldefinitie*. Het wordt meestal gewoon een *rol* genoemd. U kunt een keuze maken uit een lijst met verschillende ingebouwde rollen. Als de ingebouwde rollen niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen aangepaste rollen maken.

![Roldefinitie voor een roltoewijzing](./media/shared/rbac-role-definition.png)

Hier volgen vier fundamentele ingebouwde rollen. De eerste drie zijn op alle resourcetypen van toepassing.

- [Eigenaar](built-in-roles.md#owner): heeft volledige toegang tot alle resources, waaronder het recht om toegang aan anderen te delegeren.
- [Inzender](built-in-roles.md#contributor): kan alle typen Azure-resources maken en beheren, maar kan anderen geen toegang verlenen.
- [Lezer](built-in-roles.md#reader): kan bestaande Azure-resources bekijken.
- [Beheerder gebruikerstoegang](built-in-roles.md#user-access-administrator): kan gebruikerstoegang tot Azure-resources beheren.

Met de overige ingebouwde rollen kunnen specifieke Azure-resources worden beheerd. Met de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) kan een gebruiker bijvoorbeeld virtuele machines maken en beheren.

1. Begin met het uitgebreide artikel [Ingebouwde Azure-rollen.](built-in-roles.md) De tabel bovenaan het artikel is een index van de details verderop in het artikel.

1. Navigeer in dat artikel naar de servicecategorie (zoals compute, opslag en databases) voor de resource waaraan u machtigingen wilt verlenen. De eenvoudigste manier om te vinden waar u naar op zoek bent, is door op de pagina te zoeken naar een relevant trefwoord, zoals 'blob', 'virtuele machine', en meer.

1. Controleer de rollen die worden vermeld voor de servicecategorie en identificeer de specifieke bewerkingen die u nodig hebt. Begin altijd met de meest beperkende rol.

    Als een beveiligingsprincipaal bijvoorbeeld blobs moet lezen in een Azure-opslagaccount, [](built-in-roles.md#storage-blob-data-reader) maar geen schrijftoegang nodig heeft, kiest u Lezer [](built-in-roles.md#storage-blob-data-owner) voor opslagblobgegevens in plaats van Bijdrager voor opslagblobgegevens [(en](built-in-roles.md#storage-blob-data-contributor) zeker niet de rol eigenaar van opslagblobgegevens op beheerdersniveau). U kunt de roltoewijzingen altijd later bijwerken als dat nodig is.

1. Als u geen geschikte rol vindt, kunt u een aangepaste [rol maken.](custom-roles.md)

## <a name="step-3-identify-the-needed-scope"></a>Stap 3: het benodigde bereik identificeren

*Bereik* is de set resources waarop de toegang van toepassing is. In Azure kunt u een bereik op vier niveaus opgeven: [beheergroep,](../governance/management-groups/overview.md)abonnement, [resourcegroep](../azure-resource-manager/management/overview.md#resource-groups)en resource. Bereiken zijn gestructureerd in een bovenliggende/onderliggende relatie. Elk hiërarchieniveau maakt het bereik specifieker. U kunt rollen toewijzen aan elk van deze bereikniveaus. Het niveau dat u selecteert, bepaalt hoe breed de rol wordt toegepast. Lagere niveaus nemen rolmachtigingen over van hogere niveaus. 

![Bereik voor een roltoewijzing](./media/shared/rbac-scope.png)

Wanneer u een rol toewijst op een bovenliggend bereik, worden deze machtigingen overgenomen naar de onderliggende scopes. Bijvoorbeeld:

- Als u de rol [Lezer toewijst](built-in-roles.md#reader) aan een gebruiker in het bereik van de beheergroep, kan die gebruiker alles lezen in alle abonnementen in de beheergroep.
- Als u de rol [Factureringslezer](built-in-roles.md#billing-reader) toewijst aan een groep op abonnementsbereik, kunnen de leden van die groep factureringsgegevens lezen voor elke resourcegroep en resource in het abonnement.
- Als u de rol van [inzender](built-in-roles.md#contributor) toewijst aan een toepassing in het bereik van de resource, kunnen hiermee resources van alle typen in die resourcegroep worden beheerd, maar geen andere resourcegroepen in het abonnement.

 Zie Bereik begrijpen [voor meer informatie.](scope-overview.md)

## <a name="step-4-check-your-prerequisites"></a>Stap 4. Controleer uw vereisten

Als u rollen wilt toewijzen, moet u zijn aangemeld met een gebruiker aan wie [](built-in-roles.md#owner) een [](built-in-roles.md#user-access-administrator) rol is toegewezen met schrijfmachtigingen voor roltoewijzingen, zoals Eigenaar of Beheerder voor gebruikerstoegang voor het bereik dat u probeert toe te wijzen. Op dezelfde manier moet u de machtiging voor het verwijderen van roltoewijzingen hebben om een roltoewijzing te verwijderen.

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.Authorization/roleAssignments/delete`

Als uw gebruikersaccount geen machtiging heeft om een rol toe te wijzen binnen uw abonnement, wordt er een foutbericht weergegeven dat uw account niet is autorisatie heeft om de actie 'Microsoft.Authorization/roleAssignments/write' uit te voeren. Neem in dit geval contact op met de beheerders van uw abonnement omdat ze de machtigingen namens u kunnen toewijzen.

Als u een service-principal gebruikt om rollen toe te wijzen, krijgt u mogelijk de fout 'Onvoldoende bevoegdheden om de bewerking te voltooien'. Deze fout is waarschijnlijk omdat Azure probeert de toegewezen identiteit op te zoeken in Azure Active Directory (Azure AD) en de service-principal Azure AD standaard niet kan lezen. In dit geval moet u de service-principal machtigingen verlenen om gegevens in de map te lezen. Als u Azure CLI gebruikt, kunt u de roltoewijzing ook maken met behulp van de object-id van de toegewezene om de Azure AD-zoekactie over te slaan. Zie Problemen met [Azure RBAC oplossen voor meer informatie.](troubleshooting.md)

## <a name="step-5-assign-role"></a>Stap 5. Rol toewijzen

Zodra u de beveiligingsprincipaal, de rol en het bereik kent, kunt u de rol toewijzen. U kunt rollen toewijzen met behulp van Azure Portal, Azure PowerShell, Azure CLI, Azure SDK's of REST API's. U kunt maximaal **2000** roltoewijzingen in elk abonnement hebben. Deze limiet omvat roltoewijzingen voor het abonnement, de resourcegroep en het resourcebereik. U kunt maximaal **500** roltoewijzingen in elke beheergroep hebben.

Raadpleeg de volgende artikelen voor gedetailleerde stappen voor het toewijzen van rollen.

- [Azure-rollen toewijzen met behulp van de Azure Portal](role-assignments-portal.md)
- [Azure-rollen toewijzen met Azure PowerShell](role-assignments-powershell.md)
- [Azure-rollen toewijzen met behulp van Azure CLI](role-assignments-cli.md)
- [Azure-rollen toewijzen met behulp van REST API](role-assignments-rest.md)

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Toegang tot Azure-resources verlenen aan een gebruiker met behulp van de Azure-portal](quickstart-assign-role-user-portal.md)