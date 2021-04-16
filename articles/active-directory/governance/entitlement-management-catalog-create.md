---
title: Een & van resources in rechtenbeheer maken - Azure AD
description: Meer informatie over het maken van een nieuwe container met resources en toegangspakketten in Azure Active Directory rechtenbeheer.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8cea26bcb0926cd3af360a6489377767d681079
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532561"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Een catalogus met resources maken en beheren in Azure AD-rechtenbeheer

## <a name="create-a-catalog"></a>Een catalogus maken

Een catalogus is een container met resources en toegangspakketten. U maakt een catalogus wanneer u gerelateerde resources en toegangspakketten wilt groepeert. Degene die de catalogus maakt, wordt de eerste cataloguseigenaar. Een cataloguseigenaar kan extra cataloguseigenaren toevoegen.

**Vereiste rol:** Globale beheerder, gebruikersbeheerder of catalogusmaker

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **Catalogs.**

    ![Rechtenbeheercatalogi in de Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Klik **op Nieuwe catalogus.**

1. Voer een unieke naam in voor de catalogus en geef een beschrijving op.

    Gebruikers zien deze informatie in de details van een toegangspakket.

1. Als u wilt dat de toegangspakketten in deze catalogus beschikbaar zijn voor gebruikers zodra ze zijn gemaakt, stelt u Ingeschakeld in **op** **Ja.**

1. Als u wilt toestaan dat gebruikers in geselecteerde externe directories toegangspakketten kunnen aanvragen in deze catalogus, stelt u Ingeschakeld voor **externe** gebruikers in op **Ja.**

    ![Deelvenster Nieuwe catalogus](./media/entitlement-management-shared/new-catalog.png)

1. Klik **op Maken** om de catalogus te maken.

### <a name="creating-a-catalog-programmatically"></a>Programmatisch een catalogus maken

U kunt ook een catalogus maken met behulp van Microsoft Graph.  Een gebruiker met een geschikte rol met een toepassing met de gedelegeerde machtiging kan de API aanroepen om `EntitlementManagement.ReadWrite.All` [een accessPackageCatalog te maken.](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true)

## <a name="add-resources-to-a-catalog"></a>Resources toevoegen aan een catalogus

Als u resources wilt opnemen in een toegangspakket, moeten de resources aanwezig zijn in een catalogus. De typen resources die u kunt toevoegen, zijn groepen, toepassingen en SharePoint Online-sites. De groepen kunnen in de cloud worden gemaakt Microsoft 365 Groepen of door de cloud gemaakte Azure AD-beveiligingsgroepen. De toepassingen kunnen Azure AD-bedrijfstoepassingen zijn, waaronder SaaS-toepassingen en uw eigen toepassingen die zijn ge federeerd naar Azure AD. De sites kunnen SharePoint Online-sites of SharePoint Online-siteverzamelingen zijn.

**Vereiste rol:** Zie [Vereiste rollen om resources toe te voegen aan een catalogus](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **Catalogs** en open vervolgens de catalogus waar u resources aan wilt toevoegen.

1. Klik in het menu links op **Resources**.

1. Klik **op Resources toevoegen.**

1. Klik op een resourcetype: **Groepen en Teams,** **Toepassingen** of **SharePoint-sites.**

    Als u geen resource ziet die u wilt toevoegen of als u geen resource kunt toevoegen, moet u ervoor zorgen dat u de vereiste Azure AD-directoryrol en rechtenbeheerrol hebt. Mogelijk moet u iemand met de vereiste rollen de resource aan uw catalogus laten toevoegen. Bekijk [Vereiste rollen om bronnen aan een catalogus toe te voegen](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog) voor meer informatie.

1. Selecteer een of meer resources van het type dat u aan de catalogus wilt toevoegen.

    ![Resources toevoegen aan een catalogus](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Wanneer u klaar bent, klikt u **op Toevoegen.**

    Deze resources kunnen nu worden opgenomen in toegangspakketten in de catalogus.

### <a name="add-a-multi-geo-sharepoint-site-preview"></a>Een SharePoint-site met meerdere geografische regio's toevoegen (preview)

1. Als u [Multi-Geo hebt](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) ingeschakeld voor SharePoint, selecteert u de omgeving waar u sites uit wilt selecteren.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multigeo-select.png" alt-text="Toegangspakket - Resourcerollen toevoegen - Selecteer SharePoint Meerdere geografische sites":::

1. Selecteer vervolgens de sites die u wilt toevoegen aan de catalogus. 

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Programmatisch een resource toevoegen aan een catalogus

U kunt ook een resource toevoegen aan een catalogus met behulp van Microsoft Graph.  Een gebruiker met een geschikte rol, of een catalogus en resource-eigenaar, met een toepassing met de gedelegeerde machtiging kan de API aanroepen om een `EntitlementManagement.ReadWrite.All` [accessPackageResourceRequest te maken.](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true)

## <a name="remove-resources-from-a-catalog"></a>Resources uit een catalogus verwijderen

U kunt resources uit een catalogus verwijderen. Een resource kan alleen worden verwijderd uit een catalogus als deze niet wordt gebruikt in een van de toegangspakketten van de catalogus.

**Vereiste rol:** Zie [Vereiste rollen om resources toe te voegen aan een catalogus](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **Catalogs** en open vervolgens de catalogus waar u resources uit wilt verwijderen.

1. Klik in het menu links op **Resources.**

1. Selecteer de resources die u wilt verwijderen.

1. Klik **op Verwijderen** (of klik op het beletselteken (**...**) en klik vervolgens op **Resource verwijderen.**


## <a name="add-additional-catalog-owners"></a>Aanvullende cataloguseigenaren toevoegen

De gebruiker die een catalogus heeft gemaakt, wordt de eerste cataloguseigenaar. Als u het beheer van een catalogus wilt delegeren, voegt u gebruikers toe aan de rol van cataloguseigenaar. Dit helpt bij het delen van de verantwoordelijkheden van catalogusbeheer. 

Volg deze stappen om een gebruiker toe te wijzen aan de rol van cataloguseigenaar:

**Vereiste rol:** Globale beheerder, gebruikersbeheerder of cataloguseigenaar

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **Catalogs** en open vervolgens de catalogus waar u beheerders aan wilt toevoegen.

1. Klik in het menu links op **Rollen en beheerders.**

    ![Catalogirollen en beheerders](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Klik **op Eigenaren toevoegen** om de leden voor deze rollen te selecteren.

1. Klik **op Selecteren** om deze leden toe te voegen.

## <a name="edit-a-catalog"></a>Een catalogus bewerken

U kunt de naam en beschrijving voor een catalogus bewerken. Gebruikers zien deze informatie in de details van een toegangspakket.

**Vereiste rol:** Globale beheerder, Gebruikersbeheerder of Cataloguseigenaar

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **Catalogs** en open vervolgens de catalogus die u wilt bewerken.

1. Klik op de pagina Overzicht **van de** catalogus op **Bewerken.**

1. Bewerk de naam, beschrijving of ingeschakelde instellingen van de catalogus.

    ![Catalogusinstellingen bewerken](./media/entitlement-management-shared/catalog-edit.png)

1. Klik op **Opslaan**.

## <a name="delete-a-catalog"></a>Een catalogus verwijderen

U kunt een catalogus verwijderen, maar alleen als deze geen toegangspakketten heeft.

**Vereiste rol:** Globale beheerder, Gebruikersbeheerder of Cataloguseigenaar

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **Catalogs** en open vervolgens de catalogus die u wilt verwijderen.

1. Klik in het overzicht van **de** catalogus op **Verwijderen.**

1. Klik in het berichtvenster dat wordt weergegeven op **Ja.**

### <a name="deleting-a-catalog-programmatically"></a>Een catalogus programmatisch verwijderen

U kunt een catalogus ook verwijderen met behulp van Microsoft Graph.  Een gebruiker met een geschikte rol met een toepassing met de gedelegeerde machtiging kan de API aanroepen om `EntitlementManagement.ReadWrite.All` [een accessPackageCatalog te verwijderen.](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true)

## <a name="next-steps"></a>Volgende stappen

- [Toegangsbeheer delegeren aan toegangspakketbeheerders](entitlement-management-delegate-managers.md)
