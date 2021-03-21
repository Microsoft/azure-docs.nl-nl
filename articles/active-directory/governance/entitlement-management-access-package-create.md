---
title: Een nieuw toegangs pakket maken in het recht beheer-Azure AD
description: Meer informatie over het maken van een nieuw toegangs pakket met resources die u wilt delen in Azure Active Directory rechten beheer.
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
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3df08272b352ee789c9879b1118105c435cffbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011082"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Een nieuw toegangs pakket maken in het beheer van rechten van Azure AD

Met een toegangs pakket kunt u een eenmalige configuratie uitvoeren van resources en beleids regels die automatisch de toegang beheren voor de levens duur van het toegangs pakket. In dit artikel wordt beschreven hoe u een nieuw toegangs pakket maakt.

## <a name="overview"></a>Overzicht

Alle toegangs pakketten moeten in een container worden geplaatst die een catalogus wordt genoemd. Een catalogus definieert welke resources u aan uw toegangs pakket kunt toevoegen. Als u geen catalogus opgeeft, wordt uw toegangs pakket in de algemene catalogus geplaatst. Op dit moment kunt u een bestaand toegangs pakket niet naar een andere catalogus verplaatsen.

Als u een Access package manager bent, kunt u geen resources toevoegen die eigendom zijn van een catalogus. U bent beperkt tot het gebruik van de beschik bare resources in de catalogus. Als u resources aan een catalogus wilt toevoegen, kunt u de eigenaar van de catalogus vragen.

Alle toegangs pakketten moeten ten minste één beleid hebben. Beleids regels opgeven wie het toegangs pakket kan aanvragen en ook de instellingen voor goed keuring en levens cyclus. Wanneer u een nieuw toegangs pakket maakt, kunt u een eerste beleids regel voor gebruikers in uw Directory maken, voor gebruikers die niet in uw Directory zijn, alleen voor beheerders direct toewijzingen of u kunt ervoor kiezen om het beleid later te maken.

![Een toegangspakket maken](./media/entitlement-management-access-package-create/access-package-create.png)

Dit zijn de stappen op hoog niveau voor het maken van een nieuw toegangs pakket.

1. In Identity governance start u het proces voor het maken van een nieuw toegangs pakket.

1. Selecteer de catalogus waarin u het toegangs pakket wilt maken.

1. Resources van de catalogus toevoegen aan uw toegangs pakket.

1. Wijs resource rollen toe voor elke resource.

1. Gebruikers opgeven die toegang kunnen aanvragen.

1. Geef een goedkeurings instelling op.

1. Levenscyclus instellingen opgeven.

## <a name="start-new-access-package"></a>Nieuw toegangs pakket starten

**Vereiste rol:** globale beheerder, gebruikersbeheerder, cataloguseigenaar of toegangspakketbeheerder

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Klik op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het linkermenu op **Toegangspakketten**.

