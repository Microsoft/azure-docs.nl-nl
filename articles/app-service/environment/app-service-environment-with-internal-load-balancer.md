---
title: Een ILB ASE v1 maken
description: Een ASE maken en gebruiken met een ILB. Dit document is alleen bedoeld voor klanten die gebruikmaken van de oudere V1-ASE.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c4e5dedf2075a2e13cc91c5eed2c0f03ba498b97
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021517"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Een interne Load Balancer gebruiken met een App Service Environment

> [!NOTE] 
> Dit artikel heeft betrekking op de App Service Environment v1. Er is een nieuwere versie van de App Service Environment die eenvoudiger is te gebruiken en wordt uitgevoerd op een krachtigere infra structuur. Begin met de [Inleiding tot de app service Environment](intro.md)voor meer informatie over de nieuwe versie.
>

De functie App Service Environment (ASE) is een Premium Service-optie van Azure App Service die een verbeterde configuratie mogelijkheid biedt die niet beschikbaar is in de multi tenant-stem pels. De functie ASE implementeert in feite de Azure App Service in uw Azure Virtual Network (VNet). Als u meer inzicht wilt krijgen in de mogelijkheden die worden geboden door App Service omgevingen, leest u het document [Wat is een app service Environment][WhatisASE] -documentatie. Lees de [Veelgestelde vragen over Azure Virtual Network][virtualnetwork]als u de voor delen van het werken in een VNet niet kent. 

## <a name="overview"></a>Overzicht
Een ASE kan worden geïmplementeerd met een eind punt dat toegankelijk is via internet of met een IP-adres in uw VNet. Als u het IP-adres wilt instellen op een VNet-adres, moet u uw ASE implementeren met een interne Load Balancer (ILB). Wanneer uw ASE is geconfigureerd met een ILB, geeft u het volgende op:

* uw eigen domein of subdomein. Om het eenvoudig te maken, wordt in dit document het subdomein aangenomen, maar u kunt het op beide manieren configureren. 
* het certificaat dat wordt gebruikt voor HTTPS
* DNS-beheer voor uw subdomein. 

Hierna kunt u dingen doen zoals:

* host intranet toepassingen, zoals line-of-business-toepassingen, veilig in de cloud die u kunt gebruiken via een site naar site of ExpressRoute VPN
* apps hosten in de cloud die niet worden vermeld in open bare DNS-servers
* Maak Internet-geïsoleerde back-end-apps waarmee uw front-end-apps veilig kunnen worden geïntegreerd

#### <a name="disabled-functionality"></a>Uitgeschakelde functionaliteit
Er zijn enkele dingen die u niet kunt doen wanneer u een ILB ASE gebruikt. Deze dingen zijn onder andere:

* IPSSL gebruiken
* IP-adressen toewijzen aan specifieke apps
* kopen en gebruiken van een certificaat met een app via de portal. U kunt natuurlijk nog steeds certificaten direct verkrijgen met een certificerings instantie en deze gebruiken met uw apps, maar niet via de Azure Portal.

## <a name="creating-an-ilb-ase"></a>Een ILB-ASE maken
Het maken van een ILB ASE verschilt niet veel van het maken van een ASE. Zie [How to Create a app service Environment][HowtoCreateASE]voor een diep gaande bespreking over het maken van een ASE. Het proces voor het maken van een ILB ASE is hetzelfde als het maken van een VNet tijdens het maken van ASE of het selecteren van een al bestaand VNet. Ga als volgt te werk om een ILB AS-omgeving te maken: 

1. Selecteer in de Azure Portal **een resource maken-> web en mobiel-> app service Environment**.
2. Selecteer uw abonnement.
3. Selecteer of maak een resourcegroep.
4. Selecteer of maak een VNet.
5. Een subnet maken als u een VNet selecteert.
6. Selecteer **Virtual Network/locatie-> VNet-configuratie** en stel het VIP-type in op intern.
7. Geef een subdomeinnaam op (deze naam is het subdomein dat wordt gebruikt voor de apps die in deze ASE zijn gemaakt).
8. Selecteer **OK** en vervolgens **maken**.

![Toont de schermen die worden gebruikt voor het maken van een ILB-ASE.][1]

In het deel venster Virtual Network bevindt zich een VNet-configuratie optie waarmee u tussen een extern VIP of interne VIP kunt selecteren. De standaard is Extern. Als u deze hebt ingesteld op extern, gebruikt uw ASE een via internet toegankelijke VIP. Als u Intern selecteert, is de AS-omgeving geconfigureerd met een ILB op een IP-adres binnen uw VNet. 

