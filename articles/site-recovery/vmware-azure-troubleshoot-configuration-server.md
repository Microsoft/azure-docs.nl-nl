---
title: Problemen met de configuratie Server oplossen tijdens nood herstel van virtuele VMware-machines en fysieke servers naar Azure met behulp van Azure Site Recovery | Microsoft Docs
description: In dit artikel vindt u informatie over het oplossen van problemen met het implementeren van de configuratie server voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers naar Azure met behulp van Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: b5fd014732fd4cdfaa52f971b5e4d2c74db580d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371950"
---
# <a name="troubleshoot-configuration-server-issues"></a>Problemen met de configuratieserver oplossen

Dit artikel helpt u bij het oplossen van problemen bij het implementeren en beheren van de [Azure site Recovery](site-recovery-overview.md) configuratie server. De configuratie server fungeert als een beheer server. Gebruik de configuratie server om herstel na nood gevallen van on-premises virtuele VMware-machines en fysieke servers naar Azure in te stellen met behulp van Site Recovery. In de volgende secties worden de meest voorkomende fouten besproken die u kunt ondervinden wanneer u een nieuwe configuratie server toevoegt en wanneer u een configuratie server beheert.

## <a name="registration-failures"></a>Registratiefouten

De bron machine wordt geregistreerd bij de configuratie server wanneer u de Mobility-Agent installeert. U kunt tijdens deze stap fouten opsporen tijdens de volgende richt lijnen:

1. Open het bestand C:\ProgramData\ASR\home\svsystems\var\ configurator_register_host_static_info. log. (De map Program Data is mogelijk een verborgen map. Als de map Program Data niet wordt weer gegeven, gaat u in Verkenner naar het tabblad **weer** geven **/verbergen** en schakelt u het selectie vakje **verborgen items** in.) Fouten kunnen worden veroorzaakt door meerdere problemen.

2. Zoeken naar de teken reeks **geen geldig IP-adres gevonden**. Als de teken reeks wordt gevonden:
   1. Controleer of de aangevraagde host-ID hetzelfde is als de host-ID van de bron machine.
   2. Controleer of er ten minste één IP-adres aan de fysieke NIC is toegewezen aan de bron machine. Voor agent registratie met de configuratie server moet ten minste één geldig IP v4-adres aan de fysieke NIC zijn toegewezen.
   3. Voer een van de volgende opdrachten uit op de bron computer om alle IP-adressen van de bron computer op te halen:
      - Voor Windows: `> ipconfig /all`
      - Voor Linux: `# ifconfig -a`

3. Als de teken reeks **geen geldig IP-adres** gevonden is, zoekt u naar de teken reeks **reden =>null**. Deze fout treedt op als de bron machine een lege host gebruikt voor registratie bij de configuratie server. Als de teken reeks wordt gevonden:
    - Nadat u de problemen hebt opgelost, volgt u de richt lijnen in [REGI Steren van de bron machine bij de configuratie server](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) om de registratie hand matig opnieuw uit te voeren.

4. Als de teken reeks **reden =>null** niet wordt gevonden, opent u het bestand C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log op de bron machine. (De map Program Data is mogelijk een verborgen map. Als de map Program Data niet wordt weer gegeven, gaat u in Verkenner naar het tabblad **weer** geven **/verbergen** en schakelt u het selectie vakje **verborgen items** in.) Fouten kunnen worden veroorzaakt door meerdere problemen. 

