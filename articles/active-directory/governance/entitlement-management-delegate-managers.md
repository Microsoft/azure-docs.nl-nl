---
title: Delegeer toegangs beheer voor toegang tot pakket beheerders in azure AD-beleid voor rechten-Azure Active Directory
description: Meer informatie over het overdragen van toegangs beheer van IT-beheerders om toegang te krijgen tot pakket beheerders en project managers, zodat ze de toegang zelf kunnen beheren.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e81389ec953829115062a31a019caf040850cbea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798631"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Het toegangs beheer voor toegang tot pakket beheerders in azure AD-rechts Management delegeren

Als u het maken en beheren van toegangs pakketten in een catalogus wilt delegeren, voegt u gebruikers toe aan de rol toegangs pakket beheer. Toegangs pakket beheerders moeten vertrouwd zijn met de behoeften van gebruikers om toegang te vragen tot bronnen in een catalogus. Als er bijvoorbeeld een catalogus voor een project wordt gebruikt, kan een project lead een Access Package Manager voor die catalogus zijn.  Toegangs pakket beheerders kunnen geen resources toevoegen aan een catalogus, maar ze kunnen de toegangs pakketten en het beleid in een catalogus beheren.  Bij het delegeren naar een Access package manager kan die persoon vervolgens verantwoordelijk zijn voor:

- Welke rollen een gebruiker heeft voor de resources in een catalogus
- Wie moet er toegang toe hebben
- Wie moet de toegangs aanvragen goed keuren?
- Hoe lang het duurt voordat het project wordt

In deze video vindt u een overzicht van de manier waarop u Access governance van de catalogus eigenaar kunt delegeren voor toegang tot pakket beheer.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Als een catalogus eigenaar, delegeren aan een toegangs pakket beheer

Volg deze stappen om een gebruiker toe te wijzen aan de rol toegangs pakket beheer:

**Vereiste rol:** Globale beheerder, gebruikers beheerder of catalogus eigenaar

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **catalogi** en open vervolgens de catalogus waaraan u beheerders wilt toevoegen.

1. Klik in het menu links op **rollen en beheerders**.

    ![Catalogiseert rollen en beheerders](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Klik op **toegangs pakket beheerders toevoegen** om de leden voor deze rollen te selecteren.

1. Klik op **selecteren** om deze leden toe te voegen.

## <a name="remove-an-access-package-manager"></a>Een Access Package Manager verwijderen

Volg deze stappen om een gebruiker te verwijderen uit de rol Access Package Manager:

**Vereiste rol:** Globale beheerder, gebruikers beheerder of catalogus eigenaar

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **catalogi** en open vervolgens de catalogus waaraan u beheerders wilt toevoegen.

1. Klik in het menu links op **rollen en beheerders**.

1. Voeg een selectie vakje toe naast een Access package manager die u wilt verwijderen.

1. Klik op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- [Een nieuw toegangspakket maken](entitlement-management-access-package-create.md)
