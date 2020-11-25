---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: cec44bbabdb7d528c30a8d3396b819f2eb3c5386
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999427"
---
De functie is eenvoudig in te stellen, maar dit betekent niet dat uw ervaring geen probleem is. Als u problemen ondervindt met het verkrijgen van toegang tot uw gewenste eind punt, kunt u een aantal hulpprogram ma's gebruiken om de connectiviteit vanuit de app-console te testen. Er zijn twee consoles die u kunt gebruiken. De ene is de kudu-console en de andere is de console in de Azure Portal. Als u de kudu-console wilt bereiken vanuit uw app, gaat u naar **hulpprogram ma's**  >  **kudu**. U kunt de Kudo-console ook bereiken op [site naam]. scm. azurewebsites. net. Nadat de website is geladen, gaat u naar het tabblad **debug console** . Als u vanuit uw app naar de door Azure Portal gehoste console wilt gaan, gaat u naar de console **hulpprogram ma's**  >  **Console**.

#### <a name="tools"></a>Hulpprogramma's
In systeem eigen Windows-apps werken de hulpprogram ma's **ping**, **nslookup** en **tracert** niet via de-console vanwege beveiligings beperkingen (ze werken in [aangepaste Windows-containers](../articles/app-service/quickstart-custom-container.md)). Als u de void wilt vullen, worden er twee afzonderlijke hulpprogram ma's toegevoegd. We hebben een hulp programma toegevoegd met de naam **nameresolver.exe** om de DNS-functionaliteit te testen. De syntaxis is:

```console
nameresolver.exe hostname [optional: DNS Server]
```

U kunt nameresolver gebruiken om de hostnamen te controleren waarvan uw app afhankelijk is. Op deze manier kunt u testen of er iets mis is met uw DNS of dat u mogelijk geen toegang hebt tot uw DNS-server. U kunt de DNS-server zien die uw app gebruikt in de-console door te kijken naar de omgevings variabelen WEBSITE_DNS_SERVER en WEBSITE_DNS_ALT_SERVER.

> [!NOTE]
> nameresolver.exe werkt momenteel niet in aangepaste Windows-containers.
>

U kunt het volgende hulp programma gebruiken om te testen op TCP-connectiviteit met een combi natie van host en poort. Dit hulp programma heet **tcpping** en de syntaxis is:

```console
tcpping.exe hostname [optional: port]
```

Het **tcpping** -hulp programma vertelt u of u een specifieke host en poort kunt bereiken. Het kan alleen succes weer geven als er een toepassing luistert op de combi natie van host en poort en er is netwerk toegang vanuit uw app naar de opgegeven host en poort.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Fout opsporing van toegang tot door virtueel netwerk gehoste resources
Een aantal dingen kan verhinderen dat uw app een specifieke host en poort bereikt. In de meeste gevallen is het een van de volgende dingen:

* **Een firewall is in de weg.** Als u een firewall op de gewenste manier hebt, bereikt u de TCP-time-out. In dit geval is de TCP-time-out 21 seconden. Gebruik het hulp programma **tcpping** om de connectiviteit te testen. TCP-time-outs kunnen worden veroorzaakt door veel meer dan firewalls, maar u kunt hier beginnen.
* **DNS is niet toegankelijk.** De DNS-time-out is 3 seconden per DNS-server. Als u twee DNS-servers hebt, is de time-out 6 seconden. Gebruik nameresolver om te controleren of DNS werkt. U kunt nslookup niet gebruiken, omdat dat geen gebruik maakt van de DNS waarvoor uw virtuele netwerk is geconfigureerd. Als dat niet mogelijk is, kunt u een firewall hebben of NSG de toegang tot DNS blok keren of kan deze worden uitgeschakeld.

Als dat niet het geval is, zoekt u eerst naar zaken als:

