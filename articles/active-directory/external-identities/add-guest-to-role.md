---
title: Een gebruiker voor B2B-samenwerking toevoegen aan een rol - Azure Active Directory
description: Een gastgebruiker toevoegen aan een rol in Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32a931fe43b6be406f0b2a4b8193c1631261f7e5
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575663"
---
# <a name="grant-permissions-to-users-from-partner-organizations-in-your-azure-active-directory-tenant"></a>Machtigingen verlenen aan gebruikers van partnerorganisaties in uw Azure Active Directory tenant

Azure Active Directory B2B-samenwerkingsgebruikers (Azure AD) worden toegevoegd als gastgebruikers aan de directory en gastmachtigingen in de directory zijn standaard beperkt. Uw bedrijf heeft mogelijk een aantal gastgebruikers nodig om rollen met hogere bevoegdheden in uw organisatie in te vullen. Ter ondersteuning van het definiëren van rollen met hogere bevoegdheden kunnen gastgebruikers worden toegevoegd aan elke behoeften van uw organisatie, op basis van de behoeften van uw organisatie.

Als een directoryrol wordt toegewezen aan een gastgebruiker, krijgt de gastgebruiker extra machtigingen die bij de rol worden gebruikt, waaronder basismachtigingen voor lezen. Zie [Ingebouwde Azure AD-rollen.](https://docs.microsoft.com/azure/active-directory/roles/permissions-reference)

## <a name="default-role"></a>Standaardrol

![Schermopname van de standaarddirectory-rol](./media/add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>Globale beheerdersrol

![Schermopname met de rol van globale beheerder](./media/add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>Beperkte beheerdersrol

![Schermopname met de beperkte beheerdersrol](./media/add-guest-to-role/limited-admin-role.png)

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Eigenschappen van gebruikers van B2B-samenwerking](user-properties.md)
