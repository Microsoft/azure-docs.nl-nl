---
title: Overzicht van op rollen gebaseerd toegangsbeheer in Azure Active Directory (RBAC)
description: Uitleg van de onderdelen van een roltoewijzing en van een beperkt bereik in Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fad2c683890776908afbfbf15ee91d46d564783
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466759"
---
# <a name="overview-of-role-based-access-control-in-azure-active-directory"></a>Overzicht van op rollen gebaseerd toegangsbeheer in Azure Active Directory

In dit artikel wordt uitleg gegeven over Azure AD-rollen (Azure Active Directory) met op rollen gebaseerd toegangsbeheer. Met Azure AD-rollen kunt u gedetailleerde machtigingen verlenen aan uw beheerders, overeenkomstig het principe van minimale bevoegdheden. De werking van ingebouwde Azure AD-rollen en aangepaste rollen is vergelijkbaar met die in [het op rollen gebaseerd toegangsbeheer voor Azure resources](../../role-based-access-control/overview.md) (Azure-rollen). Het [verschil tussen deze twee op rollen gebaseerde systemen voor toegangsbeheer](../../role-based-access-control/rbac-and-directory-admin-roles.md) is als volgt:

- Met Azure AD-rollen wordt de toegang tot Azure AD-resources beheerd, zoals gebruikers, groepen en toepassingen, met behulp van Graph API
- Met Azure-rollen wordt de toegang tot Azure-resources beheerd, zoals virtuele machines of opslag, met behulp van Azure Resource Management

Beide systemen maken op een soortgelijke manier gebruik van roldefinities en roltoewijzingen. Machtigingen voor Azure AD-rollen kunnen echter niet worden gebruikt in aangepaste Azure-rollen en omgekeerd.

## <a name="understand-azure-ad-role-based-access-control"></a>Uitleg over op rollen gebaseerd toegangsbeheer van Azure AD
Azure AD biedt ondersteuning voor 2 typen roldefinities - 
* [Ingebouwde rollen](./permissions-reference.md)
* [Aangepaste rollen](./custom-create.md)

Ingebouwde rollen zijn kant-en-klare-rollen met een vaste set machtigingen. Deze roldefinities kunnen niet worden gewijzigd. Azure AD biedt ondersteuning voor veel [ingebouwde rollen](./permissions-reference.md), en de lijst wordt alleen maar langer. Azure biedt ook ondersteuning voor [aangepaste rollen](./custom-create.md), voor meer verfijning en om te voldoen aan uw meer geavanceerde vereisten. Het verlenen van machtigingen met behulp van aangepaste Azure AD-rollen is een proces in twee stappen: het maken van een aangepaste roldefinitie en vervolgens het toewijzen ervan met behulp van een roltoewijzing. Een aangepaste roldefinitie is een verzameling machtigingen die u toevoegt uit een vooraf ingestelde lijst. Deze machtigingen zijn dezelfde machtigingen die worden gebruikt in de ingebouwde rollen.  

Zodra u de aangepaste roldefinitie hebt gemaakt (of als u een ingebouwde rol gebruikt), kunt u deze toewijzen aan een gebruiker door een roltoewijzing te maken. Met een roltoewijzing worden machtigingen in een roldefinitie aan de gebruiker verleend voor een opgegeven bereik. Met dit proces in twee stappen kunt u één roldefinitie maken en deze meerdere keren toewijzen voor verschillende bereiken. Met een bereik wordt de set Azure AD-resources gedefinieerd waartoe het rollid toegang heeft. Het meest voorkomende bereik is het bereik voor de hele organisatie (organisatiebreed). Een aangepaste rol kan worden toegewezen voor het organisatiebrede bereik. Dit betekent dat de rolmachtigingen van het rollid gelden voor alle resources in de organisatie. Een aangepaste rol kan ook worden toegewezen voor een objectbereik. Een voorbeeld van een objectbereik is één toepassing. Dezelfde rol kan aan de ene gebruiker worden toegewezen voor alle toepassingen in de organisatie, en vervolgens aan een andere gebruiker met een bereik van alleen de Contoso Expense Reports-app.  

