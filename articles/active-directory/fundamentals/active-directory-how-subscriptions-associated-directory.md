---
title: Een bestaand Azure-abonnement toevoegen aan uw Tenant-Azure AD
description: Instructies voor het toevoegen van een bestaand Azure-abonnement aan uw Azure Active Directory-Tenant.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/01/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86395a91c1b2cbba6a93c7b1b7cad487129fff0a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094294"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Een Azure-abonnement aan uw Azure Active Directory-tenant toevoegen of koppelen

Een Azure-abonnement heeft een vertrouwens relatie met Azure Active Directory (Azure AD). Een abonnement vertrouwt Azure AD voor het verifiëren van gebruikers, services en apparaten.

Meerdere abonnementen kunnen dezelfde Azure AD-Directory vertrouwen. Elk abonnement kan slechts één map vertrouwen.

Een of meer Azure-abonnementen kunnen een vertrouwens relatie tot stand brengen met een exemplaar van Azure Active Directory (Azure AD) om beveiligings-principals en apparaten te verifiëren en te autoriseren voor Azure-Services.  Wanneer een abonnement is verlopen, blijft het vertrouwde exemplaar van de Azure AD-service behouden, maar de beveiligings-principals verliezen geen toegang meer tot Azure-resources.

Wanneer een gebruiker zich aanmeldt voor een micro soft-Cloud service, wordt er een nieuwe Azure AD-Tenant gemaakt en wordt de gebruiker lid van de rol globale beheerder gemaakt. Wanneer een eigenaar van een abonnement echter een abonnement neemt op een bestaande Tenant, wordt de eigenaar niet toegewezen aan de rol van globale beheerder.

Al uw gebruikers hebben één *basis* Directory voor verificatie. Uw gebruikers kunnen ook gasten zijn in andere directory's. U kunt zowel de Home-als de gast mappen voor elke gebruiker in azure AD bekijken.

> [!Important]
> Wanneer u een abonnement aan een andere Directory koppelt, gaan gebruikers die rollen hebben die zijn toegewezen met behulp [van Azure op rollen gebaseerd toegangs beheer](../../role-based-access-control/role-assignments-portal.md) hun toegang kwijt. Klassieke abonnementsbeheerders, waaronder de servicebeheerder en co-beheerders, verliezen ook hun toegang.
>
> Door uw Azure Kubernetes service-cluster (AKS) naar een ander abonnement te verplaatsen of het cluster eigenaar-abonnement te verplaatsen naar een nieuwe Tenant, wordt de functionaliteit van het cluster verloren gegaan vanwege verloren Roltoewijzingen en de rechten van de Service-Principal. Zie [Azure Kubernetes service (AKS) (Engelstalig)](../../aks/index.yml)voor meer informatie over AKS.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u uw abonnement kunt koppelen of toevoegen, moet u de volgende taken uitvoeren:

- Bekijk de volgende lijst met wijzigingen die zich voordoen nadat u uw abonnement hebt gekoppeld of toegevoegd, en hoe u dit kunt beïnvloeden:

  - Gebruikers die rollen hebben toegewezen met Azure RBAC, raken hun toegang kwijt
  - Service beheerder en Co-Administrators verliezen geen toegang
  - Als u sleutel kluizen hebt, zijn ze niet toegankelijk en moet u deze na de koppeling oplossen
  - Als u beheerde identiteiten hebt voor resources zoals Virtual Machines of Logic Apps, moet u deze opnieuw inschakelen of opnieuw maken na de koppeling
  - Als u een geregistreerde Azure Stack hebt, moet u deze na de koppeling opnieuw registreren
  - Raadpleeg [Een Azure-abonnement overzetten naar een andere Azure AD-map](../../role-based-access-control/transfer-subscription.md) voor meer informatie.

