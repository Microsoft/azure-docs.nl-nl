---
title: De ' aangemeld blijven? ' configureren vragen om Azure Active Directory accounts
description: Meer informatie over aangemeld blijven (KMSI), waarin wordt weer gegeven dat u bent aangemeld? prompt, hoe u deze kunt configureren in de Azure Active Directory-Portal en hoe u aanmeldings problemen kunt oplossen.
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed6bc43dfc15abf2bdf9f38a5de2240d348d6fb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89320253"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>De ' aangemeld blijven? ' configureren vragen om Azure AD-accounts

Aangemeld blijven (KMSI) Hiermee wordt de vraag aanmelden **blijven** weer gegeven nadat een gebruiker zich heeft aangemeld. Als een gebruiker **Ja** op deze vraag antwoordt, biedt de keep me aangemeld-service een permanent [vernieuwings token](../develop/developer-glossary.md#refresh-token). Voor federatieve tenants wordt de prompt weer gegeven nadat de gebruiker is geverifieerd met de federatieve identiteits service.

In het volgende diagram ziet u de aanmeldings stroom van de gebruiker voor een beheerde Tenant en federatieve Tenant en de nieuwe prompt aangemeld blijven. Deze stroom bevat slimme logica zodat de optie **aangemeld blijven?** niet wordt weer gegeven als het machine learning systeem een aanmelding met een hoog risico of een aanmelding van een gedeeld apparaat detecteert.

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="Diagram van de aanmeldings stroom van de gebruiker voor een beheerde versus federatieve Tenant":::

> [!NOTE]
> Voor het configureren van de optie aangemeld aanmelden moet u Azure Active Directory (Azure AD) Premium 1, Premium 2 of Basic-edities gebruiken of een Microsoft 365-licentie hebben. Zie voor meer informatie over licenties en edities [Aanmelden registreren voor Azure AD Premium](active-directory-get-started-premium.md).<br><br>Azure AD Premium-en Basic-edities zijn beschikbaar voor klanten in China met behulp van het wereld wijde exemplaar van Azure AD. De Azure AD Premium en Basic-edities worden momenteel niet ondersteund in de Azure-service die wordt beheerd door 21Vianet in China. Neem contact met ons op met het [Azure AD-forum](https://feedback.azure.com/forums/169401-azure-active-directory/)voor meer informatie.

## <a name="configure-kmsi"></a>KMSI configureren

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met het account van een globale administrator voor de map.
1. Selecteer **Azure Active Directory**, selecteer **bedrijfs huisstijl** en selecteer vervolgens **configureren**.
1. Zoek in de sectie **Geavanceerde instellingen** de **optie weer geven om de instelling aangemeld te blijven** .

   Met deze instelling kunt u kiezen of uw gebruikers aangemeld blijven bij Azure AD totdat ze zich expliciet hebben afgemeld.
   * Als u **Nee** kiest, wordt de optie **aangemeld blijven** verborgen nadat de gebruiker zich heeft aangemeld en moet de gebruiker zich aanmelden telkens wanneer de browser wordt gesloten en opnieuw wordt geopend.
   * Als u **Ja** kiest, wordt de optie **aangemeld blijven?** weer gegeven aan de gebruiker.

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="Scherm afbeelding toont de optie weer geven om aangemeld te blijven":::

## <a name="troubleshoot-sign-in-issues"></a>Problemen met aanmelden oplossen

Als een gebruiker niet op de vraag **aangemeld blijven?** wordt weer gegeven, zoals in het volgende diagram, maar de aanmeldings poging wordt afgebroken, ziet u een aanmeldings logboek vermelding die de interrupt aanduidt.

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="Ziet u de aangemeld blijven? verschijnt":::

Meer informatie over de aanmeldings fout vindt u in het voor beeld.

* **Fout code voor aanmelden**: 50140
* **Reden van fout**: deze fout is opgetreden vanwege een onderbreking bij het aanmelden bij de aanmelding van de gebruiker.

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="Voor beeld van een aanmeldings logboek vermelding met de hand aangemeld bij interrupt":::

U kunt voor komen dat gebruikers de interrupt zien door de **optie weer geven in te stellen op** **Nee** in de geavanceerde huisstijl instellingen. Hiermee wordt de KMSI-prompt voor alle gebruikers in uw Azure AD-adres lijst uitgeschakeld.

U kunt ook de permanente browser sessie besturings elementen in voorwaardelijke toegang gebruiken om te voor komen dat gebruikers de KMSI-prompt zien. Met deze optie kunt u de KMSI-prompt uitschakelen voor een geselecteerde groep gebruikers (zoals de globale beheerders) zonder dat dit van invloed is op het aanmeldings gedrag voor de resterende gebruikers in de Directory. Zie de [aanmeldings frequentie van gebruikers](../conditional-access/howto-conditional-access-session-lifetime.md)voor meer informatie. 

Om ervoor te zorgen dat de KMSI prompt alleen wordt weer gegeven wanneer deze de gebruiker kan profiteren, wordt de KMSI-prompt opzettelijk niet weer gegeven in de volgende scenario's:

* Gebruiker is aangemeld via naadloze SSO en geïntegreerde Windows-authenticatie (IWA)
* De gebruiker is aangemeld via Active Directory Federation Services en IWA
* De gebruiker is een gast in de Tenant
* De risico Score van de gebruiker is hoog
* Aanmelden treedt op tijdens de overdracht van de gebruiker of beheerder
* Permanente browser sessie beheer is geconfigureerd in een beleid voor voorwaardelijke toegang

## <a name="next-steps"></a>Volgende stappen

Meer informatie over andere instellingen die van invloed zijn op de time-out van de aanmeldings sessie:

* Microsoft 365: [time-out voor niet-actieve sessie](/sharepoint/sign-out-inactive-users)
* Voorwaardelijke toegang van Azure AD- [aanmeldings frequentie voor gebruikers](../conditional-access/howto-conditional-access-session-lifetime.md)
* Azure Portal – [time-out voor inactiviteit op Directory-niveau](../../azure-portal/set-preferences.md#change-the-directory-timeout-setting-admin)