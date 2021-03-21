---
title: Ingebouwde rollen Windows virtueel bureau blad-Azure
description: Een overzicht van ingebouwde rollen voor virtuele Windows-Bureau bladen die beschikbaar zijn voor Azure RBAC.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1120db685c54ff062f03aca9002bf77af549bc26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582116"
---
# <a name="built-in-roles-for-windows-virtual-desktop"></a>Ingebouwde rollen voor Windows virtueel bureau blad

Windows virtueel bureau blad maakt gebruik van op rollen gebaseerd toegangs beheer (RBAC) van Azure om rollen toe te wijzen aan gebruikers en beheerders. Deze rollen bieden beheerders machtigingen om bepaalde taken uit te voeren. Zie [ingebouwde rollen van Azure](../role-based-access-control/built-in-roles.md)voor meer informatie over ingebouwde rollen voor Azure RBAC.

De standaard ingebouwde rollen voor Azure zijn eigenaar, bijdrager en lezer. Windows virtueel bureau blad heeft echter aanvullende functies waarmee u beheer rollen kunt scheiden voor hostgroepen, app-groepen en werk ruimten. Met deze schei ding kunt u nauw keuriger beheer taken uitvoeren. Deze rollen worden genoemd in overeenstemming met de standaard rollen en de methodologie met minimale bevoegdheden van Azure.

Het virtuele bureau blad van Windows heeft geen specifieke rol van eigenaar. U kunt echter een standaard Eigenaargroep voor de service objecten gebruiken.

## <a name="desktop-virtualization-contributor"></a>Inzender voor bureau blad-virtualisatie

Met de rol bureau blad-Virtualisatiehost kunt u alle aspecten van de implementatie beheren. Er wordt echter geen toegang verleend tot reken resources. U hebt ook de rol beheerder van gebruikers toegang nodig om app-groepen te publiceren naar gebruikers of gebruikers groepen.


- Micro soft. DesktopVirtualization/\* 
- Micro soft. resources/abonnementen/resourceGroups/lezen
- Micro soft. resources/implementaties/\*
- Micro soft. Authorization/ \* /Read
- Micro soft. Insights/alertRules/\*
- Micro soft. support/\*

## <a name="desktop-virtualization-reader"></a>Desktop Virtualization-lezer

Met de rol Desktop Virtualization Reader kunt u alles in de implementatie weer geven, maar kunt u geen wijzigingen aanbrengen.

- Micro soft. DesktopVirtualization/ \* /Read
- Micro soft. resources/abonnementen/resourceGroups/lezen
- Micro soft. resources/implementaties/lezen
- Micro soft. Authorization/ \* /Read
- Micro soft. Insights/alertRules/\*
- Micro soft. support/\*

## <a name="host-pool-contributor"></a>Inzender van hostgroep

Met de rol Inzender van hostgroep kunt u alle aspecten van hostgroepen beheren, met inbegrip van toegang tot bronnen. U hebt een extra rol Inzender, Inzender voor virtuele machines, nodig om virtuele machines te maken. U hebt AppGroup en Inzender rollen nodig om een hostgroep te maken met behulp van de portal, of u kunt de rol Inzender voor bureau blad-virtualisatie gebruiken.

In de volgende lijst wordt beschreven welke machtigingen deze rol kan gebruiken:

- Micro soft. DesktopVirtualization/hostpools/\*
- Micro soft. resources/abonnementen/resourceGroups/lezen
- Micro soft. resources/implementaties/\*
- Micro soft. Authorization/ \* /Read
- Micro soft. Insights/alertRules/\*
- Micro soft. support/\*

## <a name="host-pool-reader"></a>Hostgroep-lezer

Met de rol van de hostgroep lezers kunt u alles in de hostgroep weer geven, maar kunt u geen wijzigingen aanbrengen.

- Micro soft. DesktopVirtualization/hostpools/ \* /Read
- Micro soft. resources/abonnementen/resourceGroups/lezen
- Micro soft. resources/implementaties/lezen
- Micro soft. Authorization/ \* /Read
- Micro soft. Insights/alertRules/\*
- Micro soft. support/\*

## <a name="application-group-contributor"></a>Inzender voor toepassings groepen

Met de rol Inzender toepassings groep kunt u alle aspecten van app-groepen beheren. Als u app-groepen wilt publiceren naar gebruikers of gebruikers groepen, hebt u de rol beheerder voor gebruikers toegang nodig.

In de volgende lijst wordt beschreven welke machtigingen deze rol kan gebruiken:

- Micro soft. DesktopVirtualization/applicationgroups/\*
- Micro soft. DesktopVirtualization/hostpools/lezen
- Micro soft. DesktopVirtualization/hostpools/sessionhosts/lezen
- Micro soft. resources/abonnementen/resourceGroups/lezen
- Micro soft. resources/implementaties/\*
- Micro soft. Authorization/ \* /Read
- Micro soft. Insights/alertRules/\*
- Micro soft. support/\*

