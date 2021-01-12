---
title: Problemen met push-installatie van de Mobility-service met Azure Site Recovery oplossen
description: Problemen met de installatie van Mobility Services oplossen bij het inschakelen van replicatie voor herstel na nood gevallen met Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 62c8240a4d2e50aa3b584f322baf7d2ee217c6d3
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127869"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Problemen met push-installatie van Mobility service oplossen

De installatie van de Mobility-service is een belang rijke stap voor het inschakelen van replicatie. Het slagen van deze stap is afhankelijk van de vereisten van de vergadering en het werken met ondersteunde configuraties. De meest voorkomende fouten die u tijdens de installatie van de Mobility-service kunt voor komen, zijn als volgt:

* [Referentie-en bevoegdheids fouten](#credentials-check-errorid-95107--95108)
* [Mislukte aanmeldingen](#login-failures-errorid-95519-95520-95521-95522)
* [Connectiviteitsfouten](#connectivity-failure-errorid-95117--97118)
* [Fouten bij bestands-en printer deling](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Windows Management Instrumentation (WMI)-fouten](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Niet-ondersteunde besturingssystemen](#unsupported-operating-systems)
* [Niet-ondersteunde opstart configuraties](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Installatie fouten van de Volume Shadow Copy-service (VSS)](#vss-installation-failures)
* [De apparaatnaam in de GRUB-configuratie in plaats van de UUID van het apparaat](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Volume voor Logical Volume Manager (LVM)](#lvm-support-from-920-version)
* [Waarschuwingen opnieuw opstarten](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Wanneer u replicatie inschakelt, probeert Azure Site Recovery de Mobility Service-agent op uw virtuele machine (VM) te installeren. Als onderdeel van dit proces probeert de configuratie server verbinding te maken met de virtuele machine en de agent te kopiëren. Als u de installatie wilt inschakelen, volgt u de stapsgewijze richt lijnen voor het oplossen van problemen.

## <a name="credentials-check-errorid-95107--95108"></a>Controle van referenties (ErrorID: 95107 & 95108)

Controleer of het gebruikers account dat u tijdens replicatie inschakelen hebt gekozen, geldig en nauw keurig is. Azure Site Recovery moet het **hoofd** account of gebruikers account met **beheerders bevoegdheden** hebben om een push-installatie uit te voeren. Anders wordt de push-installatie geblokkeerd op de bron machine.

Voor Windows (**fout 95107**) controleert u of het gebruikers account beheerders toegang heeft op de bron computer, met een lokaal account of een domein account. Als u geen domein account gebruikt, moet u toegangs beheer voor externe gebruikers uitschakelen op de lokale computer.

* Hand matig een register sleutel toevoegen die toegangs beheer voor externe gebruikers uitschakelt:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Nieuwe toevoegen `DWORD` : `LocalAccountTokenFilterPolicy`
  * Stel de waarde in op `1`

* Voer de volgende opdracht uit vanaf een opdracht prompt om de register sleutel toe te voegen:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Voor Linux (**fout 95108**), moet u het **hoofd** account kiezen voor een geslaagde installatie van de Mobility Service-agent. Daarnaast moeten SSH-File Transfer Protocol (SFTP)-services worden uitgevoerd. Het SFTP-subsysteem en de wachtwoord verificatie inschakelen in het _sshd_config_ bestand:

1. Meld u aan als **root**.
1. Ga naar _/etc/ssh/sshd_config-bestand_, zoek de regel die begint met `PasswordAuthentication` .
1. Verwijder de opmerking bij de regel en wijzig de waarde in `yes` .
1. Zoek de regel die begint met `Subsystem` en verwijder de opmerking over de regel.
1. Start de `sshd` service opnieuw.

Als u de referenties van het gekozen gebruikers account wilt wijzigen, volgt u [deze instructies](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Onvoldoende privileges-fouten (ErrorID: 95517)

Als de gebruiker die de Mobility agent heeft gekozen, geen beheerders bevoegdheden heeft, mag de configuratie server/scale-out-proces server de software van de Mobility-agent niet kopiëren naar de bron machine. Deze fout is het gevolg van een fout bij het geweigerd van de toegang. Zorg ervoor dat het gebruikers account beheerders rechten heeft.

Als u de referenties van het gekozen gebruikers account wilt wijzigen, volgt u [deze instructies](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Onvoldoende privileges-fouten (ErrorID: 95518)

Wanneer de instelling voor het vertrouwen van een domein vertrouwensrelatie tussen het primaire domein en werk station mislukt tijdens het aanmelden bij de bron machine, mislukt de installatie van de Mobility-agent met fout-ID 95518. Zorg ervoor dat het gebruikers account dat wordt gebruikt voor het installeren van de Mobility-agent, beheerders bevoegdheden heeft om zich aan te melden via het primaire domein van de bron machine.

Als u de referenties van het gekozen gebruikers account wilt wijzigen, volgt u de [volgende instructies](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Mislukte aanmeldingen (ErrorID: 95519, 95520, 95521, 95522)

In deze sectie worden de referentie-en aanmeldings fout berichten beschreven.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>De referenties van het gebruikers account zijn uitgeschakeld (ErrorID: 95519)

Het gebruikers account dat u hebt gekozen tijdens het inschakelen van replicatie, is uitgeschakeld. Als u het gebruikers account wilt inschakelen, raadpleegt u [dit artikel](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) of voert u de volgende opdracht uit door de naam van de gebruiker te vervangen door de _werkelijke naam van_ de gebruikers.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>Referenties zijn vergrendeld vanwege meerdere mislukte aanmeldings pogingen (ErrorID: 95520)

Meerdere mislukte pogingen om toegang te krijgen tot een computer, vergren delen het gebruikers account. De fout kan worden veroorzaakt door:

* De referenties die zijn verschaft tijdens de configuratie-instellingen zijn onjuist.
* Het gebruikers account dat tijdens het inschakelen van de replicatie is gekozen, is onjuist.

Wijzig de gekozen referenties door [deze instructies](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) te volgen en voer de bewerking opnieuw uit.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>Er zijn geen aanmeldings servers beschikbaar op de bron machine (ErrorID: 95521)

Deze fout treedt op wanneer de aanmeldings servers niet beschikbaar zijn op de bron machine. Als er geen aanmeldings servers beschikbaar zijn, mislukken aanmeldings aanvragen en kan de Mobility-agent niet worden geïnstalleerd. Zorg ervoor dat aanmeldings servers beschikbaar zijn op de bron machine en start de Netlogon-service voor een geslaagde aanmelding. Zie [Windows-aanmeldings scenario's](/windows-server/security/windows-authentication/windows-logon-scenarios)voor meer informatie.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>De aanmeldings service wordt niet uitgevoerd op de bron machine (ErrorID: 95522)

De aanmeldings service wordt niet uitgevoerd op de bron machine en heeft de aanmeldings aanvraag mislukt. De Mobility-agent kan niet worden geïnstalleerd. Gebruik een van de volgende methoden om de `Netlogon` service op de bron computer te starten om de fout op te lossen:

* `Netlogon`Voer de opdracht uit om de service te starten vanaf een opdracht prompt `net start Netlogon` .
* Start de service vanuit taak beheer `Netlogon` .

## <a name="connectivity-failure-errorid-95117--97118"></a>Connectiviteits fout (ErrorID: 95117 & 97118)

De configuratie server/scale-out proces server probeert verbinding te maken met de bron-VM om de Mobility-agent te installeren. Deze fout treedt op wanneer de bron machine niet bereikbaar is, omdat er problemen zijn met de netwerk verbinding.

Om de fout op te lossen:

* Controleer of het gebruikers account beheerders rechten heeft op de bron computer, met een lokaal account of een domein account. Als u geen domein account gebruikt, moet u toegangs beheer voor externe gebruikers uitschakelen op de lokale computer.
  * Hand matig een register sleutel toevoegen die toegangs beheer voor externe gebruikers uitschakelt:
    * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
    * Nieuwe toevoegen `DWORD` : `LocalAccountTokenFilterPolicy`
    * Stel de waarde in op `1`
  * Voer de volgende opdracht uit vanaf een opdracht prompt om de register sleutel toe te voegen:

    `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

* Zorg ervoor dat u de bron computer kunt pingen vanaf de configuratie server. Als u tijdens het inschakelen van de replicatie de scale-out proces server hebt gekozen, moet u ervoor zorgen dat u de bron computer kunt pingen vanaf de proces server.

* Zorg ervoor dat de service Bestands-en printer deling is ingeschakeld op de virtuele machine. Raadpleeg de stappen die [hier](vmware-azure-troubleshoot-push-install.md#file-and-printer-sharing-services-check-errorid-95105--95106)worden beschreven.

* Zorg ervoor dat de WMI-service is ingeschakeld op de virtuele machine. Raadpleeg de stappen die [hier](vmware-azure-troubleshoot-push-install.md#windows-management-instrumentation-wmi-configuration-check-error-code-95103)worden beschreven.

* Zorg ervoor dat de gedeelde netwerk mappen op uw virtuele machine toegankelijk zijn vanaf de proces server. Raadpleeg de stappen die [hier](vmware-azure-troubleshoot-push-install.md#check-access-for-network-shared-folders-on-source-machine-errorid-9510595523)worden beschreven.

* Gebruik de opdracht regel van de bron server `Telnet` om de configuratie server of scale-out proces server op HTTPS-poort 135 te pingen, zoals wordt weer gegeven in de volgende opdracht. Met deze opdracht wordt gecontroleerd of er problemen zijn met de netwerk verbinding of het blok keren van de firewall poort.

  `telnet <CS/ scale-out PS IP address> <135>`

* Daarnaast geldt voor een virtuele Linux-machine:
  * Controleer of de meest recente OpenSSH, OpenSSH-server en OpenSSL-pakketten zijn geïnstalleerd.
  * Controleer en of Secure Shell (SSH) is ingeschakeld en wordt uitgevoerd op poort 22.
  * SFTP-services moeten worden uitgevoerd. SFTP-subsysteem en-wachtwoord verificatie inschakelen in het _sshd_config_ -bestand:

    1. Meld u aan als **root**.
    1. Ga naar _/etc/ssh/sshd_config_ -bestand, zoek de regel die begint met `PasswordAuthentication` .
    1. Verwijder de opmerking bij de regel en wijzig de waarde in `yes` .
    1. Zoek de regel die begint met en `Subsystem` Verwijder de opmerking bij de regel
    1. Start de `sshd` service opnieuw.

* Een poging om verbinding te maken is mislukt als er na een bepaalde tijd geen geldige antwoorden zijn of als een tot stand gebrachte verbinding is mislukt omdat een verbonden host niet kan reageren.
* Dit kan een probleem zijn met de verbinding/het netwerk/het domein. Dit kan ook worden veroorzaakt door het oplossen van problemen met de DNS-naam of het probleem met de TCP-poort. Controleer of er bekende problemen in uw domein zijn.

## <a name="connectivity-failure-errorid-95523"></a>Connectiviteits fout (ErrorID: 95523)

Deze fout treedt op wanneer het netwerk dat de bron machine niet kan worden gevonden, is verwijderd of niet langer beschikbaar is. De enige manier om de fout op te lossen is om er zeker van te zijn dat het netwerk bestaat.

## <a name="check-access-for-network-shared-folders-on-source-machine-errorid-9510595523"></a>Controleer de toegang tot gedeelde netwerk mappen op de bron machine (ErrorID: 95105, 95523)

Controleer of de gedeelde netwerk mappen op uw virtuele machine toegankelijk zijn vanaf de proces server (PS) op afstand met de opgegeven referenties. Om toegang te bevestigen: 

1. Meld u aan bij de computer met de proces server.
2. Open Verkenner. Typ in de adres balk `\\<SOURCE-MACHINE-IP>\C$` en klik op ENTER.

    ![Map openen in PS](./media/vmware-azure-troubleshoot-push-install/open-folder-process-server.PNG)

3. Bestanden Verkenner vraagt om referenties. Voer de gebruikers naam en het wacht woord in en klik op OK. <br><br/>

    ![Referenties opgeven](./media/vmware-azure-troubleshoot-push-install/provide-credentials.PNG)

    >[!NOTE]
    > Als de bron machine lid is van een domein, geeft u de domein naam en de gebruikers naam op als `<domainName>\<username>` . Als de bron machine zich in de werk groep bevindt, geeft u alleen de gebruikers naam op.

4. Als de verbinding tot stand is gebracht, worden de mappen van de bron machine op afstand zichtbaar van de proces server.

    ![Zicht bare mappen van de bron machine](./media/vmware-azure-troubleshoot-push-install/visible-folders-from-source.png)

Als de verbinding is mislukt, controleert u of aan alle vereisten wordt voldaan.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Controle van bestands-en printer deling-Services (ErrorID: 95105 & 95106)

Controleer na een connectiviteits controle of de service Bestands-en printer deling is ingeschakeld op de virtuele machine. Deze instellingen zijn vereist voor het kopiëren van de Mobility-agent naar de bron machine.

Voor **Windows 2008 R2 en eerdere versies**:

* Om bestands-en printer deling via Windows Firewall in te scha kelen,
  1. Open **configuratie scherm**  >  **systeem-en beveiligings**  >  **Windows Firewall**. **Selecteer in** het linkerdeel venster de optie  >  **regels voor inkomend verkeer** in de console structuur.
  1. Regels bestand en printer deling (NB-session-in) en bestands-en printer deling (SMB-in) zoeken.
  1. Voor elke regel klikt u met de rechter muisknop op de regel en klikt u vervolgens op **regel inschakelen**.

* Het delen van bestanden met groepsbeleid inschakelen:
  1. Ga naar **Start**, typ `gpmc.msc` en zoek.
  1. Open in het navigatie deel venster de volgende mappen: gebruikers configuratie van het **lokale computer beleid**  >    >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **netwerk delen**.
  1. Dubbel klik in het detail venster op **voor komen dat gebruikers bestanden binnen hun profiel delen**.

     Selecteer **uitgeschakeld** om de instelling Groepsbeleid uit te scha kelen en de gebruiker in staat te stellen bestanden te delen.

  1. Selecteer **OK** om uw wijzigingen op te slaan.

  Zie voor meer informatie over het [delen van bestanden in-of uitschakelen met Groepsbeleid](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Als u bestands-en printer deling voor latere versies van Windows of Linux wilt inschakelen, volgt u de instructies in [de Mobility-service installeren voor nood herstel van virtuele VMware-machines en fysieke servers](vmware-azure-install-mobility-service.md) .

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Controle van configuratie van Windows Management Instrumentation (WMI) (fout code: 95103)

Nadat u de bestands-en printer services hebt gecontroleerd, schakelt u de WMI-service voor privé-, open bare en domein profielen in via de firewall. Deze instellingen zijn vereist voor het volt ooien van externe uitvoering op de bron machine.

WMI inschakelen:

1. Ga naar beveiliging van **het configuratie scherm**  >   en selecteer **Windows Firewall**.
1. Selecteer **instellingen wijzigen** en selecteer vervolgens het tabblad **uitzonde ringen** .
1. Schakel in het venster **uitzonde ringen** het selectie vakje voor Windows Management INSTRUMENTATION (WMI) in om WMI-verkeer via de firewall in te scha kelen.

U kunt ook WMI-verkeer via de firewall inschakelen vanaf de opdracht prompt met de volgende opdracht:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Andere artikelen over het oplossen van problemen met WMI vindt u in de volgende artikelen.

* [Eenvoudige WMI-tests](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)
* [WMI-problemen oplossen](/windows/win32/wmisdk/wmi-troubleshooting)
* [Problemen met WMI-scripts en WMI-services oplossen](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Niet-ondersteunde besturingssystemen

Een andere veelvoorkomende reden voor de fout is mogelijk veroorzaakt door een niet-ondersteund besturings systeem. Gebruik een ondersteunde versie van het besturings systeem en de kernel voor een geslaagde installatie van de Mobility-service. Vermijd het gebruik van persoonlijke patches.

Als u de lijst met besturings systemen en kernel-versies wilt weer geven die worden ondersteund door Azure Site Recovery, raadpleegt u het document met de [ondersteunings matrix](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Niet-ondersteunde configuraties voor opstart schijven (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>Opstart-en systeem partities/-volumes zijn niet dezelfde schijf (ErrorID: 95309)

Voordat de 9,20-versie, opstart-en systeem partities/-volumes op verschillende schijven worden niet-ondersteunde configuratie. Met ingang van de [9,20-versie](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)wordt deze configuratie ondersteund.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>De opstart schijf is niet beschikbaar (ErrorID: 95310)

Een virtuele machine zonder een opstart schijf kan niet worden beveiligd. Een opstart schijf zorgt voor een probleemloze herstel bewerking van een virtuele machine tijdens een failoverbewerking. Afwezigheid van een opstart schijf resulteert in een fout bij het opstarten van de computer na een failover. Zorg ervoor dat de virtuele machine een opstart schijf bevat en voer de bewerking opnieuw uit. Meerdere opstart schijven op dezelfde computer worden ook niet ondersteund.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Meerdere opstart schijven aanwezig op de bron machine (ErrorID: 95311)

Een virtuele machine met meerdere opstart schijven is geen [ondersteunde configuratie](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systeem partitie op meerdere schijven (ErrorID: 95313)

Vóór de 9,20-versie werd een basis partitie of het instellen van een volume op meerdere schijven een niet-ondersteunde configuratie. Met ingang van de [9,20-versie](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)wordt deze configuratie ondersteund.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Het inschakelen van de beveiliging is mislukt als de apparaatnaam die wordt vermeld in de GRUB-configuratie in plaats van de UUID (ErrorID: 95320)

### <a name="possible-cause"></a>Mogelijke oorzaak

De Grand Unified Bootloader (GRUB) configuratie bestanden (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_ of _/etc/default/grub_) kunnen de waarde voor de **hoofdmap** van para meters bevatten en **hervatten** als de werkelijke apparaatnamen in plaats van een Universally Unique Identifier (UUID). Site Recovery moet de UUID-benadering hebben als de apparaatnamen kunnen veranderen tijdens het opnieuw opstarten van de virtuele machine. Het is bijvoorbeeld mogelijk dat de virtuele machine niet online is met dezelfde naam als de failover en dat er problemen zijn.

Bijvoorbeeld:

- De volgende regel is afkomstig uit het GRUB-bestand _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- De volgende regel is afkomstig uit het GRUB-bestand _/boot/grub/menu.lst_:

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> De GRUB-regels bevatten werkelijke apparaatnamen voor de para meters **root** en **Resume** in plaats van de uuid.

### <a name="how-to-fix"></a>Oplossen

De apparaatnamen moeten worden vervangen door de bijbehorende UUID.

1. Zoek de UUID van het apparaat door de opdracht uit te voeren `blkid \<device name>` .

   Bijvoorbeeld:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Vervang nu de naam van het apparaat door de bijbehorende UUID in de indeling zoals `root=UUID=\<UUID>` . Als we bijvoorbeeld de apparaatnaam vervangen door UUID voor de para meter root en resume die wordt vermeld in de bestanden _/boot/grub2/grub.cfg_, _/boot/grub2/grub.cfg_ of _/etc/default/grub_ , zien de regels in de bestanden eruit als in de volgende regel:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Start de beveiliging opnieuw.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>De installatie van de Mobility-service is voltooid met een waarschuwing voor opnieuw opstarten (ErrorID: 95265 & 95266)

Site Recovery Mobility-service heeft veel onderdelen, een van de het filter stuur programma genoemd. Het filter stuur programma wordt alleen in het systeem geheugen geladen tijdens het opnieuw opstarten van het systeem. Oplossingen voor filter Stuur Programma's kunnen alleen worden gerealiseerd wanneer een nieuw filter stuur programma wordt geladen op het moment dat het systeem opnieuw wordt opgestart.

> [!IMPORTANT]
> Dit is een waarschuwing dat de bestaande replicatie kan worden uitgevoerd, zelfs na de nieuwe update van de agent. U kunt ervoor kiezen om op elk gewenst moment opnieuw op te starten om de voor delen van het nieuwe filter stuur programma te krijgen, maar als u niet opnieuw opstart, blijft het oude filter stuur programma actief. Na een update zonder opnieuw opstarten, met uitzonde ring van het filter stuur programma, **profiteren de voor delen van andere verbeteringen en oplossingen in Mobility service gerealiseerd**. Hoewel het wordt aanbevolen, is het niet verplicht om opnieuw op te starten na elke upgrade. Voor informatie over wanneer het opnieuw opstarten verplicht is, stelt u de sectie [opnieuw opstarten na de upgrade](service-updates-how-to.md#reboot-after-mobility-service-upgrade) van de Mobility-service in bij service-updates in azure site Recovery.

> [!TIP]
>Voor aanbevolen procedures voor het plannen van upgrades tijdens het onderhouds venster raadpleegt u de [ondersteuning voor het meest recente besturings systeem/kernel](service-updates-how-to.md#support-for-latest-operating-systemskernels) in service-updates in azure site Recovery.

## <a name="lvm-support-from-920-version"></a>LVM-ondersteuning van 9,20-versie

Voor de 9,20-versie werd Logical Volume Manager (LVM) alleen ondersteund voor gegevens schijven. De `/boot` partitie moet zich op een schijf partitie benemen en niet op een LVM-volume.

Vanaf de [9,20-versie](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)wordt de [besturingssysteem schijf op LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) ondersteund.

## <a name="insufficient-space-errorid-95524"></a>Onvoldoende ruimte (ErrorID: 95524)

Wanneer de Mobility-agent naar de bron machine wordt gekopieerd, is ten minste 100 MB beschik bare ruimte vereist. Zorg ervoor dat op de bron machine de vereiste hoeveelheid beschik bare ruimte is en voer de bewerking opnieuw uit.

## <a name="low-system-resources"></a>Weinig systeem bronnen

De mogelijke fout-Id's voor dit probleem zijn 95572 en 95573. Dit probleem treedt op wanneer het systeem weinig beschikbaar geheugen heeft en kan geen geheugen toewijzen voor de installatie van de Mobility-service. Zorg ervoor dat er voldoende geheugen beschikbaar is voor de installatie om door te gaan en goed te volt ooien.

## <a name="vss-installation-failures"></a>VSS-installatie fouten

De installatie van de Volume Shadow Copy-service (VSS) is onderdeel van de installatie van de Mobility-agent. Deze service wordt in het proces gebruikt voor het genereren van toepassings consistente herstel punten. Fouten tijdens de installatie van VSS kunnen om verschillende redenen optreden. Raadpleeg _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_ om de exacte fouten te identificeren. Enkele veelvoorkomende fouten en de oplossings stappen worden in de volgende sectie gemarkeerd.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS-fout-2147023170 [0x800706BE]-afsluit code 511

Dit probleem wordt doorgaans weer gegeven wanneer antivirus software de bewerkingen van Azure Site Recovery Services blokkeert.

Ga als volgt te werk om het probleem op te lossen:

1. Bekijk de lijst met [uitgesloten mappen van het antivirus programma](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
1. Volg de richt lijnen die zijn gepubliceerd door uw antivirus provider om de registratie van de DLL in Windows te blok keren.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS-fout 7 [0x7]-afsluit code 511

Deze fout is een runtime-fout die wordt veroorzaakt omdat er onvoldoende geheugen is om VSS te installeren. Verg root de schijf ruimte voor het volt ooien van deze bewerking.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS-fout-2147023824 [0x80070430]-afsluit code 517

Deze fout treedt op wanneer Azure Site Recovery VSS-Provider service is [gemarkeerd voor verwijdering](/previous-versions/ms838153(v=msdn.10)). Probeer VSS hand matig te installeren op de bron machine door de volgende opdracht uit te voeren:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS-fout-2147023841 [0x8007041F]-afsluit code 512

Deze fout treedt op wanneer Azure Site Recovery VSS Provider service-data base is [vergrendeld](/previous-versions/ms833798(v=msdn.10)). Probeer VSS hand matig te installeren op de bron machine door de volgende opdracht uit te voeren vanaf een opdracht prompt:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Als er een fout optreedt, controleert u of een antivirus programma of andere services zijn vastgelopen in een **begin** status. Een proces in een **begin** status kan de vergren deling van database services behouden. Dit leidt tot fouten bij het installeren van de VSS-provider. Zorg ervoor dat er geen service in het **begin** stadium is en probeer het opnieuw.

### <a name="vss-exit-code-806"></a>VSS-afsluit code 806

Deze fout treedt op wanneer het gebruikers account dat wordt gebruikt voor de installatie, niet over de machtigingen beschikt om de opdracht uit te voeren `CSScript` . Geef de benodigde machtigingen voor het gebruikers account op om het script uit te voeren en voer de bewerking opnieuw uit.

### <a name="other-vss-errors"></a>Andere VSS-fouten

Probeer de VSS-Provider service hand matig te installeren op de bron machine door de volgende opdracht uit te voeren vanaf een opdracht prompt:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>VSS-fout-0x8004E00F

Deze fout treedt doorgaans op tijdens de installatie van de Mobility-agent vanwege problemen in `DCOM` en `DCOM` is een kritieke status.

Gebruik de volgende procedure om de oorzaak van de fout te bepalen.

### <a name="examine-the-installation-logs"></a>De installatie logboeken controleren

1. Open het installatie logboek op _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_.
2. De aanwezigheid van de volgende fout wijst op dit probleem:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

Het probleem oplossen:

Neem contact op met het [micro soft Windows-platform](https://aka.ms/Windows_Support) om hulp te krijgen bij het oplossen van het probleem met DCOM.

Wanneer het DCOM-probleem is opgelost, installeert u de Azure Site Recovery VSS-provider hand matig met behulp van de volgende opdracht vanaf een opdracht prompt:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Als toepassings consistentie niet essentieel is voor uw vereisten voor nood herstel, kunt u de installatie van de VSS-provider overs Laan.

Als u de installatie van de Azure Site Recovery VSS-provider wilt overs Laan en hand matig wilt installeren Azure Site Recovery VSS-provider na de installatie:

1. De Mobility-service installeren. De installatie mislukt bij stap: **configuratie na installatie**.
1. De VSS-installatie overs Laan:
   1. Open de installatie directory van de Azure Site Recovery Mobility-service op de locatie:

      _C:\Program Files (x86) \Microsoft Azure site Recovery\agent._

   1. Wijzig de installatie scripts van de Azure Site Recovery VSS-provider _InMageVSSProvider_Install_ en _InMageVSSProvider_Uninstall. cmd_ om altijd slagen door de volgende regels toe te voegen:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Voer een hand matige installatie van de Mobility-agent uit.
1. Wanneer de installatie is voltooid en naar de volgende **stap wordt verplaatst**, moet u de regels die u hebt toegevoegd, verwijderen.
1. Als u de VSS-provider wilt installeren, opent u een opdracht prompt als beheerder en voert u de volgende opdracht uit:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Controleer of de Azure Site Recovery VSS-provider is geïnstalleerd als een service in Windows Services. Open de MMC van de component service om te controleren of de VSS-provider wordt vermeld.
1. Als de installatie van de VSS-provider blijft mislukken, werkt u met technische ondersteuning om de machtigingen fouten in de cryptografische Application Programming Interface (diagnostische CAPI2) op te lossen.

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>De installatie van de VSS-provider is mislukt omdat de Cluster service is ingeschakeld op de niet-cluster machine

Dit probleem leidt ertoe dat de Azure Site Recovery Mobility agent-installatie mislukt tijdens de installatie van de Azure Site Recovery VSS-provider. De fout is omdat er een probleem is met `COM+` de installatie van de VSS-provider.

### <a name="to-identify-the-issue"></a>Het probleem identificeren

In het logboek op de configuratie server op _C:\ProgramData\ASRSetupLogs\UploadedLogs \<date-time> UA_InstallLogFile. log_ ziet u de volgende uitzonde ring:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

Het probleem oplossen:

1. Controleer of deze computer een niet-cluster machine is en of de cluster onderdelen niet worden gebruikt.
1. Als de onderdelen niet worden gebruikt, verwijdert u de cluster onderdelen van de machine.

## <a name="drivers-are-missing-on-the-source-server"></a>Er ontbreken Stuur Programma's op de bron server

Als de installatie van de Mobility-agent mislukt, raadpleegt u de Logboeken onder _C:\ProgramData\ASRSetupLogs_ om te bepalen of sommige van de vereiste Stuur Programma's in sommige besturings sets ontbreken.

Het probleem oplossen:

1. Open het REGI ster met behulp van een REGI ster `regedit.msc` -Editor, zoals.
1. Open het `HKEY_LOCAL_MACHINE\SYSTEM` knoop punt.
1. `SYSTEM`Zoek de besturings sets in het knoop punt.
1. Open elke set besturings elementen en controleer of de volgende Windows-Stuur Programma's aanwezig zijn:

   * ATAPI
   * Vmbus
   * Storflt
   * Storvsc
   * Intelide

1. Installeer de ontbrekende Stuur Programma's opnieuw.

## <a name="next-steps"></a>Volgende stappen

Meer [informatie](vmware-azure-tutorial.md) over het instellen van herstel na nood geval voor virtuele VMware-machines.
