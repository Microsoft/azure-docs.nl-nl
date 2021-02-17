---
title: Een IP-Firewall configureren voor uw Azure Cognitive Search-service
titleSuffix: Azure Cognitive Search
description: Configureer IP-beheer beleid om de toegang tot uw Azure Cognitive Search-service te beperken.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 98a516e76d5e4f5c225873b934949759d7fcba8d
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100535328"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>IP-Firewall voor Azure Cognitive Search configureren

Azure Cognitive Search ondersteunt IP-regels voor ondersteuning van binnenkomende firewalls. Dit model bevat een extra beveiligingslaag voor uw zoek service, vergelijkbaar met de IP-regels die u vindt in een Azure Virtual Network-beveiligings groep. Met deze IP-regels kunt u de zoek service zo configureren dat deze alleen toegankelijk is vanaf een goedgekeurde set machines en/of Cloud Services. Voor toegang tot gegevens die zijn opgeslagen in uw zoek service van deze goedgekeurde sets computers en services, moet de aanroeper nog steeds een geldig autorisatie token presen teren.

U kunt IP-regels instellen in de Azure Portal, zoals beschreven in dit artikel. U kunt ook de [beheer rest API versie 2020-03-13](/rest/api/searchmanagement/), [Azure POWERSHELL](/powershell/module/az.search)of [Azure cli](/cli/azure/search)gebruiken.

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Een IP-Firewall configureren met behulp van de Azure Portal

Als u het beleid voor IP-toegangs beheer wilt instellen in de Azure Portal, gaat u naar de pagina van de Azure Cognitive Search-service en selecteert u **netwerken** in het navigatie menu. De netwerk verbinding met het eind punt moet **openbaar** zijn. Als uw verbinding is ingesteld op **privé**, hebt u alleen toegang tot uw zoek service via een persoonlijk eind punt.

![Scherm afbeelding die laat zien hoe u de IP-firewall in de Azure Portal configureert](./media/service-configure-firewall/azure-portal-firewall.png)

De Azure Portal biedt de mogelijkheid om IP-adressen en IP-adresbereiken op te geven in de CIDR-indeling. Een voor beeld van een CIDR-notatie is 8.8.8.0/24, waarmee de Ip's worden aangeduid van 8.8.8.0 naar 8.8.8.255.

> [!NOTE]
> Nadat u het beleid voor IP-toegangs beheer voor uw Azure Cognitive Search-service hebt ingeschakeld, worden alle aanvragen op het gegevens vlak van computers buiten de lijst met IP-adresbereiken geweigerd. Wanneer IP-regels zijn geconfigureerd, worden sommige functies van de Azure Portal uitgeschakeld. U kunt informatie over het service niveau weer geven en beheren, maar de toegang tot de portal voor het indexeren van gegevens en de verschillende onderdelen in de service, zoals de definities index, Indexer en vaardigheids, is beperkt om veiligheids redenen. Als alternatief voor de portal kunt u de [VS code-extensie](https://aka.ms/vscode-search) gebruiken om te communiceren met de verschillende onderdelen in de service.

### <a name="requests-from-your-current-ip"></a>Aanvragen van uw huidige IP-adres

Om de ontwikkeling te vereenvoudigen, helpt de Azure Portal u bij het identificeren en toevoegen van het IP-adres van uw client computer aan de lijst met toegestane apps. Apps die worden uitgevoerd op uw computer, kunnen vervolgens toegang krijgen tot uw Azure Cognitive Search-service.

De portal detecteert automatisch uw client-IP-adres. Dit kan het client-IP-adres van uw computer of netwerk gateway zijn. Zorg ervoor dat u dit IP-adres verwijdert voordat u uw werk belasting naar productie neemt.

Schakel **het IP-adres van uw client toevoegen** in om uw huidige IP toe te voegen aan de lijst met ip's. Selecteer vervolgens **Opslaan**.

![Scherm afbeelding van de configuratie van de IP-Firewall-instellingen om het huidige IP-adres toe te staan](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Problemen met een IP-toegangs beheer beleid oplossen

U kunt problemen met een IP-toegangs beheer beleid oplossen met behulp van de volgende opties:

### <a name="azure-portal"></a>Azure Portal

Als u een beleid voor IP-toegangs beheer inschakelt voor uw Azure Cognitive Search-service, worden alle aanvragen van computers buiten de lijst met IP-adresbereiken, inclusief de Azure Portal, geblokkeerd.  U kunt informatie over het service niveau weer geven en beheren, maar de toegang tot de portal voor het indexeren van gegevens en de verschillende onderdelen in de service, zoals de definities index, Indexer en vaardigheids, is beperkt om veiligheids redenen. 

### <a name="sdks"></a>SDK's

Wanneer u de Azure Cognitive Search-service opent met behulp van de SDK van computers die zich niet in de lijst met toegestane gegevens bevinden, wordt een algemeen **403 verboden** antwoord geretourneerd zonder aanvullende informatie. Controleer de lijst met toegestane IP-adressen voor uw account en zorg ervoor dat de juiste configuratie is bijgewerkt voor uw zoek service.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over het verkrijgen van toegang tot uw zoek service via een persoonlijke koppeling:

* [Een persoonlijk eind punt maken voor een beveiligde verbinding met Azure Cognitive Search](service-create-private-endpoint.md)