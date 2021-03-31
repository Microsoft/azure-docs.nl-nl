---
title: End-to-end TLS inschakelen op Azure-toepassing gateway
description: Dit artikel bevat een overzicht van de Application Gateway end-to-end TLS-ondersteuning.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: victorh
ms.openlocfilehash: c39401289ffc6f27c292168adaa15c5163a3967b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001283"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Overzicht van TLS-beëindiging en end-to-end TLS met Application Gateway

Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL), is de standaard beveiligings technologie voor het tot stand brengen van een versleutelde koppeling tussen een webserver en een browser. Deze koppeling zorgt ervoor dat alle gegevens die tussen de webserver en de browsers worden door gegeven, privé en versleuteld blijven. Application Gateway ondersteunt zowel TLS-beëindiging bij de gateway als end-to-end TLS-versleuteling.

## <a name="tls-termination"></a>TLS-beëindiging

Application Gateway ondersteunt TLS-beëindiging op de gateway, waarna verkeer doorgaans niet-versleuteld naar de back-endservers stromen. Er zijn een aantal voor delen van het uitvoeren van TLS-beëindiging op de Application Gateway:

- **Verbeterde prestaties** : de grootste prestaties bij het uitvoeren van TLS-ontsleuteling is de initiële handshake. Om de prestaties te verbeteren, voert de server die de ontsleuteling uitvoert TLS-sessie-Id's uit en beheert TLS-sessie tickets. Als dit gebeurt in de toepassings gateway, kunnen alle aanvragen van dezelfde client de in de cache opgeslagen waarden gebruiken. Als de server op de back-endservers wordt uitgevoerd, wordt de client telkens wanneer de aanvragen van de client naar een andere servers gaan, opnieuw geverifieerd. Het gebruik van TLS-tickets kan helpen om dit probleem te verhelpen, maar ze worden niet door alle clients ondersteund en kunnen lastig zijn om te configureren en te beheren.
- **Verbeterd gebruik van de back-endservers** – SSL/TLS-verwerking is zeer CPU-intensief, en wordt veel meer intensief naarmate de sleutel grootten toenemen. Door dit werk van de back-endservers te verwijderen, kunnen ze zich richten op wat ze het meest efficiënt zijn om inhoud te leveren.
- **Intelligente route ring** : door het verkeer te ontsleutelen, heeft de Application Gateway toegang tot de aanvraag inhoud, zoals HEADERS, URI, enzovoort, en kan deze gegevens gebruiken om aanvragen te routeren.
- **Certificaat beheer** : certificaten hoeven alleen te worden aangeschaft en geïnstalleerd op de toepassings gateway en niet op alle back-endservers. Dit bespaart tijd en geld.

Als u TLS-beëindiging wilt configureren, moet er een TLS/SSL-certificaat aan de listener worden toegevoegd om de Application Gateway in te scha kelen voor het afleiden van een symmetrische sleutel conform TLS/SSL-protocol specificatie. De symmetrische sleutel wordt vervolgens gebruikt voor het versleutelen en ontsleutelen van het verkeer dat naar de gateway wordt verzonden. Het TLS/SSL-certificaat moet de PFX-indeling (Personal Information Exchange) hebben. Met deze bestands indeling kunt u de persoonlijke sleutel exporteren die is vereist voor de toepassings gateway om de versleuteling en ontsleuteling van verkeer uit te voeren.

> [!IMPORTANT] 
> Voor het certificaat op de listener moet de volledige certificaat keten worden geüpload (het basis certificaat van de certificerings instantie, de tussenliggende en het blad certificaat) om de vertrouwens keten in te richten. 


> [!NOTE] 
>
> Application Gateway biedt geen mogelijkheid voor het maken van een nieuw certificaat of het verzenden van een certificaat aanvraag naar een certificerings instantie.

Als u de TLS-verbinding wilt gebruiken, moet u ervoor zorgen dat het TLS/SSL-certificaat voldoet aan de volgende voor waarden:

- Of de huidige datum en tijd binnen het datum bereik geldig van en geldig tot zijn voor het certificaat.
- De algemene naam (CN) van het certificaat komt overeen met de host-header in de aanvraag. Als de client bijvoorbeeld een aanvraag indient bij `https://www.contoso.com/`, moet de algemene naam `www.contoso.com` zijn.

