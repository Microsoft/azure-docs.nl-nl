---
title: 'Azure front deur: veelgestelde vragen'
description: Op deze pagina vindt u antwoorden op veelgestelde vragen over Azure front deur Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: a42601b696f292e9d2a9da90070fea3662acae87
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099111"
---
# <a name="frequently-asked-questions-for-azure-front-door-standardpremium-preview"></a>Veelgestelde vragen over Azure front deur Standard/Premium (preview)

In dit artikel vindt u antwoorden op veelgestelde vragen over de functies en functionaliteit van Azure front deur.

## <a name="general"></a>Algemeen

### <a name="what-is-azure-front-door"></a>Wat is Azure Front Door?

Azure front-deur is een snelle, betrouw bare en veilige moderne Cloud CDN met intelligente beveiliging tegen bedreigingen. Het biedt statische en dynamische inhouds versnelling, wereld wijde taak verdeling en verbeterde beveiliging voor uw wereld wijde, schaal bare toepassingen, Api's, websites en Cloud Services met intelligente bedreigings beveiliging.

### <a name="what-features-does-azure-front-door-support"></a>Welke functies worden ondersteund door Azure front-deur?

Azure front-deur ondersteunt:

* Zowel statische inhoud als dynamische toepassings versnelling
* TLS/SSL-offloading en end-to-end-TLS
* Web Application Firewall
* Sessie affiniteit op basis van cookies
* Op URL-pad gebaseerde route ring
* Gratis certificaten en meerdere domein beheer

Zie [overzicht van Azure front deur](overview.md)voor een volledige lijst met ondersteunde functies.

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Wat is het verschil tussen de Azure front-deur en de Azure-toepassing-gateway?

Hoewel zowel de voor deur als de Application Gateway laag 7 (HTTP/HTTPS) load balancers zijn, is het belangrijkste verschil dat de voor deur een wereld wijde service is. Application Gateway is een regionale service. Met Application Gateway kunt u een taak verdeling maken tussen de verschillende schaal eenheden/clusters/stempel eenheden tussen regio's, met behulp van de breedte van de voor deur tussen de virtuele machines en containers die zich binnen de schaal eenheid bevinden.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Wanneer moet een Application Gateway achter aan de voor deur worden geïmplementeerd?

De belangrijkste scenario's waarvan een moet worden gebruikt Application Gateway achter de voor deur zijn:

* Met de voor deur kan alleen taak verdeling op basis van een pad op het globale niveau worden uitgevoerd, maar als er nog meer verkeer in het virtuele netwerk (VNET) moet worden geladen, moeten ze Application Gateway gebruiken.
* Omdat de voor deur niet werkt op een VM-of container niveau, kan er geen verbinding worden verbroken. Met Application Gateway kunt u echter de verbinding verbreken. 
* Met een Application Gateway achter de deur kan één van 100% TLS/SSL-offload worden gerealiseerd en kunnen alleen HTTP-aanvragen worden gerouteerd binnen hun virtuele netwerk (VNET).
* De voor deur en Application Gateway de affiniteit van de ondersteunings sessie. De voor deur kan verkeer omleiden van een gebruikers sessie naar hetzelfde cluster of dezelfde back-end in een bepaalde regio. Application Gateway kunt het verkeer affinitize naar dezelfde server in het cluster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Kunnen we Azure Load Balancer implementeren achter de voor deur?

Azure front deur moet een openbaar VIP of een openbaar beschik bare DNS-naam hebben om het verkeer naar te sturen. Het implementeren van een Azure Load Balancer achter deur is een veelvoorkomende use-case.

### <a name="what-protocols-does-azure-front-door-support"></a>Welke protocollen ondersteunt Azure front-deur?

Azure front-deur ondersteunt HTTP, HTTPS en HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Hoe biedt Azure front-deur ondersteuning voor HTTP/2?