5. Zoeken naar de teken reeks **post-aanvraag: (7)-kan geen verbinding maken met de server**. Als de teken reeks wordt gevonden:
    1. Los de netwerk problemen tussen de bron computer en de configuratie server op. Controleer of de configuratie server bereikbaar is vanaf de bron computer met behulp van netwerk hulpprogramma's als ping, traceroute of een webbrowser. Zorg ervoor dat de bron machine de configuratie server kan bereiken via poort 443.
    2. Controleer of de firewall regels op de bron machine de verbinding tussen de bron computer en de configuratie server blok keren. Werk samen met uw netwerk beheerders om verbindings problemen te blok keren.
    3. Zorg ervoor dat de mappen die worden vermeld in [site Recovery map uitsluitingen van antivirus Programma's](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) worden uitgesloten van de antivirus software.
    4. Wanneer netwerk problemen zijn opgelost, voert u de registratie opnieuw uit door de richt lijnen te volgen in de [bron machine registreren bij de configuratie server](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Als de teken reeks **post-aanvraag: (7)-kan geen verbinding maken met de server** is niet gevonden. Zoek in hetzelfde logboek bestand naar de teken reeks **aanvraag: (60)-peer certificaat kan niet worden geverifieerd met de opgegeven CA-certificaten**. Deze fout kan optreden omdat het configuratie server certificaat is verlopen of de bron machine geen ondersteuning biedt voor TLS 1,0-of later-protocollen. Dit kan ook optreden als een firewall TLS-communicatie blokkeert tussen de bron computer en de configuratie server. Als de teken reeks wordt gevonden: 
    1. Als u wilt oplossen, maakt u verbinding met het IP-adres van de configuratie server met behulp van een webbrowser op de bron machine. Gebruik de URI https: \/ \/<IP-adres van de configuratie server \> : 443/. Zorg ervoor dat de bron machine de configuratie server kan bereiken via poort 443.
    2. Controleer of er firewall regels op de bron machine moeten worden toegevoegd of verwijderd voor de bron machine om te communiceren met de configuratie server. Vanwege de verschillende firewall software die mogelijk in gebruik is, kunnen we niet alle vereiste firewall configuraties weer geven. Werk samen met uw netwerk beheerders om verbindings problemen te blok keren.
    3. Zorg ervoor dat de mappen die worden vermeld in [site Recovery map uitsluitingen van antivirus Programma's](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) worden uitgesloten van de antivirus software.  
    4. Nadat u de problemen hebt opgelost, voert u de registratie opnieuw uit door de richt lijnen te volgen in [Registreer de bron machine bij de configuratie server](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. Als de waarde van het platform in <INSTALLATION_DIR \> /etc/drscout.conf is beschadigd, mislukt de registratie op Linux. Als u dit probleem wilt identificeren, opent u het bestand/var/log/ua_install. log. Zoek naar de teken reeks **die de configuratie afbreekt, omdat VM_PLATFORM waarde Null is of niet VMware/Azure is**. Het platform moet worden ingesteld op **VMware** of **Azure**. Als het bestand drscout. conf beschadigd is, raden wij u aan [de Mobility-agent te verwijderen](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) en vervolgens de Mobility-agent opnieuw te installeren. Als de installatie mislukt, voert u de volgende stappen uit: a. Open het/Uninstall.sh-bestand van de Installation_Directory en Bekijk de aanroep van de functie **StopServices** .
    b. Open het/VX/bin/uninstall.sh-bestand van de Installation_Directory en opmerkingen bij de aanroep van de functie **stop_services** .
    c. Open het/FX/uninstall.sh-bestand van Installation_Directory en bewerk de volledige sectie waarin wordt geprobeerd de FX-service te stoppen.
    d. [Verwijder](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) de Mobility-agent. Nadat de installatie is voltooid, start u het systeem opnieuw op en probeert u de Mobility-agent opnieuw te installeren.

8. Zorg ervoor dat multi-factor Authentication niet is ingeschakeld voor een gebruikers account. Azure Site Recovery biedt momenteel geen ondersteuning voor multi-factor Authentication voor gebruikers accounts. Registreer de configuratie server zonder multi-factor Authentication-gebruikers account.  

## <a name="installation-failure-failed-to-load-accounts"></a>Installatie fout: kan de accounts niet laden

Deze fout treedt op wanneer de service geen gegevens kan lezen van de transport verbinding tijdens de installatie van de Mobility-agent en registratie bij de configuratie server. Om het probleem op te lossen, moet u ervoor zorgen dat TLS 1,0 is ingeschakeld op de bron computer.

## <a name="vcenter-discovery-failures"></a>fouten bij vCenter-detectie

Als u fouten in de vCenter-detectie wilt oplossen, voegt u de vCenter-Server toe aan de proxy-instellingen voor overs Laan. 

- Down load het PsExec-hulp programma van [hier](/sysinternals/downloads/psexec) om toegang te krijgen tot inhoud van het systeem gebruiker.
- Open Internet Explorer in de inhoud van systeem gebruikers door de volgende opdracht regel PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe" uit te voeren.
- Voeg proxy-instellingen in IE toe en start de tmanssvc-service opnieuw.
- Als u DRA-proxy-instellingen wilt configureren, voert u cd C:\Program Files\Microsoft Azure Site Recovery provider uit.
- Voer vervolgens het volgende uit DRCONFIGURATOR.EXE/configure/AddBypassUrls [Voeg IP-adres/FQDN toe van vCenter Server die worden meegeleverd tijdens de configuratie van **vCenter Server/VSphere ESXi server** stap van de [implementatie van Configuration Server](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Het IP-adres van de configuratie server wijzigen

We raden u ten zeerste aan het IP-adres van een configuratie server niet te wijzigen. Zorg ervoor dat alle IP-adressen die zijn toegewezen aan de configuratie server statische IP-adressen zijn. Gebruik geen DHCP IP-adressen.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: het SAML-token is ongeldig

Als u deze fout wilt voor komen, moet u ervoor zorgen dat de tijd op de systeem klok niet meer dan 15 minuten afwijkt van de lokale tijd. Voer het installatieprogramma opnieuw uit om de registratie te voltooien.

## <a name="failed-to-create-a-certificate"></a>Kan geen certificaat maken

Een certificaat dat vereist is voor verificatie Site Recovery kan niet worden gemaakt. Voer Setup opnieuw uit nadat u ervoor hebt gezorgd dat u Setup uitvoert als een lokale beheerder.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Kan Windows-licentie niet activeren van Server Standard-evaluatie naar Server Standard

1. Als onderdeel van de implementatie van de configuratie server via OVF, wordt een evaluatie licentie gebruikt, die gedurende 180 dagen geldig is. U moet deze licentie activeren voordat deze is verlopen. Dit kan ook leiden tot het veelvuldig afsluiten van de configuratie server en kan leiden tot replicatie activiteiten.
2. Neem contact op met het [ondersteunings team van Windows](https://aka.ms/Windows_Support) om het probleem op te lossen als u de Windows-licentie niet kunt activeren.

## <a name="register-source-machine-with-configuration-server"></a>Bron machine bij de configuratie server registreren

### <a name="if-the-source-machine-runs-windows"></a>Als Windows op de bron machine wordt uitgevoerd

Voer de volgende opdracht uit op de bron computer:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Instelling | Details
--- | ---
Gebruik | UnifiedAgentConfigurator.exe/CSEndPoint <IP-adres van de configuratie server \> /PassphraseFilePath <pad naar een wachtwoordzin bestand\>
Agent configuratie logboeken | Bevindt zich onder%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Verplichte para meter. Hiermee geeft u het IP-adres van de configuratie server. Gebruik een geldig IP-adres.
/PassphraseFilePath |  Verplicht. De locatie van de wachtwoordzin. Gebruik een geldig UNC-pad of een lokaal bestandspad.

### <a name="if-the-source-machine-runs-linux"></a>Als de bron machine Linux uitvoert

Voer de volgende opdracht uit op de bron computer:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Instelling | Details
--- | ---
Gebruik | cd-/usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh-i <IP-adres van de configuratie server \> -P <pad naar de wachtwoordzin\>
-i | Verplichte para meter. Hiermee geeft u het IP-adres van de configuratie server. Gebruik een geldig IP-adres.
-P |  Verplicht. Het volledige bestandspad van het bestand waarin de wachtwoordzin wordt opgeslagen. Gebruik een geldige map.

## <a name="unable-to-configure-the-configuration-server"></a>Kan de configuratie server niet configureren

Als u andere toepassingen dan de configuratie server op de virtuele machine installeert, kunt u het hoofd doel mogelijk niet configureren. 

De configuratie server moet een server met één doel zijn en kan niet worden gebruikt als een gedeelde server niet wordt ondersteund. 

Zie de veelgestelde vragen over configuratie in [een configuratie server implementeren](vmware-azure-deploy-configuration-server.md#faqs)voor meer informatie. 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>De verouderde vermeldingen voor beveiligde items verwijderen uit de data base van de configuratie server 

Gebruik de volgende stappen om de verouderde beveiligde machine te verwijderen op de configuratie server. 
 
1. Bepalen van de bron computer en het IP-adres van de verlopen vermelding: 

    1. Open de MYSQL-opdracht regel in de beheerders modus. 
    2. Voer de volgende opdrachten uit. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Hiermee wordt de lijst met geregistreerde machines samen met hun IP-adressen en de laatste kern slag geretourneerd. Zoek de host met verouderde replicatie paren.

2. Open een opdracht prompt met verhoogde bevoegdheid en navigeer naar C:\ProgramData\ASR\home\svsystems\bin. 
4. Voer de volgende opdracht uit met behulp van de bron computer en het IP-adres van de verlopen vermelding om de gegevens van de geregistreerde hosts en de verouderde vermelding van de configuratie server te verwijderen. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Als u een bron server vermelding van ' premises-VM01 ' met een IP-adres van 10.0.0.4 hebt, gebruikt u in plaats daarvan de volgende opdracht.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Start de volgende services op de bron computer opnieuw op om de registratie bij de configuratie server ongedaan te maken. 
 
    - InMage Scout-toepassingsservice
    - Inmage Scout-agent-Sentinel-Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>De upgrade mislukt wanneer de services niet worden gestopt

De upgrade van de configuratie server mislukt wanneer bepaalde services niet worden gestopt. 

Als u het probleem wilt identificeren, gaat u naar C:\ProgramData\ASRSetupLogs\ CX_TP_InstallLogFile op de configuratie server. Als u de volgende fouten vindt, gebruikt u de onderstaande stappen om het probleem op te lossen: 

```output
2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
2018-06-28 14:28:12.944   Stopping svagents service.
2018-06-28 14:31:32.949   Unable to stop svagents service.
2018-06-28 14:31:32.949   Stopping svagents service.
2018-06-28 14:34:52.960   Unable to stop svagents service.
2018-06-28 14:34:52.960   Stopping svagents service.
2018-06-28 14:38:12.971   Unable to stop svagents service.
2018-06-28 14:38:12.971   Rolling back the install changes.
2018-06-28 14:38:12.971   Upgrade has failed.
```

Het probleem oplossen:

Stop de volgende services hand matig:

- cxprocessserver
- Inmage Scout-agent: Sentinel/Outpost, 
- Microsoft Azure Recovery Services-agent, 
- Microsoft Azure Site Recovery-service, 
- tmansvc
  
Als u de configuratie server wilt bijwerken, voert u de [Unified Setup](service-updates-how-to.md#links-to-currently-supported-update-rollups) opnieuw uit.

## <a name="azure-active-directory-application-creation-failure"></a>Fout bij het maken van Azure Active Directory toepassing

U hebt onvoldoende machtigingen voor het maken van een toepassing in Azure Active Directory (AAD) met behulp van de [Open Virtualization Application (eicellen)-](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template
) sjabloon.

Om het probleem op te lossen, meldt u zich aan bij de Azure Portal en voert u een van de volgende handelingen uit:

- Vraag de ontwikkelaar van de toepassing aan bij AAD. Zie [Administrator role permissions in azure Active Directory](../active-directory/roles/permissions-reference.md)voor meer informatie over de ontwikkelaar van de toepassing.
- Controleer of de **gebruiker kan een toepassings vlag maken** in Aad is ingesteld op *True* . Zie [How to: de portal gebruiken om een Azure AD-toepassing en Service-Principal te maken die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)voor meer informatie.

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>Proces server/Master doel kunnen niet communiceren met de configuratie server 

De module proces server (PS) en Master target (MT) kan niet communiceren met de configuratie server (CS) en de status wordt weer gegeven als niet verbonden op Azure Portal.

Dit wordt meestal veroorzaakt door een fout met poort 443. Gebruik de volgende stappen om de poort te deblokkeren en communicatie met de CS opnieuw in te scha kelen.

**Controleren of de MARS-agent wordt aangeroepen door de hoofddoel agent**

Als u wilt controleren of de hoofddoel agent een TCP-sessie kan maken voor het IP-adres van de configuratie server, zoekt u naar een tracering die vergelijkbaar is met de volgende in de logboeken van de hoofd doel agent:

TCP \<Replace IP with CS IP here> : 52739 \<Replace IP with CS IP here> : 443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT//Vervang het IP-adres door het CS-IP-adres

Als u traceringen vindt die vergelijkbaar zijn met de volgende in de logboeken van de MT-agent, worden de MT-agent fouten gerapporteerd op poort 443:

```output
#~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
#~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
```
 
Deze fout kan optreden als andere toepassingen ook poort 443 gebruiken of als gevolg van een firewall instelling die de poort blokkeert.

Het probleem oplossen:

- Controleer of poort 443 niet is geblokkeerd door uw firewall.
- Als de poort niet bereikbaar is vanwege een andere toepassing die deze poort gebruikt, stopt en verwijdert u de app.
  - Als het stoppen van de app niet haalbaar is, stelt u een nieuwe schone CS in.
- Start de configuratie server opnieuw op.
- Start de IIS-service opnieuw.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>De configuratie server is niet verbonden vanwege onjuiste UUID-vermeldingen

Deze fout kan optreden als er meerdere UUID-items van een configuratie server (CS) in de-Data Base zijn. Het probleem treedt vaak op wanneer u de VM van de configuratie server kloont.

Het probleem oplossen:

1. Verwijder de verouderde/oude CS-VM uit de vCenter. Zie  [servers verwijderen en beveiliging uitschakelen](site-recovery-manage-registration-and-protection.md)voor meer informatie.
2. Meld u aan bij de configuratie Server-VM en maak verbinding met de MySQL svsdb1-data base. 
3. Voer de volgende query uit:

    > [!IMPORTANT]
    >
    > Controleer of u de UUID-Details van de gekloonde configuratie server of de verouderde vermelding van de configuratie server die niet meer wordt gebruikt voor het beveiligen van virtuele machines. Als u een onjuiste UUID invoert, gaan de gegevens voor alle bestaande beveiligde items verloren.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Vernieuw de portal pagina.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Er treedt een oneindige lus op bij het invoeren van uw referenties

Nadat u de juiste gebruikers naam en het wacht woord hebt ingevoerd op de configuratie server OVF, wordt u gevraagd om de juiste referenties op te geven.

Dit probleem kan optreden wanneer de systeem tijd onjuist is.

Het probleem oplossen:

Stel de juiste tijd op de computer in en voer de aanmelding opnieuw uit. 