**Regionale VNet-integratie**
* Is uw doel een niet-RFC1918-adres en hebt u geen WEBSITE_VNET_ROUTE_ALL ingesteld op 1?
* Is er een NSG die uitkomend verkeer van uw integratie subnet blokkeert?
* Als u over Azure ExpressRoute of een VPN gaat, is uw on-premises gateway geconfigureerd voor het routeren van verkeer naar Azure? Als u eind punten in uw virtuele netwerk, maar niet on-premises, kunt bereiken, controleert u uw routes.
* Hebt u voldoende machtigingen voor het instellen van delegering op het subnet met integratie? Tijdens de configuratie van de regionale VNet-integratie wordt het integratie-subnet overgedragen aan micro soft. web-server farms. De VNet-integratie GEBRUIKERSINTERFACE delegeert het subnet automatisch naar micro soft. web-server farms. Als uw account niet voldoende netwerk machtigingen heeft om de overdracht in te stellen, moet u iemand die kenmerken kan instellen op het subnet voor integratie om het subnet te delegeren. Als u het subnet met integratie hand matig wilt overdragen, gaat u naar de gebruikers interface van het Azure Virtual Network-subnet en stelt u de overdracht in voor micro soft. web-server farms.

**Gateway-vereiste VNet-integratie**
* Is het punt-naar-site-adres bereik in de RFC 1918-bereiken (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Wordt de gateway in de portal weer gegeven? Als uw gateway niet beschikbaar is, zet u deze terug.
* Worden certificaten weer gegeven als gesynchroniseerd, of vermoedt u dat de netwerk configuratie is gewijzigd?  Als uw certificaten niet zijn gesynchroniseerd of als u vermoedt dat er een wijziging is aangebracht in de configuratie van uw virtuele netwerk die niet is gesynchroniseerd met uw ASPs, selecteert u **netwerk synchroniseren**.
* Als u een VPN-verbinding wilt maken, is de on-premises gateway die is geconfigureerd voor het routeren van verkeer naar Azure? Als u eind punten in uw virtuele netwerk, maar niet on-premises, kunt bereiken, controleert u uw routes.
* Probeert u een gateway voor samen werking te gebruiken die beide Point-to-site-en ExpressRoute ondersteunt? Gateways voor samen werking worden niet ondersteund met VNet-integratie.

Fout opsporing in netwerk problemen is een uitdaging omdat u niet kunt zien wat de toegang tot een specifieke host blokkeert: poort combinatie. Enkele oorzaken zijn:

* U hebt een firewall op uw host die de toegang tot de toepassings poort van uw punt-naar-site-IP-bereik voor komt. Voor kruisende subnetten is vaak open bare toegang vereist.
* De doelhost is niet beschikbaar.
* Uw toepassing is niet beschikbaar.
* U had een onjuist IP-adres of hostnaam.
* Uw toepassing luistert op een andere poort dan verwacht. U kunt uw proces-ID met de luister poort overeenkomen met ' netstat-Aon ' op de host van het eind punt.
* Uw netwerk beveiligings groepen worden zodanig geconfigureerd dat de toegang tot uw toepassingshost en poort van uw punt-naar-site-IP-bereik wordt voor komen.

U weet niet welk adres uw app daad werkelijk gebruikt. Dit kan elk adres zijn in het integratie subnet of het adres bereik van punt-naar-site. u moet dus toegang tot het hele adres bereik toestaan.

Aanvullende stappen voor fout opsporing zijn onder andere:

* Maak verbinding met een VM in uw virtuele netwerk en probeer uw bronhost: poort te bereiken. Als u wilt testen op TCP-toegang, gebruikt u de Power shell **-opdracht test-NetConnection**. De syntaxis is:

```powershell
test-netconnection hostname [optional: -Port]
```

* Een toepassing op een virtuele machine weer geven en de toegang tot die host en poort testen vanuit de-console vanuit uw app met behulp van **tcpping**.

#### <a name="on-premises-resources"></a>On-premises resources ####

Als uw app een on-premises resource niet kan bereiken, controleert u of u de bron kunt bereiken vanuit uw virtuele netwerk. Gebruik de Power shell **-opdracht test-NetConnection** om te controleren op TCP-toegang. Als uw virtuele machine uw on-premises resource niet kan bereiken, is uw VPN-of ExpressRoute-verbinding mogelijk niet juist geconfigureerd.

Als uw virtuele netwerk-gehoste VM uw on-premises systeem kan bereiken, maar uw app niet kan, kan dit een van de volgende oorzaken hebben:

* Uw routes zijn niet geconfigureerd met uw subnet-of punt-naar-site-adresbereiken in uw on-premises gateway.
* Uw netwerk beveiligings groepen blok keren de toegang tot uw punt-naar-site-IP-bereik.
* Uw on-premises firewalls blok keren verkeer van uw punt-naar-site-IP-bereik.
* U probeert een niet-RFC 1918-adres te bereiken met behulp van de functie voor regionale VNet-integratie.
