---
title: Hoge Beschik baarheid is ingesteld met STONITH voor SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Hoge Beschik baarheid instellen voor SAP HANA op Azure (grote instanties) in SUSE met behulp van de STONITH
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3dd2a618f22036fd0826a99207d83a3add390c7d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105645321"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>Hoge beschikbaarheid ingesteld in SUSE met behulp van de STONITH
Dit document bevat gedetailleerde stapsgewijze instructies voor het instellen van de hoge Beschik baarheid op SUSE-besturings systeem met behulp van het STONITH-apparaat.

**Disclaimer:** *deze hand leiding wordt afgeleid door de installatie te testen in de micro soft Hana-omgeving met grote instanties, die werkt. Als het micro soft service management-team voor HANA grote instanties biedt geen ondersteuning voor het besturings systeem, moet u mogelijk contact opnemen met SUSE voor eventuele verdere problemen oplossen of verduidelijken op de laag van het besturings systeem. Het micro soft service management-team stelt STONITH-apparaat in en biedt volledige ondersteuning en kan worden betrokken bij het oplossen van problemen met STONITH-apparaten.*
## <a name="overview"></a>Overzicht
Voor het instellen van de maximale Beschik baarheid met behulp van SUSE-clustering moeten aan de volgende vereisten worden voldaan.
### <a name="pre-requisites"></a>Vereisten
- HANA grote instanties worden ingericht
- Het besturings systeem is geregistreerd
- HANA-grote instanties servers zijn verbonden met een SMT-server voor het verkrijgen van patches/pakketten
- Er zijn nieuwste patches geïnstalleerd op het besturings systeem
- NTP (tijd server) is ingesteld
- Lees en begrijp de nieuwste versie van de SUSE-documentatie op HA Setup

### <a name="setup-details"></a>Details van de installatie
In deze hand leiding worden de volgende instellingen gebruikt:
- Besturings systeem: SLES 12 SP1 voor SAP
- HANA grote instanties: 2xS192 (vier sockets, 2 TB)
- HANA-versie: HANA 2,0 SP1
- Server namen: sapprdhdb95 (Knooppunt1) en sapprdhdb96 (Knooppunt2)
- STONITH-apparaat: op iSCSI gebaseerd STONITH-apparaat
- NTP ingesteld op een van de HANA grote instantie knooppunten

Wanneer u HANA grote instanties instelt met HSR, kunt u een micro soft service management-team aanvragen om STONITH in te stellen. Als u al een bestaande klant bent en u een STONITH-apparaat hebt ingesteld voor uw bestaande Blade, moet u de volgende informatie over het micro soft service management-team opgeven in het formulier service aanvraag (SRF). U kunt een SRF-formulier aanvragen via de Technical Account Manager of uw contact persoon van micro soft voor de onboarding van een grote instantie van HANA. De nieuwe klanten kunnen een STONITH-apparaat vragen op het moment van inrichten. De invoer is beschikbaar in het aanvraag formulier voor inrichting.

- Server naam en server-IP-adres (bijvoorbeeld myhanaserver1, 10.35.0.1)
- Locatie (bijvoorbeeld VS Oost)
- Klant naam (bijvoorbeeld micro soft)
- SID-HANA-systeem-id (bijvoorbeeld H11)

Zodra het STONITH-apparaat is geconfigureerd, biedt micro soft service management team u de SBD-apparaatnaam en het IP-adres van de iSCSI-opslag, die u kunt gebruiken voor het configureren van STONITH Setup. 

Als u de end-to-end HA wilt instellen met behulp van STONITH, moet u de volgende stappen volgen:

1.  Het SBD-apparaat identificeren
2.  Het SBD-apparaat initialiseren
3.  Het cluster configureren
4.  De Softdog-watchdog instellen
5.  Het knoop punt toevoegen aan het cluster
6.  Het cluster valideren
7.  De resources configureren voor het cluster
8.  Het failoverproces testen

## <a name="1---identify-the-sbd-device"></a>1. Identificeer het SBD-apparaat
In deze sectie wordt beschreven hoe u het SBD-apparaat voor uw Setup kunt bepalen nadat het micro soft service management team de STONITH heeft geconfigureerd. **Deze sectie is alleen van toepassing op de bestaande klant**. Als u een nieuwe klant bent, kunt u het micro soft service management-team de naam van een SBD-apparaat geven en deze sectie overs Laan.

1,1 */etc/iscsi/initiatorname.iSCI* wijzigen in 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Micro soft Service Management biedt deze teken reeks. Wijzig het bestand op **beide** knoop punten, maar het knooppunt nummer wijkt af van elk knoop punt.

