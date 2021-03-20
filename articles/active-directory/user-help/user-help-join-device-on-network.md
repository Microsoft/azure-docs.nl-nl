---
title: Uw werk apparaat toevoegen aan het netwerk-AD van uw organisatie
description: Meer informatie over het toevoegen van uw werk apparaat aan het netwerk van uw organisatie.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/03/2018
ms.author: curtand
ms.reviewer: jairoc
ms.openlocfilehash: efb3ee24add847baf8264eccdf71278be5ee9496
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91536967"
---
# <a name="join-your-work-device-to-your-organizations-network"></a>Uw werk apparaat toevoegen aan het netwerk van uw organisatie
Voeg uw werkend Windows 10-apparaat toe aan het netwerk van uw organisatie, zodat u mogelijk beperkte bronnen kunt openen.

## <a name="what-happens-when-you-join-your-device"></a>Wat gebeurt er wanneer u uw apparaat toevoegt
Terwijl u uw Windows 10-apparaat aan het netwerk van uw organisatie koppelt, zullen de volgende acties optreden:

- Windows registreert uw apparaat in het netwerk van uw organisatie, zodat u toegang hebt tot uw resources met uw persoonlijke account. Nadat het apparaat is geregistreerd, voegt Windows uw apparaat vervolgens toe aan het netwerk, zodat u de gebruikers naam en het wacht woord van uw organisatie kunt gebruiken om u aan te melden en toegang te krijgen tot beperkte bronnen.

- Afhankelijk van de keuzes van uw organisatie wordt u mogelijk gevraagd om verificatie in twee stappen in te stellen met behulp van [multi-factor Authentication](multi-factor-authentication-end-user-first-time.md) of [beveiligings gegevens](./security-info-setup-signin.md).

- U kunt eventueel, op basis van de keuzes van uw organisatie, automatisch worden inge schreven bij Mobile Device Management, zoals Microsoft Intune. Zie [uw apparaat inschrijven bij intune](/intune-user-help/enroll-your-device-in-intune-all)voor meer informatie over het inschrijven van Microsoft intune.

- U gaat de aanmeldings procedure door lopen met behulp van automatische aanmelding met uw organisatie account.

## <a name="to-join-a-brand-new-windows-10-device"></a>Een gloed nieuw Windows 10-apparaat toevoegen
Als uw apparaat een merk nieuw is dat nog niet is ingesteld, kunt u het OOBE-proces (out-of-Box Experience) van Windows door lopen om uw apparaat toe te voegen aan het netwerk.

1. Start uw nieuwe apparaat op en begin met het OOBE-proces.

2. Typ op het scherm **Aanmelden met Microsoft** het e-mail adres van uw werk of school.

    ![Aanmeld scherm met e-mail adres](./media/user-help-join-device-on-network/join-device-oobe-signin.png)

3. Typ uw wacht woord op het scherm **Voer uw wacht woord** in.

    ![Het scherm wacht woord invoeren](./media/user-help-join-device-on-network/join-device-oobe-password.png)

