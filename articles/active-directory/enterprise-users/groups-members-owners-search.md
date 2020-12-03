---
title: Groeps leden en eigen aren zoeken en filteren (preview)-Azure Active Directory | Microsoft Docs
description: Zoek en filter groeps leden en eigen aren in de Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d78a2a4e0f14d99a7a1ecada915857f59422bb58
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547369"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Zoeken in groepen en leden (preview) in Azure Active Directory

In dit artikel leest u hoe u kunt zoeken naar leden en eigen aren van een groep en hoe u zoek filters kunt gebruiken als onderdeel van de preview-versie voor het verbeteren van groepen in de Azure Active Directory-Portal (Azure AD). Er zijn veel verbeteringen in de groeps ervaringen waarmee u uw groepen, met inbegrip van leden en eigen aren, snel en eenvoudig kunt beheren. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Wijzigingen in deze preview zijn onder andere:

- Nieuwe groepen zoek mogelijkheden, zoals subtekenreeksen zoeken in groeps namen
- Nieuwe filter-en sorteer opties voor de lijsten leden en eigen aren
- Nieuwe zoek mogelijkheden voor lijsten van leden en eigen aren
- Nauw keurigere groeps aantallen voor grote groepen

## <a name="enabling-and-managing-the-preview"></a>De preview inschakelen en beheren

U kunt de preview-versie eenvoudig samen voegen:

  1. Meld u aan bij de [Azure AD-Portal](https://portal.azure.com)en selecteer **groepen**.
  2. Selecteer op de pagina groepen – alle groepen de banner boven aan de pagina om aan de preview-versie toe te voegen.

U kunt ook de nieuwste functies en verbeteringen bekijken door **Preview-info** te selecteren op de pagina **alle groepen** . Nadat u de preview hebt toegevoegd, ziet u de preview-code op alle pagina's met groepen die verbeteringen hebben en deel uitmaken van de preview-versie. Niet elke groepen pagina is bijgewerkt als onderdeel van deze preview.

Als u problemen ondervindt, kunt u teruggaan naar de oude ervaring door de banner boven aan de pagina **alle groepen** te selecteren. We stellen uw feedback op prijs zodat we onze ervaring kunnen verbeteren.

## <a name="group-search-and-sorting"></a>Groep zoeken en sorteren

De zoek opdracht in de groeps lijst is uitgebreid, zodat u met de zoek functie automatisch een `startswith` en een subtekenreeks kunt zoeken in de lijst met groeps namen. De subtekenreeks zoeken wordt alleen uitgevoerd op hele woorden en bevat geen speciale tekens. Zoeken in subtekenreeksen is hoofdletter gevoelig.

![nieuwe subtekenreeks zoekt op de pagina alle groepen](./media/groups-members-owners-search/groups-search-preview.png)

Bijvoorbeeld, een zoek opdracht voor ' beleid ' retourneert nu zowel ' MDM-beleid – westelijke ' als ' beleids groep '. Een groep met de naam ' New_policy ' zou niet worden geretourneerd.

- U kunt ook dezelfde zoek actie uitvoeren op groeps lidmaatschaps lijsten.
- U kunt de lijst met groepen nu sorteren op naam met behulp van de pijlen rechts van de kolomkop naam om de lijst in oplopende of aflopende volg orde te sorteren.

## <a name="group-member-search-and-filtering"></a>Groeps leden zoeken en filteren

### <a name="search-group-member-and-owner-lists"></a>Lijst met groeps leden en eigen aren zoeken

U kunt nu de leden van een specifieke groep op naam zoeken en dezelfde zoek opdracht uitvoeren op de lijst met de eigen aren van de groep. Als u in de nieuwe ervaring een teken reeks in het zoekvak invoert, wordt er automatisch een startsWith-zoek opdracht uitgevoerd. Als u bijvoorbeeld ' Scott ' zoekt, wordt Scott Wilkinson geretourneerd.

![nieuwe subtekenreeks zoekt op de lijst groeps leden en eigen aren](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Leden en eigen aars lijst filteren

U kunt nu niet alleen zoeken en de lijst met leden en eigen aren filteren op gebruikers type. Dit is de informatie in de kolom gebruikers type van de lijst. U kunt de lijst ook filteren op leden en gasten om te bepalen of er gasten in de groep zijn.

### <a name="view-and-manage-membership"></a>Lidmaatschap weer geven en beheren

Naast het weer geven van de directe leden van een specifieke groep, kunt u nu de lijst met alle leden van de groep weer geven op de pagina leden. De lijst met leden bevat alle unieke leden van de groep, inclusief eventuele transitieve leden.

U kunt ook de lijst direct leden en alle leden lijst afzonderlijk zoeken en filteren. Filteren van de lijst alle leden heeft geen invloed op de filters die worden toegepast op de lijst direct leden.

## <a name="improved-group-member-counts"></a>Verbeterd aantal groepslid gebruikers

We hebben de groeps **overzichts** pagina verbeterd om aantallen groepslid gebruikers te bieden voor groepen van elke grootte. U kunt het aantal leden bekijken, zelfs voor groepen met meer dan 1.000 leden. U kunt nu het totale aantal directe leden voor een groep bekijken en het totale aantal lidmaatschappen (alle unieke leden van de groep inclusief overgankelijke leden) op de pagina **overzicht** .

![Hogere nauw keurigheid bij aantallen groepslid maatschappen](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over het werken met groepen in azure AD.

- [Groepen en leden weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Groepslidmaatschap beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Dynamische regels voor gebruikers in een groep beheren](groups-create-rule.md)
- [Instellingen van uw groep bewerken](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Toegang tot resources met behulp van groepen beheren](../fundamentals/active-directory-manage-groups.md)
- [Toegang tot SaaS-apps met behulp van groepen beheren](groups-saasapps.md)
- [Groepen met behulp van PowerShell-opdrachten beheren](../enterprise-users/groups-settings-v2-cmdlets.md)
- [Een Azure-abonnement toevoegen aan Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
