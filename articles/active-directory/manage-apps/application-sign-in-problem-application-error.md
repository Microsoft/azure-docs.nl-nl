---
title: Het foutbericht wordt weergegeven op de app-pagina nadat u zich hebt | Microsoft Docs
description: Problemen met aanmelden bij Azure AD oplossen wanneer de app een foutbericht retourneert.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ddebc4778d923bc3a002f14fc4b4db1b7bb730d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379295"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Op een app-pagina wordt een foutbericht weergegeven nadat de gebruiker zich heeft aangemeld

In dit scenario meldt Azure Active Directory (Azure AD) de gebruiker aan. Maar de toepassing geeft een foutbericht weer en laat de gebruiker de aanmeldingsstroom niet voltooien. Het probleem is dat de app het antwoord dat Azure AD heeft uitgegeven, niet heeft geaccepteerd.

Er zijn verschillende mogelijke redenen waarom de app het antwoord van Azure AD niet heeft geaccepteerd. Als in het foutbericht niet duidelijk wordt aangegeven wat er ontbreekt in het antwoord, probeert u het volgende:

-   Als de app de Azure AD-galerie is, controleert u of u de stappen in How [to debug SAML-based single sign-on](./debug-saml-sso-issues.md)to applications in Azure AD hebt gevolgd.

