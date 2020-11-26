---
title: Voor deur van Azure, veelgestelde vragen
description: Op deze pagina vindt u antwoorden op veelgestelde vragen over Azure front deur
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2020
ms.author: duau
ms.openlocfilehash: abc4529d6076496b34859eec2b931a8dcbd1ce93
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296587"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Veelgestelde vragen over de voor deur van Azure

In dit artikel vindt u antwoorden op veelgestelde vragen over de functies en functionaliteit van Azure front deur. Als u het antwoord op uw vraag niet ziet, kunt u contact met ons opnemen via de volgende kanalen (in de volg orde waarin ze worden doorzocht):

1. De sectie opmerkingen van dit artikel.
2. [Azure front-deur UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Microsoft ondersteuning:** Als u een nieuwe ondersteunings aanvraag wilt maken, selecteert u in het Azure Portal op het tabblad **Help** de knop **Help en ondersteuning** en selecteert u vervolgens **nieuwe ondersteunings aanvraag**.

## <a name="general"></a>Algemeen

### <a name="what-is-azure-front-door"></a>Wat is Azure Front Door?

Azure front deur is een Application Delivery Network (ADN) als een service, met verschillende mogelijkheden voor de taak verdeling van laag 7 voor uw toepassingen. Deze service biedt dynamische siteversnelling (DSA) samen met wereldwijde taakverdeling met failover in bijna realtime. Het is een maximaal beschikbare en schaalbare service die volledig wordt beheerd door Azure.

### <a name="what-features-does-azure-front-door-support"></a>Welke functies worden ondersteund door Azure front-deur?

Azure front deur biedt ondersteuning voor dynamische site versnelling (DSA), TLS/SSL-offloading en end-to-end TLS, Web Application firewall, op cookies gebaseerde sessie affiniteit, op URL-pad gebaseerde route ring, gratis certificaten en meerdere domein beheer, en andere. Zie [overzicht van Azure front deur](front-door-overview.md)voor een volledige lijst met ondersteunde functies.

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Wat is het verschil tussen de Azure front-deur en de Azure-toepassing-gateway?

Hoewel zowel de voor deur als de Application Gateway laag 7 (HTTP/HTTPS) load balancers zijn, is het belangrijkste verschil dat de voor deur een wereld wijde service is, terwijl Application Gateway een regionale service is. Met Application Gateway kunt u een taak verdeling tussen uw virtuele machines/containers/clusters/stempels in verschillende regio's, met behulp van de voor deur, waarmee u het evenwicht tussen uw Vm's en recipiënten, enzovoort.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Wanneer moet een Application Gateway achter aan de voor deur worden geïmplementeerd?

De belangrijkste scenario's waarvan een moet worden gebruikt Application Gateway achter de voor deur zijn:

- Met de voor deur kan alleen op het globale niveau taak verdeling op basis van een pad worden uitgevoerd, maar als er nog meer verkeer in het virtuele netwerk (VNET) moet worden geladen, moeten ze Application Gateway gebruiken.
- Omdat de voor deur niet werkt op een VM-of container niveau, kan er geen verbinding worden verbroken. Met Application Gateway kunt u echter de verbinding verbreken. 
- Met een Application Gateway achter de deur kan één van 100% TLS/SSL-offload worden gerealiseerd en kunnen alleen HTTP-aanvragen worden gerouteerd binnen hun virtuele netwerk (VNET).
- De voor deur en Application Gateway de affiniteit van de ondersteunings sessie. Hoewel de front-deur volgend verkeer van een gebruikers sessie naar hetzelfde cluster of dezelfde back-end in een bepaalde regio kan omleiden, kan Application Gateway het verkeer naar dezelfde server in het cluster affinitize.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Kunnen we Azure Load Balancer implementeren achter de voor deur?

Azure front deur moet een openbaar VIP of een openbaar beschik bare DNS-naam hebben om het verkeer naar te sturen. Het implementeren van een Azure Load Balancer achter deur is een veelvoorkomende use-case.

### <a name="what-protocols-does-azure-front-door-support"></a>Welke protocollen ondersteunt Azure front-deur?

Azure front-deur ondersteunt HTTP, HTTPS en HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Hoe biedt Azure front-deur ondersteuning voor HTTP/2?

Ondersteuning voor HTTP/2-protocollen is alleen beschikbaar voor clients die alleen verbinding maken met de front-deur van Azure. De communicatie met back-ends in de back-end-groep is via HTTP/1.1. HTTP/2-ondersteuning is standaard ingeschakeld.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Welke bronnen worden vandaag ondersteund als onderdeel van de back-end-groep?

Back-endservers kunnen bestaan uit opslag, Web-app, Kubernetes-instanties of een andere aangepaste hostnaam die open bare connectiviteit heeft. Voor de Azure-deur moeten de back-ends worden gedefinieerd via een openbaar IP-adres of een openbaar omzet bare DNS-hostnaam. Leden van back-endservers kunnen zich in zones, regio's of zelfs buiten Azure bevinden, zolang ze open bare connectiviteit hebben.

### <a name="what-regions-is-the-service-available-in"></a>In welke regio's is de service beschikbaar?

Azure front-deur is een wereld wijde service en is niet gebonden aan een specifieke Azure-regio. De enige locatie die u moet opgeven tijdens het maken van een voor deur is de locatie van de resource groep, die in principe aangeeft waar de meta gegevens voor de resource groep worden opgeslagen. De voor deur van de resource zelf wordt gemaakt als globale resource en de configuratie wordt globaal geïmplementeerd op alle Pop's (Presence Point). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Wat zijn de POP-locaties voor Azure front-deur?

De front-deur van Azure heeft dezelfde lijst met POP-locaties (Point of Presence) als Azure CDN van micro soft. Voor de volledige lijst met onze Pop's verwijzen we naar [Azure CDN pop-locaties van micro soft](../cdn/cdn-pop-locations.md).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Is de voor deur van Azure een speciale implementatie voor mijn toepassing of wordt deze gedeeld met klanten?

Azure front-deur is een wereld wijd gedistribueerde multi tenant-service. De infra structuur voor de voor deur wordt dus gedeeld door alle klanten. Als u echter een front deur profiel maakt, definieert u de specifieke configuratie die is vereist voor uw toepassing. er zijn geen wijzigingen aangebracht aan de voor deur van andere front-deur configuraties.

### <a name="is-http-https-redirection-supported"></a>Wordt HTTP->HTTPS-omleiding ondersteund?

Ja. In feite ondersteunt Azure front-deur hosts, paden en het omleiden van query reeksen, evenals een deel van URL-omleiding. Meer informatie over [URL-omleiding](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>In welke volg orde worden routerings regels verwerkt?

Routes voor de voor deur worden niet besteld en een specifieke route wordt geselecteerd op basis van de beste overeenkomst. Meer informatie over [de voor deur die overeenkomt met aanvragen voor een routerings regel](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Hoe kan ik Vergrendel de toegang tot mijn back-end naar een Azure front-deur?

Als u uw toepassing wilt vergren delen om alleen verkeer van uw specifieke voor deur te accepteren, moet u IP-Acl's instellen voor uw back-end en vervolgens het verkeer op uw back-end beperken tot de specifieke waarde van de header X-Azure-FDID die wordt verzonden door de voor deur. Deze stappen worden hieronder beschreven:

- Configureer IP-Acl's voor voor uw back-ends om verkeer te accepteren van de back-end-IP-adres ruimte van de Azure front deur en alleen de infrastructuur services van Azure. Raadpleeg de onderstaande IP-gegevens voor Acl's voor van uw back-end:
 
    - Raadpleeg de sectie *AzureFrontDoor. back-end* in [Azure IP-bereiken en-Tags](https://www.microsoft.com/download/details.aspx?id=56519) voor het IPv4-back-end-IP-adres bereik van de front deur of u kunt ook de service label *AzureFrontDoor. back-end* gebruiken in uw [netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md#security-rules).
    - De [basis infrastructuur services](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) van Azure via gevirtualiseerde host-IP-adressen: `168.63.129.16` en `169.254.169.254`

    > [!WARNING]
    > De backend-back-end van de voor deur kan later worden gewijzigd, maar we zorgen ervoor dat voordat dat gebeurt, dat we hebben geïntegreerd met [Azure IP-bereiken en-Tags](https://www.microsoft.com/download/details.aspx?id=56519). U wordt aangeraden om u te abonneren op [Azure IP-bereiken en service Tags](https://www.microsoft.com/download/details.aspx?id=56519) voor eventuele wijzigingen of updates.

-    Voer een GET-bewerking uit op uw voor deur met de API-versie `2020-01-01` of hoger. Zoek in de API-aanroep naar `frontdoorID` veld. Filter op de inkomende header '**X-Azure-FDID**' die door de voor deur naar uw back-end wordt verzonden met de waarde van het veld `frontdoorID` . U kunt `Front Door ID` de waarde ook vinden in de sectie Overzicht van de portal-pagina voor de voor deur. 

- Regel filtering Toep assen op uw back-end-webserver om verkeer te beperken op basis van de resulterende waarde ' X-Azure-FDID '.

  Hier volgt een voor beeld van [micro soft Internet Information Services (IIS)](https://www.iis.net/):

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```



### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Kan het anycast-IP-adres worden gewijzigd gedurende de levens duur van mijn front-deur?

Het front-end-frontend-IP-adres voor de voor deur moet normaal gesp roken niet worden gewijzigd en kan statisch blijven gedurende de levens duur van de voor deur. Er zijn echter **geen garanties** voor dezelfde. Neem geen directe afhankelijkheden op het IP-adres.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Ondersteunt de front-deur van Azure statische of toegewezen Ip's?

Nee, Azure front deur biedt momenteel geen ondersteuning voor statische of speciale frontend-IP-adressen. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Ondersteunt Azure front-deur x-doorgestuurd-voor kopteksten?

Ja, de Azure-front-deur ondersteunt de headers X-forward-for, X-forward-host en X-forward-proto. Voor X-doorgestuurd: als de header al aanwezig is, wordt het IP-adres van de client aan de voor deur toegevoegd. Anders wordt de header met het IP-adres van de client-socket als waarde toegevoegd. Voor X-doorgestuurd-host en X-doorgestuurd-proto wordt de waarde overschreven.

Meer informatie over de [ondersteunde HTTP-headers voor de voor deur](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Hoe lang duurt het om een Azure front-deur te implementeren? Werkt mijn front-deur nog steeds wanneer ze worden bijgewerkt?

Een nieuwe voor deur maken of updates voor een bestaande front-deur neemt ongeveer drie tot vijf minuten in beslag voor wereld wijde implementatie. Dat betekent ongeveer 3 tot 5 minuten dat de configuratie van de voor deur wordt geïmplementeerd in al onze Pop's wereld wijd.

Opmerking: aangepaste TLS/SSL-certificaat updates nemen ongeveer 30 minuten in beslag.

Eventuele updates van routes of back-end-Pools, enzovoort, zijn naadloos en veroorzaken geen downtime (als de nieuwe configuratie juist is). Certificaat updates zijn ook atomisch en veroorzaken geen storingen, tenzij u overschakelt van ' AFD Managed ' naar ' uw eigen certificaat gebruiken ' of andersom.


## <a name="configuration"></a>Configuration

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Kan de taak verdeling van de voor deur van Azure voor de voor grond zijn of verkeer binnen een virtueel netwerk routeren?

Voor de Azure-deur (AFD) is een openbaar IP-adres of een openbaar omgezette DNS-naam vereist om verkeer te routeren. Het antwoord is dus geen AFD direct in staat te stellen om binnen een virtueel netwerk te routeren, maar met behulp van een Application Gateway of Azure Load Balancer in tussen wordt dit scenario opgelost.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Wat zijn de verschillende time-outs en limieten voor de Azure front-deur?

Meer informatie over alle gedocumenteerde [time-outs en limieten voor de Azure front-deur](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits).

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Hoe lang duurt het voordat een regel van kracht wordt nadat deze is toegevoegd aan de engine voor de voor deur regels?

De configuratie van de regel engine duurt ongeveer 10 tot 15 minuten om een update te volt ooien. Zodra de update is voltooid, kunt u ervan uitgaan dat de regel van kracht wordt. 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-vice-versa"></a>Kan ik Azure CDN configureren achter mijn front-deur profiel of andersom?

De voor deur en het Azure CDN van Azure kunnen niet samen worden geconfigureerd, omdat beide services gebruikmaken van dezelfde Azure Edge-sites wanneer ze op aanvragen reageren. 

## <a name="performance"></a>Prestaties

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Hoe ondersteunt Azure front-deur hoge Beschik baarheid en schaal baarheid?

Azure front-deur is een wereld wijd gedistribueerd multi tenant platform met grote hoeveel heden capaciteit voor de schaal baarheids behoeften van uw toepassing. De deur van het wereld wijde netwerk van micro soft biedt wereld wijde mogelijkheden voor taak verdeling waarmee u een failover kunt uitvoeren voor uw hele toepassing of zelfs voor afzonderlijke micro Services in regio's of verschillende Clouds.

## <a name="tls-configuration"></a>TLS-configuratie

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Welke TLS-versies worden ondersteund door Azure front-deur?

Voor alle profielen voor de voorste deur die na september 2019 worden gemaakt, wordt TLS 1,2 als standaard minimum gebruikt.

De front-deur ondersteunt TLS-versies 1,0, 1,1 en 1,2. TLS 1,3 wordt nog niet ondersteund.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Welke certificaten worden ondersteund op de front-deur van Azure?

Als u het HTTPS-protocol wilt inschakelen voor het veilig leveren van inhoud op een aangepast domein voor de voor deur, kunt u kiezen of u een certificaat wilt gebruiken dat wordt beheerd door Azure front deur of dat u uw eigen certificaat gebruikt.
Met de voor deur beheerde optie wordt een standaard TLS/SSL-certificaat ingericht via Digicert en opgeslagen in de Key Vault van de front-deur. Als u ervoor kiest uw eigen certificaat te gebruiken, kunt u een certificaat van een ondersteunde CA onboarden en een standaard TLS-, uitgebreide validatie certificaat of zelfs een certificaat met Joker tekens. Zelfondertekende certificaten worden niet ondersteund. Meer informatie [over het inschakelen van HTTPS voor een aangepast domein](./front-door-custom-domain-https.md).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Ondersteunt front-deur het omdraaien van certificaten?

Voor de optie beheerd certificaat voor de voor deur worden de certificaten automatisch gedraaid door de voor deur. Als u een door de voor deur beheerd certificaat gebruikt en u ziet dat de verval datum van het certificaat korter is dan 60 dagen, kunt u een ondersteunings ticket indienen.
</br>Voor uw eigen aangepaste TLS/SSL-certificaat wordt autorotation niet ondersteund. Net als bij het instellen van de eerste keer voor een aangepast domein, moet u de voor deur naar de juiste certificaat versie in uw Key Vault pointen en ervoor zorgen dat de service-principal voor de voor deur nog steeds toegang heeft tot de Key Vault. Deze bijgewerkte certificaat lancerings bewerking door de voor deur is Atomic en heeft geen invloed op de productie, mits de object naam of het SAN voor het certificaat niet verandert.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Wat zijn de huidige coderings suites die worden ondersteund door de Azure front-deur?

Voor TLS 1.2 worden de volgende coderings suites ondersteund: 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

Wanneer u aangepaste domeinen met TLS 1.0/1.1 gebruikt, worden de volgende coderings suites ondersteund:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Kan ik TLS-beleid configureren voor het beheren van TLS-protocol versies?

U kunt een minimale TLS-versie in azure front-deur configureren in de HTTPS-instellingen van het aangepaste domein via Azure Portal of [Azure rest API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). Op dit moment kunt u kiezen tussen 1,0 en 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Kan ik front-deur zo configureren dat alleen specifieke coderings suites worden ondersteund?

Nee, het configureren van de voor deur voor specifieke coderings suites wordt niet ondersteund. U kunt echter uw eigen aangepaste TLS/SSL-certificaat van uw certificerings instantie ontvangen (bijvoorbeeld VeriSign, belasters of Digicert) en specifieke coderings suites op het certificaat hebben gemarkeerd wanneer het is gegenereerd. 

### <a name="does-front-door-support-ocsp-stapling"></a>Biedt front-deur ondersteuning voor het koppelen van OCSP?

Ja, OCSP-nietfunctie wordt standaard door de voor deur ondersteund en er is geen configuratie vereist.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Ondersteunt Azure front-deur ook het opnieuw versleutelen van verkeer naar de back-end?

Ja, Azure front deur ondersteunt TLS/SSL-offload en end-to-end TLS, waarmee het verkeer opnieuw wordt versleuteld naar de back-end. Omdat de verbindingen met de back-end plaatsvinden via het open bare IP-adres, wordt het aanbevolen dat u de voor deur configureert om HTTPS als het doorstuur protocol te gebruiken.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Ondersteunt front-deur zelfondertekende certificaten op de back-end voor HTTPS-verbinding?

Nee, zelfondertekende certificaten worden niet op de voor deur ondersteund en de beperking is van toepassing op beide:

1. **Back**-ends: u kunt geen zelfondertekende certificaten gebruiken wanneer u het verkeer doorstuurt als HTTPS-of https-status controle of het invullen van de cache voor van oorsprong voor routerings regels waarvoor caching is ingeschakeld.
2. Front- **End**: u kunt geen zelfondertekende certificaten gebruiken wanneer u uw eigen aangepaste TLS/SSL-certificaat gebruikt om https in te scha kelen voor uw aangepaste domein.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Waarom is HTTPS-verkeer naar mijn back-end mislukt?

Als u een geslaagde HTTPS-verbinding met de back-end wilt maken, ongeacht of de status test of aanvragen worden doorgestuurd, kunnen er twee redenen zijn waarom HTTPS-verkeer kan mislukken:

1. De naam van het **certificaat onderwerp komt niet overeen**: voor HTTPS-verbindingen verwacht de voor deur dat uw back-end een certificaat van een geldige CA met de naam van de back-end (en) overeenkomt met de backend-hostnaam. Als bijvoorbeeld de hostnaam van de back-end is ingesteld op `myapp-centralus.contosonews.net` en het certificaat dat uw back-end presenteert tijdens de TLS `myapp-centralus.contosonews.net` -Handshake, noch de `*myapp-centralus*.contosonews.net` naam van het onderwerp, wordt de verbinding door de voor deur geweigerd en wordt er een fout geretourneerd. 
    1. **Oplossing**: Hoewel het niet wordt aangeraden om het probleem op te lossen, kunt u deze fout omzeilen door de naam van de certificaat houder voor uw deur te controleren. Dit is aanwezig onder instellingen in Azure Portal en onder BackendPoolsSettings in de API.
2. **Backend-hosting certificaat van ongeldige ca**: alleen certificaten van [geldige certificerings instanties](./front-door-troubleshoot-allowed-ca.md) kunnen worden gebruikt op de back-end met de voor deur. Certificaten van interne Ca's of zelfondertekende certificaten zijn niet toegestaan.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Kan ik client/wederzijdse verificatie met Azure front-deur gebruiken?

Nee. Hoewel Azure front-deur TLS 1,2 ondersteunt, die client/wederzijdse authenticatie in [RFC 5246](https://tools.ietf.org/html/rfc5246)heeft geïntroduceerd, biedt Azure front-deur momenteel geen ondersteuning voor client/wederzijdse verificatie.

## <a name="diagnostics-and-logging"></a>Diagnostische gegevens en logboekregistratie

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Welke soorten metrische gegevens en logboeken zijn beschikbaar met Azure front-deur?

Zie [metrische gegevens en logboeken voor de voor deur bewaken](front-door-diagnostics.md)voor meer informatie over Logboeken en andere diagnostische mogelijkheden.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Wat is het Bewaar beleid voor de diagnostische logboeken?

Diagnostische logboeken stroomt naar het opslag account van klanten en klanten kunnen het Bewaar beleid instellen op basis van hun voor keur. Diagnostische logboeken kunnen ook worden verzonden naar een event hub of Azure Monitor-Logboeken. Zie voor meer informatie [Azure front-deur diagnostiek](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Hoe kan ik audit logboeken voor Azure front-deur ophalen?

Er zijn audit logboeken beschikbaar voor de front-deur van Azure. Klik in de portal op **activiteiten logboek** in de menu-Blade van de voor deur om toegang te krijgen tot het audit logboek. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Kan ik waarschuwingen instellen met behulp van de voor deur van Azure?

Ja, de voor deur van Azure biedt ondersteuning voor waarschuwingen. Waarschuwingen worden geconfigureerd voor metrische gegevens. 

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
