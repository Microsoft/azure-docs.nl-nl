---
title: Bewaking van Azure Traffic Manager Endpoint | Microsoft Docs
description: Dit artikel bevat informatie over de manier waarop Traffic Manager eindpunt controle en automatische endpoint failover gebruikt om Azure-klanten te helpen bij het implementeren van toepassingen met hoge Beschik baarheid
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: duau
ms.openlocfilehash: 31048a0abd939c81b64e87b4a146ae3b6934803f
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183906"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Eindpuntbewaking in Traffic Manager

Azure Traffic Manager omvat ingebouwde eindpunt bewaking en automatische endpoint failover. Deze functie helpt u bij het leveren van toepassingen met hoge Beschik baarheid die robuust zijn voor een eindpunt fout, waaronder problemen met Azure-regio's.

## <a name="configure-endpoint-monitoring"></a>Eindpunt bewaking configureren

Als u eindpunt bewaking wilt configureren, moet u de volgende instellingen opgeven voor uw Traffic Manager profiel:

* **Protocol**. Kies HTTP, HTTPS of TCP als protocol dat Traffic Manager gebruikt wanneer u het eind punt controleert om de status ervan te controleren. HTTPS-controle verifieert niet of uw TLS/SSL-certificaat geldig is. er wordt alleen gecontroleerd of het certificaat aanwezig is.
* **Poort**. Kies de poort die voor de aanvraag wordt gebruikt.
* **Pad**. Deze configuratie-instelling is alleen geldig voor de HTTP-en HTTPS-protocollen waarvoor de instelling pad is vereist. Als u deze instelling voor het TCP-bewakings protocol opgeeft, resulteert dit in een fout. Voor HTTP-en HTTPS-protocol geeft u het relatieve pad en de naam van de webpagina of het bestand waartoe de controle toegang heeft. Een slash (/) is een geldige vermelding voor het relatieve pad. Deze waarde impliceert dat het bestand zich in de hoofdmap (standaard) bevindt.
* **Instellingen voor aangepaste header** Deze configuratie-instelling helpt u bij het toevoegen van specifieke HTTP-headers aan de status controles die Traffic Manager naar eind punten in een profiel verzendt. De aangepaste kopteksten kunnen op een profiel niveau worden opgegeven dat van toepassing is voor alle eind punten in dat profiel en/of op een eindpunt niveau dat alleen van toepassing is op dat eind punt. U kunt aangepaste headers gebruiken voor het uitvoeren van status controles op eind punten in een omgeving met meerdere tenants naar de juiste bestemming door een host-header op te geven. U kunt deze instelling ook gebruiken door unieke headers toe te voegen die kunnen worden gebruikt om Traffic Manager afkomstig van HTTP (S)-aanvragen te identificeren en ze op een andere manier te verwerken. U kunt Maxi maal acht kopteksten opgeven: waardeparen seprated door een komma. Bijvoorbeeld "Header1: waarde1, header2: waarde2". 
* **Verwachte status code-bereiken** Met deze instelling kunt u meerdere succes waarden opgeven in de notatie 200-299, 301-301. Als deze status codes worden ontvangen als reactie van een eind punt wanneer een status controle wordt gestart, markeert Traffic Manager deze eind punten als in orde. U kunt Maxi maal acht status bereik waarden opgeven. Deze instelling is alleen van toepassing op het HTTP-en HTTPS-protocol en op alle eind punten. Deze instelling bevindt zich op het niveau van Traffic Manager profiel en de waarde 200 wordt standaard gedefinieerd als de status code geslaagd.
* **Interval voor zoeken**. Met deze waarde wordt aangegeven hoe vaak een eind punt wordt gecontroleerd op de status van een Traffic Manager-Bing-agent. U kunt hier twee waarden opgeven: 30 seconden (normale Zoek tijd) en 10 seconden (snel zoeken). Als er geen waarden worden gegeven, wordt het profiel ingesteld op een standaard waarde van 30 seconden. Ga naar de pagina met [prijzen voor Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) voor meer informatie over de prijzen voor snel zoeken.
* **Toegestaan aantal fouten**. Met deze waarde wordt het aantal mislukte pogingen aangegeven dat een Traffic Manager gezochte agent is toegestaan voordat het eind punt wordt gemarkeerd als beschadigd. De waarde kan variëren tussen 0 en 9. Een waarde van 0 betekent dat een enkele bewakings fout ervoor kan zorgen dat het eind punt wordt gemarkeerd als beschadigd. Als er geen waarde is opgegeven, wordt de standaard waarde 3 gebruikt.
* **Time-out voor testen**. Met deze eigenschap geeft u de hoeveelheid tijd op die de agent voor de Traffic Manager-Bing moet wachten voordat wordt overwogen dat er een fout optreedt wanneer een status controle test naar het eind punt wordt verzonden. Als het probing-interval is ingesteld op 30 seconden, kunt u de time-outwaarde tussen 5 en 10 seconden instellen. Als er geen waarde is opgegeven, wordt de standaard waarde van 10 seconden gebruikt. Als het interval voor zoeken is ingesteld op 10 seconden, kunt u de time-outwaarde tussen 5 en 9 seconden instellen. Als er geen time-outwaarde is opgegeven, wordt de standaard waarde van 9 seconden gebruikt.

    ![Eindpuntbewaking in Traffic Manager](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Afbeelding: eindpunt bewaking Traffic Manager**

## <a name="how-endpoint-monitoring-works"></a>Hoe eindpunt controle werkt

Als het bewakings protocol is ingesteld als HTTP of HTTPS, wordt door de agent van Traffic Manager zoeken een GET-aanvraag naar het eind punt gemaakt met behulp van het Protocol, de poort en het relatieve pad dat is opgegeven. Als er een 200-OK-antwoord wordt teruggestuurd of een van de antwoorden die zijn geconfigureerd in de **verwachte status codes \***, wordt dat eind punt als gezond beschouwd. Als het antwoord een andere waarde is, of als er geen reactie wordt ontvangen binnen de opgegeven time-outperiode, wordt de Traffic Manager hand leiding van de agent opnieuw geprobeerd, afhankelijk van de instelling van het getolerantiete aantal fouten (er worden geen nieuwe pogingen gedaan als deze instelling 0 is). Als het aantal opeenvolgende fouten hoger is dan het aantal ingestelde fouten, wordt het eind punt als beschadigd gemarkeerd. 

Als het bewakings protocol TCP is, initieert de Traffic Manager zoeken-agent een TCP-verbindings aanvraag met behulp van de opgegeven poort. Als het eind punt reageert op de aanvraag met een reactie op het tot stand brengen van de verbinding, wordt die status controle gemarkeerd als geslaagd en wordt de TCP-verbinding door de agent van de Traffic Manager gezocht. Als het antwoord een andere waarde is, of als er geen reactie wordt ontvangen binnen de opgegeven time-outperiode, wordt de Traffic Manager hand leiding van de agent opnieuw geprobeerd, afhankelijk van het aantal ingestelde fouten (er worden geen nieuwe pogingen gedaan als deze instelling 0 is). Als het aantal opeenvolgende fouten hoger is dan het aantal ingestelde fouten, is dat eind punt gemarkeerd als beschadigd.

In alle gevallen worden Traffic Manager tests vanaf meerdere locaties en wordt de opeenvolgende fout bepaling binnen elke regio uitgevoerd. Dit betekent ook dat eind punten status controles ontvangen van Traffic Manager met een hogere frequentie dan de instelling die wordt gebruikt voor het interval voor zoeken.

>[!NOTE]
>Voor HTTP-of HTTPS-bewakings protocollen is een veelvoorkomende procedure aan de kant van het eind punt een aangepaste pagina in uw toepassing te implementeren, bijvoorbeeld/Health.aspx. Met dit pad voor bewaking kunt u toepassingsspecifieke controles uitvoeren, zoals het controleren van prestatie meter items of het controleren van de beschik baarheid van de data base. Op basis van deze aangepaste controles retourneert de pagina een juiste HTTP-status code.

Alle eind punten in een Traffic Manager profiel instellingen voor het delen van profielen. Als u verschillende bewakings instellingen voor verschillende eind punten wilt gebruiken, kunt u [geneste Traffic Manager profielen](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings)maken.

## <a name="endpoint-and-profile-status"></a>Status van eind punt en profiel

U kunt Traffic Manager profielen en eind punten in-en uitschakelen. Een wijziging in de eindpunt status kan echter ook optreden als gevolg van Traffic Manager geautomatiseerde instellingen en processen.

### <a name="endpoint-status"></a>Eindpunt status

U kunt een specifiek eind punt in-of uitschakelen. De onderliggende service, die mogelijk nog steeds in orde is, wordt niet beïnvloed. Het wijzigen van de eindpunt status bepaalt de beschik baarheid van het eind punt in het Traffic Manager profiel. Wanneer de status van een eind punt is uitgeschakeld, controleert Traffic Manager de status niet en wordt het eind punt niet opgenomen in een DNS-antwoord.

### <a name="profile-status"></a>Profile status

Met de instelling profiel status kunt u een specifiek profiel in-of uitschakelen. Hoewel de eindpunt status van invloed is op één eind punt, is de profiel status van invloed op het hele profiel, inclusief alle eind punten. Wanneer u een profiel uitschakelt, worden de eind punten niet gecontroleerd op status en worden er geen eind punten opgenomen in een DNS-antwoord. Er wordt een [NXDOMAIN](https://tools.ietf.org/html/rfc2308) -respons code geretourneerd voor de DNS-query.

### <a name="endpoint-monitor-status"></a>Status van eindpunt monitor

Status van eindpunt monitor is een door Traffic Manager gegenereerde waarde waarmee de status van het eind punt wordt weer gegeven. U kunt deze instelling niet hand matig wijzigen. De eindpunt Monitor status is een combi natie van de resultaten van de eindpunt bewaking en de geconfigureerde eindpunt status. De mogelijke waarden van de status van de eindpunt monitor worden weer gegeven in de volgende tabel:

| Profile status | Eindpunt status | Status van eindpunt monitor | Notities |
| --- | --- | --- | --- |
| Uitgeschakeld |Ingeschakeld |Niet-actief |Het profiel is uitgeschakeld. Hoewel de status van het eind punt is ingeschakeld, heeft de profiel status (uitgeschakeld) prioriteit. Eind punten in uitgeschakelde profielen worden niet bewaakt. Er wordt een NXDOMAIN-respons code geretourneerd voor de DNS-query. |
| &lt;alle&gt; |Uitgeschakeld |Uitgeschakeld |Het eind punt is uitgeschakeld. Uitgeschakelde eind punten worden niet bewaakt. Het eind punt is niet opgenomen in DNS-antwoorden, waardoor er geen verkeer wordt ontvangen. |
| Ingeschakeld |Ingeschakeld |Online |Het eind punt wordt bewaakt en is in orde. Het is opgenomen in DNS-antwoorden en kan verkeer ontvangen. |
| Ingeschakeld |Ingeschakeld |Verminderd beschikbaar |Status controles voor eindpunt controle zijn mislukt. Het eind punt is niet opgenomen in DNS-antwoorden en ontvangt geen verkeer. <br>Een uitzonde ring hierop is als alle eind punten worden gedegradeerd, in het geval dat deze allemaal worden beschouwd als geretourneerd in het antwoord op de query).</br>|
| Ingeschakeld |Ingeschakeld |CheckingEndpoint |Het eind punt wordt bewaakt, maar de resultaten van de eerste test zijn nog niet ontvangen. CheckingEndpoint is een tijdelijke status die gewoonlijk onmiddellijk optreedt nadat er een eind punt in het profiel is toegevoegd of ingeschakeld. Een eind punt in deze status is opgenomen in DNS-antwoorden en kan verkeer ontvangen. |
| Ingeschakeld |Ingeschakeld |Gestopt |De web-app waarnaar het eind punt verwijst, wordt niet uitgevoerd. Controleer de instellingen van de web-app. Dit kan ook gebeuren als het eind punt van het type genest eind punt is en het onderliggende profiel is uitgeschakeld of niet actief is. <br>Een eind punt met de status stopped wordt niet bewaakt. Het is niet opgenomen in DNS-antwoorden en ontvangt geen verkeer. Een uitzonde ring hierop is als alle eind punten worden gedegradeerd. in dat geval worden deze allemaal beschouwd als geretourneerd in het antwoord op de query.</br>|

Zie [geneste Traffic Manager profielen](traffic-manager-nested-profiles.md)voor meer informatie over het berekenen van de status van de eindpunt monitor voor geneste eind punten.

>[!NOTE]
> Als uw webtoepassing niet wordt uitgevoerd in de Standard-laag of hierboven, kan de status van de eindpunt monitor niet worden gestopt op App Service. Zie [integratie met App Service Traffic Manager](../app-service/web-sites-traffic-manager.md)voor meer informatie.

### <a name="profile-monitor-status"></a>Status van profiel monitor

De profiel Monitor status is een combi natie van de geconfigureerde profiel status en de status waarden van de eindpunt monitor voor alle eind punten. De mogelijke waarden worden beschreven in de volgende tabel:

| Profiel status (zoals geconfigureerd) | Status van eindpunt monitor | Status van profiel monitor | Notities |
| --- | --- | --- | --- |
| Uitgeschakeld |&lt;een &gt; of meer profielen zonder gedefinieerde eind punten. |Uitgeschakeld |Het profiel is uitgeschakeld. |
| Ingeschakeld |De status van ten minste één eind punt is gedegradeerd. |Verminderd beschikbaar |Controleer de afzonderlijke eindpunt status waarden om te bepalen welke eind punten verdere aandacht vereisen. |
| Ingeschakeld |De status van ten minste één eind punt is online. Geen van de eind punten heeft een gedegradeerde status. |Online |Het verkeer wordt geaccepteerd door de service. Er is geen verdere actie vereist. |
| Ingeschakeld |De status van ten minste één eind punt is CheckingEndpoint. Er zijn geen eind punten in de online of gedegradeerde status. |CheckingEndpoints |Deze overgangs status treedt op wanneer een profiel wordt gemaakt of ingeschakeld. De status van het eind punt wordt voor de eerste keer gecontroleerd. |
| Ingeschakeld |De statussen van alle eind punten in het profiel zijn uitgeschakeld of gestopt of het profiel heeft geen gedefinieerde eind punten. |Niet-actief |Er zijn geen eind punten actief, maar het profiel is nog ingeschakeld. |

## <a name="endpoint-failover-and-recovery"></a>Failover en herstel van eind punten

Traffic Manager controleert periodiek de status van elk eind punt, met inbegrip van beschadigde eind punten. Traffic Manager detecteert wanneer een eind punt in orde is en weer naar draaiing brengt.

Een eind punt heeft een slechte status wanneer een van de volgende gebeurtenissen optreedt:

- Als het bewakings protocol HTTP of HTTPS is:
    - Een niet-200-antwoord, of een antwoord dat niet het status bereik bevat dat is opgegeven in de instelling **verwachte status code-bereiken** , is ontvangen (inclusief een andere 2xx-code of een 301/302-omleiding).
- Als het bewakings protocol TCP is: 
    - Er wordt een ander antwoord dan ACK of SYN-ACK ontvangen in reactie op de SYN-aanvraag die door Traffic Manager is verzonden om een verbinding tot stand te brengen.
- Out. 
- Elk ander verbindings probleem waardoor het eind punt niet bereikbaar is.

Zie voor meer informatie over het oplossen van mislukte controles [problemen oplossen met een gedegradeerde status op Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md). 

De tijd lijn in de volgende afbeelding is een gedetailleerde beschrijving van het bewakings proces van Traffic Manager-eind punt dat de volgende instellingen heeft: bewakings protocol is HTTP, het probing-interval is 30 seconden, het aantal toegelaten fouten is 3, de time-outwaarde is 10 seconden en de DNS-TTL is 30 seconden.

![Failover van het eind punt en de failback-reeks Traffic Manager](./media/traffic-manager-monitoring/timeline.png)

**Afbeelding: failover van het Traffic Manager-eind punt en de herstel sequentie**

1. **Ophalen**. Voor elk eind punt voert het Traffic Manager bewakings systeem een GET-aanvraag uit op het pad dat is opgegeven in de instellingen voor controle.
2. **200 OK of aangepast code bereik dat is opgegeven Traffic Manager instellingen voor profiel bewaking** . Het bewakings systeem verwacht een HTTP 200 OK-of het aangepaste code bereik dat is opgegeven Traffic Manager het bericht controle-instellingen van het profiel moet binnen 10 seconden worden geretourneerd. Wanneer deze reactie wordt ontvangen, herkent het de service.
3. **30 seconden tussen controles**. De status controle van het eind punt wordt elke 30 seconden herhaald.
4. De **service is niet beschikbaar**. De service is niet meer beschikbaar. Traffic Manager weet pas de volgende status controle.
5. **Probeert toegang te krijgen tot het controlepad**. Het bewakings systeem voert een GET-aanvraag uit, maar ontvangt geen reactie binnen de time-outperiode van tien seconden (er kan ook een niet-200-antwoord worden ontvangen). Vervolgens wordt er drie keer per interval van 30 seconden geprobeerd. Als een van de pogingen is gelukt, wordt het aantal pogingen opnieuw ingesteld.
6. **Status ingesteld op gedegradeerd**. Na een vierde opeenvolgende fout markeert het bewakings systeem de status van het niet-beschik bare eind punt als gedegradeerd.
7. **Verkeer wordt omverkocht naar andere eind punten**. De Traffic Manager DNS-naam servers worden bijgewerkt en Traffic Manager het eind punt niet meer retourneert als reactie op DNS-query's. Nieuwe verbindingen worden omgeleid naar andere, beschik bare eind punten. Eerdere DNS-antwoorden die dit eind punt bevatten, kunnen echter nog steeds in de cache worden opgeslagen door recursieve DNS-servers en DNS-clients. Clients blijven het eind punt gebruiken totdat de DNS-cache is verlopen. Als de DNS-cache is verlopen, maken clients nieuwe DNS-query's en worden ze omgeleid naar verschillende eind punten. De cache duur wordt bepaald door de TTL-instelling in het Traffic Manager profiel, bijvoorbeeld 30 seconden.
8. **Status controles worden voortgezet**. Traffic Manager blijft de status van het eind punt controleren terwijl het een gedegradeerde status heeft. Traffic Manager detecteert wanneer het eind punt met de status wordt geretourneerd.
9. **Service is weer online**. De service wordt beschikbaar. Het eind punt behoudt de gedegradeerde status in Traffic Manager totdat het bewakings systeem de volgende status controle uitvoert.
10. **Verkeer naar service wordt hervat**. Traffic Manager verzendt een GET-aanvraag en ontvangt een reactie van 200 OK status. De service heeft een goede status geretourneerd. De Traffic Manager naam servers worden bijgewerkt en de DNS-naam van de service wordt door gegeven aan de DNS-antwoorden. Verkeer keert terug naar het eind punt als DNS-antwoorden in de cache die andere eind punten retour neren, en als bestaande verbindingen met andere eind punten worden beëindigd.

    > [!NOTE]
    > Omdat Traffic Manager op het DNS-niveau werkt, kan dit geen invloed hebben op bestaande verbindingen met een eind punt. Wanneer het verkeer tussen eind punten wordt doorgestuurd (door het wijzigen van de profiel instellingen of tijdens een failover of failback), Traffic Manager nieuwe verbindingen naar beschik bare eind punten omgeleid. Andere eind punten kunnen echter verkeer blijven ontvangen via bestaande verbindingen totdat deze sessies worden beëindigd. Als u verkeer wilt inschakelen om bestaande verbindingen uit te voeren, moeten toepassingen de sessie duur beperken die wordt gebruikt voor elk eind punt.

## <a name="traffic-routing-methods"></a>Routerings methoden voor verkeer

Wanneer een eind punt een gedegradeerde status heeft, wordt deze niet meer geretourneerd als reactie op DNS-query's. In plaats daarvan wordt een alternatief eind punt gekozen en geretourneerd. De routerings methode voor verkeer die in het profiel is geconfigureerd, bepaalt hoe het alternatieve eind punt wordt gekozen.

* **Prioriteit**. Eind punten vormen een lijst met prioriteiten. Het eerste beschik bare eind punt in de lijst wordt altijd geretourneerd. Als de status van een eind punt wordt gedegradeerd, wordt het volgende beschik bare eind punt geretourneerd.
* **Gewogen**. Elk beschikbaar eind punt wordt wille keurig gekozen op basis van het toegewezen gewicht en de gewichten van de andere beschik bare eind punten.
* **Prestaties**. Het eind punt dat het dichtst bij de eind gebruiker ligt, wordt geretourneerd. Als dat eind punt niet beschikbaar is, Traffic Manager verplaatst het verkeer naar de eind punten in de dichtstbijzijnde Azure-regio. U kunt alternatieve failover plannen configureren voor prestatie verkeer-route ring door gebruik te maken van [geneste Traffic Manager profielen](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geografisch**. Het eind punt dat is toegewezen om de geografische locatie te leveren op basis van het IP-adres van de query aanvraag, wordt geretourneerd. Als dat eind punt niet beschikbaar is, wordt een ander eind punt niet geselecteerd voor failover naar, omdat een geografische locatie alleen kan worden toegewezen aan één eind punt in een profiel (meer informatie vindt u in de [Veelgestelde vragen](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Als best practice als u geografische route ring gebruikt, raden we klanten aan om geneste Traffic Manager profielen met meer dan één eind punt te gebruiken als de eind punten van het profiel.
* **Meerdere waarden** Er zijn meerdere eind punten geretourneerd die aan IPv4/IPv6-adressen zijn toegewezen. Wanneer een query voor dit profiel wordt ontvangen, worden in goede eind punten geretourneerd op basis van het **maximum aantal records in de reactie** waarde die u hebt opgegeven. Het standaard aantal antwoorden is twee eind punten.
* **Subnet** Het eind punt dat is toegewezen aan een set IP-adresbereiken wordt geretourneerd. Wanneer een aanvraag wordt ontvangen van dat IP-adres, wordt het eind punt geretourneerd dat is toegewezen aan dat IP-adres. 

Zie [Traffic Manager methoden voor het routeren van verkeer](traffic-manager-routing-methods.md)voor meer informatie.

> [!NOTE]
> Een uitzonde ring op normaal verkeer-routerings gedrag treedt op wanneer alle in aanmerking komende eind punten een gedegradeerde status hebben. Traffic Manager maakt een poging ' Best effort ' en *reageert alsof alle gedegradeerde status eindpunten in werkelijkheid online zijn*. Dit gedrag verdient de voor keur aan het alternatief, waarbij geen eind punt in het DNS-antwoord zou worden geretourneerd. Uitgeschakelde of beëindigde eind punten worden niet bewaakt, daarom worden ze niet als geschikt voor verkeer beschouwd.
>
> Dit probleem wordt meestal veroorzaakt door een onjuiste configuratie van de service, zoals:
>
> * Een toegangs beheer lijst [ACL] die de Traffic Manager status controles blokkeert.
> * Een onjuiste configuratie van de bewakings poort of het protocol in het Traffic Manager-profiel.
>
> Als Traffic Manager status controles niet op de juiste wijze zijn geconfigureerd, kan deze worden weer gegeven in de verkeers routering, *alsof Traffic Manager goed* werkt. In dit geval kan er echter geen eindpunt failover optreden die van invloed is op de algehele Beschik baarheid van de toepassing. Het is belang rijk om te controleren of het profiel een online status bevat, geen gedegradeerde status. Een online status geeft aan dat de Traffic Manager status controles werken zoals verwacht.

Zie voor meer informatie over het oplossen van problemen met mislukte status controles [oplossen van een gedegradeerde status op Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).

## <a name="faqs"></a>Veelgestelde vragen

* [Is het Traffic Managerbaar voor problemen met Azure-regio's?](./traffic-manager-faqs.md#is-traffic-manager-resilient-to-azure-region-failures)

* [Wat is de invloed van de locatie van de resource groep op Traffic Manager?](./traffic-manager-faqs.md#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Hoe kan ik de huidige status van elk eind punt bepalen?](./traffic-manager-faqs.md#how-do-i-determine-the-current-health-of-each-endpoint)

* [Kan ik HTTPS-eind punten controleren?](./traffic-manager-faqs.md#can-i-monitor-https-endpoints)

* [Moet ik een IP-adres of een DNS-naam gebruiken wanneer ik een eind punt toevoeg?](./traffic-manager-faqs.md#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Welke typen IP-adressen kan ik gebruiken bij het toevoegen van een eind punt?](./traffic-manager-faqs.md#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [Kan ik verschillende typen eindpunt adresseren gebruiken binnen één profiel?](./traffic-manager-faqs.md#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [Wat gebeurt er wanneer het record type van een binnenkomende query verschilt van het record type dat is gekoppeld aan het adresserings type van de eind punten?](./traffic-manager-faqs.md#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [Kan ik een profiel gebruiken met door IPv4/IPv6 geadresseerde eind punten in een genest profiel?](./traffic-manager-faqs.md#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Ik heb een eind punt van een webtoepassing in mijn Traffic Manager profiel gestopt, maar ik heb geen verkeer ontvangen, zelfs nadat ik het heb gestart. Hoe kan ik dit oplossen?](./traffic-manager-faqs.md#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Kan ik Traffic Manager gebruiken, zelfs als mijn toepassing geen ondersteuning voor HTTP of HTTPS heeft?](./traffic-manager-faqs.md#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Welke specifieke antwoorden zijn vereist van het eind punt bij het gebruik van TCP-bewaking?](./traffic-manager-faqs.md#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Hoe snel Traffic Manager kan ik mijn gebruikers weghalen uit een beschadigd eind punt?](./traffic-manager-faqs.md#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Hoe kan ik verschillende bewakings instellingen opgeven voor verschillende eind punten in een profiel?](./traffic-manager-faqs.md#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Hoe kan ik HTTP-headers toewijzen aan de Traffic Manager status controles voor mijn eind punten?](./traffic-manager-faqs.md#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Welke host-header gebruikt de eindpunt status controles?](./traffic-manager-faqs.md#what-host-header-do-endpoint-health-checks-use)

* [Wat zijn de IP-adressen waarvan de status controles afkomstig zijn?](./traffic-manager-faqs.md#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Hoeveel status controles voor mijn eind punt kan ik verwachten van Traffic Manager?](./traffic-manager-faqs.md#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Hoe kan ik een melding krijgen als een van mijn eind punten uitvalt?](./traffic-manager-faqs.md#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Volgende stappen

Meer informatie [over de werking van Traffic Manager](traffic-manager-how-it-works.md)

Meer informatie over de [routerings methoden voor verkeer](traffic-manager-routing-methods.md) die door Traffic Manager worden ondersteund

Meer informatie over het [maken van een Traffic Manager profiel](traffic-manager-manage-profiles.md)

[Problemen met de gedegradeerde status](traffic-manager-troubleshooting-degraded.md) op een Traffic Manager eind punt oplossen