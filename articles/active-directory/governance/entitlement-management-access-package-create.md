---
title: Een nieuw toegangspakket maken in rechtenbeheer - Azure AD
description: Meer informatie over het maken van een nieuw toegangspakket met resources die u wilt delen in Azure Active Directory rechtenbeheer.
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
ms.openlocfilehash: cb0312d905284f8c5a9817e9550d340bf6135032
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532204"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Een nieuw toegangspakket maken in Azure AD-rechtenbeheer

Met een toegangspakket kunt u een een time-setup van resources en beleidsregels maken die automatisch toegang beheren voor de levensduur van het toegangspakket. In dit artikel wordt beschreven hoe u een nieuw toegangspakket maakt.

## <a name="overview"></a>Overzicht

Alle toegangspakketten moeten worden geplaatst in een container die een catalogus wordt genoemd. Een catalogus definieert welke resources u aan uw toegangspakket kunt toevoegen. Als u geen catalogus opgeeft, wordt uw toegangspakket in de catalogus Algemeen opgenomen. Op dit moment kunt u een bestaand toegangspakket niet verplaatsen naar een andere catalogus.

Als u een toegangspakketbeheerder bent, kunt u geen resources toevoegen aan een catalogus. U bent beperkt tot het gebruik van de resources die beschikbaar zijn in de catalogus. Als u resources aan een catalogus wilt toevoegen, kunt u dit aan de eigenaar van de catalogus vragen.

Alle toegangspakketten moeten ten minste één beleid hebben. Beleidsregels geven aan wie het toegangspakket kan aanvragen, en ook de instellingen voor goedkeuring en levenscyclus. Wanneer u een nieuw toegangspakket maakt, kunt u een eerste beleid maken voor gebruikers in uw directory, voor gebruikers die zich niet in uw directory, alleen voor directe toewijzingen van beheerders of u kunt ervoor kiezen om het beleid later te maken.

![Een toegangspakket maken](./media/entitlement-management-access-package-create/access-package-create.png)

Hier volgen de stappen op hoog niveau voor het maken van een nieuw toegangspakket.

1. Start in Identity Governance het proces voor het maken van een nieuw toegangspakket.

1. Selecteer de catalogus waarin u het toegangspakket wilt maken.

1. Voeg resources uit de catalogus toe aan uw toegangspakket.

1. Wijs resourcerollen toe voor elke resource.

1. Geef gebruikers op die toegang kunnen aanvragen.

1. Geef goedkeuringsinstellingen op.

1. Geef de levenscyclusinstellingen op.

## <a name="start-new-access-package"></a>Nieuw toegangspakket starten

**Vereiste rol:** globale beheerder, gebruikersbeheerder, cataloguseigenaar of toegangspakketbeheerder

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Klik op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het linkermenu op **Toegangspakketten**.

