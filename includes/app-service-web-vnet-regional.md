---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: a4eb22320a15cc76a7543c25583003d57ea4e538
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473766"
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

Standaard stuurt uw app alleen RFC1918-verkeer naar uw VNet. Als u al het uitgaande verkeer wilt door sturen naar uw VNet, past u de app-instelling WEBSITE_VNET_ROUTE_ALL toe op uw app. De app-instelling configureren:

1. Ga naar de **configuratie** gebruikersinterface in de app-Portal. Selecteer **Nieuwe toepassingsinstelling**.
1. Voer **WEBSITE_VNET_ROUTE_ALL** in het vak **naam** in en voer **1** in het vak **waarde** in.

   ![Toepassings instelling opgeven][4]

1. Selecteer **OK**.
1. Selecteer **Opslaan**.

> [!NOTE]
> Als u al het uitgaande verkeer naar uw VNet routert, is het onderhevig aan de Nsg's en Udr's die worden toegepast op het integratie subnet. Wanneer u al het uitgaande verkeer naar uw VNet routert, zijn uw uitgaande adressen nog steeds de uitgaande adressen die worden vermeld in de app-eigenschappen, tenzij u routes opgeeft om het verkeer elders te verzenden.
> 
> De regionale VNet-integratie kan poort 25 niet gebruiken.

Er zijn enkele beperkingen bij het gebruik van VNet-integratie met VNets in dezelfde regio:

* U kunt geen resources bereiken via globale peering-verbindingen.
* De functie is beschikbaar vanaf alle App Service schaal eenheden in Premium v2 en Premium v3. Het is ook beschikbaar in standaard, maar alleen vanaf nieuwere App Service schaal eenheden. Als u zich op een oudere schaal eenheid bevindt, kunt u de functie alleen gebruiken vanuit een Premium v2 App Service-abonnement. Als u het gebruik van de functie in een Standard App Service-abonnement wilt kunnen gebruiken, maakt u uw app in een Premium v3-App Service plan. Deze abonnementen worden alleen ondersteund op onze nieuwste schaal eenheden. U kunt omlaag schalen als u dat wilt.  
* Het integratie subnet kan slechts door één App Service schema worden gebruikt.
* De functie kan niet worden gebruikt door toepassingen van het geïsoleerde abonnement in een App Service Environment.
* De functie vereist een ongebruikt subnet dat een/28 of groter is in een Azure Resource Manager VNet.
* De app en het VNet moeten zich in dezelfde regio bevinden.
* U kunt een VNet met een geïntegreerde app niet verwijderen. Verwijder de integratie voordat u het VNet verwijdert.
* U kunt slechts één regionale VNet-integratie per App Service plan hebben. Meerdere apps in hetzelfde App Service-abonnement kunnen hetzelfde VNet gebruiken.
* U kunt het abonnement van een app of een abonnement niet wijzigen terwijl er een app is die gebruikmaakt van regionale VNet-integratie.
* Uw app kan geen adressen omzetten in Azure DNS Private Zones zonder configuratie wijzigingen

VNet-integratie is afhankelijk van het gebruik van een toegewezen subnet.  Wanneer u een subnet inricht, verliest het Azure-subnet 5 Ip's voor van het begin. Er wordt één adres gebruikt vanuit het integratie-subnet voor elk exemplaar van het abonnement. Als u uw app schaalt naar vier instanties, worden er vier adressen gebruikt. De debet van 5 adressen uit de grootte van het subnet betekent dat de Maxi maal beschik bare adressen per CIDR-blok:

- /28 heeft 11 adressen
- /27 heeft 27 adres
- /26 heeft 59 adressen

Als u omhoog of omlaag schaalt, hebt u een dubbele tijd nodig voor een korte periode. De maximale grootte betekent dat de daad werkelijke beschik bare ondersteunde instanties per subnet grootte als uw subnet een:

- /28, uw maximale horizontale schaal is 5 exemplaren
- /27, uw maximale horizontale schaal is 13 instanties
- /26, uw maximale horizontale schaal is 29 instanties

De limieten die worden aangegeven bij de maximale horizontale schaal, wordt ervan uitgegaan dat u op een bepaald moment omhoog of omlaag moet schalen. 

Aangezien de grootte van het subnet niet kan worden gewijzigd na toewijzing, gebruikt u een subnet dat groot genoeg is voor de schaal van uw app. Om problemen met de capaciteit van het subnet te voor komen, is een/26 met 64-adressen de aanbevolen grootte.  

Als u wilt dat uw apps in een ander abonnement een VNet bereiken dat al is verbonden met apps in een ander abonnement, selecteert u een ander subnet dan het subnetwerk dat wordt gebruikt door de bestaande VNet-integratie.

De functie wordt volledig ondersteund voor zowel Windows-als Linux-apps, waaronder [aangepaste containers](../articles/app-service/quickstart-custom-container.md). Alle gedragingen handelen hetzelfde tussen Windows-apps en Linux-apps.

### <a name="service-endpoints"></a>Service-eindpunten

Met regionale VNet-integratie kunt u service-eind punten gebruiken. Als u service-eind punten wilt gebruiken in combi natie met uw app, gebruikt u de regionale VNet-integratie om verbinding te maken met een geselecteerd VNet en configureert u vervolgens service-eind punten met de doel service op het subnet dat u voor de integratie hebt gebruikt. Als u vervolgens een service wilt openen via service-eind punten:

