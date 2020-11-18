---
title: RDP-shortpath van Windows virtueel bureau blad (preview-versie)
titleSuffix: Azure
description: Het instellen van RDP shortpath (preview) voor virtuele Windows-Bureau bladen.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: aee3f5602d0bd1ff12717d903d662ce4605de61d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683757"
---
# <a name="windows-virtual-desktop-rdp-shortpath-preview"></a>RDP-shortpath van Windows virtueel bureau blad (preview-versie)

> [!IMPORTANT]
> RDP-shortpath is momenteel beschikbaar als open bare preview.
> Deze preview-versie is beschikbaar zonder service level agreement. het wordt niet aanbevolen om deze te gebruiken voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

RDP shortpath is een functie van het virtuele bureau blad van Windows waarmee direct UDP-Trans Port tussen Extern bureaublad client en sessiehost wordt gemaakt. RDP gebruikt dit Trans Port om Extern bureaublad en RemoteApp te leveren en biedt een betere betrouw baarheid en een consistente latentie.

## <a name="key-benefits"></a>Belangrijkste voordelen

* RDP shortpath Trans Port is gebaseerd op zeer efficiënte  [universele frequentie Control Protocol (URCP)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/). URCP verbetert UDP met actieve bewaking van de netwerk omstandigheden en biedt een billijk en volledig koppelings gebruik. URCP werkt op lage vertragings-en verlies niveaus, zoals nodig is voor Extern bureaublad. URCP behaalt de beste prestaties door netwerk parameters dynamisch te leren en protocol te bieden met een mechanisme voor frequentie beheer.
* Met RDP-shortpath wordt de rechtstreekse verbinding tussen Extern bureaublad client en sessiehost tot stand gebracht. Directe connectiviteit vermindert de afhankelijkheid van de virtuele bureau blad-gateways van Windows, verbetert de betrouw baarheid van de verbinding en verhoogt de beschik bare band breedte voor elke gebruikers sessie.
* Het verwijderen van extra relay vermindert de retour tijd, waardoor de gebruikers ervaring met latentie gevoelige toepassingen en invoer methoden wordt verbeterd.
* RDP-shortpath biedt ondersteuning voor het configureren van de prioriteit van [Quality of service (QoS)](./rdp-quality-of-service-qos.md) voor RDP-verbindingen via een DSCP-markering (Differentiated Services Code Point)
* RDP-shortpath Trans Port maakt het mogelijk om [uitgaand netwerk verkeer te beperken](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate) door een snelheids factor voor elke sessie op te geven.

## <a name="connection-security"></a>Verbindingsbeveiliging

RDP-shortpath breidt multi transport mogelijkheden van RDP uit. Er wordt geen reverse Connect-Trans Port vervangen, maar dit wordt aangevuld. Alle eerste sessie brokering wordt beheerd via de Windows-infra structuur voor virtueel bureau blad.

UDP-poort 3390 wordt alleen gebruikt voor het binnenkomende shortpath-verkeer dat wordt geverifieerd via reverse Connect-Trans Port. RDP shortpath-listener negeert alle verbindings pogingen met de listener, tenzij ze overeenkomen met de reverse Connect-sessie.

RDP-shortpath gebruikt een TLS-verbinding tussen de client en de sessie-host met de certificaten van de sessiehost. Het certificaat dat wordt gebruikt voor RDP-versleuteling wordt standaard automatisch gegenereerd door het besturings systeem tijdens de implementatie. Zo nodig kunnen klanten centraal beheerde certificaten implementeren die zijn uitgegeven door de certificerings instantie van de onderneming. Zie [Windows Server-documentatie](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations.md)voor meer informatie over certificaat configuraties.

## <a name="rdp-shortpath-connection-sequence"></a>RDP shortpath-verbindings reeks

Na de installatie van het [Trans Port reverse Connect, maakt](./network-connectivity.md)de client en sessie-host de RDP-verbinding en onderhandelen multi-transport mogelijkheden. Aanvullende stappen die hieronder worden beschreven:

