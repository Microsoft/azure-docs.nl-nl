---
title: Algemene scenario's voor het beheer van rechten-Azure AD
description: Meer informatie over de stappen op hoog niveau die u moet volgen voor algemene scenario's in Azure Active Directory rechten beheer.
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
ms.openlocfilehash: c0f45a1481661aa350815560d795ab7411f99545
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92317832"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Algemene scenario's in het beheer van rechten van Azure AD

Er zijn verschillende manieren waarop u het rechten beheer voor uw organisatie kunt configureren. Als u echter net aan de slag gaat, is het handig om inzicht te krijgen in de algemene scenario's voor beheerders, catalogus eigenaars, toegangs pakket managers, goed keurders en aanvragers.

## <a name="delegate"></a>Gedelegeerde

### <a name="administrator-delegate-management-of-resources"></a>Beheerder: beheer van resources delegeren

1. [Video bekijken: delegeren naar Afdelings Manager](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Gebruikers delegeren aan de maker van de catalogus](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Catalogus Maker: beheer van resources delegeren

- [Een nieuwe catalogus maken](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Catalogus eigenaar: beheer van resources delegeren

1. [Mede-eigen aars toevoegen aan de catalogus](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Resources toevoegen aan de catalogus](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Catalogus eigenaar: beheer van toegangs pakketten delegeren

1. [Video bekijken: delegering van de catalogus eigenaar om toegang te krijgen tot pakket beheer](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Gebruikers delegeren voor toegang tot de rol pakket beheer](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>De toegang bepalen voor gebruikers in uw organisatie

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Access Package Manager: werk nemers in uw organisatie toestaan om toegang te vragen tot bronnen

1. [Een nieuw toegangspakket maken](entitlement-management-access-package-create.md#start-new-access-package)
1. [Groepen, teams, toepassingen of share point-sites toevoegen aan het toegangs pakket](entitlement-management-access-package-create.md#resource-roles)
1. [Een aanvraag beleid toevoegen om gebruikers in uw directory toe te staan om toegang aan te vragen](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Verval instellingen opgeven](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Aanvrager: toegang tot resources aanvragen

1. [Meld u aan bij de portal van mijn toegang](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Toegangs pakket zoeken
1. [Toegang aanvragen](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Goed keurder: aanvragen goed keuren voor resources

1. [Aanvraag openen in mijn Access-Portal](entitlement-management-request-approve.md#open-request)
1. [Toegang aanvragen goed keuren of weigeren](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Aanvrager: Bekijk de resources waarmee u al toegang hebt

1. [Meld u aan bij de portal van mijn toegang](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Actieve toegangs pakketten weer geven

## <a name="govern-access-for-users-outside-your-organization"></a>De toegang bepalen voor gebruikers buiten uw organisatie

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Beheerder: samen werken met een externe partner organisatie

1. [Lees hoe Access werkt voor externe gebruikers](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Instellingen voor externe gebruikers controleren](entitlement-management-external-users.md#settings-for-external-users)
1. [Een verbinding met de externe organisatie toevoegen](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Access Package Manager: samen werken met een externe partner organisatie

1. [Een nieuw toegangspakket maken](entitlement-management-access-package-create.md#start-new-access-package)
1. [Groepen, teams, toepassingen of share point-sites toevoegen aan het toegangs pakket](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Een aanvraag beleid toevoegen waarmee gebruikers die geen toegang hebben tot uw directory, toestemming kunnen vragen](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Verval instellingen opgeven](entitlement-management-access-package-create.md#lifecycle)
1. [De koppeling kopiëren om het toegangs pakket aan te vragen](entitlement-management-access-package-settings.md)
1. De koppeling naar uw contact partner van uw externe partner verzenden om deze te delen met hun gebruikers

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Aanvrager: toegang tot resources aanvragen als externe gebruiker

1. De toegangs pakket koppeling zoeken die u hebt ontvangen van uw contact persoon
1. [Meld u aan bij de portal van mijn toegang](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Toegang aanvragen](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Goed keurder: aanvragen goed keuren voor resources

1. [Aanvraag openen in mijn Access-Portal](entitlement-management-request-approve.md#open-request)
1. [Toegang aanvragen goed keuren of weigeren](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Aanvrager: de resources weer geven waarmee u al toegang hebt

1. [Meld u aan bij de portal van mijn toegang](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Actieve toegangs pakketten weer geven

## <a name="day-to-day-management"></a>Dagelijks beheer

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Access Package Manager: de resources voor een project bijwerken

1. [Video bekijken: dagelijks beheer: dingen gewijzigd](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Open het toegangs pakket
1. [Groepen, teams, toepassingen of share point-sites toevoegen of verwijderen](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Toegangs pakket beheer: de duur van een project bijwerken

1. [Video bekijken: dagelijks beheer: dingen gewijzigd](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Open het toegangs pakket
1. [Open de levenscyclus instellingen](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [De verloop instellingen bijwerken](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Access Package Manager: update hoe de toegang wordt goedgekeurd voor een project

1. [Video bekijken: dagelijks beheer: dingen gewijzigd](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Een bestaande beleids regel voor aanvraag instellingen openen](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [De goedkeurings instellingen bijwerken](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Toegangs pakket beheer: de personen voor een project bijwerken

1. [Video bekijken: dagelijks beheer: dingen gewijzigd](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Gebruikers verwijderen die geen toegang meer nodig hebben](entitlement-management-access-package-assignments.md)
1. [Een bestaande beleids regel voor aanvraag instellingen openen](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Gebruikers toevoegen die toegang nodig hebben](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Access Package Manager: specifieke gebruikers rechtstreeks toewijzen aan een toegangs pakket

1. [Als gebruikers verschillende levenscyclus instellingen nodig hebben, voegt u een nieuw beleid toe aan het toegangs pakket](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [Rechtstreeks toewijzen van specifieke gebruikers aan het toegangs pakket](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Toewijzingen en rapporten

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Beheerder: weer geven wie toewijzingen heeft aan een toegangs pakket

1. Een toegangs pakket openen
1. [Toewijzingen weer geven](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Rapporten en logboeken archiveren](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Beheerder: resources weer geven die aan gebruikers zijn toegewezen

1. [Toegangs pakketten voor een gebruiker weer geven](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Resource toewijzingen voor een gebruiker weer geven](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Programmatisch beheer

U kunt ook toegangs pakketten, catalogi, beleids regels, aanvragen en toewijzingen beheren met Microsoft Graph.  Een gebruiker in een geschikte rol met een toepassing die de gedelegeerde machtiging heeft, `EntitlementManagement.ReadWrite.All` kan de [rechten beheer-API](/graph/tutorial-access-package-api?view=graph-rest-beta)aanroepen.

## <a name="next-steps"></a>Volgende stappen

- [Delegering en rollen](entitlement-management-delegate.md)
- [Aanvraag proces en e-mail meldingen](entitlement-management-process.md)