![Scherm afbeelding toont een initiatorname-bestand met InitiatorName-waarden voor een knoop punt.](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1,2 */etc/iscsi/iscsid.conf* wijzigen: set *node.Session.Timeo.replacement_timeout = 5* en *node. startup = Automatic*. Wijzig het bestand op **beide** knoop punten.

1,3 de detectie opdracht uit te voeren, worden vier sessies weer gegeven. Voer de app uit op beide knoop punten.

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![Scherm afbeelding toont een console venster met de resultaten van de detectie opdracht isciadm.](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1,4 Voer de opdracht uit om u aan te melden bij het iSCSI-apparaat. er worden vier sessies weer gegeven. Voer de app uit op **beide** knoop punten.

```
iscsiadm -m node -l
```
![Scherm afbeelding toont een console venster met de resultaten van de iscsiadm knoop punt opdracht.](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1,5 Voer het script opnieuw scannen uit: *rescan-SCSI-bus.sh*.  Met dit script worden de nieuwe schijven weer gegeven die u voor u hebt gemaakt.  Voer de app uit op beide knoop punten. Als het goed is, ziet u een LUN-nummer dat groter is dan nul (bijvoorbeeld: 1, 2 enz.)

```
rescan-scsi-bus.sh
```
![Scherm afbeelding toont een console venster met de resultaten van het script.](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1,6 als u de apparaatnaam wilt ophalen, voert u de opdracht *fdisk – l*. Voer de app uit op beide knoop punten. Kies het apparaat met de grootte van **178 MIB**.

```
  fdisk –l
```

![Scherm afbeelding toont een console venster met de resultaten van de opdracht f schijf.](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. het SBD-apparaat initialiseren

2,1 het SBD-apparaat op **beide** knoop punten initialiseren

```
sbd -d <SBD Device Name> create
```
![Scherm afbeelding toont een console venster met het resultaat van de opdracht s b d Create.](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2,2 Controleer wat er is geschreven op het apparaat. Doe dit op **beide** knoop punten

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. het cluster wordt geconfigureerd
In deze sectie worden de stappen beschreven voor het instellen van het cluster SUSE HA.
### <a name="31-package-installation"></a>3,1-pakket installatie
3.1.1 Controleer of ha_sles en SAPHanaSR-doc-patronen zijn geïnstalleerd. Als deze niet is geïnstalleerd, installeert u ze. Installeer de app op **beide** knoop punten.
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![Scherm afbeelding toont een console venster met het resultaat van de patroon opdracht. ](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
 ![ Scherm afbeelding toont een console venster met het resultaat van de SAPHanaSR-doc-opdracht.](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3,2 het cluster instellen
3.2.1 u kunt *HA-cluster-init* opdracht gebruiken of de wizard YaST2 gebruiken om het cluster in te stellen. In dit geval wordt de wizard YaST2 gebruikt. U voert deze stap **alleen uit op het primaire knoop punt**.

Volg de YaST2> hoge Beschik baarheid > cluster ![ scherm opname van het YaST-configuratie centrum met hoge Beschik baarheid en een geselecteerd cluster. ](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
 ![ In de scherm afbeelding ziet u een dialoog venster met opties voor installeren en annuleren.](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

Klik op **Annuleren** omdat het halk2-pakket al is geïnstalleerd.

![Scherm afbeelding toont een bericht over uw annulerings optie.](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

Klik op **Continue**.

Verwachte waarde = aantal geïmplementeerde knoop punten (in dit geval 2) ![ scherm afbeelding toont cluster beveiliging met het selectie vakje beveiligings verificatie inschakelen.](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png)
Klik op **volgende** 
 ![ scherm afbeelding toont het venster cluster configureren met de lijsten Sync host en synchronisatie bestanden.](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
Voeg knooppunt namen toe en klik vervolgens op aanbevolen bestanden toevoegen.

Klik op ' csync2 inschakelen ' op '

Klik op ' vooraf gedeelde sleutels genereren ', deze wordt weer gegeven onder pop-up

![Scherm afbeelding toont een bericht dat uw sleutel is gegenereerd.](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

Klik op **OK**

De verificatie wordt uitgevoerd met behulp van de IP-adressen en vooraf gedeelde sleutels in Csync2. Het sleutel bestand wordt gegenereerd met csync2-k/etc/csync2/key_hagroup. Het bestand key_hagroup moet hand matig worden gekopieerd naar alle leden van het cluster nadat het is gemaakt. **Zorg ervoor dat u het bestand kopieert van het knoop punt 1 naar Knooppunt2**.

![Scherm afbeelding toont het dialoog venster voor het configureren van een cluster met opties die nodig zijn om de sleutel naar alle leden van het cluster te kopiëren.](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

Klik op **volgende** 
 ![ scherm afbeelding om het venster Cluster service weer te geven.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

In de standaard optie is opstarten uitgeschakeld, gewijzigd in aan, zodat pacemaker wordt gestart bij het opstarten. U kunt de keuze maken op basis van uw installatie vereisten.
Klik op **volgende** en de cluster configuratie is voltooid.

## <a name="4---setting-up-the-softdog-watchdog"></a>4. het instellen van de Softdog-watchdog
In deze sectie wordt de configuratie van de watchdog (softdog) beschreven.

4,1 Voeg de volgende regel toe aan */etc/init.d/boot.local* op **beide** knoop punten.
```
modprobe softdog
```
![Scherm afbeelding toont een opstart bestand waarbij de softdog-regel is toegevoegd.](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4,2 werk het bestand */etc/sysconfig/SBD* op **beide** knoop punten als volgt bij:
```
SBD_DEVICE="<SBD Device Name>"
```
![Scherm afbeelding toont het s b d-bestand met de S B D_DEVICE toegevoegde waarde.](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4,3 laden van de kernel-module op **beide** knoop punten door de volgende opdracht uit te voeren
```
modprobe softdog
```
![Scherm afbeelding toont een deel van een console venster met de opdracht modprobe softdog.](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4,4 Controleer en zorg ervoor dat softdog wordt uitgevoerd, zoals **op de** volgende knoop punten:
```
lsmod | grep dog
```
![Scherm afbeelding toont een deel van een console venster met het resultaat van het uitvoeren van de opdracht l s rest.](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4,5 start het SBD-apparaat op **beide** knoop punten
```
/usr/share/sbd/sbd.sh start
```
![Scherm afbeelding toont een deel van een console venster met de start opdracht.](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4,6 test de SBD-daemon op **beide** knoop punten. U ziet twee vermeldingen nadat u deze op **beide** knoop punten hebt geconfigureerd
```
sbd -d <SBD Device Name> list
```
![Scherm afbeelding toont een deel van een console venster waarin twee vermeldingen worden weer gegeven.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,7 een test bericht verzenden naar **een** van uw knoop punten
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![Scherm afbeelding toont een deel van een console venster waarin twee vermeldingen worden weer gegeven.](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4,8 op het **tweede** knoop punt (Knooppunt2) kunt u de bericht status controleren
```
sbd  -d <SBD Device Name> list
```
![Scherm afbeelding toont een deel van een console venster met een van de leden waarmee een test waarde voor het andere lid wordt weer gegeven.](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4,9 als u de SBD-configuratie wilt aannemen, werkt u het bestand */etc/sysconfig/SBD* als volgt bij. Het bestand op **beide** knoop punten bijwerken
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4,10 start de pacemaker-service op het **primaire knoop punt** (Knooppunt1)
```
systemctl start pacemaker
```
![Scherm afbeelding toont een console venster waarin de status wordt weer gegeven na het starten van pacemaker.](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Als de pacemaker-service *mislukt*, raadpleegt u *scenario 5: pacemaker-service mislukt*

## <a name="5---joining-the-cluster"></a>5. lid worden van het cluster
In deze sectie wordt beschreven hoe u het knoop punt aan het cluster kunt toevoegen.

### <a name="51-add-the-node"></a>5,1 het knoop punt toevoegen
Voer de volgende opdracht uit op **Knooppunt2** om Knooppunt2 aan het cluster toe te voegen.
```
ha-cluster-join
```
Als er een *fout optreedt* tijdens het toevoegen van het cluster, raadpleegt u *scenario 6: node 2 kan niet deel nemen aan het cluster*.

## <a name="6---validating-the-cluster"></a>6. het cluster wordt gevalideerd

### <a name="61-start-the-cluster-service"></a>6,1 de Cluster service starten
Om het cluster voor de eerste keer op **beide** knoop punten te controleren en te starten.
```
systemctl status pacemaker
systemctl start pacemaker
```
![Scherm afbeelding toont een console venster met de status pacemaker.](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6,2 de status controleren
Voer de opdracht uit *crm_mon* om ervoor te zorgen dat **beide** knoop punten online zijn. U kunt het uitvoeren op **een van de knoop punten** van het cluster
```
crm_mon
```
![Scherm afbeelding toont een console venster met de resultaten van c r m_mon.](media/HowToHLI/HASetupWithStonith/crm-mon.png)
U kunt zich ook aanmelden bij Hawk om de cluster status *https:// \<node IP> : 7630* te controleren. De standaard gebruiker is hacluster en het wacht woord is Linux. Indien nodig kunt u het wacht woord wijzigen met de opdracht *passwd* .

## <a name="7-configure-cluster-properties-and-resources"></a>7. cluster eigenschappen en-bronnen configureren 
In deze sectie worden de stappen beschreven voor het configureren van de cluster resources.
In dit voor beeld stelt u de volgende resource in, de rest kan worden geconfigureerd (indien nodig) door te verwijzen naar de gids van SUSE HA. Voer de configuratie alleen in **een van de knoop punten** uit. Doen op het primaire knoop punt.

- Cluster-Boots trap
- STONITH-apparaat
- Het virtuele IP-adres


### <a name="71-cluster-bootstrap-and-more"></a>7,1-cluster Boots trap en meer
Boots trap van cluster toevoegen. Maak het bestand en voeg de tekst als volgt toe:
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
Voeg de configuratie toe aan het cluster.
```
crm configure load update crm-bs.txt
```
![Scherm afbeelding toont een deel van een console venster waarop de opdracht c r m wordt uitgevoerd.](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7,2 STONITH-apparaat
Resource STONITH toevoegen. Maak het bestand en voeg de tekst als volgt toe.
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
Voeg de configuratie toe aan het cluster.
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7,3 het virtuele IP-adres
Voeg het virtuele IP-adres van de resource toe. Maak het bestand en voeg de volgende tekst toe.
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
Voeg de configuratie toe aan het cluster.
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7,4 de resources valideren

Wanneer u de opdracht *crm_mon* uitvoert, ziet u de twee resources.
![Scherm afbeelding toont een console venster met twee resources.](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

U kunt ook de status zien op *https:// \<node IP address> : 7630/CIB/Live/State*

![Scherm afbeelding toont de status van de twee resources.](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8. het failoverproces testen
Als u het failoverproces wilt testen, stopt u de pacemaker-service op Knooppunt1 en de failover van resources naar Knooppunt2.
```
Service pacemaker stop
```
Stop nu de pacemaker-service op **Knooppunt2** en bronnen waarvoor failover is uitgevoerd naar **Knooppunt1**

**Voor failover**  
![Scherm afbeelding toont de status van de twee resources vóór de failover.](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**Na een failover**  
![Scherm afbeelding toont de status van de twee resources na een failover.](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![Scherm afbeelding toont een console venster met de status van resources na een failover.](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9. problemen oplossen
In deze sectie worden de scenario's beschreven die kunnen worden aangetroffen tijdens de installatie. U hoeft deze problemen mogelijk niet op te lossen.

### <a name="scenario-1-cluster-node-not-online"></a>Scenario 1: cluster knooppunt is niet online
Als een van de knoop punten niet online in Cluster beheer wordt weer gegeven, kunt u het volgende proberen om deze online te brengen.

De iSCSI-service starten
```
service iscsid start
```

En nu moet u zich kunnen aanmelden bij dat iSCSI-knoop punt
```
iscsiadm -m node -l
```
De verwachte uitvoer ziet er als volgt uit
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>Scenario 2: de grafische weer gave wordt niet weer gegeven in YaST2
Het YaST2-grafisch scherm wordt gebruikt voor het instellen van het cluster met hoge Beschik baarheid in dit document. Als YaST2 niet wordt geopend met het grafische venster, zoals wordt weer gegeven en fout bij het genereren van Qt, voert u de volgende stappen uit. Als deze wordt geopend met het grafische venster, kunt u de stappen overs Laan.

**Fout**

![Scherm afbeelding toont een deel van een console venster met een fout bericht.](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**Verwachte uitvoer**

![Scherm afbeelding toont het YaST-beheer centrum met hoge Beschik baarheid en gemarkeerde cluster.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

Als de YaST2 niet wordt geopend met de grafische weer gave, volgt u de volgende stappen.

Installeer de vereiste pakketten. U moet zijn aangemeld als gebruiker ' root ' en instellen dat SMT is ingesteld om de pakketten te downloaden/installeren.

Als u de pakketten wilt installeren, gebruikt u YaST>software->software management>afhankelijkheden> optie Aanbevolen pakketten installeren.... In de volgende scherm afbeelding ziet u de verwachte schermen.
>[!NOTE]
>U moet de stappen op beide knoop punten uitvoeren, zodat u de grafische weer gave YaST2 van beide knoop punten kunt openen.

![Scherm afbeelding toont een console venster waarin het YaST-controle centrum wordt weer gegeven.](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

Selecteer onder afhankelijkheden de scherm afbeelding ' Aanbevolen pakketten installeren ' ![ geeft een console venster weer met de optie Aanbevolen pakketten installeren.](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

Bekijk de wijzigingen en klik op OK

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

![Scherm afbeelding van de installatie van het pakket toont een console venster waarin de voortgang van de installatie wordt weer gegeven.](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

Klik op Next

![Scherm afbeelding toont een console venster met een succes bericht.](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

Klik op volt ooien

U moet ook de libqt4-en libyui-qt-pakketten installeren.
```
zypper -n install libqt4
```
![Scherm afbeelding toont een console venster waarin het libqt4-pakket wordt geïnstalleerd.](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![Scherm afbeelding toont een console venster waarin het libyui-qt-pakket wordt geïnstalleerd. ](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
 ![ Scherm afbeelding toont een console venster waarin het libyui-qt-pakket wordt geïnstalleerd.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)
Yast2 moet de grafische weer gave nu kunnen openen, zoals hier wordt weer gegeven.
![Scherm afbeelding toont het YaST-configuratie centrum met software en online-update geselecteerd.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>Scenario 3: YaST2 biedt geen hoge Beschik baarheid
Als u de optie hoge Beschik baarheid wilt weer geven in het YaST2-beheer centrum, moet u de extra pakketten installeren.

De volgende patronen selecteren met behulp van YaST2>software>software beheer>

- SAP HANA Server-basis
- C/C++-compiler en-hulpprogram ma's
- Hoge beschikbaarheid
- SAP-toepassings server basis

In het volgende scherm ziet u de stappen voor het installeren van de patronen.

Software beheer van YaST2 > > gebruiken

![Scherm afbeelding toont het YaST-configuratie centrum met de software en de online-update die zijn geselecteerd om de installatie te starten.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

De patronen selecteren

![Scherm afbeelding toont u het eerste patroon in de C/C++-compiler en-Hulpprogram Ma's. ](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
 ![ Scherm afbeelding toont het selecteren van het tweede patroon in de C/C++-compiler en-Hulpprogram Ma's.](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

Klik op **accepteren**

![Scherm afbeelding toont het dialoog venster gewijzigde pakketten met pakketten gewijzigd om afhankelijkheden op te lossen.](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

Klik op **Continue**.

![Scherm afbeelding toont de pagina voor het uitvoeren van de installatie status.](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

Klik op **volgende** wanneer de installatie is voltooid

![Scherm afbeelding toont het installatie rapport.](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>Scenario 4: HANA-installatie mislukt met gcc-assemblages fout
De HANA-installatie mislukt met de volgende fout.

![Scherm afbeelding toont een fout bericht dat het besturings systeem niet gereed is om g c c 5-assembly's uit te voeren.](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

Om het probleem op te lossen, moet u de volgende bibliotheken (libgcc_sl en libstdc + + 6) installeren.

![Scherm afbeelding toont een console venster waarin de vereiste bibliotheken worden geïnstalleerd.](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>Scenario 5: pacemaker-service mislukt

Het volgende probleem is opgetreden tijdens het starten van de pacemaker-service.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

Om het probleem op te lossen, verwijdert u de volgende regel uit het bestand */usr/lib/systemd/System/fstrim.timer*

```
Persistent=true
```

![Scherm afbeelding toont het bestand f s met de waarde permanent = waar om te worden verwijderd.](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>Scenario 6: knoop punt 2 kan niet aan het cluster worden toegevoegd

Wanneer u de Knooppunt2 koppelt aan het bestaande cluster met behulp van de opdracht *HA-cluster-join* , is de volgende fout opgetreden.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![Scherm afbeelding toont een console venster met het fout bericht kan geen S S H-sleutels uit een I P-adres ophalen.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

Voer de volgende handelingen uit op de knoop punten om het probleem op te lossen

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![Scherm afbeelding toont een deel van een console venster waarop de opdracht wordt uitgevoerd op het eerste knoop punt.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![Scherm afbeelding toont een deel van een console venster waarop de opdracht op het tweede knoop punt wordt uitgevoerd.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

Na de voor gaande oplossing moet Knooppunt2 worden toegevoegd aan het cluster

![Scherm afbeelding toont een console venster met een voltooide HA-cluster-opdracht voor samen voegen.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10. Algemene documentatie
Meer informatie over de configuratie van SUSE HA vindt u in de volgende artikelen: 

- [SAP HANA scenario voor geoptimaliseerde SR-prestaties](https://www.suse.com/support/kb/doc/?id=000019450 )
- [Op opslag gebaseerde omheining](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [Blog-pacemaker-cluster gebruiken voor SAP HANA-deel 1](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [Blog-pacemaker-cluster gebruiken voor SAP HANA-deel 2](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
