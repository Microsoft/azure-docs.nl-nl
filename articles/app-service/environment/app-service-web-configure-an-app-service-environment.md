---
title: ASE v1 configureren
description: Configuratie, beheer en controle van de App Service Environment v1. Dit document is alleen bedoeld voor klanten die gebruikmaken van de oudere V1-ASE.
author: ccompy
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 598e43d07c213cfeb25f0ecbc7bd02b6ec54b7ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88962584"
---
# <a name="configuring-an-app-service-environment-v1"></a>Een App Service Environment v1 configureren

> [!NOTE]
> Dit artikel heeft betrekking op de App Service Environment v1.  Er is een nieuwere versie van de App Service Environment die eenvoudiger is te gebruiken en wordt uitgevoerd op een krachtigere infra structuur. Begin met de [Inleiding tot de app service Environment](intro.md)voor meer informatie over de nieuwe versie.
> 

## <a name="overview"></a>Overzicht
Op hoog niveau bestaat een Azure App Service Environment uit verschillende belang rijke onderdelen:

* Reken bronnen die worden uitgevoerd in de gehoste service App Service Environment
* Storage
* Een Data Base
* Een klassiek (v1) of Resource Manager (v2) Azure Virtual Network (VNet) 
* Een subnet waarop de App Service Environment gehoste service wordt uitgevoerd

### <a name="compute-resources"></a>Rekenresources
U gebruikt de reken resources voor uw vier resource groepen.  Elke App Service Environment (ASE) heeft een set front-ends en drie mogelijke werk groepen. U hoeft niet alle drie de werkgroepen te gebruiken. Als u wilt, kunt u slechts één of twee gebruiken.

De hosts in de resource groepen (front-ends en werk rollen) zijn niet rechtstreeks toegankelijk voor tenants. U kunt Remote Desktop Protocol (RDP) niet gebruiken om er verbinding mee te maken, hun inrichting te wijzigen of als beheerder te fungeren.

