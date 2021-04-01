---
title: Enkele en multi tenant-apps in azure AD
titleSuffix: Microsoft identity platform
description: Meer informatie over de functies en verschillen tussen apps met één Tenant en meerdere tenants in azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/24/2020
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 0868d87d977b15a552b04d5dbd6d19de6931f0ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91395903"
---
# <a name="tenancy-in-azure-active-directory"></a>Tenancy in Azure Active Directory

Azure Active Directory (Azure AD) organiseert objecten als gebruikers en apps in groepen genaamd *tenants*. Met tenants kan een beheerder beleid instellen voor de gebruikers binnen de organisatie en de apps die de organisatie bezit om te voldoen aan hun beveiligings-en operationeel beleid. 

## <a name="who-can-sign-in-to-your-app"></a>Wie kan zich aanmelden bij uw app?

Wanneer het gaat om apps te ontwikkelen, kunnen ontwikkel aars ervoor kiezen om de app te configureren voor een enkele Tenant of een multi tenant tijdens de registratie van de app in de [Azure Portal](https://portal.azure.com).
* Apps met één Tenant zijn alleen beschikbaar in de Tenant waarin ze zijn geregistreerd, ook wel bekend als hun thuis Tenant.
* Multi tenant-apps zijn beschikbaar voor gebruikers in hun thuis Tenant en andere tenants.

In de Azure Portal kunt u uw app configureren als één Tenant of multi tenant door de doel groep als volgt in te stellen.

| Doelgroep | Eén/meerdere tenants | Wie kan zich aanmelden | 
|----------|--------| ---------|
| Alleen accounts in deze map | Eén tenant | Alle gebruikers-en Gast accounts in uw Directory kunnen uw toepassing of API gebruiken.<br>*Gebruik deze optie als uw doel groep intern is voor uw organisatie.* |
| Accounts in een Azure AD-Directory | Multitenant | Alle gebruikers en gasten met een werk-of school account van micro soft kunnen uw toepassing of API gebruiken. Dit omvat scholen en bedrijven die gebruikmaken van Microsoft 365.<br>*Gebruik deze optie als uw doel groep zakelijke of educatieve klanten zijn.* |
| Accounts in een Azure AD-adres lijst en persoonlijke micro soft-accounts (zoals Skype, Xbox, Outlook.com) | Multitenant | Alle gebruikers met een werk-of school account of een persoonlijk Microsoft-account kunnen uw toepassing of API gebruiken. Het omvat scholen en bedrijven die gebruikmaken van Microsoft 365, evenals persoonlijke accounts die worden gebruikt om zich aan te melden bij services zoals Xbox en Skype.<br>*Gebruik deze optie om de breedste set micro soft-accounts te bereiken.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Aanbevolen procedures voor multi tenant-apps

Het bouwen van fantastische multi tenant-apps kan lastig zijn vanwege het aantal verschillende beleids regels dat IT-beheerders kunnen instellen in hun tenants. Als u ervoor kiest een multi tenant-app te bouwen, volgt u deze aanbevolen procedures:

* Test uw app in een Tenant met geconfigureerde [beleids regels voor voorwaardelijke toegang](../azuread-dev/conditional-access-dev-guide.md).
* Volg het principe van minimale gebruikers toegang om ervoor te zorgen dat uw app alleen machtigingen aanvraagt die daad werkelijk nodig is. 
* Geef de juiste namen en beschrijvingen op voor de machtigingen die u beschikbaar maakt als onderdeel van uw app. Op deze manier kunnen gebruikers en beheerders weten wat ze ermee instemmen wanneer ze de Api's van uw app proberen te gebruiken. Zie de sectie best practices in de [hand leiding voor machtigingen](v2-permissions-and-consent.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een app converteren naar multi tenant](howto-convert-app-to-be-multi-tenant.md)