## <a name="application-group-reader"></a>Lezer van toepassings groep

Met de rol Lezer van toepassings groep kunt u alles in de app-groep weer geven en kunt u geen wijzigingen aanbrengen.

In de volgende lijst wordt beschreven welke machtigingen deze rol kan gebruiken:

- Micro soft. DesktopVirtualization/applicationgroups/ \* /Read
- Micro soft. DesktopVirtualization/applicationgroups/lezen
- Micro soft. DesktopVirtualization/hostpools/lezen
- Micro soft. DesktopVirtualization/hostpools/sessionhosts/lezen
- Micro soft. resources/abonnementen/resourceGroups/lezen
- Micro soft. resources/implementaties/lezen
- Micro soft. Authorization/ \* /Read
- Micro soft. Insights/alertRules/\*
- Micro soft. support/\*

## <a name="workspace-contributor"></a>Inzender voor werk ruimten

Met de rol Inzender voor werk ruimten kunt u alle aspecten van werk ruimten beheren. Als u informatie wilt ophalen over toepassingen die aan de app-groepen zijn toegevoegd, moet u ook de rol van de toepassings groep lezer toewijzen.

In de volgende lijst wordt beschreven welke machtigingen deze rol kan gebruiken:

- Micro soft. DesktopVirtualization/werk ruimten/\*
- Micro soft. DesktopVirtualization/applicationgroups/lezen
- Micro soft. resources/abonnementen/resourceGroups/lezen
- Micro soft. resources/implementaties/\*
- Micro soft. Authorization/ \* /Read
- Micro soft. Insights/alertRules/\*
- Micro soft. support/\*

## <a name="workspace-reader"></a>Werkruimte lezer

Met de rol werkruimte lezer kunt u alles in de werk ruimte weer geven, maar kunt u geen wijzigingen aanbrengen.

In de volgende lijst wordt beschreven welke machtigingen deze rol kan gebruiken:

- Micro soft. DesktopVirtualization/werk ruimten/lezen
- Micro soft. DesktopVirtualization/applicationgroups/lezen
- Micro soft. resources/abonnementen/resourceGroups/lezen
- Micro soft. resources/implementaties/lezen
- Micro soft. Authorization/ \* /Read
- Micro soft. Insights/alertRules/\*
- Micro soft. support/\*

## <a name="user-session-operator"></a>Gebruikers sessie operator

Met de rol gebruikers sessie operator kunt u berichten verzenden, sessies verbreken en de functie Logoff gebruiken om sessies van de sessiehost te ondertekenen. Met deze rol kunt u echter geen Session Host-beheer uitvoeren zoals het verwijderen van een sessiehost, het wijzigen van de afvoer modus, enzovoort. Met deze rol kunnen toewijzingen worden weer geven, maar kunnen beheerders niet worden gewijzigd. U wordt aangeraden deze rol toe te wijzen aan specifieke hostgroepen. Als u deze machtiging op een niveau van een resource groep verleent, heeft de beheerder Lees machtigingen voor alle hostgroepen onder een resource groep.

In de volgende lijst wordt beschreven welke machtigingen deze rol kan gebruiken:

- Micro soft. DesktopVirtualization/hostpools/lezen
- Micro soft. DesktopVirtualization/hostpools/sessionhosts/lezen
- Micro soft. DesktopVirtualization/hostpools/sessionhosts/usersessions/\*
- Micro soft. resources/abonnementen/resourceGroups/lezen
- Micro soft. resources/implementaties/lezen
- Micro soft. Authorization/ \* /Read
- Micro soft. Insights/alertRules/\*
- Micro soft. support/\*

## <a name="session-host-operator"></a>Session Host-operator

Met de rol sessiehost host kunt u sessie-hosts weer geven en verwijderen, evenals de modus voor het wijzigen van de afvoer. Ze kunnen geen sessie-hosts toevoegen met behulp van de Azure Portal omdat ze geen schrijf machtiging hebben voor objecten van een hostgroep. Als het registratie token geldig is (gegenereerd en niet verlopen), kunt u deze rol gebruiken om sessie-hosts toe te voegen aan de hostgroep buiten Azure Portal als de beheerder reken machtigingen heeft via de rol van de virtuele machine.

In de volgende lijst wordt beschreven welke machtigingen deze rol kan gebruiken:

- Micro soft. DesktopVirtualization/hostpools/lezen
- Micro soft. DesktopVirtualization/hostpools/sessionhosts/\*
- Micro soft. resources/abonnementen/resourceGroups/lezen
- Micro soft. resources/implementaties/lezen
- Micro soft. Authorization/ \* /Read
- Micro soft. Insights/alertRules/\*
- Micro soft. support/\*
