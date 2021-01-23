---
title: Problemen oplossen met administratieve eenheden en veelgestelde vragen-Azure Active Directory | Microsoft Docs
description: Onderzoek administratieve eenheden om machtigingen te verlenen met een beperkt bereik in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fb61388b13b912166c9c07b9ee28b365c4a63b9
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740377"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD-beheer eenheden: problemen oplossen en veelgestelde vragen

Voor gedetailleerdere administratieve controle in Azure Active Directory (Azure AD) kunt u gebruikers toewijzen aan een Azure AD-rol met een bereik dat beperkt is tot een of meer beheer eenheden. Zie [werken met beheer eenheden](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true)voor voor beelden van Power shell-scripts voor algemene taken.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: Waarom kan ik geen administratieve eenheid maken?**

**A:** Alleen een beheerder van de *globale beheerder* of een *bevoegde rol* kan een administratieve eenheid maken in azure AD. Controleer of de gebruiker die de beheer eenheid probeert te maken, de rol *globale beheerder* *of beheerdersrol* heeft toegewezen.

**V: Ik heb een groep toegevoegd aan een beheer eenheid. Waarom worden de groeps leden nog steeds niet weer gegeven?**

**A:** Wanneer u een groep aan een beheer eenheid toevoegt, worden niet alle leden van de groep hieraan toegevoegd. Gebruikers moeten rechtstreeks aan de beheer eenheid worden toegewezen.

**V: Ik heb een lid van de beheer eenheid toegevoegd (of verwijderd). Waarom wordt het lid niet weer gegeven (of nog steeds weer gegeven) in de gebruikers interface?**

**A:** Soms kan het enkele minuten duren voordat het toevoegen of verwijderen van een of meer leden van een beheer eenheid wordt weer gegeven in het deel venster **beheer eenheden** . U kunt ook rechtstreeks naar de eigenschappen van de gekoppelde bron gaan en zien of de actie is voltooid. Zie [een lijst met beheer eenheden voor een gebruiker weer geven](admin-units-add-manage-users.md) en [een lijst met beheer eenheden voor een groep weer geven](admin-units-add-manage-groups.md)voor meer informatie over gebruikers en groepen in administratieve eenheden.

**V: Ik ben een gedelegeerde wachtwoord beheerder voor een administratieve eenheid. Waarom kan ik het wacht woord van een specifieke gebruiker niet opnieuw instellen?**

**A:** Als beheerder van een administratieve eenheid kunt u wacht woorden alleen opnieuw instellen voor gebruikers die zijn toegewezen aan uw beheer eenheid. Zorg ervoor dat de gebruiker waarvan het wacht woord opnieuw wordt ingesteld, deel uitmaakt van de beheer eenheid waaraan u bent toegewezen. Als de gebruiker tot dezelfde beheer eenheid behoort, maar u het wacht woord van de gebruiker nog steeds niet opnieuw kunt instellen, controleert u de rollen die aan de gebruiker zijn toegewezen. 

Om een uitbrei ding van bevoegdheden te voor komen, kan een beheerder met een beheer eenheid het wacht woord van een gebruiker die is toegewezen aan een rol met een organisatie bereik, niet opnieuw instellen.

**V: Waarom zijn er beheer eenheden nodig? Kan niet worden gebruikt voor het definiëren van een bereik?**

**A:** Beveiligings groepen hebben een bestaand doel-en autorisatie model. Een *gebruikers beheerder* kan bijvoorbeeld lidmaatschap van alle beveiligings groepen in de Azure AD-organisatie beheren. De rol kan groepen gebruiken om de toegang tot toepassingen te beheren, zoals Sales Force. Een *gebruikers beheerder* moet het delegerings model zelf niet kunnen beheren, wat het gevolg zou zijn als beveiligings groepen zijn uitgebreid ter ondersteuning van scenario's voor het groeperen van bronnen. 

Beheer eenheden, zoals organisatie-eenheden in Windows Server Active Directory, zijn bedoeld om het bereik van een breed scala aan Directory-objecten te beheren. Beveiligings groepen kunnen lid zijn van een resource bereik. Het gebruik van beveiligings groepen voor het definiëren van de set beveiligings groepen die een beheerder kan beheren, kan verwarrend worden.

**V: wat betekent het om een groep toe te voegen aan een beheer eenheid?**

**A:** Door een groep toe te voegen aan een beheer eenheid, wordt de groep automatisch in het beheer bereik van elke *gebruikers beheerder* opgenomen die ook is afgestemd op die administratieve eenheid. Gebruikers beheerders voor de beheer eenheid kunnen de naam en het lidmaatschap van de groep zelf beheren. Er worden geen *beheerders* machtigingen verleend voor het beheren van de gebruikers van de groep (bijvoorbeeld om hun wacht woorden opnieuw in te stellen). Gebruikers moeten direct lid zijn van de beheer eenheid om de *gebruikers beheerder* in staat te stellen om gebruikers te beheren.

**V: kan een resource (gebruiker of groep) lid zijn van meer dan één administratieve eenheid?**

**A:** Ja, een resource kan lid zijn van meer dan één administratieve eenheid. De resource kan worden beheerd door alle beheerders binnen de organisatie en beheerders met administratieve eenheden die machtigingen hebben voor de resource.

**V: zijn er beheerders eenheden beschikbaar in B2C-organisaties?**

**A:** Nee, er zijn geen administratieve eenheden beschikbaar voor B2C organisaties.

**V: worden geneste beheer eenheden ondersteund?**

**A:** Nee, geneste beheer eenheden worden niet ondersteund.

**V: worden er administratieve eenheden ondersteund in Power shell en de Graph API?**

**A:** Klikt. U vindt ondersteuning voor beheer eenheden in [Power shell-cmdlet-documentatie](/powershell/module/Azuread/?view=azureadps-2.0&preserve-view=true) en [voorbeeld scripts](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true).

Zoek ondersteuning voor het [resource type administrativeUnit](/graph/api/resources/administrativeunit?view=graph-rest-1.0&preserve-view=true) in Microsoft Graph.

## <a name="next-steps"></a>Volgende stappen

- [Bereik beperken voor rollen met behulp van beheer eenheden](administrative-units.md)
- [Beheereenheden beheren](admin-units-manage.md)
