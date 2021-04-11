---
title: Cloud groepen gebruiken voor het beheren van roltoewijzingen in Azure Active Directory | Microsoft Docs
description: Bekijk een voorbeeld van aangepaste Azure AD-rollen voor het delegeren van identiteitsbeheer. Toewijzingen van Azure-rollen beheren in de Azure Portal, Power shell of Graph API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a71a53cd2aff16102a54f5a24063615e02d11872
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169518"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>Cloud groepen gebruiken voor het beheren van roltoewijzingen in Azure Active Directory (preview-versie)

Azure Active Directory (Azure AD) introduceert een open bare preview waarin u een Cloud groep kunt toewijzen aan ingebouwde rollen van Azure AD. Met deze functie kunt u groepen gebruiken om beheerders toegang te verlenen in azure AD met minimale inspanning van uw globale en geprivilegieerde beheerdersrol.

Bekijk dit voor beeld: contoso heeft personen in de buitenste grafieken voor het beheren en opnieuw instellen van wacht woorden voor werk nemers in de Azure AD-organisatie. In plaats van een beheerder of globale beheerder te vragen om de beheerdersrol van de helpdesk beheerder aan elke persoon afzonderlijk toe te wijzen, kunnen ze een Contoso_Helpdesk_Administrators groep maken en deze toewijzen aan de rol. Wanneer personen aan de groep deel nemen, krijgen ze de rol indirect toegewezen. Uw bestaande beheer werk stroom kan vervolgens het goedkeurings proces en de controle van het lidmaatschap van de groep uitvoeren om ervoor te zorgen dat alleen legitieme gebruikers lid van de groep zijn en daarom worden toegewezen aan de beheerdersrol van de helpdesk beheerder.

## <a name="how-this-feature-works"></a>Hoe deze functie werkt

Maak een nieuwe Microsoft 365 of beveiligings groep waarvan de eigenschap ' isAssignableToRole ' is ingesteld op ' True '. U kunt deze eigenschap ook inschakelen bij het maken van een groep in de Azure Portal door **Azure AD-rollen in te scha kelen, kunnen worden toegewezen aan de groep**. In beide gevallen kunt u de groep vervolgens op dezelfde manier toewijzen aan een of meer Azure AD-rollen als u rollen aan gebruikers toewijst. Maxi maal 250 functie-toewijs bare groepen kunnen worden gemaakt in één Azure AD-organisatie (Tenant).

Als u niet wilt dat leden van de groep permanente toegang tot de rol hebben, kunt u Azure AD Privileged Identity Management gebruiken. Wijs een groep toe als een in aanmerking komend lid van een Azure AD-rol. Elk lid van de groep komt dan in aanmerking om hun toewijzing te activeren voor de rol waaraan de groep is toegewezen. Ze kunnen vervolgens hun roltoewijzing activeren voor een vaste tijds duur.