U kunt het aantal en de grootte van de resource groep instellen. In een ASE hebt u vier grootte opties, die zijn gemarkeerd met P1 via P4. Zie [app service prijzen](https://azure.microsoft.com/pricing/details/app-service/)voor meer informatie over deze formaten en hun prijzen.
Het wijzigen van de hoeveelheid of grootte wordt een schaal bewerking genoemd.  Er kan slechts één schaal bewerking tegelijk worden uitgevoerd.

**Front-ends**: de front-ends zijn de http/https-eind punten voor uw apps die in uw ASE worden bewaard. U voert geen workloads uit in de front-ends.

* Een ASE begint met twee P2s. Dit is voldoende voor dev/test-workloads en werk belastingen op laag niveau. We raden u ten zeerste aan om P3s te produceren voor gemiddeld tot zware productie workloads.
* Voor gemiddeld tot zware productie workloads wordt u aangeraden ten minste vier P3s te hebben om ervoor te zorgen dat er voldoende front-ends actief zijn wanneer gepland onderhoud plaatsvindt. Geplande onderhouds activiteiten gaan één front-end tegelijk omlaag. Dit reduceert de totale beschik bare front-end capaciteit tijdens onderhouds activiteiten.
* Het kan een uur duren voordat front-ends zijn ingericht. 
* Voor een verdere schaal aanpassing moet u het CPU-percentage, het geheugen percentage en actieve aanvragen voor de front-end-groep bewaken. Als het CPU-of geheugen percentage hoger is dan 70 procent bij het uitvoeren van P3s, voegt u meer front-ends toe. Als de waarde van de actieve aanvragen een gemiddelde oploopt van 15.000 tot 20.000 aanvragen per front-end, moet u ook meer front-ends toevoegen. Het algemene doel is om het CPU-en geheugen percentage lager te blijven dan 70%, en actieve aanvragen die gemiddeld naar beneden 15.000 aanvragen per front-end lopen wanneer u P3s uitvoert.  

**Werk nemers**: de werk nemers zijn waar uw apps daad werkelijk worden uitgevoerd. Wanneer u uw App Service-abonnementen opschaalt, wordt er gebruikgemaakt van werk nemers in de bijbehorende werk groep.

* U kunt geen werk nemers direct toevoegen. Het kan tot een uur duren voordat ze zijn ingericht.
* Het schalen van de grootte van een reken resource voor een pool duurt < 1 uur per update domein. Er zijn 20 Update domeinen in een ASE. Als u de reken grootte van een werk groep met tien exemplaren hebt geschaald, kan het tot tien uur duren voordat deze is voltooid.
* Als u de grootte van de reken resources die worden gebruikt in een werk groep wijzigt, zorgt u dat de apps die worden uitgevoerd in die werk groep koud worden gestart.

De snelste manier om de grootte van de reken resource te wijzigen van een werk groep die geen apps uitvoert, is:

* Schaal de hoeveelheid werk nemers op 2.  De minimale schaal grootte in de portal is 2. Het kan een paar minuten duren om de toewijzing van uw instanties ongedaan te maken. 
* Selecteer de nieuwe reken grootte en het aantal exemplaren. Hier kan het tot twee uur duren voordat de bewerking is voltooid.

Als uw apps een grotere Compute-resource grootte vereisen, kunt u niet profiteren van de voor gaande richt lijnen. In plaats van het wijzigen van de grootte van de werk groep die als host fungeert voor die apps, kunt u een andere werk groep vullen met werk nemers van de gewenste grootte en uw apps verplaatsen naar die groep.

* De extra exemplaren van de benodigde reken grootte in een andere werk groep maken. Het duurt Maxi maal een uur voordat de bewerking is voltooid.
* Wijs uw App Service-abonnementen die als host fungeren voor de apps die een grotere grootte hebben, opnieuw toe aan de zojuist geconfigureerde werk groep. Dit is een snelle bewerking die minder dan een minuut in beslag neemt.  
* U kunt de eerste groep werk nemers omlaag schalen als u niet alle ongebruikte instanties meer nodig hebt. Het duurt enkele minuten voordat deze bewerking is voltooid.

Automatisch **schalen**: een van de hulpprogram ma's die u kan helpen bij het beheren van het gebruik van de reken resource is automatisch geschaald. U kunt automatisch schalen gebruiken voor front-end-of worker-groepen. U kunt dingen doen, zoals het verg Roten van uw groeps type in de ochtend en de kortings uren. Of misschien kunt u instanties toevoegen wanneer het aantal werk rollen dat beschikbaar is in een werknemers groep onder een bepaalde drempel waarde komt.

Als u regels voor automatisch schalen wilt instellen om metrische gegevens over de resource groep te berekenen, moet u rekening houden met de tijd die nodig is voor het inrichten. Zie voor meer informatie over het automatisch schalen van App Service omgevingen, [automatisch schalen configureren in een app service Environment][ASEAutoscale].

### <a name="storage"></a>Storage
Elke ASE wordt geconfigureerd met 500 GB aan opslag ruimte. Deze ruimte wordt gebruikt in alle apps in het ASE. Deze opslag ruimte maakt deel uit van de ASE en kan momenteel niet worden overgeschakeld om uw opslag ruimte te gebruiken. Als u de route ring of beveiliging van uw virtuele netwerk wilt aanpassen, moet u nog steeds toegang tot Azure Storage toestaan, of het ASE kan niet worden gebruikt.

### <a name="database"></a>Database
De Data Base bevat de informatie die de omgeving definieert, evenals de details over de apps die erin worden uitgevoerd. Dit is ook een onderdeel van het Azure-abonnement. Het is niet iets dat u direct kunt bewerken. Als u de route ring of beveiliging van uw virtuele netwerk wilt aanpassen, moet u nog steeds toegang tot SQL Azure toestaan, of het ASE kan niet worden gebruikt.

### <a name="network"></a>Netwerk
Het VNet dat met uw ASE wordt gebruikt, is een account dat u hebt gemaakt tijdens het maken van de ASE of een van de tijd die u in de toekomst hebt gemaakt. Wanneer u het subnet tijdens het maken van een ASE maakt, wordt de ASE in dezelfde resource groep als het virtuele netwerk geforceerd. Als u wilt dat de resource groep die door uw ASE wordt gebruikt, anders is dan die van uw VNet, moet u uw ASE maken met behulp van een resource manager-sjabloon.

Er gelden enkele beperkingen voor het virtuele netwerk dat wordt gebruikt voor een ASE:

* Het virtuele netwerk moet een regionaal virtueel netwerk zijn.
* Er moet een subnet zijn met 8 of meer adressen waarop de ASE is geïmplementeerd.
* Wanneer een subnet wordt gebruikt voor het hosten van een ASE, kan het adres bereik van het subnet niet worden gewijzigd. Daarom raden wij aan dat het subnet ten minste 64 adressen bevat voor een toekomstige ASE groei.
* Er kan niets anders zijn in het subnet, maar de ASE.

In tegens telling tot de gehoste service die de ASE bevat, bevinden het [virtuele netwerk][virtualnetwork] en subnet zich onder gebruikers beheer.  U kunt uw virtuele netwerk beheren via de Virtual Network gebruikers interface of Power shell.  Een ASE kan worden geïmplementeerd in een klassiek of Resource Manager-VNet.  De portal-en API-ervaring zijn iets anders tussen klassieke en Resource Manager-VNets, maar de ASE-ervaring is hetzelfde.

Het VNet dat wordt gebruikt voor het hosten van een ASE kan gebruikmaken van particuliere RFC1918 IP-adressen of kan gebruikmaken van open bare IP-adressen.  Als u een IP-bereik wilt gebruiken dat niet wordt gedekt door RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), moet u uw VNet en subnet maken dat moet worden gebruikt door uw ASE vóór het maken van ASE.

