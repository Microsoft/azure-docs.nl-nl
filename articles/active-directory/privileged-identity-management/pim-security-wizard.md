---
title: Azure AD-rollen detectie en inzichten (preview) in Privileged Identity Management voormalige beveiligings wizard-Azure Active Directory
description: Met detectie en inzichten (voorheen beveiligings wizard) kunt u permanente toewijzingen van Azure AD-rollen omzetten in just-in-time-toewijzingen met Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/01/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4662e9fb537a93cb87c554e319256e2eca40d2e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92372426"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>Detectie en inzichten (preview) voor Azure AD-rollen (voorheen beveiligings wizard)

Als u in uw Azure Active Directory (Azure AD)-organisatie met Privileged Identity Management (PIM) begint, kunt u de pagina **detectie en inzichten (preview)** gebruiken om aan de slag te gaan. Met deze functie kunt u zien wie is toegewezen aan bevoorrechte rollen in uw organisatie en hoe u PIM kunt gebruiken om snel permanente roltoewijzingen te wijzigen in just-in-time-toewijzingen. U kunt uw permanente geprivilegieerde roltoewijzingen weer geven of wijzigen in **detectie en inzichten (preview)**. Het is een analyse programma en een actie programma.

## <a name="discovery-and-insights-preview"></a>Detectie en inzichten (preview-versie)

Voordat uw organisatie begint met Privileged Identity Management, zijn alle roltoewijzingen permanent. Gebruikers zijn altijd in hun toegewezen rollen, zelfs wanneer ze hun bevoegdheden niet nodig hebben. Detectie en inzichten (preview), waarmee de voormalige beveiligings wizard wordt vervangen, toont u een lijst met geprivilegieerde rollen en het aantal gebruikers dat momenteel deel uitmaakt van deze rollen. U kunt de toewijzingen voor een rol weer geven om meer te weten te komen over de toegewezen gebruikers als een of meer hiervan niet bekend zijn.

: heavy_check_mark: **micro soft raadt** u aan twee afbreek glazen-accounts te blijven die permanent zijn toegewezen aan de rol van globale beheerder. Zorg ervoor dat deze accounts niet hetzelfde multi-factor Authentication-mechanisme vereisen als uw normale beheerders accounts om u aan te melden, zoals beschreven in [accounts voor nood toegang beheren in azure AD](../roles/security-emergency-access.md).

Zorg er ook voor dat de roltoewijzingen permanent blijven als een gebruiker een Microsoft-account heeft (met andere woorden, een account dat wordt gebruikt om u aan te melden bij micro soft-services zoals Skype of Outlook.com). Als u multi-factor Authentication nodig hebt voor een gebruiker met een Microsoft-account voor het activeren van een roltoewijzing, wordt de gebruiker vergrendeld.

## <a name="open-discovery-and-insights-preview"></a>Detectie en inzichten openen (preview-versie)

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Open **Azure AD privileged Identity Management**.

1. Selecteer **Azure AD-rollen** en selecteer vervolgens **detectie en inzichten (preview)**. Als u de pagina opent, wordt het detectie proces gestart om relevante roltoewijzingen te vinden.

    ![Azure AD-rollen-detectie-en inzichten pagina met de 3 opties](./media/pim-security-wizard/new-preview-link.png)

1. Selecteer **globale beheerders verminderen**.

    ![Scherm opname waarin de ' detectie en inzichten (preview) ' wordt weer gegeven met de actie ' minder algemene beheerders ' geselecteerd.](./media/pim-security-wizard/new-preview-page.png)

1. Bekijk de lijst met algemene toewijzingen voor beheerdersrol.

    ![Het deel venster globale beheerders beperken-rollen die alle globale beheerders weer geven](./media/pim-security-wizard/new-global-administrator-list.png)

1. Selecteer **volgende** om de gebruikers of groepen te selecteren die u in aanmerking wilt nemen, en selecteer vervolgens **in aanmerking komende** of **Verwijder toewijzing** maken.

    ![Leden converteren naar een in aanmerking komende pagina met opties om leden te selecteren die u in aanmerking wilt stellen voor rollen](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. U kunt ook alle globale beheerders vragen hun eigen toegang te controleren.

    ![Pagina globale beheerders met toegangs beoordelingen sectie](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. Nadat u een van deze wijzigingen hebt geselecteerd, ziet u een Azure-melding.

1. U kunt vervolgens **permanente toegang elimineren** of **service-principals controleren** om de bovenstaande stappen te herhalen op andere geprivilegieerde rollen en op Service Principal Role-toewijzingen. Voor Service Principal-roltoewijzingen kunt u alleen roltoewijzingen verwijderen.

    ![Aanvullende inzichten opties voor het elimineren van permanente toegang en het controleren van service-principals ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-rollen toewijzen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Toegang verlenen aan andere beheerders om Privileged Identity Management te beheren](pim-how-to-give-access-to-pim.md)