Ondersteuning voor HTTP/2-protocollen is alleen beschikbaar voor clients die alleen verbinding maken met de front-deur van Azure. De communicatie met back-ends in de back-end-groep is via HTTP/1.1. HTTP/2-ondersteuning is standaard ingeschakeld.

### <a name="what-resources-are-supported-today-as-part-of-origin-group"></a>Welke bronnen worden vandaag ondersteund als onderdeel van de oorspronkelijke groep?

De oorspronkelijke groep kan bestaan uit opslag, Web-app, Kubernetes-instanties of een andere aangepaste hostnaam die open bare connectiviteit heeft. Voor de Azure-deur moeten de oorsprongen worden gedefinieerd via een openbaar IP-adres of een openbaar omzet bare DNS-hostnaam. Leden van de oorspronkelijke groep kunnen zich in zones, regio's of zelfs buiten Azure bevinden, zolang ze open bare connectiviteit hebben.

### <a name="what-regions-is-the-service-available-in"></a>In welke regio's is de service beschikbaar?

Azure front-deur is een wereld wijde service en is niet gekoppeld aan een specifieke Azure-regio. De enige locatie die u moet opgeven tijdens het maken van een voor deur is voor de resource groep. Deze locatie geeft in principe op waar de meta gegevens voor de resource groep worden opgeslagen. De voor deur van de resource zelf wordt gemaakt als globale resource en de configuratie wordt globaal geïmplementeerd op alle Pop's (Presence Point). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Wat zijn de POP-locaties voor Azure front-deur?

De front-deur van Azure heeft dezelfde lijst met POP-locaties (Point of Presence) als Azure CDN van micro soft. Voor de volledige lijst met onze Pop's verwijzen we naar [Azure CDN pop-locaties van micro soft](../../cdn/cdn-pop-locations.md).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Is de voor deur van Azure een speciale implementatie voor mijn toepassing of wordt deze gedeeld met klanten?

Azure front-deur is een wereld wijd gedistribueerde multi tenant-service. De infra structuur voor de voor deur wordt gedeeld door alle klanten. Door een voor deur profiel te maken, definieert u de specifieke configuratie die vereist is voor uw toepassing. Wijzigingen die in de voor deur worden aangebracht, hebben geen invloed op andere front-deur configuraties.

### <a name="is-http-https-redirection-supported"></a>Wordt HTTP->HTTPS-omleiding ondersteund?

Ja. In feite ondersteunt Azure front-deur host, pad, omleiding van query reeksen en een deel van de URL-omleiding. Meer informatie over [URL-omleiding](concept-rule-set-url-redirect-and-rewrite.md). 

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Hoe kan ik Vergrendel de toegang tot mijn back-end naar een Azure front-deur?

Als u uw toepassing wilt vergren delen om alleen verkeer van uw specifieke front-deur te accepteren, moet u IP-Acl's instellen voor uw back-end. Beperk vervolgens het verkeer van uw back-end tot de specifieke waarde van de header X-Azure-FDID die wordt verzonden door de voor deur. Deze stappen worden hieronder beschreven:

