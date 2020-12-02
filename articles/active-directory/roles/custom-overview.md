---
title: Aangepaste beheerdersrollen in Azure Active Directory | Microsoft Docs
description: Ontdek hoe u aangepaste Azure AD-rollen in Azure Active Directory (Azure AD) gebruikt met op rollen gebaseerd toegangsbeheer en resourcebereiken.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0785d8070a60ae7594ea0b182a0238bf6b4b6a58
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95899459"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Aangepaste beheerdersrollen in Azure Active Directory (preview)

In dit artikel wordt uitleg gegeven over aangepaste Azure AD-rollen in Azure AD (Active Directory) met op rollen gebaseerd toegangsbeheer en resourcebereiken. Aangepaste Azure AD-rollen brengen de onderliggende machtigingen van de [ingebouwde rollen](permissions-reference.md) naar boven, zodat u uw eigen aangepaste rollen kunt maken en organiseren. Met deze benadering kunt u op een meer verfijnde manier toegang verlenen dan via ingebouwde rollen, wanneer dit nodig is. Deze eerste release van aangepaste Azure AD-rollen omvat de mogelijkheid om een rol te maken om machtigingen toe te wijzen voor het beheer van app-registraties. In de loop van de tijd worden extra machtigingen voor organisatieresources toegevoegd, zoals bedrijfstoepassingen, gebruikers en apparaten.  

Daarnaast bieden aangepaste Azure AD-rollen ook toewijzingen per resource, naast de meer traditionele toewijzingen in de hele organisatie. Deze benadering biedt u de mogelijkheid om toegang te verlenen voor het beheren van bepaalde resources (bijvoorbeeld één app-registratie), zonder dat u toegang verleent tot alle resources (alle app-registraties).

Op rollen gebaseerd toegangsbeheer van Azure AD is een openbare preview-functie van Azure AD en is beschikbaar met elk betaald Azure AD-licentieplan. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="understand-azure-ad-role-based-access-control"></a>Uitleg over op rollen gebaseerd toegangsbeheer van Azure AD

Het verlenen van machtigingen met behulp van aangepaste Azure AD-rollen is een proces in twee stappen: het maken van een aangepaste roldefinitie en vervolgens het toewijzen ervan met behulp van een roltoewijzing. Een aangepaste roldefinitie is een verzameling machtigingen die u toevoegt uit een vooraf ingestelde lijst. Deze machtigingen zijn dezelfde machtigingen die worden gebruikt in de ingebouwde rollen.  

Zodra u de roldefinitie hebt gemaakt, kunt u deze toewijzen aan een gebruiker door een roltoewijzing te maken. Met een roltoewijzing worden machtigingen in een roldefinitie aan de gebruiker verleend voor een opgegeven bereik. Met dit proces in twee stappen kunt u één roldefinitie maken en deze meerdere keren toewijzen voor verschillende bereiken. Met een bereik wordt de set Azure AD-resources gedefinieerd waartoe het rollid toegang heeft. Het meest voorkomende bereik is het bereik voor de hele organisatie (organisatiebreed). Een aangepaste rol kan worden toegewezen voor het organisatiebrede bereik. Dit betekent dat de rolmachtigingen van het rollid gelden voor alle resources in de organisatie. Een aangepaste rol kan ook worden toegewezen voor een objectbereik. Een voorbeeld van een objectbereik is één toepassing. Dezelfde rol kan aan de ene gebruiker worden toegewezen voor alle toepassingen in de organisatie, en vervolgens aan een andere gebruiker met een bereik van alleen de Contoso Expense Reports-app.  

De werking van ingebouwde Azure AD-rollen en aangepaste rollen is vergelijkbaar met [Azure RBAC (op rollen gebaseerd toegangsbeheer)](../../active-directory-b2c/overview.md). Het [verschil tussen deze twee systemen voor op rollen gebaseerd toegangsbeheer](../../role-based-access-control/rbac-and-directory-admin-roles.md) is dat met Azure RBAC de toegang tot Azure-resources, zoals virtuele machines of opslag, wordt beheerd met Azure Resource Management, en dat met aangepaste Azure AD-rollen de toegang tot Azure AD-resources wordt beheerd met behulp van Graph API. Beide systemen maken gebruik van het concept van roldefinities en roltoewijzingen. U kunt geen Azure AD RBAC-machtigingen opnemen in Azure-rollen en vice versa.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Hoe Azure AD bepaalt of een gebruiker toegang tot een resource heeft