1. Klik op **Nieuw toegangspakket**.
   
    ![Rechtenbeheer in de Azure-portal](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Basisbeginselen

Op het **tabblad Basisinformatie** geeft u het toegangspakket een naam en geeft u op in welke catalogus het toegangspakket moet worden gemaakt.

1. Voer een weergavenaam en beschrijving in voor het toegangspakket. Gebruikers zien deze informatie wanneer ze een aanvraag voor het toegangspakket indienen.

1. Selecteer in **de** vervolgkeuzelijst Catalogus de catalogus waarin u het toegangspakket wilt maken. U kunt bijvoorbeeld een cataloguseigenaar hebben die alle marketingbronnen beheert die kunnen worden aangevraagd. In dit geval kunt u de marketingcatalogus selecteren.

    U ziet alleen catalogi waarin u toegangspakketten mag maken. Als u een toegangspakket in een bestaande catalogus wilt maken, moet u een Globale beheerder- of gebruikersbeheerder zijn, of moet u een cataloguseigenaar of toegangspakketbeheerder in die catalogus zijn.

    ![Toegangspakket - Basisbeginselen](./media/entitlement-management-access-package-create/basics.png)

    Als u een Globale beheerder, een gebruikersbeheerder of catalogusmaker bent en u uw toegangspakket wilt maken in een nieuwe catalogus die niet wordt vermeld, klikt u op **Nieuwe catalogus maken.** Voer de naam en beschrijving van de catalogus in en klik vervolgens op **Maken.**

    Het toegangspakket dat u maakt en alle resources die in het pakket zijn opgenomen, worden toegevoegd aan de nieuwe catalogus. U kunt later ook extra cataloguseigenaren toevoegen.

1. Klik op **Volgende**.

## <a name="resource-roles"></a>Resourcerollen

Op het **tabblad Resourcerollen** selecteert u de resources die u wilt opnemen in het toegangspakket. Gebruikers die het toegangspakket aanvragen en ontvangen, ontvangen alle resourcerollen in het toegangspakket.

1. Klik op het resourcetype dat u wilt toevoegen (**Groepen en Teams,** **Toepassingen** of **SharePoint-sites).**

1. Selecteer in het deelvenster Selecteren dat wordt weergegeven een of meer resources in de lijst.

    ![Toegangspakket - Resourcerollen](./media/entitlement-management-access-package-create/resource-roles.png)

    Als u het toegangspakket in de catalogus Algemeen of een nieuwe catalogus maakt, kunt u een resource kiezen uit de map die u bezit. U moet ten minste een Globale beheerder, een gebruikersbeheerder of catalogusmaker zijn.

    Als u het toegangspakket in een bestaande catalogus maakt, kunt u een resource selecteren die al in de catalogus staat zonder dat u er eigenaar van bent.

    Als u een Globale beheerder, een gebruikersbeheerder of cataloguseigenaar bent, hebt u de extra optie om resources te selecteren die nog niet in de catalogus staan. Als u resources selecteert die momenteel niet in de geselecteerde catalogus staan, worden deze resources ook toegevoegd aan de catalogus voor andere catalogusbeheerders om toegangspakketten mee te bouwen. Schakel het selectievakje Alles weergeven boven aan het  deelvenster Selecteren in om alle resources te zien die aan de catalogus kunnen worden toegevoegd. Als u alleen resources wilt selecteren die zich momenteel in de geselecteerde catalogus hebben, laat u het selectievakje **Alles** weergeven uitgeschakeld (standaardtoestand).

1. Nadat u de resources hebt geselecteerd, selecteert **u** in de lijst Rol de rol die aan gebruikers moet worden toegewezen voor de resource.

    ![Toegangspakket - Selectie van resourcerol](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Klik op **Volgende**.

>[!NOTE]
>U kunt dynamische groepen toevoegen aan een catalogus en aan een toegangspakket. U kunt echter alleen de rol Eigenaar selecteren bij het beheren van een dynamische groepsresource in een toegangspakket.

## <a name="requests"></a>Aanvragen

Op het **tabblad Aanvragen** maakt u het eerste beleid om op te geven wie het toegangspakket en ook goedkeuringsinstellingen kan aanvragen. Later kunt u meer aanvraagbeleid maken zodat extra groepen gebruikers het toegangspakket met hun eigen goedkeuringsinstellingen kunnen aanvragen.

![Toegangspakket - tabblad Aanvragen](./media/entitlement-management-access-package-create/requests.png)

Voer de stappen uit in een van de volgende secties, afhankelijk van wie u dit toegangspakket wilt kunnen aanvragen.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Controleren en maken

Op het **tabblad Beoordelen en** maken kunt u uw instellingen controleren en controleren op validatiefouten.

1. De instellingen van het toegangspakket controleren

    ![Toegangspakket - Beleidsinstelling inschakelen](./media/entitlement-management-access-package-create/review-create.png)

1. Klik **op Maken** om het toegangspakket te maken.

    Het nieuwe toegangspakket wordt weergegeven in de lijst met toegangspakketten.

## <a name="creating-an-access-package-programmatically"></a>Programmatisch een toegangspakket maken

U kunt ook een toegangspakket maken met Microsoft Graph.  Een gebruiker met een geschikte rol met een toepassing met de gedelegeerde machtiging `EntitlementManagement.ReadWrite.All` kan de API aanroepen naar

1. [Vermeld de accessPackageResources in](/graph/api/accesspackagecatalog-list?tabs=http&view=graph-rest-beta&preserve-view=true) de catalogus en maak een [accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?tabs=http&view=graph-rest-beta&preserve-view=true) voor resources die nog niet in de catalogus staan.
1. [Vermeld de accessPackageResourceRoles](/graph/api/accesspackage-list-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta&preserve-view=true) van elke accessPackageResource in een accessPackageCatalog. Deze lijst met rollen wordt vervolgens gebruikt om een rol te selecteren wanneer u vervolgens een accessPackageResourceRoleScope maakt.
1. [Maak een accessPackage](/graph/tutorial-access-package-api&view=graph-rest-beta&preserve-view=true).
1. [Maak een accessPackageAssignmentPolicy.](/graph/api/accesspackageassignmentpolicy-post?tabs=http&view=graph-rest-beta&preserve-view=true)
1. [Maak een accessPackageResourceRoleScope voor](/graph/api/accesspackage-post-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta&preserve-view=true) elke resourcerol die nodig is in het toegangspakket.

## <a name="next-steps"></a>Volgende stappen

- [Koppeling delen om een toegangspakket aan te vragen](entitlement-management-access-package-settings.md)
- [Resourcerollen voor een toegangspakket wijzigen](entitlement-management-access-package-resources.md)
- [Een gebruiker rechtstreeks toewijzen aan het toegangspakket](entitlement-management-access-package-assignments.md)
- [Een toegangsbeoordeling voor een toegangspakket maken](entitlement-management-access-reviews-create.md)
