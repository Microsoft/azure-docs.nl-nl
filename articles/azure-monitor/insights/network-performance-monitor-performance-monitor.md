---
title: Prestatie meter in Netwerkprestatiemeter
description: Met de functie prestatie meter in Netwerkprestatiemeter kunt u de netwerk connectiviteit op verschillende punten in uw netwerk bewaken. U kunt Cloud implementaties en on-premises locaties, meerdere data centers en filialen, en bedrijfskritische toepassingen voor multi-tier of micro services bewaken.
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 505584abbe573c94056888b07406437650b09611
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101728617"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Netwerkprestatiemeter oplossing: prestatie bewaking

> [!IMPORTANT]
> Vanaf 1 juli 2021 kunt u geen nieuwe tests toevoegen in een bestaande werk ruimte of een nieuwe werk ruimte inschakelen in Netwerkprestatiemeter. U kunt de tests die zijn gemaakt vóór 1 juli 2021 blijven gebruiken. Als u de service onderbreking voor uw huidige workloads wilt minimaliseren, [migreert u uw tests van Netwerkprestatiemeter naar de nieuwe verbindings monitor](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md) in azure Network Watcher vóór 29 februari 2024.

Met de functie prestatie meter in [Netwerkprestatiemeter](network-performance-monitor.md) kunt u de netwerk connectiviteit op verschillende punten in uw netwerk bewaken. U kunt Cloud implementaties en on-premises locaties, meerdere data centers en filialen, en bedrijfskritische toepassingen voor multi-tier of micro services bewaken. Met prestatie meter kunt u netwerk problemen detecteren voordat uw gebruikers klagen. De belangrijkste voor delen zijn: 

- Bewaak verlies en latentie in verschillende subnetten en stel waarschuwingen in.
- Bewaak alle paden (met inbegrip van redundante paden) in het netwerk.
- Problemen met tijdelijke en punt-in-time-netwerk problemen oplossen die moeilijk te repliceren zijn.
- Bepaal het specifieke segment in het netwerk, dat verantwoordelijk is voor verminderde prestaties.
- Bewaak de status van het netwerk, zonder dat SNMP nodig is.


