---
title: Werk accounts van het bedrijf en het partner centrum
description: Controleren of uw bedrijf beschikt over een werk account dat is ingesteld met micro soft, een nieuw werk account maken of meerdere werk accounts instellen voor gebruik met partner centrum.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: parthpandyaMSFT
ms.author: parthp
ms.date: 05/30/2019
ms.openlocfilehash: 5b4aadc506b2f4a251ccef5e9488066b609ba5a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93130524"
---
# <a name="company-work-accounts-and-partner-center"></a>Werk accounts van het bedrijf en het partner centrum

Het partner centrum gebruikt bedrijfs accounts, ook wel bekend als Azure Active Directory (AD)-tenants, voor het beheren van toegang tot accounts voor meerdere gebruikers, het beheer van machtigingen, hostgroepen en toepassingen en het onderhouden van profiel gegevens. Als u het e-mail account domein van uw bedrijf koppelt aan uw partner centrum-account, kunnen werk nemers van uw bedrijf zich aanmelden bij het partner centrum om Marketplace-aanbiedingen te beheren met hun eigen gebruikers namen en wacht woorden.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Controleren of uw bedrijf al een werk account heeft

Als uw bedrijf zich heeft geabonneerd op een micro soft-Cloud service, zoals Azure, Microsoft Intune of Microsoft 365, hebt u al een account domein van het werk-e-mail (ook wel een Azure Active Directory-Tenant genoemd) die kan worden gebruikt met partner centrum.

Voer de volgende stappen uit om te controleren:
1. Meld u aan bij de Azure-beheer Portal op https://portal.azure.com .
2. Selecteer **Azure Active Directory** in het navigatie menu aan de linkerkant en selecteer vervolgens **aangepaste domein namen**.
3. Als u al een werk account hebt, wordt uw domein naam weer gegeven.

Als uw bedrijf nog geen werk account heeft, wordt er een voor u gemaakt tijdens het inschrijvings proces van het partner centrum.

## <a name="set-up-multiple-work-accounts"></a>Meerdere werk accounts instellen

Voordat u besluit om een bestaand werk account te gebruiken, moet u rekening houden met het aantal gebruikers in het werk account dat nodig heeft om toegang te krijgen tot het partner centrum. Als u gebruikers hebt in het werk account dat geen toegang nodig heeft tot het partner centrum, kunt u overwegen om meerdere werk accounts te maken, zodat alleen de gebruikers die toegang hebben tot het partner centrum, worden weer gegeven op een bepaald account.

## <a name="create-a-new-work-account"></a>Een nieuw werk account maken

Volg de onderstaande stappen om een nieuw werk account voor uw bedrijf te maken. Mogelijk moet u hulp vragen aan iemand die beheerders rechten heeft voor het Microsoft Azure account van uw bedrijf.

1. Meld u aan bij de [Microsoft Azure-portal](https://portal.azure.com).
2. Selecteer de **Azure Active Directory** gebruikers in het navigatie menu aan de linkerkant  ->  .
3. Selecteer **nieuwe gebruiker** en maak een nieuw Azure-werk account door een naam en e-mail adres op te geven. Zorg ervoor dat de **Directory-rol** is ingesteld op **gebruiker** en schakel het selectie vakje **wacht woord weer geven** onderaan in om het automatisch gegenereerde wacht woord te bekijken en te noteren.
4. Selecteer **maken** om de nieuwe gebruiker op te slaan.

Het e-mail adres voor het gebruikers account moet een geverifieerde domein naam in uw Directory zijn. U kunt alle geverifieerde domeinen in uw directory weer geven door **Azure Active Directory**  ->  **aangepaste domein namen** te selecteren in het navigatie menu aan de linkerkant.

Zie [een domein toevoegen of koppelen in azure AD](../../active-directory/fundamentals/add-custom-domain.md)voor meer informatie over het toevoegen van aangepaste domeinen in azure Active Directory.

## <a name="troubleshoot-work-email-sign-in"></a>Problemen met aanmelding bij werk-e-mail oplossen

Als u problemen ondervindt met het aanmelden bij uw werk account (ook wel bekend als uw Azure AD-Tenant), zoekt u het scenario in het onderstaande diagram dat het beste overeenkomt met uw situatie en voert u de aanbevolen stappen uit.

![Diagram voor het oplossen van problemen met het aanmelden van een werk account](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Volgende stappen

- [Uw commerciële Marketplace-account beheren in het partner centrum](./manage-account.md)