1. Klik op **Nieuw toegangspakket**.
   
    ![Rechtenbeheer in de Azure-portal](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Basisbeginselen

Op het tabblad **basis beginselen** geeft u het toegangs pakket een naam en geeft u op in welke catalogus u het toegangs pakket wilt maken.

1. Voer een weergave naam en een beschrijving in voor het toegangs pakket. Gebruikers krijgen deze informatie te zien wanneer ze een aanvraag indienen voor het toegangs pakket.

1. Selecteer in de vervolg keuzelijst **catalogus** de catalogus waarin u het toegangs pakket wilt maken. U kunt bijvoorbeeld een catalogus eigenaar hebben die alle marketing resources beheert die kunnen worden aangevraagd. In dit geval kunt u de marketing catalogus selecteren.

    U ziet alleen catalogi waarvoor u gemachtigd bent om toegangs pakketten te maken in. Als u een toegangs pakket wilt maken in een bestaande catalogus, moet u een globale beheerder of gebruikers beheerder zijn of moet u een catalogus eigenaar zijn of toegang krijgen tot pakket beheer in die catalogus.

    ![Toegangs pakket-basis beginselen](./media/entitlement-management-access-package-create/basics.png)

    Als u een globale beheerder, een gebruikers beheerder of een maker van de catalogus bent en u uw toegangs pakket wilt maken in een nieuwe catalogus die niet wordt weer gegeven, klikt u op **nieuwe catalogus maken**. Voer de naam en beschrijving van de catalogus in en klik vervolgens op **maken**.

    Het toegangs pakket dat u maakt en alle resources die erin worden opgenomen, wordt toegevoegd aan de nieuwe catalogus. U kunt later ook extra catalogus eigenaren toevoegen.

1. Klik op **Volgende**.

## <a name="resource-roles"></a>Resourcerollen

Op het tabblad **resource rollen** selecteert u de resources die u wilt toevoegen in het toegangs pakket. Gebruikers die het toegangs pakket aanvragen en ontvangen, ontvangen alle resource rollen in het toegangs pakket.

1. Klik op het resource type dat u wilt toevoegen (**groepen en teams**, **toepassingen** of **share point-sites**).

1. Selecteer in het deel venster selecteren dat wordt weer gegeven een of meer resources uit de lijst.

    ![Toegangs pakket-resource rollen](./media/entitlement-management-access-package-create/resource-roles.png)

    Als u het toegangs pakket maakt in de algemene catalogus of een nieuwe catalogus, kunt u een wille keurige bron kiezen uit de map waarvan u de eigenaar bent. U moet ten minste een globale beheerder, een gebruikers beheerder of een maker van de catalogus zijn.

    Als u het toegangs pakket maakt in een bestaande catalogus, kunt u alle resources selecteren die zich al in de catalogus bevinden zonder dat hiervoor eigenaar van is.

    Als u een globale beheerder, een gebruikers beheerder of een catalogus eigenaar bent, hebt u de extra optie om resources te selecteren waarvan u eigenaar bent die nog niet in de catalogus staan. Als u resources selecteert die zich momenteel niet in de geselecteerde catalogus bekomen, worden deze resources ook toegevoegd aan de catalogus zodat andere catalogus beheerders toegangs pakketten met kunnen maken. Als u alle resources wilt zien die kunnen worden toegevoegd aan de catalogus, schakelt u het selectie vakje **alles weer geven** boven in het deel venster selecteren in. Als u alleen resources wilt selecteren die zich momenteel in de geselecteerde catalogus bevinden, schakelt u het selectie vakje **alle** uitschakelen (standaard status) uit.

1. Wanneer u de resources hebt geselecteerd, selecteert u in de lijst met **rollen** de rol die gebruikers moeten worden toegewezen aan de resource.

    ![Toegangs pakket-resource functie selecteren](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Klik op **Volgende**.

>[!NOTE]
>U kunt dynamische groepen toevoegen aan een catalogus en aan een toegangs pakket. U kunt echter alleen de rol eigenaar selecteren bij het beheren van een dynamische groeps bron in een toegangs pakket.

## <a name="requests"></a>Aanvragen

Op het tabblad **aanvragen** maakt u het eerste beleid om op te geven wie het toegangs pakket en ook goedkeurings instellingen kan aanvragen. Later kunt u meer aanvraag beleidsregels maken zodat extra groepen gebruikers het toegangs pakket kunnen aanvragen met hun eigen goedkeurings instellingen.

![Tabblad toegang tot pakket-aanvragen](./media/entitlement-management-access-package-create/requests.png)

Afhankelijk van wie u dit toegangs pakket wilt kunnen aanvragen, voert u de stappen uit in een van de volgende secties.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Controleren en maken

Op het tabblad **controleren en maken** kunt u uw instellingen controleren en controleren of er validatie fouten zijn.

1. De instellingen van het toegangs pakket controleren

    ![Toegangs pakket-beleids instelling inschakelen](./media/entitlement-management-access-package-create/review-create.png)

1. Klik op **maken** om het toegangs pakket te maken.

    Het nieuwe toegangs pakket wordt weer gegeven in de lijst met toegangs pakketten.

## <a name="creating-an-access-package-programmatically"></a>Een toegangs pakket programmatisch maken

U kunt ook een toegangs pakket maken met behulp van Microsoft Graph.  Een gebruiker in een geschikte rol met een toepassing die de gedelegeerde machtiging heeft, `EntitlementManagement.ReadWrite.All` kan de API aanroepen naar

1. [Vermeld de accessPackageResources in de catalogus](/graph/api/accesspackagecatalog-list?tabs=http&view=graph-rest-beta) en [Maak een accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?tabs=http&view=graph-rest-beta) voor alle resources die nog niet in de catalogus staan.
1. [Vermeld de accessPackageResourceRoles](/graph/api/accesspackage-list-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) van elke accessPackageResource in een accessPackageCatalog. Deze lijst met rollen wordt vervolgens gebruikt voor het selecteren van een rol, wanneer u vervolgens een accessPackageResourceRoleScope maakt.
1. [Een AccessPackage maken](/graph/tutorial-access-package-api?view=graph-rest-beta).
1. [Een AccessPackageAssignmentPolicy maken](/graph/api/accesspackageassignmentpolicy-post?tabs=http&view=graph-rest-beta).
1. [Maak een accessPackageResourceRoleScope](/graph/api/accesspackage-post-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) voor elke resource functie die nodig is in het toegangs pakket.

## <a name="next-steps"></a>Volgende stappen

- [Koppeling delen om een toegangs pakket aan te vragen](entitlement-management-access-package-settings.md)
- [Resource rollen wijzigen voor een toegangs pakket](entitlement-management-access-package-resources.md)
- [Een gebruiker rechtstreeks toewijzen aan het toegangs pakket](entitlement-management-access-package-assignments.md)
- [Een toegangs beoordeling maken voor een toegangs pakket](entitlement-management-access-reviews-create.md)
