---
title: Werken met CLI-opdrachten voor Defender for IoT
description: In dit artikel wordt Defender beschreven voor IoT CLI-opdrachten voor Sens oren en on-premises beheer consoles.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 93efc89722d3152d92b6f8c8038deaa566741f7c
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636557"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>Werken met CLI-opdrachten voor Defender for IoT

In dit artikel worden CLI-opdrachten voor Sens oren en on-premises beheer consoles beschreven. De opdrachten zijn toegankelijk voor de volgende gebruikers:

- Beheerder
- Cyber-x 
- Ondersteuning

Als u met de CLI wilt werken, maakt u verbinding met een Terminal. Bijvoorbeeld terminal naam `Putty` en `Support` gebruiker. 

## <a name="create-local-alert-exclusion-rules"></a>Regels voor het uitsluiten van een lokale waarschuwing maken

U kunt een regel voor het uitsluiten van een lokale waarschuwing maken door de volgende opdracht in te voeren in de CLI:

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

De volgende kenmerken kunnen worden gebruikt met de regels voor uitsluitingen van waarschuwingen:

| Kenmerk | Beschrijving |
|--|--|
| [-h] | De Help-informatie voor de opdracht afdrukken. |
| -n naam | De naam van de regel die wordt gemaakt. |
| [-TS-tijden] | De tijds Panne waarvoor de regel actief is. Dit moet worden opgegeven als:<br />`xx:yy-xx:yy`<br />U kunt meer dan één tijds periode definiëren door een komma ertussen te gebruiken. Bijvoorbeeld: `xx:yy-xx:yy, xx:yy-xx:yy`. |
| [-DIR richting] | De richting waarin de regel wordt toegepast. Dit moet worden opgegeven als:<br />`both | src | dst` |
| [-ontwikkel apparaten] | Het IP-adres en het adres type van de apparaten die moeten worden uitgesloten door de regel, opgegeven als:<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a ALERTs] | De naam van de waarschuwing die door de regel wordt uitgesloten:<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>Regels voor uitsluiting van lokale waarschuwingen toevoegen

U kunt regels voor het uitsluiten van lokale waarschuwingen toevoegen door de volgende opdracht in te voeren in de CLI:

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

De hier gebruikte kenmerken zijn hetzelfde als de kenmerken die worden beschreven in de sectie regels voor het maken van een lokale waarschuwing. Het verschil in het gebruik is dat hier de kenmerken worden toegepast op de bestaande regels.

## <a name="show-local-alert-exclusion-rules"></a>Regels voor het uitsluiten van lokale waarschuwingen weer geven

Voer de volgende opdracht in om de bestaande lijst met uitsluitings regels weer te geven:

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>Regels voor het uitsluiten van lokale waarschuwingen verwijderen

U kunt een bestaande regel voor het uitsluiten van een waarschuwing verwijderen door de volgende opdracht in te voeren:

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Het volgende kenmerk kan worden gebruikt met de regels voor uitsluitingen van waarschuwingen:

| Kenmerk | Beschrijving|
| --------- | ---------------------------------- |
| -n naam | De naam van de regel die moet worden verwijderd. |

## <a name="sync-time-from-the-ntp-server"></a>Synchronisatie tijd van de NTP-server

U kunt een tijd synchronisatie van een opgegeven NTP-server in-of uitschakelen.

### <a name="enable-ntp-sync"></a>NTP-synchronisatie inschakelen

Voer de volgende opdracht in om periodiek de tijd op te halen van de opgegeven NTP-server:

```azurecli-interactive
ntp enable IP
```

Het kenmerk dat u kunt definiëren in de opdracht, is het IP-adres van de NTP-server.

### <a name="disable-ntp-sync"></a>NTP-synchronisatie uitschakelen

Voer de volgende opdracht in om de tijd synchronisatie met de opgegeven NTP-server uit te scha kelen:

```azurecli-interactive
ntp disable IP
```

Het kenmerk dat u kunt definiëren in de opdracht, is het IP-adres van de NTP-server.

## <a name="network-configuration"></a>Netwerkconfiguratie

De volgende tabel beschrijft de opdrachten die beschikbaar zijn voor het configureren van uw netwerk opties voor Azure Defender voor IoT:

