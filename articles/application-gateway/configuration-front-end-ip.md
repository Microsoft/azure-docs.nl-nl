---
title: IP-adres configuratie van de front-end van Azure-toepassing gateway
description: In dit artikel wordt beschreven hoe u het front-end IP-adres van de Azure-toepassing gateway configureert.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: eff63510f70dd7b4cdd522cc5a2a68096cda7166
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548713"
---
# <a name="application-gateway-front-end-ip-address-configuration"></a>Configuratie van het front-end-IP-adres Application Gateway

U kunt de toepassings gateway configureren voor een openbaar IP-adres, een privé IP-adres of beide. Een openbaar IP-adres is vereist wanneer u een back-end host dat clients toegang moeten hebben via internet via een Internet gerichte virtuele IP (VIP).

## <a name="public-and-private-ip-address-support"></a>Ondersteuning voor open bare en privé IP-adressen

Application Gateway v2 biedt momenteel geen ondersteuning voor de modus Private IP. De oplossing biedt ondersteuning voor de volgende combi Naties:

* Privé-IP-adres en openbaar IP-adres
* Alleen openbaar IP-adres

Zie [Veelgestelde vragen over Application Gateway](application-gateway-faq.yml#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address)voor meer informatie.


Een openbaar IP-adres is niet vereist voor een intern eind punt dat niet beschikbaar is op internet. Dit wordt ook wel een intern ILB-eind punt ( *Load-Balancer* ) of een persoonlijk frontend-IP-adres genoemd. Een Application Gateway ILB is handig voor interne line-of-business-toepassingen die niet worden blootgesteld aan Internet. Het is ook nuttig voor services en lagen in een toepassing met meerdere lagen binnen een beveiligings grens die niet beschikbaar is op internet, maar waarvoor Round Robin-taak verdeling, sessie persistentie of TLS-beëindiging vereist is.

Er wordt slechts één openbaar IP-adres en één privé-IP-adres ondersteund. U kiest het front-end-IP-adres wanneer u de toepassings gateway maakt.

- Voor een openbaar IP-adres kunt u een nieuw openbaar IP-adres maken of een bestaande open bare IP gebruiken op dezelfde locatie als de toepassings gateway. Zie [static versus Dynamic Public IP Address](./application-gateway-components.md#static-versus-dynamic-public-ip-address)(Engelstalig) voor meer informatie.

- Voor een privé-IP-adres kunt u een privé-IP-adres opgeven in het subnet waar de toepassings gateway is gemaakt. Als u er geen opgeeft, wordt er automatisch een wille keurig IP-adres geselecteerd in het subnet. Het IP-adres type dat u selecteert (statisch of dynamisch) kan niet later worden gewijzigd. Zie [een toepassings gateway maken met een interne Load Balancer](./application-gateway-ilb-arm.md)voor meer informatie.

Een front-end-IP-adres is gekoppeld aan een *listener*, waarmee wordt gecontroleerd op binnenkomende aanvragen op de front-end-IP.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over de configuratie van de listener](configuration-listeners.md)
