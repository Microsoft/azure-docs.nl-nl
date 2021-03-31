---
title: Persoonlijke apparaten registreren op het netwerk van een organisatie-Azure AD
description: Meer informatie over het registreren van uw persoonlijke apparaat op het netwerk van uw organisatie, zodat u toegang hebt tot de beveiligde bronnen van uw organisatie.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/31/2020
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 0435b99525c34eb72d7cc5315ccb4359859cd528
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90033044"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Uw persoonlijke apparaat registreren op het netwerk van uw organisatie
Registreer uw persoonlijke apparaat (meestal een telefoon of Tablet) op het netwerk van uw organisatie. Nadat het apparaat is geregistreerd, heeft het toegang tot de beperkte resources van uw organisatie.

>[!Note]
>In dit artikel wordt een Windows-apparaat gebruikt voor demonstratie doeleinden, maar u kunt ook apparaten met iOS, Android of macOS registreren.

## <a name="what-happens-when-you-register-your-device"></a>Wat gebeurt er wanneer u uw apparaat registreert
Wanneer u uw apparaat registreert op het netwerk van uw organisatie, zullen de volgende acties optreden:

- Windows registreert uw apparaat in het netwerk van uw organisatie.

- Afhankelijk van de keuzes van uw organisatie wordt u mogelijk gevraagd om verificatie in twee stappen in te stellen via [twee ledige verificatie](multi-factor-authentication-end-user-first-time.md) of [beveiligings gegevens](./security-info-setup-signin.md).

- U kunt eventueel, op basis van de keuzes van uw organisatie, automatisch worden inge schreven bij Mobile Device Management, zoals Microsoft Intune. Zie [uw apparaat inschrijven bij intune](/intune-user-help/enroll-your-device-in-intune-all)voor meer informatie over het inschrijven van Microsoft intune.

- U gaat door met de gebruikers naam en het wacht woord voor uw werk-of school account.

## <a name="to-register-your-windows-device"></a>Uw Windows-apparaat registreren

Volg deze stappen om uw persoonlijke apparaat te registreren in uw netwerk.

1. Open **instellingen** en selecteer vervolgens **accounts**.

    ![Accounts op het scherm instellingen](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Selecteer **toegang tot werk of school** en selecteer vervolgens **verbinding maken** in het scherm **toegang werk of school** .

    ![Het scherm toegang tot werk of school met de optie verbinding maken gemarkeerd](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. Typ in het scherm **een werk-of school account toevoegen** uw e-mail adres voor uw werk-of school account en selecteer vervolgens **volgende**. Bijvoorbeeld alain@contoso.com.

4. Meld u aan bij uw werk-of school account en selecteer **Aanmelden**.

5. Voltooi de rest van het registratie proces, inclusief het goed keuren van uw aanvraag voor identiteits verificatie (als u verificatie in twee stappen gebruikt) en het instellen van Windows hello (indien nodig).

## <a name="to-verify-that-youre-registered"></a>Controleren of u bent geregistreerd
U kunt ervoor zorgen dat u bent geregistreerd door te kijken naar uw instellingen.

1. Open **instellingen** en selecteer vervolgens **accounts**.

    ![Accounts op het scherm instellingen](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Selecteer **toegang tot werk of school** en zorg ervoor dat u uw werk-of school account ziet.

    ![Het scherm werk of school openen met verbonden contoso-account](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Volgende stappen
Nadat u uw persoonlijke apparaat hebt geregistreerd bij het netwerk van uw organisatie, kunt u toegang krijgen tot de meeste resources.

- Als uw organisatie u wil toevoegen aan uw werk apparaat, raadpleegt u [uw werk apparaat toevoegen aan het netwerk van uw organisatie](user-help-join-device-on-network.md).