|Name|Opdracht|Beschrijving|
|-----------|-------|-----------|
|Ping|`ping IP`| Een adres pingen buiten het Defender voor IoT-platform.|
|Blink|`network blink`| Zoek een verbinding door ervoor te zorgen dat de interface licht knippert. |
|Het netwerk opnieuw configureren |`network edit-settings`| Een wijziging in de para meters van de netwerk configuratie in te scha kelen. |
|Netwerk instellingen weer geven |`network list`|Hiermee worden de para meters van de netwerk adapter weer gegeven. |
|De netwerk configuratie valideren |`network validate` |Geeft de instellingen van het uitvoer netwerk. <br /> <br />Bijvoorbeeld: <br /> <br />Huidige netwerk instellingen: <br /> Interface: ETH0 <br /> IP: 10.100.100.1 <br />subnet: 255.255.255.0 <br />standaard gateway: 10.100.100.254 <br />DNS: 10.100.100.254 <br />monitor interfaces: eth1|
|Certificaat importeren |`certificate import FILE` |Hiermee wordt het HTTPS-certificaat geïmporteerd. U moet het volledige pad opgeven dat leidt naar een \* . crt-bestand. |
|De datum weer geven |`date` |Retourneert de huidige datum op de host in GMT-indeling. |

## <a name="filter-network-configurations"></a>Netwerk configuraties filteren

Met de `network capture-filter` opdracht kunnen beheerders netwerk verkeer elimineren dat niet hoeft te worden geanalyseerd. U kunt verkeer filteren met behulp van een opgenomen lijst of een uitsluitings lijst.

```azurecli-interactive
network capture-filter
```

Nadat u de opdracht hebt ingevoerd, wordt u gevraagd de volgende vraag:

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

Selecteer `Y` deze optie om een nano bestand te openen waar u een apparaat, kanaal, poort en subset kunt toevoegen volgens de volgende syntaxis:

| Kenmerk | Beschrijving |
|--|--|
| 1.1.1.1 | Bevat al het verkeer voor dit apparaat. |
| 1.1.1.1, 2.2.2.2 | Omvat al het verkeer voor dit kanaal. |
| 1.1.1, 2.2.2 | Bevat al het verkeer voor dit subnet. |

Scheid argumenten door een rij te verwijderen.

Wanneer u een apparaat, kanaal of subnet opneemt, verwerkt de sensor alle geldige verkeer voor dat argument, met inbegrip van poorten en verkeer dat doorgaans niet kan worden verwerkt.

Vervolgens wordt u de volgende vraag gesteld:

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

Selecteer `Y` deze optie om een nano bestand te openen waarmee u een apparaat, kanaal, poort en subsets kunt toevoegen, afhankelijk van de volgende syntaxis:

| Kenmerk | Beschrijving |
|--|--|
| 1.1.1.1 | Hiermee sluit u alle verkeer voor dit apparaat uit. |
| 1.1.1.1, 2.2.2.2 | Sluit al het verkeer voor dit kanaal uit, wat betekent dat alle verkeer tussen twee apparaten. |
| 1.1.1.1, 2.2.2.2, 443 | Hiermee wordt alle verkeer voor dit kanaal op poort uitgesloten. |
| 1.1.1 | Hiermee wordt het verkeer voor dit subnet uitgesloten. |
| 1.1.1, 2.2.2 | Sluit alle verkeer voor tussen subnetten. |

Scheid argumenten door een rij te verwijderen.

Wanneer u een apparaat, kanaal of subnet uitsluit, wordt alle geldige verkeer voor dat argument uitgesloten door de sensor.

### <a name="ports"></a>Poorten

UDP-en TCP-poorten voor al het verkeer opnemen of uitsluiten.

>`502`: Eén poort

>`502,443`: beide poorten

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>Onderdelen

U wordt de volgende vraag gesteld:

>`In which component do you wish to apply this capture filter?`

U hebt de volgende opties:  `all` ,, `dissector` ,, `collector` `statistics-collector` `rpc-parser` of `smb-parser` .

In de meeste gevallen selecteert u `all` .

### <a name="custom-base-capture-filter"></a>Aangepast basis opname filter

Het basis opname filter is de basis lijn voor de onderdelen. Het filter bepaalt bijvoorbeeld welke poorten beschikbaar zijn voor het onderdeel.

Selecteer deze optie `Y` voor de volgende opties. Alle filters worden toegevoegd aan de basis lijn nadat de wijzigingen zijn ingesteld. Als u een wijziging aanbrengt, wordt de bestaande basis lijn overschreven.

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

Als u ervoor kiest om een subnet, zoals 1.1.1, uit te sluiten:

- `internal` alleen dat subnet wordt uitgesloten.

- `all-connected` Sluit dat subnet en alle verkeer van en naar dat subnet.

We raden u aan om te selecteren `internal` .

> [!NOTE]
> Uw keuzes worden gebruikt voor alle filters in het hulp programma en zijn niet afhankelijk van de sessie. Met andere woorden, u kunt nooit `internal`   voor sommige filters en  `all-connected`   voor anderen kiezen.

