---
title: Problemen oplossen met Windows Virtual Desktop (klassieke) sessiehost-Azure
description: Problemen oplossen bij het configureren van virtuele machines van het virtuele bureau blad van Windows (klassieke host).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: af0da66766a4b814dde8dfa45a0abe00f9e93efa
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444170"
---
# <a name="windows-virtual-desktop-classic-session-host-virtual-machine-configuration"></a>Virtuele-machine configuratie van Virtual Desktop (klassiek) van Windows-sessiehost

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../troubleshoot-vm-configuration.md) als u Azure Resource Manager Windows Virtual Desktop-objecten probeert te beheren.

Gebruik dit artikel voor het oplossen van problemen die zich voordoen bij het configureren van de virtuele machines (Vm's) voor virtuele bureau blad-sessies van Windows.

## <a name="provide-feedback"></a>Feedback geven

Ga naar de [technische community van Windows virtueel bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) voor het bespreken van de Windows Virtual Desktop-service met het product team en de actieve leden van de community.

## <a name="vms-are-not-joined-to-the-domain"></a>Vm's zijn niet toegevoegd aan het domein

Volg deze instructies als u problemen ondervindt bij het samen voegen van Vm's aan het domein.

- Voeg de VM hand matig toe met behulp van het proces in [een virtuele Windows Server-machine toevoegen aan een beheerd domein](../../active-directory-domain-services/join-windows-vm.md) of het gebruik van de [sjabloon voor domein deelname](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Probeer de domein naam te pingen vanaf de opdracht regel op de virtuele machine.
- Bekijk de lijst met fout berichten voor domein deelname in het [oplossen van fout berichten voor domein deelname](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Fout: onjuiste referenties

**Oorzaak:** Er is een type fout opgetreden bij het invoeren van de referenties in de Azure Resource Manager sjabloon-interface oplossingen.

**Oplossen:** Voer een van de volgende acties uit om het probleem op te lossen.

- Voeg de Vm's hand matig toe aan een domein.
- De sjabloon opnieuw implementeren wanneer de referenties zijn bevestigd. Zie [een hostgroep maken met Power shell](create-host-pools-powershell-2019.md).
- Virtuele machines toevoegen aan een domein met behulp van een sjabloon met een [bestaande Windows-VM koppelen aan een AD-domein](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Fout: time-out bij het wachten op invoer van de gebruiker

**Oorzaak:** Het account dat wordt gebruikt voor het volt ooien van het lidmaatschap van een domein, heeft mogelijk multi-factor Authentication (MFA).

**Oplossen:** Voer een van de volgende acties uit om het probleem op te lossen.

- MFA voor het account tijdelijk verwijderen.
- Gebruik een service account.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Fout: het account dat wordt gebruikt tijdens het inrichten, heeft geen machtigingen om de bewerking te volt ooien

**Oorzaak:** Het account dat wordt gebruikt, heeft geen machtigingen om Vm's toe te voegen aan het domein vanwege naleving en voor Schriften.

**Oplossen:** Voer een van de volgende acties uit om het probleem op te lossen.

- Gebruik een account dat lid is van de groep Administrators.
- Verleen de benodigde machtigingen voor het account dat wordt gebruikt.

### <a name="error-domain-name-doesnt-resolve"></a>Fout: de domein naam kan niet worden omgezet

**Oorzaak 1:** Vm's bevinden zich in een virtueel netwerk dat niet is gekoppeld aan het virtuele netwerk (VNET) waarin het domein zich bevindt.

**Oplossing 1:** Maak VNET-peering tussen het VNET waar Vm's zijn ingericht en het VNET waar de domein controller (DC) wordt uitgevoerd. Zie [een peering voor een virtueel netwerk maken-Resource Manager, verschillende abonnementen](../../virtual-network/create-peering-different-subscriptions.md).

**Oorzaak 2:** Wanneer u Azure Active Directory Domain Services (Azure AD DS) gebruikt, zijn de DNS-server instellingen van het virtuele netwerk niet bijgewerkt zodat ze verwijzen naar de beheerde domein controllers.

**Oplossing 2:** Zie [DNS-instellingen bijwerken voor het virtuele Azure-netwerk](../../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)om de DNS-instellingen voor het virtuele netwerk met Azure AD DS bij te werken.

**Oorzaak 3:** De DNS-server instellingen van de netwerk interface verwijzen niet naar de juiste DNS-server in het virtuele netwerk.

**Oplossing 3:** Voer een van de volgende acties uit om het probleem op te lossen, volgens de stappen in [Change DNS servers].
- Wijzig de DNS-server instellingen van de netwerk interface in **aangepast** met de stappen van [DNS-servers wijzigen](../../virtual-network/virtual-network-network-interface.md#change-dns-servers) en geef de privé-IP-adressen op van de DNS-servers in het virtuele netwerk.
- Wijzig de instellingen van de DNS-server van de netwerk interface in **overnemen van het virtuele netwerk** met de stappen van [DNS-servers wijzigen](../../virtual-network/virtual-network-network-interface.md#change-dns-servers)en wijzig vervolgens de DNS-server instellingen van het virtuele netwerk met de stappen van [DNS-servers wijzigen](../../virtual-network/manage-virtual-network.md#change-dns-servers).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>De Windows Virtual Desktop agent en het Windows-opstart laad programma voor virtueel bureau blad zijn niet geïnstalleerd

De aanbevolen manier om Vm's in te richten, is met behulp van de Azure Resource Manager sjabloon voor het **maken en inrichten van Windows virtueel-bureaublad groep** . De sjabloon installeert automatisch de Windows Virtual Desktop agent en de opstart lader van de Windows Virtual Desktop agent.

Volg deze instructies om te bevestigen dat de onderdelen zijn geïnstalleerd en om te controleren op fout berichten.

1. Controleer of de twee onderdelen zijn geïnstalleerd door Program ma's   >    >  **en onderdelen** van het configuratie scherm te controleren. Als **Windows Virtual Desktop agent** en de **opstart lader van de Windows Virtual Desktop agent** niet zichtbaar zijn, zijn ze niet geïnstalleerd op de virtuele machine.
2. Open **bestanden Verkenner** en ga naar **C:\Windows\Temp\ScriptLog.log**. Als het bestand ontbreekt, geeft dit aan dat de Power shell DSC waarmee de twee onderdelen zijn geïnstalleerd, niet kan worden uitgevoerd in de beschik bare beveiligings context.
3. Als het bestand **C:\Windows\Temp\ScriptLog.log** aanwezig is, opent u het en controleert u op fout berichten.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Fout: Windows Virtual Desktop agent en de opstart lader van de Windows Virtual Desktop agent ontbreken. C:\Windows\Temp\ScriptLog.log ontbreekt ook

**Oorzaak 1:** De referenties die zijn opgegeven tijdens de invoer voor de Azure Resource Manager sjabloon zijn onjuist of de machtigingen zijn ontoereikend.

**Oplossing 1:** Voeg de ontbrekende onderdelen hand matig toe aan de virtuele machines met behulp [van een hostgroep maken met Power shell](create-host-pools-powershell-2019.md).

**Oorzaak 2:** Power shell DSC kan worden gestart en uitgevoerd, maar is niet voltooid omdat het niet kan worden aangemeld bij het virtuele Windows-bureau blad en de benodigde informatie kan verkrijgen.

**Oplossing 2:** Bevestig de items in de volgende lijst.

- Zorg ervoor dat het account geen MFA heeft.
- Controleer of de naam van de Tenant juist is en of de Tenant bestaat in het virtuele bureau blad van Windows.
- Bevestig dat het account ten minste RDS-Inzender machtigingen heeft.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Fout: de verificatie is mislukt, fout in C:\Windows\Temp\ScriptLog.log

**Oorzaak:** Power shell DSC kan worden uitgevoerd, maar kan geen verbinding maken met het virtuele bureau blad van Windows.

**Oplossen:** Bevestig de items in de volgende lijst.

- Registreer de Vm's hand matig met de virtueel-bureaublad service van Windows.
- Het account dat wordt gebruikt om verbinding te maken met het virtuele bureau blad van Windows, heeft machtigingen voor de Tenant om hostgroepen op te stellen.
- Het bevestigen van het account heeft geen MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows Virtual Desktop agent registreert niet bij de virtuele bureau blad-service van Windows

Wanneer de virtuele Windows-bureau blad-agent voor het eerst is geïnstalleerd op de host van de sessiehost (hand matig of via de Azure Resource Manager sjabloon en Power shell DSC), biedt deze een registratie token. De volgende sectie bevat informatie over het oplossen van problemen die van toepassing zijn op de Windows Virtual Desktop agent en het token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Fout: de status die in Get-RdsSessionHost-cmdlet is opgeslagen, toont de status als niet beschikbaar

> [!div class="mx-imgBorder"]
> ![Met de cmdlet Get-RdsSessionHost wordt de status weer gegeven als niet beschikbaar.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Oorzaak:** De agent kan zichzelf niet bijwerken naar een nieuwe versie.

**Oplossen:** Volg deze instructies om de agent hand matig bij te werken.

1. Down load een nieuwe versie van de agent op de host-VM van de sessie.
2. Start taak beheer en stop de RDAgentBootLoader-service op het tabblad Service.
3. Voer het installatie programma uit voor de nieuwe versie van de virtuele bureau blad-agent van Windows.
4. Als u wordt gevraagd om het registratie token, verwijdert u de vermelding INVALID_TOKEN en klikt u op volgende (er is geen nieuw token vereist).
5. Voltooi de installatie wizard.
6. Open taak beheer en start de RDAgentBootLoader-service.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Fout: de register vermelding IsRegistered van de virtuele-bureaublad agent bevat een waarde van 0

**Oorzaak:** Het registratie token is verlopen of is gegenereerd met de verval waarde van 999999.

**Oplossen:** Volg deze instructies om de register fout van de agent op te lossen.

1. Als er al een registratie token bestaat, verwijdert u het met Remove-RDSRegistrationInfo.
2. Nieuw token genereren met RDS-NewRegistrationInfo.
3. Controleer of de para meter-ExpriationHours is ingesteld op 72 (Max-waarde is 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Fout: Windows Virtual Desktop agent rapporteert geen heartbeat bij het uitvoeren van Get-RdsSessionHost

**Oorzaak 1:** De RDAgentBootLoader-service is gestopt.

**Oplossing 1:** Open taak beheer en start de service als het tabblad Service een status gestopt voor de RDAgentBootLoader-service meldt.

**Oorzaak 2:** Poort 443 kan worden gesloten.

**Oplossing 2:** Volg deze instructies voor het openen van poort 443.

1. Bevestig dat poort 443 is geopend door het PSPing-hulp programma te downloaden van [sysinternal-hulpprogram ma's](/sysinternals/downloads/psping/).
2. Installeer PSPing op de host-VM waarop de agent wordt uitgevoerd.
3. Open de opdracht prompt als beheerder en geef de volgende opdracht op:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Bevestig dat PSPing de gegevens terug heeft ontvangen van de RDBroker:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Problemen met de Windows-stack aan de zijkant van het virtuele bureau blad oplossen

De Windows-stack voor virtueel bureau blad wordt automatisch geïnstalleerd met Windows Server 2019. Gebruik micro soft Installer (MSI) om de side-by-stack te installeren op micro soft Windows Server 2016 of Windows Server 2012 R2. Voor micro soft Windows 10 is de Windows-stack voor virtuele Bureau bladen naast elkaar ingeschakeld met **enablesxstackrs.ps1**.

Er zijn drie belang rijke manieren waarop de side-by-stack wordt geïnstalleerd of ingeschakeld op virtuele machines van de sessiehost:

- Met de Azure Resource Manager nieuwe sjabloon voor het **maken en inrichten van een Windows Virtual Desktop-hostgroep**
- Door in te voegen en in te scha kelen op de master installatie kopie
- Hand matig geïnstalleerd of ingeschakeld op elke VM (of met uitbrei dingen/Power shell)

Als u problemen ondervindt met de Windows-stack naast elkaar, typt u de opdracht **qwinsta** vanaf de opdracht prompt om te bevestigen dat de side-by-side-stack is geïnstalleerd of ingeschakeld.

In de uitvoer van **qwinsta** wordt **RDP-SxS** vermeld in de uitvoer als de side-by-side-stack is geïnstalleerd en ingeschakeld.

> [!div class="mx-imgBorder"]
> ![Side-by-side stack is geïnstalleerd of ingeschakeld met qwinsta die worden vermeld als RDP-SxS in de uitvoer.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Controleer de hieronder vermelde Register vermeldingen en controleer of de waarden overeenkomen. Als er register sleutels ontbreken of als de waarden niet overeenkomen, volgt u de instructies in [een hostgroep maken met Power shell](create-host-pools-powershell-2019.md) voor informatie over het opnieuw installeren van de side-by-side-stack.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Fout: O_REVERSE_CONNECT_STACK_FAILURE

> [!div class="mx-imgBorder"]
> ![Fout code O_REVERSE_CONNECT_STACK_FAILURE.](../media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Oorzaak:** De side-by-side-stack is niet geïnstalleerd op de host-VM van de sessie.

**Oplossen:** Volg deze instructies voor het installeren van de side-by-side-stack op de host-VM van de sessie.

1. Gebruik Remote Desktop Protocol (RDP) om rechtstreeks naar de host-VM te gaan als lokale beheerder.
2. Down load en importeer [de Windows Virtual Desktop Power shell-module](/powershell/windows-virtual-desktop/overview/) voor gebruik in uw Power shell-sessie als u dit nog niet hebt gedaan, voert u deze cmdlet uit om u aan te melden bij uw account:

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. De side-by-side stack installeren met behulp van [een hostgroep maken met Power shell](create-host-pools-powershell-2019.md).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Een Windows-stack met virtuele Bureau bladen die niet goed werkt herstellen

Er zijn bekende omstandigheden waardoor de side-by-side-stack defect kan raken:

- Niet de juiste volg orde van de stappen voor het inschakelen van de side-by-side-stack
- Automatisch bijwerken naar Windows 10 Enhanced veelzijdige schijf (EVD)
- De rol van de Extern bureaublad sessiehost (RDSH) ontbreekt
- Meerdere keren uitgevoerd enablesxsstackrc.ps1
- enablesxsstackrc.ps1 uitvoeren in een account dat geen lokale beheerders bevoegdheden heeft

De instructies in deze sectie kunnen u helpen bij het verwijderen van de Windows-stack met virtuele Bureau bladen. Nadat u de side-by-stack hebt verwijderd, gaat u naar ' de virtuele machine registreren bij de Windows Virtual Desktop-hostgroep ' in [een hostgroep met Power shell maken](create-host-pools-powershell-2019.md) om de side-by-side-stack opnieuw te installeren.

De virtuele machine die wordt gebruikt voor het uitvoeren van herstel, moet zich op hetzelfde subnet en domein bevinden als de virtuele machine met de stack-by-side-stapel.

Volg deze instructies voor het uitvoeren van herstel vanuit hetzelfde subnet en domein:

1. Maak verbinding met Standard Remote Desktop Protocol (RDP) naar de VM vanaf waar de oplossing wordt toegepast.
2. Down load PsExec van https://docs.microsoft.com/sysinternals/downloads/psexec .
3. Pak het gedownloade bestand uit.
4. Start de opdracht prompt als lokale beheerder.
5. Ga naar map waar PsExec is uitgepakt.
6. Gebruik de volgende opdracht vanaf de opdracht prompt:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!NOTE]
    >VMname is de computer naam van de virtuele machine met de niet-gelijktijdige stack.

7. Accepteer de gebruiksrecht overeenkomst voor PsExec door op akkoord te klikken.

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van de software licentie overeenkomst.](../media/SoftwareLicenseTerms.png)

    >[!NOTE]
    >Dit dialoog venster wordt alleen weer gegeven voor de eerste keer dat PsExec wordt uitgevoerd.

8. Wanneer de sessie met de opdracht prompt op de virtuele machine wordt geopend met de niet-gelijktijdige stack, voert u qwinsta uit en controleert u of er een vermelding met de naam RDP-SxS beschikbaar is. Als dat niet het geval is, is er geen side-by-side stack aanwezig op de VM, zodat het probleem niet is gekoppeld aan de side-by-side-stack.

    > [!div class="mx-imgBorder"]
    > ![Opdracht prompt van beheerder](../media/AdministratorCommandPrompt.png)

9. Voer de volgende opdracht uit, waarmee micro soft-onderdelen die op de virtuele machine zijn geïnstalleerd, worden weer geven met de stack-by-side-stapel.

    ```cmd
        wmic product get name
    ```

10. Voer de onderstaande opdracht uit met product namen uit de bovenstaande stap.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Verwijder alle producten die beginnen met "Extern bureaublad".

12. Nadat alle onderdelen van het virtuele bureau blad van Windows zijn verwijderd, volgt u de instructies voor uw besturings systeem:

13. Als uw besturings systeem Windows Server is, start u de VM die de defecte side-by-side stack had, opnieuw op (met Azure Portal of van het hulp programma PsExec).

Als uw besturings systeem micro soft Windows 10 is, gaat u verder met de onderstaande instructies:

14. Open in de virtuele machine met PsExec de bestanden Verkenner en kopieer disablesxsstackrc.ps1 naar het systeem station van de virtuele machine met de gestapelde side-by-side-stack.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname is de computer naam van de virtuele machine met de niet-gelijktijdige stack.

15. Het aanbevolen proces: vanuit het PsExec-hulp programma start u Power shell en navigeert u naar de map uit de vorige stap en voert u disablesxsstackrc.ps1 uit. U kunt ook de volgende cmdlets uitvoeren:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Wanneer de cmdlets worden uitgevoerd, start u de VM opnieuw op met de stack-aan-zij die niet goed werkt.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>De licentie modus Extern bureaublad is niet geconfigureerd

Als u zich met een Administrator-account aanmeldt bij Windows 10 Enter prise multi-session, ontvangt u mogelijk een melding met de tekst ' Extern bureaublad licentie modus is niet geconfigureerd Extern bureaublad-services werkt niet meer over X dagen. Gebruik op de Connection Broker-server Serverbeheer om de Extern bureaublad licentie modus op te geven. "

Als de tijds limiet verloopt, wordt een fout bericht weer gegeven met de tekst ' de externe sessie is beëindigd omdat er geen Extern bureaublad licenties voor client toegang beschikbaar zijn voor deze computer. '

Als u een van deze berichten ziet, betekent dit dat de installatie kopie niet de meest recente Windows-updates heeft geïnstalleerd of dat u de Extern bureaublad licentie modus instelt via groeps beleid. Volg de stappen in de volgende secties om de groeps beleids instelling te controleren, de versie van Windows 10 Enter prise multi-session te identificeren en de bijbehorende update te installeren.

>[!NOTE]
>Voor het virtuele bureau blad van Windows is alleen een RDS-Client Access License (CAL) vereist wanneer uw hostgroep Windows Server Session hosts bevat. Zie [licentie voor uw RDS-implementatie met licenties voor client toegang](/windows-server/remote/remote-desktop-services/rds-client-access-license/)voor meer informatie over het configureren van een RDS CAL.

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>De groeps beleids instelling Extern bureaublad licentie modus uitschakelen

Controleer de groeps beleids instelling door de Groepsbeleid editor te openen in de virtuele machine en te navigeren naar **Beheersjablonen**  >  **Windows-onderdelen**  >  **extern bureaublad-services**  >  **extern bureaublad host**-  >  **licentie verlening**  >  **de Extern bureaublad licentie modus instellen**. Als de groeps beleids instelling is **ingeschakeld**, wijzigt u deze in **uitgeschakeld**. Als de functie al is uitgeschakeld, kunt u deze vervolgens ongewijzigd laten.

>[!NOTE]
>Als u groeps beleid via uw domein instelt, schakelt u deze instelling uit voor beleids regels die zijn gericht op deze Windows 10 Enter prise-Vm's met meerdere sessies.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Bepalen welke versie van Windows 10 Enter prise multi-sessie u gebruikt

Controleren welke versie van Windows 10 Enter prise multi-session u hebt:

1. Meld u aan met uw beheerders account.
2. Geef "about" op in de zoek balk naast het menu Start.
3. Selecteer **over uw PC**.
4. Controleer het nummer naast versie. Het getal moet ' 1809 ' of ' 1903 ' zijn, zoals wordt weer gegeven in de volgende afbeelding.

    > [!div class="mx-imgBorder"]
    > ![Een scherm opname van het Windows-specificaties venster. Het versie nummer is blauw gemarkeerd.](../media/windows-specifications.png)

Nu u het versie nummer weet, gaat u verder met de relevante sectie.

### <a name="version-1809"></a>Versie 1809

Als uw versie nummer 1809 is, installeert u [de update KB4516077](https://support.microsoft.com/help/4516077).

### <a name="version-1903"></a>Versie 1903

Implementeer het hostbesturingssysteem opnieuw met de nieuwste versie van de installatie kopie van Windows 10, versie 1903 van de Azure Gallery.

## <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>Er kan geen verbinding worden gemaakt met de externe computer vanwege een beveiligings fout

Als uw gebruikers een fout melding krijgen dat er geen verbinding kan worden gemaakt met de externe PC vanwege een beveiligings fout. Als dit probleem zich blijft voordoen, vraagt u uw beheerder of technische ondersteuning voor hulp, "Valideer alle bestaande beleids regels die standaard-RDP-machtigingen wijzigen. Een beleids regel die ervoor kan zorgen dat deze fout wordt weer gegeven, is ' aanmelden toestaan via Extern bureaublad-services beveiligings beleid '.

Zie [Aanmelden via Extern bureaublad-services toestaan](/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services)voor meer informatie over dit beleid.

## <a name="next-steps"></a>Volgende stappen

- Zie [probleemoplossings overzicht, feedback en ondersteuning](troubleshoot-set-up-overview-2019.md)voor een overzicht van het oplossen van problemen met het virtuele bureau blad van Windows en de escalatie trajecten.
- Zie [Tenant en hostgroep maken](troubleshoot-set-up-issues-2019.md)voor informatie over het oplossen van problemen bij het maken van een Tenant en een hostgroep in een virtueel-bureaublad omgeving van Windows.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration-2019.md)van de host.
- Zie [Windows Virtual Desktop Service Connections](troubleshoot-service-connection-2019.md)(Engelstalig) voor het oplossen van problemen met Windows Virtual Desktop-Client verbindingen.
- Zie [problemen met de Extern bureaublad-client oplossen](../troubleshoot-client.md) om problemen met extern bureaublad-clients op te lossen
- Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell-2019.md)(Engelstalig) voor informatie over het oplossen van problemen met het gebruik van Power shell met Windows virtueel bureau blad.
- Zie [Windows Virtual Desktop Environment](environment-setup-2019.md)(Engelstalig) voor meer informatie over de service.
- Zie [zelf studie: problemen met implementaties van Resource Manager-sjablonen oplossen](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)om de zelf studie voor problemen oplossen op te lossen.
- Zie [bewerkingen controleren met Resource Manager](../../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over controle acties.
- Zie [implementatie bewerkingen weer geven](../../azure-resource-manager/templates/deployment-history.md)voor meer informatie over acties om de fouten te bepalen tijdens de implementatie.
