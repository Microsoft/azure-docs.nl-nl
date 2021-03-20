---
title: Stappen voor het toewijzen van een Azure-rol-Azure RBAC
description: Meer informatie over de stappen voor het toewijzen van Azure-rollen aan gebruikers, groepen, service-principals of beheerde identiteiten met behulp van Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 081335779ffc4b3a6ddf09e56b773c6d34b210be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556042"
---
# <a name="steps-to-assign-an-azure-role"></a>Stappen voor het toewijzen van een Azure-rol

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] In dit artikel wordt beschreven hoe u Azure-functies toewijst met behulp van de [Azure Portal](role-assignments-portal.md), [Azure POWERSHELL](role-assignments-powershell.md), [Azure cli](role-assignments-cli.md)of de [rest API](role-assignments-rest.md).

## <a name="step-1-determine-who-needs-access"></a>Stap 1: bepalen wie toegang moet hebben

U moet eerst bepalen wie toegang moet hebben. U kunt een rol toewijzen aan een gebruiker, groep, Service-Principal of beheerde identiteit. Dit wordt ook wel een *beveiligingsprincipal* genoemd.

![Beveiligings-principal voor een roltoewijzing](./media/shared/rbac-security-principal.png)

- Gebruiker: een persoon een profiel heeft in Azure Active Directory. U kunt ook rollen toewijzen aan gebruikers in andere tenants. Zie [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md) voor meer informatie over gebruikers in andere organisaties.
- Groep: een aantal gebruikers dat in Azure Active Directory is gemaakt. Wanneer u een rol aan een groep toewijst, hebben alle gebruikers binnen die groep die rol. 
- Service-principal: een beveiligings-id die wordt gebruikt door toepassingen of services om toegang tot specifieke Azure-resources te krijgen. U kunt het zien als een *gebruikersidentiteit* (gebruikersnaam en wachtwoord of certificaat) voor een toepassing.
- Beheerde identiteit - een identiteit in Azure Active Directory die automatisch wordt beheerd door Azure. [Beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md) worden doorgaans gebruikt bij het ontwikkelen van cloudtoepassingen voor het beheren van de referenties voor verificatie bij Azure-services.

## <a name="step-2-select-the-appropriate-role"></a>Stap 2: Selecteer de juiste rol

Machtigingen worden samen in een *roldefinitie* gegroepeerd. Het wordt meestal gewoon een *rol* genoemd. U kunt kiezen uit een lijst met verschillende ingebouwde rollen. Als de ingebouwde rollen niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen aangepaste rollen maken.

![Roldefinitie voor een roltoewijzing](./media/shared/rbac-role-definition.png)

Hier volgen vier fundamentele ingebouwde rollen. De eerste drie zijn op alle resourcetypen van toepassing.

