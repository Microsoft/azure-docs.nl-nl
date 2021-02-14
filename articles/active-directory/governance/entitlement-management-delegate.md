---
title: Delegering en rollen in rechten beheer-Azure AD
description: Meer informatie over het overdragen van toegangs beheer van IT-beheerders aan afdelings managers en project managers zodat ze de toegang zelf kunnen beheren.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8e6c13600cb5940351d31b54af403584cc68a5f
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515645"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Overdracht en rollen in het beheer van rechten van Azure AD

Standaard kunnen globale beheerders en gebruikers beheerders alle aspecten van het beheer van rechten van Azure AD maken en beheren. Het is echter mogelijk dat de gebruikers in deze rollen niet alle situaties kennen waarin toegangs pakketten zijn vereist. Normaal gesp roken is het gebruikers binnen de respectieve afdelingen, teams of projecten die weten wie ze samen werken, met welke resources en hoe lang. In plaats van onbeperkte machtigingen te verlenen aan niet-beheerders, kunt u gebruikers de minste machtigingen verlenen die ze nodig hebben om hun taak uit te voeren en te voor komen dat er conflicterende of onjuiste toegangs rechten zijn.

In deze video vindt u een overzicht van de manier waarop u Access governance van de IT-beheerder kunt delegeren aan gebruikers die geen beheerder zijn.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Voor beeld van gemachtigde

Als u wilt weten hoe u Access governance kunt delegeren in het recht op beheer, kunt u een voor beeld overwegen. Stel dat uw organisatie de volgende beheerder en managers heeft.

