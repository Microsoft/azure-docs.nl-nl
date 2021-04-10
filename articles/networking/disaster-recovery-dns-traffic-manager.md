---
title: Herstel na nood geval met behulp van Azure DNS en Traffic Manager | Microsoft Docs
description: Overzicht van de oplossing voor herstel na nood gevallen met behulp van Azure DNS en Traffic Manager.
services: dns
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/08/2018
ms.author: kumud
ms.openlocfilehash: 8cb1a490ac8edf2630253b45d99c3394bbe721b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234151"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Herstel na noodgevallen met Azure DNS en Traffic Manager

Herstel na noodgevallen is gericht op het herstellen van een ernstig verlies van de functionaliteit van de toepassing. Als u een oplossing voor herstel na nood gevallen wilt kiezen, moeten zakelijke en technologische eigen aars eerst het benodigde functionaliteits niveau bepalen tijdens een nood geval, zoals-niet beschikbaar, gedeeltelijk beschikbaar via verminderde functionaliteit of een vertraagde Beschik baarheid of een volledig beschik bare versie.
De meeste zakelijke klanten kiezen een architectuur met meerdere regio's voor tolerantie op basis van een failover op toepassings-of infrastructuur niveau. Klanten kunnen kiezen voor een aantal benaderingen in het streven naar failover en hoge Beschik baarheid via redundante architectuur. Hier volgen enkele van de populaire benaderingen:

- **Actief-passief met koude stand-by**: in deze failover-oplossing zijn de vm's en andere apparaten die worden uitgevoerd in de stand-bymodus niet actief totdat er een failover nodig is. De productie omgeving wordt echter gerepliceerd in de vorm van back-ups, VM-installatie kopieën of Resource Manager-sjablonen naar een andere regio. Dit failover-mechanisme is kosten effectief, maar neemt langer tijd om een volledige failover uit te voeren.
 
    ![Actief/passief met koude stand-by](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Afbeelding-actief/passief met configuratie voor nood herstel bij koude stand-by*

- **Actief/passief met pilot light**: in deze failover-oplossing wordt de standby-omgeving ingesteld met een minimale configuratie. Het installatie programma heeft alleen de benodigde services die worden uitgevoerd ter ondersteuning van een minimale en kritieke set toepassingen. In de systeem eigen vorm kan dit scenario alleen minimale functionaliteit uitvoeren, maar kan er extra services worden opgeschaald om grote hoeveel heden productie belasting uit te voeren als er een failover optreedt.
    
    ![Actief/passief met pilot light](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Afbeelding: actief/passief met configuratie voor nood herstel met pilot lampje*

- **Actief/passief met warme stand-by**: in deze failover-oplossing is de stand-by-regio vooraf gewarmd en is deze gereed om de basis belasting te halen, automatisch schalen is ingeschakeld en alle exemplaren actief zijn. Deze oplossing wordt niet geschaald om de volledige productie belasting te halen, maar is functioneel en alle services zijn actief. Deze oplossing is een uitgebreide versie van de test lampje-benadering.
    
    ![Actief/passief met warme stand-by](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Afbeelding: actief/passief met warme stand-by nood herstel configuratie*
    
Zie [herstel na nood gevallen voor Azure-toepassingen](/azure/architecture/resiliency/disaster-recovery-azure-applications)voor meer informatie over failover en hoge Beschik baarheid.


## <a name="planning-your-disaster-recovery-architecture"></a>De architectuur voor herstel na nood gevallen plannen

Er zijn twee technische aspecten voor het instellen van uw nood herstel architectuur:
-  Een implementatie mechanisme gebruiken om instanties, gegevens en configuraties tussen primaire en stand-by-omgevingen te repliceren. Dit type herstel na nood geval kan met behulp van Azure Site-Recovery worden uitgevoerd via Microsoft Azure partner-apparaten/-services zoals Veritas of NetApp. 
- Het ontwikkelen van een oplossing voor het omleiden van netwerk-/webverkeer van de primaire site naar de stand-by-site. Dit type herstel na nood geval kan worden bereikt via Azure DNS, Azure Traffic Manager (DNS) of globale load balancers van derden.

Dit artikel is beperkt tot benaderingen via netwerk-en webverkeer omleiden. Zie [Azure site Recovery-documentatie](../site-recovery/index.yml)voor instructies voor het instellen van Azure site Recovery.
DNS is een van de meest efficiënte mechanismen voor het omleiden van netwerk verkeer omdat DNS vaak globaal en extern is voor het Data Center en wordt geïsoleerd van eventuele storingen op het niveau van regionale of beschikbaarheids zones (AZ). Eén kan gebruikmaken van een op DNS gebaseerd failover-mechanisme en in azure, twee DNS-services kunnen worden uitgevoerd op een manier Azure DNS (gezaghebbende DNS) en Azure Traffic Manager (route ring op basis van op DNS gebaseerd verkeer). 

Het is belang rijk om inzicht te krijgen in enkele concepten in DNS die uitvoerig worden gebruikt voor het bespreken van de oplossingen die in dit artikel worden beschreven:
- **DNS A-record** : a-records zijn pointers die verwijzen naar een IPv4-adres. 
- **CNAME of canonieke naam** : dit record type wordt gebruikt om naar een andere DNS-record te verwijzen. CNAME reageert niet met een IP-adres, maar in plaats daarvan met de verwijzing naar de record die het IP-adres bevat. 
- **Gewogen route ring** : één kan ervoor kiezen om een gewicht aan service-eind punten te koppelen en vervolgens het verkeer te verdelen op basis van het toegewezen gewicht. Deze routerings methode is een van de vier mechanismen voor het routeren van verkeer die beschikbaar is in Traffic Manager. Zie voor meer informatie [gewogen routerings methode](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Prioriteits routering** : prioriteits routering is gebaseerd op status controles van eind punten. Standaard verzendt Azure Traffic Manager al het verkeer naar het eind punt met de hoogste prioriteit en bij een storing of ramp Traffic Manager het verkeer naar het secundaire eind punt routeren. Zie voor meer informatie [routerings methode voor prioriteit](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method).

## <a name="manual-failover-using-azure-dns"></a>Hand matige failover met Azure DNS
De Azure DNS hand matige failover-oplossing voor herstel na nood gevallen gebruikt het standaard-DNS-mechanisme om een failover naar de back-upsite te maken. De hand matige optie via Azure DNS werkt het beste als u deze gebruikt in combi natie met de koude stand-by-modus of de test lampje-methode. 

![Hand matige failover met Azure DNS](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Afbeelding-hand matige failover met Azure DNS*

De veronderstellingen die zijn gemaakt voor de oplossing zijn:
- Zowel het primaire als het secundaire eind punt hebben statische IP-adressen die niet vaak worden gewijzigd. Stel dat het IP-adres van de primaire site 100.168.124.44 is en dat het IP-adres voor de secundaire site 100.168.124.43 is.
- Er bestaat een Azure DNS zone voor zowel de primaire als de secundaire site. Stel dat voor de primaire site het eind punt prod.contoso.com is en dat de back-upsite dr.contoso.com is. Er bestaat ook een DNS-record voor de hoofd toepassing die als www-contoso.com wordt genoemd \. .   
- De TTL is op of onder de SLA voor RTO in de organisatie. Als een onderneming bijvoorbeeld de RTO van de toepassing voor een nood geval instelt op 60 minuten, moet de TTL kleiner zijn dan 60 minuten, bij voor keur de lagere waarde. 
  U kunt als volgt Azure DNS instellen voor hand matige failover:
- Een DNS-zone maken
- DNS-zone records maken
- CNAME-record bijwerken

### <a name="step-1-create-a-dns"></a>Stap 1: een DNS maken
Maak een DNS-zone (bijvoorbeeld www \. -contoso.com) zoals hieronder wordt weer gegeven:

![Een DNS-zone maken in azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Afbeelding: een DNS-zone maken in azure*

### <a name="step-2-create-dns-zone-records"></a>Stap 2: DNS-zone records maken

In deze zone maakt u drie records (bijvoorbeeld-www \. contoso.com, Prod.contoso.com en Dr.consoto.com), zoals hieronder weer gegeven.

![DNS-zone records maken](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Afbeelding: DNS-zone records maken in azure*

In dit scenario heeft de site www- \. contoso.com een TTL van 30 minuten. Dit is onder de vermelde RTO en verwijst naar de productie site Prod.contoso.com. Deze configuratie is tijdens normale bedrijfs activiteiten. De TTL van prod.contoso.com en dr.contoso.com is ingesteld op 300 seconden of 5 minuten. U kunt een Azure-bewakings service gebruiken, zoals Azure Monitor of Azure-app inzichten, of alle oplossingen voor het controleren van de partner, zoals Dynatrace, u kunt zelfs gekweekte oplossingen gebruiken waarmee u toepassingen kunt bewaken of fouten op virtuele infra structuur kunnen controleren.

### <a name="step-3-update-the-cname-record"></a>Stap 3: de CNAME-record bijwerken

Wanneer de fout is gedetecteerd, wijzigt u de record waarde zodat deze verwijst naar dr.contoso.com, zoals hieronder wordt weer gegeven:
       
![CNAME-record bijwerken](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Afbeelding: de CNAME-record in azure bijwerken*

Binnen 30 minuten, gedurende welke de meeste resolvers het zone bestand in de cache vernieuwen, wordt een query naar www- \. contoso.com omgeleid naar Dr.contoso.com.
U kunt ook de volgende Azure CLI-opdracht uitvoeren om de CNAME-waarde te wijzigen:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Deze stap kan hand matig of via Automation worden uitgevoerd. Dit kan hand matig worden gedaan via de-console of door de Azure CLI. De Azure SDK en API kunnen worden gebruikt voor het automatiseren van de CNAME-update zodat er geen hand matige interventie nodig is. Automation kan worden gebouwd met behulp van Azure functions of binnen een bewakings toepassing van derden of zelfs van on-premises.

### <a name="how-manual-failover-works-using-azure-dns"></a>Hoe hand matige failover werkt met Azure DNS
Omdat de DNS-server zich buiten de failover-of ramp zone bevindt, wordt deze voor elke downtime geïsoleerd. Hiermee kan de gebruiker een eenvoudig failover-scenario bouwen dat rendabel is en het werkt op alle tijdstippen waarbij de operator netwerk verbinding heeft tijdens een nood geval en de Flip kan maken. Als de oplossing is gescripteerd, moet er een worden geïsoleerd dat de server of service die het script uitvoert, moet worden geisolatied tegen het probleem dat van invloed is op de productie omgeving. Houd ook rekening met de lage TTL die is ingesteld voor de zone, zodat er geen resolver over de hele wereld het eind punt in de cache voor lange en klanten toegang heeft tot de site binnen de RTO. Voor een koud stand-by-en pilot lampje is het mogelijk dat er een aantal voorbereidings-en andere beheer activiteiten vereist zijn: er moet ook voldoende tijd zijn voordat de Flip kan worden gespiegeld.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatische failover met Azure Traffic Manager
Wanneer u complexe architecturen en meerdere sets resources hebt waarmee dezelfde functie kan worden uitgevoerd, kunt u Azure Traffic Manager (op basis van DNS) configureren om de status van uw resources te controleren en het verkeer van de niet-gezonde resource te routeren naar de in orde zijnde resource. In het volgende voor beeld hebben zowel de primaire regio als de secundaire regio een volledige implementatie. Deze implementatie omvat de Cloud Services en een gesynchroniseerde data base. 

![Automatische failover met Azure Traffic Manager](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Afbeelding-automatische failover met Azure Traffic Manager*

De netwerk aanvragen van de gebruikers worden echter alleen actief verwerkt door de primaire regio. De secundaire regio wordt alleen actief wanneer de primaire regio een service onderbreking ondervindt. In dat geval worden alle nieuwe netwerk aanvragen gerouteerd naar de secundaire regio. Omdat het maken van een back-up van de data base zich in de buurt bevindt, hebben beide load balancers IP-adressen waarvoor de status kan worden gecontroleerd en zijn de exemplaren altijd actief. deze topologie biedt een optie voor het uitvoeren van een lage RTO en failover zonder hand matige tussen komst. De secundaire failover-regio moet direct na uitval van de primaire regio gereed zijn voor gebruik.
Dit scenario is ideaal voor het gebruik van Azure-Traffic Manager met ingebouwde tests voor verschillende soorten status controles, waaronder HTTP/HTTPS en TCP. Azure Traffic Manager heeft ook een regel engine die kan worden geconfigureerd voor failover als er een fout optreedt zoals hieronder wordt beschreven. Laten we eens kijken naar de volgende oplossing met behulp van Traffic Manager:
- De klant heeft de regio #1 eind punt bekend als prod.contoso.com met een statisch IP-adres als 100.168.124.44 en een regio #2 eind punt dat wordt aangeduid als dr.contoso.com met een statisch IP-adres als 100.168.124.43. 
-   Elk van deze omgevingen bevindt zich in een open bare eigenschap, zoals een load balancer. De load balancer kan worden geconfigureerd met een op een DNS gebaseerd eind punt of een Fully Qualified Domain Name (FQDN) zoals hierboven wordt weer gegeven.
-   Alle instanties in regio 2 zijn bijna realtime-replicatie met regio 1. De machine installatie kopieën zijn bijgewerkt en alle software-en configuratie gegevens worden gepatched en zijn in overeenstemming met regio 1.  
-   Automatisch schalen is vooraf vooraf geconfigureerd. 

De stappen voor het configureren van de failover met Azure Traffic Manager zijn als volgt:
1. Een nieuw Azure Traffic Manager-profiel maken
2. Eind punten maken binnen het Traffic Manager profiel
3. Status controle en failover-configuratie instellen

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Stap 1: een nieuw Azure Traffic Manager-profiel maken
Maak een nieuw Azure Traffic Manager-profiel met de naam contoso123 en selecteer de routerings methode als prioriteit. Als u een bestaande resource groep hebt waaraan u wilt koppelen, kunt u een bestaande resource groep selecteren, anders maakt u een nieuwe resource groep.

![Traffic Manager profiel maken](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Afbeelding: een Traffic Manager profiel maken*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Stap 2: eind punten maken binnen het Traffic Manager profiel

In deze stap maakt u eind punten die verwijzen naar de sites voor productie en nood herstel. Kies hier het **type** als een extern eind punt, maar als de resource wordt gehost in azure, kunt u ook **Azure-eind punt** kiezen. Als u **Azure-eind punt** kiest, selecteert u een **doel bron** die een **app service** of een **openbaar IP-adres** is dat wordt toegewezen door Azure. De prioriteit is ingesteld op **1** , omdat deze de primaire service voor regio 1 is.
U kunt ook het eind punt voor herstel na nood gevallen maken binnen Traffic Manager.

![Nood herstel eindpunten maken](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Afbeelding-nood herstel eindpunten maken*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Stap 3: status controle en failover-configuratie instellen

In deze stap stelt u de DNS TTL in op 10 seconden, die wordt gehonoreerd door de meeste recursieve resolvers op internet. Deze configuratie betekent dat de gegevens niet langer dan 10 seconden in de cache worden opgeslagen. Voor de instellingen van het controle programma voor eind punten is het pad actueel ingesteld op/of root, maar u kunt de eindpunt instellingen aanpassen om een pad te evalueren, bijvoorbeeld prod.contoso.com/index. In het onderstaande voor beeld ziet u de **https** als het probing-protocol. U kunt echter ook **http** of **TCP** kiezen. De keuze van het protocol is afhankelijk van de eind toepassing. Het probing-interval is ingesteld op 10 seconden, waardoor het zoeken snel kan worden uitgevoerd en de nieuwe poging is ingesteld op 3. Als gevolg hiervan Traffic Manager een failover naar het tweede eind punt als er drie opeenvolgende intervallen een fout registreren. De volgende formule definieert de totale tijd voor een automatische failover: tijdstip voor failover = TTL + nieuwe poging * probing-interval en in dit geval is de waarde 10 + 3 * 10 = 40 seconden (max).
Als de nieuwe poging is ingesteld op 1 en TTL is ingesteld op 10 seconden, wordt de tijd voor failover 10 + 1 * 10 = 20 seconden. Stel de nieuwe poging in op een waarde die groter is dan **1** om de kans op failovers te elimineren als gevolg van valse positieve of secundaire netwerk problemen. 


![Status controle instellen](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Afbeelding: status controle en failover-configuratie instellen*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Hoe automatische failover werkt met Traffic Manager

Tijdens een nood geval wordt het primaire eind punt onderzocht en wordt de status gewijzigd in **gedegradeerd** en blijft de site voor nood herstel **online**. Standaard verzendt Traffic Manager al het verkeer naar het primaire eindpunt (met de hoogste prioriteit). Als het primaire eind punt gedegradeerd wordt weer gegeven, stuurt Traffic Manager het verkeer naar het tweede eind punt, zolang dit in orde is. Een van de opties voor het configureren van meer eind punten in Traffic Manager die als extra failover-eind punten kunnen fungeren, of, als load balancers de belasting tussen eind punten delen.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Meer informatie over [Azure DNS](../dns/dns-overview.md).