---
title: Diagnostisch hulp programma voor het oplossen van problemen met het StorSimple 8000-apparaat | Microsoft Docs
description: Dit artikel bevat een overzicht van het diagnostische hulp programma en beschrijft hoe het hulp programma kan worden gebruikt met een StorSimple-apparaat.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: bf0570cd62a2c329407cfb6dd087020f53cdc991
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954066"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Het hulp programma StorSimple Diagnostics gebruiken om problemen met de 8000 Series-apparaten op te lossen

## <a name="overview"></a>Overzicht

Het hulp programma StorSimple Diagnostics diagnosticeert problemen met betrekking tot de status van het systeem, de prestaties, het netwerk en het onderdeel Hardware voor een StorSimple-apparaat. Het hulp programma Diagnostics kan worden gebruikt in verschillende scenario's. Deze scenario's omvatten het plannen van werk belastingen, het implementeren van een StorSimple-apparaat, het beoordelen van de netwerk omgeving en het bepalen van de prestaties van een operationeel apparaat. Dit artikel bevat een overzicht van het diagnostische hulp programma en beschrijft hoe het hulp programma kan worden gebruikt met een StorSimple-apparaat.

Het diagnostische hulp programma is voornamelijk bedoeld voor 8000 StorSimple-Series on-premises apparaten (8100 en 8600).

## <a name="run-diagnostics-tool"></a>Diagnostisch hulp programma uitvoeren

Dit hulp programma kan worden uitgevoerd via de Windows Power shell-interface van uw StorSimple-apparaat. Er zijn twee manieren om toegang te krijgen tot de lokale interface van uw apparaat:

* [Putty gebruiken om verbinding te maken met de seriële console van het apparaat](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Extern toegang tot het hulp programma via de Windows PowerShell voor StorSimple](storsimple-8000-remote-connect.md).

In dit artikel wordt ervan uitgegaan dat u via PuTTy verbinding hebt gemaakt met de seriële console van het apparaat.

#### <a name="to-run-the-diagnostics-tool"></a>Het hulp programma voor diagnostische gegevens uitvoeren

