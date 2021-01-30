---
title: Klassieke abonnementsbeheerdersrollen, Azure-rollen en Azure AD-rollen
description: In dit artikel worden de verschillende rollen in Azure beschreven, te weten klassieke abonnementsbeheerdersrollen, Azure-rollen en Azure Active Directory-rollen (Azure AD)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2021
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro;
ms.openlocfilehash: 0b43f30c25767a135b98b756d61ed2535e1fbd22
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092197"
---
# <a name="classic-subscription-administrator-roles-azure-roles-and-azure-ad-roles"></a>Klassieke abonnementsbeheerdersrollen, Azure-rollen en Azure AD-rollen

Als u nieuw bent bij Azure, is het misschien lastig om een goed beeld te krijgen van de verschillende rollen in Azure. In dit artikel vindt u informatie over de volgende rollen en wanneer u elk type rol moet gebruiken:
- Klassieke abonnementsbeheerdersrollen
- Azure-rollen
- Azure Active Directory-rollen (Azure AD)

## <a name="how-the-roles-are-related"></a>Relatie tussen de rollen

Een korte beschrijving van de geschiedenis van Azure helpt om een beter beeld te krijgen van de verschillende rollen. Bij de introductie van Azure waren er voor het beheer van resources slechts drie beheerdersrollen: Accountbeheerder, Servicebeheerder en Co-beheerder. Later is Azure RBAC (op rollen gebaseerd toegangsbeheer) toegevoegd. Azure RBAC is een nieuwer autorisatiesysteem dat gedetailleerd toegangsbeheer biedt voor Azure-resources. Azure RBAC bevat allerlei ingebouwde rollen, kan worden toegewezen aan verschillende bereiken en stelt u in staat om uw eigen aangepaste rollen te maken. Voor het beheren van resources in Azure AD, zoals gebruikers, groepen en domeinen, zijn verschillende Azure AD-rollen beschikbaar.

In het volgende schema diagram ziet u een algemeen overzicht van hoe de klassieke abonnementsbeheerdersrollen, Azure-rollen en Azure AD-rollen zich tot elkaar verhouden.

![De verschillende rollen in Azure](./media/rbac-and-directory-admin-roles/rbac-admin-roles.png)


## <a name="classic-subscription-administrator-roles"></a>Klassieke abonnementsbeheerdersrollen

Accountbeheerder, Servicebeheerder en Medebeheerder zijn de drie klassieke abonnementsbeheerdersrollen in Azure. Klassieke abonnementsbeheerders hebben volledige toegang tot het Azure-abonnement. Ze kunnen resources beheren met behulp van Azure Portal, API's van Azure Resource Manager en API's van het klassieke implementatiemodel. Het account dat wordt gebruikt voor registratie bij Azure wordt automatisch ingesteld als de accountbeheerder en de servicebeheerder. Vervolgens kunnen er extra medebeheerders of co-beheerders worden toegevoegd. De servicebeheerder en de medebeheerders hebben dezelfde toegang als gebruikers met de rol van eigenaar (een Azure-rol) op abonnementsniveau. De volgende tabel beschrijft de verschillen tussen deze drie klassieke abonnementsbeheerdersrollen.

