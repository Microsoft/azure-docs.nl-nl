---
title: Een dynamische groep maken of bewerken en de status ophalen-Azure AD | Microsoft Docs
description: Het maken of bijwerken van een regel voor groepslid maatschap in het Azure Portal en de verwerkings status controleren.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8525e9a76fa2439692cdb26b36c0bb761d63177c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550226"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Een dynamische groep in Azure Active Directory maken of bijwerken

In Azure Active Directory (Azure AD) kunt u regels gebruiken om het groepslid maatschap te bepalen op basis van eigenschappen van gebruikers of apparaten. In dit artikel leest u hoe u een regel instelt voor een dynamische groep in de Azure Portal.
Dynamische lidmaatschappen worden ondersteund voor beveiligings groepen of Microsoft 365 groepen. Wanneer een regel voor groepslid maatschap wordt toegepast, worden de kenmerken van gebruikers en apparaten geëvalueerd voor overeenkomsten met de lidmaatschaps regel. Wanneer een kenmerk wordt gewijzigd voor een gebruiker of apparaat, worden alle regels voor de dynamische groep in de organisatie verwerkt voor wijzigingen in het lidmaatschap. Gebruikers en apparaten worden toegevoegd of verwijderd als ze voldoen aan de voor waarden voor een groep. Beveiligings groepen kunnen worden gebruikt voor apparaten of gebruikers, maar Microsoft 365 groepen kunnen alleen gebruikers groepen zijn. Voor het gebruik van dynamische groepen is Azure AD Premium P1-licentie vereist. Zie [dynamische lidmaatschaps regels voor groepen](./groups-dynamic-membership.md) voor meer informatie. 

## <a name="rule-builder-in-the-azure-portal"></a>De opbouwfunctie voor regels in Azure Portal

Azure AD biedt een opbouwfunctie voor regels om uw belangrijke regels sneller te maken en bij te werken. De opbouw functie voor regels ondersteunt de bouw Maxi maal vijf expressies. Met de opbouwfunctie voor regels kunt u eenvoudiger een regel maken met enkele eenvoudige expressies, maar u kunt hiermee niet elke regel reproduceren. Als de opbouwfunctie voor regels geen ondersteuning biedt voor de regel die u wilt maken, kunt u het tekstvak gebruiken.

Hier volgen enkele voorbeelden van geavanceerde regels of syntaxis die u het beste kunt maken via het tekstvak:

