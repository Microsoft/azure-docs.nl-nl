---
title: Voorbereiden op wijziging van binnenkomend IP-adres
description: Als uw binnenkomende IP-adres wordt gewijzigd, leest u wat u moet doen om ervoor te zorgen dat uw app blijft werken na de wijziging.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 999511c4759ee761f028a61fbfaa0310ac93d9b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74672420"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Voor bereiding voor het wijzigen van een inkomend IP-adres

Als u een melding hebt ontvangen dat het inkomende IP-adres van uw Azure App Service-app wordt gewijzigd, volgt u de instructies in dit artikel.

## <a name="determine-if-you-have-to-do-anything"></a>Bepaal of u iets moet doen

* Optie 1: als uw App Service-app geen aangepast domein heeft, hoeft u geen actie te ondernemen.

* Optie 2: als er alleen een CNAME-record (DNS-record die verwijst naar een URI) is geconfigureerd in uw domein registratie Portal (DNS-provider of Azure DNS van derden), hoeft u geen actie te ondernemen.

* Optie 3: als een record (DNS-record die rechtstreeks naar uw IP-adres verwijst) is geconfigureerd in uw domein registratie Portal (DNS-provider of Azure DNS van derden), vervangt u het bestaande IP-adres door het nieuwe. U kunt het nieuwe IP-adres vinden door de instructies in de volgende sectie te volgen.

* Optie 4: als uw toepassing zich achter een load balancer, IP-filter of een ander IP-mechanisme bevindt waarvoor het IP-adres van uw app is vereist, vervangt u het bestaande IP-adres door het nieuwe. U kunt het nieuwe IP-adres vinden door de instructies in de volgende sectie te volgen.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Zoek het nieuwe inkomende IP-adres in de Azure Portal

Het nieuwe inkomende IP-adres dat wordt toegewezen aan uw app bevindt zich in de portal in het veld **virtuele IP-adres** . Dit nieuwe IP-adres en de oude zijn nu verbonden met uw app, en later wordt de verbinding met de oude verbroken.

1.  Open de [Azure Portal](https://portal.azure.com).

2.  Selecteer **app Services** in het navigatie menu aan de linkerkant.

3.  Selecteer uw App Service-app in de lijst.

1.  Als de app een functie-app is, raadpleegt u het [binnenkomende IP-adres van de functie-app](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  Klik onder de kop **instellingen** op **Eigenschappen** in het linkernavigatievenster en zoek de sectie met het **virtuele IP-adres**.

5. Kopieer het IP-adres en configureer uw domein record of IP-mechanisme opnieuw.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u een wijziging van een IP-adres voorbereidt die is geïnitieerd door Azure. Zie voor meer informatie over IP-adressen in Azure App Service [binnenkomende en uitgaande IP-adressen in azure app service](overview-inbound-outbound-ips.md).
