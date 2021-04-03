---
title: Toegangs pakket verbergen of verwijderen in rechten beheer-Azure AD
description: Meer informatie over het verbergen of verwijderen van een toegangs pakket in Azure Active Directory rechten beheer.
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
ms.openlocfilehash: 22aa97c6e627c2072636ca2e079877ff0f608b68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798797"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Een toegangs pakket verbergen of verwijderen in het beheer van rechten van Azure AD

Toegangs pakketten worden standaard gedetecteerd. Dit betekent dat als een beleid toestaat dat een gebruiker het toegangs pakket kan aanvragen, het toegangs pakket in hun mijn Access-Portal automatisch wordt weer gegeven. U kunt de **verborgen** instelling echter zodanig wijzigen dat het toegangs pakket niet wordt weer gegeven in de mijn Access-portal van de gebruiker.

In dit artikel wordt beschreven hoe u een toegangs pakket kunt verbergen of verwijderen.

## <a name="change-the-hidden-setting"></a>De verborgen instelling wijzigen

Volg deze stappen om de **verborgen** instelling voor een toegangs pakket te wijzigen.

**Vereiste rol:** globale beheerder, gebruikersbeheerder, cataloguseigenaar of toegangspakketbeheerder

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op de pagina overzicht op **bewerken**.

1. Stel de instelling **verborgen** in.

    Als deze waarde is ingesteld op **Nee**, wordt het toegangs pakket weer gegeven in de mijn Access-portal van de gebruiker.

    Als deze instelling is ingesteld op **Ja**, wordt het toegangs pakket niet vermeld in de mijn Access-portal van de gebruiker. De enige manier waarop een gebruiker het toegangs pakket kan weer geven, is als hij of zij de **koppeling directe mijn Access-Portal** naar het toegangs pakket heeft. Zie [koppeling delen om een toegangs pakket aan te vragen](entitlement-management-access-package-settings.md)voor meer informatie.

## <a name="delete-an-access-package"></a>Een toegangs pakket verwijderen

Een toegangs pakket kan alleen worden verwijderd als er geen actieve gebruikers toewijzingen zijn. Volg deze stappen voor het verwijderen van een toegangs pakket.

**Vereiste rol:** globale beheerder, gebruikersbeheerder, cataloguseigenaar of toegangspakketbeheerder

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik in het menu links op **toewijzingen** en verwijder toegang voor alle gebruikers.

1. Klik in het menu links op **overzicht** en klik vervolgens op **verwijderen**.

1. Klik op **Ja** in het bericht verwijderen dat wordt weer gegeven.

## <a name="next-steps"></a>Volgende stappen

- [Toewijzingen voor een toegangs pakket weer geven, toevoegen en verwijderen](entitlement-management-access-package-assignments.md)
- [Rapporten en logboeken weergeven](entitlement-management-reports.md)
