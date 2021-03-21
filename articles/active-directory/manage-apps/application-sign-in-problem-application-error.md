---
title: Er wordt een fout bericht weer gegeven op de app-pagina nadat u zich hebt aangemeld | Microsoft Docs
description: Problemen met Azure AD-aanmelding oplossen wanneer de app een fout bericht retourneert.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c96209f33491645510d8592997c418472d4f227c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258809"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Op een app-pagina wordt een foutbericht weergegeven nadat de gebruiker zich heeft aangemeld

In dit scenario ondertekent Azure Active Directory (Azure AD) de gebruiker in. Maar er wordt een fout bericht weer gegeven in de toepassing, zodat de gebruiker de aanmeldings stroom niet kan volt ooien. Het probleem is dat de app geen antwoord heeft geaccepteerd dat door Azure AD is uitgegeven.

Er zijn verschillende mogelijke redenen waarom de app de reactie van Azure AD niet heeft geaccepteerd. Als in het fout bericht niet duidelijk wordt aangegeven wat er in het antwoord ontbreekt, kunt u het volgende proberen:

-   Als de app de Azure AD-galerie is, controleert u of u de stappen in de [procedure voor het opsporen van op SAML gebaseerde eenmalige aanmelding bij toepassingen in azure AD](./debug-saml-sso-issues.md)hebt gevolgd.

