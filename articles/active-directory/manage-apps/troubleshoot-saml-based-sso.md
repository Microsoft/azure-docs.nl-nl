---
title: Problemen oplossen met eenmalige aanmelding op basis van SAML in Azure Active Directory
description: Problemen oplossen met een Azure AD-app die is geconfigureerd voor een aanmelding op basis van SAML.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.openlocfilehash: c4a4f7bfad4254e7c3fa5851e62532ed427ced8b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376423"
---
# <a name="troubleshoot-saml-based-single-sign-on-in-azure-active-directory"></a>Problemen oplossen met eenmalige aanmelding op basis van SAML in Azure Active Directory
Als u een probleem ondervindt bij het configureren van een toepassing. Controleer of u alle stappen in de zelfstudie voor de toepassing hebt gevolgd. In de configuratie van de toepassing hebt u inline documentatie over het configureren van de toepassing. U hebt ook toegang tot de lijst met zelfstudies over het integreren van [SaaS-apps](../saas-apps/tutorial-list.md) met Azure Active Directory voor een gedetailleerde stapsgewijs richtlijnen.

## <a name="cant-add-another-instance-of-the-application"></a>Kan geen ander exemplaar van de toepassing toevoegen
Als u een tweede exemplaar van een toepassing wilt toevoegen, moet u het volgende kunnen doen:
-   Configureer een unieke id voor het tweede exemplaar. U kunt niet dezelfde id configureren die wordt gebruikt voor het eerste exemplaar.
-   Configureer een ander certificaat dan het certificaat dat wordt gebruikt voor het eerste exemplaar.

Als de toepassing geen van de bovenstaande ondersteunt. Vervolgens kunt u geen tweede instantie configureren.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Kan de id of antwoord-URL niet toevoegen
Als u de id of antwoord-URL niet kunt configureren, controleert u of de waarden voor Id en Antwoord-URL overeenkomen met de patronen die vooraf zijn geconfigureerd voor de toepassing.

Als u wilt weten welke patronen vooraf zijn geconfigureerd voor de toepassing:
1. Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder** **of co-beheerder.** Ga naar stap 7. Als u zich al op de blade toepassingsconfiguratie in Azure AD.
2. Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu linksboven.
3. Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.
4. klik **op Bedrijfstoepassingen** in Azure Active Directory navigatiemenu aan de linkerkant.
5. klik **op Alle toepassingen** om een lijst met al uw toepassingen te bekijken.
   * Als u de toepassing die u hier wilt zien niet ziet,  gebruikt u het **besturingselement Filter** bovenaan de lijst Alle toepassingen en stelt u de **optie** Tonen in op **Alle toepassingen.**
6. Selecteer de toepassing die u wilt configureren voor een aanmelding.
7. Zodra de toepassing is geladen, klikt u op **Een aanmelding** in het linkernavigatiemenu van de toepassing.
8. Selecteer **Aanmelding op basis van SAML in** de **vervolgkeuzekeuze** selecteren.
9. Ga naar het **tekstvak Id** **of Antwoord-URL** onder de **sectie Domein en URL's.**
10. Er zijn drie manieren om de ondersteunde patronen voor de toepassing te kennen:
    * In het tekstvak ziet u de ondersteunde patronen als tijdelijke aanduiding *Voorbeeld:* <https://contoso.com> .
    * Als het patroon niet wordt ondersteund, ziet u een rood uitroepteken wanneer u de waarde in het tekstvak probeert in te voeren. Als u de muisaanwijzer boven het rode uitroepteken beweegt, ziet u de ondersteunde patronen.
    * In de zelfstudie voor de toepassing kunt u ook informatie krijgen over de ondersteunde patronen. Onder de **sectie Een aanmelding van Azure AD configureren.** Ga naar de stap voor het configureren van de waarden in de **sectie Domein en URL's.**

Als de waarden niet overeenkomen met de patronen die vooraf zijn geconfigureerd in Azure AD. U kunt:
-   Werk samen met de leverancier van de toepassing om waarden op te halen die overeenkomen met het patroon dat vooraf is geconfigureerd in Azure AD
-   U kunt ook contact opnemen met het Azure AD-team op of een opmerking plaatsen in de zelfstudie om de update van de ondersteunde patronen voor <aadapprequest@microsoft.com> de toepassing aan te vragen

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Waar kan ik de EntityID-indeling (gebruikers-id) instellen?
U kunt na gebruikersverificatie niet de EntityID-indeling (gebruikers-id) selecteren die Azure AD naar de toepassing verzendt in het antwoord.

Azure AD selecteert de indeling voor het kenmerk NameID (gebruikers-id) op basis van de geselecteerde waarde of de indeling die is aangevraagd door de toepassing in de SAML-authRequest. Ga voor meer informatie naar het [artikel Single Sign-On SAML-protocol](../develop/single-sign-on-saml-protocol.md#authnrequest) in de sectie NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Kan de Azure AD-metagegevens niet vinden om de configuratie met de toepassing te voltooien
Volg deze stappen om de metagegevens of het certificaat van de toepassing te downloaden van Azure AD:
1. Open het [**Azure Portal**](https://portal.azure.com/) meld u aan als **globale beheerder** **of co-beheerder.**
2. Open de **Azure Active Directory door** te klikken op **Alle services** bovenaan het navigatiemenu linksboven.
3. Typ **'Azure Active Directory'** in het filterzoekvak en selecteer het **Azure Active Directory** item.
4. klik **op Bedrijfstoepassingen** in Azure Active Directory navigatiemenu aan de linkerkant.
5. klik **op Alle toepassingen** om een lijst met al uw toepassingen te bekijken.
   * Als u de toepassing die u hier wilt zien niet ziet,  gebruikt u het **besturingselement Filter** bovenaan de lijst Alle toepassingen en stelt u de **optie** Tonen in op **Alle toepassingen.**
6. Selecteer de toepassing die u hebt geconfigureerd voor een aanmelding.
7. Zodra de toepassing is geladen, **klikt** u in het navigatiemenu aan de linkerkant van de toepassing op Een aanmelding.
8. Ga naar **de sectie SAML-handtekeningcertificaat** en klik vervolgens op **Kolomwaarde** downloaden. Afhankelijk van wat de toepassing vereist voor het configureren van een enkele aanmelding, ziet u de optie om het XML-bestand met metagegevens of het certificaat te downloaden.

Azure AD biedt geen URL om de metagegevens op te halen. De metagegevens kunnen alleen worden opgehaald als een XML-bestand.

## <a name="customize-saml-claims-sent-to-an-application"></a>SAML-claims aanpassen die worden verzonden naar een toepassing
Zie Claimtoewijzing in Azure Active Directory voor meer informatie over het aanpassen van de SAML-kenmerkclaims die naar uw [toepassing](../develop/active-directory-claims-mapping.md) worden verzonden.

## <a name="next-steps"></a>Volgende stappen
* [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)