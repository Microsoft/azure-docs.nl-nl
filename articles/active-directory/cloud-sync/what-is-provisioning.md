---
title: Wat is identiteitsinrichting met Azure AD? | Microsoft Docs
description: Geeft een overzicht van identiteitsinrichting.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1c85f2a6d58a5dbeae93b951cea812d6aa91326
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98614448"
---
# <a name="what-is-identity-provisioning"></a>Wat is identiteitsinrichting?

Vandaag de dag maken organisaties en bedrijven steeds meer gebruik van een mix van on-premises toepassingen en cloudtoepassingen.  Gebruikers hebben zowel on-premises als in de cloud toegang tot toepassingen nodig. Er moet een enkele identiteit zijn in deze verschillende toepassingen (on-premises en in de cloud).

Inrichting is het proces van het maken van een object op basis van bepaalde voorwaarden, waarbij het object up-to-date wordt gehouden en wordt verwijderd wanneer niet langer aan de voorwaarden wordt voldaan. Wanneer een nieuwe gebruiker bijvoorbeeld toetreedt tot uw organisatie, wordt deze gebruiker ingevoerd in het HR-systeem.  Op dat moment kan met inrichting een bijbehorend gebruikersaccount worden gemaakt in de cloud, Active Directory, en verschillende toepassingen waartoe de gebruiker toegang nodig heeft.  Dit stelt de gebruiker meteen de eerste dag al in staat om aan de slag te gaan, en de benodigde toepassingen en systemen te gebruiken. 

![Diagram waarin cloudinrichting met Azure Active Directory wordt weergegeven.](media/what-is-provisioning/cloud-1.png)

Met betrekking tot Azure Active Directory kan inrichting worden uitgesplitst in de volgende belangrijke scenario's.  

- **[Inrichting op basis van HR](#hr-driven-provisioning)**  
- **[App-inrichting](#app-provisioning)**  
- **[Directory-inrichting](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>Inrichting op basis van HR

![Diagram waarin HR-gerichte inrichting wordt weergegeven met behulp van Cloud HR, On-premises HR en Azure Active Directory.](media/what-is-provisioning/cloud-2.png)

Bij inrichting van HR in de cloud moeten objecten (gebruikers, rollen, groepen, enzovoort) worden gemaakt op basis van de informatie die aanwezig is in uw HR-systeem.  

Het meest voorkomende scenario is wanneer een nieuwe werknemer toetreedt tot uw bedrijf en deze wordt ingevoerd in het HR-systeem.  Als dit het geval is, wordt deze persoon ingericht in de cloud.  In dit geval Azure AD.  Inrichting vanuit HR kan voorkomen in de volgende scenario's. 

- **Nieuwe medewerkers aannemen**: wanneer een nieuwe werknemer wordt toegevoegd aan HR in de cloud, wordt automatisch een gebruikersaccount gemaakt in Active Directory, Azure Active Directory, en optioneel in Microsoft 365 en andere SaaS-toepassingen die worden ondersteund in Azure AD, met een terugschrijfbewerking van het e-mailadres naar HR in de cloud.
- **Updates van kenmerken en profielen van werknemers**: wanneer de record van een werknemer wordt bijgewerkt in de cloud (zoals naam, functie of manager), wordt het gebruikersaccount automatisch bijgewerkt in Active Directory, Azure Active Directory, en optioneel in Microsoft 365 en andere SaaS-toepassingen die worden ondersteund in Azure AD.
- **Ontslagen van werknemers**: wanneer een werknemer wordt verwijderd uit HR in de cloud, wordt het bijbehorende gebruikersaccount automatisch uitgeschakeld in Active Directory, Azure Active Directory, en optioneel in Office 365 en andere SaaS-toepassingen die worden ondersteund in Azure AD.
- **Werknemers weer in dienst nemen**: wanneer een werknemer opnieuw wordt toegevoegd in HR in de cloud, kan het oude gebruikersaccount automatisch opnieuw worden geactiveerd of ingericht (afhankelijk van wat uw voorkeur heeft) in Active Directory, Azure Active Directory, en optioneel in Microsoft 365 en andere SaaS-toepassingen die worden ondersteund in Azure AD.


## <a name="app-provisioning"></a>App-inrichting

![Diagram waarin de app-inrichting wordt weergegeven voor on-premises apps, niet-microsoft cloud-apps en Azure Active Directory.](media/what-is-provisioning/cloud-3.png)

In Azure AD (Active Directory) verwijst de term **[app-inrichting](../app-provisioning/user-provisioning.md)** naar het automatisch maken van gebruikersidentiteiten en -rollen in de cloud-toepassingen waartoe gebruikers toegang nodig hebben. Naast het maken van gebruikersidentiteiten omvat automatische inrichting het onderhoud en de verwijdering van gebruikersidentiteiten, zoals gewijzigde status of rollen. Veelvoorkomende scenario's zijn onder andere het inrichten van een Azure AD-gebruiker in toepassingen als [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Sales Force](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) en meer.

## <a name="directory-provisioning"></a>Directory-inrichting

![cloudinrichting](media/what-is-provisioning/cloud-4.png)

On-premises inrichting omvat het inrichten van on-premises bronnen (zoals Active Directory) naar Azure AD.  

Het meest voorkomende scenario is wanneer een gebruiker in AD (Active Directory) wordt ingericht in Azure AD.

Dit wordt gedaan via Azure AD Connect-synchronisatie, Azure AD Connect-cloudinrichting en Microsoft Identity Manager. 
 
## <a name="next-steps"></a>Volgende stappen 

- [Wat is Azure AD Connect--cloudsynchronisatie?](what-is-cloud-sync.md)
- [Cloudinrichting installeren](how-to-install.md)