### <a name="certificates-supported-for-tls-termination"></a>Certificaten die worden ondersteund voor het beëindigen van TLS

Application Gateway ondersteunt de volgende typen certificaten:

- CA-certificaat (certificerings instantie): een CA-certificaat is een digitaal certificaat dat is uitgegeven door een certificerings instantie (CA)
- EV-certificaat (Extended validatie): een EV-certificaat is een certificaat dat voldoet aan de richt lijnen voor de normen van het gestandaardiseerde certificaat. Hiermee wordt de browser Locator-balk groen en wordt de bedrijfs naam ook gepubliceerd.
- Joker certificaat: dit certificaat ondersteunt een wille keurig aantal subdomeinen op basis van *. site.com, waarbij uw subdomein de * vervangen. Het biedt echter geen ondersteuning voor site.com, dus als de gebruikers toegang hebben tot uw website zonder de toonaangevende ' www ' te hoeven typen, heeft het Joker certificaat hier geen betrekking op.
- Self-Signed certificaten: client browsers vertrouwen deze certificaten niet en de gebruiker wordt gewaarschuwd dat het certificaat van de virtuele service geen deel uitmaakt van een vertrouwens keten. Zelfondertekende certificaten zijn geschikt voor testen of omgevingen waarin beheerders de clients beheren en de beveiligings waarschuwingen van de browser veilig kunnen passeren. Werk belastingen voor productie moeten nooit zelfondertekende certificaten gebruiken.

Zie [Configure TLS Termination with Application Gateway](./create-ssl-portal.md)(Engelstalig) voor meer informatie.

### <a name="size-of-the-certificate"></a>Grootte van het certificaat
Controleer de sectie [limieten van Application Gateway](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits) om de maximale grootte van TLS/SSL-certificaten te weten die wordt ondersteund.

## <a name="end-to-end-tls-encryption"></a>End-to-end TLS-versleuteling

Mogelijk wilt u niet-versleutelde communicatie met de back-endservers. Mogelijk hebt u beveiligings vereisten, nalevings vereisten of kan de toepassing alleen een beveiligde verbinding accepteren. Azure-toepassing gateway een end-to-end TLS-versleuteling heeft om deze vereisten te ondersteunen.

Met end-to-end TLS kunt u gevoelige gegevens versleutelen en veilig verzenden naar de back-end terwijl u gebruikmaakt van Application Gateway de functies voor taak verdeling van laag 7. Deze functies omvatten sessie affiniteit op basis van cookies, route ring op basis van URL, ondersteuning voor route ring op basis van sites, de mogelijkheid om X-doorgestuurde-*-headers opnieuw te schrijven of te injecteren.

