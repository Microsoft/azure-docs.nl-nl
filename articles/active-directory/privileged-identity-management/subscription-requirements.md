---
title: Licentie vereisten voor het gebruik van Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Beschrijft de licentie vereisten voor het gebruik van Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 04/05/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d0428f73c4df3d97002def03823ff8cced1d647
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552628"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Licentievereisten voor het gebruik van Privileged Identity Management

Als u Azure Active Directory (Azure AD) Privileged Identity Management (PIM) wilt gebruiken, moet een directory een geldige licentie hebben. Daarnaast moeten de licenties aan de beheerders en relevante gebruikers worden toegewezen. In dit artikel worden de licentie vereisten beschreven voor het gebruik van Privileged Identity Management.

## <a name="valid-licenses"></a>Geldige licenties

U moet [!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)] PIM en alle instellingen ervan gebruiken. Op dit moment kunt u een toegangs beoordeling voor service-principals met toegang tot Azure AD en Azure-resource rollen (preview) bereiken met een Azure Active Directory Premium P2-editie actief is in uw Tenant. Het licentie model voor service-principals wordt voltooid voor algemene Beschik baarheid van deze functie. mogelijk zijn er extra licenties nodig.

## <a name="licenses-you-must-have"></a>Licenties die u nodig hebt

Zorg ervoor dat uw directory ten minste evenveel Azure AD Premium P2-licenties heeft als u werknemers heeft die de volgende taken uitvoeren:

- Gebruikers die in aanmerking komen voor Azure AD of Azure-rollen die worden beheerd met PIM
- Gebruikers die zijn toegewezen als in aanmerking komende leden of eigen aren van geprivilegieerde toegangs groepen
- Gebruikers kunnen activerings aanvragen goed keuren of afwijzen in PIM
- Gebruikers die zijn toegewezen aan een toegangs beoordeling
- Gebruikers die toegangs beoordelingen uitvoeren

Azure AD Premium P2-licenties zijn **niet** vereist voor de volgende taken:

- Er zijn geen licenties vereist voor gebruikers die PIM instellen, beleids regels configureren, waarschuwingen ontvangen en toegangs beoordelingen instellen.

Zie [Licenties toewijzen en verwijderen met behulp van het Azure Active Directory-portal](../fundamentals/license-users-groups.md) voor meer informatie over licenties.

## <a name="example-license-scenarios"></a>Voorbeelden van licentiescenario's

Hier volgen enkele voorbeelden van licentiescenario's om te helpen bepalen hoeveel licenties u nodig heeft.

| Scenario | Berekening | Aantal licenties |
| --- | --- | --- |
| Woodgrove Bank heeft 10 beheerders voor verschillende afdelingen en twee globale beheerders die PIM configureren en beheren. Ze maken vijf beheerders in aanmerking. | Vijf licenties voor de beheerders die in aanmerking komen | 5 |
| Grafisch ontwerp Institute heeft 25 beheerders waarvan 14 wordt beheerd via PIM. Activering van rol vereist goed keuring en er zijn drie verschillende gebruikers in de organisatie die activeringen kunnen goed keuren. | 14 licenties voor de in aanmerking komende rollen + drie goed keurders | 17 |
| Contoso heeft 50 beheerders van welke 42 via PIM worden beheerd. Activering van rol vereist goed keuring en er zijn vijf verschillende gebruikers in de organisatie die activeringen kunnen goed keuren. Contoso houdt ook maandelijkse beoordelingen bij van gebruikers die zijn toegewezen aan beheerders rollen en controleurs zijn de managers van de gebruikers waarvan zes geen beheerder rollen hebben die worden beheerd door PIM. | 42 licenties voor de in aanmerking komende rollen + vijf goed keurders + zes revisoren | 53 |

## <a name="when-a-license-expires"></a>Wanneer een licentie verloopt

Als een Azure AD Premium P2, EMS E5 of een proef licentie verloopt, zijn Privileged Identity Management-functies niet meer beschikbaar in uw Directory:

- Permanente roltoewijzingen aan Azure AD-rollen worden niet beïnvloed.
- De Privileged Identity Management-service in de Azure Portal en de Graph API-cmdlets en Power shell-interfaces van Privileged Identity Management zijn niet langer beschikbaar voor gebruikers om geprivilegieerde rollen te activeren, bevoorrechte toegang te beheren of toegangs beoordelingen van geprivilegieerde rollen uit te voeren.
- De in aanmerking komende roltoewijzingen van Azure AD-rollen worden verwijderd omdat gebruikers geen bevoegde rollen meer kunnen activeren.
- Alle doorlopende toegangs beoordelingen van Azure AD-rollen worden beëindigd en Privileged Identity Management configuratie-instellingen worden verwijderd.
- Privileged Identity Management verzendt geen e-mail berichten meer over roltoewijzings wijzigingen.

## <a name="next-steps"></a>Volgende stappen

- [Privileged Identity Management implementeren](pim-deployment-plan.md)
- [Aan de slag met Privileged Identity Management](pim-getting-started.md)
- [Rollen die u niet kunt beheren in Privileged Identity Management](pim-roles.md)
- [Een toegangs beoordeling van Azure-resource rollen maken in PIM](pim-resource-roles-start-access-review.md)
- [Een toegangs beoordeling van Azure AD-rollen maken in PIM](pim-how-to-start-security-review.md)