Omdat deze functie de Azure App Service in uw virtuele netwerk plaatst, betekent dit dat uw apps die worden gehost in uw ASE nu toegang hebben tot bronnen die beschikbaar worden gesteld via ExpressRoute of site-naar-site-Vpn's. De apps die zich in uw App Service Environment bevinden, hebben geen aanvullende netwerk functies nodig om toegang te krijgen tot bronnen die beschikbaar zijn voor het virtuele netwerk dat als host fungeert voor uw App Service Environment. Dit betekent dat u VNET-integratie of Hybride verbindingen niet hoeft te gebruiken om resources in of verbonden te krijgen met uw virtuele netwerk. U kunt nog steeds beide functies gebruiken om toegang te krijgen tot bronnen in netwerken die niet zijn verbonden met uw virtuele netwerk.

U kunt bijvoorbeeld VNET-integratie gebruiken om te integreren met een virtueel netwerk dat zich in uw abonnement bevindt, maar is niet verbonden met het virtuele netwerk waarin uw ASE zich bevindt. U kunt nog steeds Hybride verbindingen gebruiken om toegang te krijgen tot bronnen die zich in andere netwerken bevinden, net zoals u dat ook normaal kunt doen.  

Als uw virtuele netwerk is geconfigureerd met een ExpressRoute-VPN, moet u rekening houden met enkele van de routerings behoeften die een ASE heeft. Er zijn een aantal door de gebruiker gedefinieerde route (UDR) configuraties die niet compatibel zijn met een ASE. Zie [een app service Environment uitvoeren in een virtueel netwerk met ExpressRoute][ExpressRoute]voor meer informatie over het uitvoeren van een ASE in een virtueel netwerk met ExpressRoute.