- [Eigenaar](built-in-roles.md#owner): heeft volledige toegang tot alle resources, waaronder het recht om toegang aan anderen te delegeren.
- [Inzender](built-in-roles.md#contributor): kan alle typen Azure-resources maken en beheren, maar kan anderen geen toegang verlenen.
- [Lezer](built-in-roles.md#reader): kan bestaande Azure-resources bekijken.
- [Beheerder gebruikerstoegang](built-in-roles.md#user-access-administrator): kan gebruikerstoegang tot Azure-resources beheren.

Met de overige ingebouwde rollen kunnen specifieke Azure-resources worden beheerd. Met de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) kan een gebruiker bijvoorbeeld virtuele machines maken en beheren.

1. Begin met het uitgebreide artikel [Azure ingebouwde rollen](built-in-roles.md). De tabel boven aan het artikel is een index in de details verderop in het artikel.

1. In dat artikel navigeert u naar de service categorie (zoals compute, Storage en data bases) voor de resource waaraan u machtigingen wilt verlenen. De eenvoudigste manier om te zoeken naar de pagina voor een relevant tref woord, zoals ' BLOB ', ' virtuele machine ', enzovoort.

1. Bekijk de functies die worden weer gegeven voor de service categorie en Identificeer de specifieke bewerkingen die u nodig hebt. Altijd beginnen met de meest beperkende rol.

    Als een beveiligings-principal bijvoorbeeld blobs in een Azure-opslag account moet lezen, maar geen schrijf toegang nodig heeft, kiest u [gegevens lezer voor opslag-BLOB](built-in-roles.md#storage-blob-data-reader) in plaats van de rol van [BLOB-gegevens bijdrager](built-in-roles.md#storage-blob-data-contributor) (en zeker niet de eigenaar van de [opslag-BLOB gegevens](built-in-roles.md#storage-blob-data-owner) op beheerders niveau). U kunt de roltoewijzingen altijd later zo nodig bijwerken.

1. Als u geen geschikte rol vindt, kunt u een [aangepaste rol](custom-roles.md)maken.

## <a name="step-3-identify-the-needed-scope"></a>Stap 3: het benodigde bereik identificeren

*Bereik* is de set resources waarop de toegang van toepassing is. In azure kunt u een bereik op vier niveaus opgeven: [beheer groep](../governance/management-groups/overview.md), abonnement, [resource groep](../azure-resource-manager/management/overview.md#resource-groups)en resource. Bereiken zijn gestructureerd in een bovenliggende/onderliggende relatie. Met elk hiërarchie niveau wordt het bereik specifiek gemaakt. U kunt rollen toewijzen aan elk van deze bereikniveaus. Het niveau dat u selecteert, bepaalt hoe breed de rol wordt toegepast. Lagere niveaus nemen rolmachtigingen over van hogere niveaus. 

![Bereik voor een roltoewijzing](./media/shared/rbac-scope.png)

Wanneer u een rol in een bovenliggend bereik toewijst, worden deze machtigingen overgenomen in de onderliggende bereiken. Bijvoorbeeld:

- Als u de rol van [lezer](built-in-roles.md#reader) toewijst aan een gebruiker in het bereik van de beheer groep, kan die gebruiker alles lezen in alle abonnementen in de beheer groep.
- Als u de rol [facturerings lezer](built-in-roles.md#billing-reader) toewijst aan een groep in het abonnements bereik, kunnen de leden van die groep facturerings gegevens voor elke resource groep en resource in het abonnement lezen.
- Als u de rol van [inzender](built-in-roles.md#contributor) toewijst aan een toepassing in het bereik van de resource, kunnen hiermee resources van alle typen in die resourcegroep worden beheerd, maar geen andere resourcegroepen in het abonnement.

 Zie [inzicht in bereik](scope-overview.md)voor meer informatie.

## <a name="step-4-check-your-prerequisites"></a>Stap 4. Controleer uw vereisten

Als u rollen wilt toewijzen, moet u zijn aangemeld met een gebruiker aan wie een rol is toegewezen die een functie toewijzing heeft, zoals de [eigenaar](built-in-roles.md#owner) of de beheerder van de [gebruikers toegang](built-in-roles.md#user-access-administrator) in het bereik dat u probeert toe te wijzen aan de rol. Als u een roltoewijzing wilt verwijderen, moet u de machtiging voor roltoewijzingen verwijderen hebben.

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.Authorization/roleAssignments/delete`

Als uw gebruikers account geen machtiging heeft voor het toewijzen van een rol binnen uw abonnement, ziet u een fout bericht dat uw account niet is gemachtigd om de actie micro soft. Authorization/roleAssignments/write uit te voeren. Neem in dit geval contact op met de beheerders van uw abonnement, aangezien deze namens u de machtigingen kunnen toewijzen.

## <a name="step-5-assign-role"></a>Stap 5. Rol toewijzen

Zodra u de beveiligingsprincipal, de rol en het bereik kent, kunt u de rol toewijzen. U kunt rollen toewijzen met behulp van de Azure Portal, Azure PowerShell, Azure CLI, Azure Sdk's of REST Api's. U kunt maximaal **2000** roltoewijzingen in elk abonnement hebben. Deze limiet omvat roltoewijzingen voor het abonnement, de resourcegroep en het resourcebereik. U kunt maximaal **500** roltoewijzingen in elke beheergroep hebben.

Bekijk de volgende artikelen voor gedetailleerde stappen voor het toewijzen van rollen.

- [Azure-rollen toewijzen met behulp van de Azure Portal](role-assignments-portal.md)
- [Azure-rollen toewijzen met behulp van Azure PowerShell](role-assignments-powershell.md)
- [Azure-rollen toewijzen met behulp van Azure CLI](role-assignments-cli.md)
- [Azure-rollen toewijzen met behulp van de REST API](role-assignments-rest.md)

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Toegang tot Azure-resources verlenen aan een gebruiker met behulp van de Azure-portal](quickstart-assign-role-user-portal.md)