1. regionale VNet-integratie met uw web-app configureren
1. Ga naar de doel service en configureer service-eind punten op het subnet dat wordt gebruikt voor de integratie

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen

U kunt netwerk beveiligings groepen gebruiken om binnenkomend en uitgaand verkeer naar resources in een VNet te blok keren. Een app die gebruikmaakt van regionale VNet-integratie kan een [netwerk beveiligings groep][VNETnsg] gebruiken om uitgaand verkeer naar resources in uw VNet of Internet te blok keren. Als u verkeer naar open bare adressen wilt blok keren, moet u de toepassings instelling WEBSITE_VNET_ROUTE_ALL instellen op 1. De binnenkomende regels in een NSG zijn niet van toepassing op uw app, omdat de VNet-integratie alleen van invloed is op uitgaand verkeer van uw app.

Als u inkomend verkeer naar uw app wilt beheren, gebruikt u de functie toegangs beperkingen. Een NSG die wordt toegepast op uw integratie subnet, is van kracht, ongeacht eventuele routes die worden toegepast op het integratie subnet. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1 en u geen routes hebt die van invloed zijn op het open bare adres verkeer op het subnet met integratie, is al uw uitgaand verkeer nog steeds afhankelijk van Nsg's die zijn toegewezen aan uw integratie subnet. Als WEBSITE_VNET_ROUTE_ALL niet is ingesteld, worden Nsg's alleen toegepast op RFC1918-verkeer.

### <a name="routes"></a>Routes

U kunt route tabellen gebruiken om uitgaand verkeer van uw app naar de gewenste locatie te routeren. Routerings tabellen zijn standaard alleen van invloed op uw RFC1918-doel verkeer. Als u WEBSITE_VNET_ROUTE_ALL op 1 instelt, worden al uw uitgaande oproepen beïnvloed. Routes die zijn ingesteld op het integratie subnet, zijn niet van invloed op antwoorden op aanvragen voor inkomende apps. Algemene doelen kunnen Firewall apparaten of gateways zijn.

Als u al het uitgaande verkeer on-premises wilt routeren, kunt u een route tabel gebruiken om al het uitgaande verkeer naar uw ExpressRoute-gateway te verzenden. Als u verkeer naar een gateway stuurt, moet u ervoor zorgen dat routes worden ingesteld in het externe netwerk om antwoorden terug te sturen.

Border Gateway Protocol (BGP)-routes hebben ook invloed op uw app-verkeer. Als u BGP-routes van iets zoals een ExpressRoute-gateway hebt, heeft dit gevolgen voor het uitgaande verkeer van uw app. BGP-routes zijn standaard alleen van invloed op uw RFC1918-doel verkeer. Als WEBSITE_VNET_ROUTE_ALL is ingesteld op 1, kan al het uitgaande verkeer worden beïnvloed door de BGP-routes.

### <a name="azure-dns-private-zones"></a>Azure DNS Private Zones 

Nadat uw app met uw VNet is geïntegreerd, maakt deze gebruik van dezelfde DNS-server die is geconfigureerd voor uw VNet. Uw app werkt standaard niet met Azure DNS Private Zones. Als u met Azure DNS Private Zones wilt werken, moet u de volgende app-instellingen toevoegen:


1. WEBSITE_DNS_SERVER met waarde 168.63.129.16
1. WEBSITE_VNET_ROUTE_ALL met waarde 1


Met deze instellingen worden al uw uitgaande oproepen vanuit uw app naar uw VNet verzonden en kan uw app Azure DNS privé zones gebruiken.   Met deze instellingen worden alle uitgaande oproepen vanuit uw app naar uw VNet verzonden. Daarnaast wordt de app in staat stellen om Azure DNS te gebruiken door de Privé-DNS zone op werk niveau te doorzoeken. Deze functionaliteit moet worden gebruikt wanneer een actieve app toegang tot een Privé-DNS zone heeft.

> [!NOTE]
>Het is niet mogelijk om een aangepast domein toe te voegen aan een web-app met behulp van Privé-DNS zone, maar niet met de VNET-integratie. De aangepaste domein validatie wordt uitgevoerd op het niveau van de controller, niet op het niveau van de werk nemer, waardoor de DNS-records niet zichtbaar zijn. Als u een aangepast domein van een Privé-DNS zone wilt gebruiken, moet de validatie worden omzeild met een Application Gateway of ILB App Service Environment.

### <a name="private-endpoints"></a>Privé-eindpunten

Als u aanroepen naar [persoonlijke eind punten][privateendpoints]wilt maken, moet u ervoor zorgen dat uw DNS-Zoek opdrachten worden omgezet naar het persoonlijke eind punt. Om ervoor te zorgen dat de DNS-zoek acties van uw app naar uw persoonlijke eind punten verwijzen, kunt u het volgende doen:

* Integreer met Azure DNS Private Zones. Als uw VNet geen aangepaste DNS-server heeft, wordt dit automatisch
* beheer het persoonlijke eind punt in de DNS-server die door uw app wordt gebruikt. Als u dit wilt doen, moet u het adres van het persoonlijke eind punt weten en vervolgens het eind punt aanwijzen dat u wilt bereiken met een record.
* uw eigen DNS-server configureren om door te sturen naar Azure DNS particuliere zones

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
