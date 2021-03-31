---
title: Over de Mobility-service voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers met Azure Site Recovery | Microsoft Docs
description: Meer informatie over de Mobility Service-agent voor nood herstel van virtuele VMware-machines en fysieke servers naar Azure met behulp van de Azure Site Recovery-service.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: 8c90427e2dd4ecf23cb803c4f75dbee34c38f992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898576"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Over de Mobility-service voor VMware-Vm's en fysieke servers

Wanneer u herstel na nood gevallen instelt voor virtuele VMware-machines (VM) en fysieke servers met behulp van [Azure site Recovery](site-recovery-overview.md), installeert u de site Recovery Mobility-service op elke on-premises VMware-VM en fysieke server. De Mobility-service legt gegevens op de computer vast en stuurt deze door naar de Site Recovery-proces server. De Mobility-service wordt geïnstalleerd door de Mobility Service agent-software die u kunt implementeren met behulp van de volgende methoden:

- [Push-installatie](#push-installation): Wanneer beveiliging is ingeschakeld via de Azure Portal, site Recovery de Mobility-service op de server installeert.
- Hand matige installatie: u kunt de Mobility-service hand matig op elke computer installeren via de [gebruikers interface (UI)](#install-the-mobility-service-using-ui) of de [opdracht prompt](#install-the-mobility-service-using-command-prompt).
- [Geautomatiseerde implementatie](vmware-azure-mobility-install-configuration-mgr.md): u kunt de installatie van de Mobility-service automatiseren met hulpprogram ma's voor software-implementatie, zoals Configuration Manager.

> [!NOTE]
> De Mobility-service gebruikt ongeveer 6%-10% van het geheugen op de bron machines voor virtuele VMware-machines of fysieke computers.

## <a name="antivirus-on-replicated-machines"></a>Anti virus op gerepliceerde computers

Als op computers die u wilt repliceren, antivirus software wordt uitgevoerd, sluit u de installatiemap van de Mobility-service _C:\ProgramData\ASR\agent_ van antivirus bewerkingen uit. Met deze uitsluiting zorgt u ervoor dat de replicatie werkt zoals verwacht.

## <a name="push-installation"></a>Push-installatie

Push installatie is een integraal onderdeel van de taak die wordt uitgevoerd vanuit de Azure Portal om [replicatie in te scha kelen](vmware-azure-enable-replication.md#enable-replication). Nadat u de set Vm's hebt gekozen die u wilt beveiligen en replicatie wilt inschakelen, duwt de configuratie server de agent van de Mobility-service naar de servers, installeert de agent en voltooit hij de registratie van de agent met de configuratie server.

### <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat aan alle [vereisten](vmware-azure-install-mobility-service.md) voor de push-installatie is voldaan.
- Zorg ervoor dat alle server configuraties voldoen aan de criteria in de [ondersteunings matrix voor nood herstel van virtuele VMware-machines en fysieke servers naar Azure](vmware-physical-azure-support-matrix.md).
- Van 9,36-versie en hoger, voor SUSE Linux Enterprise Server 11 SP3, RHEL 5, CentOS 5, Debian 7 Controleer of het meest recente installatie programma [beschikbaar is op de configuratie server en scale-out proces server](#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server)

De push-installatie werk stroom wordt beschreven in de volgende secties:

### <a name="mobility-service-agent-version-923-and-higher"></a>Mobility Service-agent versie 9,23 en hoger

Zie [Update pakket 35 voor Azure site Recovery](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery)voor meer informatie over versie 9,23.

Tijdens een push-installatie van de Mobility-service worden de volgende stappen uitgevoerd:

1. De agent wordt naar de bron machine gepusht. Het kopiëren van de agent naar de bron machine kan mislukken vanwege meerdere omgevings fouten. Ga naar [onze richt lijnen](vmware-azure-troubleshoot-push-install.md) voor het oplossen van problemen met push-installatie.
1. Nadat de agent is gekopieerd naar de server, wordt een controle op vereisten uitgevoerd op de server.
   - Als aan alle vereisten wordt voldaan, wordt de installatie gestart.
   - De installatie mislukt als er niet aan een of meer van de [vereisten](vmware-physical-azure-support-matrix.md) wordt voldaan.
1. Als onderdeel van de agent installatie wordt de Volume Shadow Copy Service Provider (VSS) voor Azure Site Recovery geïnstalleerd. De VSS-provider wordt gebruikt voor het genereren van toepassings consistente herstel punten. Als de installatie van de VSS-provider mislukt, wordt deze stap overgeslagen en wordt de agent installatie voortgezet.
1. Als de agent is geïnstalleerd, maar de installatie van de VSS-provider is mislukt, wordt de taak status als **waarschuwing** gemarkeerd. Dit heeft geen invloed op het vastlopen van het genereren van herstel punten.

    - Als u toepassings consistente herstel punten wilt genereren, raadpleegt u [onze richt lijnen](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) voor het volt ooien van een hand matige installatie van de site Recovery VSS-provider.
    - Als u geen toepassings consistente herstel punten wilt genereren, wijzigt u [het replicatie beleid](vmware-azure-set-up-replication.md#create-a-policy) om toepassings consistente herstel punten uit te scha kelen.

### <a name="mobility-service-agent-version-922-and-below"></a>Mobility Service-agent versie 9,22 en lager

1. De agent wordt naar de bron machine gepusht. Het kopiëren van de agent naar de bron machine kan mislukken vanwege meerdere omgevings fouten. Raadpleeg [onze richt lijnen](vmware-azure-troubleshoot-push-install.md) voor het oplossen van problemen met push-installatie.
1. Nadat de agent is gekopieerd naar de server, wordt een controle op vereisten uitgevoerd op de server.
   - Als aan alle vereisten wordt voldaan, wordt de installatie gestart.
   - De installatie mislukt als er niet aan een of meer van de [vereisten](vmware-physical-azure-support-matrix.md) wordt voldaan.

1. Als onderdeel van de agent installatie wordt de Volume Shadow Copy Service Provider (VSS) voor Azure Site Recovery geïnstalleerd. De VSS-provider wordt gebruikt voor het genereren van toepassings consistente herstel punten.
   - Als de VSS-provider niet kan worden geïnstalleerd, mislukt de installatie van de agent. Om te voor komen dat de agent wordt geïnstalleerd, gebruikt u [versie 9,23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) of hoger om crash-consistente herstel punten te genereren en een hand matige installatie van de VSS-provider uit te voeren.

## <a name="install-the-mobility-service-using-ui"></a>De Mobility-service installeren met behulp van de gebruikers interface

### <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat alle server configuraties voldoen aan de criteria in de [ondersteunings matrix voor nood herstel van virtuele VMware-machines en fysieke servers naar Azure](vmware-physical-azure-support-matrix.md).
- [Zoek het installatie programma](#locate-installer-files) voor het besturings systeem van de server.

>[!IMPORTANT]
> Gebruik niet de installatie methode van de gebruikers interface als u een Azure-infra structuur als een service (IaaS) repliceert van de ene Azure-regio naar een andere. Gebruik de installatie van de [opdracht prompt](#install-the-mobility-service-using-command-prompt) .

1. Kopieer het installatie bestand naar de computer en voer dit uit.
1. Selecteer in **installatie optie** **Mobility service installeren**.
1. Kies de installatie locatie en selecteer **installeren**.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Pagina installatie optie voor Mobility-service.":::

1. Controleer de installatie in **voortgang** van de installatie. Nadat de installatie is voltooid, selecteert u **door gaan naar configuratie** om de service te registreren bij de configuratie server.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Scherm opname van de voortgang van de installatie en de knop actief door gaan naar configuratie wanneer de installatie is voltooid.":::

1. Geef in details van de **Configuratie server** het IP-adres en de wachtwoordzin op die u hebt geconfigureerd.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Pagina registratie van Mobility-service.":::

1. Selecteer **registreren** om de registratie te volt ooien.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="Laatste pagina Registratie Mobility-service.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>De Mobility-service installeren met behulp van de opdracht prompt

### <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat alle server configuraties voldoen aan de criteria in de [ondersteunings matrix voor nood herstel van virtuele VMware-machines en fysieke servers naar Azure](vmware-physical-azure-support-matrix.md).
- [Zoek het installatie programma](#locate-installer-files) voor het besturings systeem van de server.

### <a name="windows-machine"></a>Windows-computer

- Voer vanaf een opdracht prompt de volgende opdrachten uit om het installatie programma te kopiëren naar een lokale map, zoals _C:\Temp_, op de server die u wilt beveiligen. Vervang de naam van het installatie bestand door de daad werkelijke bestands naam.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- Voer deze opdracht uit om de agent te installeren.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- Voer deze opdrachten uit om de agent te registreren bij de configuratie server.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Installatie-instellingen

Instelling | Details
--- | ---
Syntax | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
Installatielogboeken | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Verplichte installatie parameter. Hiermee geeft u op of de Mobility-service (MS) of het hoofd doel (MT) moet worden geïnstalleerd.
`/InstallLocation`| Optionele parameter. Hiermee geeft u de installatie locatie van de Mobility-service (een map).
`/Platform` | Verplicht. Hiermee geeft u het platform op waarop de Mobility-service is geïnstalleerd: <br/> **VMware** voor virtuele VMware-machines/fysieke servers. <br/> **Azure** voor Azure-vm's.<br/><br/> Als u Azure-Vm's als fysieke machines behandelt, moet u **VMware** opgeven.
`/Silent`| Optioneel. Hiermee geeft u op of het installatie programma moet worden uitgevoerd in de Stille modus.

#### <a name="registration-settings"></a>Registratie-instellingen

Instelling | Details
--- | ---
Syntax | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Agent configuratie logboeken | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Verplichte para meter. `<CSIP>` Hiermee geeft u het IP-adres van de configuratie server. Gebruik een geldig IP-adres.
`/PassphraseFilePath` |  Verplicht. Locatie van de wachtwoordzin. Gebruik een geldig UNC-pad of een lokaal bestandspad.

### <a name="linux-machine"></a>Linux-computer

1. Kopieer vanuit een terminal sessie het installatie programma naar een lokale map, zoals _map/tmp_ op de server die u wilt beveiligen. Vervang de naam van het installatie bestand door de werkelijke bestands naam van de Linux-distributie en voer de opdrachten uit.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. Installeer dit als volgt:

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. Nadat de installatie is voltooid, moet de Mobility-service zijn geregistreerd bij de configuratie server. Voer de volgende opdracht uit om de Mobility-service te registreren bij de configuratie server.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Installatie-instellingen

Instelling | Details
--- | ---
Syntax | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Verplichte installatie parameter. Hiermee geeft u op of de Mobility-service (MS) of het hoofd doel (MT) moet worden geïnstalleerd.
`-d` | Optionele parameter. Hiermee geeft u de installatie locatie van de Mobility-service op: `/usr/local/ASR` .
`-v` | Verplicht. Hiermee geeft u het platform op waarop de Mobility-service is geïnstalleerd. <br/> **VMware** voor virtuele VMware-machines/fysieke servers. <br/> **Azure** voor Azure-vm's.
`-q` | Optioneel. Hiermee geeft u op of het installatie programma moet worden uitgevoerd in de Stille modus.

#### <a name="registration-settings"></a>Registratie-instellingen

Instelling | Details
--- | ---
Syntax | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Verplichte para meter. `<CSIP>` Hiermee geeft u het IP-adres van de configuratie server. Gebruik een geldig IP-adres.
`-P` |  Verplicht. Volledig bestandspad van het bestand waarin de wachtwoordzin wordt opgeslagen. Gebruik een geldige map.

## <a name="azure-virtual-machine-agent"></a>Agent van de virtuele machine van Azure

- **Windows-vm's**: van versie 9.7.0.0 van de Mobility-service wordt de [Azure VM-agent](../virtual-machines/extensions/features-windows.md#azure-vm-agent) geïnstalleerd door het installatie programma van de Mobility-service. Op deze manier zorgt u ervoor dat wanneer de computer failover naar Azure wordt uitgevoerd, de Azure VM voldoet aan de installatie vereisten van de agent voor het gebruik van een VM-extensie.
- **Virtuele Linux-machines**: de  [WALinuxAgent](../virtual-machines/extensions/update-linux-agent.md) moet hand matig worden geïnstalleerd op de virtuele Azure-machine na een failover.

## <a name="locate-installer-files"></a>Installatie bestanden zoeken

Ga op de configuratie server naar de map _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository_. Controleer welk installatie programma u nodig hebt op basis van het besturings systeem. De volgende tabel bevat een overzicht van de installatie bestanden voor elke VMware-VM en een fysiek Server besturingssysteem. Voordat u begint, kunt u de [ondersteunde besturings systemen](vmware-physical-azure-support-matrix.md#replicated-machines)bekijken.

> [!NOTE]
> De bestands namen gebruiken de syntaxis die in de volgende tabel wordt weer gegeven met _versie_ en _datum_ als tijdelijke aanduidingen voor de werkelijke waarden. De daad werkelijke bestands namen zien er ongeveer als volgt uit:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

Installatie bestand | Besturings systeem (alleen 64-bits)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
[Moeten worden gedownload en hand matig in deze map worden geplaatst](#rhel-5-or-centos-5-server) | Red Hat Enterprise Linux (RHEL) 5 </br> CentOS 5
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_RHEL8-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 8 </br> CentOS 8
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> Inclusief SP2 en SP3.
[Moeten worden gedownload en hand matig in deze map worden geplaatst](#suse-11-sp3-server) | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_SLES15-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 15 
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enter prise Linux 6,4 </br> Oracle Enter prise Linux 6,5
`Microsoft-ASR_UA_version_OL7-64_GA_date_release.tar.gz` | Oracle Enter prise Linux 7 
`Microsoft-ASR_UA_version_OL8-64_GA_date_release.tar.gz` | Oracle Enter prise Linux 8
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14,04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16,04 LTS-server
`Microsoft-ASR_UA_version_UBUNTU-18.04-64_GA_date_release.tar.gz` | Ubuntu Linux 18,04 LTS-server
`Microsoft-ASR_UA_version_UBUNTU-20.04-64_GA_date_release.tar.gz` | Ubuntu Linux 20,04 LTS-server
[Moeten worden gedownload en hand matig in deze map worden geplaatst](#debian-7-server) | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8
`Microsoft-ASR_UA_version_DEBIAN9-64_GA_date_release.tar.gz` | Debian 9

## <a name="download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server"></a>Nieuwste installatie programma van de Mobility-agent voor SUSE 11 SP3, RHEL 5, Debian 7-server downloaden

### <a name="suse-11-sp3-server"></a>SUSE 11 SP3-server

Als een **vereiste voor het bijwerken of beveiligen van SuSE Linux Enterprise Server 11 SP3-machines** vanaf 9,36-versie:

1. Zorg ervoor dat het nieuwste installatie programma van de Mobility-agent is gedownload van het micro soft Download centrum en in de push-installatie opslagplaats op de configuratie server en alle scale-out proces servers wordt geplaatst
2. [Down load](site-recovery-whats-new.md) het nieuwste installatie programma voor de agent van SuSE Linux Enterprise Server 11 SP3.
3. Navigeer naar configuratie server, kopieer het installatie programma van de agent van SUSE Linux Enterprise Server 11 SP3 op het pad INSTALL_DIR \home\svsystems\pushinstallsvc\repository
1. Nadat u het nieuwste installatie programma hebt gekopieerd, start u de inmage PushInstall-service opnieuw. 
1. Ga nu naar de bijbehorende scale-out proces servers, herhaal stap 3 en stap 4.
1. Als installatiepad **bijvoorbeeld** C:\Program Files (x86) \Microsoft Azure site Recovery, worden de hierboven genoemde directory's
    1. C:\Program Files (x86) \Microsoft Azure site Recovery\home\svsystems\pushinstallsvc\repository

### <a name="rhel-5-or-centos-5-server"></a>RHEL 5-of CentOS 5-server

Als **u een vereiste hebt om RHEL 5-machines bij te werken of te beschermen** van 9,36-versie, kunt u het volgende doen:

1. Zorg ervoor dat het nieuwste installatie programma van de Mobility-agent is gedownload van het micro soft Download centrum en in de push-installatie opslagplaats op de configuratie server en alle scale-out proces servers wordt geplaatst
2. [Down load](site-recovery-whats-new.md) het meest recente RHEL 5-of CentOS 5-agent installatie programma. 
3. Navigeer naar configuratie server, kopieer het installatie programma van de agent voor RHEL 5 of CentOS 5 op het pad-INSTALL_DIR \home\svsystems\pushinstallsvc\repository
1. Nadat u het nieuwste installatie programma hebt gekopieerd, start u de inmage PushInstall-service opnieuw. 
1. Ga nu naar de bijbehorende scale-out proces servers, herhaal stap 3 en stap 4.
1. Als installatiepad **bijvoorbeeld** C:\Program Files (x86) \Microsoft Azure site Recovery, worden de hierboven genoemde directory's
    1. C:\Program Files (x86) \Microsoft Azure site Recovery\home\svsystems\pushinstallsvc\repository

## <a name="debian-7-server"></a>Debian 7-server

Als een **vereiste voor het bijwerken of beveiligen van Debian 7-computers** vanaf 9,36-versie:

1. Zorg ervoor dat het nieuwste installatie programma van de Mobility-agent is gedownload van het micro soft Download centrum en in de push-installatie opslagplaats op de configuratie server en alle scale-out proces servers wordt geplaatst
2. [Down load](site-recovery-whats-new.md) het nieuwste installatie programma voor de Debian 7-agent.
3. Navigeer naar configuratie server, kopieer het installatie programma van de Debian 7-agent op het pad-INSTALL_DIR \home\svsystems\pushinstallsvc\repository
1. Nadat u het nieuwste installatie programma hebt gekopieerd, start u de inmage PushInstall-service opnieuw. 
1. Ga nu naar de bijbehorende scale-out proces servers, herhaal stap 3 en stap 4.
1. Als installatiepad **bijvoorbeeld** C:\Program Files (x86) \Microsoft Azure site Recovery, worden de hierboven genoemde directory's
    1. C:\Program Files (x86) \Microsoft Azure site Recovery\home\svsystems\pushinstallsvc\repository

## <a name="next-steps"></a>Volgende stappen

[Stel een push-installatie in voor de Mobility-service](vmware-azure-install-mobility-service.md).
