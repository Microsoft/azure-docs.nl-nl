---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 7796b94609a9be05fdb72900d0725747440f8042
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582325"
---
Door gebruik te maken van regionale VNet-integratie kan uw app toegang tot:

* Resources in een VNet in dezelfde regio als uw app.
* Resources in VNets die zijn gekoppeld aan het VNet waarin uw app is geïntegreerd.
* Beveiligde services voor service-eind punten.
* Bronnen in azure ExpressRoute-verbindingen.
* Resources in het VNet waarmee u bent geïntegreerd.
* Bronnen tussen peered verbindingen, waaronder Azure ExpressRoute-verbindingen.
* Privé-eindpunten 

Wanneer u VNet-integratie met VNets in dezelfde regio gebruikt, kunt u de volgende Azure-netwerk functies gebruiken:

* **Netwerk beveiligings groepen (nsg's)**: u kunt uitgaand verkeer blok keren met een NSG die op uw integratie subnet is geplaatst. De regels voor binnenkomende verbindingen zijn niet van toepassing omdat u VNet-integratie niet kunt gebruiken om inkomende toegang tot uw app te bieden.
* **Route tabellen (udr's)**: u kunt een route tabel plaatsen op het subnet met integratie om uitgaand verkeer te verzenden naar de gewenste locatie.

Standaard stuurt uw app alleen RFC1918-verkeer naar uw VNet. Als u al het uitgaande verkeer naar uw VNet wilt door sturen, gebruikt u de volgende stappen om de `WEBSITE_VNET_ROUTE_ALL` instelling in uw app toe te voegen: 

1. Ga naar de **configuratie** gebruikersinterface in de app-Portal. Selecteer **Nieuwe toepassingsinstelling**.
1. Voer `WEBSITE_VNET_ROUTE_ALL` in het vak **naam** in en voer `1` in het vak **waarde** in.

   ![Toepassings instelling opgeven][4]

1. Selecteer **OK**.
1. Selecteer **Opslaan**.

> [!NOTE]
> Wanneer u al het uitgaande verkeer naar uw VNet routert, is het onderhevig aan de Nsg's en Udr's die worden toegepast op het integratie subnet. Wanneer `WEBSITE_VNET_ROUTE_ALL` is ingesteld op `1` , wordt uitgaand verkeer nog steeds verzonden vanaf de adressen die worden vermeld in de app-eigenschappen, tenzij u routes opgeeft waarmee het verkeer elders wordt omgeleid.
> 
> De regionale VNet-integratie kan poort 25 niet gebruiken.

Er zijn enkele beperkingen bij het gebruik van VNet-integratie met VNets in dezelfde regio:

* U kunt geen resources bereiken via globale peering-verbindingen.
* De functie is beschikbaar vanaf alle App Service schaal eenheden in Premium v2 en Premium v3. Het is ook beschikbaar in standaard, maar alleen vanaf nieuwere App Service schaal eenheden. Als u zich op een oudere schaal eenheid bevindt, kunt u de functie alleen gebruiken vanuit een Premium v2 App Service-abonnement. Als u er zeker van wilt zijn dat u de functie in een Standard App Service-abonnement kunt gebruiken, maakt u uw app in een Premium v3-App Service plan. Deze abonnementen worden alleen ondersteund op onze nieuwste schaal eenheden. U kunt omlaag schalen als u dat wilt.  
* Het integratie subnet kan slechts door één App Service schema worden gebruikt.
* De functie kan niet worden gebruikt door toepassingen van het geïsoleerde abonnement in een App Service Environment.
* De functie vereist een ongebruikt subnet dat een/28 of groter is in een Azure Resource Manager VNet.
* De app en het VNet moeten zich in dezelfde regio bevinden.
* U kunt een VNet met een geïntegreerde app niet verwijderen. Verwijder de integratie voordat u het VNet verwijdert.
* U kunt slechts één regionale VNet-integratie per App Service plan hebben. Meerdere apps in hetzelfde App Service-abonnement kunnen hetzelfde VNet gebruiken.
* U kunt het abonnement van een app of een abonnement niet wijzigen terwijl er een app is die gebruikmaakt van regionale VNet-integratie.
* In uw app kunnen geen adressen worden omgezet in Azure DNS Private Zones zonder configuratie wijzigingen.

VNet-integratie is afhankelijk van een toegewezen subnet. Wanneer u een subnet inricht, verliest het Azure-subnet vijf IP-adressen van het begin. Er wordt één adres gebruikt vanuit het integratie-subnet voor elk exemplaar van het abonnement. Wanneer u uw app schaalt naar vier instanties, worden er vier adressen gebruikt. 

Wanneer u omhoog of omlaag schaalt, wordt de benodigde adres ruimte voor een korte periode verdubbeld. Dit is van invloed op de echte, beschik bare ondersteunde instanties voor een bepaalde subnet-grootte. In de volgende tabel ziet u de Maxi maal beschik bare adressen per CIDR-blok en de impact hiervan op horizontale schaal:

| CIDR-blok grootte | Maxi maal aantal beschik bare adressen | Maximum aantal horizontale schalen (instanties)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Er wordt van uitgegaan dat u op een bepaald moment omhoog of omlaag moet schalen in een van de formaten of op de SKU. 

Aangezien de grootte van het subnet niet kan worden gewijzigd na toewijzing, gebruikt u een subnet dat groot genoeg is voor de schaal van uw app. Als u problemen met de capaciteit van het subnet wilt voor komen, moet u een/26 met 64-adressen gebruiken.  

Als u wilt dat uw apps in een ander abonnement een VNet bereiken dat al is verbonden met apps in een ander abonnement, selecteert u een ander subnet dan het subnetwerk dat wordt gebruikt door de al bestaande VNet-integratie.

De functie wordt volledig ondersteund voor zowel Windows-als Linux-apps, waaronder [aangepaste containers](../articles/app-service/quickstart-custom-container.md). Alle gedragingen handelen hetzelfde tussen Windows-apps en Linux-apps.

### <a name="service-endpoints"></a>Service-eindpunten

Met regionale VNet-integratie kunt u service-eind punten gebruiken. De basis stappen voor het verkrijgen van toegang tot een service vanuit uw app via service-eind punten is als volgt:

1. Configureer de regionale VNet-integratie met uw web-app om verbinding te maken met een specifiek subnet voor integratie.
1. Ga naar de doel service en configureer service-eind punten voor het integratie subnet.

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen

U kunt netwerk beveiligings groepen gebruiken om binnenkomend en uitgaand verkeer naar resources in een VNet te blok keren. Een app die gebruikmaakt van regionale VNet-integratie kan een [netwerk beveiligings groep][VNETnsg] gebruiken om uitgaand verkeer naar resources in uw VNet of Internet te blok keren. Als u verkeer naar open bare adressen wilt blok keren, moet de toepassings instelling zijn `WEBSITE_VNET_ROUTE_ALL` ingesteld op `1` . De binnenkomende regels in een NSG zijn niet van toepassing op uw app, omdat de VNet-integratie alleen van invloed is op uitgaand verkeer van uw app.

Als u inkomend verkeer naar uw app wilt beheren, gebruikt u de functie toegangs beperkingen. Een NSG die wordt toegepast op uw integratie subnet, is van kracht, ongeacht eventuele routes die worden toegepast op het integratie subnet. Als `WEBSITE_VNET_ROUTE_ALL` is ingesteld op `1` en u geen routes hebt die van invloed zijn op het verkeer van het open bare adres op het subnet met integratie, is al uw uitgaand verkeer nog steeds afhankelijk van nsg's die zijn toegewezen aan uw integratie subnet. Als `WEBSITE_VNET_ROUTE_ALL` niet is ingesteld, worden nsg's alleen toegepast op RFC1918-verkeer.

### <a name="routes"></a>Routes

U kunt route tabellen gebruiken om uitgaand verkeer van uw app naar de gewenste locatie te routeren. Routerings tabellen zijn standaard alleen van invloed op uw RFC1918-doel verkeer. Wanneer u instelt `WEBSITE_VNET_ROUTE_ALL` op `1` , worden al uw uitgaande oproepen beïnvloed. Routes die zijn ingesteld op het integratie subnet, zijn niet van invloed op antwoorden op aanvragen voor inkomende apps. Algemene doelen kunnen Firewall apparaten of gateways zijn.

Als u al het uitgaande verkeer on-premises wilt routeren, kunt u een route tabel gebruiken om al het uitgaande verkeer naar uw ExpressRoute-gateway te verzenden. Als u verkeer naar een gateway stuurt, moet u ervoor zorgen dat routes worden ingesteld in het externe netwerk om antwoorden terug te sturen.

Border Gateway Protocol (BGP)-routes hebben ook invloed op uw app-verkeer. Als u BGP-routes van iets zoals een ExpressRoute-gateway hebt, heeft dit gevolgen voor het uitgaande verkeer van uw app. BGP-routes zijn standaard alleen van invloed op uw RFC1918-doel verkeer. Wanneer `WEBSITE_VNET_ROUTE_ALL` is ingesteld op `1` , kan al het uitgaande verkeer worden beïnvloed door de BGP-routes.

### <a name="azure-dns-private-zones"></a>Azure DNS particuliere zones 

Nadat uw app met uw VNet is geïntegreerd, maakt deze gebruik van dezelfde DNS-server die is geconfigureerd voor uw VNet. Uw app werkt standaard niet met Azure DNS persoonlijke zones. Als u wilt werken met Azure DNS particuliere zones, moet u de volgende app-instellingen toevoegen:

1. `WEBSITE_DNS_SERVER` met waarde `168.63.129.16`
1. `WEBSITE_VNET_ROUTE_ALL` met waarde `1`

Met deze instellingen worden al uw uitgaande oproepen vanuit uw app naar uw VNet verzonden en kunnen uw apps toegang krijgen tot een Azure DNS privé zone. Met deze instellingen kan uw app Azure DNS gebruiken door een query uit te richten op de persoonlijke DNS-zone op het niveau van de werk nemer.  

> [!NOTE]
> Het is niet mogelijk om een aangepast domein toe te voegen aan een web-app met behulp van een persoonlijke DNS-zone met de VNET-integratie. De aangepaste domein validatie wordt uitgevoerd op het niveau van de controller, niet op het niveau van de werk nemer, waardoor de DNS-records niet zichtbaar zijn. Als u een aangepast domein van een persoonlijke DNS-zone wilt gebruiken, moet u de validatie overs laan met behulp van een [Application Gateway](../articles/app-service/networking/app-gateway-with-service-endpoints.md) of [ILB app service Environment](../articles/app-service/environment/create-ilb-ase.md).

### <a name="private-endpoints"></a>Privé-eindpunten

Als u aanroepen naar [persoonlijke eind punten][privateendpoints]wilt maken, moet u ervoor zorgen dat uw DNS-zoek acties worden omgezet naar het persoonlijke eind punt. U kunt dit gedrag op een van de volgende manieren afdwingen: 

* Integreer met Azure DNS persoonlijke zones. Als uw VNet geen aangepaste DNS-server heeft, wordt dit automatisch uitgevoerd.
* Beheer het persoonlijke eind punt in de DNS-server die door uw app wordt gebruikt. Als u dit wilt doen, moet u het adres van het persoonlijke eind punt kennen en vervolgens het eind punt aanwijzen dat u met een record wilt bereiken naar dat adres.
* Configureer uw eigen DNS-server om door te sturen naar Azure DNS particuliere zones.

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
