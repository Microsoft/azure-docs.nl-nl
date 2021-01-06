---
title: Problemen met de beschik baarheid van Azure Load Balancer resource-, front-end-en back-endservers oplossen
description: Gebruik de beschik bare metrische gegevens om uw gedegradeerde of niet-beschik bare Azure-Standard Load Balancer te diagnosticeren.
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2020
ms.author: errobin
ms.openlocfilehash: 3acaaba86c9a546a0bd45b5386287908168d50d0
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955617"
---
# <a name="troubleshoot-resource-health-and-inbound-availability-issues"></a>Problemen met de resource status en inkomende Beschik baarheid oplossen 

Dit artikel is een hand leiding voor het onderzoeken van problemen die van invloed zijn op de beschik baarheid van uw load balancer frontend-IP-en back-endservers. 

De Resource Health Check (RHC) voor de Load Balancer wordt gebruikt om de status van uw load balancer te bepalen. Hiermee wordt de metrische gegevens van het gegevenspad geanalyseerd gedurende een interval van **twee minuten** om te bepalen of de taakverdelings eindpunten, de front-end-IP-en frontend-poorten combi Naties met taakverdelings regels, beschikbaar zijn.

In de onderstaande tabel wordt de RHC-logica beschreven die wordt gebruikt om de status van uw load balancer te bepalen.

| Status van resource status | Beschrijving |
| --- | --- |
| Beschikbaar | Uw standaard load balancer resource is in orde en beschikbaar. |
| Verminderd beschikbaar | Uw standaard load balancer heeft platform of door de gebruiker gestarte gebeurtenissen die invloed hebben op de prestaties. De metriek voor het gegevenspad heeft een beschikbaarheid van minder dan 90% en meer dan 25% gerapporteerd gedurende ten minste twee minuten. U ondervindt aanzienlijke invloed op de prestaties. 
| Niet beschikbaar | Uw standaard load balancer resource is niet in orde. De metriek voor DataPath-Beschik baarheid heeft minder dan 25% status gerapporteerd voor ten minste twee minuten. U ondervindt aanzienlijke gevolgen voor de prestaties of gebrek aan Beschik baarheid voor binnenkomende verbindingen. Er zijn mogelijk gebruikers-of platform gebeurtenissen waardoor er geen Beschik baarheid wordt veroorzaakt. |
| Onbekend | De resource status voor uw standaard load balancer resource is nog niet bijgewerkt of heeft niet de beschikbaarheids gegevens van het gegevenspad ontvangen voor de afgelopen 10 minuten. Dit hoort slechts tijdelijk het geval te zijn. De juiste status wordt weergegeven zodra er gegevens worden ontvangen. |


## <a name="about-the-metrics-well-use"></a>Over de metrische gegevens die we gebruiken
De twee metrische gegevens die moeten worden gebruikt, zijn *gegevenspad Beschik baarheid* en status van *Health probe* . het is belang rijk om te begrijpen wat hun betekenis is om de juiste inzichten te verkrijgen. 

## <a name="data-path-availability"></a>Gegevenspadbeschikbaarheid
De metrische gegevens voor de beschik baarheid van het gegevenspad worden elke 25 seconden met een TCP-ping gegenereerd op alle frontend-poorten waarvoor taak verdeling en binnenkomende NAT-regels zijn geconfigureerd. Deze TCP-ping wordt vervolgens doorgestuurd naar een van de in orde zijnde back-end-exemplaren. Als de service een reactie op de ping ontvangt, wordt deze als geslaagd beschouwd en wordt de som van de metriek één keer herhaald, als dat niet het geval is. Het aantal van deze metrieke waarde is 1/100 van het totale aantal TCP-pings per sample periode. Daarom willen we het gemiddelde overwegen, waarin het gemiddelde van de som/het aantal voor de tijds periode wordt weer gegeven. De metrische gegevens over de beschik baarheid van het gegevenspad worden gemiddeld berekend, waardoor het percentage geslaagde pogingen voor TCP-Pings op uw frontend-IP-adres wordt aangegeven: poort voor elk van de taak verdeling en binnenkomende NAT-regels.

## <a name="health-probe-status"></a>Status van statustest
De metrische waarde van de status test wordt gegenereerd door een ping van het protocol dat is gedefinieerd in de status test. Deze ping wordt verzonden naar elk exemplaar van de back-endserver en op de poort die is gedefinieerd in de status test. Voor HTTP-en HTTPS-tests vereist een geslaagde ping een HTTP 200 OK-antwoord, terwijl een antwoord op de TCP-test wordt uitgevoerd. De opeenvolgende successen of fouten van elke sonde bepalen vervolgens of een back-end-exemplaar in orde is en dat er verkeer kan worden ontvangen voor de taakverdelings regels waaraan de back-end-pool is toegewezen. Net als bij de beschik baarheid van gegevenspaden gebruiken we de gemiddelde aggregatie, waarmee we tijdens het steekproef interval de gemiddelde geslaagde/totale pings vertelt. Met deze status waarde wordt de backend-status in isolatie van uw load balancer aangegeven door uw back-end-exemplaren te zoeken zonder verkeer via het front-end te verzenden.

