---
title: Wat is Privileged Identity Management? - Azure AD | Microsoft Docs
description: Biedt een overzicht van Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 03/19/2021
ms.author: curtand
ms.custom: pim ; azuread-video-2020
ms.collection: M365-identity-device-management
ms.openlocfilehash: d42611705b7756ed2fd0c7a488339f0f638fb5b4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802162"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Wat is Azure AD Privileged Identity Management?

 Privileged Identity Management (PIM) is een service in Azure Active Directory (Azure AD) waarmee u toegang tot belangrijke resources in uw organisatie kunt beheren, controleren en bewaken. Deze resources omvatten resources in Azure AD, Azure en andere Microsoft-onlineservices, zoals Microsoft 365 of Microsoft Intune. In de volgende video kunt u kennismaken met belangrijke PIM-concepten en -functies.
<br><br>

> [!VIDEO https://www.youtube.com/embed/f-0K7mRUPpQ]

## <a name="reasons-to-use"></a>Redenen voor gebruik

Organisaties willen het aantal mensen dat toegang heeft tot beveiligde gegevens of resources beperken, om zo de kans te verkleinen dat een kwaadwillende gebruiker toegang verkrijgt of een geautoriseerde gebruiker per ongeluk een gevoelige resource wijzigt. Gebruikers moeten echter wel bevoorrechte bewerkingen kunnen uitvoeren in Azure AD, Azure, Microsoft 365 of SaaS-apps. Organisaties kunnen gebruikers daarom bevoorrechte JIT-toegang (Just-In-Time) geven tot Azure-resources en Azure AD. Het is belangrijk dat er toezicht is op wat gebruikers met hun beheerdersbevoegdheden doen.

## <a name="what-does-it-do"></a>Wat doet het?

Privileged Identity Management biedt op tijd en goedkeuring gebaseerde rolactiveringen om de risico's van buitensporige, onnodige of verkeerd gebruikte toegangsmachtigingen te beperken voor resources die u belangrijk vindt. Hier volgen enkele van de belangrijkste functies van Privileged Identity Management:

- Bevoorrechte **JIT**-toegang (Just-In-Time) bieden aan Azure Active Directory- en Azure-resources
- **Tijdsgebonden** toegang bieden aan resources met behulp van begin- en einddatums
- **Goedkeuring** vereisen om bevoorrechte rollen te activeren
- **Meervoudige verificatie** afdwingen om een rol te activeren
- Gebruikmaken van **redenen** om te begrijpen waarom gebruikers activeren
- **Meldingen** ontvangen wanneer bevoorrechte rollen zijn geactiveerd
- **Toegangsbeoordelingen** uitvoeren om te controleren of gebruikers rollen nog steeds nodig hebben
- **Controlegeschiedenis** downloaden voor interne of externe controle

## <a name="what-can-i-do-with-it"></a>Wat kan ik ermee doen?

Zodra u Privileged Identity Management hebt ingesteld, ziet u de opties **Taken**, **Beheren** en **Activiteit** in het navigatiemenu links. Als beheerder kunt u kiezen uit opties zoals het beheren van **Azure AD-rollen**, het beheren van **Azure-resource**-rollen of groepen met uitgebreide toegang. Wanneer u hebt gekozen wat u wilt beheren, ziet u de juiste set opties voor die optie.

![Schermopname van Privileged Identity Management in Azure Portal](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>Wie kan wat doen?

Voor Azure AD-rollen in Privileged Identity Management kunnen alleen gebruikers die een beheerder voor bevoorrechte rollen of globale beheerder zijn, toewijzingen voor andere beheerders beheren. U kunt [andere beheerders toegang verlenen voor het beheren van Privileged Identity Management](pim-how-to-give-access-to-pim.md). Globale beheerders, beveiligingsbeheerders, globale lezers en beveiligingslezers kunnen ook toewijzingen aan Azure AD-rollen in Privileged Identity Management weergeven.

Voor rollen voor Azure-resources in Privileged Identity Management geldt dat alleen een abonnementsbeheerder, een resource-eigenaar of een beheerder voor gebruikerstoegang tot resources toewijzingen voor andere beheerders kan beheren. Gebruikers die beheerder voor bevoorrechte rollen, beveiligingsbeheerder of beveiligingslezer zijn, kunnen niet standaard toewijzingen aan rollen voor Azure-resources in Privileged Identity Management weergeven.

## <a name="scenarios"></a>Scenario's

Privileged Identity Management ondersteunt de volgende scenario's:

### <a name="privileged-role-administrator-permissions"></a>Machtigingen van beheerders voor bevoorrechte rollen

- Goedkeuring voor specifieke rollen inschakelen
- Gebruikers of groepen toewijzen als fiatteur om aanvragen goed te keuren
- De geschiedenis van aanvragen en goedkeuringen bekijken voor alle bevoorrechte rollen

### <a name="approver-permissions"></a>Machtigingen van fiatteurs

- Goedkeuringen in behandeling (aanvragen) bekijken
- Aanvragen voor rolverhoging goedkeuren of afwijzen (afzonderlijk en bulksgewijs)
- De reden voor de goedkeuring of afwijzing opgeven

### <a name="eligible-role-user-permissions"></a>Gebruikersmachtigingen van in aanmerking komende rollen

- Activering van een rol waarvoor goedkeuring nodig is, aanvragen
- De status van uw aanvraag voor activeren bekijken
- Uw taak voltooien in Azure AD als de activering is goedgekeurd

## <a name="terminology"></a>Terminologie

Bekijk de volgende terminologie voor een beter begrip van Privileged Identity Management en de bijbehorende documentatie.

| Term of concept | Roltoewijzingscategorie | Beschrijving |
| --- | --- | --- |
| in aanmerking komend | Type | Een roltoewijzing die vereist dat een gebruiker een of meer acties uitvoert om de rol te kunnen gebruiken. Als een gebruiker in aanmerking komt voor een rol, betekent dit dat de gebruiker de rol kan activeren wanneer deze nodig is om bevoegde taken uit te voeren. Er is geen verschil in de toegang voor iemand met een permanente roltoewijzing ten opzichte van iemand die in aanmerking komt voor een roltoewijzing. Het enige verschil is dat sommige gebruikers niet voortdurend toegang nodig hebben. |
| actief | Type | Een roltoewijzing die niet vereist dat een gebruiker een actie uitvoert om de rol te kunnen gebruiken. Gebruikers die zijn toegewezen als actief zijn in het bezit van de bevoegdheden die zijn toegewezen aan de rol. |
| activeren |  | Het proces van het uitvoeren van een of meer acties om de rol te kunnen gebruiken waarvoor de gebruiker in aanmerking komt. Acties kunnen bijvoorbeeld een meervoudige verificatiecontrole, het opgeven van een zakelijke reden of het vragen om toestemming bij aangewezen fiatteurs zijn. |
| toegewezen | Status | Een gebruiker met een actieve roltoewijzing. |
| geactiveerd | Status | Een gebruiker die in aanmerking komt voor een roltoewijzing, de acties voor het activeren van de rol heeft uitgevoerd en nu actief is.  Wanneer de rol is geactiveerd, kan de gebruiker deze gebruiken gedurende een vooraf geconfigureerde periode voordat ze de rol opnieuw moeten activeren. |
| permanent in aanmerking komend | Duur | Een roltoewijzing waarbij een gebruiker altijd in aanmerking komt om de rol te activeren. |
| permanent actief | Duur | Een roltoewijzing waarbij een gebruiker de rol altijd kan gebruiken zonder acties te hoeven uitvoeren. |
| tijdgebonden tijd in aanmerking komend | Duur | Een roltoewijzing waarbij een gebruiker in aanmerking komt voor het activeren van de rol alleen binnen de begin-en eind datum. |
| tijd gebonden actief | Duur | Een roltoewijzing waarbij een gebruiker de rol alleen mag gebruiken binnen de begin-en eind datum. |
| Just-in-time-toegang (JIT) |  | Een model waarbij gebruikers tijdelijke machtigingen ontvangen om bepaalde taken uit te mogen voeren, waardoor kwaadwillende of onbevoegde gebruikers geen toegang kunnen krijgen na het verlopen van deze machtigingen. Toegang wordt alleen verleend wanneer gebruikers deze nodig hebben. |
| Principe van toegang met minimale bevoegdheden |  | Een aanbevolen beveiligingsprocedure waarbij alle gebruikers enkel de minimale bevoegdheden krijgt toegewezen die nodig zijn om de taken uit te voeren waarvoor ze bevoegd zijn. Met deze procedure wordt het aantal globale beheerders tot het minimum beperkt en worden er specifieke beheerdersrollen gebruikt voor bepaalde scenario's. |

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Zie [Licentievereisten voor het gebruik van Privileged Identity Management](subscription-requirements.md) voor informatie over licenties voor gebruikers.

## <a name="next-steps"></a>Volgende stappen

- [Licentievereisten voor het gebruik van Privileged Identity Management](subscription-requirements.md)
- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](../roles/security-planning.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Privileged Identity Management implementeren](pim-deployment-plan.md)