### <a name="comments"></a>Opmerkingen

U kunt filters weer geven in  ```/var/cyberx/properties/cybershark.properties``` :

- **Statistieken-Collector**:  `bpf_filter property` in ```/var/cyberx/properties/net.stats.collector.properties```

- **Dissector**: `override.capture_filter`   eigenschap in ```/var/cyberx/properties/cybershark.properties```

- **RPC-parser**: `override.capture_filter` eigenschap in ```/var/cyberx/properties/rpc-parser.properties```

- **SMB-parser**:  `override.capture_filter`   eigenschap in ```/var/cyberx/properties/smb-parser.properties```

- **Collector**: `general.bpf_filter`   eigenschap in ```/var/cyberx/properties/collector.properties```

U kunt de standaard configuratie herstellen door de volgende code in te voeren voor de gebruiker cyberx:

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>Hosts voor client en server definiëren

Als Defender voor IoT de client en Server hosts niet automatisch detecteren, voert u de volgende opdracht in om de client-en Server-hosts in te stellen:

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

U kunt de volgende kenmerken gebruiken met de `directions` opdracht:

| Kenmerk | Beschrijving |
|--|--|
| [-h] | Help-informatie voor de opdracht afdrukken. |
| [--id-id] | De server-id. |
| [--poort poort] | De server poort. |
| [--verwijderen] | Hiermee verwijdert u een client of een server host uit de lijst. |
| [--toevoegen] | Hiermee wordt een client of een server host aan de lijst toegevoegd. |
| [--TCP] | Gebruik TCP bij communicatie met deze host. |
| [--UDP] | Gebruik UDP bij communicatie met deze host. |

## <a name="system-actions"></a>Systeem acties
De volgende tabel beschrijft de opdrachten die beschikbaar zijn voor het uitvoeren van verschillende systeem acties binnen Defender voor IoT:

|Name|Code|Description|
|----|----|-----------|
|De datum weer geven|`date`|Retourneert de huidige datum op de host in GMT-indeling.|
|De host opnieuw opstarten|`system reboot`|Het hostapparaat wordt opnieuw opgestart.|
|De host afsluiten|`system shutdown`|De host wordt afgesloten.|
|Back-up maken van het systeem|`system backup`|Initieert een onmiddellijke back-up (een niet-geplande back-up).|
|Het systeem herstellen vanuit een back-up|`system restore`|Hiermee herstelt u de meest recente back-up.|
|De back-upbestanden weer geven|`system backup-list`|Een lijst met de beschik bare back-upbestanden.|
|De status van alle Defender voor IoT-platform services weer geven|`system sanity`|Controleert de prestaties van het systeem door de huidige status van alle Defender voor IoT-platform services weer te geven.|
|De software versie weer geven|`system version`|Geeft de versie weer van de software die momenteel op het systeem wordt uitgevoerd.|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>SSL-en TLS-certificaten implementeren op apparaten

Voer de volgende opdracht in om SSL-en TLS-ondernemings certificaten te importeren in de CLI:

```azurecli-interactive
cyberx-xsense-certificate-import
```
Als u het hulp programma wilt gebruiken, moet u de certificaat bestanden uploaden naar het apparaat. U kunt dit doen via hulpprogram ma's zoals WinSCP of wget. 

De opdracht ondersteunt de volgende invoer vlaggen:

| Vlag | Beschrijving |
|--|--|
| -h | Hiermee wordt de Help-syntaxis van de opdracht regel weer gegeven. |
| --CRT | Het pad naar het certificaat bestand (. CRT-extensie). |
| --sleutel | Het \* . key-bestand. De sleutel lengte moet mini maal 2.048 bits zijn. |
| --keten | Het pad naar het certificaat keten bestand (optioneel). |
| --Pass | Wachtwoordzin die wordt gebruikt voor het versleutelen van het certificaat (optioneel). |
| --set met wachtwoordzin | De standaard waarde is **False**, **ongebruikt**. <br />Stel deze **waarde** in op True als u de vorige wachtwoordzin wilt gebruiken die is opgegeven met het vorige certificaat (optioneel). |  |

Wanneer u het hulp programma gebruikt:

- Controleer of de certificaat bestanden kunnen worden gelezen op het apparaat. 

- Bevestig het toestel domein (zoals het in het certificaat wordt weer gegeven) met uw DNS-server en het bijbehorende IP-adres. 
    
## <a name="see-also"></a>Zie ook

[Defender voor IoT API-sensor-en beheer console-Api's](references-work-with-defender-for-iot-apis.md)
