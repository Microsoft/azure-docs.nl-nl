---
title: 'Azure AD Connect: Selecteer het installatie type | Microsoft Docs'
description: In dit onderwerp wordt uitgelegd hoe u het installatie type selecteert dat u wilt gebruiken voor Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7275d115210100bdd4a3a2eb683c867a6a4a4f4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996607"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Het installatietype voor Azure AD Connect selecteren
Azure AD Connect heeft twee installatie typen voor een nieuwe installatie: Express en aangepast. In dit onderwerp kunt u bepalen welke optie u tijdens de installatie wilt gebruiken.

## <a name="express"></a>Express
Express is de meest voorkomende optie en wordt gebruikt door ongeveer 90% van alle nieuwe installaties. Het is ontworpen om een configuratie te bieden die geschikt is voor de meest voorkomende klant scenario's.

Hierbij wordt ervan uitgegaan:

- U hebt één Active Directory forest on-premises.
- U hebt een beheerders account voor ondernemingen dat u kunt gebruiken voor de installatie.
- Uw on-premises Active Directory heeft minder dan 100.000 objecten.

U krijgt:

- [Wachtwoord hash-synchronisatie](how-to-connect-password-hash-synchronization.md) van on-premises naar Azure AD voor eenmalige aanmelding.
- Een configuratie waarmee [gebruikers, groepen, contact personen en Windows 10-computers](concept-azure-ad-connect-sync-default-configuration.md)worden gesynchroniseerd.
- Synchronisatie van alle in aanmerking komende objecten in alle domeinen en alle organisatie-eenheden.
- [Automatische upgrade](how-to-connect-install-automatic-upgrade.md) is ingeschakeld om ervoor te zorgen dat u altijd de meest recente beschik bare versie gebruikt.

Opties die u nog steeds kunt gebruiken:

- Als u niet alle Ou's wilt synchroniseren, kunt u nog steeds de snelle en op de laatste pagina gebruiken, de selectie ervan opheffen * * start het synchronisatie proces... * * *. Voer vervolgens de installatie wizard opnieuw uit en wijzig de organisatie-eenheden in [configuratie opties](how-to-connect-installation-wizard.md#customize-synchronization-options) en schakel geplande synchronisatie in.
- U wilt een van de functies in Azure AD Premium inschakelen, zoals wacht woord terugschrijven. Bekijk eerst de eerste keer dat de installatie is voltooid. Voer vervolgens de installatie wizard opnieuw uit en wijzig de [configuratie opties](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Aangepast
Het aangepaste pad biedt veel meer opties dan Express. Het moet worden gebruikt in alle gevallen waarin de configuratie die in de vorige sectie voor Express wordt beschreven, niet representatief is voor uw organisatie.

Gebruiken wanneer:

- U hebt geen toegang tot een ondernemings Administrator-account in Active Directory.
- U hebt meer dan één forest of u wilt in de toekomst meer dan één forest synchroniseren.
- U hebt domeinen in uw forest die niet bereikbaar zijn vanaf de Connect-server.
- U wilt de Federatie of Pass-Through-verificatie voor gebruikers aanmelding gebruiken.
- U hebt meer dan 100.000 objecten en u moet een volledige SQL Server gebruiken.
- U wilt filteren op basis van groepen en niet alleen filters op basis van een domein of organisatie-eenheid.

## <a name="upgrade-from-dirsync"></a>Upgrade van DirSync
Als u momenteel DirSync gebruikt, volgt u de stappen in [upgraden van DirSync](how-to-dirsync-upgrade-get-started.md) om uw bestaande configuratie bij te werken. Er zijn twee verschillende upgrade opties:

- In-place upgrade om Connect op dezelfde server te installeren.
- Parallelle implementatie om verbinding te installeren op een nieuwe server terwijl de bestaande DirSync-server nog actief is.

## <a name="upgrade-from-azure-ad-sync"></a>Upgrade van Azure AD Sync
Als u momenteel gebruikmaakt van Azure AD Sync, kunt u [dezelfde stappen](how-to-upgrade-previous-version.md) volgen als bij het upgraden van de ene verbindings versie naar een nieuwer. Er zijn twee verschillende upgrade opties:

- In-place upgrade om Connect op dezelfde server te installeren.
- Swing-migratie om verbinding te installeren op een nieuwe server terwijl de bestaande Azure AD Sync server nog operationeel is.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migreren van FIM 2010 of MIM2016
Als u momenteel Forefront Identity Manager 2010 of Microsoft Identity Manager 2016 met de Azure AD-connector gebruikt, is de enige optie een migratie. Volg de stappen die worden beschreven in [Swing-migratie](how-to-upgrade-previous-version.md#swing-migration). Vervang in de stappen een wille keurige vermelding van Azure AD Sync met FIM 2010/MIM2016.

## <a name="next-steps"></a>Volgende stappen
Afhankelijk van de optie die u hebt geselecteerd voor gebruik, gebruikt u de inhouds opgave aan de linkerkant om uw artikel te vinden met de gedetailleerde stappen.
