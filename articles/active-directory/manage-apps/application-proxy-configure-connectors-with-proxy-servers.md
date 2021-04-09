---
title: Werken met bestaande on-premises proxy servers en Azure Active Directory
description: In dit artikel wordt beschreven hoe u kunt werken met bestaande on-premises proxy servers met Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/07/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: 09a257c4b80fd796ac4e1e8203f00857d2d95eaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259114"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Werken met bestaande on-premises proxyservers

In dit artikel wordt uitgelegd hoe u de connectors van de toepassings proxy van Azure Active Directory (Azure AD) kunt configureren voor gebruik met uitgaande proxy servers. Het is bedoeld voor klanten met netwerkomgevingen met bestaande proxy's.

We gaan nu naar deze belangrijkste implementatie scenario's kijken:

* Connectors configureren om uw on-premises uitgaande proxy's over te slaan.
* Connectors configureren voor het gebruik van een uitgaande proxy om toegang te krijgen tot Azure AD-toepassingsproxy.
* Configureer het gebruik van een proxy tussen de connector en de back-end-toepassing.

Zie [Meer informatie over Azure AD-toepassingsproxyconnectoren](application-proxy-connectors.md) voor meer informatie over de werking van connectoren.

## <a name="bypass-outbound-proxies"></a>Uitgaande proxy's overslaan

Connectoren hebben onderliggende besturingssysteemonderdelen waardoor de uitgaande aanvragen worden gemaakt. Deze onderdelen zoeken automatisch een proxyserver in het netwerk met behulp van Web Proxy Auto-Discovery (WPAD).

De besturingssysteemonderdelen zoeken een proxyserver door een DNS-zoekactie voor wpad.domainsuffix uit te voeren. Als de zoekopdracht wordt omgezet in DNS, wordt vervolgens een HTTP-aanvraag ingediend op het IP-adres voor wpad.dat. Deze aanvraag wordt het proxyconfiguratiescript in uw omgeving. Dit script wordt door de connector gebruikt om een uitgaande proxyserver te selecteren. Het is echter mogelijk dat verkeer op de connector nog steeds niet doorkomt, omdat er extra configuratie-instellingen nodig zijn op de proxy.

U kunt de connector configureren om uw on-premises proxy over te slaan, zodat u zeker weet dat de connector rechtstreeks is verbonden met de Azure-services. Dit is de aanbevolen methode zolang deze mogelijk is volgens uw netwerkbeleid, omdat u in dit geval één configuratie minder hoeft te onderhouden.

Als u het uitgaande proxy gebruik voor de connector wilt uitschakelen, bewerkt u de map C:\Program Files\Microsoft AAD app-proxy Connector\ApplicationProxyConnectorService.exe.config bestand en voegt u de sectie *System.net* toe, zoals weer gegeven in dit code voorbeeld:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Om ervoor te zorgen dat de Connector Updater-service ook de proxy omzeilt, brengt u een vergelijk bare wijziging aan in het ApplicationProxyConnectorUpdaterService.exe.config bestand. Dit bestand bevindt zich in C:\Program Files\Microsoft AAD app proxy Connector Updater.

Vergeet niet om kopieën van de oorspronkelijke bestanden te maken, in het geval u de .config-bestanden naar de standaardinstellingen moet terugzetten.

## <a name="use-the-outbound-proxy-server"></a>De uitgaande proxy server gebruiken

In sommige omgevingen moet al het uitgaande verkeer via een uitgaande proxy zonder uitzonde ring worden door lopen. Als gevolg hiervan is het overs laan van de proxy geen optie.