4. Goed keuren uw apparaat op uw mobiele apparaat om toegang tot uw account te krijgen. 

    ![Scherm afbeelding met het meldings scherm ' Voer uw wacht woord in.](./media/user-help-join-device-on-network/join-device-oobe-mobile.png)

5. Voltooi het OOBE-proces, inclusief het instellen van uw privacy-instellingen en het instellen van Windows hello (indien nodig).

    Uw apparaat is nu lid van het netwerk van uw organisatie.

## <a name="to-make-sure-youre-joined-new-device"></a>Om ervoor te zorgen dat u bent aangemeld (nieuw apparaat)
U kunt er zeker van zijn dat u lid bent van uw instellingen.

1. Open **instellingen** en selecteer vervolgens **accounts**.

    ![Accounts op het scherm instellingen](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Selecteer **toegang tot werk of school** en zorg ervoor dat er tekst wordt weer geven die er ongeveer als volgt uitziet: **verbonden met *\<your_organization>* Azure AD**.

    ![Scherm opname van het venster ' toegang tot werk of school ' met het account ' verbonden met (uw organisatie) Azure AD ' geselecteerd.](./media/user-help-join-device-on-network/join-device-oobe-verify.png)


## <a name="to-join-an-already-configured-windows-10-device"></a>Lid worden van een al geconfigureerd Windows 10-apparaat
Als u uw apparaat al een tijdje hebt en het reeds is ingesteld, kunt u deze stappen volgen om uw apparaat toe te voegen aan het netwerk.

1. Open **instellingen** en selecteer vervolgens **accounts**.

2. Selecteer **toegang tot werk of school** en selecteer vervolgens **verbinding maken**.

    ![Toegang tot werk of school en koppelingen maken](./media/user-help-join-device-on-network/join-device-access-work-school-connect.png)

3. Selecteer op het scherm **een werk-of school account instellen** de optie **Dit apparaat toevoegen aan Azure Active Directory**.

    ![Het scherm voor een werk-of school account instellen](./media/user-help-join-device-on-network/join-device-setup-join-aad.png)

4. Typ uw e-mail adres (bijvoorbeeld) op het scherm de optie **nu** aanmelden alain@contoso.com . Klik vervolgens op **volgende**.

    ![U kunt u aanmelden op het scherm](./media/user-help-join-device-on-network/join-device-setup-get-signed-in.png)

5. Typ uw wacht woord in het scherm **wacht woord invoeren** en selecteer **Aanmelden**.

    ![Wachtwoord invoeren](./media/user-help-join-device-on-network/join-device-setup-password.png)

6. Goed keuren uw apparaat op uw mobiele apparaat om toegang tot uw account te krijgen. 

    ![Scherm voor mobiele meldingen](./media/user-help-join-device-on-network/join-device-setup-mobile.png)

7. Bekijk op het scherm  **Zorg ervoor dat dit uw organisatie is** en controleer de informatie om er zeker van te zijn dat het goed is, en selecteer vervolgens **toevoegen**.

    ![Controleer of dit het scherm voor de verificatie van uw organisatie is](./media/user-help-join-device-on-network/join-device-setup-confirm.png)

8. Klik op **gereed** in het scherm **u bent alle set** .

    ![U bent klaar met het instellen van het scherm](./media/user-help-join-device-on-network/join-device-setup-finish.png)

## <a name="to-make-sure-youre-joined"></a>Zorg ervoor dat u lid bent van
U kunt er zeker van zijn dat u lid bent van uw instellingen.

1. Open **instellingen** en selecteer vervolgens **accounts**.

    ![Accounts op het scherm instellingen](./media/user-help-join-device-on-network/join-device-settings-accounts.png)

2. Selecteer **toegang tot werk of school** en zorg ervoor dat er tekst wordt weer geven die er ongeveer als volgt uitziet: **verbonden met *\<your_organization>* Azure AD**.

    ![Het scherm werk of school openen met verbonden contoso-account](./media/user-help-join-device-on-network/join-device-setup-verify.png)

## <a name="next-steps"></a>Volgende stappen
Nadat u uw apparaat aan het netwerk van uw organisatie hebt toegevoegd, kunt u toegang krijgen tot al uw resources met behulp van de gegevens van uw werk-of school account.

- Als uw organisatie uw persoonlijke apparaat wil registreren, zoals uw telefoon, raadpleegt u [uw persoonlijke apparaat registreren op het netwerk van uw organisatie](user-help-register-device-on-network.md).

- Als uw organisatie wordt beheerd met Microsoft Intune en u vragen hebt over inschrijving, aanmelden of een ander probleem met betrekking tot intune, raadpleegt u de [Help-inhoud van intune-gebruikers](/intune-user-help/use-managed-devices-to-get-work-done).