- Meld u aan met een account dat:

  - Beschikt over [een roltoewijzing](../../role-based-access-control/built-in-roles.md#owner) voor het abonnement. Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het toewijzen van de rol van eigenaar.
  - Bestaat zowel in de huidige map als in de nieuwe map. De huidige map is gekoppeld aan het abonnement. U koppelt de nieuwe map aan het abonnement. Zie [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](../external-identities/add-users-administrator.md)voor meer informatie over het verkrijgen van toegang tot een andere map.

- Zorg ervoor dat u geen gebruik maakt van een Azure Cloud service provider (CSP)-abonnement (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), een intern micro soft-abonnement (MS-AZR-0015P) of een Microsoft Imagine-abonnement (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Een abonnement aan een directory koppelen<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Voer de volgende stappen uit om een bestaand abonnement aan uw Azure AD-adres lijst te koppelen:

1. Meld u aan en selecteer het abonnement dat u wilt gebruiken op de [pagina Abonnementen in azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Selecteer **map wijzigen**.

   ![Pagina Abonnementen, met de optie map wijzigen gemarkeerd](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Bekijk eventuele waarschuwingen die worden weer gegeven en selecteer **wijzigen**.

   ![Wijzig de Directory-pagina met de Directory waarnaar moet worden gewijzigd](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

   Nadat de Directory voor het abonnement is gewijzigd, wordt een bericht weer gegeven.

1. Selecteer **wisselende directory's** op de pagina abonnement om naar de nieuwe map te gaan.

   ![Pagina Directory-wisselaar, met voorbeeld gegevens](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

   Het kan enkele uren duren voordat alles goed wordt weer gegeven. Als het te lang lijkt te duren, controleert u het **filter voor algemeen abonnement**. Zorg ervoor dat het verplaatste abonnement niet is verborgen. Mogelijk moet u zich afmelden bij de Azure Portal en u opnieuw aanmelden om de nieuwe map te zien.

Het wijzigen van de abonnements Directory is een bewerking op service niveau. Dit heeft geen invloed op het eigendom van het abonnement. Als u de oorspronkelijke map wilt verwijderen, moet u het eigendom van het abonnement overdragen aan een nieuwe account beheerder. Zie [eigendom van een Azure-abonnement overdragen aan een ander account](../../cost-management-billing/manage/billing-subscription-transfer.md)voor meer informatie over het overdragen van het eigendom van de facturering.

## <a name="post-association-steps"></a>Stappen na de koppeling

Nadat u een abonnement aan een andere map hebt gekoppeld, moet u mogelijk de volgende taken uitvoeren om de bewerkingen te hervatten:

- Als u sleutel kluizen hebt, moet u de Tenant-ID van de sleutel kluis wijzigen. Zie voor meer informatie [een sleutel kluis Tenant-id wijzigen nadat een abonnement is verplaatst](../../key-vault/general/move-subscription.md).

- Als u door het systeem toegewezen beheerde identiteiten voor bronnen hebt gebruikt, moet u deze identiteiten weer inschakelen. Als u door de gebruiker toegewezen beheerde identiteiten hebt gebruikt, moet u deze identiteiten opnieuw maken. Nadat u de beheerde identiteiten opnieuw hebt ingeschakeld of opnieuw hebt gemaakt, moet u de machtigingen die zijn toegewezen aan deze identiteiten opnieuw instellen. Zie [Wat zijn beheerde identiteiten voor Azure-resources?](../managed-identities-azure-resources/overview.md) voor meer informatie.

- Als u een Azure Stack met dit abonnement hebt geregistreerd, moet u zich opnieuw registreren. Zie [Azure stack registreren bij Azure](/azure-stack/operator/azure-stack-registration)voor meer informatie.

- Raadpleeg [Een Azure-abonnement overzetten naar een andere Azure AD-map](../../role-based-access-control/transfer-subscription.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Als u een nieuwe Azure AD-Tenant wilt maken, raadpleegt u [Quick Start: een nieuwe Tenant maken in azure Active Directory](active-directory-access-create-new-tenant.md).

- Zie voor meer informatie over hoe Microsoft Azure het beheren van toegang tot resources, [klassieke abonnements beheerders rollen, Azure-rollen en Azure AD-beheerders rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Zie voor meer informatie over het toewijzen van rollen in azure AD, [beheerders en niet-beheerders rollen toewijzen aan gebruikers met Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