De werking van ingebouwde Azure AD-rollen en aangepaste rollen is vergelijkbaar met [Azure RBAC (op rollen gebaseerd toegangsbeheer)](../develop/access-tokens.md#payload-claims). Het [verschil tussen deze twee systemen voor op rollen gebaseerd toegangsbeheer](../../role-based-access-control/rbac-and-directory-admin-roles.md) is dat met Azure RBAC de toegang tot Azure-resources, zoals virtuele machines of opslag, wordt beheerd met Azure Resource Management, en dat met aangepaste Azure AD-rollen de toegang tot Azure AD-resources wordt beheerd met behulp van Graph API. Beide systemen maken gebruik van het concept van roldefinities en roltoewijzingen. U kunt geen Azure AD RBAC-machtigingen opnemen in Azure-rollen en vice versa.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Hoe Azure AD bepaalt of een gebruiker toegang tot een resource heeft

Hier volgen de stappen op hoog niveau die in Azure AD worden gebruikt om te bepalen of u toegang hebt tot een beheerresource. Gebruik deze informatie om problemen met toegang op te lossen.

1. Een gebruiker (of service-principal) verkrijgt een token voor het Microsoft Graph- of Azure AD Graph-eindpunt.
1. De gebruiker doet een API-aanroep naar Azure AD (Active Directory) via Microsoft Graph of Azure AD Graph met behulp van het uitgegeven token.
1. Afhankelijk van de omstandigheden wordt in Azure AD een van de volgende acties uitgevoerd:
   - Evalueren van de rollidmaatschappen van de gebruiker op basis van de [wids-claim](../../active-directory-b2c/access-tokens.md) in het toegangstoken van de gebruiker.
   - Ophalen van alle roltoewijzingen die van toepassing zijn op de gebruiker, hetzij rechtstreeks of via groepslidmaatschap, voor de resource waarvoor de actie wordt uitgevoerd.
1. Azure AD bepaalt of de actie in de API-aanroep is opgenomen in de rollen die de gebruiker heeft voor deze resource.
1. Als de gebruiker geen rol met de actie heeft in het aangevraagde bereik, wordt er geen toegang verleend. Anders wordt toegang verleend.

## <a name="role-assignment"></a>Roltoewijzing

Een roltoewijzing is een Azure AD-resource op basis waarvan een *roldefinitie* is gekoppeld aan een *gebruiker* voor een bepaald *bereik*, om toegang tot een Azure AD-resource te verlenen. Toegang wordt verleend door een roltoewijzing te maken en toegang kan worden ingetrokken door een roltoewijzing te verwijderen. De kern van een roltoewijzing bestaat uit drie elementen:

- Azure AD-gebruiker
- Roldefinitie
- Resourcebereik

U kunt [roltoewijzingen maken](custom-create.md) met behulp van Azure Portal, Azure AD PowerShell, of Graph API. U kunt ook [de roltoewijzingen weer geven](view-assignments.md).

Het volgende diagram toont een voorbeeld van een roltoewijzing. In dit voorbeeld is aan Chris Green de aangepaste rol App-registratiebeheerder toegewezen voor het bereik van de Contoso Widget Builder-app-registratie. De toewijzing verleent Chris alleen voor deze specifieke app-registratie de machtigingen van de rol App-registratiebeheerder.

![Roltoewijzing is een manier om machtigingen af te dwingen, en bestaat uit drie delen](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Beveiligings-principal

Een beveiligingsprincipal vertegenwoordigt de gebruiker die toegang moet krijgen tot Azure AD-resources. Een gebruiker is een persoon met een gebruikersprofiel in Azure Active Directory.

### <a name="role"></a>Rol

Een roldefinitie, of rol, is een verzameling machtigingen. Een roldefinitie beschijft de bewerkingen die kunnen worden uitgevoerd in Azure AD-resources, zoals maken, lezen, bijwerken en verwijderen. Er zijn twee typen gebruikersrollen in Azure AD:

- Ingebouwde rollen die door Microsoft zijn gemaakt en die niet kunnen worden gewijzigd.
- Aangepaste rollen die door de organisatie zijn gemaakt en beheerd.

### <a name="scope"></a>Bereik

Een bereik is de beperking van toegestane acties voor een bepaalde Azure AD-resource als onderdeel van een roltoewijzing. Wanneer u een rol toewijst, kunt u een bereik opgeven waarmee de toegang van een beheerder tot een specifieke resource wordt beperkt. Als u bijvoorbeeld aan een ontwikkelaar een aangepaste rol wilt verlenen, maar alleen om een specifieke toepassingsregistratie te beheren, kunt u de specifieke toepassingsregistratie als een bereik opnemen in de roltoewijzing.

## <a name="required-license-plan"></a>Vereiste licentieplan

Het gebruik van ingebouwde rollen in Azure AD is gratis, terwijl voor aangepaste rollen een Azure AD Premium P1-licentie is vereist. Zie [Algemeen beschikbare functies van de edities Gratis, Basic en Premium vergelijken](https://azure.microsoft.com/pricing/details/active-directory) als u een licentie zoekt die bij uw vereisten past.

## <a name="next-steps"></a>Volgende stappen

- [Inzicht in Azure AD-rollen](concept-understand-roles.md)
- Aangepaste roltoewijzingen maken met behulp van [Azure Portal, Azure AD PowerShell, en Graph API](custom-create.md)
- [Lijst met roltoewijzingen weergeven](view-assignments.md)