-   Gebruik een hulpprogramma zoals [Fiddler om](https://www.telerik.com/fiddler) de SAML-aanvraag, het antwoord en het token vast te leggen.

-   Verzend het SAML-antwoord naar de leverancier van de app en vraag wat er ontbreekt.

## <a name="attributes-are-missing-from-the-saml-response"></a>Kenmerken ontbreken in het SAML-antwoord

Als u een kenmerk wilt toevoegen aan de Azure AD-configuratie die wordt verzonden in het Azure AD-antwoord, volgt u deze stappen:

1. Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als globale beheerder of co-beheerder.

2. Selecteer bovenaan het navigatiedeelvenster aan de linkerkant **Alle services om** de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het zoekvak en selecteer vervolgens **Azure Active Directory**.

4. Selecteer **Bedrijfstoepassingen** in het navigatiedeelvenster van Azure AD.

5. Selecteer **Alle toepassingen om** een lijst met uw apps weer te geven.

   > [!NOTE]
   > Als u de want-app niet ziet, gebruikt u het **besturingselement Filter** bovenaan de **lijst Alle toepassingen.** Stel de **optie** Tonen in op Alle toepassingen.

6. Selecteer de toepassing die u wilt configureren voor een aanmelding.

7. Nadat de app is geladen, **selecteert u Een aanmelding** in het navigatiedeelvenster.

8. Selecteer in **de sectie Gebruikerskenmerken** de optie **Alle andere gebruikerskenmerken** weergeven en bewerken. Hier kunt u wijzigen welke kenmerken naar de app moeten worden verzenden in het SAML-token wanneer gebruikers zich aanmelden.

   Een kenmerk toevoegen:

   1. Selecteer **Kenmerk toevoegen.** Voer de **Naam** in en selecteer **de Waarde** in de vervolgkeuzelijst.

   1.  Selecteer **Opslaan**. U ziet het nieuwe kenmerk in de tabel.

9. Sla de configuratie op.

   De volgende keer dat de gebruiker zich bij de app meldt, verzendt Azure AD het nieuwe kenmerk in het SAML-antwoord.

## <a name="the-app-doesnt-identify-the-user"></a>De app identificeert de gebruiker niet

Aanmelden bij de app mislukt omdat er een kenmerk zoals een rol ontbreekt in het SAML-antwoord. Of het mislukt omdat de app een andere indeling of waarde verwacht voor het kenmerk **NameID** (gebruikers-id).

Als u automatische gebruikers [inrichten](../app-provisioning/user-provisioning.md) in Azure AD gebruikt om gebruikers in de app te maken, onderhouden en verwijderen, controleert u of de gebruiker is ingericht voor de SaaS-app. Zie No users are being provisioned to an Azure AD Gallery application (Geen [gebruikers worden ingericht voor een toepassing in de Azure AD-galerie) voor meer informatie.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Een kenmerk toevoegen aan de configuratie van de Azure AD-app

Als u de waarde van de gebruikers-id wilt wijzigen, volgt u deze stappen:

1. Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als globale beheerder of co-beheerder.

2. Selecteer **Alle services** bovenaan het navigatiedeelvenster aan de linkerkant om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het filterzoekvak en selecteer vervolgens **Azure Active Directory**.

4. Selecteer **Bedrijfstoepassingen** in het navigatiedeelvenster van Azure AD.

5. Selecteer **Alle toepassingen om** een lijst met uw apps weer te geven.

   > [!NOTE]
   > Als u de want-app niet ziet, gebruikt u het **besturingselement Filter** bovenaan de **lijst Alle toepassingen.** Stel de **optie** Tonen in op Alle toepassingen.

6. Selecteer de app die u wilt configureren voor eenmalige aanmelding.

7. Nadat de app is geladen, **selecteert u Een aanmelding** in het navigatiedeelvenster.

8. Selecteer **onder Gebruikerskenmerken** de unieke id voor de gebruiker in **de** vervolgkeuzelijst Gebruikers-id.

## <a name="change-the-nameid-format"></a>De NameID-indeling wijzigen

Als de toepassing een andere indeling verwacht voor het kenmerk **NameID** (user identifier), zie [NameID](../develop/active-directory-saml-claims-customization.md#editing-nameid) bewerken om de NameID-indeling te wijzigen.

Azure AD selecteert de indeling voor het **NameID-kenmerk** (gebruikers-id) op basis van de geselecteerde waarde of de indeling die is aangevraagd door de app in de SAML-authRequest. Zie de sectie NameIDPolicy van het [SAML-protocol](../develop/single-sign-on-saml-protocol.md#nameidpolicy)voor een aanmelding voor meer informatie.

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>De app verwacht een andere handtekeningmethode voor het SAML-antwoord

Als u wilt wijzigen welke onderdelen van het SAML-token digitaal zijn ondertekend door Azure AD, volgt u deze stappen:

1. Open het [Azure Portal](https://portal.azure.com/) meld u aan als globale beheerder of co-beheerder.

2. Selecteer **Alle services** bovenaan het navigatiedeelvenster aan de linkerkant om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het zoekvak en selecteer vervolgens **Azure Active Directory**.

4. Selecteer **Bedrijfstoepassingen** in het navigatiedeelvenster van Azure AD.

5. Selecteer **Alle toepassingen om** een lijst met uw apps weer te geven.

   > [!NOTE]
   > Als u de toepassing die u wilt niet ziet, gebruikt u het **besturingselement Filter** bovenaan de **lijst Alle toepassingen.** Stel de **optie** Tonen in op Alle toepassingen.

6. Selecteer de toepassing die u wilt configureren voor een aanmelding.

7. Nadat de toepassing is geladen, **selecteert u Een aanmelding** in het navigatiedeelvenster.

8. Selecteer **onder SAML-handtekeningcertificaat** de **optie Geavanceerde instellingen voor certificaat ondertekenen tonen.**

9. Selecteer de **ondertekeningsoptie** die de app verwacht uit een van de volgende opties:

   * **SAML-antwoord ondertekenen**
   * **SAML-antwoord en -bewering ondertekenen**
   * **SAML-bewering ondertekenen**

   De volgende keer dat de gebruiker zich bij de app meldt, ondertekent Azure AD het deel van het SAML-antwoord dat u hebt geselecteerd.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>De app verwacht het SHA-1-ondertekeningsalgoritme

Standaard ondertekent Azure AD het SAML-token met behulp van het veiligste algoritme. U wordt aangeraden het ondertekeningsalgoritme niet te wijzigen *in SHA-1, tenzij sha-1* is vereist voor de app.

Volg deze stappen om het ondertekeningsalgoritme te wijzigen:

1. Open het [Azure Portal](https://portal.azure.com/) meld u aan als globale beheerder of co-beheerder.

2. Selecteer **Alle services** bovenaan het navigatiedeelvenster aan de linkerkant om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het zoekvak filter en selecteer **Azure Active Directory**.

4. Selecteer **Bedrijfstoepassingen** in het navigatiedeelvenster van Azure AD.

5. Selecteer **Alle toepassingen om** een lijst met uw toepassingen te bekijken.

   > [!NOTE]
   > Als u de toepassing die u wilt niet ziet, gebruikt u het **besturingselement Filter** bovenaan de **lijst Alle toepassingen.** Stel de **optie** Tonen in op Alle toepassingen.

6. Selecteer de app die u wilt configureren voor een aanmelding.

7. Nadat de app is geladen, **selecteert u Een aanmelding** in het navigatiedeelvenster aan de linkerkant van de app.

8. Selecteer **onder SAML-handtekeningcertificaat** de **optie Geavanceerde instellingen voor certificaat ondertekenen tonen.**

9. Selecteer **SHA-1** als het **ondertekeningsalgoritme.**

   De volgende keer dat de gebruiker zich bij de app meldt, ondertekent Azure AD het SAML-token met behulp van het SHA-1-algoritme.

## <a name="next-steps"></a>Volgende stappen
[Fouten opsporen in op SAML gebaseerde een aanmelding bij toepassingen in Azure AD](./debug-saml-sso-issues.md).