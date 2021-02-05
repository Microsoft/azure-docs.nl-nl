---
title: Problemen met back-endservers in Azure-toepassing gateway oplossen
description: Hierin wordt beschreven hoe u problemen met de back-end van de Azure-toepassing-gateway oplost
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: 95b74e5fc6c5d2c09ff04b3f14e920ae675ab6e1
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592752"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Problemen met back-endservers oplossen in Application Gateway
==================================================

<a name="overview"></a>Overzicht
--------

Azure-toepassing gateway test back-endservers standaard om de status te controleren en om te controleren of ze gereed zijn om te dienen voor aanvragen. Gebruikers kunnen ook aangepaste tests maken om de hostnaam, het pad dat wordt onderzocht en de status codes als in orde te vermelden. Als de back-endserver niet met succes reageert, markeert Application Gateway de server als beschadigd en stopt het door sturen van aanvragen naar de server. Nadat de server met succes is gestart, wordt Application Gateway hervat met het door sturen van de aanvragen.

### <a name="how-to-check-backend-health"></a>De status van de back-end controleren

Als u de status van uw back-end-pool wilt controleren, kunt u de **back-end-status** pagina gebruiken op de Azure Portal. U kunt ook [Azure PowerShell](/powershell/module/az.network/get-azapplicationgatewaybackendhealth), [cli](/cli/azure/network/application-gateway#az-network-application-gateway-show-backend-health)of [rest API](/rest/api/application-gateway/applicationgateways/backendhealth)gebruiken.

De status die wordt opgehaald door een van deze methoden, kan een van de volgende zijn:

- In orde

- Niet in orde

- Onbekend

Als de status van de back-end voor een server in orde is, betekent dit dat Application Gateway de aanvragen naar die server stuurt. Als de status van de back-end voor alle servers in een back-end-groep beschadigd is of onbekend is, kunnen er problemen optreden wanneer u probeert toegang te krijgen tot toepassingen. In dit artikel worden de symptomen, de oorzaak en de oplossing beschreven voor elk van de weer gegeven fouten.

<a name="backend-health-status-unhealthy"></a>Backend-status: beschadigd
-------------------------------

Als de status van de back-end niet in orde is, ziet de portal weergave eruit als in de volgende scherm afbeelding:

![Status van Application Gateway-back-end-slechte status](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Als u een Azure PowerShell-, CLI-of Azure REST API-query gebruikt, ontvangt u een antwoord dat er ongeveer als volgt uitziet:
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
Nadat u een status van een onjuiste back-endserver hebt ontvangen voor alle servers in een back-end-groep, worden er geen aanvragen doorgestuurd naar de servers en retourneert de Application Gateway de fout melding ' 502-ongeldige gateway ' naar de aanvragende client. Als u dit probleem wilt oplossen, controleert u de kolom **Details** op het tabblad **back-end** .

In het bericht dat wordt weer gegeven in de kolom **Details** vindt u meer informatie over het probleem en op basis van die, kunt u beginnen met het oplossen van het probleem.

> [!NOTE]
> De standaard test aanvraag wordt verzonden met de indeling \<protocol\> ://127.0.0.1: \<port\> /. Bijvoorbeeld http://127.0.0.1:80 voor een http-test op poort 80. Alleen HTTP-status codes van 200 tot en met 399 worden als gezond beschouwd. Het protocol en de doel poort worden overgenomen van de HTTP-instellingen. Als u Application Gateway wilt testen op een ander protocol, hostnaam of pad en een andere status code als in orde herkent, configureert u een aangepaste test en koppelt u deze aan de HTTP-instellingen.

<a name="error-messages"></a>Foutberichten
------------------------
#### <a name="backend-server-timeout"></a>Time-out van back-endserver

**Bericht:** De tijd die de back-end heeft genomen om te reageren op de \' status test van de toepassings gateway, is hoger dan de drempel waarde voor time-out in de test instelling.

**Oorzaak:** Nadat Application Gateway een HTTP (S)-test aanvraag naar de back-endserver verzendt, wordt gewacht op een reactie van de back-endserver voor een geconfigureerde periode. Als de back-endserver niet reageert binnen de geconfigureerde periode (de time-outwaarde), wordt deze als beschadigd gemarkeerd totdat de server opnieuw reageert binnen de geconfigureerde time-outperiode.

**Oplossing:** Controleer waarom de back-endserver of toepassing niet reageert binnen de geconfigureerde time-outperiode en Controleer ook de toepassings afhankelijkheden. Controleer bijvoorbeeld of er problemen zijn met de data base die een vertraging in de reactie kunnen veroorzaken. Als u op de hoogte bent van het gedrag van de toepassing en deze alleen na de time-outwaarde moet reageren, verhoogt u de time-outwaarde van de aangepaste test instellingen. U moet een aangepaste test hebben om de time-outwaarde te wijzigen. [Zie de documentatie pagina](./application-gateway-create-probe-portal.md)voor meer informatie over het configureren van een aangepaste test.

Voer de volgende stappen uit om de time-outwaarde te verhogen:

1.  Direct toegang tot de back-endserver en controleer de tijd die nodig is om op de pagina op de server te reageren. U kunt elk hulp programma gebruiken om toegang te krijgen tot de back-end-server, inclusief een browser met ontwikkel hulpprogramma's.

1.  Nadat u hebt gezien hoe lang het duurt voordat de toepassing reageert, selecteert u het tabblad **status controles** en selecteert u vervolgens de test die is gekoppeld aan uw HTTP-instellingen.

1.  Voer in seconden een time-outwaarde in die groter is dan de reactie tijd van de toepassing.

1.  Sla de instellingen voor de aangepaste test op en controleer of de status van de back-end nu in orde wordt weer gegeven.

#### <a name="dns-resolution-error"></a>DNS-omzettings fout

**Bericht:** Application Gateway kan geen test maken voor deze back-end. Dit gebeurt meestal wanneer de FQDN van de back-end niet correct is ingevoerd. 

**Oorzaak:** Als de back-end-pool van het type IP-adres/FQDN of App Service is, wordt Application Gateway omgezet naar het IP-adres van de FQDN die is ingevoerd via Domain Name System (DNS) (aangepaste of Azure-standaard) en wordt geprobeerd verbinding te maken met de server op de TCP-poort die wordt vermeld in de HTTP-instellingen. Maar als dit bericht wordt weer gegeven, wordt voorgesteld dat Application Gateway het opgegeven IP-adres van de ingevoerde FQDN niet succesvol kan oplossen.

**Oplossing:**

1.  Controleer of de FQDN die is opgegeven in de back-end-groep juist is en of deze een openbaar domein is, en probeer deze vervolgens op te lossen vanaf uw lokale computer.

1.  Als u het IP-adres kunt omzetten, kan er iets mis zijn met de DNS-configuratie in het virtuele netwerk.

1.  Controleer of het virtuele netwerk is geconfigureerd met een aangepaste DNS-server. Als dat het geval is, controleert u de DNS-server op waarom deze niet kan worden omgezet in het IP-adres van de opgegeven FQDN.

1.  Als u de standaard-DNS van Azure gebruikt, controleert u of het juiste registratie-of CNAME-record is voltooid.

1.  Als het een persoonlijk of intern domein is, probeert u het probleem op te lossen vanaf een VM in hetzelfde virtuele netwerk. Als u het probleem kunt oplossen, start u Application Gateway opnieuw op en controleert u het opnieuw. Als u Application Gateway opnieuw wilt opstarten, moet u [stoppen](/powershell/module/azurerm.network/stop-azurermapplicationgateway) en [starten](/powershell/module/azurerm.network/start-azurermapplicationgateway) met de Power shell-opdrachten die in deze gekoppelde resources worden beschreven.

#### <a name="tcp-connect-error"></a>TCP-verbindings fout

**Bericht:** Application Gateway kan geen verbinding maken met de back-end.
Controleer of de back-end reageert op de poort die wordt gebruikt voor de test.
Controleer ook of NSG/UDR/firewall de toegang tot het IP-adres en de poort van deze back-end blokkeert

**Oorzaak:** Na de DNS-omzettings fase probeert Application Gateway verbinding te maken met de back-end-server op de TCP-poort die is geconfigureerd in de HTTP-instellingen. Als Application Gateway geen TCP-sessie kunt instellen op de opgegeven poort, wordt de test gemarkeerd als beschadigd met dit bericht.

**Oplossing:** Als deze fout wordt weer gegeven, voert u de volgende stappen uit:

1.  Controleer of u verbinding kunt maken met de back-endserver op de poort die wordt vermeld in de HTTP-instellingen met behulp van een browser of Power shell. Voer bijvoorbeeld de volgende opdracht uit: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Als de vermelde poort niet de gewenste poort is, voert u het juiste poort nummer in voor Application Gateway verbinding maken met de back-end-server

1.  Als u ook geen verbinding kunt maken met de poort op de lokale computer, dan:

    a.  Controleer de instellingen van de netwerk beveiligings groep (NSG) van de netwerk adapter en het subnet van de back-endserver en of binnenkomende verbindingen met de geconfigureerde poort zijn toegestaan. Als dat niet het geval is, maakt u een nieuwe regel om de verbindingen toe te staan. [Zie de documentatie pagina](../virtual-network/tutorial-filter-network-traffic.md#create-security-rules)voor meer informatie over het maken van NSG-regels.

    b.  Controleer of de NSG-instellingen van Application Gateway het subnet uitgaand openbaar en privé verkeer toestaan, zodat er een verbinding kan worden gemaakt. Controleer de document pagina die wordt weer gegeven in stap 3a voor meer informatie over het maken van NSG-regels.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Controleer de instellingen van de door de gebruiker gedefinieerde routes (UDR) van Application Gateway en het subnet van de back-endserver voor eventuele afwijkings van de route ring. Zorg ervoor dat de UDR niet het verkeer vanuit het back-end-subnet doorstuurt. Controleer bijvoorbeeld op routes naar virtuele netwerk apparaten of standaard routes die worden geadverteerd naar het Application Gateway subnet via Azure ExpressRoute en/of VPN.

    d.  Als u de juiste routes en regels voor een netwerk adapter wilt controleren, kunt u de volgende Power shell-opdrachten gebruiken:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Als u geen problemen met NSG of UDR kunt vinden, controleert u uw back-endserver op toepassings problemen waardoor clients geen TCP-sessie kunnen maken op de geconfigureerde poorten. Een aantal dingen om te controleren:

    a.  Open een opdracht prompt (Win + R- \> cmd), Voer in `netstat` en selecteer ENTER.

    b.  Controleer of de server luistert op de poort die is geconfigureerd. Bijvoorbeeld:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Als deze niet op de geconfigureerde poort luistert, controleert u de webserver instellingen. Bijvoorbeeld: site bindingen in IIS, Server blok in NGINX en virtual host in Apache.

    d.  Controleer de firewall instellingen van het besturings systeem om ervoor te zorgen dat inkomend verkeer naar de poort is toegestaan.

#### <a name="http-status-code-mismatch"></a>HTTP-status code komt niet overeen

**Bericht:** De status code van het \' HTTP-antwoord van de back-end komt niet overeen met de test instelling. Verwacht: {HTTPStatusCode0} ontvangen: {HTTPStatusCode1}.

**Oorzaak:** Nadat de TCP-verbinding tot stand is gebracht en een TLS-handshake is uitgevoerd (als TLS is ingeschakeld), stuurt Application Gateway de test als een HTTP GET-aanvraag naar de back-endserver. Zoals eerder beschreven, is de standaard test naar \<protocol\> ://127.0.0.1: \<port\> /en worden de antwoord status codes in de rage 200 tot en met 399 als in orde beschouwd. Als de server een andere status code retourneert, wordt deze als beschadigd gemarkeerd met dit bericht.

**Oplossing:** Afhankelijk van de reactie code van de back-endserver kunt u de volgende stappen uitvoeren. Enkele van de algemene status codes worden hier weer gegeven:

| **Fout** | **Acties** |
| --- | --- |
| Status code van test komt niet overeen: ontvangen 401 | Controleer of verificatie is vereist voor de back-endserver. Application Gateway tests kunnen geen referenties door geven voor authenticatie. Sta toe dat \" HTTP 401 \" in een test status code overeenkomt of test naar een pad waarvoor geen verificatie is vereist voor de server. | |
| Status code van test komt niet overeen: ontvangen 403 | Toegang verboden. Controleer of toegang tot het pad is toegestaan op de back-endserver. | |
| Status code van test komt niet overeen: ontvangen 404 | Pagina is niet gevonden. Controleer of het pad naar de hostnaam toegankelijk is op de back-endserver. Wijzig de hostnaam of het pad para meter in een toegankelijke waarde. | |
| Status code van test komt niet overeen: ontvangen 405 | De test aanvragen voor Application Gateway gebruiken de HTTP GET-methode. Controleer of deze methode is toegestaan op uw server. | |
| Status code van test komt niet overeen: ontvangen 500 | Interne serverfout. Controleer de status van de back-endserver en of de services worden uitgevoerd. | |
| Status code van test komt niet overeen: ontvangen 503 | Service niet beschikbaar. Controleer de status van de back-endserver en of de services worden uitgevoerd. | |

Of als u denkt dat het antwoord legitiem is en u Application Gateway andere status codes als in orde wilt accepteren, kunt u een aangepaste test maken. Deze aanpak is nuttig in situaties waarin de back-end-website verificatie nodig heeft. Omdat de test aanvragen geen gebruikers referenties afdragen, zullen ze mislukken en wordt een HTTP 401-status code geretourneerd door de back-endserver.

Volg [deze stappen](./application-gateway-create-probe-portal.md)om een aangepaste test te maken.

#### <a name="http-response-body-mismatch"></a>Onjuiste hoofd tekst van HTTP-antwoord

**Bericht:** De hoofd tekst van het \' HTTP-antwoord van de back-end-s komt niet overeen met de test instelling. De ontvangen antwoord tekst bevat geen {string}.

**Oorzaak:** Wanneer u een aangepaste test maakt, hebt u de mogelijkheid om een back-endserver als in orde te markeren door te voldoen aan een teken reeks uit de hoofd tekst van het antwoord. U kunt bijvoorbeeld Application Gateway zodanig configureren dat "niet-geautoriseerd" als teken reeks wordt geaccepteerd. Als het antwoord van de back-endserver voor de test aanvraag de teken reeks zonder **toestemming** bevat, wordt deze als in orde gemarkeerd. Als dat niet het geval is, wordt het bericht gemarkeerd als beschadigd.

**Oplossing:** Voer de volgende stappen uit om dit probleem op te lossen:

1.  Open de back-endserver lokaal of van een client computer op het controlepad en controleer de tekst van het antwoord.

1.  Controleer of de antwoord tekst in de Application Gateway aangepaste test configuratie overeenkomt met wat is geconfigureerd.

1.  Als ze niet overeenkomen, wijzigt u de test configuratie zodat de juiste teken reeks waarde wordt geaccepteerd.

Meer informatie over [Application Gateway probe matching](./application-gateway-probe-overview.md#probe-matching).

>[!NOTE]
> Zie de pagina [overzicht van TLS](ssl-overview.md) voor alle aan TLS gerelateerde fout berichten voor meer informatie over het gedrag van SNI en verschillen tussen de sku's v1 en v2.


#### <a name="backend-server-certificate-invalid-ca"></a>Back-end-server certificaat ongeldig CA

**Bericht:** Het server certificaat dat door de back-end wordt gebruikt, is niet ondertekend door een bekende certificerings instantie (CA). De back-end op het Application Gateway toestaan door het basis certificaat van het server certificaat te uploaden dat wordt gebruikt door de back-end.

**Oorzaak:** End-to-end SSL met Application Gateway v2 vereist dat het certificaat van de back-endserver wordt geverifieerd om te zorgen dat de server in orde is.
Als een TLS/SSL-certificaat wordt vertrouwd, moet dat certificaat van de back-endserver worden uitgegeven door een certificerings instantie die is opgenomen in het vertrouwde archief van Application Gateway. Als het certificaat niet is uitgegeven door een vertrouwde certificerings instantie (bijvoorbeeld als er een zelfondertekend certificaat is gebruikt), moeten gebruikers het certificaat van de verlener uploaden naar Application Gateway.

**Oplossing:** Volg deze stappen om het vertrouwde basis certificaat te exporteren en te uploaden naar Application Gateway. (Deze stappen gelden voor Windows-clients.)

1.  Meld u aan bij de computer waarop uw toepassing wordt gehost.

1.  Selecteer Win + R of klik met de rechter muisknop op de knop **Start** en selecteer vervolgens **uitvoeren**.

1.  Voer in `certmgr.msc` en selecteer ENTER. U kunt ook zoeken naar Certificate manager in het menu **Start** .

1.  Zoek het certificaat, meestal in `\Certificates - Current User\\Personal\\Certificates\` , en open het.

1.  Selecteer het basis certificaat en selecteer vervolgens **certificaat weer geven**.

1.  Selecteer in de certificaat eigenschappen het tabblad **Details** .

1.  Op het tabblad **Details** selecteert u de optie **kopiëren naar bestand** en slaat u het bestand op in de base-64 Encoded X. 509 (. CER)-indeling.

1.  Open de pagina HTTP- **instellingen** Application Gateway in het Azure Portal.

1. Open de HTTP-instellingen, selecteer **certificaat toevoegen** en zoek het certificaat bestand dat u zojuist hebt opgeslagen.

1. Selecteer **Opslaan** om de HTTP-instellingen op te slaan.

U kunt het basis certificaat ook exporteren vanaf een client computer door rechtstreeks toegang te krijgen tot de server (Application Gateway overs Laan) via de browser en het basis certificaat te exporteren vanuit de browser.

Zie voor meer informatie over het uitpakken en uploaden van vertrouwde basis certificaten in Application Gateway het [vertrouwde basis certificaat exporteren (voor v2-SKU)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Niet-overeenkomende vertrouwde basis certificaten

**Bericht:** Het basis certificaat van het server certificaat dat door de back-end wordt gebruikt, komt niet overeen met het vertrouwde basis certificaat dat is toegevoegd aan de toepassings gateway. Zorg ervoor dat u het juiste basis certificaat toevoegt aan allowlist de back-end.

**Oorzaak:** End-to-end SSL met Application Gateway v2 vereist dat het certificaat van de back-endserver wordt geverifieerd om te zorgen dat de server in orde is.
Een TLS/SSL-certificaat kan alleen worden vertrouwd als het back-endserver certificaat is uitgegeven door een certificerings instantie die is opgenomen in het vertrouwde archief van Application Gateway. Als het certificaat niet is uitgegeven door een vertrouwde certificerings instantie (bijvoorbeeld als er een zelfondertekend certificaat is gebruikt), moeten gebruikers het certificaat van de verlener uploaden naar Application Gateway.

Het certificaat dat is geüpload naar Application Gateway HTTP-instellingen, moet overeenkomen met het basis certificaat van het back-endserver certificaat.

**Oplossing:** Als dit fout bericht wordt weer gegeven, is er een verschil tussen het certificaat dat is geüpload naar Application Gateway en het dat is geüpload naar de back-endserver.

Volg de stappen 1-11 in de voor gaande methode om het juiste vertrouwde basis certificaat te uploaden naar Application Gateway.

Zie voor meer informatie over het uitpakken en uploaden van vertrouwde basis certificaten in Application Gateway het [vertrouwde basis certificaat exporteren (voor v2-SKU)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Deze fout kan ook optreden als de back-endserver de volledige keten van het certificaat niet uitwisselt, met inbegrip van de basis > tussenliggende (indien van toepassing) > Leaf tijdens de TLS-handshake. Als u wilt controleren, kunt u OpenSSL-opdrachten van elke client gebruiken en verbinding maken met de back-endserver met behulp van de geconfigureerde instellingen in de Application Gateway-test.

Bijvoorbeeld:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Als in de uitvoer niet de volledige keten van het certificaat wordt weer gegeven, exporteert u het certificaat opnieuw met de volledige keten, inclusief het basis certificaat. Configureer dat certificaat op uw back-endserver. 

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

#### <a name="backend-certificate-invalid-common-name-cn"></a>Back-end-certificaat ongeldige common name (CN)

**Bericht:** De algemene naam (CN) van het back-end-certificaat komt niet overeen met de host-header van de test.

**Oorzaak:** Application Gateway controleert of de hostnaam die is opgegeven in de back-end-HTTP-instellingen overeenkomt met die van de CN-naam van het TLS/SSL-certificaat van de back-endserver. Dit is Standard_v2 en het WAF_v2 SKU-gedrag (v2). De Standard-en WAF SKU (v1) Servernaamindicatie (SNI) is ingesteld als de FQDN in het adres van de back-end-pool. Zie [overzicht van TLS-beëindiging en end-to-end-TLS met Application Gateway](ssl-overview.md)voor meer informatie over het gedrag van SNI en verschillen tussen de sku's v1 en v2.

Als er in de v2-SKU een standaard test is (er is geen aangepaste test geconfigureerd en gekoppeld), wordt SNI ingesteld op basis van de hostnaam die wordt vermeld in de HTTP-instellingen. Of als ' hostnaam van het back-upadres kiezen ' wordt vermeld in de HTTP-instellingen, waarbij de back-end-adres groep een geldige FQDN bevat, wordt deze instelling toegepast.

Als er een aangepaste test is gekoppeld aan de HTTP-instellingen, wordt SNI ingesteld op basis van de hostnaam die wordt vermeld in de aangepaste test configuratie. Of, als de waarde **hostname selecteren uit de back-end-HTTP-instellingen** is geselecteerd in de aangepaste test, wordt SNI ingesteld op basis van de hostnaam die wordt vermeld in de HTTP-instellingen.

Als de **hostnaam uit het back-end-adres kiezen** is ingesteld in de HTTP-instellingen, moet de back-endadresgroep een geldige FQDN-adres groep bevatten.

Als u dit fout bericht ontvangt, komt de CN van het back-end-certificaat niet overeen met de hostnaam die is geconfigureerd in de aangepaste test of de HTTP-instellingen (als de waarde voor **hostnaam uit de back-end-HTTP-instellingen kiezen** is geselecteerd). Als u een standaard test gebruikt, wordt de naam van de host ingesteld op **127.0.0.1**. Als dat geen gewenste waarde is, maakt u een aangepaste test en koppelt u deze aan de HTTP-instellingen.

**Oplossing:**

Volg deze stappen om het probleem op te lossen.

Voor Windows:

1.  Meld u aan bij de computer waarop uw toepassing wordt gehost.

1.  Selecteer Win + R of klik met de rechter muisknop op de knop **Start** en selecteer **uitvoeren**.

1.  Voer **certmgr. msc** in en selecteer ENTER. U kunt ook zoeken naar Certificate manager in het menu **Start** .

1.  Zoek het certificaat (meestal in `\Certificates - Current User\\Personal\\Certificates` ) en open het certificaat.

1.  Controleer op het tabblad **Details** het certificaat **onderwerp**.

1.  Controleer de CN van het certificaat op basis van de details en geef hetzelfde op in het veld hostnaam van de aangepaste test of in de HTTP-instellingen (als **hostnaam kiezen van back-end-HTTP-instellingen** is geselecteerd). Als dat niet de gewenste hostnaam voor uw website is, moet u een certificaat voor dat domein ophalen of de juiste hostnaam opgeven in de configuratie van de aangepaste test of HTTP-instelling.

Voor Linux met behulp van OpenSSL:

1.  Voer deze opdracht uit in OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  Zoek de CN van het certificaat in de weer gegeven eigenschappen en voer in het veld hostnaam van de HTTP-instellingen dezelfde naam in. Als dat niet de gewenste hostnaam voor uw website is, moet u een certificaat voor dat domein ophalen of de juiste hostnaam opgeven in de configuratie van de aangepaste test of HTTP-instelling.

#### <a name="backend-certificate-is-invalid"></a>Back-end certificaat is ongeldig

**Bericht:** Back-end certificaat is ongeldig. De huidige datum valt niet binnen het \" geldig van- \" en \" geldig tot- \" datum bereik op het certificaat.

**Oorzaak:** Elk certificaat wordt geleverd met een geldig geldigheids bereik en de HTTPS-verbinding is niet beveiligd tenzij het TLS/SSL-certificaat van de server geldig is. De huidige gegevens moeten binnen de **geldige waarde van** en **geldig tot** het bereik liggen. Als dat niet het geval is, wordt het certificaat beschouwd als ongeldig en wordt er een beveiligings probleem gemaakt waarbij Application Gateway de back-endserver als beschadigd markeert.

**Oplossing:** Als uw TLS/SSL-certificaat is verlopen, verlengt u het certificaat met uw leverancier en werkt u de server instellingen bij met het nieuwe certificaat. Als het een zelfondertekend certificaat is, moet u een geldig certificaat genereren en het basis certificaat uploaden naar de Application Gateway HTTP-instellingen. Voer hiervoor de volgende stappen uit:

1.  Open de Application Gateway HTTP-instellingen in de portal.

1.  Selecteer de instelling met het verlopen certificaat, selecteer **certificaat toevoegen** en open het nieuwe certificaat bestand.

1.  Verwijder het oude certificaat met behulp van het pictogram **verwijderen** naast het certificaat en selecteer vervolgens **Opslaan**.

#### <a name="certificate-verification-failed"></a>Verificatie van certificaat is mislukt

**Bericht:** De geldigheid van het back-end-certificaat kan niet worden geverifieerd. Als u de reden wilt achterhalen, controleert u de OpenSSL Diagnostics voor het bericht dat is gekoppeld aan de fout code {error codes}

**Oorzaak:** Deze fout treedt op wanneer Application Gateway de geldigheid van het certificaat niet kunt controleren.

**Oplossing:** U kunt dit probleem oplossen door te controleren of het certificaat op de server correct is gemaakt. U kunt bijvoorbeeld [openssl](https://www.openssl.org/docs/man1.0.2/man1/verify.html) gebruiken om het certificaat en de bijbehorende eigenschappen te controleren en het certificaat vervolgens opnieuw te uploaden naar de Application Gateway http-instellingen.

<a name="backend-health-status-unknown"></a>Status van back-end: onbekend
-------------------------------
Als de status van de back-end wordt weer gegeven als onbekend, ziet de portal weergave eruit als in de volgende scherm afbeelding:

![Status van Application Gateway-back-end-onbekend](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Dit gedrag kan om een of meer van de volgende redenen optreden:

1.  De NSG op het Application Gateway-subnet blokkeert de inkomende toegang tot poorten 65503-65534 (v1 SKU) of 65200-65535 (v2-SKU) van Internet.
1.  De UDR op het Application Gateway subnet is ingesteld op de standaard route (0.0.0.0/0) en de volgende hop is niet opgegeven als ' Internet '.
1.  De standaard route wordt via BGP geadverteerd door een ExpressRoute/VPN-verbinding met een virtueel netwerk.
1.  De aangepaste DNS-server is geconfigureerd in een virtueel netwerk dat geen openbare domeinnamen kan omzetten.
1.  Toepassingsgateway bevindt zich in een foutstatus.

**Oplossing:**

1.  Controleer of uw NSG toegang tot de poorten 65503-65534 (v1 SKU) of 65200-65535 (v2-SKU) van **Internet** blokkeert:

    a.  Selecteer op het tabblad Application Gateway **overzicht** de koppeling **Virtual Network/subnet** .

    b.  Selecteer op het tabblad **subnetten** van het virtuele netwerk het subnet waar Application Gateway is geïmplementeerd.

    c.  Controleer of er een NSG is geconfigureerd.

    d.  Als een NSG is geconfigureerd, zoekt u naar die NSG-bron op het tabblad **zoeken** of in **alle resources**.

    e.  Voeg in de sectie **regels voor binnenkomende** verbindingen een regel voor binnenkomend verkeer toe om het doel poort bereik 65503-65534 voor v1 sku of 65200-65535 v2-SKU toe te staan met de **bron** set als **een** of **Internet**.

    f.  Selecteer **Opslaan** en controleer of u de back-end als in orde kunt weer geven. U kunt dit ook doen via [Power shell/cli](../virtual-network/manage-network-security-group.md).

1.  Controleer of uw UDR een standaard route (0.0.0.0/0) heeft met de volgende hop die niet is ingesteld als **Internet**:
    
    a.  Volg de stappen 1a en 1b om uw subnet te bepalen.

    b.  Controleer of er een UDR is geconfigureerd. Als dat het geval is, zoekt u naar de resource op de zoek balk of in **alle resources**.

    c.  Controleer of er standaard routes (0.0.0.0/0) zijn waarbij de volgende hop niet is ingesteld als **Internet**. Als de instelling **virtueel apparaat** of **Virtual Network gateway** is, moet u ervoor zorgen dat het pakket door het virtuele apparaat of het on-premises station correct naar de Internet bestemming kan worden doorgestuurd zonder het pakket te wijzigen.

    d.  Anders wijzigt u de volgende hop naar **Internet**, selecteert u **Opslaan** en controleert u de status van de back-end.

1.  Standaard route geadverteerd door de ExpressRoute/VPN-verbinding met het virtuele netwerk via BGP:

    a.  Als u een ExpressRoute/VPN-verbinding met het virtuele netwerk via BGP hebt en u een standaard route adverteert, moet u ervoor zorgen dat het pakket teruggestuurd wordt naar de Internet bestemming zonder het te wijzigen. U kunt controleren met behulp van de optie **verbinding oplossen** in de Application Gateway portal.

    b.  Kies de bestemming hand matig als elk IP-adres dat met Internet kan worden gerouteerd, zoals 1.1.1.1. Stel de doel poort in als wille keurig en controleer de connectiviteit.

    c.  Als de volgende hop een virtuele netwerk gateway is, is er mogelijk een standaard route die is geadverteerd via ExpressRoute of VPN.

1.  Als er een aangepaste DNS-server is geconfigureerd in het virtuele netwerk, controleert u of de server (of servers) open bare domeinen kan omzetten. Het kan zijn dat de naam omzetting van open bare domeinen vereist is in scenario's waarbij Application Gateway moet worden bereikt met externe domeinen als OCSP-servers of om de intrekkings status van het certificaat te controleren.

1.  Als u wilt controleren of Application Gateway in orde is en wordt uitgevoerd, gaat u naar de optie **resource Health** in de portal en controleert u of de status in **orde** is. [Neem contact op met de ondersteuning](https://azure.microsoft.com/support/options/)als u een **slechte** of **slechte** status ziet.

<a name="next-steps"></a>Volgende stappen
----------

Meer informatie over [Application Gateway diagnostische gegevens en logboek registratie](./application-gateway-diagnostics.md).
