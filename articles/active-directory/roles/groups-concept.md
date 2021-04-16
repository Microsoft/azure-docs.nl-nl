---
title: Cloudgroepen gebruiken voor het beheren van roltoewijzingen in Azure Active Directory | Microsoft Docs
description: Bekijk een voorbeeld van aangepaste Azure AD-rollen voor het delegeren van identiteitsbeheer. Beheer Azure-roltoewijzingen in Azure Portal, PowerShell of Graph API.
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
ms.openlocfilehash: e1fea64305d4735c5bf1bf59b86ae5283600e622
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502195"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>Cloudgroepen gebruiken voor het beheren van roltoewijzingen in Azure Active Directory (preview)

Azure Active Directory (Azure AD) introduceert een openbare preview waarin u een cloudgroep kunt toewijzen aan ingebouwde Azure AD-rollen. Met deze functie kunt u groepen gebruiken om beheerderstoegang in Azure AD te verlenen met minimale inspanning van uw globale en bevoorrechte rolbeheerders.

Kijk eens naar dit voorbeeld: Contoso heeft mensen in verschillende geografische gebieden ingehuurd om wachtwoorden voor werknemers in de Azure AD-organisatie te beheren en opnieuw in te stellen. In plaats van een beheerder met bevoorrechte rol of globale beheerder te vragen om de rol helpdeskbeheerder aan elke persoon afzonderlijk toe te wijzen, kunnen ze een Contoso_Helpdesk_Administrators maken en deze toewijzen aan de rol. Wanneer mensen lid worden van de groep, wordt de rol indirect aan hen toegewezen. Uw bestaande governancewerkstroom kan vervolgens het goedkeuringsproces en de controle van het lidmaatschap van de groep uitvoeren om ervoor te zorgen dat alleen legitieme gebruikers lid zijn van de groep en dus zijn toegewezen aan de beheerdersrol Helpdesk.

## <a name="how-this-feature-works"></a>Hoe deze functie werkt

Maak een nieuwe Microsoft 365 of beveiligingsgroep met de eigenschap 'isAssignableToRole' ingesteld op 'true'. U kunt deze eigenschap ook inschakelen bij het maken van een groep in de Azure Portal door Azure AD-rollen in te **stellen, kunnen worden toegewezen aan de groep**. U kunt de groep vervolgens op dezelfde manier toewijzen aan een of meer Azure AD-rollen als u rollen aan gebruikers toewijst. Er kunnen maximaal 250 rol toewijsbare groepen worden gemaakt in één Azure AD-organisatie (tenant).

Als u niet wilt dat leden van de groep permanente toegang hebben tot de rol, kunt u Azure AD Privileged Identity Management. Wijs een groep toe als een in aanmerking komend lid van een Azure AD-rol. Elk lid van de groep komt vervolgens in aanmerking om de toewijzing te activeren voor de rol die aan de groep is toegewezen. Vervolgens kunnen ze hun roltoewijzing voor een vaste tijdsduur activeren.