> [!Note]
> U moet over een bijgewerkte versie van Privileged Identity Management beschikken om een groep aan de Azure AD-rol via PIM toe te wijzen. U kunt een oudere versie van PIM gebruiken omdat uw Azure AD-organisatie gebruikmaakt van de Privileged Identity Management-API. Neem contact op met de alias pim_preview@microsoft.com om uw organisatie te verplaatsen en uw API bij te werken. Meer informatie over [Azure AD-rollen en-functies in PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>Waarom we het maken van een speciale groep voor het toewijzen aan een rol afdwingen

Als aan een groep een rol is toegewezen, kan elke IT-beheerder die groepslid maatschap kan beheren, het lidmaatschap van die rol ook indirect beheren. Stel bijvoorbeeld dat een groeps Contoso_User_Administrators is toegewezen aan de rol van gebruikersrol voor gebruikers accounts. Een Exchange-beheerder die groepslid maatschap kan wijzigen, kan zichzelf toevoegen aan de Contoso_User_Administrators groep en op die manier een beheerder van een gebruikers account worden. Zoals u ziet, kan een beheerder de bevoegdheid op een manier die u niet van plan bent te verhogen.

Met Azure AD kunt u een groep die is toegewezen aan een rol, beveiligen met behulp van een nieuwe eigenschap met de naam isAssignableToRole voor groepen. Alleen Cloud groepen waarvoor de eigenschap isAssignableToRole is ingesteld op ' True ' op de aanmaak tijd, kunnen worden toegewezen aan een rol. Deze eigenschap is onveranderbaar. Wanneer een groep is gemaakt en deze eigenschap is ingesteld op ' True ', kan deze niet worden gewijzigd. U kunt de eigenschap niet instellen voor een bestaande groep.
We hebben ontworpen hoe groepen worden toegewezen aan rollen om te voor komen dat er mogelijke schendingen van het probleem zich voordoen:

- Alleen globale beheerders en geprivilegieerde beheerdersrol kunnen een door een functie toewijs bare groep maken (waarbij de eigenschap isAssignableToRole is ingeschakeld).
- Het kan geen dynamische Azure AD-groep zijn. dat wil zeggen dat het lidmaatschaps type ' toegewezen ' moet zijn. Automatische populatie van dynamische groepen kan ertoe leiden dat een ongewenst account wordt toegevoegd aan de groep en daarom aan de rol wordt toegewezen.
- Standaard kunnen alleen globale beheerders en geprivilegieerde beheerdersrol het lidmaatschap van een door een functie toewijs bare groep beheren, maar u kunt het beheer van aan rollen toewijs bare groepen delegeren door groeps eigenaren toe te voegen.
- Als u uitbrei ding van bevoegdheden wilt voor komen, kunnen de referenties van de leden en eigen aren van een door een functie toewijs bare groep alleen worden gewijzigd door een beheerder met beheerders rechten of een globale beheerder.
- Geen Nesting. Een groep kan niet worden toegevoegd als lid van een door een functie toewijs bare groep.

## <a name="limitations"></a>Beperkingen

De volgende scenario's worden momenteel niet ondersteund:  

- On-premises groepen toewijzen aan Azure AD-rollen (ingebouwd of aangepast)

## <a name="known-issues"></a>Bekende problemen

- *Alleen klanten met een licentie voor Azure AD P2*: wijs een groep niet als actief toe aan een rol via Azure ad en PRIVILEGED Identity Management (PIM). Wijs met name geen rol toe aan een door de gebruiker toewijs bare groep wanneer deze wordt gemaakt *en* wijs later met Pim een rol aan de groep toe. Dit leidt tot problemen waar gebruikers hun actieve roltoewijzingen niet kunnen zien in de PIM, en de mogelijkheid tot het verwijderen van die PIM-toewijzing. In dit scenario worden in aanmerking komende toewijzingen niet beïnvloed. Als u probeert deze toewijzing te maken, ziet u mogelijk onverwacht gedrag zoals:
  - De eind tijd voor de roltoewijzing kan onjuist worden weer gegeven.
  - In de PIM-portal kunnen **mijn rollen** slechts één roltoewijzing tonen, ongeacht het aantal methoden waarmee de toewijzing wordt verleend (via een of meer groepen en direct).
- De functie **gefaseerde implementatie voor beheerde gebruikers aanmelding inschakelen** biedt geen ondersteuning voor toewijzing via groep.
- *Alleen klanten met een licentie voor Azure AD P2* Zelfs na het verwijderen van de groep wordt er nog steeds een in aanmerking komend lid van de rol weer gegeven in PIM-gebruikers interface. Er is functioneel geen probleem. het is slechts een cache probleem in de Azure Portal.  
- Gebruik het nieuwe [Exchange-beheer centrum](https://admin.exchange.microsoft.com/) voor roltoewijzingen via groepslid maatschap. Het oude Exchange-beheer centrum biedt nog geen ondersteuning voor deze functie. Exchange Power shell-cmdlets werken zoals verwacht.
- Azure Information Protection-Portal (de klassieke Portal) herkent het lidmaatschap van de rol nog niet via groep. U kunt [migreren naar het Unified sensitivive labeling platform](/azure/information-protection/configure-policy-migrate-labels) en vervolgens het Office 365 Security & compliance gebruiken om groeps toewijzingen te gebruiken voor het beheren van rollen.
- Het [apps-beheer centrum](https://config.office.com/) biedt nog geen ondersteuning voor deze functie. Gebruikers rechtstreeks toewijzen aan de beheerders functie van Office-apps.

Deze problemen worden opgelost.

## <a name="required-license-plan"></a>Vereiste licentieplan

Als u deze functie wilt gebruiken, hebt u een beschik bare Azure AD Premium P1-licentie in uw Azure AD-organisatie nodig. Als u ook wilt gebruiken Privileged Identity Management voor Just-in-time-functie activering, moet u een beschik bare Azure AD Premium P2-licentie hebben. Zie [Algemeen beschik bare functies van de gratis en Premium-abonnementen vergelijken](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses)om de juiste licentie voor uw vereisten te vinden.

## <a name="next-steps"></a>Volgende stappen

- [Een roltoewijzbare groep maken](groups-create-eligible.md)
- [Een rol toewijzen aan een door een functie toewijs bare groep](groups-assign-role.md)
