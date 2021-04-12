---
title: Azure AD-toepassingen toevoegen en beheren-Azure Marketplace
description: Meer informatie over het toevoegen en beheren van Azure AD-toepassingen voor het Commercial Marketplace-programma in Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/06/2021
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.openlocfilehash: be527647466ad76455585e16baabb26e39e42193
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108239"
---
# <a name="add-and-manage-azure-ad-applications"></a>Azure AD-toepassingen toevoegen en beheren

**Juiste rollen**

- Eigenaar
- Manager

U kunt toepassingen of services die deel uitmaken van de Azure Active Directory van uw bedrijf (Azure AD) toestaan om toegang te krijgen tot uw partner centrum-account.

## <a name="add-existing-azure-ad-applications"></a>Bestaande Azure AD-toepassingen toevoegen

Toepassingen toevoegen die al bestaan in de Azure Active Directory van uw bedrijf:

1. Selecteer op de pagina **gebruikers** (onder **account instellingen**) de optie **Azure AD-toepassingen toevoegen**.
1. Selecteer een of meer Azure AD-toepassingen in de lijst die wordt weer gegeven. U kunt het zoekvak gebruiken om te zoeken naar specifieke Azure AD-toepassingen. Als u meer dan één Azure AD-toepassing selecteert om aan uw partner Center-account toe te voegen, moet u deze dezelfde rol of set aangepaste machtigingen toewijzen. Als u meerdere Azure AD-toepassingen met verschillende rollen/machtigingen wilt toevoegen, herhaalt u deze stappen voor elke rol of set aangepaste machtigingen.
1. Wanneer u klaar bent met het selecteren van Azure AD-toepassingen, selecteert u **geselecteerde toevoegen**.
1. Geef in de sectie **functies** de rol (len) of aangepaste machtigingen voor de geselecteerde Azure AD-toepassing (en) op.
1. Selecteer **Opslaan**.

## <a name="add-new-azure-ad-applications"></a>Nieuwe Azure AD-toepassingen toevoegen

Als u partner Center toegang wilt verlenen tot een gloed nieuwe Azure AD-toepassings account, kunt u er een maken in de sectie **gebruikers** . Hiermee maakt u een nieuw account in uw werk account (Azure AD-Tenant), niet alleen in uw partner centrum-account. Als u hoofd zakelijk gebruikmaakt van deze Azure AD-toepassing voor Partner Center-verificatie en gebruikers geen toegang meer nodig hebt, kunt u een geldig adres invoeren voor de **antwoord-URL** en de URI van de **App-ID**, zolang deze waarden niet worden gebruikt door een andere Azure AD-toepassing in uw Directory.

1. Selecteer op de pagina **gebruikers** (onder **account instellingen**) de optie **Azure AD-toepassingen toevoegen**.
1. Selecteer op de volgende pagina de optie **nieuwe Azure AD-toepassing**.
1. Voer de **antwoord-URL** in voor de nieuwe Azure AD-toepassing. Dit is de URL waar gebruikers zich kunnen aanmelden en uw Azure AD-toepassing gebruiken (ook wel de URL van de app of de Sign-On URL genoemd). De *antwoord-URL* mag niet langer zijn dan 256 tekens en moet uniek zijn binnen uw Directory.
1. Voer de **App-ID-URI** in voor de nieuwe Azure AD-toepassing. Dit is een logische id voor de Azure AD-toepassing die wordt weer gegeven wanneer een eenmalige aanmelding wordt verzonden naar Azure AD. De *URI van de App-ID* moet uniek zijn voor elke Azure AD-toepassing in uw Directory. Deze ID mag niet langer zijn dan 256 tekens. Zie [toepassingen integreren met Azure Active Directory](/azure/active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts)voor meer informatie over de App-ID-URI.
1. Geef in de sectie **rollen** de rol (len) of aangepaste machtigingen voor de Azure AD-toepassing op.
1. Selecteer **Opslaan**.

Nadat u een Azure AD-toepassing hebt toegevoegd of gemaakt, kunt u teruggaan naar de sectie **gebruikers** en de toepassings naam selecteren om de instellingen voor de toepassing te controleren, waaronder de Tenant-id, de client-id, de antwoord-URL en de URI van de App-ID.

## <a name="remove-an-azure-ad-application"></a>Een Azure AD-toepassing verwijderen

Als u een toepassing uit uw werk account (Azure AD-Tenant) wilt verwijderen, gaat u naar **gebruikers** (onder **account instellingen**), selecteert u de toepassing die u wilt verwijderen met het selectie vakje in de kolom uiterst rechts en kiest u vervolgens **verwijderen** in de beschik bare acties. Er wordt een pop-upvenster weer gegeven waarin u kunt bevestigen dat u de geselecteerde toepassing (en) wilt verwijderen.

## <a name="manage-keys-for-an-azure-ad-application"></a>Sleutels voor een Azure AD-toepassing beheren

Als uw Azure AD-toepassing gegevens in Microsoft Azure AD leest en schrijft, is er een sleutel nodig. U kunt sleutels voor een Azure AD-toepassing maken door de gegevens te bewerken in Partner Center. U kunt ook sleutels verwijderen die niet meer nodig zijn.

1. Selecteer op de pagina **gebruikers** (onder **account instellingen**) de naam van de Azure AD-toepassing. U ziet alle actieve sleutels voor de Azure AD-toepassing, met inbegrip van de datum waarop de sleutel is gemaakt en het verlopen van 50.
1. Selecteer **verwijderen** om een sleutel te verwijderen die niet meer nodig is.
1. Selecteer **nieuwe sleutel toevoegen** om een nieuwe sleutel toe te voegen.
1. Er wordt een scherm weer gegeven met de **client-id** en **sleutel waarden**. Zorg ervoor dat u deze informatie afdrukt of kopieert, omdat u deze niet meer kunt openen nadat u deze pagina verlaat.
1. Als u meer sleutels wilt maken, selecteert u **nog een sleutel toevoegen**.
