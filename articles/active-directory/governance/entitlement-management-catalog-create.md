---
title: Een catalogus met resources maken & beheren-Azure AD
description: Meer informatie over het maken van een nieuwe container met resources en toegangs pakketten in Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b87fdd7c73d089c46d456491c02d49cb6f8883d
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97745960"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Een catalogus met resources maken en beheren in azure AD-rechts beheer

## <a name="create-a-catalog"></a>Een catalogus maken

Een catalogus is een container met resources en toegangs pakketten. U maakt een catalogus wanneer u gerelateerde resources en toegangs pakketten wilt groeperen. Degene die de catalogus maakt, wordt de eerste eigenaar van de catalogus. Een catalogus eigenaar kan extra catalogus eigenaren toevoegen.

**Vereiste rol:** Globale beheerder, gebruikers beheerder of maker van catalogus

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **catalogi**.

    ![Rechten beheer catalogi in de Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Klik op **nieuwe catalogus**.

1. Voer een unieke naam in voor de catalogus en geef een beschrijving op.

    Gebruikers zien deze informatie in de details van een toegangs pakket.

1. Als u wilt dat de toegangs pakketten in deze catalogus beschikbaar zijn voor gebruikers om aan te vragen zodra ze zijn gemaakt, stelt u **ingeschakeld** in op **Ja**.

1. Als u wilt toestaan dat gebruikers in geselecteerde externe directory's toegangs pakketten kunnen aanvragen in deze catalogus, stelt u **ingeschakeld voor externe gebruikers** in op **Ja**.

    ![Deel venster nieuwe catalogus](./media/entitlement-management-shared/new-catalog.png)

1. Klik op **maken** om de catalogus te maken.

### <a name="creating-a-catalog-programmatically"></a>Een catalogus programmatisch maken

U kunt ook een catalogus maken met behulp van Microsoft Graph.  Een gebruiker in een geschikte rol met een toepassing die de gedelegeerde machtiging heeft, `EntitlementManagement.ReadWrite.All` kan de API aanroepen om [een accessPackageCatalog te maken](/graph/api/accesspackagecatalog-post?view=graph-rest-beta).

## <a name="add-resources-to-a-catalog"></a>Resources toevoegen aan een catalogus

Als u resources wilt toevoegen aan een toegangs pakket, moeten de resources aanwezig zijn in een catalogus. De typen resources die u kunt toevoegen, zijn groepen, toepassingen en share point online-sites. De groepen kunnen door de Cloud gemaakte Microsoft 365 groepen of Azure AD-beveiligings groepen in de Cloud zijn gemaakt. De toepassingen kunnen Azure AD-bedrijfs toepassingen zijn, met inbegrip van zowel SaaS-toepassingen als uw eigen toepassingen die federatieve zijn voor Azure AD. De sites kunnen share point online-sites of share point online-site verzamelingen zijn.

**Vereiste rol:** De [vereiste rollen weer geven om resources toe te voegen aan een catalogus](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **catalogi** en open vervolgens de catalogus waaraan u resources wilt toevoegen.

1. Klik in het linkermenu op **resources**.

1. Klik op **resources toevoegen**.

1. Klik op een resource type: **groepen en teams**, **toepassingen** of **share point-sites**.

    Als u een resource die u wilt toevoegen niet ziet of u geen resource kunt toevoegen, moet u ervoor zorgen dat u beschikt over de vereiste functie voor Azure AD-Directory en rechten beheer. Mogelijk moet u iemand met de vereiste rollen hebben om de resource toe te voegen aan uw catalogus. Bekijk [Vereiste rollen om bronnen aan een catalogus toe te voegen](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog) voor meer informatie.

1. Selecteer een of meer resources van het type dat u wilt toevoegen aan de catalogus.

    ![Resources toevoegen aan een catalogus](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Wanneer u klaar bent, klikt u op **toevoegen**.

    Deze resources kunnen nu worden opgenomen in toegangs pakketten in de catalogus.

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Een resource via een programma aan een catalogus toevoegen

U kunt ook een resource toevoegen aan een catalogus met behulp van Microsoft Graph.  Een gebruiker in een geschikte rol of een catalogus-en resource-eigenaar, met een toepassing die de gedelegeerde `EntitlementManagement.ReadWrite.All` machtiging heeft, kan de API aanroepen om [een accessPackageResourceRequest te maken](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta).

## <a name="remove-resources-from-a-catalog"></a>Resources uit een catalogus verwijderen

U kunt resources verwijderen uit een catalogus. Een resource kan alleen uit een catalogus worden verwijderd als deze niet wordt gebruikt in een van de toegangs pakketten van de catalogus.

**Vereiste rol:** De [vereiste rollen weer geven om resources toe te voegen aan een catalogus](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **catalogi** en open vervolgens de catalogus waaruit u resources wilt verwijderen.

1. Klik in het linkermenu op **resources**.

1. Selecteer de resources die u wilt verwijderen.

1. Klik op **verwijderen** (of klik op het weglatings teken (**...**) en klik vervolgens op **resource verwijderen**.


## <a name="add-additional-catalog-owners"></a>Aanvullende catalogus eigenaren toevoegen

De gebruiker die een catalogus heeft gemaakt, wordt de eerste eigenaar van de catalogus. Als u het beheer van een catalogus wilt delegeren, voegt u gebruikers toe aan de rol van de catalogus eigenaar. Zo kunt u de verantwoordelijkheden voor het beheer van catalogi delen. 

Volg deze stappen om een gebruiker toe te wijzen aan de rol van de catalogus eigenaar:

**Vereiste rol:** Globale beheerder, gebruikers beheerder of catalogus eigenaar

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **catalogi** en open vervolgens de catalogus waaraan u beheerders wilt toevoegen.

1. Klik in het menu links op **rollen en beheerders**.

    ![Catalogiseert rollen en beheerders](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Klik op **eigen aren toevoegen** om de leden voor deze rollen te selecteren.

1. Klik op **selecteren** om deze leden toe te voegen.

## <a name="edit-a-catalog"></a>Een catalogus bewerken

U kunt de naam en beschrijving voor een catalogus bewerken. Gebruikers zien deze informatie in de details van een toegangs pakket.

**Vereiste rol:** Globale beheerder, gebruikers beheerder of catalogus eigenaar

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **catalogi** en open vervolgens de catalogus die u wilt bewerken.

1. Klik op de pagina **overzicht** van de catalogus op **bewerken**.

1. Bewerk de naam, beschrijving of ingeschakelde instellingen van de catalogus.

    ![Catalogus instellingen bewerken](./media/entitlement-management-shared/catalog-edit.png)

1. Klik op **Opslaan**.

## <a name="delete-a-catalog"></a>Een catalogus verwijderen

U kunt een catalogus verwijderen, maar alleen als deze geen toegangs pakketten heeft.

**Vereiste rol:** Globale beheerder, gebruikers beheerder of catalogus eigenaar

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **catalogi** en open vervolgens de catalogus die u wilt verwijderen.

1. Klik op **verwijderen** in het **overzicht** van de catalogus.

1. Klik op **Ja** in het bericht venster dat wordt weer gegeven.

### <a name="deleting-a-catalog-programmatically"></a>Een catalogus programmatisch verwijderen

U kunt ook een catalogus verwijderen met Microsoft Graph.  Een gebruiker in een geschikte rol met een toepassing die de gedelegeerde machtiging heeft, `EntitlementManagement.ReadWrite.All` kan de API aanroepen om [een accessPackageCatalog te verwijderen](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta).

## <a name="next-steps"></a>Volgende stappen

- [Toegangs beheer delegeren voor toegang tot pakket beheerders](entitlement-management-delegate-managers.md)