>[!IMPORTANT]
>Health probe status wordt op één minuut verzameld. Dit kan leiden tot kleine schommelingen in een andere constante waarde. Als er bijvoorbeeld twee back-end-instanties zijn, één wordt getest en er één wordt ingedrukt, kan de Health probe-service 7 voor beelden vastleggen voor de gezonde instantie en 6 voor het beschadigde exemplaar. Dit leidt tot een eerder geconstante waarde van 50 die wordt weer gegeven als 46,15 gedurende een interval van één minuut. 

## <a name="diagnose-degraded-and-unavailable-load-balancers"></a>Problemen met gedegradeerde en niet-beschik bare load balancers vaststellen
Zoals beschreven in het artikel over de [resource status](load-balancer-standard-diagnostics.md#resource-health-status), is er een gedegradeerde Load Balancer die wordt weer gegeven tussen 25% en 90% Beschik baarheid van gegevenspaden en een niet-beschik bare Load Balancer met een Beschik baarheid van minder dan 25% gegevenspaden, gedurende een periode van twee minuten. U kunt dezelfde stappen uitvoeren om het probleem te onderzoeken dat wordt weer gegeven in de status van alle Beschik baarheid van een Health probie of voor gegevens paden die u hebt geconfigureerd. We gaan de zaak ontdekken waar we onze resource status hebben gecontroleerd en dat onze load balancer niet meer beschikbaar zijn met de beschik baarheid van een gegevenspad van 0%. onze service is niet actief.

Eerst gaan we naar de weer gave gedetailleerde metrische gegevens van onze load balancer Insights-Blade. U kunt dit doen via de Blade load balancer resource of de koppeling in het status bericht van uw resource.  Vervolgens gaat u naar het tabblad Beschik baarheid front-end en back-end en controleert u een periode van dertig minuten van de tijd waarin de status van de gedegradeerde of niet-beschik bare toestand is opgetreden. Als we zien dat de beschik baarheid van het gegevenspad 0% is, weten we dat er een probleem is met het voor komen van verkeer voor al onze taak verdeling en binnenkomende NAT-regels. Dit kan zien hoe lang deze impact heeft gehad. 

De volgende plaats die we nodig hebben om te bepalen of het gegevenspad niet beschikbaar is, is de metrische gegevens over Health Probe, omdat we geen gezonde back-end-instanties hebben die verkeer niet kunnen verwerken. Als er ten minste één gezond back-end-exemplaar is voor al onze taak verdeling en binnenkomende regels, weten we dat het niet mogelijk is om de gegevens paden niet beschikbaar te maken. In dit scenario wordt aangegeven dat er een probleem is met het Azure-platform, maar niet fret als u deze als geautomatiseerde waarschuwing ontvangt, wordt naar ons team verzonden om snel alle platform problemen op te lossen.

## <a name="diagnose-health-probe-failures"></a>Problemen met status testen vaststellen
Stel dat we de status van de status testen controleren en erachter komen dat alle exemplaren worden weer gegeven als beschadigd. Met deze zoek informatie wordt uitgelegd waarom het pad van de gegevens niet beschikbaar is omdat het verkeer nergens gaat. Vervolgens moet u de volgende controle lijst door lopen om veelvoorkomende configuratie fouten uit te stellen:
* Controleer het CPU-gebruik voor uw resources om te controleren of uw instanties in orde zijn
  * U kunt dit controleren 
* Als u een HTTP-of HTTPS-test gebruikt, moet u controleren of de toepassing in orde is en reageert.
  * Valideer deze functie door rechtstreeks toegang te krijgen tot de toepassingen via het privé-IP-adres of het open bare IP-adres op exemplaar niveau dat is gekoppeld aan uw back-end-exemplaar
* Controleer de netwerk beveiligings groepen die zijn toegepast op onze back-endservers. Zorg ervoor dat er geen regels met een hogere prioriteit zijn dan AllowAzureLoadBalancerInBound die de status test blok keren
  * U kunt dit doen door de Blade netwerk van uw back-end-Vm's of Virtual Machine Scale Sets te bezoeken
  * Als dit NSG probleem het geval is, verplaatst u de bestaande regel voor toestaan of maakt u een nieuwe regel met hoge prioriteit om AzureLoadBalancer-verkeer toe te staan
* Controleer uw besturings systeem. Zorg ervoor dat uw virtuele machines Luis teren op de test poort en controleer de firewall regels van het besturings systeem om ervoor te zorgen dat het test verkeer dat afkomstig is van IP-adres 168.63.129.16 niet wordt geblokkeerd.
  * U kunt de luisterende poorten controleren door netstat-a de Windows-opdracht prompt of netstat-l in een Linux-Terminal uit te voeren
* Plaats geen firewall NVA VM in de back-endadresgroep van de load balancer, gebruik door de [gebruiker gedefinieerde routes](../virtual-network/virtual-networks-udr-overview.md#user-defined) om verkeer door te sturen naar back-end-instanties via de firewall
* Zorg ervoor dat u het juiste protocol gebruikt, als u HTTP gebruikt voor het testen van een poort luistert voor een niet-HTTP-toepassing, mislukt de test

Als u deze controle lijst hebt door lopen en nog steeds problemen met de status test hebt gevonden, kunnen er zeldzame platform problemen optreden die van invloed zijn op de test service voor uw instanties. In dit geval heeft Azure uw vorige en er wordt een automatische waarschuwing naar ons team verzonden om alle platform problemen snel op te lossen.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de Azure Load Balancer Health probe](load-balancer-custom-probe-overview.md)
* [Meer informatie over Azure Load Balancer metrische gegevens](load-balancer-standard-diagnostics.md)
