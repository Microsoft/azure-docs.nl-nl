---
title: De Mobility-service automatiseren voor nood herstel van de installatie in Azure Site Recovery
description: De Mobility-service voor VMware/Physical server-herstel na nood gevallen automatisch installeren met Azure Site Recovery.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: 2159ab8c2639f0f87fd53e8559dad518a3daa663
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92544814"
---
# <a name="automate-mobility-service-installation"></a>De installatie van de Mobility-service automatiseren

In dit artikel wordt beschreven hoe u de installatie en updates voor de Mobility Service-agent in [Azure site Recovery](site-recovery-overview.md)kunt automatiseren.

Wanneer u Site Recovery implementeert voor herstel na nood gevallen van on-premises VMware-Vm's en fysieke servers naar Azure, installeert u de Mobility Service-agent op elke computer die u wilt repliceren. De Mobility-service legt gegevens op de computer vast en stuurt deze door naar de Site Recovery process-server voor replicatie. U kunt de Mobility-service op een aantal manieren implementeren:

- **Push-installatie**: laat site Recovery de Mobility Service-agent installeren wanneer u replicatie inschakelt voor een computer in de Azure Portal.
- **Hand matige installatie**: Installeer de Mobility-service hand matig op elke computer. Meer [informatie](vmware-physical-mobility-service-overview.md) over push en hand matige installatie.
- **Geautomatiseerde implementatie**: de installatie automatiseren met hulpprogram ma's voor software-implementatie, zoals micro soft endpoint Configuration Manager, of hulpprogram ma's van derden, zoals JetPatch.

Automatische installatie en update biedt een oplossing als:

- Uw organisatie staat geen push-installatie op beveiligde servers toe.
- Uw bedrijfs beleid vereist dat wacht woorden periodiek worden gewijzigd. U moet een wacht woord opgeven voor de push-installatie.
- Uw beveiligings beleid staat het toevoegen van Firewall uitzonderingen voor specifieke computers niet toe.
- U fungeert als hosting serviceprovider en wilt geen referenties voor de computer van de klant opgeven die nodig zijn voor een push-installatie met Site Recovery.
- U moet de installatie van agents naar een groot aantal servers tegelijkertijd schalen.
- U wilt installaties en upgrades plannen tijdens geplande onderhouds Vensters.

## <a name="prerequisites"></a>Vereisten

Als u de installatie wilt automatiseren, hebt u de volgende items nodig:

- Een geïmplementeerde oplossing voor software-installatie, zoals [Configuration Manager](/configmgr/) of [JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/).
- Implementatie vereisten in [Azure](tutorial-prepare-azure.md) en [on-premises](vmware-azure-tutorial-prepare-on-premises.md) voor VMware-herstel na nood gevallen, of voor herstel na nood gevallen voor de [fysieke server](physical-azure-disaster-recovery.md) . Bekijk de [ondersteunings vereisten](vmware-physical-azure-support-matrix.md) voor herstel na nood gevallen.

## <a name="prepare-for-automated-deployment"></a>Automatische implementatie voorbereiden

De volgende tabel bevat een overzicht van de hulpprogram ma's en processen voor het automatiseren van de implementatie van de Mobility-service.

