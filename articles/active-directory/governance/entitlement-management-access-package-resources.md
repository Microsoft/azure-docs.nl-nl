---
title: Resource rollen wijzigen voor een toegangs pakket in het beheer van rechten van Azure AD-Azure Active Directory
description: Meer informatie over het wijzigen van de resource rollen voor een bestaand toegangs pakket in Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65f69cf492ec3e28d7f4aa86971dc6c91b34bdf5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644171"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Resource rollen wijzigen voor een toegangs pakket in het beheer van rechten van Azure AD

Als toegangs pakket beheer kunt u de resources in een toegangs pakket op elk gewenst moment wijzigen zonder dat u zich zorgen hoeft te maken over het inrichten van de toegang van de gebruiker tot de nieuwe resources of het verwijderen van de toegang tot de vorige resources. In dit artikel wordt beschreven hoe u de resource rollen voor een bestaand toegangs pakket wijzigt.

In deze video vindt u een overzicht van hoe u een toegangs pakket wijzigt.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Catalogus controleren op resources

Als u resources aan een toegangs pakket moet toevoegen, moet u controleren of de resources die nodig zijn in de catalogus beschikbaar zijn. Als u een Access package manager bent, kunt u geen resources toevoegen aan een catalogus, zelfs niet als u ze bezit. U bent beperkt tot het gebruik van de beschik bare resources in de catalogus.

**Vereiste rol:** globale beheerder, gebruikersbeheerder, cataloguseigenaar of toegangspakketbeheerder

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **catalogus** en open vervolgens de catalogus.