Wanneer u verbinding hebt gemaakt met de Windows Power shell-interface van het apparaat, voert u de volgende stappen uit om de cmdlet uit te voeren.
1. Meld u aan bij de seriële console van het apparaat door de stappen in [putty gebruiken te volgen om verbinding te maken met de seriële console van het apparaat](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Typ de volgende opdracht:

    `Invoke-HcsDiagnostics`

    Als de bereik parameter niet is opgegeven, voert de cmdlet alle diagnostische tests uit. Deze tests omvatten systeem, de status van het hardwareonderdeel, het netwerk en de prestaties. 
    
    Als u alleen een specifieke test wilt uitvoeren, geeft u de bereik parameter op. Als u bijvoorbeeld alleen de netwerk test wilt uitvoeren, typt u

    `Invoke-HcsDiagnostics -Scope Network`

3. Selecteer en kopieer de uitvoer van het PuTTy-venster naar een tekst bestand voor verdere analyse.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Scenario's voor het gebruik van het hulp programma voor diagnostische gegevens

Gebruik het diagnostische hulp programma voor het oplossen van problemen met het netwerk, de prestaties, het systeem en de hardware-status van het systeem. Hier volgen enkele mogelijke scenario's:

* **Apparaat offline** : uw StorSimple 8000-serie apparaat is offline. Vanuit de Windows Power shell-interface lijkt het echter alsof beide controllers actief zijn.
    * U kunt dit hulp programma gebruiken om de netwerk status te bepalen.
         
         > [!NOTE]
         > Gebruik dit hulp programma niet om de prestaties en netwerk instellingen op een apparaat te beoordelen vóór de registratie (of configureren via de wizard Setup). Er wordt een geldig IP-adres toegewezen aan het apparaat tijdens de installatie wizard en registratie. U kunt deze cmdlet uitvoeren op een apparaat dat niet is geregistreerd voor hardware-status en systeem. Gebruik de bereik parameter, bijvoorbeeld:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Problemen met persistent apparaat** -u ondervindt problemen met het apparaat dat persistent lijkt te zijn. Zo mislukt de registratie. U kunt ook hardwareproblemen ondervinden nadat het apparaat is geregistreerd en operationeel is.
    * In dit geval gebruikt u dit hulp programma voor het oplossen van problemen voordat u een service aanvraag registreert bij Microsoft Ondersteuning. U wordt aangeraden dit hulp programma uit te voeren en de uitvoer van dit hulp programma vast te leggen. Vervolgens kunt u deze uitvoer opgeven ter ondersteuning van het versnellen van het oplossen van problemen.
    * Als er hardware-onderdelen of cluster fouten zijn, moet u zich aanmelden bij een Ondersteuningsaanvraag.

* **Prestaties van minder apparaat** : uw StorSimple-apparaat is traag.
    * In dit geval voert u deze cmdlet uit met de bereik parameter ingesteld op prestaties. Analyseer de uitvoer. U krijgt de latentie voor lezen/schrijven in de Cloud. Gebruik de gerapporteerde latenties als Maxi maal Haal bare doel, factor in enige overhead voor het verwerken van interne gegevens en implementeer vervolgens de werk belastingen op het systeem. Ga voor meer informatie naar [gebruik van de netwerk test om de prestaties van het apparaat op te lossen](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Diagnostische tests en voorbeeld uitvoer

### <a name="hardware-test"></a>Hardware testen

Deze test bepaalt de status van de hardwareonderdelen, de eigenschappen van-firmware en de schijf firmware die op uw systeem wordt uitgevoerd.

* De hardware-onderdelen die zijn gerapporteerd, zijn de onderdelen waarvan de test is mislukt of die niet aanwezig zijn in het systeem.
* De eigenschappen van firmware en de firmware versie van de schijf worden gerapporteerd voor de controller 0, controller 1 en gedeelde onderdelen in uw systeem. Voor een volledige lijst met hardwareonderdelen gaat u naar:

    * [Onderdelen in primaire behuizing](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Onderdelen in EBOD Enclosure](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Als de hardware-test mislukte onderdelen rapporteert, [meldt u zich aan bij een service aanvraag met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Voorbeeld uitvoer van de test uitvoering van een hardware op een 8100-apparaat

Hier volgt een voor beeld van een uitvoer van een StorSimple 8100-apparaat. In het 8100-model apparaat is de EBOD Enclosure niet aanwezig. Daarom worden de onderdelen van de EBOD-controller niet gerapporteerd.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>Systeem test

Deze test rapporteert de systeem gegevens, de beschik bare updates, de cluster gegevens en de service-informatie voor uw apparaat.

* De systeem informatie omvat het model, serie nummer van het apparaat, de tijd zone, de controller status en de gedetailleerde software versie die op het systeem wordt uitgevoerd. Om inzicht te krijgen in de verschillende systeem parameters die als uitvoer zijn gerapporteerd, gaat u naar [systeem informatie interpreteren](#appendix-interpreting-system-information).

* De beschik baarheid van de Update meldt of de standaard-en onderhouds modus beschikbaar zijn en de bijbehorende pakket namen. Als dat zo `RegularUpdates` `MaintenanceModeUpdates` is `false` , geeft dit aan dat de updates niet beschikbaar zijn. Het apparaat is up-to-date.
* De cluster informatie bevat de informatie over de verschillende logische onderdelen van alle HCS-cluster groepen en de bijbehorende statussen. Als u in deze sectie van het rapport een offline cluster groep ziet, [neemt u contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md).
* De service-informatie bevat de namen en statussen van alle HCS-en CiS-services die op het apparaat worden uitgevoerd. Deze informatie is nuttig voor de Microsoft Ondersteuning bij het oplossen van problemen met het apparaat.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Voorbeeld uitvoer van de test uitvoering van een systeem op een 8100-apparaat

Hier volgt een voor beeld van de uitvoer van de systeem test op een 8100-apparaat.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_service        Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Netwerk test

Deze test valideert de status van de netwerk interfaces, poorten, DNS-en NTP-server connectiviteit, TLS/SSL-certificaat, referenties van het opslag account, de connectiviteit met de update servers en de connectiviteit van webproxy op uw StorSimple-apparaat.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Voorbeeld uitvoer van netwerk test wanneer alleen DATA0 is ingeschakeld

Hier volgt een voor beeld van de uitvoer van het 8100-apparaat. U kunt in de uitvoer zien dat:
* Alleen DATA 0-en DATA 1-netwerk interfaces zijn ingeschakeld en geconfigureerd.
* DATA 2-5 zijn niet ingeschakeld in de portal.
* De configuratie van de DNS-server is geldig en het apparaat kan verbinding maken via de DNS-server.
* De NTP-server verbinding is ook prima.
* De poorten 80 en 443 zijn geopend. Poort 9354 is echter geblokkeerd. Op basis van de [systeem netwerk vereisten](./storsimple-8000-system-requirements.md)moet u deze poort openen voor de service bus-communicatie.
* Het TLS/SSL-certificaat is geldig.
* Het apparaat kan verbinding maken met het opslag account: _myss8000storageacct_.
* De verbinding met update servers is geldig.
* De webproxy is niet geconfigureerd op dit apparaat.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Voorbeeld uitvoer van netwerk test wanneer DATA0 en DATA1 zijn ingeschakeld

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Prestatietest

Deze test rapporteert de prestaties van de Cloud via de latentie lees-schrijf bewerkingen in de Cloud voor uw apparaat. Dit hulp programma kan worden gebruikt om een basis lijn te maken van de prestaties van de cloud die u kunt realiseren met StorSimple. Het hulp programma rapporteert het maximale prestatie niveau (een beste scenario voor latentie Lees bewerkingen) dat u voor uw verbinding kunt krijgen.

Wanneer het hulp programma de Maxi maal Haal bare prestaties rapporteert, kunnen we de gerapporteerde wacht tijden voor lezen en schrijven gebruiken als doelen bij het implementeren van de werk belastingen.

De test simuleert de BLOB-grootten die zijn gekoppeld aan de verschillende volume typen op het apparaat. Normale lagen en back-ups van lokaal vastgemaakte volumes gebruiken een grootte van 64 KB-blobs. Gelaagde volumes met de optie Archief selectie vakje grootte van 512 KB BLOB-gegevens. Als uw apparaat belaagde en lokaal vastgemaakte volumes heeft geconfigureerd, wordt alleen de test uitgevoerd die overeenkomt met de grootte van 64 KB BLOB-gegevens.

Als u dit hulp programma wilt gebruiken, voert u de volgende stappen uit:

1.  Maak eerst een combi natie van gelaagde volumes en gelaagde volumes met de optie gearchiveerd ingeschakeld. Met deze actie zorgt u ervoor dat het hulp programma de tests uitvoert voor zowel 64 KB als 512 KB-BLOB-groottes.

2. Voer de cmdlet uit nadat u de volumes hebt gemaakt en geconfigureerd. Type:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Noteer de wacht tijden voor lezen en schrijven die door het hulp programma worden gerapporteerd. Het uitvoeren van deze test kan enkele minuten duren voordat de resultaten worden gerapporteerd.

4. Als de latentie van de verbinding zich allemaal onder het verwachte bereik bevinden, kunnen de latenties die door het hulp programma worden gerapporteerd, worden gebruikt als Maxi maal Haal bare doel bij het implementeren van de werk belastingen. Factor in enige overhead voor het verwerken van interne gegevens.

    Als de latentie voor lees bewerkingen die is gerapporteerd door het diagnostische hulp programma hoog is:

    1. Configureer Opslaganalyse voor BLOB-Services en analyseer de uitvoer om inzicht te krijgen in de latenties voor het Azure-opslag account. Ga naar [Opslaganalyse inschakelen en configureren](../storage/blobs/monitor-blob-storage.md)voor gedetailleerde instructies. Als deze latenties ook hoog zijn en vergelijkbaar zijn met de getallen die u hebt ontvangen van het hulp programma StorSimple Diagnostics, moet u een service aanvraag registreren bij Azure Storage.

    2. Als de wacht tijden van het opslag account laag zijn, neemt u contact op met uw netwerk beheerder om latentie problemen in uw netwerk te onderzoeken.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Voorbeeld uitvoer van de prestatie test wordt uitgevoerd op een 8100-apparaat

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>Bijlage: systeem informatie interpreteren

Hier volgt een tabel met een beschrijving van de verschillende Windows Power shell-para meters in de systeem informatie worden toegewezen aan. 

| PowerShell-parameter    | Beschrijving  |
|-------------------------|------------------|
| Instance ID             | Elke controller heeft een unieke id of een GUID die eraan is gekoppeld.|
| Name                    | De beschrijvende naam van het apparaat, zoals geconfigureerd via de Azure Portal tijdens de implementatie van het apparaat. De standaard beschrijvende naam is het serie nummer van het apparaat. |
| Model                   | Het model van uw StorSimple 8000-serie apparaat. Het model kan 8100 of 8600 zijn.|
| SerialNumber            | Het serie nummer van het apparaat wordt toegewezen aan de fabriek en is 15 tekens lang. 8600-SHX0991003G44HT geeft bijvoorbeeld het volgende aan:<br> 8600: is het model van het apparaat.<br>SHX: is de productie site.<br> 0991003-is een specifiek product. <br> G44HT: de laatste vijf cijfers worden verhoogd om unieke serie nummers te maken. Dit kan geen opeenvolgende set zijn.|
| Tijdzone                | De tijd zone van het apparaat zoals geconfigureerd in de Azure Portal tijdens de implementatie van het apparaat.|
| CurrentController       | De controller waarmee u verbinding hebt via de Windows Power shell-interface van uw StorSimple-apparaat.|
| ActiveController        | De controller die actief is op uw apparaat en die alle netwerk-en schijf bewerkingen beheert. Dit kan controller 0 of controller 1 zijn.  |
| Controller0Status       | De status van controller 0 op het apparaat. De controller status kan normaal, in de herstel modus of onbereikbaar zijn.|
| Controller1Status       | De status van controller 1 op het apparaat.  De controller status kan normaal, in de herstel modus of onbereikbaar zijn.|
| SystemMode              | De algemene status van uw StorSimple-apparaat. De apparaatstatus kan normaal, onderhoud of uit bedrijf worden genomen (komt overeen met deactiveren in de Azure Portal).|
| FriendlySoftwareVersion | De beschrijvende teken reeks die overeenkomt met de versie van de apparaatsoftware. Voor een systeem waarop update 4 wordt uitgevoerd, is de beschrijvende software versie StorSimple 8000 Series update 4,0.|
| HcsSoftwareVersion      | De versie van de HCS-software die op uw apparaat wordt uitgevoerd. De HCS-software versie die overeenkomt met StorSimple 8000 Series update 4,0 is bijvoorbeeld 6.3.9600.17820. |
| ApiVersion              | De software versie van de Windows Power shell-API van het HCS-apparaat.|
| VhdVersion              | De software versie van de fabrieks installatie kopie waarmee het apparaat is geleverd. Als u de fabrieks instellingen van uw apparaat herstelt, wordt deze software versie uitgevoerd.|
| OSVersion               | De software versie van het Windows Server-besturings systeem dat op het apparaat wordt uitgevoerd. Het StorSimple-apparaat is gebaseerd op de Windows Server 2012 R2 die overeenkomt met 6.3.9600.|
| CisAgentVersion         | De versie van de CIS-agent die wordt uitgevoerd op uw StorSimple-apparaat. Deze agent helpt te communiceren met de StorSimple Manager-service die wordt uitgevoerd in Azure.|
| MdsAgentVersion         | De versie die overeenkomt met de MDS-agent die wordt uitgevoerd op uw StorSimple-apparaat. Met deze agent worden gegevens verplaatst naar de service bewaking en diagnose.|
| Lsisas2Version          | De versie die overeenkomt met de LSI-Stuur Programma's op uw StorSimple-apparaat.|
| Capaciteit                | De totale capaciteit van het apparaat in bytes.|
| RemoteManagementMode    | Hiermee wordt aangegeven of het apparaat extern kan worden beheerd via de Windows Power shell-interface. |
| FipsMode                | Hiermee geeft u op of de FIPS-modus (Verenigde Staten Federal Information Processing Standard) is ingeschakeld op uw apparaat. De FIPS 140-standaard definieert cryptografische algoritmen die zijn goedgekeurd voor gebruik door Amerikaanse Federal Government-computer systemen voor de beveiliging van gevoelige gegevens. Voor apparaten met update 4 of hoger is de FIPS-modus standaard ingeschakeld. |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [syntaxis van de cmdlet Invoke-HcsDiagnostics](/previous-versions/windows/powershell-scripting/mt795371(v=wps.630)).

* Meer informatie over het [oplossen van implementatie problemen](./storsimple-8000-troubleshoot-deployment.md) op uw StorSimple-apparaat.