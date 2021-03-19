---
title: 'Voorwaardelijke toegang: compatibele apparaten vereisen-Azure Active Directory'
description: Een aangepast beleid voor voorwaardelijke toegang maken om compatibele apparaten te vereisen
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c98269f9851272e8caa9b26ae0c57ed13e9a99f2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89049125"
---
# <a name="conditional-access-require-compliant-devices"></a>Voorwaardelijke toegang: compatibele apparaten vereisen

Organisaties die Microsoft Intune hebben geïmplementeerd, kunnen de informatie gebruiken die wordt geretourneerd door hun apparaten om apparaten te identificeren die voldoen aan de nalevings vereisten, zoals:

* Een pincode vereisen om te ontgrendelen
* Apparaatversleuteling vereisen
* Een minimum-of maximum versie van het besturings systeem vereisen
* Vereisen dat een apparaat niet is opengebroken of geroot

Deze informatie over beleids naleving wordt doorgestuurd naar Azure AD, waarbij voorwaardelijke toegang kan besluiten om toegang tot resources toe te kennen of te blok keren. Meer informatie over nalevings beleid voor apparaten vindt u in het artikel, [regels instellen op apparaten om toegang tot resources in uw organisatie toe te staan met intune](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

De volgende stappen helpen u bij het maken van een beleid voor voorwaardelijke toegang om te vereisen dat apparaten die toegang hebben tot resources, worden gemarkeerd als compatibel met het intune-nalevings beleid van uw organisatie.

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen** selecteert u **gebruikers en groepen**
   1. Onder **insluiten** selecteert u **alle gebruikers**.
   1. Onder **uitsluiten** selecteert u **gebruikers en groepen** en kiest u de accounts voor nood toegang of het afbreek glas van uw organisatie. 
   1. Selecteer **Gereed**.
1. Onder **Cloud-apps of acties**  >  , selecteert u **alle Cloud-apps**.
   1. Als u specifieke toepassingen van uw beleid moet uitsluiten, kunt u ze kiezen op het tabblad **uitsluiten** onder **Selecteer uitgesloten Cloud-apps** en kiest u **selecteren**.
   1. Selecteer **Gereed**.
1. Selecteer onder **voor waarden**  >  **client-apps (preview-versie)**  >  **de client-apps waarop dit beleid van toepassing** is, geef alle geselecteerde standaard instellingen op en selecteer **gereed**.
1. Onder **toegangs beheer**  >  **toekennen** selecteert **u apparaat vereisen dat moet worden gemarkeerd als compatibel**.
   1. Kies **Selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid in te stellen.

> [!NOTE]
> U kunt uw nieuwe apparaten registreren bij intune, zelfs als u **vereisen dat apparaat moet worden gemarkeerd als compatibel** voor **alle gebruikers** en **alle Cloud-apps** met behulp van de bovenstaande stappen. **Vereisen dat het apparaat wordt gemarkeerd als compatibel** besturings element de registratie van intune wordt niet geblokkeerd. 

### <a name="known-behavior"></a>Bekend gedrag

In Windows 7, iOS, Android, macOS en sommige webbrowsers van derden identificeert het apparaat met behulp van een client certificaat dat is ingericht op het moment dat het apparaat wordt geregistreerd bij Azure AD. Wanneer een gebruiker zich voor het eerst aanmeldt via de browser, wordt de gebruiker gevraagd het certificaat te selecteren. De eind gebruiker moet dit certificaat selecteren om de browser te kunnen blijven gebruiken.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Effect bepalen met de modus alleen rapport-alleen voor voorwaardelijke toegang](howto-conditional-access-insights-reporting.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)

[Apparaatnalevingsbeleid werkt met Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
