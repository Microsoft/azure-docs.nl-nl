---
title: Groeps eigenaren toevoegen of verwijderen-Azure Active Directory | Microsoft Docs
description: Instructies voor het toevoegen of verwijderen van groeps eigenaren met behulp van Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 358a551908a7263f3258f47dfe1cceeffe2307b1
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860487"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Groeps eigenaren toevoegen aan of verwijderen uit Azure Active Directory
Azure Active Directory-groepen (Azure AD) zijn eigendom van en worden beheerd door groeps eigenaren. Groeps eigenaren kunnen gebruikers of service-principals zijn en kunnen de groep met inbegrip van lidmaatschap beheren. Alleen bestaande groeps eigenaren of groep-Beheerders kunnen groeps eigenaren toewijzen. Groeps eigenaren hoeven geen lid te zijn van de groep.

Wanneer een groep geen eigenaar heeft, kunnen beheerders van groeps beheer de groep blijven beheren. U kunt het beste elke groep ten minste één eigenaar hebben. Zodra de eigen aren zijn toegewezen aan een groep, kan de laatste eigenaar van de groep niet worden verwijderd. Zorg ervoor dat u een andere eigenaar selecteert voordat u de laatste eigenaar uit de groep verwijdert.

## <a name="add-an-owner-to-a-group"></a>Een eigenaar aan een groep toevoegen
Hieronder vindt u instructies voor het toevoegen van een gebruiker als een eigenaar aan een groep met behulp van de Azure AD-Portal. Als u een Service-Principal als eigenaar van een groep wilt toevoegen, volgt u de instructies om dit te doen met behulp van [Power shell](/powershell/module/Azuread/Add-AzureADGroupOwner).

### <a name="to-add-a-group-owner"></a>Een groeps eigenaar toevoegen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**, selecteer **groepen** en selecteer vervolgens de groep waarvoor u een eigenaar wilt toevoegen (voor dit voor beeld *MDM-beleid-West*).

3. Selecteer op de pagina **MDM-beleid-West-overzicht** de optie **eigen aren**.

    ![MDM-beleid-overzichts pagina met de eigen aars-optie gemarkeerd](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Selecteer op de pagina **MDM-beleid-West-eigen** aars de optie **eigen aren toevoegen**, zoek naar en selecteer de gebruiker die de nieuwe groeps eigenaar wordt, en kies vervolgens **selecteren**.

    ![MDM-beleid-West-eigen aars pagina met de optie eigen aars toevoegen gemarkeerd](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Nadat u de nieuwe eigenaar hebt geselecteerd, kunt u de pagina **eigen aars** vernieuwen en de naam zien die is toegevoegd aan de lijst met eigen aren.

## <a name="remove-an-owner-from-a-group"></a>Een eigenaar uit een groep verwijderen
Een eigenaar verwijderen uit een groep met behulp van Azure AD.

### <a name="to-remove-an-owner"></a>Een eigenaar verwijderen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met het account van een globale administrator voor de map.

2. Selecteer **Azure Active Directory**, selecteer **groepen** en selecteer vervolgens de groep waarvoor u een eigenaar wilt verwijderen (voor dit voor beeld *MDM-beleid-West*).

3. Selecteer op de pagina **MDM-beleid-West-overzicht** de optie **eigen aren**.

    ![MDM-beleid-overzichts pagina met de optie voor het verwijderen van eigen aars gemarkeerd](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Selecteer op de pagina **MDM-beleid-West-eigen aars** de gebruiker die u wilt verwijderen als groeps eigenaar, kies **verwijderen** op de pagina gegevens van de gebruiker en selecteer **Ja** om uw beslissing te bevestigen.

    ![De pagina informatie van de gebruiker met de optie verwijderen gemarkeerd](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Nadat u de eigenaar hebt verwijderd, kunt u teruggaan naar de pagina **eigen aars** en de naam weer geven is verwijderd uit de lijst met eigen aren.

## <a name="next-steps"></a>Volgende stappen
- [Toegang tot resources beheren met Azure Active Directory groepen](active-directory-manage-groups.md)

- [Azure Active Directory cmdlets voor het configureren van groepsinstellingen](../enterprise-users/groups-settings-cmdlets.md)

- [Groepen gebruiken om toegang toe te wijzen aan een geïntegreerde SaaS-app](../enterprise-users/groups-saasapps.md)

- [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](../hybrid/whatis-hybrid-identity.md)

- [Azure Active Directory cmdlets voor het configureren van groepsinstellingen](../enterprise-users/groups-settings-v2-cmdlets.md)