1. De sessie-host verzendt de lijst met de persoonlijke en open bare IPv4-en IPv6-adressen naar de client.
2. De client start de achtergrond thread om een op parallelle UDP gebaseerd Trans Port rechtstreeks naar een van de IP-adressen van de host vast te leggen.
3. Terwijl de client de gegeven IP-adressen doorzoekt, wordt de eerste verbinding tot stand gebracht via het omgekeerde Connect-Trans Port om ervoor te zorgen dat de gebruikers verbinding geen vertraging oploopt.
4. Als de client een directe regel voor de detectie heeft en de firewall configuratie correct is, brengt de client een beveiligde TLS-verbinding tot stand met de sessiehost.
5. Nadat u het shortpath-Trans Port hebt ingesteld, verplaatst RDP alle dynamische virtuele kanalen (DVCs), inclusief externe afbeeldingen, invoer en apparaatomleiding naar het nieuwe Trans Port.
6. Als een firewall of netwerk topologie voor komt dat de client directe UDP-connectiviteit tot stand brengt, wordt RDP voortgezet met een reverse Connect-Trans Port.

In het onderstaande diagram vindt u een overzicht van de netwerk verbinding RDP shortpath.

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="Diagram van RDP shortpath-netwerk verbindingen" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>Vereisten

Voor de ondersteuning van RDP-shortpath moet de Windows virtual desktop-client een directe regel voor de sessie host hebben. U kunt een directe gezichts lijn krijgen met behulp van een van de volgende technologieën:

* [ExpressRoute persoonlijke peering](../expressroute/expressroute-circuit-peerings.md)
* [Site-naar-site VPN (op IPsec gebaseerd)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Punt-naar-site-VPN (op IPsec gebaseerd)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Toewijzing van openbaar IP-adres](../virtual-network/virtual-network-public-ip-address.md)

Als u andere VPN-typen gebruikt om verbinding te maken met het virtuele Azure-netwerk, raden we u aan op UDP gebaseerde VPN te gebruiken voor de beste resultaten. Hoewel het meren deel van VPN-oplossingen op basis van TCP alle IP-pakketten, inclusief UDP, inkapselt, voegen ze overgenomen overhead toe voor TCP-congestie beheer waardoor RDP-prestaties zouden vertragen.

De directe regel van het gezichts vermogen houdt in dat firewalls UDP-poort 3390 niet blok keren en dat de client rechtstreeks verbinding kan maken met de sessiehost.

## <a name="enabling-rdp-shortpath-preview"></a>RDP shortpath-preview inschakelen

Als u wilt deel nemen aan de preview van RDP-shortpath, moet u RDP shortpath-listener inschakelen op de sessiehost. U kunt RDP-shortpath inschakelen op een wille keurig aantal sessie-hosts die in uw omgeving worden gebruikt. Het is niet nodig om RDP-shortpath in te scha kelen op alle hosts in de groep.
Als u shortpath-listener wilt inschakelen, moet u de volgende register waarden configureren:

> [!WARNING]
> Er kunnen ernstige problemen optreden als u het REGI ster onjuist wijzigt met behulp van de REGI ster-editor of met een andere methode. Deze problemen kunnen ertoe leiden dat u het besturings systeem opnieuw moet installeren. Microsoft kan niet garanderen dat deze problemen kunnen worden opgelost. Wijzig het register op eigen risico.

1. Start Regedit.exe op de sessiehost en navigeer vervolgens naar de volgende locatie:

    ```cmd
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
    ```

2. Maak een nieuwe **DWORD** -waarde met de naam **fUseUdpPortRedirector** en stel deze in op **1** (decimaal)
3. Maak een nieuwe **DWORD** -waarde met de naam **UdpPortNumber** en stel deze in op **3390** (decimaal)
4. Sluit de REGI ster-editor af.
5. Session Host opnieuw opstarten

U kunt ook de volgende cmdlets uitvoeren in een Power shell-venster met verhoogde bevoegdheid om deze register waarden in te stellen:

```powershell
$WinstationsKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations'
New-ItemProperty -Path $WinstationsKey -Name 'fUseUdpPortRedirector' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 1 -Force
New-ItemProperty -Path $WinstationsKey -Name 'UdpPortNumber' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 3390 -Force
```