Hier volgen de stappen op hoog niveau die in Azure AD worden gebruikt om te bepalen of u toegang hebt tot een beheerresource. Gebruik deze informatie om problemen met toegang op te lossen.

1. Een gebruiker (of service-principal) verkrijgt een token voor het Microsoft Graph- of Azure AD Graph-eindpunt.

1. De gebruiker doet een API-aanroep naar Azure AD (Active Directory) via Microsoft Graph of Azure AD Graph met behulp van het uitgegeven token.

1. Afhankelijk van de omstandigheden wordt in Azure AD een van de volgende acties uitgevoerd:

    - Evalueren van de rollidmaatschappen van de gebruiker op basis van de [wids-claim](../../active-directory-b2c/access-tokens.md) in het toegangstoken van de gebruiker.
    - Ophalen van alle roltoewijzingen die van toepassing zijn op de gebruiker, hetzij rechtstreeks of via groepslidmaatschap, voor de resource waarvoor de actie wordt uitgevoerd.

1. Azure AD bepaalt of de actie in de API-aanroep is opgenomen in de rollen die de gebruiker heeft voor deze resource.
1. Als de gebruiker geen rol met de actie heeft in het aangevraagde bereik, wordt er geen toegang verleend. Anders wordt toegang verleend.

### <a name="role-assignments"></a>Roltoewijzingen

Een roltoewijzing is het object op basis waarvan een roldefinitie is gekoppeld aan een gebruiker voor een bepaald bereik, om toegang tot een Azure AD-resource te verlenen. Toegang wordt verleend door een roltoewijzing te maken en toegang kan worden ingetrokken door een roltoewijzing te verwijderen. De kern van een roltoewijzing bestaat uit drie elementen:

- Gebruiker (een persoon met een gebruikersprofiel in Azure Active Directory)
- Roldefinitie
- Resourcebereik

U kunt [roltoewijzingen maken](custom-create.md) met behulp van Azure Portal, Azure AD PowerShell, of Graph API. U kunt ook [de toewijzingen voor een aangepaste rol bekijken](custom-view-assignments.md#view-the-assignments-of-a-role).

Het volgende diagram toont een voorbeeld van een roltoewijzing. In dit voorbeeld is aan Chris Green de aangepaste rol App-registratiebeheerder toegewezen voor het bereik van de Contoso Widget Builder-app-registratie. De toewijzing verleent Chris alleen voor deze specifieke app-registratie de machtigingen van de rol App-registratiebeheerder.

![Roltoewijzing is een manier om machtigingen af te dwingen, en bestaat uit drie delen](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Beveiligings-principal

Een beveiligingsprincipal vertegenwoordigt de gebruiker die toegang moet krijgen tot Azure AD-resources. Een *gebruiker* is een persoon met een gebruikersprofiel in Azure Active Directory.

### <a name="role"></a>Rol

Een roldefinitie, of rol, is een verzameling machtigingen. Een roldefinitie beschijft de bewerkingen die kunnen worden uitgevoerd in Azure AD-resources, zoals maken, lezen, bijwerken en verwijderen. Er zijn twee typen gebruikersrollen in Azure AD:

- Ingebouwde rollen die door Microsoft zijn gemaakt en die niet kunnen worden gewijzigd.
- Aangepaste rollen die door de organisatie zijn gemaakt en beheerd.

### <a name="scope"></a>Bereik

Een bereik is de beperking van toegestane acties voor een bepaalde Azure AD-resource als onderdeel van een roltoewijzing. Wanneer u een rol toewijst, kunt u een bereik opgeven waarmee de toegang van een beheerder tot een specifieke resource wordt beperkt. Als u bijvoorbeeld aan een ontwikkelaar een aangepaste rol wilt verlenen, maar alleen om een specifieke toepassingsregistratie te beheren, kunt u de specifieke toepassingsregistratie als een bereik opnemen in de roltoewijzing.

  > [!Note]
  > Aangepaste rollen kunnen worden toegewezen voor een mapbereik en voor een resourcebereik. Ze kunnen nog niet worden toegewezen voor het bereik van een beheereenheid.
  > Ingebouwde rollen kunnen worden toegewezen voor een mapbereik, en in sommige gevallen ook voor het bereik van een beheereenheid. Ze kunnen nog niet worden toegewezen voor een Azure AD-resourcebereik.

## <a name="required-license-plan"></a>Vereiste licentieplan

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Volgende stappen

- Aangepaste roltoewijzingen maken met behulp van [Azure Portal, Azure AD PowerShell, en Graph API](custom-create.md)
- [De toewijzingen voor een aangepaste rol bekijken](custom-view-assignments.md)