U kunt het connector verkeer configureren om via de uitgaande proxy door te gaan, zoals wordt weer gegeven in het volgende diagram:

 ![Connector verkeer configureren om door te gaan met een uitgaande proxy naar Azure AD-toepassingsproxy](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Als gevolg van alleen uitgaand verkeer, hoeft u geen inkomende toegang via uw firewalls te configureren.

> [!NOTE]
> Toepassings proxy biedt geen ondersteuning voor verificatie voor andere proxy's. De connector-en Updater netwerk service accounts moeten verbinding kunnen maken met de proxy zonder te worden bevraagd voor authenticatie.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Stap 1: de connector en gerelateerde services configureren om via de uitgaande proxy door te gaan

Als WPAD is ingeschakeld in de omgeving en op de juiste wijze is geconfigureerd, detecteert de connector automatisch de uitgaande proxy server en probeert deze te gebruiken. U kunt de connector echter expliciet configureren om door te gaan met een uitgaande proxy.

Als u dit wilt doen, bewerkt u de map C:\Program Files\Microsoft AAD app Connector\ApplicationProxyConnectorService.exe.config bestand en voegt u de sectie *System.net* toe, die wordt weer gegeven in dit code voorbeeld. Wijzig *proxy server: 8080* zodat deze overeenkomt met de naam van uw lokale proxy server of IP-adres en de poort waarop deze luistert. De waarde moet het voor voegsel http://bevatten, zelfs als u een IP-adres gebruikt.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Configureer vervolgens de Connector Updater-Service voor het gebruik van de proxy door een soort gelijke wijziging aan te brengen in de map C:\Program Files\Microsoft AAD app proxy connector Updater\ApplicationProxyConnectorUpdaterService.exe.config bestand.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Stap 2: Configureer de proxy zodanig dat verkeer van de connector en gerelateerde services wordt door lopen

Er zijn vier aspecten waarmee u rekening moet houden bij de uitgaande proxy:

* Regels voor uitgaande proxy
* Proxy verificatie
* Proxy poorten
* TLS-inspectie

#### <a name="proxy-outbound-rules"></a>Regels voor uitgaande proxy

Sta toegang tot de volgende URL's toe:

| URL | Poort |  Hoe dat wordt gebruikt |
| --- | --- | --- |
| &ast;.msappproxy.net<br>&ast;.servicebus.windows.net | 443/HTTPS | Communicatie tussen de connector en de Application Proxy-cloudservice |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP | De connector gebruikt deze URL's om certificaten te verifiëren. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;.microsoftonline-p.com<br>&ast;.msauth.net<br>&ast;.msauthimages.net<br>&ast;.msecnd.net<br>&ast;.msftauth.net<br>&ast;.msftauthimages.net<br>&ast;.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com | 443/HTTPS | De connector gebruikt deze URL's tijdens het registratieproces. |
| ctldl.windowsupdate.com | 80/HTTP | De connector gebruikt deze URL tijdens het registratieproces. |

Als uw firewall of proxy u toestaat om DNS-acceptatie lijsten te configureren, kunt u verbindingen met \* . msappproxy.net en \* . servicebus.Windows.net toestaan.

Als u geen verbinding kunt maken met FQDN en u in plaats daarvan IP-bereiken moet opgeven, gebruikt u de volgende opties:

* Hiermee staat u de uitgaande toegang van de connector tot alle doelen toe.
* Hiermee wordt de uitgaande toegang van de connector tot alle IP-bereiken van het Azure-Data Center toegestaan. De uitdaging bij het gebruik van de lijst met IP-bereiken van Azure Data Center is dat deze wekelijks wordt bijgewerkt. U moet een proces plaatsen om ervoor te zorgen dat uw toegangs regels dienovereenkomstig worden bijgewerkt. Alleen het gebruik van een subset van de IP-adressen kan ertoe leiden dat uw configuratie wordt verbroken. Als u de meest recente IP-bereiken van Azure Data Center wilt downloaden, gaat u naar [https://download.microsoft.com](https://download.microsoft.com) en zoekt u naar ' Azure IP Ranges and service tags '. Zorg ervoor dat u de relevante Cloud selecteert. De IP-adresbereiken voor de open bare Cloud kunnen bijvoorbeeld worden gevonden met Azure IP-bereiken en service Tags – open bare Cloud. U kunt de cloud van de Amerikaanse overheid vinden door te zoeken naar Azure IP-bereiken en service Tags – Amerikaanse overheids Cloud.

#### <a name="proxy-authentication"></a>Proxy verificatie

Proxy authenticatie wordt momenteel niet ondersteund. Onze huidige aanbeveling is de connector anonieme toegang tot de Internet bestemmingen te geven.

#### <a name="proxy-ports"></a>Proxy poorten

De connector maakt uitgaande op TLS gebaseerde verbindingen met behulp van de methode CONNECT. Met deze methode wordt in feite een tunnel ingesteld via de uitgaande proxy. Configureer de proxy server voor het toestaan van tunneling naar poorten 443 en 80.

> [!NOTE]
> Als Service Bus over HTTPS wordt uitgevoerd, wordt poort 443 gebruikt. Service Bus probeert echter standaard TCP-verbindingen te sturen en terug te vallen op HTTPS als de directe verbinding mislukt.

#### <a name="tls-inspection"></a>TLS-inspectie

Gebruik geen TLS-inspectie voor het connector verkeer, omdat dit problemen veroorzaakt voor het verkeer van de connector. De connector gebruikt een certificaat om te verifiëren bij de toepassings proxy service, en dat certificaat kan verloren gaan tijdens TLS-inspectie.

## <a name="configure-using-a-proxy-between-the-connector-and-backend-application"></a>Configureren met behulp van een proxy tussen de connector en de back-end-toepassing
Het gebruik van een doorstuur proxy voor de communicatie naar de back-end-toepassing kan in sommige omgevingen een speciale vereiste zijn.
Voer de volgende stappen uit om dit in te scha kelen:

### <a name="step-1-add-the-required-registry-value-to-the-server"></a>Stap 1: de vereiste register waarde toevoegen aan de server
1. Als u het gebruik van de standaard proxy wilt inschakelen, voegt u de volgende register waarde (DWORD) toe `UseDefaultProxyForBackendRequests = 1` aan de register sleutel connector configuratie in HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft AAD App Proxy Connector.

### <a name="step-2-configure-the-proxy-server-manually-using-netsh-command"></a>Stap 2: Configureer de proxy server hand matig met de opdracht netsh
1.  Schakel het groeps beleid proxy-instellingen per computer in. Dit vindt u in: Computerconfiguratie\beleid\beheersjablonen\windows-onderdelen\Extern Explorer. Deze moet worden ingesteld in plaats van dat dit beleid moet worden ingesteld op per gebruiker.
2.  Voer uit `gpupdate /force` op de-server of start de server opnieuw op om ervoor te zorgen dat de bijgewerkte groeps beleids instellingen worden gebruikt.
3.  Start een opdracht prompt met verhoogde bevoegdheid met beheerders rechten en voer in `control inetcpl.cpl` .
4.  Configureer de vereiste proxy instellingen. 

Deze instellingen zorgen ervoor dat de connector dezelfde doorstuur proxy gebruikt voor de communicatie met Azure en de back-end-toepassing. Als voor de communicatie tussen de connector en Azure geen doorstuur proxy of een andere doorstuur proxy nodig is, kunt u dit instellen met het wijzigen van de bestands ApplicationProxyConnectorService.exe.config, zoals beschreven in de secties uitgaande proxy's overs Laan of de uitgaande proxy server gebruiken.

> [!NOTE]
> Er zijn verschillende manieren om de Internet proxy in het besturings systeem te configureren. Proxy-instellingen die zijn geconfigureerd via NETSH WINHTTP (uitvoeren `NETSH WINHTTP SHOW PROXY` om te controleren) overschrijven de proxy-instellingen die u in stap 2 hebt geconfigureerd. 

De connector Updater-Service gebruikt ook de computer proxy. Dit gedrag kan worden gewijzigd door het bestand ApplicationProxyConnectorUpdaterService.exe.config te wijzigen.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Problemen met Connector proxy en problemen met de service connectiviteit oplossen

Nu ziet u al het verkeer dat via de proxy stroom loopt. Als u problemen ondervindt, kunt u de volgende informatie over het oplossen van problemen ondervinden.

De beste manier om verbindings problemen met connectors op te sporen en op te lossen is het maken van een netwerk vastleggen tijdens het starten van de connector service. Hier volgen enkele tips voor het vastleggen en filteren van netwerk traceringen.

U kunt het controle programma van uw keuze gebruiken. Voor de doel einden van dit artikel hebben we micro soft Message Analyzer gebruikt.

> [!NOTE]
> [Micro soft Message Analyzer (MMA) is buiten gebruik gesteld](/openspecs/blog/ms-winintbloglp/dd98b93c-0a75-4eb0-b92e-e760c502394f) en de Download pakketten zijn verwijderd van Microsoft.com-sites op 25 2019 november.  Er is op dit moment geen micro soft-vervanging voor micro soft Message Analyzer in ontwikkeling.  Voor een vergelijk bare functionaliteit kunt u overwegen een hulp programma voor netwerk protocol analyse van derden, zoals wireshark, te gebruiken.

De volgende voor beelden zijn specifiek voor Message Analyzer, maar de principes kunnen worden toegepast op elk analyse programma.

### <a name="take-a-capture-of-connector-traffic"></a>Neem een opname van connector verkeer

Voer de volgende stappen uit voor eerste probleem oplossing:

1. Stop de Azure AD-toepassingsproxy-connector service vanuit Services. msc.

   ![Azure AD-toepassingsproxy-connector service in Services. msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Voer Message Analyzer uit als Administrator.
1. Selecteer **lokale tracering starten**.
1. Start de service Azure AD-toepassingsproxy-connector.
1. Stop de netwerk opname.

   ![Scherm afbeelding toont de knop netwerk opname stoppen](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Controleren of het connector verkeer uitgaande proxy's omzeilt

Als u de connector voor de toepassings proxy hebt geconfigureerd om de proxy servers te omzeilen en rechtstreeks verbinding te maken met de toepassings proxy service, wilt u zoeken in het netwerk vastleggen voor mislukte TCP-verbindings pogingen.

Gebruik het filter Message Analyzer om deze pogingen te identificeren. Voer `property.TCPSynRetransmit` in het vak Filter in en selecteer **Toep assen**.

Een SYN-pakket is het eerste pakket dat wordt verzonden om een TCP-verbinding tot stand te brengen. Als dit pakket geen antwoord retourneert, wordt de SYN opnieuw geprobeerd. U kunt het voor gaande filter gebruiken om alle opnieuw verzonden SYNs te bekijken. Vervolgens kunt u controleren of deze SYNs overeenkomen met elk verkeer dat betrekking heeft op de connector.

Als u verwacht dat de connector directe verbindingen met de Azure-Services maakt, geven SynRetransmit-antwoorden op poort 443 een indicatie dat er een probleem is met het netwerk of de firewall.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Controleren of het connector verkeer uitgaande proxy's gebruikt

Als u het verkeer van de toepassings proxy connector hebt geconfigureerd om de proxy servers te passeren, wilt u controleren of er mislukte HTTPS-verbindingen met uw proxy zijn.

Als u de netwerk opname voor deze verbindings pogingen wilt filteren, typt u `(https.Request or https.Response) and tcp.port==8080` in het filter Message Analyzer, waarbij u 8080 vervangt door uw proxy service-poort. Selecteer **Toep assen** om de filter resultaten weer te geven.

In het voor gaande filter worden alleen de HTTPs-aanvragen en reacties van/naar de Proxy poort weer gegeven. U zoekt naar de VERBINDINGS aanvragen die communicatie met de proxy server weer geven. Wanneer dit is gelukt, krijgt u een HTTP OK (200)-antwoord.

Als u andere antwoord codes ziet, zoals 407 of 502, betekent dit dat de proxy verificatie vereist of het verkeer om een andere reden niet toestaat. Op dit moment kunt u uw ondersteunings team voor de proxy server benaderen.

## <a name="next-steps"></a>Volgende stappen

* [Azure AD-toepassingsproxy-connectors begrijpen](application-proxy-connectors.md)
* Als u problemen ondervindt met connector verbindings problemen, kunt u uw vraag stellen op de [pagina micro soft Q&een vraag voor Azure Active Directory](/answers/topics/azure-active-directory.html) of een ticket maken met het ondersteunings team.