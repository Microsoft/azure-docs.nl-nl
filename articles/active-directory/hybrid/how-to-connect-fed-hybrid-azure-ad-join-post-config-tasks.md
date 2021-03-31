---
title: 'Azure AD Connect: hybride Azure AD-deelname post configuratie taken | Microsoft Docs'
description: Dit document bevat informatie over de configuratie taken die nodig zijn om de hybride Azure AD-deelname te volt ooien
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da5cefbacbd3851d2609a687c1948d9bcba5ffae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88612466"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Taken na configuratie voor hybride Azure AD-koppeling

Nadat u Azure AD Connect hebt uitgevoerd om uw organisatie te configureren voor hybride Azure AD-deelname, zijn er enkele extra stappen die u moet volt ooien om de installatie te volt ooien.  Voer alleen de stappen uit die van toepassing zijn op uw apparaten.

## <a name="1-configure-controlled-rollout-optional"></a>1. beheerde implementatie configureren (optioneel)
Alle apparaten die lid zijn van een domein met Windows 10 en Windows Server 2016 worden automatisch geregistreerd bij Azure AD zodra alle configuratie stappen zijn voltooid. Als u de voor keur geeft aan een beheerde implementatie in plaats van deze automatische registratie, kunt u groeps beleid gebruiken om de automatische implementatie selectief in of uit te scha kelen.  Dit groeps beleid moet worden ingesteld voordat de andere configuratie stappen worden gestart:
* Maak een groeps beleidsobject in uw Active Directory.
* Noem het (ex-Hybrid Azure AD-deelname).
* Bewerken en ga naar: computer configuratie > beleid > Beheersjablonen > Windows-onderdelen > apparaatregistratie.

>[!NOTE]
>Voor 2012R2 zijn de beleids instellingen ingesteld op **computer configuratie > beleid > Beheersjablonen > Windows-onderdelen > Workplace join > automatisch werk plek toevoegen aan client computers**

* Schakel deze instelling in: Registreer computers die lid zijn van een domein als apparaten.
* Toep assen en klik op OK.
* Koppel het groeps beleidsobject aan de gewenste locatie (organisatie-eenheid, beveiligings groep of het domein voor alle apparaten).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. netwerk met apparaatregistratie-eind punten configureren
Zorg ervoor dat de volgende Url's toegankelijk zijn vanaf computers in het netwerk van uw organisatie voor registratie bij Azure AD:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com` 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. WPAD implementeren voor Windows 10-apparaten
Als uw organisatie toegang heeft tot internet via een uitgaande proxy, implementeert u Web Proxy Auto-Discovery (WPAD) zodat Windows 10-computers zich kunnen registreren bij Azure AD.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Configureer het SCP in een forest dat niet is geconfigureerd door Azure AD Connect 

Het SCP (Service Connection Point) bevat uw Azure AD-Tenant gegevens die door uw apparaten worden gebruikt voor automatische registratie.  Voer het Power shell-script ConfigureSCP.ps1 uit dat u hebt gedownload van Azure AD Connect.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Configureer een Federation-service die niet is geconfigureerd door Azure AD Connect

Als uw organisatie een Federation-service gebruikt om u aan te melden bij Azure AD, moeten de claim regels in uw Azure AD Relying Party-vertrouwen apparaat-authenticatie toestaan. Als u Federatie gebruikt met AD FS, gaat u naar [AD FS Help](https://aka.ms/aadrptclaimrules) voor het genereren van de claim regels. Als u een Federatie oplossing die niet van micro soft is, neemt u contact op met de provider voor hulp.  

>[!NOTE]
>Als u Windows-apparaten op een lager niveau hebt, moet de service ondersteuning bieden voor de authenticatie van de wiaormultiauthn en claims bij het ontvangen van aanvragen aan de Azure AD-vertrouwens relatie. In AD FS moet u een regel voor uitgifte transformatie toevoegen die door de verificatie methode wordt door gegeven.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Schakel Azure AD naadloze SSO in voor Windows-apparaten op lagere niveaus

Als uw organisatie gebruikmaakt van hash-synchronisatie van wacht woorden of Pass-Through-verificatie om zich aan te melden bij Azure AD, schakelt u de naadloze SSO van Azure AD in met die aanmeldings methode om Windows-apparaten op lagere niveaus te verifiëren:  https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso . 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Stel het Azure AD-beleid voor Windows-apparaten op lagere niveaus in

Als u Windows-apparaten op een lager niveau wilt registreren, moet u ervoor zorgen dat gebruikers met het Azure AD-beleid apparaten kunnen registreren. 

* Meld u aan bij uw account in de Azure Portal.
* Ga naar: Azure Active Directory > apparaten > Apparaatinstellingen
* Stel ' gebruikers kunnen hun apparaten registreren bij Azure AD '.
* Klik op Opslaan

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Azure AD-eind punt toevoegen aan Windows-apparaten op lager niveau

Voeg het Azure AD-eind punt voor verificatie van apparaten toe aan de lokale intranet zones op uw Windows-apparaten op het lagere niveau om certificaat prompts te voor komen bij het verifiëren van de apparaten: `https://device.login.microsoftonline.com` 

Als u [naadloze SSO](how-to-connect-sso.md)gebruikt, schakelt u ook de optie updates op status balk toestaan via script in op die zone en voegt u het volgende eind punt toe: `https://autologon.microsoftazuread-sso.com` 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Installeer micro soft-Workplace Join op Windows-apparaten op een lager niveau

Dit installatie programma maakt een geplande taak op het apparaatsysteem dat wordt uitgevoerd in de context van de gebruiker. De taak wordt geactiveerd wanneer de gebruiker zich aanmeldt bij Windows. De taak wordt na verificatie met behulp van geïntegreerde Windows-verificatie op de achtergrond toegevoegd aan het apparaat met Azure AD met de referenties van de gebruiker. Het Download centrum bevindt zich op https://www.microsoft.com/download/details.aspx?id=53554 . 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. groeps beleid zo configureren dat apparaatregistratie is toegestaan

Zie [gecontroleerde validatie van hybride Azure AD-deelname](../devices/hybrid-azuread-join-control.md)voor meer informatie over het toestaan van hybride Azure AD-deelname voor afzonderlijke apparaten.

## <a name="next-steps"></a>Volgende stappen
[Write-back van apparaat configureren](how-to-connect-device-writeback.md)