1. Klik in het linkermenu op **resources** om de lijst met resources in deze catalogus weer te geven.

    ![Lijst met resources in een catalogus](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Als u een Access package manager bent en u resources aan de catalogus wilt toevoegen, kunt u de eigenaar van de catalogus vragen om ze toe te voegen.

## <a name="add-resource-roles"></a>Resource rollen toevoegen

Een resource-rol is een verzameling machtigingen die zijn gekoppeld aan een resource. De manier waarop u resources beschikbaar maakt voor gebruikers, is door resource rollen toe te voegen aan uw toegangs pakket. U kunt resource rollen toevoegen voor groepen, teams, toepassingen en share point-sites.

**Vereiste rol:** globale beheerder, gebruikersbeheerder, cataloguseigenaar of toegangspakketbeheerder

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik in het linkermenu op **resource rollen**.

1. Klik op **resource rollen toevoegen** om de pagina Resource rollen toevoegen voor toegang tot het pakket te openen.

    ![Toegangs pakket-resource rollen toevoegen](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. Afhankelijk van of u een groep, team, toepassing of share point-site wilt toevoegen, voert u de stappen uit in een van de volgende secties van de resource-rol.

## <a name="add-a-group-or-team-resource-role"></a>Een resource functie voor een groep of team toevoegen

U kunt met het rechten beheer automatisch gebruikers toevoegen aan een groep of een team in micro soft teams wanneer ze een toegangs pakket toewijzen. 

- Wanneer een groep of team deel uitmaakt van een toegangs pakket en een gebruiker aan dat toegangs pakket is toegewezen, wordt de gebruiker toegevoegd aan die groep of dit team, als deze nog niet aanwezig is.
- Wanneer de toewijzing van een toegangs pakket voor een gebruiker verloopt, worden deze verwijderd uit de groep of het team, tenzij ze momenteel een toewijzing hebben aan een ander toegangs pakket dat dezelfde groep of hetzelfde team bevat.

U kunt elke [Azure AD-beveiligings groep of Microsoft 365 groep](../fundamentals/active-directory-groups-create-azure-portal.md)selecteren. Beheerders kunnen een groep toevoegen aan een catalogus. Catalog-eigen aren kunnen een wille keurige groep toevoegen aan de catalogus als ze eigenaar van de groep zijn. Houd bij het selecteren van een groep de volgende Azure AD-beperkingen in acht:

- Wanneer een gebruiker, inclusief een gast, wordt toegevoegd als een lid aan een groep of team, kunnen ze alle andere leden van die groep of dit team zien.
- Azure AD kan het lidmaatschap van een groep die is gesynchroniseerd vanuit Windows Server Active Directory met Azure AD Connect of die in Exchange Online is gemaakt als distributie groep niet wijzigen.  
- Het lidmaatschap van dynamische groepen kan niet worden bijgewerkt door een lid toe te voegen of te verwijderen, zodat dynamische groepslid maatschappen niet geschikt zijn voor gebruik met rechten beheer.

Zie groepen en [Microsoft 365 groepen en micro soft teams](/microsoftteams/office-365-groups) [vergelijken](/office365/admin/create-groups/compare-groups) voor meer informatie.

1. Klik op de pagina **resource rollen toevoegen aan toegangs pakket** op **groepen en teams** om het deel venster groepen selecteren te openen.

1. Selecteer de groepen en teams die u wilt toevoegen in het toegangs pakket.

    ![Toegangs pakket-resource rollen toevoegen-groepen selecteren](./media/entitlement-management-access-package-resources/group-select.png)

1. Klik op **Selecteren**.

    Wanneer u de groep of het team selecteert, wordt in de kolom **subtype** een van de volgende subtypen weer geven:

    | Subtype | Beschrijving |
    | --- | --- |
    | Beveiliging | Wordt gebruikt voor het verlenen van toegang tot resources. |
    | Distributie | Wordt gebruikt voor het verzenden van meldingen naar een groep personen. |
    | Microsoft 365 | Microsoft 365 groep waarvoor geen teams zijn ingeschakeld. Wordt gebruikt voor samen werking tussen gebruikers, zowel binnen als buiten uw bedrijf. |
    | Team | Microsoft 365 groep waarvoor teams zijn ingeschakeld. Wordt gebruikt voor samen werking tussen gebruikers, zowel binnen als buiten uw bedrijf. |

1. Selecteer in de lijst **rol** de optie **eigenaar** of **lid**.

    Doorgaans selecteert u de leden rol. Als u de rol eigenaar selecteert, kunnen gebruikers andere leden of eigen aren toevoegen of verwijderen.

    ![Toegangs pakket-resource functie toevoegen voor een groep of team](./media/entitlement-management-access-package-resources/group-role.png)

1. Klik op **Add**.

    Gebruikers met een bestaande toewijzing aan het toegangs pakket worden automatisch lid van deze groep of dit team wanneer ze worden toegevoegd.

## <a name="add-an-application-resource-role"></a>Een toepassings resource functie toevoegen

U kunt Azure AD automatisch gebruikers toegang geven tot een Azure AD-bedrijfs toepassing, met inbegrip van zowel SaaS-toepassingen als de toepassingen van uw organisatie die federatieve zijn voor Azure AD, wanneer aan een gebruiker een toegangs pakket is toegewezen. Voor toepassingen die worden geïntegreerd met Azure AD via federatieve eenmalige aanmelding, worden door Azure AD Federatie tokens uitgegeven voor gebruikers die zijn toegewezen aan de toepassing.

Toepassingen kunnen meerdere rollen hebben. Wanneer u een toepassing toevoegt aan een toegangs pakket en deze toepassing meer dan één rol heeft, moet u de juiste rol voor deze gebruikers opgeven. Als u toepassingen ontwikkelt, kunt u meer lezen over hoe deze functies worden toegevoegd aan uw toepassingen in [How to: configure the Role claim die is uitgegeven in het SAML-token voor zakelijke toepassingen](../develop/active-directory-enterprise-app-role-management.md).

Zodra een toepassingsrol deel uitmaakt van een toegangs pakket:

- Wanneer een gebruiker dat toegangs pakket toewijst, wordt de gebruiker toegevoegd aan die toepassingsrol, indien deze nog niet aanwezig is.
- Wanneer de toewijzing van een toegangs pakket van een gebruiker verloopt, wordt hun toegang verwijderd uit de toepassing, tenzij ze een toewijzing hebben aan een ander toegangs pakket dat die toepassingsrol bevat.

Hier volgen enkele overwegingen bij het selecteren van een toepassing:

- Toepassingen kunnen ook groepen hebben die zijn toegewezen aan hun rollen.  U kunt ervoor kiezen om een groep toe te voegen in plaats van een toepassingsrol in een toegangs pakket, maar de toepassing is echter niet zichtbaar voor de gebruiker als onderdeel van het toegangs pakket in de portal mijn toegang.

1. Klik op de pagina **resource rollen toevoegen aan toegangs pakket** op **toepassingen** om het deel venster toepassingen selecteren te openen.

1. Selecteer de toepassingen die u wilt toevoegen in het toegangs pakket.

    ![Toegangs pakket-resource rollen toevoegen-toepassingen selecteren](./media/entitlement-management-access-package-resources/application-select.png)

1. Klik op **Selecteren**.

1. Selecteer een toepassingsrol in de lijst met **rollen** .

    ![Toegangs pakket-resource functie toevoegen voor een toepassing](./media/entitlement-management-access-package-resources/application-role.png)

1. Klik op **Add**.

    Gebruikers met bestaande toewijzingen aan het toegangs pakket krijgen automatisch toegang tot deze toepassing wanneer ze worden toegevoegd.

## <a name="add-a-sharepoint-site-resource-role"></a>Een resource functie voor een share point-site toevoegen

Azure AD kan gebruikers automatisch toegang geven tot een share point online-site of share point online-site verzameling wanneer ze een toegangs pakket toewijzen.

1. Klik op de pagina **resource rollen toevoegen aan toegangs pakket** op **share point-sites** om het deel venster share point online-sites selecteren te openen.

    :::image type="content" source="media/entitlement-management-access-package-resources/resource-sharepoint-add.png" alt-text="Toegangs pakket-resource rollen toevoegen-share point-sites selecteren-Portal weergave":::

1. Selecteer de share point online-sites die u wilt toevoegen in het toegangs pakket.

    ![Toegangs pakket-resource rollen toevoegen-share point online-sites selecteren](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Klik op **Selecteren**.

1. Selecteer in de lijst **rol** een share point online-site functie.

    ![Toegangs pakket-resource functie toevoegen voor een share point online-site](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Klik op **Add**.

    Gebruikers met bestaande toewijzingen aan het toegangs pakket krijgen automatisch toegang tot deze share point online-site wanneer ze worden toegevoegd.

## <a name="remove-resource-roles"></a>Resource rollen verwijderen

**Vereiste rol:** globale beheerder, gebruikersbeheerder, cataloguseigenaar of toegangspakketbeheerder

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik in het linkermenu op **resource rollen**.

1. Zoek in de lijst met resource rollen de resource functie die u wilt verwijderen.

1. Klik op het weglatings teken (**...**) en klik vervolgens op **resource functie verwijderen**.

    Gebruikers met bestaande toewijzingen aan het toegangs pakket hebben automatisch hun toegang tot deze resource functie ingetrokken wanneer deze wordt verwijderd.

## <a name="when-changes-are-applied"></a>Wanneer wijzigingen worden toegepast

In het recht op beheer worden bulksgewijs wijzigingen in de toewijzing en resources in uw toegangs pakketten enkele keren per dag verwerkt door Azure AD. Als u dus een toewijzing maakt of de resource rollen van uw toegangs pakket wijzigt, kan het tot 24 uur duren voordat deze wijziging wordt doorgevoerd in azure AD, plus de hoeveelheid tijd die nodig is om deze wijzigingen door te geven aan andere micro soft Online Services of verbonden SaaS-toepassingen. Als uw wijziging van invloed is op slechts enkele objecten, duurt het waarschijnlijk slechts enkele minuten om toe te passen in azure AD, waarna andere onderdelen van Azure AD vervolgens detecteren dat de SaaS-toepassingen worden gewijzigd en bijgewerkt. Als uw wijziging gevolgen heeft voor duizenden objecten, neemt de wijziging langer in beslag. Als u bijvoorbeeld een toegangs pakket hebt met 2 toepassingen en 100 gebruikers toewijzingen en u besluit een share point-siterol toe te voegen aan het toegangs pakket, kan er een vertraging optreden totdat alle gebruikers deel uitmaken van die share point-siterol. U kunt de voortgang bewaken via het Azure AD-controle logboek, het Azure AD-inrichtings logboek en de audit logboeken van de share point-site.

Wanneer u een lid van een team verwijdert, worden deze ook uit de groep Microsoft 365 verwijderd. Het verwijderen uit de chatfunctionaliteit van het team kan worden uitgesteld. Zie [groepslid maatschap](/microsoftteams/office-365-groups#group-membership)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Een basisgroep maken en leden toevoegen met Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Procedure: de rol claim configureren die is uitgegeven in het SAML-token voor zakelijke toepassingen](../develop/active-directory-enterprise-app-role-management.md)
- [Inleiding tot share point online](/sharepoint/introduction)