> [!Note]
> U moet een bijgewerkte versie van Privileged Identity Management om via PIM een groep aan een Azure AD-rol te kunnen toewijzen. U kunt een oudere versie van PIM gebruiken omdat uw Azure AD-organisatie gebruik maakt van de Privileged Identity Management API. Neem contact op met de alias om pim_preview@microsoft.com uw organisatie te verplaatsen en uw API bij te werken. Meer informatie over [Azure AD-rollen en -functies in PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>Waarom we het maken van een speciale groep afdwingen om deze toe te wijzen aan een rol

Als aan een groep een rol is toegewezen, kan elke IT-beheerder die het groepslidmaatschap kan beheren ook indirect het lidmaatschap van die rol beheren. Stel bijvoorbeeld dat een groep Contoso_User_Administrators is toegewezen aan de beheerdersrol Gebruikersaccount. Een Exchange-beheerder die het groepslidmaatschap kan wijzigen, kan zichzelf toevoegen aan Contoso_User_Administrators groep en op die manier een gebruikersaccountbeheerder worden. Zoals u ziet, kan een beheerder zijn bevoegdheden op een manier verhogen die u niet wilde.

Met Azure AD kunt u een groep beveiligen die is toegewezen aan een rol met behulp van een nieuwe eigenschap met de naam isAssignableToRole voor groepen. Alleen cloudgroepen met de eigenschap isAssignableToRole ingesteld op 'true' tijdens het maken, kunnen worden toegewezen aan een rol. Deze eigenschap is onveranderbaar; zodra een groep is gemaakt met deze eigenschap ingesteld op 'true', kan deze niet worden gewijzigd. U kunt de eigenschap niet instellen voor een bestaande groep.
We hebben ontworpen hoe groepen worden toegewezen aan rollen om te voorkomen dat dergelijke mogelijke schendingen plaatsvinden:

- Alleen globale beheerders en beheerders van bevoorrechte rollen kunnen een rol toewijsbare groep maken (met de eigenschap isAssignableToRole ingeschakeld).
- Het mag geen dynamische Azure AD-groep zijn; Dat wil zeggen dat het lidmaatschapstype 'Toegewezen' moet hebben. Geautomatiseerde populatie van dynamische groepen kan ertoe leiden dat een ongewenst account wordt toegevoegd aan de groep en dus wordt toegewezen aan de rol.
- Standaard kunnen alleen globale beheerders en beheerders met bevoorrechte rollen het lidmaatschap van een rol toewijsbare groep beheren, maar u kunt het beheer van rol toewijsbare groepen delegeren door groepseigenaren toe te voegen.
- Om verhoging van toegangsrechten te voorkomen, kunnen de referenties van leden en eigenaren van een rol toewijsbare groep alleen worden gewijzigd door een Privileged Verificatiebeheerder of een Globale beheerder.
- Geen nesting. Een groep kan niet worden toegevoegd als lid van een rol toewijsbare groep.

## <a name="limitations"></a>Beperkingen

De volgende scenario's worden momenteel niet ondersteund:  

- On-premises groepen toewijzen aan Azure AD-rollen (ingebouwd of aangepast)

## <a name="known-issues"></a>Bekende problemen

- *Alleen klanten met een Azure AD P2-licentie:* wijs een groep niet als Actief toe aan een rol via zowel Azure AD als Privileged Identity Management (PIM). Wijs met name geen rol toe aan een rol toewijsbare groep wanneer deze wordt gemaakt en wijs later een rol toe aan de groep met behulp van PIM.  Dit leidt tot problemen waarbij gebruikers hun actieve roltoewijzingen niet kunnen zien in de PIM en het onvermogen om die PIM-toewijzing te verwijderen. In aanmerking komende toewijzingen worden in dit scenario niet beïnvloed. Als u deze toewijzing probeert te maken, ziet u mogelijk onverwacht gedrag, zoals:
  - De eindtijd voor de roltoewijzing wordt mogelijk onjuist weergegeven.
  - In de PIM-portal kan **Mijn** rollen slechts één roltoewijzing tonen, ongeacht het aantal methoden waarmee de toewijzing wordt verleend (via een of meer groepen en rechtstreeks).
- *Alleen klanten met Een Azure AD P2-licentie* Zelfs na het verwijderen van de groep wordt er nog steeds een in aanmerking komend lid van de rol weergegeven in de PIM-gebruikersinterface. Functioneel is er geen probleem; het is gewoon een cacheprobleem in de Azure Portal.  
- Gebruik het nieuwe [Exchange-beheercentrum voor](https://admin.exchange.microsoft.com/) roltoewijzingen via groepslidmaatschap. Het oude Exchange-beheercentrum biedt nog geen ondersteuning voor deze functie. Exchange PowerShell-cmdlets werken zoals verwacht.
- Azure Information Protection Portal (de klassieke portal) herkent nog geen rollidmaatschap via groep. U kunt migreren naar het [unified sensitivity labeling-platform](/azure/information-protection/configure-policy-migrate-labels) en vervolgens het Office 365 Security & Compliance center gebruiken om groepstoewijzingen te gebruiken voor het beheren van rollen.
- [Het Beheercentrum voor](https://config.office.com/) apps biedt nog geen ondersteuning voor deze functie. Gebruikers rechtstreeks toewijzen aan de rol Office Apps-beheerder.
- [M365 Compliance Center biedt](https://compliance.microsoft.com/) nog geen ondersteuning voor deze functie. Wijs gebruikers rechtstreeks toe aan de juiste Azure AD-rollen om deze portal te gebruiken.

We lossen deze problemen op.

## <a name="required-license-plan"></a>Vereiste licentieplan

Voor het gebruik van deze functie moet u beschikken over een Azure AD Premium P1-licentie in uw Azure AD-organisatie. Als u ook Privileged Identity Management just-in-time-rolactivering wilt gebruiken, hebt u een beschikbare P2-licentie Azure AD Premium P2 nodig. Zie Algemeen beschikbare functies van de gratis en Premium-abonnementen vergelijken om de juiste licentie voor uw vereisten [te vinden.](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses)

## <a name="next-steps"></a>Volgende stappen

- [Een roltoewijzbare groep maken](groups-create-eligible.md)
- [Een rol toewijzen aan een rol toewijsbare groep](groups-assign-role.md)
