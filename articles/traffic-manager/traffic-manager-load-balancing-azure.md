---
title: Taakverdelings Services gebruiken in azure | Microsoft Docs
description: 'Deze zelf studie laat zien hoe u een scenario maakt met behulp van de Azure Load Balancing Port Folio: Traffic Manager, Application Gateway en Load Balancer.'
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: duau
ms.openlocfilehash: eaf50f3bdacaf5680bc5ecb1379faff20133b5ce
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184370"
---
# <a name="using-load-balancing-services-in-azure"></a>Taakverdelingsservices gebruiken in Azure

## <a name="introduction"></a>Inleiding

Microsoft Azure biedt meerdere services voor het beheren van hoe netwerk verkeer wordt gedistribueerd en taak verdeling. U kunt deze services afzonderlijk gebruiken of hun methoden combi neren, afhankelijk van uw behoeften, om de optimale oplossing te bouwen.

In deze zelf studie definieert u eerst een gebruiks voorbeeld van een klant en ziet u hoe deze kan worden verbeterd en kunnen worden uitgevoerd met behulp van de volgende Azure Load Balancing-Port Folio: Traffic Manager, Application Gateway en Load Balancer. We geven vervolgens stapsgewijze instructies voor het maken van een implementatie die geografisch redundant is, verkeer naar Vm's, en helpt u bij het beheren van verschillende typen aanvragen.

Bij een conceptueel niveau speelt elk van deze services een afzonderlijke rol in de taakverdelings hiërarchie.

* **Traffic Manager** voorziet in globale DNS-taak verdeling. Er wordt gekeken naar inkomende DNS-aanvragen en reageert met een gezonde eind punt, in overeenstemming met het routerings beleid dat de klant heeft geselecteerd. Opties voor routerings methoden zijn:
  * Prestatie routering voor het verzenden van de aanvrager naar het dichtstbijzijnde eind punt in termen van latentie.
  * Prioriteits routering om al het verkeer door te sturen naar een eind punt, met andere eind punten als back-up.
  * Gewogen Round Robin-route ring, waarmee verkeer wordt gedistribueerd op basis van de weging die aan elk eind punt is toegewezen.
  * Op geografische basis route ring om het verkeer naar uw toepassings eindpunten te distribueren op basis van de geografische locatie van de gebruiker.
  * Route ring op basis van het subnet om het verkeer naar uw toepassings eindpunten te distribueren op basis van het subnet (IP-adres bereik) van de gebruiker.
  * Route ring met meerdere waarden waarmee u IP-adressen van meer dan één toepassings eindpunten kunt verzenden in één DNS-antwoord.

  De client maakt rechtstreeks verbinding met het eind punt dat wordt geretourneerd door Traffic Manager. Azure Traffic Manager detecteert wanneer een eind punt een slechte status heeft en stuurt de clients vervolgens om naar een ander ongezonde exemplaar. Raadpleeg de [documentatie van Azure Traffic Manager](traffic-manager-overview.md) voor meer informatie over de service.
* **Application Gateway** biedt ADC (Application Delivery controller) als een service met verschillende mogelijkheden voor taak verdeling voor laag 7 voor uw toepassing. Hiermee kunnen klanten de productiviteit van webfarms optimaliseren door CPU-intensieve TLS-beëindiging naar de Application Gateway te offloaden. Andere routerings mogelijkheden voor laag 7 zijn onder andere round-robin distributie van inkomend verkeer, sessie affiniteit op basis van cookies, route ring op basis van URL-pad en de mogelijkheid om meerdere websites achter één toepassings gateway te hosten. Application Gateway kunnen worden geconfigureerd als een Internet gerichte gateway, een alleen-interne gateway of een combi natie van beide. Application Gateway is volledig beheerde, schaal bare en Maxi maal beschik bare Azure. Het biedt een uitgebreide verzameling diagnostische gegevens en functies voor logboekregistratie voor betere beheersbaarheid.
* **Load Balancer** is een integraal onderdeel van de Azure Sdn-stack, met hoge prestaties en lage latentie Services voor taak verdeling voor alle UDP-en TCP-protocollen. Het beheert binnenkomende en uitgaande verbindingen. U kunt openbare en interne load-balancedeindpunten configureren en regels definiëren om binnenkomende verbindingen toe te wijzen aan back-end-poolbestemmingen met behulp van TCP- en HTTP-opties voor statustesten om de beschikbaarheid van de service te beheren.

## <a name="scenario"></a>Scenario