Nadat u intern hebt geselecteerd, is de mogelijkheid om meer IP-adressen aan uw ASE toe te voegen verwijderd en moet u in plaats daarvan het subdomein van de ASE opgeven. In een ASE met een externe VIP wordt de naam van de ASE gebruikt in het subdomein voor apps die zijn gemaakt in die ASE. Als uw ASE de naam **_contosotest_* _ heeft en uw app in dat ASE de naam _*_mytest_*_ heeft, is het subdomein van de indeling _*_contosotest.p.azurewebsites.net_*_ en de URL voor die app is _*_mytest.contosotest.p.azurewebsites.net_*_. Als u het VIP-type instelt op intern, wordt uw ASE-naam niet gebruikt in het subdomein voor de ASE. U geeft het subdomein expliciet op. Als uw subdomein _*_contoso.Corp.net_*_ is en u een app hebt gemaakt in die ASE met de naam _*_timereporting_*_, wordt de URL voor die app _*_timereporting.contoso.Corp.net_*_.

## <a name="apps-in-an-ilb-ase"></a>Apps in een ILB-ASE
Het maken van een app in een ILB ASE is hetzelfde als het maken van een app in een ASE normaal gesp roken. 

1. Selecteer in de Azure Portal _ *een resource maken-> web en mobiel-> Web** of een **mobiele** of **API-app**.
2. Voer de naam van de app in.
3. Selecteer uw abonnement.
4. Selecteer of maak een resourcegroep.
5. Selecteer of maak een App Service plan (ASP). Als u een nieuwe ASP maakt, selecteert u uw ASE als locatie en selecteert u de werk groep waarmee u uw ASP wilt maken. Wanneer u de ASP maakt, selecteert u uw ASE als locatie en de werk groep. Wanneer u de naam van de app opgeeft, ziet u dat het subdomein onder de naam van uw app wordt vervangen door het subdomein voor uw ASE. 
6. Selecteer **Maken**. Schakel het selectie vakje **vastmaken aan dash board** in als u wilt dat de app wordt weer gegeven op uw dash board. 

![Laat zien hoe u een app maakt in een ILB ASE in de Azure Portal.][2]

Onder de naam van de app wordt de subdomeinnaam bijgewerkt op basis van het subdomein van uw ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Validatie na maken van ILB-ASE
Een ILB AS-omgeving verschilt iets van de niet-ILB AS-omgeving. Zoals u al hebt opgemerkt, moet u uw eigen DNS beheren en moet u ook uw eigen certificaat voor HTTPS-verbindingen opgeven. 

Nadat u uw ASE hebt gemaakt, ziet u dat in het subdomein het subdomein wordt weer gegeven dat u hebt opgegeven en dat er een nieuw item is in het menu **instelling** met de naam **ILB certificaat**. De ASE wordt gemaakt met een zelfondertekend certificaat, waardoor het eenvoudiger wordt om HTTPS te testen. De portal geeft aan dat u uw eigen certificaat moet opgeven voor HTTPS, maar dit is om u te stimuleren dat u een certificaat met uw subdomein hebt. 

![Hier wordt het subdomein weer gegeven dat u hebt opgegeven tijdens het maken van uw ASE.][3]

Als u gewoon van dingen werkt en u niet weet hoe u een certificaat moet maken, kunt u de IIS MMC-console toepassing gebruiken om een zelfondertekend certificaat te maken. Zodra de app is gemaakt, kunt u deze exporteren als een. pfx-bestand en deze vervolgens uploaden naar de gebruikers interface van het ILB-certificaat. Wanneer u toegang krijgt tot een site die is beveiligd met een zelfondertekend certificaat, geeft u in uw browser een waarschuwing dat de site waartoe u toegang hebt, niet beveiligd is omdat het niet mogelijk is om het certificaat te valideren. Als u deze waarschuwing wilt voor komen, hebt u een correct ondertekend certificaat nodig dat overeenkomt met uw subdomein en beschikt u over een vertrouwens keten die wordt herkend door uw browser.

![Laat zien hoe u de IIS MMC-console toepassing gebruikt om een zelfondertekend certificaat te maken.][6]

Als u de stroom met uw eigen certificaten wilt proberen en de HTTP-en HTTPS-toegang tot uw ASE wilt testen:

