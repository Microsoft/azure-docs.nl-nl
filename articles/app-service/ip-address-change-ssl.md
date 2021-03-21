---
title: De wijziging van het SSL-IP-adres wordt voor bereid
description: Als uw SSL-IP-adres wordt gewijzigd, kunt u lezen wat u moet doen om ervoor te zorgen dat uw app blijft werken na de wijziging.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: dcfe11bcab25f6267a557de5faf7befab467bc29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020956"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Voor bereiding voor het wijzigen van een SSL-IP-adres

Als u een melding hebt ontvangen dat het SSL-IP-adres van uw Azure App Service-app wordt gewijzigd, volgt u de instructies in dit artikel om het bestaande SSL-IP-adres vrij te geven en een nieuw account toe te wijzen.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>SSL-IP-adressen vrijgeven en nieuwe toewijzen

1.  Open de [Azure Portal](https://portal.azure.com).

2.  Selecteer **app Services** in het navigatie menu aan de linkerkant.

3.  Selecteer uw App Service-app in de lijst.

4.  Klik onder de header **instellingen** op **SSL-instellingen** in het linkernavigatievenster.

1. Selecteer in de sectie TLS/SSL-bindingen de record met de hostnaam. In de editor die wordt geopend, kiest u **SNI SSL** in de vervolg keuzelijst **SSL-type** en klikt u op **binding toevoegen**. Wanneer het bericht geslaagde bewerking wordt weer gegeven, is het bestaande IP-adres vrijgegeven.

6.  Selecteer in de sectie **SSL-bindingen** nogmaals hetzelfde hostnaam-record met het certificaat. In de editor die wordt geopend, kiest u op **IP gebaseerd SSL** in de vervolg keuzelijst **SSL-type** en klikt u op **binding toevoegen**. Wanneer het bericht geslaagde bewerking wordt weer gegeven, hebt u een nieuw IP-adres verkregen.

7.  Als een A-record (DNS-record die rechtstreeks naar uw IP-adres verwijst) is geconfigureerd in uw domein registratie Portal (DNS-provider of Azure DNS van derden), vervangt u het bestaande IP-adres door de zojuist gegenereerde naam. U kunt het nieuwe IP-adres vinden door de instructies in de volgende sectie te volgen.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Het nieuwe SSL-IP-adres zoeken in azure Portal

1.  Wacht enkele minuten en open vervolgens de [Azure Portal](https://portal.azure.com).

2.  Selecteer **app Services** in het navigatie menu aan de linkerkant.

3.  Selecteer uw App Service-app in de lijst.

4.  Klik onder de kop **instellingen** op **Eigenschappen** in het linkernavigatievenster en zoek de sectie met het **virtuele IP-adres**.

5. Kopieer het IP-adres en configureer uw domein record of IP-mechanisme opnieuw.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u een wijziging van een IP-adres voorbereidt die is geïnitieerd door Azure. Zie voor meer informatie over IP-adressen in Azure App Service [binnenkomende en uitgaande IP-adressen in azure app service](overview-inbound-outbound-ips.md).