![Netwerkprestatiemeter](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Configuratie
Als u de configuratie voor Netwerkprestatiemeter wilt openen, opent u de [Netwerkprestatiemeter-oplossing](network-performance-monitor.md)en selecteert **u configureren**.

![Netwerkprestatiemeter configureren](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Nieuwe netwerken maken

Een netwerk in Netwerkprestatiemeter is een logische container voor subnetten. Zo kunt u de bewaking van uw netwerk infrastructuur organiseren volgens uw behoeften. U kunt een netwerk met een beschrijvende naam maken en hieraan subnetten toevoegen volgens uw bedrijfs logica. U kunt bijvoorbeeld een netwerk met de naam Londen maken en alle subnetten toevoegen in uw Londen Data Center. U kunt ook een netwerk met de naam *ContosoFrontEnd* maken en dit netwerk toevoegen aan alle subnetten met de naam contoso die de front-end van uw app bedienen. De oplossing maakt automatisch een standaard netwerk dat alle subnetten bevat die in uw omgeving zijn gedetecteerd. 

Wanneer u een netwerk maakt, voegt u hieraan een subnet toe. Vervolgens wordt dat subnet verwijderd uit het standaard netwerk. Als u een netwerk verwijdert, worden alle subnetten automatisch teruggestuurd naar het standaard netwerk. Het standaard netwerk fungeert als een container voor alle subnetten die geen deel uitmaken van een door de gebruiker gedefinieerd netwerk. U kunt het standaard netwerk niet bewerken of verwijderen. Het blijft altijd in het systeem. U kunt zoveel aangepaste netwerken maken als u nodig hebt. In de meeste gevallen worden de subnetten in uw organisatie gerangschikt in meer dan één netwerk. Maak een of meer netwerken om uw subnetten te groeperen voor uw bedrijfs logica.

Een nieuw netwerk maken:


1. Selecteer het tabblad **netwerken** .
1. Selecteer **netwerk toevoegen** en voer vervolgens de naam en beschrijving van het netwerk in. 
2. Selecteer een of meer subnetten en selecteer vervolgens **toevoegen**. 
3. Selecteer **Opslaan** om de configuratie op te slaan. 


### <a name="create-monitoring-rules"></a>Bewakings regels maken 

Prestatie meter genereert status gebeurtenissen wanneer de drempel waarde van de prestaties van netwerk verbindingen tussen twee subnetwerken of tussen twee netwerken wordt geschonden. Het systeem kan deze drempel waarden automatisch achterhalen. U kunt ook aangepaste drempel waarden opgeven. Het systeem maakt automatisch een standaard regel, waardoor een status gebeurtenis wordt gegenereerd als het verlies of de latentie tussen een wille keurig paar netwerk-of subnetwerk koppelingen overtreedt op de drempel waarde die door het systeem is geleerd. Dit proces helpt de oplossing uw netwerk infrastructuur te controleren totdat u expliciet geen bewakings regels hebt gemaakt. Als de standaard regel is ingeschakeld, sturen alle knoop punten synthetische trans acties naar alle andere knoop punten die u hebt ingeschakeld voor bewaking. De standaard regel is handig voor kleine netwerken. Een voor beeld is een scenario waarin u een klein aantal servers hebt waarop een micro service wordt uitgevoerd en u ervoor wilt zorgen dat alle servers verbinding met elkaar hebben.

>[!NOTE]
> We raden u aan de standaard regel uit te scha kelen en aangepaste bewakings regels te maken, met name voor grote netwerken waarin u een groot aantal knoop punten gebruikt voor bewaking. Aangepaste bewakings regels kunnen het verkeer dat door de oplossing wordt gegenereerd verminderen en helpt u bij het organiseren van de bewaking van uw netwerk.

Maak bewakings regels volgens uw bedrijfs logica. Een voor beeld hiervan is als u de prestaties van de netwerk verbinding van twee Office-sites op hoofd kantoor wilt bewaken. Groepeer alle subnetten in Office site1 in netwerk O1. Groepeer vervolgens alle subnetten in Office Site2 in netwerk O2. Groepeer tot slot alle subnetten in het hoofd kantoor in netwerk H. twee controle regels maken: een tussen O1 en H en de andere tussen O2 en H. 

Aangepaste bewakings regels maken:

1. Selecteer **regel toevoegen** op het tabblad **monitor** en voer de naam en beschrijving van de regel in.
2. Selecteer het paar netwerk-of subnetwerk koppelingen dat u vanuit de lijsten wilt bewaken. 
3. Selecteer in de vervolg keuzelijst netwerk het netwerk dat de gewenste subnetwerken bevat. Selecteer vervolgens de subnetwerken in de vervolg keuzelijst met de bijbehorende subnetwerk. Als u alle subnetwerken in een netwerk koppeling wilt bewaken, selecteert u **alle subnetwerken**. Selecteer op dezelfde manier de andere subnetwerken die u wilt. Selecteer **uitzonde ring toevoegen** om bewaking uit te sluiten voor bepaalde subnetwerk koppelingen van de selecties die u hebt gemaakt. 
4. Kies tussen ICMP-en TCP-protocollen om synthetische trans acties uit te voeren. 
5. Als u geen status gebeurtenissen wilt maken voor de items die u hebt geselecteerd, schakelt u **status controle inschakelen in op de koppelingen waarop deze regel van toepassing** is. 
6. Kies bewakings voorwaarden. Als u aangepaste drempels wilt instellen voor het genereren van status gebeurtenissen, voert u drempel waarden in. Wanneer de waarde van de voor waarde de geselecteerde drempel overschrijdt voor het geselecteerde netwerk of subnetwerk, wordt een status gebeurtenis gegenereerd. 
7. Selecteer **Opslaan** om de configuratie op te slaan. 

Nadat u een bewakings regel hebt opgeslagen, kunt u deze regel met Waarschuwingenbeheer integreren door **waarschuwing maken** te selecteren. Er wordt automatisch een waarschuwings regel gemaakt met de zoek query. Andere vereiste para meters worden automatisch ingevuld. U kunt met behulp van een waarschuwings regel e-mail waarschuwingen ontvangen, naast de bestaande waarschuwingen in Netwerkprestatiemeter. Waarschuwingen kunnen ook herstel bewerkingen activeren met runbooks, of ze kunnen integreren met bestaande Service beheer oplossingen met behulp van webhooks. Selecteer **waarschuwing beheren** om de instellingen van de waarschuwing te bewerken. 

U kunt nu meer regels voor prestatie controle maken of naar het dash board van de oplossing gaan om de mogelijkheid te gebruiken.

### <a name="choose-the-protocol"></a>Het protocol kiezen

Netwerkprestatiemeter maakt gebruik van synthetische trans acties voor het berekenen van metrische gegevens over netwerk prestaties, zoals pakket verlies en koppelings latentie. Voor een beter begrip van dit concept moet u een Netwerkprestatiemeter agent die is verbonden met één uiteinde van een netwerk koppeling. Deze Netwerkprestatiemeter-agent stuurt test pakketten naar een tweede Netwerkprestatiemeter agent die is verbonden met een ander eind van het netwerk. De tweede agent antwoordt op antwoord pakketten. Dit proces wordt een paar keer herhaald. Door het aantal antwoorden te meten en de tijd die nodig is om elk antwoord te ontvangen, worden de eerste Netwerkprestatiemeter agent de verbindings latentie en pakket drup pels geëvalueerd. 

De notatie, grootte en volg orde van deze pakketten wordt bepaald door het protocol dat u kiest wanneer u bewakings regels maakt. Op basis van het Protocol van de pakketten kan de tussenliggende netwerk apparaten, zoals routers en switches, deze pakketten anders verwerken. Daarom is uw protocol keuze van invloed op de nauw keurigheid van de resultaten. Uw protocol keuze bepaalt ook of u hand matige stappen moet uitvoeren nadat u de Netwerkprestatiemeter oplossing hebt geïmplementeerd. 

Netwerkprestatiemeter biedt u de keuze tussen ICMP-en TCP-protocollen voor het uitvoeren van synthetische trans acties. Als u ICMP kiest wanneer u een synthetische transactie regel maakt, gebruiken de Netwerkprestatiemeter-agents ICMP-ECHO berichten om de netwerk latentie en pakket verlies te berekenen. ICMP-ECHO gebruikt hetzelfde bericht dat door het conventionele ping-hulp programma wordt verzonden. Wanneer u TCP als protocol gebruikt, verzenden Netwerkprestatiemeter agents TCP SYN-pakketten via het netwerk. Deze stap wordt gevolgd door de voltooiing van een TCP-handshake en de verbinding wordt verwijderd met behulp van eerste pakketten. 

Houd rekening met de volgende informatie voordat u een protocol kiest: 

* **Detectie van meerdere netwerk routes.** TCP is nauw keuriger bij het detecteren van meerdere routes en er zijn minder agents in elk subnet nodig. Een of twee agents die gebruikmaken van TCP, kunnen bijvoorbeeld alle redundante paden tussen subnetten detecteren. U hebt verschillende agents nodig die ICMP gebruiken om Vergelijk bare resultaten te krijgen. Als u met behulp van ICMP een aantal routes tussen twee subnetten hebt, hebt u meer dan 5N agents nodig in een bron-of doel-subnet.

* **Nauw keurigheid van de resultaten.** Routers en switches wijzen vaak een lagere prioriteit toe aan ICMP-ECHO pakketten vergeleken met TCP-pakketten. In bepaalde situaties, wanneer netwerk apparaten zwaar worden belast, zijn de gegevens die worden verkregen door de TCP nauw keuriger, het verlies en de latentie van toepassingen. Dit probleem treedt op omdat het meeste toepassings verkeer via TCP verloopt. In dergelijke gevallen levert ICMP minder nauw keurige resultaten ten opzichte van TCP. 

* **Firewall configuratie.** TCP-protocol vereist dat TCP-pakketten worden verzonden naar een doel poort. De standaard poort die wordt gebruikt door Netwerkprestatiemeter agents is 8084. U kunt de poort wijzigen wanneer u agents configureert. Zorg ervoor dat uw netwerk firewalls of NSG-regels (netwerk beveiligings groep) verkeer op de poort toestaan. U moet er ook voor zorgen dat de lokale firewall op de computers waarop agents zijn geïnstalleerd, zodanig is geconfigureerd dat verkeer wordt toegestaan op deze poort. U kunt Power shell-scripts gebruiken om firewall regels te configureren op uw computers met Windows, maar u moet de netwerk firewall hand matig configureren. Daarentegen werkt ICMP niet met behulp van een poort. In de meeste bedrijfs scenario's wordt ICMP-verkeer via de firewalls toegestaan om u in staat te stellen diagnostische hulpprogram ma's voor het netwerk, zoals het hulp programma Ping, te gebruiken. Als u een computer van een andere machine kunt pingen, kunt u het ICMP-protocol gebruiken zonder dat u firewalls hand matig hoeft te configureren.

>[!NOTE] 
> Sommige firewalls blok keren mogelijk ICMP, wat kan leiden tot opnieuw verzenden als gevolg van een groot aantal gebeurtenissen in uw beveiligings gegevens en het beheer systeem voor gebeurtenissen. Zorg ervoor dat het door u gekozen protocol niet wordt geblokkeerd door een netwerk firewall of NSG. Als dat niet het geval is, kan Netwerkprestatiemeter het netwerk segment niet bewaken. U wordt aangeraden TCP te gebruiken voor bewaking. Gebruik ICMP in scenario's waarin u TCP niet kunt gebruiken, zoals: 
>
> - U gebruikt Windows-knoop punten op basis van een client, omdat TCP raw-sockets niet zijn toegestaan in Windows-clients.
> - De netwerk firewall of NSG blokkeert TCP.
> - U weet niet hoe u het protocol kunt wijzigen.

Als u ervoor kiest om ICMP te gebruiken tijdens de implementatie, kunt u op elk gewenst moment overstappen op TCP door de standaard regel voor bewaking te bewerken.

1. Ga naar de **netwerk prestatie**   >  **meter**   >  ****   >  **monitor** configureren. Selecteer vervolgens **standaard regel**. 
2. Ga naar de sectie **protocol** en selecteer het protocol dat u wilt gebruiken. 
3. Selecteer **Opslaan** om de instelling toe te passen. 

Zelfs als de standaard regel een specifiek protocol gebruikt, kunt u nieuwe regels maken met een ander protocol. U kunt zelfs een combi natie van regels maken waarbij sommige regels ICMP gebruiken en anderen TCP gebruiken. 

## <a name="walkthrough"></a>Walkthrough 

Bekijk nu een eenvoudig onderzoek naar de hoofd oorzaak van een status gebeurtenis.

In het dash board van de oplossing ziet u een status gebeurtenis die aangeeft dat een netwerk koppeling slecht is. Als u het probleem wilt onderzoeken, selecteert u de tegel **netwerk koppelingen die worden bewaakt** .

Op de pagina inzoomen ziet u dat de netwerk koppeling van de **DMZ2-DMZ1** slecht is. Selecteer **subnet-koppelingen weer geven** voor deze netwerk koppeling. 


Op de pagina inzoomen worden alle subnetwerk koppelingen weer gegeven in de **DMZ2-DMZ1-** netwerk koppeling. Voor beide koppelingen van het subnetwerk heeft de latentie de drempel waarde overschreden, waardoor de netwerk koppeling niet meer in orde is. U kunt ook de latentie trends van beide koppelingen van het subnetwerk bekijken. Gebruik het besturings element tijd selectie in de grafiek om de focus te richten op het vereiste tijds bereik. U kunt het tijdstip van de dag bekijken waarop de latentie de piek bereikt. Zoek op een later tijdstip de logboeken voor deze periode om het probleem te onderzoeken. Selecteer **knooppunt koppelingen weer geven** om verder in te zoomen. 
 
 ![Pagina met koppelingen naar het subnetwerk](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Net als bij de vorige pagina bevat de inzoom pagina voor de desbetreffende subnetwerk koppeling de samenstellende knooppunt koppelingen. U kunt hier soort gelijke acties uitvoeren zoals u in de vorige stap hebt gedaan. Selecteer **topologie weer geven** om de topologie tussen de twee knoop punten weer te geven. 
 
 ![Pagina knooppunt koppelingen](media/network-performance-monitor-performance-monitor/node-links.png) 

Alle paden tussen de twee geselecteerde knoop punten worden uitgezet in de topologie kaart. U kunt de topologie van de hop-by-hop van routes tussen twee knoop punten op de topologie kaart visualiseren. Hiermee krijgt u een duidelijk beeld van het aantal routes tussen de twee knoop punten en de paden waarmee de gegevens pakketten worden uitgevoerd. Knel punten in netwerk prestaties worden rood weer gegeven. Als u een defecte netwerk verbinding of een defect netwerk apparaat wilt zoeken, kijkt u naar de rode elementen op de topologie kaart. 

 ![Topologie dashboard met topologie overzicht](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

U kunt het verlies, de latentie en het aantal hops in elk pad in het deel venster **actie** bekijken. Gebruik de schuif balk om de details van de beschadigde paden weer te geven. Gebruik de filters om de paden met de onjuiste hop te selecteren, zodat de topologie voor alleen de geselecteerde paden wordt uitgezet. Gebruik uw muis wiel om in of uit te zoomen op de topologie kaart. 

In de volgende afbeelding wordt de hoofd oorzaak van de probleem gebieden naar de specifieke sectie van het netwerk weer gegeven in de rode paden en hops. Selecteer een knoop punt in de topologie kaart om de eigenschappen van het knoop punt weer te geven, inclusief de FQDN en het IP-adres. Als u een hop selecteert, wordt het IP-adres van de hop weer gegeven. 
 
![Topologie toewijzing met geselecteerde knooppunt eigenschappen](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Volgende stappen
[Zoek logboeken](../logs/log-query-overview.md) om gedetailleerde gegevens records voor netwerk prestaties weer te geven.