![Delegeren van IT-beheerder naar managers](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Als IT-beheerder heeft Hana contact personen in elke afdeling: Mamta in marketing, financiële gegevens markeren en Jan in juridisch die verantwoordelijk zijn voor de resources en bedrijfs kritieke inhoud van hun afdeling.

Met het recht op rechten kunt u toegangs beheer aan deze niet-beheerders delegeren, omdat ze weten welke gebruikers toegang nodig hebben, voor hoe lang en op welke resources. Dit zorgt ervoor dat de juiste mensen de toegang voor hun afdelingen beheren.

Dit is een manier waarop Hana Access governance kan delegeren aan de afdelingen marketing, financiën en juridisch.

1. Hana maakt een nieuwe Azure AD-beveiligings groep en voegt Mamta, Mark en Joe toe als leden van de groep.

1. Hana voegt die groep toe aan de rol catalogus makers.

    Mamta, Mark en Joe kunnen nu catalogi maken voor hun afdelingen, resources toevoegen die hun afdelingen nodig hebben en verdere delegering uitvoeren in de catalogus.

    Houd er rekening mee dat Mamta, Mark en Joe de catalogussen van elkaar niet kunnen zien.

1. Mamta maakt een **marketing** catalogus. Dit is een container met resources.

1. Mamta voegt de resources die haar marketing afdeling eigendom is van deze catalogus toe.

1. Mamta kan extra personen van haar afdeling toevoegen als catalogus eigenaars voor deze catalogus. Zo kunt u de verantwoordelijkheden voor het beheer van catalogi delen.

1. Mamta kan het maken en beheren van toegangs pakketten in de marketing catalogus verder overdragen aan project managers in de marketing afdeling. Ze kan dit doen door ze toe te wijzen aan de rol Access Package Manager. Een Access package manager kan toegangs pakketten maken en beheren. 

In het volgende diagram ziet u catalogi met resources voor de afdelingen marketing, financiën en juridisch. Met behulp van deze catalogi kunnen project managers toegangs pakketten voor hun teams of projecten maken.

![Voor beeld van een recht op gedelegeerd beheer](./media/entitlement-management-delegate/elm-delegate.png)

Na de overdracht heeft de marketing afdeling mogelijk rollen die vergelijkbaar zijn met die in de volgende tabel.

| Gebruiker | Functie | Azure AD-rol | Rol van rechten beheer |
| --- | --- | --- | --- |
| Hana | IT-beheerder | Globale beheerder of gebruikers beheerder |  |
| Mamta | Marketing Manager | Gebruiker | Catalogus maker en catalogus eigenaar |
| Bob | Marketing Lead | Gebruiker | Catalogus eigenaar |
| Jessica | Marketing project manager | Gebruiker | Toegangs pakket beheer |

## <a name="entitlement-management-roles"></a>Rechten beheer rollen

Het beheer van rechten heeft de volgende rollen die specifiek zijn voor het beheer van rechten.

| Rol van rechten beheer | Description |
| --- | --- |
| Maker van catalogus | Maak en beheer catalogi. Doorgaans een IT-beheerder die geen globale beheerder of een resource-eigenaar is voor een verzameling resources. De persoon die een catalogus maakt, wordt automatisch de eerste catalogus eigenaar van de catalogus en kan extra catalogus eigenaren toevoegen. Een maker van de catalogus kan geen catalogi beheren of weer geven die niet eigenaar zijn en resources niet kunnen toevoegen die niet zijn opgenomen in een catalogus. Als de maker van de catalogus een andere catalogus moet beheren of resources toevoegt waarvan ze geen eigenaar zijn, kunnen ze een mede-eigenaar van die catalogus of resource aanvragen. |
| Catalogus eigenaar | Bestaande catalogi bewerken en beheren. Doorgaans een IT-beheerder of eigenaar van een resource, of een gebruiker die de eigenaar van de catalogus heeft opgegeven. |
| Toegangs pakket beheer | Bewerk en beheer alle bestaande toegangs pakketten in een catalogus. |
| Pakket toewijzings beheer openen | Alle bestaande toewijzingen van het toegangs pakket bewerken en beheren. |

Daarnaast hebben een aangewezen goed keurder en een aanvrager van een toegangs pakket ook rechten, hoewel ze geen rollen zijn.

| Rechts | Description |
| --- | --- |
| Fiatteur | Geautoriseerd door een beleid voor het goed keuren of weigeren van aanvragen voor toegang tot pakketten, hoewel ze de toegangs pakket definities niet kunnen wijzigen. |
| Requestor | Geautoriseerd door een beleid van een toegangs pakket om het toegangs pakket aan te vragen. |

De volgende tabel geeft een lijst van de taken die de rechten voor het beheer kunnen uitvoeren.

| Taak | Beheerder | Maker van catalogus | Catalogus eigenaar | Toegangs pakket beheer | Pakket toewijzings beheer openen |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Delegeren aan een maker van de catalogus](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |  |
| [Een verbonden organisatie toevoegen](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |  |
| [Een nieuwe catalogus maken](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [Een resource toevoegen aan een catalogus](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Een catalogus eigenaar toevoegen](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Een catalogus bewerken](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Een catalogus verwijderen](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Delegeren aan een Access package manager](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Een Access Package Manager verwijderen](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [Een nieuw toegangs pakket maken in een catalogus](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [Resource rollen wijzigen in een toegangs pakket](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Beleid maken en bewerken](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Een gebruiker rechtstreeks toewijzen aan een toegangs pakket](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Een gebruiker rechtstreeks uit een toegangs pakket verwijderen](entitlement-management-access-package-assignments.md#remove-an-assignment) | :heavy_check_mark:  |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Weer geven wie een toewijzing voor een toegangs pakket heeft](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Aanvragen van een toegangs pakket weer geven](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [De leverings fouten van een aanvraag weer geven](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [Een aanvraag opnieuw verwerken](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [Een aanvraag in behandeling annuleren](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Een toegangs pakket verbergen](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Een toegangs pakket verwijderen](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Vereiste rollen om resources toe te voegen aan een catalogus

Een globale beheerder kan een groep toevoegen of verwijderen (door de Cloud gemaakte beveiligings groepen of door de Cloud gemaakte Microsoft 365 groepen), de toepassing of de share point online-site in een catalogus. Een gebruikers beheerder kan een wille keurige groep of toepassing toevoegen aan of verwijderen uit een catalogus, met uitzonde ring van een groep die is geconfigureerd als toegewezen aan een directory-rol. Houd er rekening mee dat een gebruikers beheerder toegangs pakketten kan beheren in een catalogus die groepen bevat die zijn geconfigureerd als toegewezen aan een directory-rol.

Voor een gebruiker die geen globale beheerder of gebruikers beheerder is, om groepen, toepassingen of share point online-sites toe te voegen aan een catalogus, moet die gebruiker *zowel* de vereiste beheer functie voor Azure AD-adreslijst functies als de bevoegdheid van de catalogus eigenaar hebben. De volgende tabel geeft een lijst van de functie combinaties die nodig zijn om resources toe te voegen aan een catalogus. Als u resources uit een catalogus wilt verwijderen, moet u dezelfde rollen hebben.

| Azure AD-adreslijst functie | Rol van rechten beheer | Kan beveiligings groep toevoegen | Kan Microsoft 365 groep toevoegen | Kan app toevoegen | Kan de share point online-site toevoegen |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Globale beheerder](../roles/permissions-reference.md) | n.v.t. |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gebruikersbeheerder](../roles/permissions-reference.md) | n.v.t. |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune-beheerder](../roles/permissions-reference.md) | Catalogus eigenaar | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange-beheerder](../roles/permissions-reference.md) | Catalogus eigenaar |  | :heavy_check_mark: |  |  |
| [Teams service-beheerder](../roles/permissions-reference.md) | Catalogus eigenaar |  | :heavy_check_mark: |  |  |
| [SharePoint-beheerder](../roles/permissions-reference.md) | Catalogus eigenaar |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Toepassings beheerder](../roles/permissions-reference.md) | Catalogus eigenaar |  |  | :heavy_check_mark: |  |
| [Beheerder van de Cloud toepassing](../roles/permissions-reference.md) | Catalogus eigenaar |  |  | :heavy_check_mark: |  |
| Gebruiker | Catalogus eigenaar | Alleen als de groeps eigenaar | Alleen als de groeps eigenaar | Alleen als de eigenaar van de app |  |

Als u de minst bevoorrechte rol voor een taak wilt bepalen, kunt u ook verwijzen naar [beheerders rollen per beheer taak in azure Active Directory](../roles/delegate-by-task.md#entitlement-management).

## <a name="next-steps"></a>Volgende stappen

- [Toegangs beheer van de catalogus overdragen aan makers van catalogi](entitlement-management-delegate-catalog.md)
- [Een catalogus met resources maken en beheren](entitlement-management-catalog-create.md)