1. Ga naar de ASE-gebruikers interface nadat ASE **ASE-instellingen >-> ILB-certificaten** hebt gemaakt.
2. Stel ILB-certificaat in door het pfx-certificaat bestand te selecteren en wacht woord op te geven. Deze stap neemt enige tijd in beslag en het bericht dat er een schaal bewerking wordt uitgevoerd wordt weer gegeven.
3. Haal het ILB-adres voor uw ASE (**ASE->-eigenschappen-> virtueel IP-adres**) op.
4. Maak na het maken van een web-app in ASE. 
5. Maak een virtuele machine als u deze niet in het VNET hebt (niet in hetzelfde subnet als de ASE of dingen).
6. Stel DNS in voor uw subdomein. U kunt een Joker teken gebruiken met uw subdomein in uw DNS of als u een aantal eenvoudige tests wilt uitvoeren, bewerkt u het hosts-bestand op uw virtuele machine om de naam van de web-app in te stellen op VIP IP-adres. Als uw ASE de subdomeinnaam. ilbase.com had en u de web-app mytestapp hebt gemaakt, zodat deze op mytestapp.ilbase.com wordt opgelost, stelt u die in uw hosts-bestand in. (Het hosts-bestand bevindt zich op Windows op C:\Windows\System32\drivers\etc\)
7. Gebruik een browser op deze VM en ga naar `https://mytestapp.ilbase.com` (of de naam van uw web-app met uw subdomein).
8. Gebruik een browser op die virtuele machine en ga naar `https://mytestapp.ilbase.com`. U moet het gebrek aan beveiliging accepteren als u een zelfondertekend certificaat gebruikt. 

Het IP-adres voor uw ILB wordt vermeld in uw eigenschappen als het virtuele IP-adres.

![Geeft aan dat het IP-adres voor uw ILB wordt vermeld in uw eigenschappen als het virtuele IP-adres.][4]

## <a name="using-an-ilb-ase"></a>Een ILB-ASE gebruiken
#### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen
Een ILB-ASE maakt netwerk isolatie mogelijk voor uw apps. De apps zijn niet toegankelijk of zelfs niet bekend via internet. Deze aanpak is uitstekend voor het hosten van intranet sites, zoals line-of-business-toepassingen. Wanneer u de toegang nog verder wilt beperken, kunt u nog steeds netwerk beveiligings groepen (Nsg's) gebruiken om de toegang op netwerk niveau te beheren. 

Als u Nsg's wilt gebruiken om de toegang verder te beperken, moet u ervoor zorgen dat u niet de communicatie verbreekt die de ASE nodig heeft om te kunnen werken. Hoewel de HTTP/HTTPS-toegang alleen is via de ILB die door de ASE wordt gebruikt, is de ASE nog steeds afhankelijk van resources buiten het VNet. Zie [inkomend verkeer beheren voor een app service Environment][ControlInbound] en  [netwerk configuratie Details voor app service omgevingen met ExpressRoute][ExpressRoute]om te zien welke netwerk toegang nog steeds vereist is. 

Als u uw Nsg's wilt configureren, moet u het IP-adres weten dat door Azure wordt gebruikt voor het beheren van uw ASE. Dat IP-adres is ook het uitgaande IP-adres van uw ASE als Internet aanvragen worden uitgevoerd. Het uitgaande IP-adres voor uw ASE blijft statisch voor de levens duur van uw ASE. Als u uw ASE verwijdert en opnieuw maakt, krijgt u een nieuw IP-adres. Als u het IP-adres wilt vinden, gaat u naar **instellingen-> eigenschappen** en zoekt u het **uitgaande IP-adres**. 

![Hier wordt weer gegeven waar u het uitgaande IP-adres voor uw ASE kunt vinden.][5]

#### <a name="general-ilb-ase-management"></a>Algemeen ILB ASE-beheer
Het beheren van een ILB-ASE is grotendeels hetzelfde als het beheren van een ASE. U moet uw werknemers groepen omhoog schalen om meer ASP-instanties te hosten en de front-endservers omhoog te schalen om meer bedragen aan HTTP/HTTPS-verkeer te kunnen verwerken. Zie [Configure an app service Environment][ASEConfig](Engelstalig) voor algemene informatie over het beheren van de configuratie van een ASE. 

De extra beheer items zijn certificaat beheer en DNS-beheer. U moet het certificaat dat wordt gebruikt voor HTTPS ophalen en uploaden nadat ILB ASE gemaakt en vervangen voordat het verloopt. Omdat Azure eigenaar is van het basis domein, kan dit certificaten bieden voor as met een externe VIP. Omdat het subdomein dat wordt gebruikt door een ILB-ASE, een wille keurige reden kan zijn, moet u uw eigen certificaat voor HTTPS opgeven. 

#### <a name="dns-configuration"></a>DNS-configuratie
Wanneer u een extern VIP-adres gebruikt, wordt de DNS beheerd door Azure. Elke app die is gemaakt in de AS-omgeving wordt automatisch toegevoegd aan Azure DNS, wat een openbaar DNS is. In een ILB AS-omgeving moet u uw eigen DNS beheren. Voor een bepaald subdomein, zoals contoso.corp.net, moet u DNS A-records maken die verwijzen naar uw ILB-adres voor:

- \*
- *. scm
- ftp
- publish

## <a name="getting-started"></a>Aan de slag
Zie [Inleiding tot app service omgevingen][WhatisASE] om aan de slag te gaan met app service omgevingen

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md