| Klassiek abonnement beheerder | Limiet | Machtigingen | Opmerkingen |
| --- | --- | --- | --- |
| Accountbeheerder | 1 per Azure-account | <ul><li>Facturering beheren in de [Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)</li><li>Alle abonnementen in een account beheren</li><li>Nieuwe abonnementen maken</li><li>Abonnementen annuleren</li><li>De facturering voor een abonnement wijzigen</li><li>De servicebeheerder wijzigen</li></ul> | Conceptueel gezien de factureringseigenaar van het abonnement. |
| Servicebeheerder | 1 per Azure-abonnement | <ul><li>Services beheren in [Azure Portal](https://portal.azure.com)</li><li>Het abonnement opzeggen</li><li>Gebruikers de rol van medebeheerder geven</li></ul> | Voor een nieuw abonnement is het standaard zo dat de accountbeheerder ook de servicebeheerder is.<br>De servicebeheerder heeft dezelfde toegang als een gebruiker met de rol van eigenaar op abonnementsniveau.<br>De servicebeheerder heeft volledige toegang tot de Azure-portal. |
| Medebeheerder | 200 per abonnement | <ul><li>Deze rol heeft dezelfde toegangsrechten als de rol Servicebeheerder, maar kan de koppeling van abonnementen aan Azure-adreslijsten niet wijzigen</li><li>Gebruikers toewijzen aan de rol Medebeheerder, maar kan de servicebeheerder niet wijzigen</li></ul> | De medebeheerder heeft dezelfde toegang als een gebruiker met de rol van eigenaar op abonnementsniveau. |

In de Azure-portal kunt u co-beheerders beheren of de servicebeheerder weergeven met behulp van het tabblad **Klassieke beheerders**.

![Klassieke abonnementsbeheerders van Azure in de Azure-portal](./media/rbac-and-directory-admin-roles/subscription-view-classic-administrators.png)

In de Azure-portal kunt u de servicebeheerder weergeven of wijzigen en de accountbeheerder weergeven op de blade Eigenschappen van uw abonnement.

![Accountbeheerder en Servicebeheerder in Azure Portal](./media/rbac-and-directory-admin-roles/account-admin.png)

Ga voor meer informatie naar [Klassieke abonnementsbeheerders van Azure](classic-administrators.md).

### <a name="azure-account-and-azure-subscriptions"></a>Azure-account en Azure-abonnementen

Een Azure-account vertegenwoordigt een factureringsrelatie. Een Azure-account wordt gevormd door een gebruikersidentiteit, een of meer Azure-abonnementen en een bijbehorende set Azure-resources. De persoon die het account maakt, is de accountbeheerder voor alle abonnementen die in dat account worden gemaakt. Deze persoon is standaard ook de servicebeheerder voor het abonnement.

Azure-abonnementen helpen u de toegang tot Azure-resources in goede banen te leiden. Ze helpen u ook om te bepalen hoe resourcegebruik wordt gerapporteerd, gefactureerd en betaald. Elk abonnement kan een andere facturerings- en betalingsinstelling hebben, zodat u per kantoor, afdeling, project, enzovoort verschillende abonnementen en plannen kunt hebben. Elke service hoort bij een abonnement en de abonnements-id is mogelijk vereist voor programmatische bewerkingen.

Elk abonnement is gekoppeld aan een Azure AD-directory. Als u wilt zoeken naar de map waaraan het abonnement is gekoppeld, opent u **Abonnementen** in de Azure-portal en selecteert u vervolgens een abonnement om de map weer te geven.

Accounts en abonnementen worden beheerd in het [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

## <a name="azure-roles"></a>Azure-rollen

Azure RBAC staat voor op rollen gebaseerd toegangsbeheer en is een machtigingssysteem dat is gebouwd op [Azure Resource Manager](../azure-resource-manager/management/overview.md). Het voorziet in een geavanceerd toegangsbeheer van resources in Azure, zoals rekenbronnen en opslag. Azure RBAC bevat meer dan 70 ingebouwde rollen. Er zijn vier primaire Azure-rollen. De eerste drie gelden voor alle resourcetypen:

| Azure-rol | Machtigingen | Opmerkingen |
| --- | --- | --- |
| [Eigenaar](built-in-roles.md#owner) | <ul><li>Volledige toegang tot alle resources</li><li>Toegang aan anderen delegeren</li></ul> | De servicebeheerder en medebeheerders krijgen de rol van eigenaar op abonnementsniveau<br>Geldt voor alle resourcetypen. |
| [Inzender](built-in-roles.md#contributor) | <ul><li>Alle soorten Azure-resources maken en beheren</li><li>Een nieuwe tenant maken in Azure Active Directory</li><li>Kan geen toegang aan anderen verlenen</li></ul> | Geldt voor alle resourcetypen. |
| [Lezer](built-in-roles.md#reader) | <ul><li>Azure-resources weergeven</li></ul> | Geldt voor alle resourcetypen. |
| [Beheerder van gebruikerstoegang](built-in-roles.md#user-access-administrator) | <ul><li>Toegang van gebruikers tot Azure-resources beheren</li></ul> |  |

De overige ingebouwde rollen zijn bedoeld voor het beheer van specifieke Azure-resources. Met de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) kan een gebruiker bijvoorbeeld virtuele machines maken en beheren. Zie [Ingebouwde Azure-rollen](built-in-roles.md) voor een lijst met alle ingebouwde rollen.

Alleen de Azure-portal en de API's van Azure Resource Manager ondersteunen RBAC voor Azure-portal. Gebruikers, groepen en toepassingen met toegewezen Azure-rollen kunnen geen gebruikmaken van de [API's van het klassieke implementatiemodel van Azure](../azure-resource-manager/management/deployment-models.md).

In de Azure-portal worden roltoewijzingen met Azure RBAC weergegeven op de blade **Toegangsbeheer (IAM)** . Deze blade is overal in de portal beschikbaar, zoals in beheergroepen, abonnementen, resourcegroepen en verschillende resources.

![De blade Toegangsbeheer (IAM) in Azure Portal](./media/rbac-and-directory-admin-roles/access-control-role-assignments.png)

Wanneer u op het tabblad **Rollen** klikt, ziet u de lijst met geïntegreerde en aangepaste rollen.

![Ingebouwde rollen in Azure Portal](./media/rbac-and-directory-admin-roles/roles-list.png)

Zie [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure-portal](role-assignments-portal.md) voor meer informatie.

## <a name="azure-ad-roles"></a>Azure AD-rollen

Azure AD-rollen worden gebruikt voor het beheren van Azure AD-resources in een directory, zoals voor het maken of bewerken van gebruikers, het toewijzen van beheerdersrollen aan anderen, het opnieuw instellen van gebruikerswachtwoorden, het beheren van gebruikerslicenties en het beheren van domeinen. In de volgende tabel worden enkele van de belangrijkste Azure AD-rollen beschreven.

| Azure AD-rol | Machtigingen | Opmerkingen |
| --- | --- | --- |
| [Globale beheerder](../active-directory/roles/permissions-reference.md#global-administrator-permissions) | <ul><li>Toegang tot alle beheerfuncties in Azure Active Directory beheren, evenals services die federeren naar Azure Active Directory</li><li>Beheerdersrollen aan anderen toewijzen</li><li>Het wachtwoord voor gebruikers en alle andere beheerders opnieuw instellen</li></ul> | De persoon die zich registreert voor de Azure Active Directory-tenant wordt de globale beheerder. |
| [Gebruikersbeheerder](../active-directory/roles/permissions-reference.md#user-administrator) | <ul><li>Alle aspecten van gebruikers en groepen maken en beheren</li><li>Ondersteuningstickets beheren</li><li>Servicestatus bewaken</li><li>Wachtwoorden wijzigen voor gebruikers, helpdeskbeheerders en andere gebruikersbeheerders</li></ul> |  |
| [Factureringsbeheerder](../active-directory/roles/permissions-reference.md#billing-administrator) | <ul><li>Aankopen doen</li><li>Abonnementen beheren</li><li>Ondersteuningstickets beheren</li><li>Servicestatus beheren</li></ul> |  |

In de Azure-portal kunt u de lijst met Azure AD-rollen bekijken op de blade **Rollen en beheerders**. Zie [Machtigingen voor beheerdersrol toewijzen in Azure Active Directory](../active-directory/roles/permissions-reference.md) voor een lijst met alle Azure AD-rollen.

![Azure AD-rollen in de Azure-portal](./media/rbac-and-directory-admin-roles/directory-admin-roles.png)

## <a name="differences-between-azure-roles-and-azure-ad-roles"></a>Verschillen tussen Azure-rollen en Azure AD-rollen

In algemene zin worden Azure-rollen gebruikt voor het beheren van Azure-resources, terwijl Azure AD-rollen worden gebruikt voor het beheren van Azure Active Directory-resources. In de volgende tabel worden enkele verschillen vergeleken.

| Azure-rollen | Azure AD-rollen |
| --- | --- |
| Toegang tot Azure-resources beheren | Toegang tot Azure Active Directory-resources beheren |
| Ondersteuning voor aangepaste rollen | Ondersteuning voor aangepaste rollen |
| Bereik kan worden opgegeven op meerdere niveaus (beheergroep, abonnement, resourcegroep, resource) | Bereik is op tenantniveau |
| Gegevens van rollen zijn beschikbaar vanuit Azure Portal, Azure CLI, Azure PowerShell, Azure Resource Manager-sjablonen en de REST-API | Gegevens van rollen zijn beschikbaar vanuit de beheerportal van Azure, het beheercentrum van Microsoft 365, Microsoft Graph en AzureAD PowerShell |

### <a name="do-azure-roles-and-azure-ad-roles-overlap"></a>Overlappen Azure-rollen en Azure AD-rollen elkaar?

In het standaardscenario is er geen overlap tussen Azure-rollen en Azure AD-rollen. Als een globale beheerder zijn of haar toegang echter verhoogt door de schakeloptie **Toegangsbeheer voor Azure-resources** te kiezen in de Azure-portal, krijgt de globale beheerder de rol [Beheerder van gebruikerstoegang](built-in-roles.md#user-access-administrator) (een Azure-rol) voor alle abonnementen voor een bepaalde tenant. De rol Beheerder van gebruikerstoegang stelt de gebruiker in staat om andere gebruikers toegang te verlenen tot Azure-resources. Deze schakeloptie kan handig zijn om weer toegang te krijgen tot een abonnement. Zie voor meer informatie [Toegang verhogen om alle Azure-abonnementen en beheergroepen te beheren](elevate-access-global-admin.md).

Er zijn verschillende Azure AD-rollen die Azure AD en Microsoft 365 overlappen, zoals de rollen Globale beheerder en Gebruikersbeheerder. Als u bijvoorbeeld lid bent van de rol Globale beheerder, hebt u de bevoegdheden van globale beheerders in Azure AD en Microsoft 365, zoals het doorvoeren van wijzigingen in Microsoft Exchange en Microsoft SharePoint. De globale beheerder heeft echter standaard geen toegang tot Azure-resources.

![Azure RBAC-rollen vergeleken met Azure AD-rollen](./media/rbac-and-directory-admin-roles/azure-office-roles.png)

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure RBAC (toegangsbeheer op basis van rollen)?](overview.md)
- [Machtigingen voor beheerrol in Azure Active Directory](../active-directory/roles/permissions-reference.md)
- [Klassieke abonnementsbeheerders van Azure](classic-administrators.md)
