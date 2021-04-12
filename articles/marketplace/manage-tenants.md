---
title: Tenants beheren in de commerciële Marketplace-Azure Marketplace
description: Meer informatie over het beheren van tenants voor het programma voor commerciële Marketplace in het partner centrum.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: 446792b26527126a4db99da14a2585c17cf8610c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108223"
---
# <a name="manage-tenants-in-the-commercial-marketplace"></a>Tenants beheren in de commerciële Marketplace

**Juiste rollen**

- Manager

Een Azure Active Directory-Tenant (AD), ook wel uw *werk account* genoemd in deze documentatie, is een weer gave van uw organisatie die in de Azure Portal is ingesteld en helpt u bij het beheren van een specifiek exemplaar van micro soft-Cloud Services voor uw interne en externe gebruikers. Als uw organisatie zich abonneert op een micro soft-Cloud service, zoals Azure, Microsoft Intune of Microsoft 365, is er een Azure AD-Tenant voor u gemaakt.

U kunt meerdere tenants instellen voor gebruik met partner Center. U kunt dit doen als uw bedrijf meerdere tenants heeft (bijvoorbeeld contoso.com, contoso.uk, enzovoort), dan moet u de extra tenants hier koppelen. Met deze koppeling kunt u gebruikers toevoegen en beheren vanuit de extra tenants op uw commerciële Marketplace-account.

Elke gebruiker met de rol Manager in het partner centrum-account heeft de mogelijkheid om Azure AD-tenants toe te voegen aan of te verwijderen uit het account.

## <a name="add-an-existing-tenant"></a>Een bestaande Tenant toevoegen

Een andere Azure AD-Tenant koppelen aan uw partner centrum-account:

1. Selecteer in de rechter bovenhoek van het partner centrum **instellingen**  >  **account instellingen**.
1. Onder **organisatie profiel** selecteert u **tenants**. De huidige Tenant koppelingen worden weer gegeven.
1. Selecteer **koppelen** op het tabblad **ontwikkelaar** .
1. Voer uw Azure AD-referenties in voor de Tenant die u wilt koppelen.
1. Controleer de organisatie-en domein naam voor uw Azure AD-Tenant. Selecteer **bevestigen** om de koppeling te volt ooien.

Als de koppeling is geslaagd, kunt u account gebruikers toevoegen en beheren in de sectie gebruikers van het partner centrum. Zie [gebruikers beheren](add-manage-users.md)voor meer informatie over het toevoegen van gebruikers.

## <a name="create-a-new-tenant"></a>Een nieuwe tenant maken

Een gloed nieuwe Azure AD-Tenant maken met uw partner centrum-account:

1. Selecteer in de rechter bovenhoek van het partner centrum **instellingen**  >  **account instellingen**.
1. Onder **organisatie profiel** selecteert u **tenants**. De huidige Tenant koppelingen worden weer gegeven.
1. Op het tabblad ontwikkelaar selecteert u **maken**.
1. Voer de Directory-informatie in voor uw nieuwe Azure AD:
    - **Domein naam**: de unieke naam die we gebruiken voor uw Azure AD-domein, samen met '. onmicrosoft.com '. Als u bijvoorbeeld ' voor beeld ' hebt ingevoerd, zou uw Azure AD-domein ' example.onmicrosoft.com ' zijn.
    - **Contact opnemen met e-mail**: een e-mail adres waar wij zo nodig contact met u kunnen opnemen over uw account.
    - **Gebruikers account van globale beheerder**: de voor naam, achternaam, gebruikers naam en het wacht woord die u wilt gebruiken voor het nieuwe account van de globale beheerder.
1. Selecteer maken om de nieuwe domein-en account gegevens te bevestigen.
1. Meld u aan met uw nieuwe gebruikers naam en wacht woord voor de globale beheerder van Azure AD om [gebruikers toe te voegen en te beheren](add-manage-users.md).

Zie het artikel [een nieuwe Tenant maken in azure Active Directory](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)voor meer informatie over het maken van nieuwe tenants in uw Azure Portal, in plaats van de portal van de partner centrum.

## <a name="remove-a-tenant"></a>Een Tenant verwijderen

Als u een Tenant uit uw partner centrum-account wilt verwijderen, zoekt u de naam op de pagina **tenants** (in **account instellingen**) en selecteert u vervolgens **verwijderen**. U wordt gevraagd om te bevestigen dat u de Tenant wilt verwijderen. Nadat u dit hebt gedaan, kunnen gebruikers in die Tenant zich niet aanmelden bij het partner centrum-account. alle machtigingen die u voor deze gebruikers hebt geconfigureerd, worden verwijderd.

> [!TIP]
> U kunt een Tenant niet verwijderen als u momenteel bent aangemeld bij een partner centrum met een account in dezelfde Tenant. Als u een Tenant wilt verwijderen, moet u zich aanmelden bij het partner centrum als **Manager** voor een andere Tenant die is gekoppeld aan het account. Als er slechts één Tenant is gekoppeld aan het account, kan die Tenant pas worden verwijderd nadat u zich hebt aangemeld met de Microsoft-account waarmee het account is geopend.