**Hulpprogramma** | **Details** | **Instructies**
--- | --- | ---
**Configuration Manager** | 1. Controleer of u de hierboven vermelde [vereisten](#prerequisites) hebt. <br/><br/> 2. Implementeer herstel na nood gevallen door de bron omgeving in te stellen, inclusief het downloaden van een bestand met de Site Recovery configuratie server als een VMware-VM met behulp van een OVF-sjabloon.<br/><br/> 3. u registreert de configuratie server bij de Site Recovery-service, stelt de doel-Azure-omgeving in en configureert een replicatie beleid.<br/><br/> 4. voor automatische implementatie van Mobility-Services maakt u een netwerk share met de configuratie server wachtwoordzin en de Mobility service-installatie bestanden.<br/><br/> 5. u maakt een Configuration Manager-pakket met de installatie of updates en bereidt de implementatie van de Mobility-service voor.<br/><br/> 6. u kunt vervolgens replicatie naar Azure inschakelen voor de computers waarop de Mobility-service is geïnstalleerd. | [Automatiseren met Configuration Manager](#automate-with-configuration-manager)
**JetPatch** | 1. Controleer of u de hierboven vermelde [vereisten](#prerequisites) hebt. <br/><br/> 2. Implementeer herstel na nood gevallen door de bron omgeving in te stellen, met inbegrip van het downloaden en implementeren van JetPatch agent Manager voor Azure Site Recovery in uw Site Recovery omgeving, met behulp van een OVF-sjabloon.<br/><br/> 3. u registreert de configuratie server bij Site Recovery, stelt de doel-Azure-omgeving in en configureert een replicatie beleid.<br/><br/> 4. voor automatische implementatie initialiseert en voltooit u de configuratie van JetPatch agent Manager.<br/><br/> 5. in JetPatch kunt u een Site Recovery-beleid maken om de implementatie en upgrade van de Mobility Service-agent te automatiseren. <br/><br/> 6. u kunt vervolgens replicatie naar Azure inschakelen voor de computers waarop de Mobility-service is geïnstalleerd. | [Automatiseren met JetPatch agent Manager](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Problemen met de installatie van de agent in JetPatch oplossen](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Automatiseren met Configuration Manager

### <a name="prepare-the-installation-files"></a>De installatie bestanden voorbereiden

1. Zorg ervoor dat de vereiste onderdelen aanwezig zijn.
1. Maak een beveiligde netwerk bestands share (SMB-share) die toegankelijk is voor de computer waarop de configuratie server wordt uitgevoerd.
1. In Configuration Manager [categoriseert u de servers](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) waarop u de Mobility-service wilt installeren of bijwerken. Eén verzameling moet alle Windows-servers, de andere alle Linux-servers bevatten.
1. Maak een map op de netwerk share:

   - Voor installatie op Windows-computers maakt u een map met de naam _MobSvcWindows_.
   - Voor installatie op Linux-machines maakt u een map met de naam _MobSvcLinux_.

1. Meld u aan bij de computer met de configuratie server.
1. Open een opdracht prompt met beheerders rechten op de computer met de configuratie server.
1. Als u het wachtwoordzinbestand wilt genereren, voert u deze opdracht uit:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Kopieer het bestand _MobSvc. wachtwoordzin_ naar de map Windows en de map Linux.
1. Als u wilt bladeren naar de map met de installatie bestanden, voert u deze opdracht uit:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Kopieer de volgende installatie bestanden naar de netwerk share:

   - Voor Windows kopieert u _Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ naar _MobSvcWindows_.
   - Voor Linux kopieert u de volgende bestanden naar _MobSvcLinux_:
     - _Micro soft-ASR_UARHEL6 -64release. tar. gz_
     - _Micro soft-ASR_UARHEL7 -64release. tar. gz_
     - _Micro soft-ASR_UASLES11-SP3-64release. tar. gz_
     - _Micro soft-ASR_UASLES11-SP4-64release. tar. gz_
     - _Micro soft-ASR_UAOL6 -64release. tar. gz_
     - _Micro soft-ASR_UAUBUNTU-14.04 -64release. tar. gz_

1. Zoals beschreven in de volgende procedures kopieert u de code naar de Windows-of Linux-mappen. We gaan ervan uit dat:

   - Het IP-adres van de configuratie server is `192.168.3.121` .
   - De beveiligde netwerk bestands share is `\\ContosoSecureFS\MobilityServiceInstallers` .

### <a name="copy-code-to-the-windows-folder"></a>Code kopiëren naar de Windows-map

Kopieer de volgende code:

- Sla de code in de map _MobSvcWindows_ op als _install.bat_.
- Vervang de `[CSIP]` tijdelijke aanduidingen in dit script door de werkelijke waarden van het IP-adres van de configuratie server.
- Het script ondersteunt nieuwe installaties van de Mobility Service-agent en updates van agents die al zijn geïnstalleerd.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Code kopiëren naar de Linux-map

Kopieer de volgende code:

- Sla de code in de map _MobSvcLinux_ op als _install_linux. sh_.
- Vervang de `[CSIP]` tijdelijke aanduidingen in dit script door de werkelijke waarden van het IP-adres van de configuratie server.
- Het script ondersteunt nieuwe installaties van de Mobility Service-agent en updates van agents die al zijn geïnstalleerd.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Een pakket maken

1. Meld u aan bij de Configuration Manager-console en ga naar **software bibliotheek**  >  **Application Management**-  >  **pakketten**.
1. Klik met de rechter muisknop op **pakketten**  >  **maken pakket**.
1. Geef pakket Details op, inclusief een naam, beschrijving, fabrikant, taal en versie.
1. Selecteer **Dit pakket bevat bron bestanden**.
1. Klik op **Bladeren** en selecteer de netwerk share en de map die het relevante installatie programma bevat (_MobSvcWindows_ of _MobSvcLinux_). Selecteer vervolgens **Volgende**.

   ![Scherm opname van de wizard pakket en programma maken](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. Selecteer in **het programma type kiezen dat u wilt maken de** optie **standaard programma**  >  **volgende**.

   ![Scherm opname van de wizard pakket en programma maken waarin de standaard programma optie wordt weer gegeven.](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. Geef bij **Geef informatie op over deze standaard programma** pagina de volgende waarden op:

    **Parameter** | **Windows-waarde** | **Linux-waarde**
    --- | --- | ---
    **Naam** | Microsoft Azure Mobility-service installeren (Windows) | Installeer Microsoft Azure Mobility service (Linux).
    **Opdrachtregel** | install.bat | ./install_linux. sh
    **Het programma kan worden uitgevoerd** | Ongeacht of er een gebruiker is aangemeld of niet | Ongeacht of er een gebruiker is aangemeld of niet
    **Andere para meters** | Standaard instelling gebruiken | Standaard instelling gebruiken

   ![Scherm afbeelding met de informatie die u kunt opgeven voor het standaard programma.](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. Voer de volgende taken uit in **de vereisten voor dit standaard programma opgeven**:

   - Voor Windows-computers selecteert u **dit programma kan alleen op de opgegeven platforms worden uitgevoerd**. Selecteer vervolgens de [ondersteunde Windows-besturings systemen](vmware-physical-azure-support-matrix.md#replicated-machines) en selecteer **volgende**.
   - Voor Linux-machines selecteert u **dit programma kan op elk platform worden uitgevoerd**. Selecteer vervolgens **Volgende**.

1. Sluit de wizard af.

### <a name="deploy-the-package"></a>Het pakket implementeren

1. Klik in de Configuration Manager-console met de rechter muisknop op het pakket en selecteer **inhoud distribueren**.

   ![Scherm opname van Configuration Manager-console](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Selecteer de distributie punten waarop de pakketten moeten worden gekopieerd. [Meer informatie](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Voltooi de wizard. Het pakket begint vervolgens met het repliceren naar de opgegeven distributie punten.
1. Nadat de pakket distributie is voltooid, klikt u met de rechter muisknop op het pakket > **implementeren**.

   ![Scherm afbeelding van de Configuration Manager-console waarin de menu optie implementeren wordt weer gegeven.](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Selecteer de Windows-of Linux-apparaatgroep die u eerder hebt gemaakt.
1. Selecteer op de pagina **de doel inhoud opgeven** **distributie punten**.
1. Stel het **doel** in op **vereist** **om te bepalen hoe deze software wordt geïmplementeerd op de** pagina.

   ![Scherm opname van de wizard software implementeren](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. Stel in **de planning voor deze implementatie** een planning in. [Meer informatie](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - De Mobility-service wordt geïnstalleerd volgens het schema dat u opgeeft.
   - Om te voor komen dat het systeem onnodig opnieuw wordt opgestart, moet u de installatie van het pakket plannen tijdens het maandelijkse onderhouds venster of het venster software-updates

1. Configureer op de pagina **distributie punten** de instellingen en voltooi de wizard.
1. Bewaak de voortgang van de implementatie in de Configuration Manager-console. Ga naar **bewakings**  >  **implementaties**  >  _\<your package name\>_ .

### <a name="uninstall-the-mobility-service"></a>De Mobility-service verwijderen

U kunt Configuration Manager-pakketten maken voor het verwijderen van de Mobility-service. Met het volgende script wordt de Mobility-service bijvoorbeeld verwijderd:

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>Volgende stappen

[Replicatie inschakelen](vmware-azure-enable-replication.md) voor vm's.
