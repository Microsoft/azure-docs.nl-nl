---
title: Aanbevolen procedures voor Azure RBAC
description: Aanbevolen procedures voor het gebruik van op rollen gebaseerd toegangs beheer van Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: 00fd00112dad9efc31cea83f69bb458a8e1ca935
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617371"
---
# <a name="best-practices-for-azure-rbac"></a>Aanbevolen procedures voor Azure RBAC

In dit artikel worden enkele aanbevolen procedures beschreven voor het gebruik van Azure RBAC (op rollen gebaseerd toegangs beheer). Deze aanbevolen procedures zijn afgeleid van onze ervaring met Azure RBAC en de ervaringen van klanten, zoals uzelf.

## <a name="only-grant-the-access-users-need"></a>Alleen de gebruikers die toegang nodig hebben, toestaan

Met op rollen gebaseerd toegangsbeheer van Azure kunt u taken scheiden binnen uw team en alleen de mate van toegang verlenen aan gebruikers die nodig is om de taken uit te voeren. In plaats van iedereen onbeperkte machtigingen te verlenen in uw Azure-abonnement of -resources, kunt u zelf bepalen welke acties er met een bepaalde bereik zijn toegestaan.

Het is een aanbevolen procedure om tijdens het plannen van een strategie voor toegangsbeheer gebruikers minimale bevoegdheden te verlenen om hun werk gedaan te krijgen. Vermijd het toewijzen van bredere rollen in bredere bereiken, zelfs als deze in eerste instantie handiger lijkt te zijn. Wanneer u aangepaste rollen maakt, moet u alleen de benodigde machtigingen voor gebruikers bevatten. Door de rollen en bereiken te beperken, beperkt u de resources die risico lopen als de beveiligings-principal ooit is aangetast.

Het volgende diagram toont een voorgesteld patroon voor het gebruik van Azure RBAC.

![Azure RBAC en minimale bevoegdheid](./media/best-practices/rbac-least-privilege.png)

Voor informatie over het toevoegen van roltoewijzingen, raadpleegt u [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Het aantal abonnements eigenaren beperken

U moet een maximum van 3 abonnements eigenaren hebben om de kans te verkleinen dat er inbreuk is op een verzwakte eigenaar. Deze aanbeveling kan worden bewaakt in Azure Security Center. Zie [Security aanbevelingen-a Reference Guide (Engelstalig](../security-center/recommendations-reference.md)) voor andere aanbevelingen voor identiteits-en toegangs beveiliging in Security Center.

## <a name="use-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management gebruiken

Als u geprivilegieerde accounts van kwaad aardige aanvallen wilt beveiligen, kunt u Azure Active Directory Privileged Identity Management (PIM) gebruiken om de belichtings tijd van bevoegdheden te verlagen en uw zicht baarheid te verg Roten met rapporten en waarschuwingen. PIM helpt geprivilegieerde accounts te beveiligen door just-in-time privileged toegang te bieden tot Azure AD en Azure-resources. De toegang kan worden toegewezen aan de tijds limiet, waarna de bevoegdheden automatisch worden ingetrokken. 

Zie [Wat is Azure AD privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Problemen met Azure RBAC oplossen](troubleshooting.md)