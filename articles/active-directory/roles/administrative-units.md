---
title: Beheereenheden in Azure Active Directory | Microsoft Docs
description: Gebruik beheereenheden voor een meer gedetailleerde delegatie van machtigingen in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49f2c290c69fcadd594d6cbd5879e7d9f5304a42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558012"
---
# <a name="administrative-units-in-azure-active-directory"></a>Beheereenheden in Azure Active Directory

In dit artikel worden beheereenheden in Azure Active Directory (Azure AD) beschreven. Een beheereenheid is een Azure AD-resource die een container kan zijn voor andere Azure AD-resources. Een beheereenheid kan alleen gebruikers en groepen bevatten.

Beheereenheden beperken de machtigingen voor een rol tot elk deel van uw organisatie dat u definieert. U kunt bijvoorbeeld via beheereenheden de rol [Helpdeskbeheerder](permissions-reference.md#helpdesk-administrator) delegeren aan regionale ondersteuningsspecialisten, zodat deze alleen de gebruikers beheren in de regio die zij ondersteunen.

## <a name="deployment-scenario"></a>Implementatiescenario

Het kan nuttig zijn het beheerbereik te beperken met behulp van beheereenheden in organisaties die bestaan uit onafhankelijke divisies van welke aard dan ook. Bekijk het voorbeeld van een grote universiteit die bestaat uit een groot aantal zelfstandige scholen (faculteit Bedrijfskunde, faculteit Bouwkunde, enzovoort). Elke school heeft een team van IT-beheerders die de toegang beheren, gebruikers beheren en beleidsregels voor hun school instellen.

Een centrale beheerder kan het volgende doen:

- Een rol maken met beheerdersmachtigingen die betrekking hebben op uitsluitend Azure AD-gebruikers in de beheereenheid voor de faculteit Bedrijfskunde.
- Een beheereenheid maken voor de faculteit Bedrijfskunde.
- De beheereenheid vullen met uitsluitend de studenten en medewerkers van de faculteit Bedrijfskunde.
- Het IT-team van de faculteit Bedrijfskunde toevoegen aan de rol samen met het bijbehorende bereik.

## <a name="license-requirements"></a>Licentievereisten

Voor het gebruik van beheereenheden heeft u een Azure Active Directory Premium-licentie nodig voor elke beheerder van een beheereenheid en Azure Active Directory Free-licenties voor leden van de beheereenheid. Zie [Aan de slag met Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) voor meer informatie.

## <a name="manage-administrative-units"></a>Beheereenheden beheren

U kunt beheereenheden beheren met behulp van Azure Portal, PowerShell-cmdlets en -scripts of Microsoft Graph. Zie voor meer informatie:

- [Rollen maken, verwijderen, vullen en toevoegen aan beheereenheden](admin-units-manage.md): Bevat volledige instructieprocedures.
- [Werken met beheereenheden](/powershell/azure/active-directory/working-with-administrative-units): Behandelt het werken met beheereenheden met behulp van PowerShell.
- [Graph-ondersteuning voor beheereenheden](/graph/api/resources/administrativeunit): Geeft gedetailleerde documentatie over Microsoft Graph voor beheereenheden.

### <a name="plan-your-administrative-units"></a>Plan uw beheereenheden

U kunt beheereenheden gebruiken voor het logisch groeperen van Azure Active Directory-resources. Het kan zijn dat een organisatie waarvan de IT-afdeling over de hele wereld is verspreid beheereenheden maakt waarmee de relevante geografische grenzen worden gedefinieerd. In een ander scenario waarin een wereldwijde organisatie suborganisaties heeft die semi-autonoom hun activiteiten uitvoeren, kunnen beheereenheden een weerspiegeling vormen van de suborganisaties.

De criteria voor het maken van beheereenheden worden bepaald door de unieke vereisten van een organisatie. Beheereenheden zijn een veelgebruikte manier om structuur aan te brengen in Microsoft 365-services. Het wordt aanbevolen om bij het voorbereiden van uw beheereenheden rekening te houden met het gebruik ervan in Microsoft 365-services. U kunt beheereenheden optimaal benutten wanneer u veelgebruikte resources in Microsoft 365 kunt koppelen in een beheereenheid.

Het maken van beheereenheden in de organisatie doorloopt doorgaans de volgende fasen:

1. **Eerste ingebruikname**: Uw organisatie begint met het maken van beheereenheden op basis van aanvankelijke criteria en het aantal beheereenheden zal toenemen naarmate de criteria worden verfijnd.
1. **Snoeien**: Nadat de criteria zijn gedefinieerd, worden beheereenheden verwijderd die niet meer nodig zijn.
1. **Stabilisatie**: De structuur van uw organisatie is gedefinieerd en het aantal beheereenheden zal op korte termijn niet beduidend veranderen.

## <a name="currently-supported-scenarios"></a>Scenario's die momenteel worden ondersteund

Als globale beheerder of beheerder voor bevoorrechte rollen kunt u de Azure AD-portal gebruiken om het volgende te doen:

- Beheereenheden maken
- Gebruikers en groepsleden van beheereenheden toevoegen
- Wijs IT-personeel toe aan de rol Beheerder voor een specifieke beheereenheid.

Beheerders van een specifieke beheereenheid kunnen het Microsoft 365-beheercentrum gebruiken voor het basisbeheer van gebruikers in hun beheereenheden. Een groepsbeheerder voor een specifieke beheereenheid kan groepen beheren met PowerShell, Microsoft Graph en de Microsoft 365-beheercentra.

>[!Note]
>Alleen de functies die in deze sectie worden beschreven, zijn beschikbaar in het Microsoft 365-beheercentrum. Er zijn geen functies op organisatieniveau beschikbaar voor een Azure AD-rol met het bereik van de beheereenheid.

In de volgende secties wordt de huidige ondersteuning voor scenario's met beheereenheden beschreven.

### <a name="administrative-unit-management"></a>Het beheren van beheereenheden

| Machtigingen |   Graph/PowerShell   | Azure AD-portal | Het Microsoft 365-beheercentrum |
| --- | --- | --- | --- |
| Beheereenheden maken en verwijderen   |    Ondersteund    |   Ondersteund   |    Niet ondersteund |
| Leden van een beheereenheid afzonderlijk toevoegen en verwijderen    |   Ondersteund    |   Ondersteund   |    Niet ondersteund |
| Leden van een beheereenheid bulksgewijs toevoegen en verwijderen met CSV-bestanden   |    Niet ondersteund     |  Ondersteund   |    Geen abonnement die dit ondersteunt |
| Beheerders voor een specifieke beheereenheid toewijzen  |     Ondersteund    |   Ondersteund    |   Niet ondersteund |
| Leden voor een beheereenheid dynamisch toevoegen en verwijderen op basis van kenmerken | Niet ondersteund | Niet ondersteund | Niet ondersteund

### <a name="user-management"></a>Gebruikersbeheer

| Machtigingen |   Graph/PowerShell   | Azure AD-portal | Het Microsoft 365-beheercentrum |
| --- | --- | --- | --- |
| Beheer van gebruikerseigenschappen, wachtwoorden en licenties voor een specifieke beheereenheid   |    Ondersteund     |  Ondersteund   |   Ondersteund |
| Blokkeren en deblokkeren van gebruikersaanmeldingen voor een specifieke beheereenheid    |   Ondersteund   |    Ondersteund   |    Ondersteund |
| Beheer van meervoudige verificatie-referenties van gebruikers voor een specifieke beheereenheid   |    Ondersteund   |   Ondersteund   |   Niet ondersteund |

### <a name="group-management"></a>Groepsbeheer

| Machtigingen |   Graph/PowerShell   | Azure AD-portal | Het Microsoft 365-beheercentrum |
| --- | --- | --- | --- |
| Beheer van groepseigenschappen en -leden voor een specifieke beheereenheid     |  Ondersteund   |    Ondersteund    |  Niet ondersteund |
| Beheer van groepslicenties voor een specifieke beheereenheid   |    Ondersteund  |    Ondersteund   |   Niet ondersteund |

Het bereik van beheereenheden is alleen van toepassing op beheermachtigingen. Ze verhinderen niet dat leden of beheerders hun [standaardgebruikersmachtigingen](../fundamentals/users-default-permissions.md) gebruiken om door andere gebruikers, groepen of resources buiten de beheereenheid te bladeren. In het Microsoft 365-beheercentrum worden gebruikers buiten de beheereenheden van een specifieke beheerder eruit gefilterd. U kunt echter door andere gebruikers bladeren in de Azure Active Directory-portal, PowerShell en andere Microsoft-services.

## <a name="next-steps"></a>Volgende stappen

- [Beheereenheden beheren](admin-units-manage.md)
- [Gebruikers beheren in beheereenheden](admin-units-add-manage-users.md)
- [Groepen beheren in beheereenheden](admin-units-add-manage-groups.md)
- [Specifieke rollen toewijzen aan een beheereenheid](admin-units-assign-roles.md)