- Regel met meer dan vijf expressies
- De regel voor direct ondergeschikten
- [Prioriteit van operator](groups-dynamic-membership.md#operator-precedence) instellen
- [Regels met complexe expressies](groups-dynamic-membership.md#rules-with-complex-expressions); bijvoorbeeld `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> De opbouwfunctie voor regels kan mogelijk geen regels weergeven die zijn gemaakt in het tekstvak. Mogelijk wordt een bericht weergegeven wanneer de opbouwfunctie voor regels de regel niet kan weergeven. Met de opbouwfunctie voor regels wijzigt u niet de ondersteunde syntaxis, validatie of verwerking van dynamische groepsregels.

![Scherm opname van de pagina ' Dynamic Membership Rules ' met de actie ' expressie toevoegen ' op het tabblad regels configureren geselecteerd.](./media/groups-create-rule/update-dynamic-group-rule.png)

Zie voor voor beelden van de syntaxis, ondersteunde eigenschappen, Opera tors en waarden voor een lidmaatschaps regel, [dynamische lidmaatschaps regels voor groepen in azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Een regel voor groepslid maatschap maken

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met een account dat zich in de rol globale beheerder, intune-beheerder of gebruikers beheerder in de Azure AD-organisatie bevindt.
1. Zoek en selecteer **groepen**.
1. Selecteer **alle groepen** en selecteer **nieuwe groep**.

   ![Selecteer de opdracht voor het toevoegen van een nieuwe groep](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. Voer op de pagina **groep** een naam en een beschrijving in voor de nieuwe groep. Selecteer een **lidmaatschaps type** voor gebruikers of apparaten, en selecteer vervolgens **dynamische query toevoegen**. De opbouw functie voor regels ondersteunt Maxi maal vijf expressies. Als u meer dan vijf expressies wilt toevoegen, moet u het tekstvak gebruiken.

   ![Scherm opname waarin de pagina alle groepen wordt weer gegeven, waarbij de actie nieuwe groep is geselecteerd.](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Voor een overzicht van de aangepaste extensie-eigenschappen die beschikbaar zijn voor uw lidmaatschaps query:
   1. **Eigenschappen van aangepaste uitbrei ding ophalen** selecteren
   1. Voer de toepassings-ID in en selecteer vervolgens **Eigenschappen vernieuwen**.
1. Nadat u de regel hebt gemaakt, selecteert u **Opslaan**.
1. Selecteer **maken** op de pagina **nieuwe groep** om de groep te maken.

Als de regel die u hebt ingevoerd niet geldig is, wordt uitgelegd waarom de regel niet kan worden verwerkt, wordt weer gegeven in een Azure-melding in de portal. Lees het zorgvuldig om te begrijpen hoe u de regel kunt herstellen.

## <a name="to-update-an-existing-rule"></a>Een bestaande regel bijwerken

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met een account dat zich in de rol globale beheerder, groeps beheerder, intune-beheerder of gebruikers beheerder in de Azure AD-organisatie bevindt.
1. Selecteer **groepen**  >  **alle groepen**.
1. Selecteer een groep om het profiel ervan te openen.
1. Selecteer op de profiel pagina voor de groep **dynamische lidmaatschaps regels**. De opbouw functie voor regels ondersteunt Maxi maal vijf expressies. Als u meer dan vijf expressies wilt toevoegen, moet u het tekstvak gebruiken.

   ![Lidmaatschapsregel voor een dynamische groep toevoegen](./media/groups-create-rule/update-dynamic-group-rule.png)

1. Voor een overzicht van de aangepaste extensie-eigenschappen die beschikbaar zijn voor uw lidmaatschaps regel:
   1. **Eigenschappen van aangepaste uitbrei ding ophalen** selecteren
   1. Voer de toepassings-ID in en selecteer vervolgens **Eigenschappen vernieuwen**.
1. Nadat u de regel hebt bijgewerkt, selecteert u **Opslaan**.

## <a name="turn-on-or-off-welcome-email"></a>Welkomst bericht in-of uitschakelen

Wanneer een nieuwe Microsoft 365 groep wordt gemaakt, wordt een welkomst-e-mail melding verzonden naar de gebruikers die aan de groep zijn toegevoegd. Later, als de kenmerken van een gebruiker of apparaat wijzigen, worden alle regels van de dynamische groep in de organisatie verwerkt voor wijzigingen in het lidmaatschap. Gebruikers die zijn toegevoegd, ontvangen ook de welkomst melding. U kunt dit gedrag uitschakelen in [Exchange Power shell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup).

## <a name="check-processing-status-for-a-rule"></a>De verwerkings status controleren voor een regel

U kunt de verwerkings status van lidmaatschap en de datum van laatste update **voor de groep** zien.
  
  ![weer gave van de status van de dynamische groep](./media/groups-create-rule/group-status.png)

De volgende status berichten kunnen worden weer gegeven voor de **verwerkings status van lidmaatschappen** :

- **Evalueren**: de groeps wijziging is ontvangen en de updates worden geëvalueerd.
- **Verwerken**: updates worden verwerkt.
- **Update voltooid**: verwerking is voltooid en alle toepasselijke updates zijn aangebracht.
- **Verwerkings fout**: verwerking kan niet worden voltooid vanwege een fout bij het evalueren van de lidmaatschaps regel.
- **Update onderbroken**: updates van dynamische lidmaatschaps regel zijn onderbroken door de beheerder. MembershipRuleProcessingState is ingesteld op ' paused '.

De volgende status berichten kunnen worden weer gegeven voor het lidmaatschap van de **laatste bijgewerkte** status:

- &lt;**Datum en tijd** &gt; : de laatste keer dat het lidmaatschap is bijgewerkt.
- Wordt **uitgevoerd**: updates worden momenteel uitgevoerd.
- **Onbekend**: de tijd van de laatste update kan niet worden opgehaald. De groep is mogelijk nieuw.

Als er een fout optreedt tijdens het verwerken van de lidmaatschaps regel voor een specifieke groep, wordt er een waarschuwing weer gegeven boven aan de **pagina overzicht** voor de groep. Als er voor meer dan 24 uur geen updates voor dynamische lidmaatschappen in behandeling kunnen worden verwerkt voor alle groepen binnen de organisatie, wordt een waarschuwing weer gegeven aan de bovenkant van **alle groepen**.

![fout bericht waarschuwingen verwerken](./media/groups-create-rule/processing-error.png)

Deze artikelen bevatten aanvullende informatie over Azure Active Directory-groepen.

- [Bestaande groepen weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Een nieuwe groep maken en leden toevoegen](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Instellingen van een groep beheren](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Lidmaatschappen van een groep beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Dynamische regels voor gebruikers in een groep beheren](groups-dynamic-membership.md)