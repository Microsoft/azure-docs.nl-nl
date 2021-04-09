---
title: Problemen oplossen met eenmalige aanmelding op basis van SAML in Azure Active Directory
description: Problemen oplossen met een Azure AD-app die is geconfigureerd voor eenmalige aanmelding op basis van SAML.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: b169616042892c379196dd1d38c2343704aa1030
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257523"
---
# <a name="troubleshoot-saml-based-single-sign-on-in-azure-active-directory"></a>Problemen oplossen met eenmalige aanmelding op basis van SAML in Azure Active Directory
Als er een probleem optreedt bij het configureren van een toepassing. Controleer of u alle stappen in de zelf studie voor de toepassing hebt gevolgd. In de configuratie van de toepassing hebt u inline-documentatie over het configureren van de toepassing. U kunt ook toegang krijgen tot de [lijst met zelf studies over het integreren van SaaS-apps met Azure Active Directory](../saas-apps/tutorial-list.md) voor een gedetailleerde stapsgewijze uitleg.

## <a name="cant-add-another-instance-of-the-application"></a>Kan geen ander exemplaar van de toepassing toevoegen
Als u een tweede exemplaar van een toepassing wilt toevoegen, moet u het volgende kunnen doen:
-   Configureer een unieke id voor het tweede exemplaar. U kunt niet dezelfde id configureren die wordt gebruikt voor het eerste exemplaar.
-   Configureer een ander certificaat dan dat voor het eerste exemplaar.

Als de toepassing geen van de bovenstaande voor keuren ondersteunt. Daarna kunt u een tweede exemplaar niet configureren.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>De id of de antwoord-URL kan niet worden toegevoegd
Als u de id of de antwoord-URL niet kunt configureren, bevestigt u dat de id-en antwoord-URL-waarden overeenkomen met de patronen die vooraf zijn geconfigureerd voor de toepassing.

Als u wilt weten welke patronen vooraf zijn geconfigureerd voor de toepassing:
1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.** Gaat u naar stap 7. Als u zich al in de Blade toepassings configuratie op Azure AD bevindt.
2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.
3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.
4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.
5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.
   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**
6. Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren.
7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.
8. Selecteer **op SAML gebaseerde aanmelding** in de **modus** vervolg keuzelijst.
9. Ga naar het tekstvak **id** of **antwoord-URL** , onder de **sectie domein en url's.**
10. Er zijn drie manieren om de ondersteunde patronen voor de toepassing te kennen:
    * In het tekstvak ziet u de ondersteunde patroon (en) als tijdelijke aanduiding *voor beeld:* <https://contoso.com> .
    * Als het patroon niet wordt ondersteund, ziet u een rood uitroep teken wanneer u probeert de waarde in het tekstvak in te voeren. Als u de muis aanwijzer boven het rode uitroep teken houdt, ziet u de ondersteunde patronen.
    * In de zelf studie voor de toepassing kunt u ook informatie over de ondersteunde patronen ophalen. Onder de sectie **eenmalige aanmelding voor Azure AD configureren** . Ga naar de stap voor het configureren van de waarden in de sectie **domein en url's** .

Als de waarden niet overeenkomen met de patronen die vooraf zijn geconfigureerd in azure AD. U kunt:
-   Werk samen met de leverancier van de toepassing om waarden op te halen die overeenkomen met het patroon dat vooraf is geconfigureerd in azure AD
-   U kunt ook contact opnemen met het Azure AD-team <aadapprequest@microsoft.com> of een opmerking in de zelf studie laten staan om de update van de ondersteunde patronen voor de toepassing aan te vragen

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Waar kan ik de EntityID-indeling (gebruikers-id) instellen
U kunt de EntityID-indeling (gebruikers-id) niet selecteren die door Azure AD wordt verzonden naar de toepassing in het antwoord na de verificatie van de gebruiker.

Azure AD Selecteer de indeling voor het NameID-kenmerk (gebruikers-id) op basis van de geselecteerde waarde of de indeling die door de toepassing is aangevraagd in het SAML-AuthRequest. Ga voor meer informatie naar het artikel [Single Sign-On SAML-protocol](../develop/single-sign-on-saml-protocol.md#authnrequest) in de sectie NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>De Azure AD-meta gegevens zijn niet gevonden voor het volt ooien van de configuratie met de toepassing
Voer de volgende stappen uit om de meta gegevens of het certificaat van de toepassing te downloaden uit Azure AD:
1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**
2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.
3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.
4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.
5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.
   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**
6. Selecteer de toepassing waarvoor u eenmalige aanmelding hebt geconfigureerd.
7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.
8. Ga naar de sectie **SAML-handtekening certificaat** en klik vervolgens op kolom waarde **downloaden** . Afhankelijk van wat de toepassing nodig heeft om eenmalige aanmelding te configureren, ziet u de optie voor het downloaden van de meta gegevens-XML of het certificaat.

Azure AD biedt geen URL om de meta gegevens op te halen. De meta gegevens kunnen alleen worden opgehaald als een XML-bestand.

## <a name="customize-saml-claims-sent-to-an-application"></a>SAML-claims aanpassen die worden verzonden naar een toepassing
Zie [claim toewijzing in azure Active Directory](../develop/active-directory-claims-mapping.md) voor meer informatie over het aanpassen van de SAML-kenmerk claims die naar uw toepassing worden verzonden.

## <a name="next-steps"></a>Volgende stappen
* [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)