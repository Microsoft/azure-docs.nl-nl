---
title: Algemene scenario's voor rechtenbeheer - Azure AD
description: Meer informatie over de stappen op hoog niveau die u moet volgen voor algemene scenario's in Azure Active Directory rechtenbeheer.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28c16e4d73fc2379806e1a2bce2fa5dbb3247fed
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531966"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Algemene scenario's in Azure AD-rechtenbeheer

Er zijn verschillende manieren waarop u rechtenbeheer voor uw organisatie kunt configureren. Als u echter nog maar net begint, is het handig om inzicht te krijgen in de algemene scenario's voor beheerders, cataloguseigenaren, toegangspakketbeheerders, fiatteurs en requestors.

## <a name="delegate"></a>Gedelegeerde

### <a name="administrator-delegate-management-of-resources"></a>Beheerder: Beheer van resources delegeren

1. [Video bekijken: Delegatie van IT naar afdelingsmanager](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Gebruikers delegeren aan rol catalogusmaker](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Catalogusmaker: Beheer van resources delegeren

- [Een nieuwe catalogus maken](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Cataloguseigenaar: Beheer van resources delegeren

1. [Mede-eigenaren toevoegen aan de catalogus](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Resources toevoegen aan de catalogus](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Cataloguseigenaar: Beheer van toegangspakketten delegeren

1. [Video bekijken: Overdracht van cataloguseigenaar naar toegang tot pakketbeheer](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Gebruikers delegeren voor toegang tot pakketbeheerrol](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Toegang voor gebruikers in uw organisatie regelen

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Toegangspakketbeheerder: toestaan dat werknemers in uw organisatie toegang tot resources aanvragen

1. [Een nieuw toegangspakket maken](entitlement-management-access-package-create.md#start-new-access-package)
1. [Groepen, Teams, toepassingen of SharePoint-sites toevoegen voor toegang tot het pakket](entitlement-management-access-package-create.md#resource-roles)
1. [Een aanvraagbeleid toevoegen zodat gebruikers in uw directory toegang kunnen aanvragen](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Verloopinstellingen opgeven](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Requestor: Toegang tot resources aanvragen

1. [Meld u aan bij de Mijn toegang portal](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Toegangspakket zoeken
1. [Toegang aanvragen](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Goedkeurder: Aanvragen voor resources goedkeuren

1. [Aanvraag openen in Mijn toegang portal](entitlement-management-request-approve.md#open-request)
1. [Toegangsaanvraag goedkeuren of weigeren](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Requestor: Bekijk de resources tot wie u al toegang hebt

1. [Meld u aan bij de Mijn toegang portal](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Actieve toegangspakketten weergeven

## <a name="govern-access-for-users-outside-your-organization"></a>Toegang regelen voor gebruikers buiten uw organisatie

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Beheerder: samenwerken met een externe partnerorganisatie

1. [Lees hoe toegang werkt voor externe gebruikers](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Instellingen voor externe gebruikers controleren](entitlement-management-external-users.md#settings-for-external-users)
1. [Een verbinding toevoegen aan de externe organisatie](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Toegangspakketbeheer: samenwerken met een externe partnerorganisatie

1. [Een nieuw toegangspakket maken](entitlement-management-access-package-create.md#start-new-access-package)
1. [Groepen, Teams, toepassingen of SharePoint-sites toevoegen voor toegang tot het pakket](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Een aanvraagbeleid toevoegen om toe te staan dat gebruikers die zich niet in uw directory hebben, toegang kunnen aanvragen](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Verloopinstellingen opgeven](entitlement-management-access-package-create.md#lifecycle)
1. [Kopieer de koppeling om het toegangspakket aan te vragen](entitlement-management-access-package-settings.md)
1. Verzend de koppeling naar de contactpersoon van uw externe partner om deze te delen met hun gebruikers

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Requestor: Toegang tot resources aanvragen als externe gebruiker

1. Zoek de koppeling naar het toegangspakket dat u van uw contactpersoon hebt ontvangen
1. [Meld u aan bij Mijn toegang portal](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Toegang aanvragen](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Goedkeurder: Aanvragen voor resources goedkeuren

1. [Aanvraag openen in Mijn toegang portal](entitlement-management-request-approve.md#open-request)
1. [Toegangsaanvraag goedkeuren of weigeren](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Requestor: Bekijk de resources waar u al toegang toe hebt

1. [Meld u aan bij Mijn toegang portal](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Actieve toegangspakketten weergeven

## <a name="day-to-day-management"></a>Dagelijks beheer

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Toegangspakketbeheer: de resources voor een project bijwerken

1. [Video bekijken: Dagelijks beheer: Dingen zijn gewijzigd](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Het toegangspakket openen
1. [Groepen, Teams, toepassingen of SharePoint-sites toevoegen of verwijderen](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Toegangspakketbeheer: de duur van een project bijwerken

1. [Video bekijken: Dagelijks beheer: Dingen zijn gewijzigd](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Het toegangspakket openen
1. [De instellingen voor de levenscyclus openen](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [De verloopinstellingen bijwerken](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Toegangspakketbeheer: bijwerken hoe toegang wordt goedgekeurd voor een project

1. [Bekijk de video: Dagelijks beheer: Dingen zijn gewijzigd](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Een bestaand beleid voor aanvraaginstellingen openen](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [De goedkeuringsinstellingen bijwerken](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Toegangspakketbeheer: werk de personen voor een project bij

1. [Bekijk de video: Dagelijks beheer: Dingen zijn gewijzigd](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Gebruikers verwijderen die geen toegang meer nodig hebben](entitlement-management-access-package-assignments.md)
1. [Een bestaand beleid voor aanvraaginstellingen openen](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Gebruikers toevoegen die toegang nodig hebben](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Toegangspakketbeheer: specifieke gebruikers rechtstreeks toewijzen aan een toegangspakket

1. [Als gebruikers verschillende levenscyclusinstellingen nodig hebben, voegt u een nieuw beleid toe aan het toegangspakket](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Specifieke gebruikers rechtstreeks toewijzen aan het toegangspakket](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Toewijzingen en rapporten

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Beheerder: weergeven wie toewijzingen heeft aan een toegangspakket

1. Een toegangspakket openen
1. [Toewijzingen weergeven](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Rapporten en logboeken archiveren](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Beheerder: Resources weergeven die zijn toegewezen aan gebruikers

1. [Toegangspakketten voor een gebruiker weergeven](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Resourcetoewijzingen voor een gebruiker weergeven](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Programmatisch beheer

U kunt ook toegangspakketten, catalogi, beleidsregels, aanvragen en toewijzingen beheren met behulp Microsoft Graph.  Een gebruiker met een geschikte rol met een toepassing met de gedelegeerde machtiging `EntitlementManagement.ReadWrite.All` kan de [rechtenbeheer-API aanroepen.](/graph/tutorial-access-package-api)

## <a name="next-steps"></a>Volgende stappen

- [Delegering en rollen](entitlement-management-delegate.md)
- [Proces- en e-mailmeldingen aanvragen](entitlement-management-process.md)