* Configureer IP-Acl's voor voor uw back-ends om verkeer te accepteren van de back-end-IP-adres ruimte van de Azure front deur en alleen de infrastructuur services van Azure. Raadpleeg de onderstaande IP-gegevens voor Acl's voor van uw back-end:
 
    * Zie de sectie *AzureFrontDoor. back-end* in [Azure IP-bereiken en service Tags](https://www.microsoft.com/download/details.aspx?id=56519) voor het IP-adres bereik van de voor deur van de front-end. U kunt ook de service label *AzureFrontDoor. backend* gebruiken in uw [netwerk beveiligings groepen](../../virtual-network/network-security-groups-overview.md#security-rules).
    * De [basis infrastructuur services](../../virtual-network/network-security-groups-overview.md#azure-platform-considerations) van Azure via gevirtualiseerde host-IP-adressen: `168.63.129.16` en `169.254.169.254` .

    > [!WARNING]
    > De backend-back-end van de voor deur kan later worden gewijzigd, maar we zorgen ervoor dat voordat dat gebeurt, dat we hebben geïntegreerd met [Azure IP-bereiken en-Tags](https://www.microsoft.com/download/details.aspx?id=56519). U wordt aangeraden om u te abonneren op [Azure IP-bereiken en service Tags](https://www.microsoft.com/download/details.aspx?id=56519) voor eventuele wijzigingen of updates.

* Voer een GET-bewerking uit op uw voor deur met de API-versie `2020-01-01` of hoger. Zoek in de API-aanroep naar `frontdoorID` veld. Filter op de inkomende header '**X-Azure-FDID**' die door de voor deur naar uw back-end wordt verzonden met de waarde van het veld `frontdoorID` . U kunt `Front Door ID` de waarde ook vinden in de sectie Overzicht van de portal-pagina voor de voor deur. 

* Regel filtering Toep assen op uw back-end-webserver om verkeer te beperken op basis van de resulterende waarde ' X-Azure-FDID '.

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

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Hoe lang duurt het om een Azure front-deur te implementeren? Werkt mijn front-deur nog steeds wanneer ze worden bijgewerkt?

Een nieuwe voor deur maken of updates voor een bestaande front-deur neemt ongeveer drie tot vijf minuten in beslag voor wereld wijde implementatie. Dat betekent ongeveer 3 tot 5 minuten dat de configuratie van de voor deur wordt geïmplementeerd in al onze Pop's wereld wijd.

Opmerking: aangepaste TLS/SSL-certificaat updates nemen ongeveer 30 minuten in beslag.

Eventuele updates van routes of back-end-Pools zijn naadloos en veroorzaken geen downtime (als de nieuwe configuratie juist is). Certificaat updates veroorzaken geen storingen, tenzij u overschakelt van ' Azure front-deur beheerd ' naar ' uw eigen certificaat gebruiken ' of op een andere manier.


## <a name="configuration"></a>Configuratie

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Kan de taak verdeling van de voor deur van Azure voor de voor grond zijn of verkeer binnen een virtueel netwerk routeren?

Voor de Azure-deur (AFD) is een openbaar IP-adres of een openbaar omzet bare DNS-naam vereist om verkeer te routeren. Azure front-deur kan niet rechtstreeks naar resources in een virtueel netwerk worden gerouteerd. U kunt een Application Gateway of een Azure Load Balancer met een openbaar IP-adres gebruiken om dit probleem op te lossen.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Wat zijn de verschillende time-outs en limieten voor de Azure front-deur?

Meer informatie over alle gedocumenteerde [time-outs en limieten voor de Azure front-deur](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits).

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Hoe lang duurt het voordat een regel van kracht wordt nadat deze is toegevoegd aan de engine voor de voor deur regels?

De configuratie van de regel engine duurt ongeveer 10 tot 15 minuten om een update te volt ooien. Zodra de update is voltooid, kunt u ervan uitgaan dat de regel van kracht wordt. 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-front-door-behind-my-azure-cdn"></a>Kan ik Azure CDN configureren achter mijn front-deur profiel of voor deur achter mijn Azure CDN?

De voor deur en het Azure CDN van Azure kunnen niet samen worden geconfigureerd, omdat beide services gebruikmaken van dezelfde Azure Edge-sites wanneer ze op aanvragen reageren. 

## <a name="performance"></a>Prestaties

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Hoe ondersteunt Azure front-deur hoge Beschik baarheid en schaal baarheid?

Azure front-deur is een wereld wijd gedistribueerd multi tenant platform met een enorme hoeveelheid capaciteit om te voldoen aan de schaal baarheids behoeften van uw toepassing. De deur van het wereld wijde netwerk van micro soft biedt wereld wijde mogelijkheden voor taak verdeling waarmee u een failover kunt uitvoeren voor uw hele toepassing of zelfs voor afzonderlijke micro Services in regio's of verschillende Clouds.

## <a name="tls-configuration"></a>TLS-configuratie

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Welke TLS-versies worden ondersteund door Azure front-deur?

Voor alle profielen voor de voorste deur die na september 2019 worden gemaakt, wordt TLS 1,2 als standaard minimum gebruikt.

De front-deur ondersteunt TLS-versies 1,0, 1,1 en 1,2. TLS 1,3 wordt nog niet ondersteund.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Welke certificaten worden ondersteund op de front-deur van Azure?

Als u het HTTPS-protocol wilt inschakelen op een aangepast domein voor de voor deur, kunt u een certificaat kiezen dat wordt beheerd door Azure front deur of uw eigen certificaat gebruiken.
Met de voor deur beheerde optie wordt een standaard TLS/SSL-certificaat ingericht via Digicert en opgeslagen in de Key Vault van de front-deur. Als u ervoor kiest uw eigen certificaat te gebruiken, kunt u een certificaat van een ondersteunde CA onboarden en een standaard TLS-, uitgebreide validatie certificaat of zelfs een certificaat met Joker tekens. Zelfondertekende certificaten worden niet ondersteund.

### <a name="does-front-door-support-autorotation-of-certificates"></a>Ondersteunt front-deur het omdraaien van certificaten?

Voor de optie beheerd certificaat voor de voor deur worden de certificaten automatisch gedraaid door de voor deur. Als u een door de voor deur beheerd certificaat gebruikt en u ziet dat de verval datum van het certificaat korter is dan 60 dagen, kunt u een ondersteunings ticket indienen.

Voor uw eigen aangepaste TLS/SSL-certificaat wordt autorotation niet ondersteund. Net als bij het instellen van de eerste keer voor een aangepast domein, moet u de voor deur naar de juiste certificaat versie in uw Key Vault wijzen. Zorg ervoor dat de service-principal voor de voor deur nog steeds toegang heeft tot de Key Vault. Deze bijgewerkte certificaat lancerings bewerking door de voor deur leidt niet tot enige productie tijd, op voor waarde dat de onderwerpnaam of het SAN voor het certificaat niet verandert.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Wat zijn de huidige coderings suites die worden ondersteund door de Azure front-deur?

Voor TLS 1.2 worden de volgende coderings suites ondersteund: 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

Voor het gebruik van aangepaste domeinen met TLS 1.0/1.1 zijn de volgende coderings suites ondersteund:

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

U kunt een minimale TLS-versie in azure front-deur configureren in de HTTPS-instellingen van het aangepaste domein met behulp van de Azure Portal of de [Azure rest API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). Op dit moment kunt u kiezen tussen 1,0 en 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Kan ik front-deur zo configureren dat alleen specifieke coderings suites worden ondersteund?

Nee, het configureren van de voor deur voor specifieke coderings suites wordt niet ondersteund. U kunt uw eigen aangepaste TLS/SSL-certificaat van uw certificerings instantie ontvangen (bijvoorbeeld VeriSign, belasters of Digicert). Vervolgens moet u tijdens het genereren specifieke coderings suites op het certificaat hebben gemarkeerd. 

### <a name="does-front-door-support-ocsp-stapling"></a>Biedt front-deur ondersteuning voor het koppelen van OCSP?

Ja, OCSP-nietfunctie wordt standaard door de voor deur ondersteund en er is geen configuratie vereist.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Ondersteunt Azure front-deur ook het opnieuw versleutelen van verkeer naar de back-end?

Ja, Azure front deur ondersteunt TLS/SSL-offload en end-to-end TLS, waarmee het verkeer opnieuw wordt versleuteld naar de back-end. Omdat de verbindingen met de back-end plaatsvinden via het open bare IP-adres, is het raadzaam om de voor deur te configureren voor het gebruik van HTTPS als het doorstuur protocol.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Ondersteunt front-deur zelfondertekende certificaten op de back-end voor HTTPS-verbinding?

Nee, zelfondertekende certificaten worden niet op de voor deur ondersteund en de beperking is van toepassing op beide:

* **Back**-ends: u kunt geen zelfondertekende certificaten gebruiken wanneer u het verkeer doorstuurt als HTTPS-of https-status controle of het invullen van de cache voor van oorsprong voor routerings regels waarvoor caching is ingeschakeld.
* Front- **End**: u kunt geen zelfondertekende certificaten gebruiken wanneer u uw eigen aangepaste TLS/SSL-certificaat gebruikt om https in te scha kelen voor uw aangepaste domein.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Waarom is HTTPS-verkeer naar mijn back-end mislukt?

Als u een geslaagde HTTPS-verbinding met de back-end wilt maken, ongeacht of de status test of aanvragen worden doorgestuurd, kunnen er twee redenen zijn waarom HTTPS-verkeer kan mislukken:

* De naam van het **certificaat onderwerp komt niet overeen**: voor HTTPS-verbindingen verwacht de voor deur dat uw back-end een certificaat van een geldige CA met de naam van de back-end (en) overeenkomt met de backend-hostnaam. Als u bijvoorbeeld de hostnaam van de back-end hebt ingesteld op `myapp-centralus.contosonews.net` en het certificaat dat uw back-end presenteert tijdens de TLS-Handshake, niet `myapp-centralus.contosonews.net` voor komt `*myapp-centralus*.contosonews.net` in de onderwerpnaam. Vervolgens wordt de verbinding door de voor deur geweigerd en resulteert dit in een fout. 
    * **Oplossing**: het wordt niet aangeraden om deze fout op te lossen, maar u kunt dit probleem omzeilen door de onderwerpnaam van het certificaat voor uw voor deur uit te scha kelen. U kunt deze optie vinden onder instellingen in Azure Portal en onder BackendPoolsSettings in de API.
* **Backend-hosting certificaat van ongeldige ca**: alleen certificaten van geldige certificerings [instanties](troubleshoot-allowed-certificate-authority.md) kunnen worden gebruikt op de back-end met de voor deur. Certificaten van interne Ca's of zelfondertekende certificaten zijn niet toegestaan.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Kan ik client/wederzijdse verificatie met Azure front-deur gebruiken?

Nee. Hoewel Azure front-deur TLS 1,2 ondersteunt, die client/wederzijdse authenticatie in [RFC 5246](https://tools.ietf.org/html/rfc5246)heeft geïntroduceerd, biedt Azure front-deur momenteel geen ondersteuning voor client/wederzijdse verificatie.

## <a name="diagnostics-and-logging"></a>Diagnostische gegevens en logboekregistratie

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Welke soorten metrische gegevens en logboeken zijn beschikbaar met Azure front-deur?

Zie metrische gegevens en logboeken voor de voor deur bewaken voor meer informatie over Logboeken en andere diagnostische mogelijkheden.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Wat is het Bewaar beleid voor de diagnostische logboeken?

Diagnostische logboeken stroomt naar het opslag account van klanten en klanten kunnen het Bewaar beleid instellen op basis van hun voor keur. Diagnostische logboeken kunnen ook worden verzonden naar een event hub of Azure Monitor-Logboeken. Zie [logboek registratie voor Azure front-deur](how-to-logs.md)voor meer informatie.

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Hoe kan ik audit logboeken voor Azure front-deur ophalen?

Er zijn audit logboeken beschikbaar voor de front-deur van Azure. Selecteer in de portal het **activiteiten logboek** op de menu pagina van de voor deur om toegang te krijgen tot het audit logboek. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Kan ik waarschuwingen instellen met behulp van de voor deur van Azure?

Ja, de voor deur van Azure biedt ondersteuning voor waarschuwingen. Waarschuwingen worden geconfigureerd voor metrische gegevens. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een voor deur standaard/Premium](create-front-door-portal.md).