Wanneer het is geconfigureerd met een end-to-end TLS-communicatie modus, worden de TLS-sessies op de gateway door Application Gateway beëindigd en wordt het gebruikers verkeer ontsleuteld. Vervolgens worden de geconfigureerde regels toegepast voor het selecteren van het juiste exemplaar van de back-endgroep waarnaar het verkeer moet worden doorgeleid. Application Gateway initieert vervolgens een nieuwe TLS-verbinding met de back-endserver en versleutelt de gegevens opnieuw met het open bare-sleutel certificaat van de back-endserver voordat de aanvraag naar de back-end wordt verzonden. Reacties van de webserver ondergaan hetzelfde proces terug naar de eindgebruiker. End-to-end TLS wordt ingeschakeld door de protocol instelling in de [back-end-HTTP-instelling](./configuration-overview.md#http-settings) in te stellen op https, die vervolgens wordt toegepast op een back-end-groep.

Voor de SKU Application Gateway en WAF v1 is het TLS-beleid van toepassing op zowel frontend-als back-end-verkeer. Op de front-end Application Gateway fungeren als de server en wordt het beleid afgedwongen. Op de backend fungeert Application Gateway als de client en verzendt de protocol/cipher-informatie als voor keur tijdens de TLS-handshake.

Voor de SKU Application Gateway en WAF v2 is het TLS-beleid alleen van toepassing op het front-end verkeer en worden alle versleuteling aangeboden aan de back-end-server, die controle heeft over het selecteren van specifieke code ringen en TLS-versie tijdens de handshake.

Application Gateway communiceert alleen met die back-endservers die toestaan dat hun certificaat is toegestaan met de Application Gateway of waarvan de certificaten zijn ondertekend met bekende CA-instanties en de CN van het certificaat overeenkomt met de hostnaam in de HTTP-back-end-instellingen. Dit zijn onder andere de vertrouwde Azure-Services, zoals Azure App Service/Web Apps en Azure API Management.

Als de certificaten van de leden in de back-end-groep niet zijn ondertekend met bekende CA-instanties, moet elk exemplaar in de back-end-groep met end-to-end TLS ingeschakeld worden geconfigureerd met een certificaat om beveiligde communicatie toe te staan. Door het certificaat toe te voegen, zorgt u ervoor dat de Application Gateway alleen communiceert met bekende back-end-exemplaren. Hiermee wordt de end-to-end-communicatie verder beveiligd.

> [!NOTE] 
>
> Het certificaat dat is toegevoegd aan de **back-end-HTTP-instelling** voor authenticatie van de back-end-servers, kan hetzelfde zijn als het certificaat dat is toegevoegd aan de **listener** voor TLS-beëindiging op Application Gateway of op een andere manier voor verbeterde beveiliging.

![end-to-end TLS-scenario][1]

In dit voor beeld worden aanvragen die gebruikmaken van TLS 1.2 gerouteerd naar back-endservers in Pool1 met end-to-end TLS.

## <a name="end-to-end-tls-and-allow-listing-of-certificates"></a>End-to-end-TLS en toestaan dat certificaten worden vermeld

Application Gateway communiceert alleen met bekende back-end-instanties waarvoor het certificaat met de toepassings gateway is toegestaan. Er zijn enkele verschillen in het end-to-end TLS-installatie proces ten opzichte van de versie van Application Gateway gebruikt. In de volgende sectie worden deze afzonderlijk uitgelegd.

## <a name="end-to-end-tls-with-the-v1-sku"></a>End-to-end TLS met de V1-SKU

Als u end-to-end TLS wilt inschakelen met de back-endservers en voor Application Gateway om aanvragen naar hen te routeren, moeten de status controles slagen en een gezonde reactie retour neren.

Voor HTTPS-status tests gebruikt de Application Gateway v1-SKU een exacte overeenkomst van het verificatie certificaat (open bare sleutel van het back-end-server certificaat en niet het basis certificaat) dat moet worden geüpload naar de HTTP-instellingen.

Er zijn alleen verbindingen met bekende en toegestane back-endservers toegestaan. De resterende back-ends worden beschouwd als slecht door de status controles. Zelfondertekende certificaten zijn uitsluitend bedoeld voor testdoeleinden en het wordt afgeraden om deze in een productieomgeving te gebruiken. Dergelijke certificaten moeten worden vermeld bij de toepassings gateway, zoals beschreven in de voor gaande stappen voordat ze kunnen worden gebruikt.

> [!NOTE]
> De instellingen voor verificatie en vertrouwd basis certificaat zijn niet vereist voor vertrouwde Azure-Services, zoals Azure App Service. Deze worden standaard als vertrouwd beschouwd.

## <a name="end-to-end-tls-with-the-v2-sku"></a>End-to-end TLS met de v2-SKU

Verificatie certificaten zijn afgeschaft en vervangen door vertrouwde basis certificaten in de Application Gateway v2-SKU. Ze werken op dezelfde manier als verificatie certificaten met enkele belang rijke verschillen:

- Voor certificaten die zijn ondertekend door bekende CA-instanties waarvan de naam overeenkomt met de hostnaam in de HTTP-back-end-instellingen, is geen extra stap vereist voor end-to-end-TLS. 

   Als de back-end-certificaten bijvoorbeeld zijn uitgegeven door een bekende CA en een CN van contoso.com hebben en het veld host van de back-end-http-instelling ook is ingesteld op contoso.com, zijn er geen aanvullende stappen vereist. U kunt het http-instellings Protocol van de back-end instellen op HTTPS en zowel voor de status test als voor het gegevenspad is TLS ingeschakeld. Als u Azure App Service of andere Azure-webservices als uw back-end gebruikt, worden deze impliciet vertrouwd en zijn er geen verdere stappen vereist voor end-to-end-TLS.
   
> [!NOTE] 
>
> Als een TLS/SSL-certificaat wordt vertrouwd, moet dat certificaat van de back-endserver zijn uitgegeven door een certificerings instantie die bekend is. Als het certificaat niet is uitgegeven door een vertrouwde certificerings instantie, de toepassings gateway controleert vervolgens of het certificaat van de uitgevende CA is uitgegeven door een vertrouwde certificerings instantie, zodat er een vertrouwde CERTIFICERINGs instantie wordt gevonden (op welk moment een vertrouwde, beveiligde verbinding wordt ingesteld) of er geen vertrouwde certificerings instantie kan worden gevonden (op welk punt bevindt de toepassings gateway de backend slecht). Daarom wordt aanbevolen dat het back-endserver certificaat zowel de basis-Ca's als de tussenliggende certificerings instantie bevat.

- Als het certificaat zelfondertekend is of door onbekende tussen personen is ondertekend, moet er een vertrouwd basis certificaat worden gedefinieerd om end-to-end TLS in de v2-SKU in te scha kelen. Application Gateway communiceert alleen met back-ends waarvan het basis certificaat van het server certificaat overeenkomt met een van de lijst met vertrouwde basis certificaten in de back-end-http-instelling die aan de groep is gekoppeld.

- Naast het basis certificaat komt met Application Gateway v2 ook te controleren of de host-instelling die is opgegeven in de back-end-http-instelling overeenkomt met die van de algemene naam (CN) die wordt gepresenteerd door het TLS/SSL-certificaat van de back-endserver. Wanneer u probeert een TLS-verbinding met de back-end tot stand te brengen, stelt Application Gateway v2 de uitbrei ding Servernaamindicatie (SNI) in op de host die is opgegeven in de back-end-http-instelling.

- Als de optie **hostnaam van back-end kiezen** wordt gekozen in plaats van het veld host in de back-end-http-instelling, wordt de SNI-header altijd ingesteld op de FQDN-naam van de back-end en de CN op het TLS/SSL-certificaat van de back-endserver moet overeenkomen met de FQDN. Back-end-groeps leden met IP-adressen worden niet ondersteund in dit scenario.

- Het basis certificaat is een base64-gecodeerd basis certificaat van de back-end-server certificaten.

## <a name="sni-differences-in-the-v1-and-v2-sku"></a>SNI-verschillen in de v1-en v2-SKU

Zoals eerder vermeld, wordt door Application Gateway het TLS-verkeer van de client bij de Application Gateway listener (de frontend-verbinding) te decoderen, het verkeer ontsleuteld, worden de benodigde regels toegepast om te bepalen welke back-endserver moet worden doorgestuurd en wordt er een nieuwe TLS-sessie met de back-end-server tot stand gebracht (de back-end-verbinding wordt aangeroepen).

In de volgende tabellen worden de verschillen in SNI tussen de v1-en v2-SKU in termen van frontend-en back-end-verbindingen beschreven.

### <a name="frontend-tls-connection-client-to-application-gateway"></a>Front-end TLS-verbinding (client naar toepassings gateway)

---
Scenario | v1 | v2 |
| --- | --- | --- |
| Als de client de SNI-header bevat en alle listeners voor meerdere locaties zijn ingeschakeld met de vlag ' vereisen SNI ' | Het juiste certificaat retour neren en als de site niet bestaat (volgens de server_name), wordt de verbinding opnieuw ingesteld. | Hiermee wordt het juiste certificaat geretourneerd, indien beschikbaar, anders retourneert het certificaat van de eerste HTTPS-listener die is geconfigureerd (in de volg orde)|
| Als de client geen SNI-header opgeeft en alle headers met meerdere sites zijn ingeschakeld met ' vereisen SNI ' | De verbinding opnieuw instellen | Retourneert het certificaat van de eerste HTTPS-listener die is geconfigureerd (in de volg orde)
| Als de client geen SNI-header opgeeft en als er een basis-listener is geconfigureerd met een certificaat | Retourneert het certificaat dat is geconfigureerd in de Basic-listener voor de client (standaard-of terugval certificaat) | Retourneert het certificaat van de eerste HTTPS-listener die is geconfigureerd (in de volg orde) |

### <a name="backend-tls-connection-application-gateway-to-the-backend-server"></a>Back-end-TLS-verbinding (Application Gateway naar de back-end-server)

#### <a name="for-probe-traffic"></a>Voor test verkeer

---
Scenario | v1 | v2 |
| --- | --- | --- |
| SNI-header (server_name) tijdens de TLS-Handshake als FQDN | Stel in als FQDN-adres van de back-endserver. Volgens [RFC 6066](https://tools.ietf.org/html/rfc6066)zijn letterlijke IPv4-en IPv6-adressen niet toegestaan in de SNI-hostnaam. <br> **Opmerking:** FQDN-naam van de back-endserver moet worden omgezet naar het IP-adres van de back-endserver (openbaar of privé) | De SNI-header (server_name) wordt ingesteld als de hostnaam van de aangepaste test die is gekoppeld aan de HTTP-instellingen (indien geconfigureerd), anders dan de hostnaam die wordt vermeld in de HTTP-instellingen, anders dan de FQDN die is vermeld in de back-end-groep. De volg orde van prioriteit is aangepaste test > HTTP-instellingen > back-end-pool. <br> **Opmerking:** Als de hostnamen die zijn geconfigureerd in HTTP-instellingen en aangepaste test verschillend zijn, wordt SNI ingesteld als de hostnaam van de aangepaste test.
| Als het adres van de back-endadresgroep een IP-adres is (v1) of als de hostnaam van de aangepaste test is geconfigureerd als IP-adres (v2) | SNI (server_name) wordt niet ingesteld. <br> **Opmerking:** In dit geval moet de back-endserver een standaard-terugval certificaat kunnen retour neren. dit moet staan vermeld in HTTP-instellingen onder verificatie certificaat. Als er geen standaard-/terugval certificaat is geconfigureerd op de back-endserver en SNI wordt verwacht, wordt de verbinding mogelijk opnieuw ingesteld door de server en worden er fouten in de test optreden | In de volg orde van prioriteit die eerder is vermeld, als ze een IP-adres hebben als hostname, wordt SNI niet ingesteld op per [RFC 6066](https://tools.ietf.org/html/rfc6066). <br> **Opmerking:** SNI wordt ook niet in v2-tests ingesteld als er geen aangepaste test is geconfigureerd en er geen hostnaam is ingesteld voor HTTP-instellingen of back-end-pool |

> [!NOTE] 
> Als er geen aangepaste test is geconfigureerd, stuurt Application Gateway een standaard test in deze indeling- \<protocol\> ://127.0.0.1: \<port\> /. Voor een standaard HTTPS-test wordt deze bijvoorbeeld verzonden als https://127.0.0.1:443/ . Houd er rekening mee dat de hier genoemde 127.0.0.1 alleen wordt gebruikt als de header van de HTTP-host en volgens RFC 6066 niet wordt gebruikt als SNI-header. Raadpleeg voor meer informatie over fouten met betrekking tot de status testen de [hand leiding back-end](application-gateway-backend-health-troubleshooting.md)voor het oplossen van problemen.

#### <a name="for-live-traffic"></a>Voor live verkeer

---
Scenario | v1 | v2 |
| --- | --- | --- |
| SNI-header (server_name) tijdens de TLS-Handshake als FQDN | Stel in als FQDN-adres van de back-endserver. Volgens [RFC 6066](https://tools.ietf.org/html/rfc6066)zijn letterlijke IPv4-en IPv6-adressen niet toegestaan in de SNI-hostnaam. <br> **Opmerking:** FQDN-naam van de back-endserver moet worden omgezet naar het IP-adres van de back-endserver (openbaar of privé) | De SNI-header (server_name) wordt ingesteld als de hostnaam van de HTTP-instellingen, anders als de optie *PickHostnameFromBackendAddress* is gekozen of als er geen hostnaam wordt vermeld, wordt deze ingesteld als de FQDN in de configuratie van de back-endserver
| Als het adres van de back-endadresgroep een IP-adres of hostnaam is niet ingesteld in HTTP-instellingen | SNI wordt niet ingesteld volgens [RFC 6066](https://tools.ietf.org/html/rfc6066) als de vermelding in de back-ENDADRESGROEP geen FQDN-naam is | SNI wordt ingesteld als de hostnaam van de ingevoerde FQDN van de client en de CN van het back-end-certificaat moet overeenkomen met deze hostnaam.

## <a name="next-steps"></a>Volgende stappen

Nadat u hebt leren over end-to-end TLS, gaat u naar [end-to-end TLS configureren met behulp van Application Gateway met Power shell](application-gateway-end-to-end-ssl-powershell.md) om een toepassings gateway te maken met end-to-end TLS.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png