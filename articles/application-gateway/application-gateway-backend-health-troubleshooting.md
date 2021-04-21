---
title: Problemen met de back-Azure Application Gateway
description: Beschrijft hoe u problemen met de back-Azure Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: 8664f9327af37345c7104c65b2521212669ae806
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786322"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Problemen met de back-Application Gateway
==================================================

<a name="overview"></a>Overzicht
--------

Standaard test Azure Application Gateway back-endservers om hun status te controleren en om te controleren of ze gereed zijn om aanvragen te verwerken. Gebruikers kunnen ook aangepaste tests maken om de hostnaam, het pad dat moet worden onderzocht en de statuscodes te vermelden die moeten worden geaccepteerd als In orde. Als de back-endserver in elk geval niet reageert, Application Gateway de server als Beschadigd en stopt het doorsturen van aanvragen naar de server. Nadat de server met succes reageert, Application Gateway het doorsturen van de aanvragen hervat.

### <a name="how-to-check-backend-health"></a>Back-end-status controleren

Als u de status van uw back-endpool wilt controleren, kunt u de **pagina Back-Azure Portal.** U kunt ook [Azure PowerShell,](/powershell/module/az.network/get-azapplicationgatewaybackendhealth) [CLI](/cli/azure/network/application-gateway#az_network_application_gateway_show_backend_health)of [REST API.](/rest/api/application-gateway/applicationgateways/backendhealth)

De status die door een van deze methoden wordt opgehaald, kan een van de volgende zijn:

- In orde

- Niet in orde

- Onbekend

Als de status van de back-end voor een server In orde is, betekent dit dat Application Gateway aanvragen naar die server doorsturen. Maar als de back-end-status voor alle servers in een back-endpool niet in orde is, kunnen er problemen ontstaan wanneer u toepassingen probeert te openen. In dit artikel worden de symptomen, de oorzaak en de oplossing beschreven voor elk van de weergegeven fouten.

<a name="backend-health-status-unhealthy"></a>Status van back-end: Niet in orde
-------------------------------

Als de status van de back-end niet in orde is, lijkt de portalweergave op de volgende schermopname:

![Application Gateway back-Application Gateway - Niet in orde](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Als u een Azure PowerShell-, CLI- of Azure REST API-query gebruikt, krijgt u een antwoord dat er als volgt uit ziet:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
Nadat u de status van de back-endserver met een slechte status hebt ontvangen voor alle servers in een back-endpool, worden aanvragen niet doorgestuurd naar de servers en retourneert Application Gateway de fout '502 Bad Gateway' naar de aanvragende client. Als u dit probleem wilt oplossen, controleert u **de kolom Details** op het tabblad **Back-end-status.**

Het bericht dat wordt weergegeven in de **kolom Details** biedt meer gedetailleerde inzichten over het probleem. Op basis van deze informatie kunt u beginnen met het oplossen van het probleem.

> [!NOTE]
> De standaardtestaanvraag wordt verzonden in de indeling \<protocol\> ://127.0.0.1: \<port\> /. Bijvoorbeeld voor http://127.0.0.1:80 een HTTP-test op poort 80. Alleen HTTP-statuscodes van 200 tot en met 399 worden als in orde beschouwd. Het protocol en de doelpoort worden overgenomen van de HTTP-instellingen. Als u wilt dat Application Gateway een ander protocol, hostnaam of pad test en een andere statuscode als In orde herkent, configureert u een aangepaste test en koppelt u deze aan de HTTP-instellingen.

<a name="error-messages"></a>Foutberichten
------------------------
#### <a name="backend-server-timeout"></a>Time-out van back-en-mailserver

**Bericht:** De tijd die de back-end nodig heeft om te reageren op de statustest van de toepassingsgateway, is meer dan de \' time-outdrempelwaarde in de testinstelling.

**Oorzaak:** Nadat Application Gateway een HTTP(S)-testaanvraag naar de back-mailserver hebt gestuurd, wacht deze op een reactie van de back-endserver voor een geconfigureerde periode. Als de back-endserver niet binnen de geconfigureerde periode reageert (de time-outwaarde), wordt deze gemarkeerd als Beschadigd totdat deze opnieuw binnen de geconfigureerde time-outperiode reageert.

**Oplossing:** Controleer waarom de back-endserver of -toepassing niet reageert binnen de geconfigureerde time-outperiode en controleer ook de toepassingsafhankelijkheden. Controleer bijvoorbeeld of de database problemen heeft die een vertraging in reactie kunnen veroorzaken. Als u op de hoogte bent van het gedrag van de toepassing en deze pas na de time-outwaarde moet reageren, verhoogt u de time-outwaarde van de aangepaste testinstellingen. U moet een aangepaste test hebben om de time-outwaarde te wijzigen. Zie de documentatiepagina voor meer informatie over het configureren [van een aangepaste test.](./application-gateway-create-probe-portal.md)

Volg deze stappen om de time-outwaarde te verhogen:

1.  Ga rechtstreeks naar de back-endserver en controleer de tijd die de server nodig heeft om op die pagina te reageren. U kunt elk hulpprogramma gebruiken om toegang te krijgen tot de back-endserver, met inbegrip van een browser met ontwikkelhulpprogramma's.

1.  Nadat u de tijd hebt ingesteld die de toepassing nodig heeft om te reageren, selecteert u het tabblad **Statustests** en selecteert u vervolgens de test die is gekoppeld aan uw HTTP-instellingen.

1.  Voer in seconden een time-outwaarde in die groter is dan de reactietijd van de toepassing.

1.  Sla de aangepaste testinstellingen op en controleer of de back-end-status nu in orde is.

#### <a name="dns-resolution-error"></a>DNS-resolutiefout

**Bericht:** Application Gateway kan geen test voor deze back-end maken. Dit gebeurt meestal wanneer de FQDN van de back-end niet correct is ingevoerd. 

**Oorzaak:** Als de back-endpool van het type IP-adres/FQDN of App Service is, wordt Application Gateway omgegaan met het IP-adres van de FQDN die is ingevoerd via Domain Name System (DNS) (aangepast of standaard in Azure) en wordt geprobeerd verbinding te maken met de server op de TCP-poort die wordt vermeld in de HTTP-instellingen. Maar als dit bericht wordt weergegeven, wordt ervan uitgegaan dat Application Gateway ip-adres van de ingevoerde FQDN niet kan worden opgelost.

**Oplossing:**

1.  Controleer of de FQDN die is ingevoerd in de back-endpool juist is en of het een openbaar domein is en probeer het vervolgens op te lossen vanaf uw lokale computer.

1.  Als u het IP-adres kunt oplossen, is er mogelijk iets mis met de DNS-configuratie in het virtuele netwerk.

1.  Controleer of het virtuele netwerk is geconfigureerd met een aangepaste DNS-server. Als dat het zo is, controleert u de DNS-server waarom deze niet kan worden opgelost met het IP-adres van de opgegeven FQDN.

1.  Als u standaard-DNS van Azure gebruikt, moet u contact opnemen met uw domeinnaamregistrar om na te gaan of de juiste A-record- of CNAME-recordtoewijzing is voltooid.

1.  Als het domein privé of intern is, probeert u het op te lossen vanaf een virtuele machine in hetzelfde virtuele netwerk. Als u dit kunt oplossen, start u de Application Gateway en controleert u het opnieuw. Als u Application Gateway opnieuw wilt [](/powershell/module/azurerm.network/stop-azurermapplicationgateway) starten, moet u stoppen en [beginnen](/powershell/module/azurerm.network/start-azurermapplicationgateway) met behulp van de PowerShell-opdrachten die worden beschreven in deze gekoppelde resources.

#### <a name="tcp-connect-error"></a>TCP-verbindingsfout

**Bericht:** Application Gateway kan geen verbinding maken met de back-end.
Controleer of de back-end reageert op de poort die voor de test wordt gebruikt.
Controleer ook of een NSG/UDR/Firewall de toegang tot het IP-adres en de poort van deze back-end blokkeert

**Oorzaak:** Na de DNS-resolutiefase probeert Application Gateway verbinding te maken met de back-endserver op de TCP-poort die is geconfigureerd in de HTTP-instellingen. Als Application Gateway geen TCP-sessie tot stand kan brengen op de opgegeven poort, wordt de test gemarkeerd als Beschadigd met dit bericht.

**Oplossing:** Als deze fout wordt weergegeven, volgt u deze stappen:

1.  Controleer met behulp van een browser of PowerShell of u verbinding kunt maken met de back-endserver op de poort die wordt vermeld in de HTTP-instellingen. Voer bijvoorbeeld de volgende opdracht uit: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Als de vermelde poort niet de gewenste poort is, voert u het juiste poortnummer in voor Application Gateway verbinding te maken met de back-endserver

1.  Als u ook vanaf uw lokale computer geen verbinding kunt maken op de poort, gaat u als volgende te werk:

    a.  Controleer de instellingen van de netwerkbeveiligingsgroep (NSG) van de netwerkadapter en het subnet van de back-mailserver en of binnenkomende verbindingen met de geconfigureerde poort zijn toegestaan. Als dat niet zo is, maakt u een nieuwe regel om de verbindingen toe te staan. Zie de documentatiepagina voor meer informatie over het maken [van NSG-regels.](../virtual-network/tutorial-filter-network-traffic.md#create-security-rules)

    b.  Controleer of de NSG-instellingen van het Application Gateway subnet uitgaand openbaar en privéverkeer toestaan, zodat er een verbinding kan worden gemaakt. Controleer de documentpagina die is opgegeven in stap 3a voor meer informatie over het maken van NSG-regels.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Controleer de door de gebruiker gedefinieerde routes (UDR)-instellingen van Application Gateway en het subnet van de back-eindgebruikerserver op eventuele routeringsafwijkingen. Zorg ervoor dat de UDR het verkeer niet weg leidt van het back-endsubnet. Controleer bijvoorbeeld op routes naar virtuele netwerkapparaten of standaardroutes die worden geadverteerd aan het Application Gateway subnet via Azure ExpressRoute en/of VPN.

    d.  Als u de effectieve routes en regels voor een netwerkadapter wilt controleren, kunt u de volgende PowerShell-opdrachten gebruiken:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Als u geen problemen met NSG of UDR vindt, controleert u de back-endserver op toepassingsproblemen die verhinderen dat clients een TCP-sessie tot stand brengen op de geconfigureerde poorten. Een aantal dingen die u moet controleren:

    a.  Open een opdrachtprompt (Win+R - \> cmd), voer `netstat` in en selecteer Enter.

    b.  Controleer of de server luistert op de poort die is geconfigureerd. Bijvoorbeeld:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Als deze niet luistert op de geconfigureerde poort, controleert u de instellingen van uw webserver. Bijvoorbeeld: sitebindingen in IIS, serverblok in NGINX en virtuele host in Apache.

    d.  Controleer de firewallinstellingen van uw besturingssysteem om te controleren of binnenkomend verkeer naar de poort is toegestaan.

#### <a name="http-status-code-mismatch"></a>HTTP-statuscode komt niet overeen

**Bericht:** De statuscode van het \' HTTP-antwoord van de back-end komt niet overeen met de testinstelling. Expected:{HTTPStatusCode0} Received:{HTTPStatusCode1}.

**Oorzaak:** Nadat de TCP-verbinding tot stand is gebracht en er een TLS-handshake is uitgevoerd (als TLS is ingeschakeld), verzendt Application Gateway de test als een HTTP GET-aanvraag naar de back-endserver. Zoals eerder beschreven, wordt de standaardtest ingesteld op \<protocol\> ://127.0.0.1: /, en worden de responsstatuscodes in de \<port\> tussen 200 en 399 als in orde gezien. Als de server andere statuscodes retourneert, wordt deze gemarkeerd als Niet in orde met dit bericht.

**Oplossing:** Afhankelijk van de antwoordcode van de back-endserver kunt u de volgende stappen uitvoeren. Hier worden enkele algemene statuscodes vermeld:

| **Fout** | **Acties** |
| --- | --- |
| Teststatuscode komt niet overeen: 401 ontvangen | Controleer of verificatie is vereist voor de back-endserver. Application Gateway-tests kunnen geen referenties doorgeven voor verificatie. Sta \" HTTP 401 toe in een teststatuscode of test naar een pad waar de \" server geen verificatie vereist. |
| Teststatuscode komt niet overeen: 403 ontvangen | Toegang verboden. Controleer of toegang tot het pad is toegestaan op de back-endserver. |
| Teststatuscode komt niet overeen: 404 ontvangen | De pagina is niet gevonden. Controleer of het pad naar de hostnaam toegankelijk is op de back-endserver. Wijzig de hostnaam of padparameter in een toegankelijke waarde. |
| Teststatuscode komt niet overeen: 405 ontvangen | De testaanvragen voor Application Gateway de HTTP GET-methode gebruiken. Controleer of uw server deze methode toestaat. |
| Teststatuscode komt niet overeen: 500 ontvangen | Interne serverfout. Controleer de status van de back-endserver en of de services worden uitgevoerd. |
| Teststatuscode komt niet overeen: 503 ontvangen | Service niet beschikbaar. Controleer de status van de back-endserver en of de services worden uitgevoerd. |

Of als u denkt dat het antwoord legitiem is en u wilt dat Application Gateway statuscodes accepteert als In orde, kunt u een aangepaste test maken. Deze aanpak is handig in situaties waarin verificatie op de back-endwebsite is vereist. Omdat de testaanvragen geen gebruikersreferenties hebben, mislukken ze en wordt er een HTTP 401-statuscode geretourneerd door de back-endserver.

Volg deze stappen om een aangepaste test [te maken.](./application-gateway-create-probe-portal.md)

#### <a name="http-response-body-mismatch"></a>HTTP-antwoord body komt niet overeen

**Bericht:** De body van het \' HTTP-antwoord van de back-end komt niet overeen met de testinstelling. De ontvangen antwoord-body bevat geen {tekenreeks}.

**Oorzaak:** Wanneer u een aangepaste test maakt, hebt u een optie om een back-endserver te markeren als In orde door een tekenreeks uit de antwoord body te koppelen. U kunt bijvoorbeeld configureren dat Application Gateway 'niet-geautoriseerd' accepteert als een tekenreeks die moet overeenkomen. Als het antwoord van de back-endserver voor de testaanvraag de tekenreeks **niet** geautoriseerd bevat, wordt deze gemarkeerd als In orde. Anders wordt dit bericht gemarkeerd als Niet in orde.

**Oplossing:** Volg deze stappen om dit probleem op te lossen:

1.  Toegang tot de back-endserver lokaal of vanaf een clientmachine op het testpad en controleer de antwoord body.

1.  Controleer of de antwoord body in Application Gateway configuratie van de aangepaste test overeenkomt met wat er is geconfigureerd.

1.  Als ze niet overeenkomen, wijzigt u de testconfiguratie zodat deze de juiste tekenreekswaarde heeft om te accepteren.

Meer informatie over Application Gateway [test die overeenkomt](./application-gateway-probe-overview.md#probe-matching)met .

>[!NOTE]
> Voor alle TLS-gerelateerde foutberichten raadpleegt u de [overzichtspagina van TLS](ssl-overview.md) voor meer informatie over SNI-gedrag en verschillen tussen de v1- en v2-SKU.


#### <a name="backend-server-certificate-invalid-ca"></a>Ongeldige CA voor back-endservercertificaat

**Bericht:** Het servercertificaat dat door de back-end wordt gebruikt, is niet ondertekend door een bekende certificeringsinstantie (CA). Sta de back-Application Gateway toe door het basiscertificaat te uploaden van het servercertificaat dat door de back-end wordt gebruikt.

**Oorzaak:** End-to-end SSL met Application Gateway v2 vereist dat het certificaat van de back-endserver wordt geverifieerd om de server in orde te achten.
Om een TLS/SSL-certificaat te kunnen vertrouwen, moet dat certificaat van de back-endserver worden uitgegeven door een CERTIFICERINGsinstantie die is opgenomen in het vertrouwde Application Gateway. Als het certificaat niet is uitgegeven door een vertrouwde CERTIFICERINGsinstantie (bijvoorbeeld als er een zelf-ondertekend certificaat is gebruikt), moeten gebruikers het certificaat van de vergever uploaden naar Application Gateway.

**Oplossing:** Volg deze stappen om het vertrouwde basiscertificaat te exporteren en te uploaden naar Application Gateway. (Deze stappen zijn voor Windows-clients.)

1.  Meld u aan bij de computer waarop uw toepassing wordt gehost.

1.  Selecteer Win+R of klik met de rechtermuisknop op **de knop Start** en selecteer vervolgens **Uitvoeren.**

1.  Voer `certmgr.msc` in en selecteer Enter. U kunt ook zoeken naar Certificaatbeheer in het **menu** Start.

1.  Zoek het certificaat, meestal in `\Certificates - Current User\\Personal\\Certificates\` , en open het.

1.  Selecteer het basiscertificaat en selecteer vervolgens **Certificaat weergeven.**

1.  Selecteer in de certificaateigenschappen het **tabblad Details.**

1.  Selecteer op **het** tabblad Details de optie **Kopiëren** naar bestand en sla het bestand op in de met Base-64 gecodeerde X.509 (. CER)-indeling.

1.  Open de Application Gateway **HTTP-instellingen** in de Azure Portal.

1. Open de HTTP-instellingen, selecteer **Certificaat toevoegen** en zoek het certificaatbestand dat u zojuist hebt opgeslagen.

1. Selecteer **Opslaan om** de HTTP-instellingen op te slaan.

U kunt het basiscertificaat ook exporteren vanaf een clientmachine door rechtstreeks toegang te krijgen tot de server (door Application Gateway te omzeilen) via de browser en het basiscertificaat vanuit de browser te exporteren.

Zie Vertrouwd basiscertificaat exporteren [(voor v2 SKU)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku)voor meer informatie over het extraheren en uploaden van vertrouwde basiscertificaten in Application Gateway.

#### <a name="trusted-root-certificate-mismatch"></a>Niet-overeenkomend basiscertificaat

**Bericht:** Het basiscertificaat van het servercertificaat dat door de back-end wordt gebruikt, komt niet overeen met het vertrouwde basiscertificaat dat is toegevoegd aan de toepassingsgateway. Zorg ervoor dat u het juiste basiscertificaat toevoegt om de back-end toe te staan.

**Oorzaak:** End-to-end SSL met Application Gateway v2 vereist dat het certificaat van de back-endserver wordt geverifieerd om de server in orde te achten.
Een TLS/SSL-certificaat kan alleen worden vertrouwd als het back-endservercertificaat wordt uitgegeven door een CERTIFICERINGsinstantie die is opgenomen in het vertrouwde Application Gateway. Als het certificaat niet is uitgegeven door een vertrouwde certificeringsinstantie (er is bijvoorbeeld een zelf-ondertekend certificaat gebruikt), moeten gebruikers het certificaat van de vergever uploaden naar Application Gateway.

Het certificaat dat is geüpload naar Application Gateway HTTP-instellingen moet overeenkomen met het basiscertificaat van het back-endservercertificaat.

**Oplossing:** Als u dit foutbericht ontvangt, komt het certificaat dat is geüpload naar Application Gateway niet overeen met het certificaat dat is geüpload naar de back-upserver.

Volg stap 1-11 in de voorgaande methode om het juiste vertrouwde basiscertificaat te uploaden naar Application Gateway.

Zie Vertrouwd basiscertificaat exporteren [(voor v2 SKU)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku)voor meer informatie over het extraheren en uploaden van vertrouwde basiscertificaten in Application Gateway.
> [!NOTE]
> Deze fout kan ook optreden als de back-endserver de volledige keten van het certificaat niet uitwisselt, inclusief de Root > Intermediate (indien van toepassing) > Leaf tijdens de TLS-handshake. Om dit te controleren, kunt u OpenSSL-opdrachten van elke client gebruiken en verbinding maken met de back-endserver met behulp van de geconfigureerde instellingen in de test Application Gateway maken.

Bijvoorbeeld:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Als de uitvoer niet de volledige keten toont van het certificaat dat wordt geretourneerd, exporteert u het certificaat opnieuw met de volledige keten, inclusief het basiscertificaat. Configureer dat certificaat op uw back-endserver. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>Ongeldige algemene naam (CN) van back-endcertificaat

**Bericht:** De algemene naam (CN) van het back-endcertificaat komt niet overeen met de hostheader van de test.

**Oorzaak:** Application Gateway controleert of de hostnaam die is opgegeven in de HTTP-instellingen van de back-eind overeenkomt met die van de CN die wordt gepresenteerd door het TLS/SSL-certificaat van de back-ende server. Dit is Standard_v2 gedrag WAF_v2 SKU (V2). De SNI (Standard- en WAF-SKU)-SKU (v1) Servernaamindicatie is ingesteld als de FQDN in het adres van de back-endpool. Zie Overzicht van TLS-beëindiging en [end-to-end-TLS](ssl-overview.md)met Application Gateway voor meer informatie over SNI-gedrag en verschillen tussen v1 en v2 SKU.

Als er in de v2-SKU een standaardtest is (er is geen aangepaste test geconfigureerd en gekoppeld), wordt SNI ingesteld vanaf de hostnaam die wordt vermeld in de HTTP-instellingen. Of als 'Hostnaam kiezen uit back-adres' wordt vermeld in de HTTP-instellingen, waarbij de back-adresgroep een geldige FQDN bevat, wordt deze instelling toegepast.

Als er een aangepaste test is gekoppeld aan de HTTP-instellingen, wordt SNI ingesteld op basis van de hostnaam die wordt vermeld in de configuratie van de aangepaste test. Of als Hostnaam kiezen uit de HTTP-instellingen van de **back-end** is geselecteerd in de aangepaste test, wordt SNI ingesteld op basis van de hostnaam die wordt vermeld in de HTTP-instellingen.

Als **Hostnaam kiezen uit back-adres** is ingesteld in de HTTP-instellingen, moet de back-end-adresgroep een geldige FQDN bevatten.

Als u dit foutbericht ontvangt, komt de CN van het back-endcertificaat niet overeen met de hostnaam die is geconfigureerd in de aangepaste test of de HTTP-instellingen (als Hostnaam kiezen uit DE HTTP-instellingen van de **back-end** is geselecteerd). Als u een standaardtest gebruikt, wordt de hostnaam ingesteld op **127.0.0.1.** Als dat geen gewenste waarde is, moet u een aangepaste test maken en deze koppelen aan de HTTP-instellingen.

**Oplossing:**

Volg deze stappen om het probleem op te lossen.

Voor Windows:

1.  Meld u aan bij de computer waarop uw toepassing wordt gehost.

1.  Selecteer Win+R of klik met de rechtermuisknop op **de knop Start** en selecteer **Uitvoeren.**

1.  Voer **certmgr.msc in** en selecteer Enter. U kunt ook zoeken naar Certificaatbeheer in het **menu** Start.

1.  Zoek het certificaat (meestal in `\Certificates - Current User\\Personal\\Certificates` ) en open het certificaat.

1.  Controleer op **het tabblad** Details het certificaat **Onderwerp**.

1.  Controleer de CN van het certificaat in de details en voer hetzelfde in het veld hostnaam van de aangepaste test of in de HTTP-instellingen in (als Hostnaam kiezen uit de HTTP-instellingen van de **back-ende** is geselecteerd). Als dit niet de gewenste hostnaam voor uw website is, moet u een certificaat voor dat domein verkrijgen of de juiste hostnaam invoeren in de configuratie van de aangepaste test of HTTP-instelling.

Voor Linux met OpenSSL:

1.  Voer deze opdracht uit in OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  Zoek in de weergegeven eigenschappen de CN van het certificaat en voer hetzelfde in het veld hostnaam van de HTTP-instellingen in. Als dit niet de gewenste hostnaam voor uw website is, moet u een certificaat voor dat domein verkrijgen of de juiste hostnaam invoeren in de configuratie van de aangepaste test of HTTP-instelling.

#### <a name="backend-certificate-is-invalid"></a>Back-endcertificaat is ongeldig

**Bericht:** Het back-endcertificaat is ongeldig. De huidige datum valt niet binnen het bereik Geldig van en Geldig \" tot heden op het \" \" \" certificaat.

**Oorzaak:** Elk certificaat wordt geleverd met een geldigheidsbereik en de HTTPS-verbinding is niet beveiligd, tenzij het TLS/SSL-certificaat van de server geldig is. De huidige gegevens moeten binnen het geldige **van en** geldig zijn voor **het bereik.** Als dat niet het probleem is, wordt het certificaat als ongeldig beschouwd en wordt er een beveiligingsprobleem Application Gateway back-endserver als Beschadigd wordt markeert.

**Oplossing:** Als uw TLS/SSL-certificaat is verlopen, vernieuwt u het certificaat bij uw leverancier en werkt u de serverinstellingen bij met het nieuwe certificaat. Als het een zelf-ondertekend certificaat is, moet u een geldig certificaat genereren en het basiscertificaat uploaden naar Application Gateway HTTP-instellingen. Voer hiervoor de volgende stappen uit:

1.  Open uw Application Gateway HTTP-instellingen in de portal.

1.  Selecteer de instelling met het verlopen certificaat, selecteer **Certificaat toevoegen** en open het nieuwe certificaatbestand.

1.  Verwijder het oude certificaat met behulp van **het pictogram Verwijderen** naast het certificaat en selecteer **opslaan.**

#### <a name="certificate-verification-failed"></a>Verificatie van certificaat is mislukt

**Bericht:** De geldigheid van het back-endcertificaat kan niet worden geverifieerd. Als u de reden wilt weten, controleert u De diagnostische gegevens van OpenSSL voor het bericht dat is gekoppeld aan foutcode {errorCode}

**Oorzaak:** Deze fout treedt Application Gateway de geldigheid van het certificaat niet kan verifiëren.

**Oplossing:** U kunt dit probleem oplossen door te controleren of het certificaat op uw server correct is gemaakt. U kunt bijvoorbeeld [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) gebruiken om het certificaat en de eigenschappen ervan te controleren en vervolgens proberen het certificaat opnieuw te uploaden naar Application Gateway HTTP-instellingen.

<a name="backend-health-status-unknown"></a>Status van back-en-die: onbekend
-------------------------------
Als de back-end-status wordt weergegeven als Onbekend, lijkt de portalweergave op de volgende schermopname:

![Application Gateway back-Application Gateway - Onbekend](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Dit gedrag kan een of meer van de volgende oorzaken hebben:

1.  De NSG op het Application Gateway-subnet blokkeert de inkomende toegang tot de poorten 65503-65534 (v1 SKU) of 65200-65535 (v2 SKU) van 'Internet'.
1.  De UDR op het Application Gateway-subnet is ingesteld op de standaardroute (0.0.0.0/0) en de volgende hop is niet opgegeven als 'Internet'.
1.  De standaardroute wordt geadverteerd via een ExpressRoute-/VPN-verbinding met een virtueel netwerk via BGP.
1.  De aangepaste DNS-server is geconfigureerd in een virtueel netwerk dat geen openbare domeinnamen kan omzetten.
1.  Toepassingsgateway bevindt zich in een foutstatus.

**Oplossing:**

1.  Controleer of uw NSG de toegang tot de poorten 65503-65534 (v1 SKU) of 65200-65535 (v2 SKU) vanaf **internet blokkeert:**

    a.  Selecteer op Application Gateway **tabblad Overzicht** de koppeling **Virtual Network/Subnet.**

    b.  Selecteer op **het tabblad Subnetten** van het virtuele netwerk het subnet Application Gateway is geïmplementeerd.

    c.  Controleer of er een NSG is geconfigureerd.

    d.  Als er een NSG is geconfigureerd, zoekt u naar die NSG-resource op het **tabblad Zoeken** of onder **Alle resources.**

    e.  Voeg in de sectie **Binnenkomende** regels een regel voor binnenkomende toegang toe om het doelpoortbereik 65503-65534 toe te staan voor  v1 SKU of 65200-65535 v2 SKU met de bron ingesteld op **Any** of **Internet**.

    f.  Selecteer **Opslaan** en controleer of u de back-end kunt weergeven als In orde. U kunt dit ook doen via [PowerShell/CLI.](../virtual-network/manage-network-security-group.md)

1.  Controleer of uw UDR een standaardroute (0.0.0.0/0) heeft met de volgende hop die niet is ingesteld op **Internet:**
    
    a.  Volg stap 1a en 1b om uw subnet te bepalen.

    b.  Controleer of er een UDR is geconfigureerd. Als deze er is, zoekt u de resource op de zoekbalk of onder **Alle resources.**

    c.  Controleer of er standaardroutes zijn (0.0.0.0/0) met de volgende hop die niet is ingesteld op **Internet**. Als de instelling  Virtueel apparaat of **Virtual Network-gateway** is, moet u ervoor zorgen dat uw virtuele apparaat of het on-premises apparaat het pakket op de juiste manier kan terugverplaatsen naar de internetbestemming zonder het pakket te wijzigen.

    d.  Anders wijzigt u de volgende hop in **Internet,** **selecteert u Opslaan** en controleert u de back-end-status.

1.  Standaardroute die wordt geadverteerd door de ExpressRoute-/VPN-verbinding met het virtuele netwerk via BGP:

    a.  Als u een ExpressRoute-/VPN-verbinding met het virtuele netwerk via BGP hebt en als u een standaardroute adverteert, moet u ervoor zorgen dat het pakket wordt teruggeleid naar de internetbestemming zonder deze te wijzigen. U kunt dit controleren met behulp van de optie **Verbinding** oplossen in Application Gateway portal.

    b.  Kies de bestemming handmatig als een via internet routeerbaar IP-adres, zoals 1.1.1.1. Stel de doelpoort in als alles en controleer de connectiviteit.

    c.  Als de volgende hop virtuele netwerkgateway is, is er mogelijk een standaardroute die wordt geadverteerd via ExpressRoute of VPN.

1.  Als er een aangepaste DNS-server is geconfigureerd in het virtuele netwerk, controleert u of de server (of servers) openbare domeinen kan oplossen. Openbare domeinnaamoplossing is mogelijk vereist in scenario's waarin Application Gateway externe domeinen, zoals OCSP-servers, moeten bereiken of de intrekkingsstatus van het certificaat moeten controleren.

1.  Als u wilt controleren Application Gateway status in orde en actief is, gaat u naar **de** Resource Health in de portal en controleert u of de status **In orde is.** Als u de status **Beschadigd of** **Gedegradeerd ziet,** neemt u [contact op met de ondersteuning](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Volgende stappen
----------

Meer informatie over [Application Gateway en logboekregistratie.](./application-gateway-diagnostics.md)