In dit voorbeeld scenario gebruiken we een eenvoudige website die twee soorten inhoud bedient: afbeeldingen en dynamisch gerenderde webpagina's. De website moet geografisch redundant zijn, en deze moet de gebruikers van de dichtstbijzijnde (laagste latentie) locatie. De ontwikkelaar van de toepassing heeft besloten dat alle Url's die overeenkomen met het patroon/images/* worden geleverd vanuit een toegewezen groep Vm's die afwijken van de rest van de webfarm.

Daarnaast moet de standaard-VM-groep met de dynamische inhoud communiceren met een back-end-data base die wordt gehost op een cluster met hoge Beschik baarheid. De volledige implementatie wordt ingesteld via Azure Resource Manager.

Met behulp van Traffic Manager, Application Gateway en Load Balancer kan deze website de volgende ontwerp doelstellingen bereiken:

* **Multi-geo-redundantie**: als een regio uitvalt, Traffic Manager verkeer naadloos naar de dichtstbijzijnde regio gerouteerd zonder tussen komst van de eigenaar van de toepassing.
* **Beperkte latentie**: omdat Traffic Manager de klant automatisch doorstuurt naar de dichtstbijzijnde regio, ondervindt de klant een lagere latentie bij het aanvragen van de inhoud van de webpagina.
* **Onafhankelijke schaal baarheid**: omdat de werk belasting van de web-app wordt gescheiden door het type inhoud, kan de eigenaar van de toepassing de werk belasting van de aanvraag onafhankelijk van elkaar schalen. Application Gateway zorgt ervoor dat het verkeer wordt doorgestuurd naar de juiste Pools op basis van de opgegeven regels en de status van de toepassing.
* **Interne taak verdeling**: omdat Load Balancer zich vóór het cluster met hoge Beschik baarheid bevindt, wordt alleen het actieve en gezonde eind punt voor een Data Base blootgesteld aan de toepassing. Daarnaast kan een database beheerder de werk belasting optimaliseren door actieve en passieve replica's in het cluster onafhankelijk van de front-end-toepassing te distribueren. Load Balancer levert verbindingen met het cluster met hoge Beschik baarheid en zorgt ervoor dat alleen in orde zijnde data bases verbindings aanvragen ontvangen.

In het volgende diagram ziet u de architectuur van dit scenario:

![Diagram van de architectuur voor taak verdeling](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Dit voor beeld is slechts een van de vele mogelijke configuraties van de taakverdelings services die Azure biedt. Traffic Manager, Application Gateway en Load Balancer kunnen worden gecombineerd en afgestemd op uw behoeften voor taak verdeling. Als bijvoorbeeld TLS-offload of Layer 7-verwerking niet nodig is, kan Load Balancer worden gebruikt in plaats van Application Gateway.

## <a name="setting-up-the-load-balancing-stack"></a>De taakverdelings stack instellen

### <a name="step-1-create-a-traffic-manager-profile"></a>Stap 1: een Traffic Manager profiel maken

1. Klik in de Azure Portal op **een resource maken**  >  **netwerk**  >  **Traffic Manager profiel**  >  **maken**.
2. Voer de volgende basis informatie in:

   * **Naam**: geef uw Traffic Manager een naam voor het DNS-voor voegsel.
   * **Routerings methode**: Selecteer het beleid voor de routerings methode voor verkeer. Zie [over Traffic Manager verkeers routerings methoden](traffic-manager-routing-methods.md)voor meer informatie over de methoden.
   * **Abonnement**: Selecteer het abonnement dat het profiel bevat.
   * **Resource groep**: Selecteer de resource groep die het profiel bevat. Dit kan een nieuwe of bestaande resource groep zijn.
   * **Locatie van de resource groep**: de Traffic Manager service is globaal en niet gebonden aan een locatie. U moet echter een regio opgeven voor de groep waarin de meta gegevens van het Traffic Manager profiel zich bevinden. Deze locatie heeft geen invloed op de runtime-Beschik baarheid van het profiel.

3. Klik op **maken** om het Traffic Manager profiel te genereren.

   ![Blade Traffic Manager maken](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Stap 2: de toepassings gateways maken

1. Klik in het linkerdeel venster van de Azure Portal op **een resource maken**  >  **netwerk**  >  **Application Gateway**.
2. Voer de volgende basis informatie over de toepassings gateway in:

   * **Naam**: de naam van de toepassings gateway.
   * **SKU-grootte**: de grootte van de toepassings gateway, beschikbaar als klein, gemiddeld of groot.
   * **Aantal** exemplaren: het aantal instanties, een waarde van 2 tot en met 10.
   * **Resource groep**: de resource groep die de toepassings gateway bevat. Dit kan een bestaande resource groep of een nieuwe zijn.
   * **Locatie**: de regio voor de toepassings gateway. Dit is dezelfde locatie als de resource groep. De locatie is belang rijk, omdat het virtuele netwerk en het open bare IP-adres zich op dezelfde locatie bevindt als de gateway.
3. Klik op **OK**.
4. Definieer het virtuele netwerk, subnet, front-end IP en listener-configuraties voor de toepassings gateway. In dit scenario is het IP-adres van de front-end **openbaar**, zodat het kan worden toegevoegd als een eind punt aan het profiel van de Traffic Manager op een later tijdstip.
5. Configureer de listener met een van de volgende opties:
    * Als u HTTP gebruikt, hoeft u niets te configureren. Klik op **OK**.
    * Als u HTTPS gebruikt, is verdere configuratie vereist. Raadpleeg voor [het maken van een toepassings gateway](../application-gateway/quick-create-portal.md), beginnend bij stap 9. Wanneer u de configuratie hebt voltooid, klikt u op **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>URL-route ring configureren voor toepassings gateways

Wanneer u een back-end-pool kiest, neemt een toepassings gateway die is geconfigureerd met een regel op basis van een pad patroon van de aanvraag-URL naast round robin-distributie. In dit scenario voegen we een regel op basis van een pad toe om elke URL met '/images/ \* ' om te leiden naar de Server groep voor installatie kopieën. Voor meer informatie over het configureren van op URL-pad gebaseerde route ring voor een toepassings gateway, raadpleegt u [een op pad gebaseerde regel voor een toepassings gateway maken](../application-gateway/create-url-route-portal.md).

![Diagram van de weblaag Application Gateway](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Ga vanuit de resource groep naar het exemplaar van de toepassings gateway dat u in de voor gaande sectie hebt gemaakt.
2. Selecteer **back-endservers** onder **instellingen** en selecteer **toevoegen** om de vm's toe te voegen die u wilt koppelen aan de back-endservers van de weblaag.
3. Voer de naam van de back-end-pool en alle IP-adressen in van de computers die zich in de groep bevinden. In dit scenario verbinden we twee back-end-server groepen van virtuele machines.

   ![Application Gateway back-end-pool toevoegen](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Onder **instellingen** van de toepassings gateway selecteert u **regels** en klikt u vervolgens op de knop **op basis van het pad** om een regel toe te voegen.

   ![Knop voor Application Gateway regels op basis van pad](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Configureer de regel door de volgende informatie op te geven.

   Basis instellingen:

   + **Naam**: de beschrijvende naam van de regel die toegankelijk is in de portal.
   + **Listener**: de listener die wordt gebruikt voor de regel.
   + **Standaard back-endserver**: de back-end-pool die moet worden gebruikt met de standaard regel.
   + **Standaard HTTP-instellingen**: de HTTP-instellingen die moeten worden gebruikt met de standaard regel.

   Op pad gebaseerde regels:

   + **Naam**: de beschrijvende naam van de regel op basis van het pad.
   + **Paden**: de padregel die wordt gebruikt voor het door sturen van verkeer.
   + **Back**-end-pool: de back-endgegevensbron die met deze regel moet worden gebruikt.
   + **Http-instelling**: de HTTP-instellingen die met deze regel moeten worden gebruikt.

   > [!IMPORTANT]
   > Paden: geldige paden moeten beginnen met "/". Het Joker teken \* is alleen toegestaan aan het einde. Geldige voor beelden zijn/XYZ,/XYZ \* of/xyz/ \* .

   ![Application Gateway Blade ' op pad gebaseerde regel toevoegen '](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Stap 3: toepassings gateways toevoegen aan de Traffic Manager-eind punten

In dit scenario is Traffic Manager verbonden met toepassings gateways (zoals geconfigureerd in de voor gaande stappen) die zich in verschillende regio's bevinden. Nu de toepassings gateways zijn geconfigureerd, is de volgende stap om deze te verbinden met uw Traffic Manager-profiel.

1. Open uw Traffic Manager-profiel. Als u dit wilt doen, kijkt u in uw resource groep of zoekt u naar de naam van het Traffic Manager profiel in **alle resources**.
2. Selecteer **eind punten** in het linkerdeel venster en klik vervolgens op **toevoegen** om een eind punt toe te voegen.

   ![Knop toevoegen Traffic Manager eind punten](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Een eind punt maken door de volgende gegevens in te voeren:

   * **Type**: Selecteer het type eind punt waarvoor u de taak verdeling wilt Toep assen. In dit scenario selecteert u **Azure-eind punt** omdat er verbinding wordt gemaakt met de instanties van de toepassings gateway die eerder zijn geconfigureerd.
   * **Naam**: Voer de naam van het eind punt in.
   * **Doel bron type**: Selecteer **openbaar IP-adres** en selecteer vervolgens onder **doel bron** de open bare IP-adressen van de toepassings gateway die eerder zijn geconfigureerd.

   ![Traffic Manager "eind punt toevoegen"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. U kunt nu uw installatie testen door deze te openen met de DNS van uw Traffic Manager-profiel (in dit voor beeld: TrafficManagerScenario.trafficmanager.net). U kunt aanvragen opnieuw verzenden, Vm's en webservers weer geven die in verschillende regio's zijn gemaakt en de Traffic Manager profiel instellingen wijzigen om uw installatie te testen.

### <a name="step-4-create-a-load-balancer"></a>Stap 4: een load balancer maken

In dit scenario distribueert Load Balancer verbindingen van de weblaag naar de data bases binnen een cluster met hoge Beschik baarheid.

Als uw database cluster met hoge Beschik baarheid gebruikmaakt van SQL Server AlwaysOn, raadpleegt u [een of meer listeners voor Always on-beschikbaarheids groep configureren](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) voor stapsgewijze instructies.

Zie [een interne Load Balancer in de Azure portal maken](../load-balancer/quickstart-load-balancer-standard-internal-portal.md)voor meer informatie over het configureren van een interne Load Balancer.

1. Klik in het linkerdeel venster van de Azure Portal op **een resource**  >  **netwerk**  >  **taak verdeler** maken.
2. Kies een naam voor de load balancer.
3. Stel het **type** in op **intern** en kies het juiste virtuele netwerk en subnet voor de Load Balancer zich in bevindt.
4. Onder **IP-adres toewijzing** selecteert u **dynamisch** of **statisch**.
5. Kies onder **resource groep** de resource groep voor de Load Balancer.
6. Kies onder **locatie** de juiste regio voor de Load Balancer.
7. Klik op **maken** om de Load Balancer te genereren.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Een back-end-database laag verbinden met de load balancer

1. Zoek in de resource groep de load balancer die in de vorige stappen is gemaakt.
2. Klik onder **instellingen** op **back-end-Pools** en klik vervolgens op **toevoegen** om een back-end-groep toe te voegen.

   ![Load Balancer back-end-pool toevoegen](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Voer de naam van de back-end-pool in.
4. Voeg afzonderlijke machines of beschik bare sets toe aan de back-end-pool.

#### <a name="configure-a-probe"></a>Een test configureren

1. Selecteer in uw load balancer onder **instellingen** de optie **testes en klik** vervolgens op **toevoegen** om een test toe te voegen.

   ![Load Balancer test toevoegen](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Voer de naam in voor de test.
3. Selecteer het **protocol** voor de test. Voor een Data Base wilt u mogelijk een TCP-test gebruiken in plaats van een HTTP-test. Raadpleeg voor meer informatie over de Load Balancer-tests [begrijpen Load Balancer probe](../load-balancer/load-balancer-custom-probe-overview.md).
4. Voer de **poort** in van de data base die moet worden gebruikt voor toegang tot de test.
5. Geef onder **interval** op hoe vaak u de toepassing wilt testen.
6. Onder **onjuiste drempel waarde**, geeft u het aantal voortdurende test fouten op dat moet worden uitgevoerd voor de back-end-VM als een slechte status.
7. Klik op **OK** om de test te maken.

#### <a name="configure-the-load-balancing-rules"></a>De regels voor taak verdeling configureren

1. Selecteer onder **instellingen** van de Load Balancer **taakverdelings regels** en klik vervolgens op **toevoegen** om een regel te maken.
2. Voer de **naam** in voor de taakverdelings regel.
3. Kies het **frontend-IP-adres** van de Load Balancer, het **protocol** en de **poort**.
4. Geef onder **backend-poort** de poort op die moet worden gebruikt in de back-end-pool.
5. Selecteer de **back-end-pool** en de **test** die in de vorige stappen is gemaakt om de regel toe te passen op.
6. Kies onder **sessie persistentie** hoe u de sessies wilt behouden.
7. Onder **time-out voor inactiviteit** geeft u het aantal minuten op voor een time-out voor inactiviteit.
8. Onder **zwevend IP** selecteert u **uitgeschakeld** of **ingeschakeld**.
9. Klik op **OK** om de regel te maken.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Stap 5: virtuele machines op de weblaag verbinden met de load balancer

Nu configureren we het IP-adres en de front-end-poort van de Load Balancer in de toepassingen die worden uitgevoerd op de virtuele machines op de weblaag voor alle database verbindingen. Deze configuratie is specifiek voor de toepassingen die op deze virtuele machines worden uitgevoerd. Raadpleeg de documentatie van de toepassing voor het configureren van het doel-IP-adres en de-poort. Als u het IP-adres van de front-end wilt vinden, gaat u in het Azure Portal naar de front-end-IP-groep op de **Load Balancer-instellingen**.

![Het navigatie deel venster voor de frontend-IP-groep Load Balancer](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Traffic Manager](traffic-manager-overview.md)
* [Overzicht van Application Gateway](../application-gateway/overview.md)
* [Overzicht van Azure Load Balancer](../load-balancer/load-balancer-overview.md)