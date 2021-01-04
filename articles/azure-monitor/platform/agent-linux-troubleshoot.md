---
title: Problemen met Azure Log Analytics Linux-agent oplossen | Microsoft Docs
description: Beschrijf de symptomen, oorzaken en oplossingen voor de meest voorkomende problemen met de Log Analytics agent voor Linux in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 2b811b1ace646cc4e0a93b937fbb90cfbf7aec0f
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704891"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Problemen met de Log Analytics-agent voor Linux oplossen 

In dit artikel vindt u informatie over het oplossen van problemen die u mogelijk ondervindt met de Log Analytics-agent voor Linux in Azure Monitor en worden mogelijke oplossingen voorgesteld om deze op te lossen.

Als geen van deze stappen voor u werkt, zijn de volgende ondersteunings kanalen ook beschikbaar:

* Klanten met premier-ondersteunings voordelen kunnen een ondersteunings aanvraag openen met [premier](https://premier.microsoft.com/).
* Klanten met ondersteunings overeenkomsten voor Azure kunnen een ondersteunings aanvraag openen [in de Azure Portal](https://manage.windowsazure.com/?getsupport=true).
* Diagnose OMI-problemen met de [Omi-gids voor probleem oplossing](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Een [github-probleem oplossen](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Ga naar de Log Analytics feedback pagina om de verzonden ideeën en bugs te bekijken [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) of een nieuwe bestand te openen. 

## <a name="log-analytics-troubleshooting-tool"></a>Hulp programma voor probleem oplossing Log Analytics

Het hulp programma voor probleem oplossing van Log Analytics agent Linux is een script dat is ontworpen om problemen met de Log Analytics agent te vinden en te onderzoeken. Het wordt bij de installatie automatisch opgenomen in de-agent. Het uitvoeren van het hulp programma moet de eerste stap zijn bij het vaststellen van een probleem.

### <a name="how-to-use"></a>Het gebruik van
U kunt het hulp programma voor probleem oplossing uitvoeren door de volgende opdracht in een Terminal venster op een computer met de Log Analytics-agent te plakken: `sudo /opt/microsoft/omsagent/bin/troubleshooter`

### <a name="manual-installation"></a>Handmatige installatie
Het hulp programma voor probleem oplossing wordt automatisch opgenomen bij de installatie van de Log Analytics-agent. Als de installatie echter op enigerlei wijze mislukt, kan deze ook hand matig worden geïnstalleerd door de volgende stappen uit te voeren.

1. Kopieer de probleem Oplosser-bundel naar uw computer: `wget https://raw.github.com/microsoft/OMS-Agent-for-Linux/master/source/code/troubleshooter/omsagent_tst.tar.gz`
2. De bundel uitpakken: `tar -xzvf omsagent_tst.tar.gz`
3. Voer de hand matige installatie uit: `sudo ./install_tst`

### <a name="scenarios-covered"></a>Scenario's die worden behandeld
Hieronder ziet u een lijst met scenario's die worden gecontroleerd door het hulp programma voor probleem oplossing:

1. De agent is beschadigd. heartbeat werkt niet goed
2. De agent start niet, kan geen verbinding maken met de logboek analyse Services
3. Syslog-agent werkt niet
4. De agent heeft een hoog CPU-of geheugen gebruik
5. Agent met installatie problemen
6. Aangepaste logboeken van agents werken niet
7. Agent logboeken verzamelen

Bekijk onze [github-documentatie](https://github.com/microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting-Tool.md)voor meer informatie.

 >[!NOTE]
 >Voer het hulp programma logboek verzamelaar uit wanneer u een probleem ondervindt. Als u de logboeken in eerste instantie hebt, helpt het ondersteunings team het probleem sneller op te lossen.

## <a name="purge-and-re-install-the-linux-agent"></a>De Linux-agent opschonen en Re-Install

We hebben gezien dat een schone herinstallatie van de agent de meeste problemen zal oplossen. Dit is in feite de eerste suggestie van ondersteuning om de agent in een uncurropted-status van ons ondersteunings team te krijgen. Als u de probleem Oplosser uitvoert, logboek verzamelen en een schone herinstallatie probeert uit te voeren, worden problemen sneller opgelost.

1. Het opschoon script downloaden:
- `$ wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/purge_omsagent.sh`
2. Voer het opschoon script uit (met sudo-machtigingen):
- `$ sudo sh purge_omsagent.sh`

## <a name="important-log-locations-and-log-collector-tool"></a>Belang rijke logboek locaties en logboek verzamelaar-hulp programma

 File | Pad
 ---- | -----
 Log Analytics agent voor Linux-logboek bestand | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Logboek bestand voor configuratie van Log Analytics agent | `/var/opt/microsoft/omsconfig/omsconfig.log`

 U wordt aangeraden ons hulp programma voor logboek verzamelaar te gebruiken voor het ophalen van belang rijke logboeken voor het oplossen van problemen of voordat u een GitHub-probleem verzendt. Meer informatie over het hulp programma vindt u [hier](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Belang rijke configuratie bestanden

 Categorie | Bestandslocatie
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf` of `/etc/rsyslog.conf` of `/etc/rsyslog.d/95-omsagent.conf`
 Prestaties, nagios, zabbix, Log Analytics uitvoer en algemene agent | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Aanvullende configuraties | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Het bewerken van configuratie bestanden voor prestatie meter items en syslog wordt overschreven als de verzameling is geconfigureerd in het [menu data log Analytics geavanceerde instellingen](./agent-data-sources.md#configuring-data-sources) in de Azure portal voor uw werk ruimte. Als u de configuratie voor alle agents wilt uitschakelen, schakelt u verzameling uit Log Analytics **Geavanceerde instellingen** of voor één agent het volgende uit:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Installatie fout codes

| Foutcode | Betekenis |
| --- | --- |
| NOT_DEFINED | Omdat de benodigde afhankelijkheden niet zijn geïnstalleerd, wordt de auoms-invoeg toepassing voor controle niet geïnstalleerd | De installatie van auoms is mislukt, de installatie van het pakket is gecontroleerd. |
| 2 | Er is een ongeldige optie aan de shell-bundel door gegeven. Uitvoeren `sudo sh ./omsagent-*.universal*.sh --help` voor gebruik |
| 3 | Er is geen optie aan de shell-bundel door gegeven. Uitvoeren `sudo sh ./omsagent-*.universal*.sh --help` voor gebruik. |
| 4 | Ongeldig pakket type of ongeldige proxy instellingen. omsagent-*rpm*. sh-pakketten kunnen alleen worden geïnstalleerd op installaties op basis van rpm en omsagent-*deb*. sh-pakketten kunnen alleen worden geïnstalleerd op Debian-systemen. Het is raadzaam om het universele installatie programma van de [meest recente release](../learn/quick-collect-linux-computer.md#install-the-agent-for-linux)te gebruiken. Controleer ook de verificatie van uw proxy-instellingen. |
| 5 | De shell bundel moet worden uitgevoerd als root of er is een 403-fout geretourneerd tijdens het onboarden. Voer de opdracht uit met `sudo` . |
| 6 | Ongeldige pakket architectuur of er is een fout opgetreden 200 fout opgetreden tijdens het voorbereiden; omsagent-*x64.sh-pakketten kunnen alleen worden geïnstalleerd op 64-bits systemen en omsagent-x86.sh-* pakketten kunnen alleen worden geïnstalleerd op 32-bits systemen. Down load het juiste pakket voor uw architectuur vanuit de [nieuwste versie](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Installatie van het OMS-pakket is mislukt. Bekijk de uitvoer van de opdracht voor de hoofd fout. |
| 19 | Installatie van OMI-pakket is mislukt. Bekijk de uitvoer van de opdracht voor de hoofd fout. |
| 20 | Installatie van het SCX-pakket is mislukt. Bekijk de uitvoer van de opdracht voor de hoofd fout. |
| 21 | De installatie van de provider kits is mislukt. Bekijk de uitvoer van de opdracht voor de hoofd fout. |
| 22 | Installatie van gebundeld pakket is mislukt. Bekijk de uitvoer van de opdracht voor de hoofd fout |
| 23 | Het SCX-of OMI-pakket is al geïnstalleerd. Gebruik `--upgrade` in plaats van `--install` om de shell-bundel te installeren. |
| 30 | Interne bundel fout. Een [github-probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met details van de uitvoer bestand. |
| 55 | Niet-ondersteunde openssl-versie of kan geen verbinding maken met Azure Monitor of met dpkg is een vergrendeld of ontbrekend krul-programma. |
| 61 | De python ctypes-bibliotheek ontbreekt. Installeer de python ctypes-bibliotheek of het-pakket (python-ctypes). |
| 62 | Ontbrekend tar-programma, installeer tar. |
| 63 | Het programma sed ontbreekt, installeer sed. |
| 64 | Het krul programma ontbreekt, installeer krul. |
| 65 | Ontbrekend gpg-programma, installeert u gpg. |

## <a name="onboarding-error-codes"></a>Fout codes voor onboarding

| Foutcode | Betekenis |
| --- | --- |
| 2 | Er is een ongeldige optie aan het omsadmin-script door gegeven. Uitvoeren `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` voor gebruik. |
| 3 | Er is een ongeldige configuratie aan het omsadmin-script door gegeven. Uitvoeren `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` voor gebruik. |
| 4 | Er is een ongeldige proxy aan het omsadmin-script door gegeven. Controleer de proxy en Raadpleeg onze [documentatie voor het gebruik van een HTTP-proxy](log-analytics-agent.md#firewall-requirements). |
| 5 | 403 HTTP-fout ontvangen van Azure Monitor. Zie de volledige uitvoer van het script omsadmin voor meer informatie. |
| 6 | Er is een niet-200 HTTP-fout ontvangen van Azure Monitor. Zie de volledige uitvoer van het script omsadmin voor meer informatie. |
| 7 | Kan geen verbinding maken met Azure Monitor. Zie de volledige uitvoer van het script omsadmin voor meer informatie. |
| 8 | Fout bij onboarding van Log Analytics-werk ruimte. Zie de volledige uitvoer van het script omsadmin voor meer informatie. |
| 30 | Interne script fout. Een [github-probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met details van de uitvoer bestand. |
| 31 | Fout bij het genereren van agent-ID. Een [github-probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met details van de uitvoer bestand. |
| 32 | Fout bij het genereren van certificaten. Zie de volledige uitvoer van het script omsadmin voor meer informatie. |
| 33 | Fout bij het genereren van de omsconfig voor de configuratie. Een [github-probleem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) met details van de uitvoer bestand. |
| 34 | Het script voor het genereren van de configuratie is niet aanwezig. Probeer het opnieuw met `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>` . |

## <a name="enable-debug-logging"></a>Logboek registratie voor fout opsporing inschakelen
### <a name="oms-output-plugin-debug"></a>Fout opsporing van invoeg toepassing OMS-uitvoer
 Met vloeiende ondersteuning kunt u voor specifieke logboek registratie niveaus verschillende logboek niveaus opgeven voor invoer en uitvoer. Als u een ander logboek niveau voor OMS-uitvoer wilt opgeven, bewerkt u de algemene agent configuratie op `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` .  

 Wijzig in de invoeg toepassing OMS, vóór het einde van het configuratie bestand, de `log_level` eigenschap van `info` in `debug` :

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

Met logboek registratie voor fout opsporing kunt u batch-uploads zien die worden Azure Monitor gescheiden door type, het aantal gegevens items en de gebruikte tijd voor verzen ding:

*Voor beeld van debug-logboek:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Uitgebreide uitvoer
In plaats van de OMS-uitvoer-invoeg toepassing te gebruiken, kunt u ook rechtstreeks gegevens items uitvoeren naar `stdout` , die zichtbaar zijn in het logboek bestand van de log Analytics-agent voor Linux.

In het configuratie bestand Log Analytics algemene agent op `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` voert u een opmerking uit in de invoeg toepassing voor de OMS-uitvoer door een voor elke regel toe te voegen `#` :

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Verwijder onder de invoeg toepassing voor uitvoer de opmerking de volgende sectie door de `#` voor zijde van elke regel te verwijderen:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Probleem: kan geen verbinding maken via een proxy met Azure Monitor

### <a name="probable-causes"></a>Mogelijke oorzaken
* De proxy die tijdens de onboarding is opgegeven, is onjuist
* De service-eind punten Azure Monitor en Azure Automation zijn niet opgenomen in de lijst met goedgekeurde Services in uw Data Center 

### <a name="resolution"></a>Oplossing
1. Onboarding voor Azure Monitor met de Log Analytics-agent voor Linux met behulp van de volgende opdracht met de optie `-v` ingeschakeld. Hiermee kan uitgebreide uitvoer van de agent die verbinding maakt met de proxy, worden Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Raadpleeg de sectie [proxy-instellingen bijwerken](agent-manage.md#update-proxy-settings) om te controleren of u de agent op de juiste manier hebt geconfigureerd om te communiceren via een proxy server.    

3. Controleer of de eind punten die zijn beschreven in de lijst Azure Monitor [netwerk firewall vereisten](log-analytics-agent.md#firewall-requirements) , correct worden toegevoegd aan een lijst met toegestane producten. Als u Azure Automation gebruikt, worden de benodigde stappen voor de netwerk configuratie ook hierboven gekoppeld.

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Probleem: er wordt een 403-fout bericht weer gegeven wanneer u probeert onboarding uit te voeren

### <a name="probable-causes"></a>Mogelijke oorzaken
* Datum en tijd zijn onjuist op Linux-server 
* De gebruikte werk ruimte-ID en werkruimte sleutel zijn onjuist

### <a name="resolution"></a>Oplossing

1. Controleer de tijd op uw Linux-server met de datum van de opdracht. Als de tijd +/-15 minuten vanaf de huidige tijd is, mislukt de onboarding. Corrigeer deze update door de datum en/of tijd zone van de Linux-server op te lossen. 
2. Controleer of u de nieuwste versie van de Log Analytics-agent voor Linux hebt geïnstalleerd.  De nieuwste versie waarschuwt u nu als een tijd verschil de onboarding-fout veroorzaakt.
3. Onboarding uitvoeren met de juiste werk ruimte-ID en werkruimte sleutel na de installatie-instructies eerder in dit artikel.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Probleem: u ziet de fout 500 en 404 in het logboek bestand direct na het voorbereiden
Dit is een bekend probleem dat zich voordoet bij de eerste upload van Linux-gegevens in een Log Analytics-werk ruimte. Dit heeft geen invloed op de gegevens die worden verzonden of de service-ervaring.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Probleem: u ziet omiagent met een CPU van 100%

### <a name="probable-causes"></a>Mogelijke oorzaken
Een regressie in nss-PEM Package [v 1.0.3 -5. EL7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) heeft een ernstig prestatie probleem veroorzaakt, die we hebben gezien een heleboel in RedHat/CentOS 7. x-distributies. Raadpleeg de volgende documentatie voor meer informatie over dit probleem: bug [1667121 prestatie regressie in libkrul](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

Problemen met betrekking tot prestaties doen zich niet altijd voor en ze zijn zeer lastig te reproduceren. Als u een dergelijk probleem ondervindt met omiagent, moet u het script omiHighCPUDiagnostics.sh gebruiken waarmee de stack tracering van de omiagent wordt verzameld wanneer een bepaalde drempel waarde wordt overschreden.

1. Het script downloaden <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Diagnostische gegevens 24 uur uitvoeren met een CPU-drempel van 30% <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Call stack wordt in omiagent_trace bestand gedumpt. Als u veel krul-en NSS-functie aanroepen ziet, volgt u de onderstaande stappen voor de oplossing.

### <a name="resolution-step-by-step"></a>Oplossing (stapsgewijze stap)

1. Voer een upgrade uit voor het pakket nss-PEM naar [v 1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Als nss-PEM niet beschikbaar is voor upgrade (meestal gebeurt op CentOS), downgradet u krul naar 7.29.0-46. Als u per ongeluk ' yum update ' uitvoert, wordt krul bijgewerkt naar 7.29.0-51 en wordt het probleem opnieuw uitgevoerd. <br/>
`sudo yum downgrade curl libcurl`

3. OMI opnieuw starten: <br/>
`sudo scxadmin -restart`


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Probleem: u ziet geen doorgestuurde syslog-berichten 

### <a name="probable-causes"></a>Mogelijke oorzaken
* De configuratie die wordt toegepast op de Linux-server staat het verzamelen van de verzonden opslag ruimten en/of logboek niveaus niet toe
* Syslog wordt niet correct doorgestuurd naar de Linux-server
* Het aantal berichten dat per seconde wordt doorgestuurd, is te groot voor de basis configuratie van de Log Analytics-agent voor Linux dat kan worden verwerkt

### <a name="resolution"></a>Oplossing
* Controleer of de configuratie in de werk ruimte Log Analytics voor syslog alle faciliteiten en de juiste logboek niveaus heeft. Beoordeling [van syslog-verzameling configureren in de Azure Portal](./data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Controleren of de systeem eigen syslog Messa ging-daemons ( `rsyslog` , `syslog-ng` ) de doorgestuurde berichten kunnen ontvangen
* Controleer de firewall instellingen op de syslog-server om er zeker van te zijn dat berichten niet worden geblokkeerd
* Een syslog-bericht simuleren voor het Log Analytics met behulp van de `logger` opdracht
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Probleem: u ontvangt het errno-adres dat al in gebruik is in het omsagent-logboek bestand
Als u `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` in omsagent. log ziet.

### <a name="probable-causes"></a>Mogelijke oorzaken
Deze fout geeft aan dat de Linux Diagnostic extension (LAD) wordt geïnstalleerd naast de VM-extensie van de Log Analytics Linux en dezelfde poort gebruikt voor het verzamelen van syslog-gegevens als omsagent.

### <a name="resolution"></a>Oplossing
1. Voer de volgende opdrachten uit als hoofdmap (Let op: 25224 is een voor beeld en is mogelijk in uw omgeving een ander poort nummer dat wordt gebruikt door LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Vervolgens moet u het juiste `rsyslogd` of `syslog_ng` configuratie bestand bewerken en de Lad-configuratie wijzigen om naar poort 25229 te schrijven.

2. Als de virtuele machine wordt uitgevoerd `rsyslogd` , is het bestand dat moet worden gewijzigd: `/etc/rsyslog.d/95-omsagent.conf` (als dit bestaat, anders `/etc/rsyslog` ). Als de virtuele machine wordt uitgevoerd `syslog_ng` , is het bestand dat moet worden gewijzigd: `/etc/syslog-ng/syslog-ng.conf` .
3. Start omsagent opnieuw `sudo /opt/microsoft/omsagent/bin/service_control restart` .
4. Start de syslog-service opnieuw.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Probleem: u kunt omsagent niet verwijderen met de optie leegmaken

### <a name="probable-causes"></a>Mogelijke oorzaken

* De diagnostische extensie voor Linux is geïnstalleerd
* De diagnostische Linux-extensie is geïnstalleerd en verwijderd, maar er wordt nog steeds een fout weer geven over omsagent die worden gebruikt door mdsd en kan niet worden verwijderd.

### <a name="resolution"></a>Oplossing
1. Verwijder de Linux-extensie voor diagnostische gegevens (LAD).
2. Bestanden van de Linux-extensie voor diagnostische gegevens verwijderen van de computer als deze aanwezig zijn op de volgende locatie: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` en `/var/opt/microsoft/omsagent/LAD/` .

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Probleem: u kunt geen gegevens van nagios-gegevens weer geven 

### <a name="probable-causes"></a>Mogelijke oorzaken
* Omsagent-gebruiker heeft geen machtigingen om het nagios-logboek bestand te lezen
* Nagios-bron en-filter zijn niet verwijderd uit het bestand omsagent. conf

### <a name="resolution"></a>Oplossing
1. Voeg de omsagent-gebruiker toe om te lezen uit het nagios-bestand door deze [instructies](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts)te volgen.
2. Zorg ervoor dat in het algemene configuratie bestand van de Log Analytics-agent voor Linux in de `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` nagios-bron en het filter geen opmerkingen zijn. 

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Probleem: u ziet geen Linux-gegevens 

### <a name="probable-causes"></a>Mogelijke oorzaken
* Onboarding naar Azure Monitor is mislukt
* De verbinding met de Azure Monitor is geblokkeerd
* De virtuele machine is opnieuw opgestart
* Het OMI-pakket is hand matig bijgewerkt naar een nieuwere versie, vergeleken met wat is geïnstalleerd door Log Analytics agent voor Linux-pakket
* OMI is geblokkeerd, de OMS-agent wordt geblokkeerd
* Fout in de klasse van de DSC-resource Logboeken is *niet gevonden* in het `omsconfig.log` logboek bestand
* Er wordt een back-up gemaakt van Log Analytics-agent voor gegevens
* DSC-logboeken *huidige configuratie bestaat niet. Voer Start-DscConfiguration opdracht met de para meter-Path uit om een configuratie bestand op te geven en maak eerst een huidige configuratie.* in het `omsconfig.log` logboek bestand, maar er bestaat geen logboek bericht over `PerformRequiredConfigurationChecks` bewerkingen.

### <a name="resolution"></a>Oplossing
1. Installeer alle afhankelijkheden zoals het gecontroleerde pakket.
2. Controleer of er een onboarding van Azure Monitor is geslaagd door te controleren of het volgende bestand bestaat: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` .  Als dat niet het geval is, gebruikt u de omsadmin.sh-opdracht regel [instructies](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
4. Als u een proxy gebruikt, raadpleegt u de bovenstaande stappen voor het oplossen van problemen met proxy.
5. In sommige Azure-distributie systemen wordt Omid OMI server daemon niet gestart nadat de virtuele machine opnieuw is opgestart. Dit leidt ertoe dat er geen gegevens over de audit, de change tracking of de UpdateManagement-oplossing worden weer gegeven. De tijdelijke oplossing is om de Omi-server hand matig te starten door uit te voeren `sudo /opt/omi/bin/service_control restart` .
6. Nadat het OMI-pakket hand matig is bijgewerkt naar een nieuwere versie, moet het hand matig opnieuw worden opgestart om Log Analytics agent verder te kunnen werken. Deze stap is vereist voor sommige distributies waarbij OMI-server niet automatisch wordt gestart nadat de upgrade is uitgevoerd. Voer uit `sudo /opt/omi/bin/service_control restart` om Omi opnieuw te starten.
* In sommige gevallen kan OMI worden geblokkeerd. De OMS-agent kan een geblokkeerde status invoeren die wacht op OMI, waarbij alle gegevens verzameling wordt geblokkeerd. Het proces van de OMS-agent wordt uitgevoerd, maar er is geen activiteit, die wordt vastgelegd door geen nieuwe logboek regels (zoals verzonden heartbeats) aanwezig in `omsagent.log` . Start OMI opnieuw `sudo /opt/omi/bin/service_control restart` op om de agent te herstellen.
7. Als er een fout bericht *klasse niet gevonden* wordt weer gegeven in omsconfig. log, voert u uit `sudo /opt/omi/bin/service_control restart` .
8. In sommige gevallen wordt er een back-up gemaakt van de gegevens op de agent, wanneer de Log Analytics-agent voor Linux niet kan communiceren met Azure Monitor, de volledige buffer grootte: 50 MB. De agent moet opnieuw worden gestart door de volgende opdracht uit te voeren `/opt/microsoft/omsagent/bin/service_control restart` .

    >[!NOTE]
    >Dit probleem is opgelost in Agent versie 1.1.0-28 of hoger
    >

* Als `omsconfig.log` in het logboek bestand niet wordt aangegeven dat `PerformRequiredConfigurationChecks` bewerkingen periodiek op het systeem worden uitgevoerd, is er mogelijk een probleem met de cron-taak/-service. Zorg ervoor dat de cron-taak bestaat onder `/etc/cron.d/OMSConsistencyInvoker` . Voer, indien nodig, de volgende opdrachten uit om de cron-taak te maken:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Zorg er ook voor dat de cron-service wordt uitgevoerd. U kunt gebruiken `service cron status` met Debian, Ubuntu, SuSE of `service crond status` met RHEL, CentOS, Oracle Linux om de status van deze service te controleren. Als de service niet bestaat, kunt u de binaire bestanden installeren en de service starten met behulp van het volgende:

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Probleem: bij het configureren van een verzameling vanuit de portal voor syslog-of Linux-prestatie meter items, worden de instellingen niet toegepast

### <a name="probable-causes"></a>Mogelijke oorzaken
* De Log Analytics-agent voor Linux heeft niet de meest recente configuratie opgehaald
* De gewijzigde instellingen in de portal zijn niet toegepast

### <a name="resolution"></a>Oplossing
**Achtergrond:** `omsconfig` is de Log Analytics agent voor Linux-configuratie agent die elke vijf minuten zoekt naar nieuwe configuratie op de portal-zijde. Deze configuratie wordt vervolgens toegepast op de Log Analytics agent voor Linux-configuratie bestanden die zich bevinden op/etc/opt/Microsoft/omsagent/conf/omsagent.conf.

* In sommige gevallen is het mogelijk dat de Log Analytics agent voor Linux-configuratie agent geen communicatie kan maken met de Portal Configuration-service, wat resulteert in de meest recente configuratie die niet wordt toegepast.
  1. Controleer of de `omsconfig` agent is geïnstalleerd door of uit te voeren `dpkg --list omsconfig` `rpm -qi omsconfig` .  Als deze niet is geïnstalleerd, installeert u de nieuwste versie van de Log Analytics-agent voor Linux opnieuw.

  2. Controleer of de `omsconfig` agent kan communiceren met Azure monitor door de volgende opdracht uit te voeren `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` . Met deze opdracht wordt de configuratie geretourneerd die de agent ontvangt van de service, inclusief syslog-instellingen, Linux-prestatie meter items en aangepaste Logboeken. Als deze opdracht mislukt, voert u de volgende opdracht uit `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` . Met deze opdracht wordt de omsconfig-agent gedwongen om over Azure Monitor te praten en de nieuwste configuratie op te halen.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Probleem: u ziet geen aangepaste logboek gegevens 

### <a name="probable-causes"></a>Mogelijke oorzaken
* Het onboarden van Azure Monitor is mislukt.
* De instelling **voor het Toep assen van de volgende configuratie op mijn Linux-servers** is niet geselecteerd.
* omsconfig heeft de nieuwste aangepaste logboek configuratie niet opgehaald van de service.
* Log Analytics agent voor Linux-gebruiker `omsagent` kan geen toegang krijgen tot het aangepaste logboek vanwege machtigingen of niet gevonden.  Mogelijk worden de volgende fouten weer geven:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Bekend probleem met een race condition die is opgelost in Log Analytics agent voor Linux-versie 1.1.0-217

### <a name="resolution"></a>Oplossing
1. Controleer of het volgende bestand bestaat als de onboarding van Azure Monitor is geslaagd `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` . Als dat niet het geval is, hetzij:  

  1. Onboarding uitvoeren met behulp van de omsadmin.sh-opdracht regel [instructies](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
  2. Zorg ervoor dat onder **Geavanceerde instellingen** in de Azure Portal de instelling **de volgende configuratie Toep assen op mijn Linux-servers** is ingeschakeld.  

2. Controleer of de `omsconfig` agent kan communiceren met Azure monitor door de volgende opdracht uit te voeren `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` .  Met deze opdracht wordt de configuratie geretourneerd die de agent ontvangt van de service, inclusief syslog-instellingen, Linux-prestatie meter items en aangepaste Logboeken. Als deze opdracht mislukt, voert u de volgende opdracht uit `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` . Met deze opdracht wordt de omsconfig-agent gedwongen om over Azure Monitor te praten en de nieuwste configuratie op te halen.

**Achtergrond:** In plaats van de Log Analytics-agent voor Linux dat als een bevoegde gebruiker wordt uitgevoerd `root` , wordt de agent uitgevoerd als de `omsagent` gebruiker. In de meeste gevallen moet er expliciete machtiging aan deze gebruiker worden verleend, zodat bepaalde bestanden kunnen worden gelezen. `omsagent`Voer de volgende opdrachten uit om toestemming te geven aan de gebruiker:

1. De `omsagent` gebruiker toevoegen aan een specifieke groep `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Universele Lees toegang verlenen tot het vereiste bestand `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Er is een bekend probleem met een race voorwaarde met de Log Analytics agent voor Linux-versie ouder dan 1.1.0-217. Voer na het bijwerken naar de nieuwste agent de volgende opdracht uit om de meest recente versie van de invoeg toepassing voor uitvoer op te halen `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` .

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Probleem: u probeert uit te voeren voor een nieuwe werk ruimte
Wanneer u probeert een agent voor een nieuwe werk ruimte vrij te maken, moet u de configuratie van de Log Analytics-agent opschonen voordat u het onboarding kunt uitvoeren. Als u de oude configuratie van de agent wilt opschonen, voert u de shell-bundel uit met `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
of

```
sudo sh ./onboard_agent.sh --purge
```

U kunt door gaan met het gebruik van de `--purge` optie

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Log Analytics agent-extensie in de Azure Portal is gemarkeerd met een mislukte status: inrichten is mislukt

### <a name="probable-causes"></a>Mogelijke oorzaken
* Log Analytics agent is verwijderd uit het besturings systeem
* Log Analytics Agent-service is niet actief, uitgeschakeld of niet geconfigureerd

### <a name="resolution"></a>Oplossing 
Voer de volgende stappen uit om het probleem te verhelpen.
1. Verwijder de extensie uit Azure Portal.
2. Installeer de agent volgens de [instructies](../learn/quick-collect-linux-computer.md).
3. Start de agent opnieuw door de volgende opdracht uit te voeren: `sudo /opt/microsoft/omsagent/bin/service_control restart` .
* Wacht enkele minuten en de inrichtings status is gewijzigd in **inrichting geslaagd**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Probleem: de upgrade van de Log Analytics-agent op aanvraag

### <a name="probable-causes"></a>Mogelijke oorzaken

De Log Analytics agent pakketten op de host zijn verouderd.

### <a name="resolution"></a>Oplossing 
Voer de volgende stappen uit om het probleem te verhelpen.

1. Controleer op de meest recente release op de [pagina](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Installatie script downloaden (1.4.2-124 als voorbeeld versie):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Upgrade pakketten door uit te voeren `sudo sh ./omsagent-*.universal.x64.sh --upgrade` .
