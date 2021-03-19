---
title: Dynamische groepen en B2B-samen werking-Azure Active Directory | Microsoft Docs
description: Laat zien hoe u dynamische Azure AD-groepen kunt gebruiken met Azure Active Directory B2B-samen werking
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b820b8b9606795709d03414fa14ec29a1b5c519
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92441552"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dynamische groepen en Azure Active Directory B2B-samen werking

## <a name="what-are-dynamic-groups"></a>Wat zijn dynamische groepen?
Dynamische configuratie van het lidmaatschap van de beveiligings groep voor Azure Active Directory (Azure AD) is beschikbaar in [de Azure Portal](https://portal.azure.com). Beheerders kunnen regels instellen voor het invullen van groepen die zijn gemaakt in azure AD op basis van gebruikers kenmerken (zoals User type, afdeling of land/regio). Leden kunnen automatisch worden toegevoegd aan of verwijderd uit een beveiligings groep op basis van hun kenmerken. Deze groepen kunnen toegang bieden tot toepassingen of cloud resources (share point-sites, documenten) en licenties toewijzen aan leden. Lees meer over dynamische groepen in [toegewezen groepen in azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

De juiste [Azure AD Premium P1 of P2-licentie](https://azure.microsoft.com/pricing/details/active-directory/) is vereist voor het maken en gebruiken van dynamische groepen. Meer informatie vindt u in het artikel op [kenmerken gebaseerde regels maken voor dynamisch groepslid maatschap in azure Active Directory](../enterprise-users/groups-dynamic-membership.md).

## <a name="creating-an-all-users-dynamic-group"></a>Een dynamische groep ' alle gebruikers ' maken
U kunt een groep met alle gebruikers binnen een Tenant maken met behulp van een lidmaatschaps regel. Wanneer gebruikers in de toekomst worden toegevoegd of verwijderd uit de Tenant, wordt het lidmaatschap van de groep automatisch aangepast.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een account dat is toegewezen aan de rol van globale beheerder of gebruiker beheerder in de Tenant.
1. Selecteer **Azure Active Directory**.
2. Selecteer onder **beheren** de optie **groepen** en selecteer vervolgens **nieuwe groep**.
1. Selecteer op de pagina **nieuwe groep** onder **groeps type** de optie **beveiliging**. Voer een **naam** en **beschrijving** in voor de nieuwe groep. 
2. Selecteer onder **lidmaatschaps type** de optie **dynamische gebruiker** en selecteer vervolgens **dynamische query toevoegen**. 
4. Selecteer boven het tekstvak **syntaxis van regel** de optie **bewerken**. Typ op de pagina **syntaxis van regel bewerken** de volgende expressie in het tekstvak:

   ```
   user.objectId -ne null
   ```
1. Selecteer **OK**. De regel wordt weer gegeven in het vak syntaxis van regel:

   ![Syntaxis van de regel voor alle gebruikers dynamische groep](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  Selecteer **Opslaan**. De nieuwe dynamische groep bevat nu B2B-gast gebruikers en gebruikers van leden.


1. Selecteer **maken** op de pagina **nieuwe groep** om de groep te maken.

## <a name="creating-a-group-of-members-only"></a>Alleen een groep leden maken

Als u wilt dat de groep gast gebruikers uitsluit en alleen leden van uw Tenant bevat, maakt u een dynamische groep zoals hierboven wordt beschreven, maar voert u in het vak **regel syntaxis** de volgende expressie in:

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

In de volgende afbeelding ziet u de syntaxis van de regel voor een dynamische groep die is gewijzigd om alleen leden op te nemen en gasten uit te sluiten.

![Geeft de regel weer waarbij het gebruikers type is ingesteld op lid](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>Alleen een groep gasten maken

Het kan ook handig zijn om een nieuwe dynamische groep te maken die alleen gast gebruikers bevat, zodat u beleid (zoals beleid voor voorwaardelijke toegang voor Azure AD) kunt Toep assen. Maak een dynamische groep zoals hierboven wordt beschreven, maar Voer in het vak **regel syntaxis** de volgende expressie in:

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

In de volgende afbeelding ziet u de syntaxis van de regel voor een dynamische groep die is gewijzigd om gasten alleen op te nemen en gebruikers van leden uit te sluiten.

![Geeft de regel aan waar het gebruikers type gelijk is aan gast](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>Volgende stappen

- [Eigenschappen van gebruikers van B2B-samenwerking](user-properties.md)
- [Een B2B-samenwerkings gebruiker toevoegen aan een rol](add-guest-to-role.md)
- [Voorwaardelijke toegang voor B2B-samenwerkings gebruikers](conditional-access.md)