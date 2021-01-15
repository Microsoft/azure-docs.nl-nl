---
title: Externe toegang beheren met voorwaardelijke toegang Azure Active Directory
description: Het gebruik van Azure Active Directory beleid voor voorwaardelijke toegang om externe toegang tot bronnen te beveiligen.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64209a4d9ca200c69783a4ae317b38beef8ded89
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222306"
---
# <a name="manage-external-access-with-conditional-access-policies"></a>Externe toegang beheren met beleid voor voorwaardelijke toegang 

[Voorwaardelijke toegang](../conditional-access/overview.md) is het hulp programma dat door Azure AD wordt gebruikt om signalen samen te brengen, beleid af te dwingen en te bepalen of een gebruiker toegang moet krijgen tot bronnen. Zie voor gedetailleerde informatie over het maken en gebruiken van beleids regels voor voorwaardelijke toegang (beleid voor voorwaardelijke toegang) [een implementatie van voorwaardelijke toegang plannen](../conditional-access/plan-conditional-access.md). 

![Diagram van voorwaardelijke toegangs signalen en beslissingen](media/secure-external-access//7-conditional-access-signals.png)



In dit artikel wordt beschreven hoe u beleid voor voorwaardelijke toegang toepast op externe gebruikers en wordt ervan uitgegaan dat u geen toegang hebt tot de functionaliteit van [rechten beheer](../governance/entitlement-management-overview.md) . Beleid voor voorwaardelijke toegang kan worden gebruikt en wordt samen met het rechten beheer.

Eerder in deze documentenset hebt u [een beveiligings plan gemaakt](3-secure-access-plan.md) dat wordt beschreven:

* Toepassingen en bronnen hebben dezelfde beveiligings vereisten en kunnen worden gegroepeerd voor toegang.

* Aanmeldings vereisten voor externe gebruikers.

U gebruikt dat plan voor het maken van uw beleid voor voorwaardelijke toegang voor externe toegang. 

> [!IMPORTANT]
> Maak een paar externe gebruikers test accounts zodat u het beleid dat u maakt, kunt testen voordat u ze toepast op alle externe gebruikers.

## <a name="conditional-access-policies-for-external-access"></a>Beleid voor voorwaardelijke toegang voor externe toegang

Hieronder vindt u de aanbevolen procedures voor het beheren van externe toegang met beleid voor voorwaardelijke toegang.

* Als u verbonden organisaties niet in rechten beheer kunt gebruiken, maakt u een Azure AD-beveiligings groep of een Microsoft 365 groep voor elke partner organisatie waarmee u samenwerkt. Wijs alle gebruikers van die partner toe aan de groep. U kunt deze groepen vervolgens gebruiken in beleids regels voor voorwaardelijke toegang.

* Maak zo weinig mogelijk beleids regels voor voorwaardelijke toegang. Voor toepassingen die dezelfde toegangs vereisten hebben, voegt u deze allemaal toe aan hetzelfde beleid.  
‎ 
   > [!NOTE]
   > Beleid voor voorwaardelijke toegang kan worden toegepast op Maxi maal 250 toepassingen. Als meer dan 250 apps dezelfde toegangs vereisten hebben, moet u dubbele beleids regels maken. Beleid A wordt toegepast op apps 1-250, beleid B is van toepassing op apps 251-500, enzovoort.

* Duidelijk naam beleid dat specifiek is voor externe toegang met een naamgevings Conventie. Er is één naam Conventie *ExternalAccess_actiontaken_AppGroup*. Bijvoorbeeld ExternalAccess_Block_FinanceApps.

## <a name="block-all-external-users-from-resources"></a>Alle externe gebruikers van resources blok keren

U kunt voor komen dat externe gebruikers toegang krijgen tot specifieke sets resources met beleid voor voorwaardelijke toegang. Wanneer u de set resources hebt bepaald waarvoor u de toegang wilt blok keren, maakt u een beleid.

Een beleid maken dat de toegang voor externe gebruikers blokkeert voor een set toepassingen:

1. Open de **Azure Portal**, selecteer **Azure Active Directory**, selecteer **beveiliging** en selecteer vervolgens **voorwaardelijke toegang**.

2. Selecteer **Nieuw beleid** en voer een **naam** in, bijvoorbeeld ExternalAccess_Block_FinanceApps

3. Selecteer **gebruikers en groep** s. Klik op het tabblad include op **gebruikers en groepen selecteren** en selecteer vervolgens **alle gasten en externe gebruikers**. 

4. Selecteer **uitsluiten** en voer uw beheerders groep (en) en alle accounts voor nood toegang (afbreek glazen) in.

5. Selecteer **Cloud-apps of-acties**, kies **apps selecteren**, selecteer alle apps waarvoor u externe toegang wilt blok keren en kies vervolgens **selecteren**.

6. Selecteer **voor waarden**, selecteer **locaties** onder Selecteer **Ja** en selecteer **een wille keurige locatie**.

7. Selecteer onder besturings elementen voor toegang **verlenen**, wijzig de wissel knop in **blok keren** en kies **selecteren**.

8. Zorg ervoor dat de instelling beleid inschakelen is ingesteld op **alleen rapport** en selecteer vervolgens **maken**.

## <a name="block-external-access-to-all-except-specific-external-users"></a>Externe toegang tot alle behalve specifieke externe gebruikers blok keren

Het kan voor komen dat u externe gebruikers wilt blok keren, behalve een specifieke groep. Het is bijvoorbeeld mogelijk dat u alle externe gebruikers wilt blok keren, behalve die die voor het financiële team werken vanuit de financiële toepassingen. Om dit te doen:

1. Maak een beveiligings groep voor de externe gebruikers die toegang moeten hebben tot de groep Financiën.

2. Volg de stappen 1-3 in externe toegang buiten de bovenstaande bronnen blok keren.

3. Voeg in stap 4 de beveiligings groep die u wilt uitsluiten van de financiën-apps toe.

4. Voer de overige stappen uit.

## <a name="implement-conditional-access"></a>Voorwaardelijke toegang implementeren

Er zijn veel gang bare beleids regels voor voorwaardelijke toegang gedocumenteerd. Bekijk het volgende dat u kunt aanpassen voor externe gebruikers.

* [Multi-Factor Authentication vereisen voor alle gebruikers](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

* [Voorwaardelijke gebruikerstoegang op basis van risico's](../conditional-access/howto-conditional-access-policy-risk-user.md)

* [Multi-Factor Authentication vereisen voor toegang vanuit niet-vertrouwde netwerken](../conditional-access/untrusted-networks.md) 

* [Gebruiks voorwaarden vereisen](../conditional-access/terms-of-use.md)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over het beveiligen van externe toegang tot bronnen. U wordt aangeraden de acties in de vermelde volg orde uit te voeren.

1. [Bepaal de gewenste beveiligings postuur voor externe toegang](1-secure-access-posture.md)

2. [Uw huidige status ontdekken](2-secure-access-current-state.md)

3. [Een governance-plan maken](3-secure-access-plan.md)

4. [Groepen gebruiken voor beveiliging](4-secure-access-groups.md)

5. [Overgang naar Azure AD B2B](5-secure-access-b2b.md)

6. [Veilige toegang met het rechten beheer](6-secure-access-entitlement-managment.md)

7. [Veilige toegang met beleid voor voorwaardelijke toegang](7-secure-access-conditional-access.md) (u bent hier)

8. [Veilige toegang met gevoeligheids labels](8-secure-access-sensitivity-labels.md)

9. [Beveiligde toegang tot micro soft teams, OneDrive en share point](9-secure-access-teams-sharepoint.md)