U kunt ook Power shell gebruiken om groeps beleid te configureren

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'fUseUdpPortRedirector' -Value 1 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'UdpPortNumber' -Value 3390 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
```

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>Windows Defender firewall configureren met geavanceerde beveiliging

Gebruik het knoop punt Windows Defender Firewall met geavanceerde beveiliging in de MMC-module groepsbeleid beheer om firewall regels te maken om binnenkomend netwerk verkeer voor RDP-shortpath toe te staan.

1. Open de console Groepsbeleidbeheer voor [Windows Defender Firewall met geavanceerde beveiliging](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security).
2. Selecteer in het navigatie deel venster **regels voor binnenkomende verbindingen**.
3. Selecteer **actie** en selecteer vervolgens **nieuwe regel**.
4. Selecteer op de pagina **regel type** van de wizard Nieuwe regel voor binnenkomende verbindingen de optie **aangepast** en selecteer vervolgens **volgende**.
5. Selecteer op de pagina **programma** **Dit** programmapad en typ% System root% \system32\svchost.exe en selecteer vervolgens **volgende**.
6. Selecteer op de pagina **protocollen en poorten** het type UDP-protocol. Selecteer in de **lokale poort** specifieke poorten en typ 3390.
7. Op de pagina **bereik** kunt u opgeven dat de regel alleen van toepassing is op netwerk verkeer naar of van de IP-adressen die op deze pagina zijn ingevoerd. Configureer zo nodig voor uw ontwerp en selecteer **volgende**.
8. Selecteer op de pagina **actie** **de optie de verbinding toestaan** en selecteer vervolgens **volgende**.
9. Selecteer op de pagina **profiel** de typen netwerk locatie waarop deze regel van toepassing is en selecteer vervolgens **volgende**.
10. Typ op de pagina **naam** een naam en beschrijving voor de regel en selecteer vervolgens **volt ooien**.

U kunt controleren of de nieuwe regel overeenkomt met de onderstaande scherm afbeeldingen: :::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="scherm afbeelding van het tabblad Algemeen voor de firewall configuratie voor RDP shortpath-netwerk verbindingen" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="Scherm afbeelding van het tabblad Program Ma's en services voor de firewall configuratie voor RDP shortpath-netwerk verbindingen" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="Scherm afbeelding van het tabblad Protocollen en poorten voor de firewall configuratie voor RDP shortpath-netwerk verbindingen" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

U kunt ook Power shell gebruiken om Windows Firewall te configureren:

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>Power shell gebruiken om Windows Defender firewall te configureren

U kunt ook Power shell gebruiken om groeps beleid te configureren

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

## <a name="configuring-azure-network-security-group"></a>Azure-netwerk beveiligings groep configureren

Als u toegang wilt verlenen tot de RDP shortpath-listener over netwerk beveiligings grenzen, moet u de Azure-netwerk beveiligings groep zodanig configureren dat binnenkomende UDP-poort 3390 wordt toegestaan.
Volg de [documentatie voor de netwerk beveiligings groep](../virtual-machines/windows/nsg-quickstart-portal.md) om een binnenkomende beveiligings regel te maken die verkeer met de volgende para meters toestaat:

* **Bron**  -  **Een** of het IP-bereik waar de clients zich bevinden
* **Poort bereik van bron** -* *\** _ _ **bestemming**  -  **Any**
* Poortbereiken van **doel**  -  **3390**
* **Protocol**  -  **UDP**
* **Actie**  -  **Toestaan**
* Wijzig desgewenst de **prioriteit**. De prioriteit is van invloed op de volg orde waarin regels worden toegepast: de lagere numerieke waarde, de eerdere regel wordt toegepast.
* **Naam** -- **RDP-shortpath**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>RDP-shortpath voor een specifiek subnet uitschakelen

Als u specifieke subnetten wilt blok keren van het gebruik van het RDP shortpath-Trans Port, kunt u extra netwerk beveiligings groepen configureren die de bron-IP-adresbereiken aangeven.

## <a name="verifying-the-connectivity"></a>De connectiviteit controleren

### <a name="using-connection-information-dialog"></a>Dialoog venster verbindings gegevens gebruiken

Als u wilt controleren of verbindingen gebruikmaken van RDP-shortpath, opent u het dialoog venster verbindings gegevens door te klikken op het pictogram antenne in de werk balk verbinding.

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="Afbeelding van Verbinding met extern bureaublad balk":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="Afbeelding van het dialoog venster Info over Verbinding met extern bureaublad":::

### <a name="using-event-logs"></a>Gebeurtenis Logboeken gebruiken

Controleren of de sessie gebruikmaakt van RDP shortpath Trans Port:

1. Maak verbinding met het bureau blad van de virtuele machine met behulp van de Windows-client voor virtueel bureau blad.
2. Start de Logboeken en ga naar het volgende knoop punt: **Logboeken toepassingen en Services > micro soft > Windows > RemoteDesktopServices-RdpCoreCDV > operationeel**
3. Zoek naar gebeurtenis-ID 131 om te bepalen of RDP shortpath-Trans Port wordt gebruikt.

### <a name="using-log-analytics-to-verify-shortpath-connectivity"></a>De shortpath-verbinding controleren met behulp van Log Analytics

Als u [Azure log Analytics](./diagnostics-log-analytics.md)gebruikt, kunt u verbindingen bewaken door de WVDConnections- [tabel](/azure/azure-monitor/reference/tables/wvdconnections)op te vragen. Een kolom met de naam UdpUse, geeft aan of RDP-stack van Windows virtueel bureau blad gebruikmaakt van het UDP-protocol voor de huidige gebruikers verbinding.
De mogelijke waarden zijn:

* **0** : gebruikers verbinding maakt geen gebruik van RDP-shortpath
* **1** -gebruikers verbinding maakt gebruik van RDP-shortpath
  
In de volgende query lijst kunt u verbindings gegevens bekijken. U kunt deze query uitvoeren in de [log Analytics query-editor](../azure-monitor/log-query/get-started-portal.md#write-and-run-basic-queries). Vervang elke query door `userupn` de UPN van de gebruiker die u wilt zoeken.

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="verify-shortpath-listener"></a>Shortpath-listener controleren

Als u wilt controleren of de UDP-listener is ingeschakeld, gebruikt u de volgende Power shell-opdracht op de sessiehost:

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

Als deze functie is ingeschakeld, ziet u de uitvoer als de volgende:

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

Als er een conflict optreedt, kunt u het proces identificeren dat de poort in beslag neemt met de volgende opdracht

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>RDP-shortpath uitschakelen

In sommige gevallen moet u RDP shortpath-Trans Port mogelijk uitschakelen. U kunt RDP-shortpath uitschakelen met behulp van groeps beleid.

### <a name="disabling-rdp-shortpath-on-the-client"></a>RDP-shortpath op de client uitschakelen

Als u RDP-shortpath voor een specifieke client wilt uitschakelen, kunt u de volgende groepsbeleid gebruiken om de UDP-ondersteuning uit te scha kelen:

1. Voer **gpedit. msc** op de client uit.
2. Navigeer naar **computer configuratie > beheer sjablonen > Windows-onderdelen > Extern bureaublad-services > verbinding met extern bureaublad-client**.
3. Stel de instelling **UDP op client** uitschakelen in op **ingeschakeld**

### <a name="disabling-rdp-shortpath-on-the-session-host"></a>RDP-shortpath op de sessiehost uitschakelen

Als u RDP-shortpath voor een specifieke sessiehost wilt uitschakelen, kunt u de volgende groepsbeleid gebruiken om de UDP-ondersteuning uit te scha kelen:

1. Voer **gpedit. msc** uit op de sessiehost.
2. Navigeer naar **computer configuratie > beheer sjablonen > Windows-onderdelen > Extern bureaublad-services > verbinding met extern bureaublad host**>-verbindingen.
3. Stel de instelling **RDP-transport protocollen selecteren** alleen in op **TCP**

## <a name="public-preview-feedback"></a>Feedback voor open bare preview

We horen graag van u over uw ervaringen met deze open bare preview.
* Voor vragen, aanvragen, opmerkingen en andere feedback [gebruikt u dit feedback formulier](https://aka.ms/RDPShortpathFeedback).

## <a name="next-steps"></a>Volgende stappen

* Zie wat is Windows Virtual Desktop- [netwerk](network-connectivity.md)connectiviteit? voor meer informatie over de Windows-verbinding met virtueel bureau blad-netwerken.
* Als u aan de slag wilt gaan met Quality of Service (QoS) voor virtueel bureau blad van Windows, raadpleegt u [implementing Quality of service (QoS) voor Windows virtueel bureau blad](rdp-quality-of-service-qos.md).