-   Gebruik een hulp programma zoals [Fiddler](https://www.telerik.com/fiddler) om de SAML-aanvraag, het antwoord en het token vast te leggen.

-   Stuur het SAML-antwoord naar de leverancier van de app en vraag wat er ontbreekt.

## <a name="attributes-are-missing-from-the-saml-response"></a>Er ontbreken kenmerken in het SAML-antwoord

Voer de volgende stappen uit om een kenmerk toe te voegen aan de Azure AD-configuratie die wordt verzonden in de Azure AD-respons:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als globale beheerder of co-beheerder.

2. Selecteer boven aan het navigatie deel venster aan de linkerkant **alle services** om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het vak Zoek opdracht filteren en selecteer vervolgens **Azure Active Directory**.

4. Selecteer **bedrijfs toepassingen** in het navigatie deel venster van Azure AD.

5. Selecteer **alle toepassingen** om een lijst met uw apps weer te geven.

   > [!NOTE]
   > Als u de gewenste app niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen**. Stel de optie **weer geven** in op alle toepassingen.

6. Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

7. Nadat de app is geladen, selecteert u **eenmalige aanmelding** in het navigatie deel venster.

8. Selecteer in de sectie **gebruikers kenmerken** de optie **alle andere gebruikers kenmerken weer geven en bewerken**. Hier kunt u wijzigen welke kenmerken in het SAML-token naar de app worden verzonden wanneer gebruikers zich aanmelden.

   Een kenmerk toevoegen:

   1. Selecteer **kenmerk toevoegen**. Voer de **naam** in en selecteer de **waarde** in de vervolg keuzelijst.

   1.  Selecteer **Opslaan**. U ziet het nieuwe kenmerk in de tabel.

9. Sla de configuratie op.

   De volgende keer dat de gebruiker zich aanmeldt bij de app, verzendt Azure AD het nieuwe kenmerk in het SAML-antwoord.

## <a name="the-app-doesnt-identify-the-user"></a>De app identificeert de gebruiker niet

Het aanmelden bij de app is mislukt omdat het SAML-antwoord een kenmerk, zoals een rol, ontbreekt. Het kan ook zijn dat de app een andere indeling of waarde verwacht voor het kenmerk **NameID** (gebruikers-id).

Als u gebruikmaakt van [automatische gebruikers inrichting van Azure AD](../app-provisioning/user-provisioning.md) voor het maken, onderhouden en verwijderen van gebruikers in de app, controleert u of de gebruiker is ingericht voor de SaaS-app. Voor meer informatie, Zie [er worden geen gebruikers ingericht voor een Azure AD Gallery-toepassing](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Een kenmerk toevoegen aan de configuratie van de Azure AD-app

Voer de volgende stappen uit om de waarde voor de gebruikers-id te wijzigen:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als globale beheerder of co-beheerder.

2. Selecteer **alle services** boven aan het navigatie deel venster aan de linkerkant om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het vak Zoek opdracht filteren en selecteer vervolgens **Azure Active Directory**.

4. Selecteer **bedrijfs toepassingen** in het navigatie deel venster van Azure AD.

5. Selecteer **alle toepassingen** om een lijst met uw apps weer te geven.

   > [!NOTE]
   > Als u de gewenste app niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen**. Stel de optie **weer geven** in op alle toepassingen.

6. Selecteer de app die u wilt configureren voor eenmalige aanmelding.

7. Nadat de app is geladen, selecteert u **eenmalige aanmelding** in het navigatie deel venster.

8. Onder **gebruikers kenmerken** selecteert u de unieke id voor de gebruiker uit de vervolg keuzelijst **gebruikers-id** .

## <a name="change-the-nameid-format"></a>De NameID-indeling wijzigen

Als de toepassing een andere indeling verwacht voor het kenmerk **NameID** (gebruikers-id), raadpleegt u [NameID bewerken](../develop/active-directory-saml-claims-customization.md#editing-nameid) om de NameID-indeling te wijzigen.

Azure AD selecteert de indeling voor het **NameID** -kenmerk (gebruikers-id) op basis van de waarde die is geselecteerd of de indeling die door de app is aangevraagd in het SAML-AuthRequest. Zie de sectie ' NameIDPolicy ' van het [SAML-protocol voor eenmalige aanmelding](../develop/single-sign-on-saml-protocol.md#nameidpolicy)voor meer informatie.

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>De app verwacht een andere handtekening methode voor het SAML-antwoord

Voer de volgende stappen uit om te wijzigen welke onderdelen van het SAML-token digitaal zijn ondertekend door Azure AD:

1. Open de [Azure Portal](https://portal.azure.com/) en meld u aan als globale beheerder of co-beheerder.

2. Selecteer **alle services** boven aan het navigatie deel venster aan de linkerkant om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het vak Zoek opdracht filteren en selecteer vervolgens **Azure Active Directory**.

4. Selecteer **bedrijfs toepassingen** in het navigatie deel venster van Azure AD.

5. Selecteer **alle toepassingen** om een lijst met uw apps weer te geven.

   > [!NOTE]
   > Als u de gewenste toepassing niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen**. Stel de optie **weer geven** in op alle toepassingen.

6. Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

7. Nadat de toepassing is geladen, selecteert u **eenmalige aanmelding** in het navigatie deel venster.

8. Onder **SAML-handtekening certificaat** selecteert u  **Geavanceerde instellingen voor certificaat ondertekening weer geven**.

9. Selecteer de **handtekening optie** die door de app wordt verwacht van de volgende opties:

   * **SAML-respons ondertekenen**
   * **SAML-respons en-bevestiging ondertekenen**
   * **SAML-bevestiging ondertekenen**

   De volgende keer dat de gebruiker zich aanmeldt bij de app, zal Azure AD het deel van het SAML-antwoord ondertekenen dat u hebt geselecteerd.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>De app verwacht het SHA-1-Ondertekeningsalgoritme

Standaard ondertekent Azure AD het SAML-token met het veiligste algoritme. Het is raadzaam om het Ondertekeningsalgoritme niet te wijzigen in *SHA-1* , tenzij de app SHA-1 vereist.

Voer de volgende stappen uit om het Ondertekeningsalgoritme te wijzigen:

1. Open de [Azure Portal](https://portal.azure.com/) en meld u aan als globale beheerder of co-beheerder.

2. Selecteer **alle services** boven aan het navigatie deel venster aan de linkerkant om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het vak Zoek opdracht filteren en selecteer vervolgens **Azure Active Directory**.

4. Selecteer **bedrijfs toepassingen** in het navigatie deel venster van Azure AD.

5. Selecteer **alle toepassingen** om een lijst met uw toepassingen weer te geven.

   > [!NOTE]
   > Als u de gewenste toepassing niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen**. Stel de optie **weer geven** in op alle toepassingen.

6. Selecteer de app die u wilt configureren voor eenmalige aanmelding.

7. Nadat de app is geladen, selecteert u **eenmalige aanmelding** in het navigatie deel venster aan de linkerkant van de app.

8. Onder **SAML-handtekening certificaat** selecteert u **Geavanceerde instellingen voor certificaat ondertekening weer geven**.

9. Selecteer **SHA-1** als het **Ondertekeningsalgoritme**.

   De volgende keer dat de gebruiker zich aanmeldt bij de app, wordt het SAML-token door Azure AD ondertekend met behulp van het algoritme SHA-1.

## <a name="next-steps"></a>Volgende stappen
[Fout opsporing op SAML gebaseerde eenmalige aanmelding bij toepassingen in azure AD](./debug-saml-sso-issues.md).