#### <a name="securing-inbound-traffic"></a>Inkomend verkeer beveiligen
Er zijn twee primaire methoden om inkomend verkeer naar uw ASE te beheren.  U kunt netwerk beveiligings groepen (Nsg's) gebruiken om te bepalen welke IP-adressen toegang hebben tot uw ASE, zoals hier wordt beschreven [hoe u inkomend verkeer in een app service Environment kunt beheren en hoe](app-service-app-service-environment-control-inbound-traffic.md) u uw ASE ook kunt configureren met een interne load BALANCER (ILB).  Deze functies kunnen ook samen worden gebruikt als u de toegang wilt beperken met behulp van Nsg's naar uw ILB-ASE.

Wanneer u een ASE maakt, wordt er een VIP in uw VNet gemaakt.  Er zijn twee typen VIP: externe en interne.  Wanneer u een ASE met een externe VIP maakt, zijn uw apps in uw ASE toegankelijk via een IP-adres dat via internet kan worden gerouteerd. Wanneer u intern selecteert, wordt uw ASE geconfigureerd met een ILB en is deze niet rechtstreeks toegankelijk via internet.  Voor een ILB ASE is nog steeds een externe VIP vereist, maar deze wordt alleen gebruikt voor toegang tot Azure-beheer en-onderhoud.  

Tijdens het maken van ILB ASE geeft u het subdomein op dat wordt gebruikt door de ILB ASE en moet u uw eigen DNS beheren voor het subdomein dat u opgeeft.  Omdat u de naam van het subdomein hebt ingesteld, moet u ook het certificaat beheren dat wordt gebruikt voor HTTPS-toegang.  Nadat u ASE hebt gemaakt, wordt u gevraagd om het certificaat op te geven.  Voor meer informatie over het maken en gebruiken van een ILB ASE lezen met [behulp van een interne Load Balancer met een app service Environment][ILBASE]. 

## <a name="portal"></a>Portal
U kunt uw App Service Environment beheren en bewaken met behulp van de gebruikers interface in de Azure Portal. Als u een ASE hebt, ziet u waarschijnlijk het App Service-symbool op uw zijbalk. Dit symbool wordt gebruikt om App Service omgevingen in het Azure Portal weer te geven:

![App Service Environment-symbool][1]

Als u de gebruikers interface wilt openen waarin al uw App Service omgevingen worden weer gegeven, kunt u het pictogram gebruiken of het scheidings teken (' > ') selecteren aan de onderkant van de zijbalk om App Service omgevingen te selecteren. Als u een van de as selecteert, opent u de gebruikers interface die wordt gebruikt om deze te controleren en te beheren.

![Gebruikers interface voor het bewaken en beheren van uw App Service Environment][2]

Op de eerste blade ziet u enkele eigenschappen van uw ASE, samen met een metrische grafiek per resource groep. Sommige van de eigenschappen die worden weer gegeven in het **Essentials** -blok zijn ook Hyper links waarmee de Blade wordt geopend die eraan is gekoppeld. U kunt bijvoorbeeld de **Virtual Network** naam selecteren om de gebruikers interface te openen die is gekoppeld aan het virtuele netwerk waarop uw ASE wordt uitgevoerd. **App service plannen** en **apps** elke open Blades die een lijst met deze items in uw ASE.  

### <a name="monitoring"></a>Bewaking
Met de grafieken kunt u diverse prestatie gegevens in elke resource groep bekijken. Voor de front-end-pool kunt u de gemiddelde CPU en het geheugen bewaken. Voor werk groepen kunt u de hoeveelheid bewaken die wordt gebruikt en de hoeveelheid die beschikbaar is.

Meerdere App Service-abonnementen kunnen gebruikmaken van de werk rollen in een werk groep. De werk belasting wordt niet op dezelfde manier gedistribueerd als bij de front-endservers, waardoor het CPU-en geheugen gebruik niet veel op de manier van nuttige informatie wordt geboden. Het is belang rijker om bij te houden hoeveel werk rollen u hebt gebruikt en beschikbaar is, met name als u dit systeem beheert zodat anderen het kunnen gebruiken.  

U kunt ook alle metrische gegevens die in de grafieken kunnen worden bijgehouden, gebruiken om waarschuwingen in te stellen. Het instellen van waarschuwingen is hetzelfde als elders in App Service. U kunt een waarschuwing instellen vanuit het onderdeel **waarschuwingen** van de gebruikers interface of van inzoomen in een metrische gebruikers interface en een **waarschuwing toevoegen** selecteren.

![Gebruikers interface voor metrische gegevens][3]

De metrische gegevens die zojuist zijn besproken, zijn de App Service Environment metrische gegevens. Er zijn ook metrische gegevens die beschikbaar zijn op het niveau van de App Service plan. Hier is de bewakings-CPU en het geheugen veel zinniger.

In een ASE zijn alle App Service plannen toegewezen App Service plannen. Dit betekent dat de enige apps die worden uitgevoerd op de hosts die worden toegewezen aan dat App Service plan de apps zijn in dat App Service plan. Als u de details van uw App Service plan wilt zien, gaat u naar uw App Service plan vanuit een van de lijsten in de ASE-gebruikers interface of door **app service plannen te bladeren** (waarin al deze items worden weer gegeven).   

### <a name="settings"></a>Instellingen
Op de Blade ASE vindt u een sectie met **instellingen** die een aantal belang rijke mogelijkheden bevat:

**Instellingen**  >  **Eigenschappen**: de Blade **instellingen** wordt automatisch geopend wanneer u uw ASE-Blade samenbrengt. Bovenaan vindt u **Eigenschappen**. Er zijn hier een aantal items die overbodig zijn voor wat u ziet in **Essentials**, maar wat nuttig is voor **virtueel IP-adres**, evenals **uitgaande IP-adressen**.

![Blade instellingen en eigenschappen][4]

**Instellingen**  >  **IP-adressen**: wanneer u een ip-Secure Sockets Layer (SSL)-app in uw ASE maakt, hebt u een IP SSL-adres nodig. Uw ASE moet IP SSL adressen hebben waarvan het eigenaar is dat deze kan worden toegewezen. Wanneer een ASE wordt gemaakt, heeft deze een IP SSL adres voor dit doel, maar u kunt er meer toevoegen. Er worden kosten in rekening gebracht voor extra IP SSL adressen, zoals wordt weer gegeven in [app service prijzen][AppServicePricing] (in de sectie op SSL-verbindingen). De extra prijs is de IP SSL prijs.

**Instellingen**  >  **Front-end-pool**  /  **Werk groepen**: elk van deze Blades van de resource groep biedt de mogelijkheid om alleen informatie in die resource groep te bekijken, naast de besturings elementen om de resource groep volledig te schalen.  

De basis-Blade voor elke resource groep biedt een grafiek met metrische gegevens voor die resource groep. Net als bij de grafieken van de Blade ASE kunt u naar de grafiek gaan en de gewenste waarschuwingen instellen. Het instellen van een waarschuwing op de Blade ASE voor een specifieke resource groep heeft hetzelfde effect als het uitvoeren van de resource groep. Op de Blade **instellingen** voor werk groepen hebt u toegang tot alle Apps of app service plannen die worden uitgevoerd in deze werk groep.

![Gebruikers interface voor instellingen van werk groep][5]

### <a name="portal-scale-capabilities"></a>Schaal mogelijkheden voor portal
Er zijn drie schaal bewerkingen:

* Wijzigen van het aantal IP-adressen in de ASE dat beschikbaar is voor IP SSL gebruik.
* De grootte wijzigen van de reken resource die wordt gebruikt in een resource groep.
* Wijzigen van het aantal reken resources dat wordt gebruikt in een resource groep, hetzij hand matig of automatisch schalen.

In de portal zijn er drie manieren om te bepalen hoeveel servers u in uw resource groepen hebt:

* Een schaal bewerking vanuit de hoofd-ASE-Blade bovenaan. U kunt meerdere schaal configuratie wijzigingen aanbrengen in de front-end-en worker-groepen. Ze worden allemaal toegepast als één bewerking.
* Een hand matige schaal bewerking vanaf de Blade van de afzonderlijke resource groep **schaal** , die onder **instellingen** valt.
* Automatisch schalen, dat u instelt op de Blade **schaal** van de afzonderlijke resource groep.

Als u de schaal bewerking op de Blade ASE wilt gebruiken, sleept u de schuif regelaar naar de gewenste hoeveelheid en slaat u deze op. Deze gebruikers interface biedt ook ondersteuning voor het wijzigen van de grootte.  

![Gebruikers interface schalen][6]

Als u de mogelijkheden hand matig of automatisch schalen in een specifieke resource groep wilt gebruiken, gaat u naar de gewenste **instellingen** van de  >  **front-end groep**  /  **werk groepen** . Open vervolgens de groep die u wilt wijzigen. Ga naar **instellingen**  >  **uitschalen** of **instellingen**  >  **Omhoog schalen**. Met de Blade **uitschalen** kunt u de hoeveelheid van het exemplaar bepalen. U kunt de resource grootte bepalen door **omhoog te schalen** .  

![Gebruikers interface voor schaal instellingen][7]

## <a name="fault-tolerance-considerations"></a>Overwegingen voor fout tolerantie
U kunt een App Service Environment configureren voor het gebruik van Maxi maal 55 totale reken resources. Van deze 55 Compute-resources kan alleen 50 worden gebruikt voor het hosten van werk belastingen. De reden hiervoor is twee. Er zijn mini maal twee front-end Compute-resources.  Dat blijft 53 voor ondersteuning van de toewijzing van worker-groepen. Als u fout tolerantie wilt opgeven, moet u een extra reken Resource hebben die wordt toegewezen aan de hand van de volgende regels:

* Elke werk groep moet ten minste één extra Compute-Resource hebben die niet beschikbaar is om een werk belasting te krijgen.
* Wanneer het aantal Compute-resources in een werk groep boven een bepaalde waarde komt, is een andere reken resource vereist voor fout tolerantie. Dit is niet het geval in de front-end-pool.

Binnen een groep werk nemers gelden de fout tolerantie vereisten voor een bepaalde waarde van X-resources die zijn toegewezen aan een werknemers groep:

* Als X tussen 2 en 20 ligt, is de hoeveelheid bruikbare reken resources die u voor werk belastingen kunt gebruiken X-1.
* Als X tussen 21 en 40 ligt, is de hoeveelheid bruikbare reken resources die u voor werk belastingen kunt gebruiken X-2.
* Als X tussen 41 en 53, is de hoeveelheid bruikbare reken resources die u voor werk belastingen kunt gebruiken X-3.

De minimale footprint heeft 2 front-end servers en twee werk rollen.  Met de bovenstaande instructies zijn er hier enkele voor beelden om te verduidelijken:  

* Als u 30 werk nemers in één pool hebt, kunnen ze ook worden gebruikt voor het hosten van werk belastingen.
* Als u twee werk nemers in één pool hebt, kan 1 worden gebruikt voor het hosten van werk belastingen.
* Als u 20 werk nemers in één groep hebt, kan 19 worden gebruikt voor het hosten van werk belastingen.  
* Als u al 21 werk nemers in één pool hebt, dan kan nog steeds 19 worden gebruikt voor het hosten van werk belastingen.  

Het aspect van de fout tolerantie is belang rijk, maar u moet er rekening mee laten lopen wanneer u boven bepaalde drempel waarden schaalt. Als u meer capaciteit wilt toevoegen dan 20 exemplaren, gaat u naar 22 of hoger omdat 21 geen capaciteit meer toevoegt. Hetzelfde geldt voor de waarde van 40, waarbij het volgende aantal extra capaciteit is 42.  

## <a name="deleting-an-app-service-environment"></a>Een App Service Environment verwijderen
Als u een App Service Environment wilt verwijderen, gebruikt u gewoon de actie **verwijderen** boven aan de blade app service environment. Wanneer u dit doet, wordt u gevraagd de naam van uw App Service Environment op te geven om te bevestigen dat u dit echt wilt doen. Houd er rekening mee dat wanneer u een App Service Environment verwijdert, u ook alle inhoud erin verwijdert.  

![Een App Service Environment gebruikers interface verwijderen][9]  

## <a name="getting-started"></a>Aan de slag
Zie [een app service Environment maken](app-service-web-how-to-create-an-app-service-environment.md)om aan de slag te gaan met app service omgevingen.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md