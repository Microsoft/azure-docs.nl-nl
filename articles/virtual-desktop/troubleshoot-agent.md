---
title: Problemen met Windows Virtual Desktop agent oplossen-Azure
description: Veelvoorkomende problemen met de agent en connectiviteit oplossen.
author: Sefriend
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 86296385a0e657246e415f326261ce401e3cdeaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604182"
---
# <a name="troubleshoot-common-windows-virtual-desktop-agent-issues"></a>Veelvoorkomende problemen met Windows Virtual Desktop agent oplossen

De Windows Virtual Desktop-agent kan verbindings problemen veroorzaken vanwege meerdere factoren:
   - Er is een fout opgetreden in de Broker waardoor de agent de service stopt.
   - Problemen met updates.
   - Problemen met de installatie tijdens de installatie van de agent, waardoor de verbinding met de sessie-host wordt verstoord.

Dit artikel leidt u door oplossingen voor deze algemene scenario's en over het oplossen van verbindings problemen.

>[!NOTE]
>Voor het oplossen van problemen met sessie connectiviteit en de virtuele bureau blad-agent van Windows, raden we u aan de gebeurtenis Logboeken in **Logboeken**  >  **Windows-logboeken**-  >  **toepassing** te controleren. Zoek naar gebeurtenissen met een van de volgende bronnen om het probleem te identificeren:
>
>- WVD-Agent
>- WVD-agent-updater
>- RDAgentBootLoader
>- MsiInstaller

## <a name="error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running"></a>Fout: de RDAgentBootLoader-en/of Extern bureaublad agent-lader is gestopt met uitvoeren

Als u een van de volgende problemen ziet, betekent dit dat het opstart laad programma, dat de agent laadt, de agent niet op de juiste wijze kon installeren en de Agent service niet wordt uitgevoerd:
- **RDAgentBootLoader** is gestopt of niet actief.
- Er is geen status voor de **extern bureaublad agent loader**.

Om dit probleem op te lossen, start u de RDAgent-opstart lader:

1. Klik in het venster Services met de rechter muisknop op **extern bureaublad agent loader**.
2. Selecteer **Starten**. Als deze optie voor u grijs wordt weer gegeven, hebt u geen beheerders machtigingen en moet u deze ontvangen om de service te starten.
3. Wacht 10 seconden en klik met de rechter muisknop op **extern bureaublad agent loader**.
4. Selecteer **Vernieuwen**.
5. Als de service stopt nadat u deze hebt gestart en vernieuwd, is er mogelijk een registratie fout opgetreden. Zie [INVALID_REGISTRATION_TOKEN](#error-invalid_registration_token)voor meer informatie.

## <a name="error-invalid_registration_token"></a>Fout: INVALID_REGISTRATION_TOKEN

Ga naar **Logboeken**  >  **Windows-logboeken**-  >  **toepassing**. Als er een gebeurtenis met ID 3277 wordt weer geven met de tekst **INVALID_REGISTRATION_TOKEN** in de beschrijving, wordt het registratie token dat u niet als geldig herkend.

U kunt dit probleem oplossen door een geldig registratie token te maken:

1. Als u een nieuw registratie token wilt maken, volgt u de stappen in de sectie [een nieuwe registratie sleutel genereren voor de VM](#step-3-generate-a-new-registration-key-for-the-vm) .
2. Open de register-editor. 
3. Ga naar **HKEY_LOCAL_MACHINE**-  >  **Software**  >  **micro soft**  >  **RDInfraAgent**.
4. Selecteer **IsRegistered**. 
5. Typ in het vak **Waardegegevens:** invoer **0** en selecteer **OK**. 
6. Selecteer **RegistrationToken**. 
7. Plak in het vak **Waardegegevens:** invoer het registratie token uit stap 1. 

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van IsRegistered 0](media/isregistered-token.png)

8. Open een opdrachtprompt als beheerder.
9. Voer **net stop RDAgentBootLoader** in. 
10. Voer **net start RDAgentBootLoader** in. 
11. Open de register-editor.
12. Ga naar **HKEY_LOCAL_MACHINE**-  >  **Software**  >  **micro soft**  >  **RDInfraAgent**.
13. Controleer of **IsRegistered** is ingesteld op 1 en er niets is in de kolom gegevens voor **RegistrationToken**. 

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van IsRegistered 1](media/isregistered-registry.png)

## <a name="error-agent-cannot-connect-to-broker-with-invalid_form"></a>Fout: de agent kan geen verbinding maken met de broker met INVALID_FORM

Ga naar **Logboeken**  >  **Windows-logboeken**-  >  **toepassing**. Als er een gebeurtenis met ID 3277 wordt weer geven met de melding ' INVALID_FORM ' in de beschrijving, is er iets mis gegaan met de communicatie tussen de agent en de Broker. De agent kan geen verbinding maken met de Broker of een bepaalde URL bereiken vanwege bepaalde firewall-of DNS-instellingen.

Om dit probleem op te lossen, controleert u of u BrokerURI en BrokerURIGlobal kunt bereiken:
1. Open de REGI ster-editor. 
2. Ga naar **HKEY_LOCAL_MACHINE**-  >  **Software**  >  **micro soft**  >  **RDInfraAgent**. 
3. Noteer de waarden voor **BrokerURI** en **BrokerURIGlobal**.

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van Broker URI en Broker URI Global](media/broker-uri.png)

 
4. Open een browser en ga naar *\<BrokerURI\> API/Health*. 
   - Zorg ervoor dat u de waarde uit stap 3 gebruikt in de **BrokerURI**. In deze sectie is dit een voor beeld <https://rdbroker-g-us-r0.wvd.microsoft.com/api/health> .
5. Open een ander tabblad in de browser en ga naar *\<BrokerURIGlobal\> API/Health*. 
   - Zorg ervoor dat u de waarde uit stap 3 gebruikt in de **BrokerURIGlobal** -koppeling. In deze sectie is dit een voor beeld <https://rdbroker.wvd.microsoft.com/api/health> .
6. Als het netwerk de Broker-verbinding niet blokkeert, worden beide pagina's geladen en wordt er een bericht weer gegeven met de tekst **' RD Broker is in orde '** , zoals in de volgende scherm afbeeldingen wordt weer gegeven. 

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van de toegang tot de Broker-URI is geladen](media/broker-uri-web.png)

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van de toegang tot globale URI van Broker is geladen](media/broker-global.png)
 

7. Als het netwerk de Broker-verbinding blokkeert, worden de pagina's niet geladen, zoals wordt weer gegeven in de volgende scherm afbeelding. 

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van de geladen Broker-toegang is mislukt](media/unsuccessful-broker-uri.png)

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van de niet-voltooide geladen Broker-wereld wijde toegang](media/unsuccessful-broker-global.png)

8. Als het netwerk deze Url's blokkeert, moet u de blok kering van de vereiste Url's opheffen. Zie de [lijst met vereiste url's](safe-url-list.md)voor meer informatie.
9. Als het probleem hierdoor niet wordt opgelost, controleert u of er geen groeps beleid is met code ringen waarmee de agent wordt geblokkeerd voor de Broker-verbinding. Windows virtueel bureau blad maakt gebruik van dezelfde TLS 1,2-code ringen als [Azure front-deur](../frontdoor/front-door-faq.MD#what-are-the-current-cipher-suites-supported-by-azure-front-door). Zie [Connection Security](network-connectivity.md#connection-security)(Engelstalig) voor meer informatie.

## <a name="error-3703"></a>Fout: 3703

Ga naar **Logboeken**  >  **Windows-logboeken**-  >  **toepassing**. Als er een gebeurtenis met de ID 3703 wordt weer geven met de tekst ' RD-gateway URL: is niet toegankelijk ' in de beschrijving, kan de agent de gateway-Url's niet bereiken. Als u verbinding wilt maken met uw sessiehost en netwerk verkeer naar deze eind punten wilt toestaan om beperkingen te omzeilen, moet u de blok kering van de Url's in de lijst van de [vereiste](safe-url-list.md)url's opheffen. Zorg er ook voor dat uw firewall of proxy instellingen deze Url's niet blok keren. Het blok keren van deze Url's is vereist voor het gebruik van Windows virtueel bureau blad.

U kunt dit probleem oplossen door te controleren of uw firewall en/of DNS-instellingen deze Url's niet blok keren:
1. [Gebruik Azure firewall om implementaties van Windows virtueel bureau blad te beveiligen.](../firewall/protect-windows-virtual-desktop.md)
2. Configureer uw [Azure firewall DNS-instellingen](../firewall/dns-settings.md).

## <a name="error-3019"></a>Fout: 3019

Ga naar **Logboeken**  >  **Windows-logboeken**-  >  **toepassing**. Als er een gebeurtenis met ID 3019 wordt weer geven, betekent dit dat de agent de Url's van de Web socket-Trans Port niet kan bereiken. Om verbinding te maken met uw sessiehost en netwerk verkeer toe te staan deze beperkingen te omzeilen, moet u de blok kering van de Url's in de [lijst vereiste URL](safe-url-list.md)opheffen. Werk samen met het netwerk team van Azure om ervoor te zorgen dat uw firewall, proxy en DNS-instellingen deze Url's niet blok keren. U kunt ook de logboeken van de netwerk tracering controleren om te bepalen waar de Windows Virtual Desktop-service wordt geblokkeerd. Als u een ondersteunings aanvraag voor dit specifieke probleem opent, moet u ervoor zorgen dat u uw netwerk traceer logboeken aan de aanvraag koppelt.

## <a name="error-installationhealthcheckfailedexception"></a>Fout: InstallationHealthCheckFailedException

Ga naar **Logboeken**  >  **Windows-logboeken**-  >  **toepassing**. Als u een gebeurtenis ziet met ID 3277 die "InstallationHealthCheckFailedException" in de beschrijving voor komt, betekent dit dat de stack-listener niet werkt omdat de Terminal Server de register sleutel voor de stack-listener heeft in-of uitgeschakeld.

Ga als volgt te werk om het probleem op te lossen:
1. Controleer of [de stack-listener werkt](#error-stack-listener-isnt-working-on-windows-10-2004-vm).
2. Als de stack-listener niet werkt, [verwijdert u hand matig het stack onderdeel en installeert u het opnieuw](#error-vms-are-stuck-in-unavailable-or-upgrading-state).

## <a name="error-endpoint_not_found"></a>Fout: ENDPOINT_NOT_FOUND

Ga naar **Logboeken**  >  **Windows-logboeken**-  >  **toepassing**. Als u een gebeurtenis ziet met ID 3277 die "ENDPOINT_NOT_FOUND" in de beschrijving betekent dat de Broker geen eind punt kan vinden voor het maken van een verbinding met. Dit verbindings probleem kan een van de volgende oorzaken hebben:

- Er zijn geen virtuele machines in uw hostgroep
- De Vm's in uw hostgroep zijn niet actief
- Alle virtuele machines in uw hostgroep hebben de maximale sessie limiet overschreden
- Voor geen van de virtuele machines in uw hostgroep wordt de Agent service uitgevoerd

Ga als volgt te werk om het probleem op te lossen:

1. Zorg ervoor dat de virtuele machine is ingeschakeld en niet is verwijderd uit de hostgroep.
2. Zorg ervoor dat de virtuele machine de limiet voor het maximum aantal sessies niet overschrijdt.
3. Zorg ervoor dat de [Agent service wordt uitgevoerd](#error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running) en dat de [stack-listener werkt](#error-stack-listener-isnt-working-on-windows-10-2004-vm).
4. Zorg ervoor dat [de agent verbinding kan maken met de Broker](#error-agent-cannot-connect-to-broker-with-invalid_form).
5. Zorg ervoor dat [uw virtuele machine een geldig registratie token heeft](#error-invalid_registration_token).
6. Zorg ervoor dat [het registratie token van de virtuele machine nog niet is verlopen](faq.md#how-often-should-i-turn-my-vms-on-to-prevent-registration-issues). 

## <a name="error-installmsiexception"></a>Fout: InstallMsiException

Ga naar **Logboeken**  >  **Windows-logboeken**-  >  **toepassing**. Als u een gebeurtenis ziet met de ID 3277, met de tekst **InstallMsiException** in de beschrijving, wordt het installatie programma al uitgevoerd voor een andere toepassing tijdens het installeren van de agent of wordt het msiexec.exe programma geblokkeerd door een beleid.

U kunt dit probleem oplossen door het volgende beleid uit te scha kelen:
   - Windows Installer uitschakelen  
      - Pad naar categorie: Gebruikersconfiguratie\beheersjablonen\windows-onderdelen\Windows Installer
   
>[!NOTE]
>Dit is geen uitgebreide lijst met beleids regels, alleen de instellingen waarvan we momenteel op de hoogte zijn.

Een beleid uitschakelen:
1. Open een opdrachtprompt als beheerder.
2. Voer **RSoP. msc** in en voer deze uit.
3. Ga in het venster **Resulterende verzameling beleids** regels dat verschijnt op het pad naar de categorie.
4. Selecteer het beleid.
5. Selecteer **Uitgeschakeld**.
6. Selecteer **Toepassen**.   

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van Windows Installer beleid in resulterende verzameling beleids regels](media/gpo-policy.png)

## <a name="error-win32exception"></a>Fout: Win32Exception

Ga naar **Logboeken**  >  **Windows-logboeken**-  >  **toepassing**. Als u een gebeurtenis ziet met ID 3277, die **InstallMsiException** in de beschrijving, een beleid blokkeert cmd.exe van het starten. Als u dit programma blokkeert, kunt u het console venster niet uitvoeren. Dit is wat u moet gebruiken om de service opnieuw te starten wanneer de agent wordt bijgewerkt.

U kunt dit probleem oplossen door het volgende beleid uit te scha kelen:
   - Toegang tot de opdrachtprompt voorkomen   
      - Pad naar categorie: Gebruikersconfiguratie\Beheersjablonen\Windows-onderdelen\Windows Computerconfiguratie\beheersjablonen\systeem
    
>[!NOTE]
>Dit is geen uitgebreide lijst met beleids regels, alleen de instellingen waarvan we momenteel op de hoogte zijn.

Een beleid uitschakelen:
1. Open een opdrachtprompt als beheerder.
2. Voer **RSoP. msc** in en voer deze uit.
3. Ga in het venster **Resulterende verzameling beleids** regels dat verschijnt op het pad naar de categorie.
4. Selecteer het beleid.
5. Selecteer **Uitgeschakeld**.
6. Selecteer **Toepassen**.   

## <a name="error-stack-listener-isnt-working-on-windows-10-2004-vm"></a>Fout: de stack-listener werkt niet op de VM met Windows 10 2004

Voer **qwinsta** uit in de opdracht prompt en noteer het versie nummer dat naast **RDP-SxS** wordt weer gegeven. Als u de onderdelen van **RDP-TCP** en **RDP-SxS** niet ziet, **Luister** dan of ze helemaal niet worden weer gegeven na het uitvoeren van **qwinsta**, betekent dit dat er een stack probleem is. Stack-updates worden geïnstalleerd samen met agent updates en wanneer deze installatie awry gaat, werkt de virtueel bureau blad-listener van Windows niet.

Ga als volgt te werk om het probleem op te lossen:
1. Open de register-editor.
2. Ga naar **HKEY_LOCAL_MACHINE**  >  **System**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**-  >  **winst**.
3. Onder **winst** weergave ziet u mogelijk meerdere mappen voor verschillende stack versies. Selecteer de map die overeenkomt met de versie-informatie die u hebt gezien tijdens het uitvoeren van **Qwinsta** in de opdracht prompt.
4. Zoek **fReverseConnectMode** en controleer of de gegevens waarde **1** is. Zorg er ook voor dat **fEnableWinStation** is ingesteld op **1**.

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van fReverseConnectMode](media/fenable-2.png)

5. Als **fReverseConnectMode** niet is ingesteld op **1**, selecteert u **fReverseConnectMode** en voert u **1** in het veld waarde in. 
6. Als **fEnableWinStation** niet is ingesteld op **1**, selecteert u **fEnableWinStation** en voert u **1** in het veld waarde in.
7. Start de VM opnieuw op. 

>[!NOTE]
>Als u de **fReverseConnectMode** -of **fEnableWinStation** -modus voor meerdere vm's tegelijk wilt wijzigen, kunt u een van de volgende twee dingen doen:
>
>- Exporteer de register sleutel van de computer die u al hebt en importeer deze in alle andere computers die deze wijziging nodig hebben.
>- Maak een groeps beleidsobject (GPO) waarmee de register sleutel waarde wordt ingesteld voor de machines die de wijziging nodig hebben.

7. Ga naar **HKEY_LOCAL_MACHINE**  >  **System**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **ClusterSettings**.
8. Zoek onder **ClusterSettings** naar **SessionDirectoryListener** en controleer of de gegevens waarde **RDP-SxS is...**.
9. Als **SessionDirectoryListener** niet is ingesteld op **RDP-SxS...**, moet u de stappen in de sectie [het verwijderen van de agent en de opstart lader](#step-1-uninstall-all-agent-boot-loader-and-stack-component-programs) volgen om eerst de agent, het opstart laad programma en stack onderdelen te verwijderen en vervolgens [de agent en het laad programma voor het opstarten opnieuw te installeren](#step-4-reinstall-the-agent-and-boot-loader). Hiermee wordt de side-by-side-stack opnieuw geïnstalleerd.

## <a name="error-heartbeat-issue-where-users-keep-getting-disconnected-from-session-hosts"></a>Fout: heartbeat-probleem waarbij gebruikers de verbinding met de sessie-hosts blijven verbreken

Als uw server geen heartbeat van de virtueel bureau blad-service van Windows ophaalt, moet u de drempel waarde voor heartbeats wijzigen. Volg de instructies in deze sectie als er een of meer van de volgende scenario's van toepassing zijn:

- U ontvangt een **CheckSessionHostDomainIsReachableAsync** -fout
- U ontvangt een **ConnectionBrokenMissedHeartbeatThresholdExceeded** -fout
- U ontvangt een **ConnectionEstablished: UnexpectedNetworkDisconnect** -fout
- Gebruikers clients blijven de verbinding verbreken
- Gebruikers blijven de verbinding met hun sessie-hosts verbreken

De drempel waarde voor heartbeats wijzigen:
1. Open de opdracht prompt als beheerder.
2. Voer de opdracht **qwinsta** uit en voer deze uit.
3. Er moeten twee stack onderdelen worden weer gegeven: **RDP-TCP** en **RDP-SxS**. 
   - Afhankelijk van de versie van het besturings systeem dat u gebruikt, kan **RDP-SxS** worden gevolgd door het build-nummer. Als dat het geval is, noteert u dit nummer voor later.
4. Open de register-editor.
5. Ga naar **HKEY_LOCAL_MACHINE**  >  **System**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**-  >  **winst**.
6. Onder **winst**, ziet u mogelijk meerdere mappen voor verschillende stack versies. Selecteer de map die overeenkomt met het versie nummer uit stap 3.
7. Maak een nieuwe DWORD-register sleutel door met de rechter muisknop op de REGI ster-editor te klikken en vervolgens **nieuwe**  >  **DWORD-waarde (32-bits)** te selecteren. Wanneer u de DWORD maakt, voert u de volgende waarden in:
   - HeartbeatInterval: 10000
   - HeartbeatWarnCount: 30 
   - HeartbeatDropCount: 60 
8. Start de VM opnieuw op.

>[!NOTE]
>Als het probleem niet wordt opgelost door de heartbeat-drempel waarde te wijzigen, hebt u mogelijk een onderliggend netwerk probleem dat u nodig hebt om contact op te nemen met het Azure-netwerk team over.

## <a name="error-downloadmsiexception"></a>Fout: DownloadMsiException

Ga naar **Logboeken**  >  **Windows-logboeken**-  >  **toepassing**. Als er een gebeurtenis wordt weer geven met de ID 3277, met de tekst **DownloadMsiException** in de beschrijving, is er onvoldoende ruimte op de schijf voor de RDAgent.

U kunt dit probleem oplossen door de ruimte op uw schijf te maken door:
   - Bestanden verwijderen die niet langer deel uitmaken van de gebruiker
   - De opslag capaciteit van uw virtuele machine verhogen

## <a name="error-agent-fails-to-update-with-missingmethodexception"></a>Fout: kan de agent niet bijwerken met MissingMethodException

Ga naar **Logboeken**  >  **Windows-logboeken**-  >  **toepassing**. Als er een gebeurtenis met ID 3389 wordt weer gegeven met de tekst "MissingMethodException: methode niet gevonden" in de beschrijving, betekent dit dat de virtuele Windows-bureau blad-agent niet is bijgewerkt en naar een eerdere versie is teruggekeerd. Dit komt mogelijk doordat het versie nummer van het .NET Framework dat momenteel op uw Vm's is geïnstalleerd, lager is dan 4.7.2. Om dit probleem op te lossen, moet u de .NET bijwerken naar versie 4.7.2 of hoger door de installatie-instructies te volgen in de [documentatie van .NET Framework](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2).


## <a name="error-vms-are-stuck-in-unavailable-or-upgrading-state"></a>Fout: Vm's zijn vastgelopen in niet-beschik bare of upgrade-status

Open een Power shell-venster als beheerder en voer de volgende cmdlet uit:

```powershell
Get-AzWvdSessionHost -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> | Select-Object *
```

Als de status die wordt vermeld voor de sessiehost of hosts in uw hostgroep altijd ' niet beschikbaar ' of ' upgrade ' bevat, is de agent of stack niet geïnstalleerd.

Om dit probleem op te lossen, installeert u de side-by-side-stack opnieuw:
1. Open een opdrachtprompt als beheerder.
2. Voer **net stop RDAgentBootLoader** in. 
3. Ga naar **configuratie scherm**  >  **Program** ma's  >  **Program ma's en onderdelen**.
4. Verwijder de nieuwste versie van de **extern bureaublad-services SxS-netwerk stack** of de versie die wordt vermeld in **HKEY_LOCAL_MACHINE**  >  **System**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**-  >  **winst** onder **ReverseConnectListener**.
5. Open een console venster als beheerder en ga naar **programma bestanden**  >  **micro soft RDInfra**.
6. Selecteer het onderdeel **SxSStack** of voer de opdracht **msiexec/i SxSStack- <version> . MSI** uit om het MSI-bestand te installeren.
8. Start de VM opnieuw op.
9. Ga terug naar de opdracht prompt en voer de opdracht **qwinsta** uit.
10. Controleer of het stack onderdeel dat in stap 6 is geïnstalleerd, hierop wordt **geluisterd** .
   - Als dit het geval is, voert u **net start RDAgentBootLoader** in het opdracht prompt in en start u de VM opnieuw op.
   - Als dat niet het geval is, moet u [de virtuele machine opnieuw registreren en het onderdeel agent opnieuw installeren](#your-issue-isnt-listed-here-or-wasnt-resolved) .

## <a name="error-connection-not-found-rdagent-does-not-have-an-active-connection-to-the-broker"></a>Fout: kan geen verbinding vinden: RDAgent heeft geen actieve verbinding met de Broker

Uw Vm's kunnen de verbindings limiet hebben, zodat de virtuele machine geen nieuwe verbindingen kan accepteren.

Ga als volgt te werk om het probleem op te lossen:
   - Verminder de maximale sessie limiet. Dit zorgt ervoor dat resources gelijkmatig worden gedistribueerd over sessie-hosts en de uitputting van resources voor komen.
   - Verg root de resource capaciteit van de Vm's.

## <a name="error-operating-a-pro-vm-or-other-unsupported-os"></a>Fout: een professionele virtuele machine of een ander niet-ondersteund besturings systeem wordt uitgevoerd

De side-by-side stack wordt alleen ondersteund door Windows Enter prise-of Windows Server Sku's, wat betekent dat besturings systemen zoals Pro VM niet. Als u geen Enter prise-of server-SKU hebt, wordt de stack geïnstalleerd op uw VM, maar niet geactiveerd, zodat deze niet wordt weer gegeven wanneer u **qwinsta** in de opdracht regel uitvoert.

U kunt dit probleem oplossen door een VM te maken die Windows Enter prise of Windows Server is.
1. Ga naar [Details van de virtuele machine](create-host-pools-azure-marketplace.md#virtual-machine-details) en volg de stappen 1-12 om een van de volgende aanbevolen installatie kopieën in te stellen:
   - Windows 10 Enter prise-meerdere sessies, versie 1909
   - Windows 10 Enter prise multi-session, versie 1909 + Microsoft 365-apps
   - Windows Server 2019 Datacenter
   - Windows 10 Enter prise-meerdere sessies, versie 2004
   - Windows 10 Enter prise multi-session, versie 2004 + Microsoft 365-apps
2. Selecteer **controleren en maken**.

## <a name="error-name_already_registered"></a>Fout: NAME_ALREADY_REGISTERED

De naam van uw virtuele machine is al geregistreerd en is waarschijnlijk een duplicaat.

Ga als volgt te werk om het probleem op te lossen:
1. Volg de stappen in de sectie [hostgroep verwijderen van](#step-2-remove-the-session-host-from-the-host-pool) de sessiehost.
2. [Maak een andere VM](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal). Zorg ervoor dat u een unieke naam voor deze virtuele machine kiest.
3. Ga naar de [Azure Portal](https://portal.azure.com) en open de **overzichts** pagina voor de hostgroep waarin de VM zich bevindt. 
4. Open het tabblad **Session hosts** en controleer of alle sessie-hosts zich in die hostgroepen bevinden.
5. Wacht 5-10 minuten totdat de status van de sessie-host **beschikbaar is**.

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van beschik bare sessie-host](media/hostpool-portal.png)

## <a name="your-issue-isnt-listed-here-or-wasnt-resolved"></a>Uw probleem wordt hier niet vermeld of het is niet opgelost

Als u het probleem niet kunt vinden in dit artikel of als u de instructies niet hebt gevonden, raden wij u aan Windows Virtual Desktop Agent te verwijderen, opnieuw te installeren en opnieuw te registreren. In de instructies in deze sectie wordt uitgelegd hoe u uw virtuele machine opnieuw registreert bij de virtueel-bureaublad service van Windows door alle agents, opstart lader en stack onderdelen te verwijderen, de sessiehost uit de hostgroep te wissen, een nieuwe registratie sleutel te genereren voor de virtuele machine en de agent en het laad programma opnieuw te installeren. Als een of meer van de volgende scenario's van toepassing zijn op u, volgt u deze instructies:
- De VM is vastgelopen in de **upgrade** of is **niet beschikbaar**
- De stack-listener werkt niet en u werkt met Windows 10 1809, 1903 of 1904
- U ontvangt een **EXPIRED_REGISTRATION_TOKEN** fout
- Uw Vm's worden niet weer gegeven in de lijst sessie hosts
- De **extern bureaublad agent loader** wordt niet weer geven in het venster Services
- U ziet het onderdeel **RdAgentBootLoader** niet in taak beheer
- U ontvangt een **Connection Broker die de instellingen** fout op aangepaste installatie kopie-vm's niet kan valideren
- De instructies in dit artikel hebben het probleem niet opgelost

### <a name="step-1-uninstall-all-agent-boot-loader-and-stack-component-programs"></a>Stap 1: alle agents, opstart lader en stack component-Program ma's verwijderen

Voordat u de agent, het opstart laad programma en de stack opnieuw installeert, moet u alle bestaande onderdelen van de virtuele machine verwijderen. Alle agents, opstart lader en stack component-Program ma's verwijderen:
1. Meld u als beheerder aan bij de virtuele machine. 
2. Ga naar **configuratie scherm**  >  **Program** ma's  >  **Program ma's en onderdelen**.
3. Verwijder de volgende Program ma's:
   - Opstart lader van Extern bureaublad agent
   - Extern bureaublad-services infrastructuur agent
   - Agent van Extern bureaublad-services-infra structuur Genève
   - Extern bureaublad-services SxS-netwerk stack
   
>[!NOTE]
>Mogelijk ziet u meerdere exemplaren van deze Program ma's. Zorg ervoor dat u deze verwijdert.

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van het verwijderen van Program ma's](media/uninstall-program.png)

### <a name="step-2-remove-the-session-host-from-the-host-pool"></a>Stap 2: de sessiehost uit de hostgroep verwijderen

Wanneer u de sessiehost uit de hostgroep verwijdert, wordt de sessiehost niet meer geregistreerd bij die hostgroep. Dit fungeert als opnieuw instellen voor de registratie van de sessiehost. De sessiehost verwijderen uit de hostgroep:
1. Ga naar de **overzichts** pagina voor de hostgroep waarin uw VM zich bevindt in de [Azure Portal](https://portal.azure.com). 
2. Ga naar het tabblad **Session hosts** om de lijst met alle sessie-hosts in die hostgroep weer te geven.
3. Bekijk de lijst met sessie-hosts en selecteer de virtuele machine die u wilt verwijderen.
4. Selecteer **Verwijderen**.  

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van het verwijderen van een virtuele machine uit een hostgroep](media/remove-sh.png)

### <a name="step-3-generate-a-new-registration-key-for-the-vm"></a>Stap 3: een nieuwe registratie sleutel genereren voor de VM

U moet een nieuwe registratie sleutel genereren die wordt gebruikt om uw VM opnieuw te registreren bij de hostgroep en de service. Een nieuwe registratie sleutel genereren voor de VM:
1. Open de [Azure Portal](https://portal.azure.com) en ga naar de **overzichts** pagina voor de HOSTGROEP van de virtuele machine die u wilt bewerken.
2. Selecteer **registratie sleutel**.

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van de registratie sleutel in de portal](media/reg-key.png)

3. Open het tabblad **registratie sleutel** en selecteer **nieuwe sleutel genereren**.
4. Voer de verval datum in en selecteer **OK**.  

>[!NOTE]
>De verval datum mag niet korter zijn dan een uur en niet langer dan 27 dagen vanaf de generatie tijd en-datum. We raden u aan de verval datum in te stellen op het maximum van 27 dagen.

5. Kopieer de zojuist gegenereerde sleutel naar het klem bord. U hebt deze sleutel later nodig.

### <a name="step-4-reinstall-the-agent-and-boot-loader"></a>Stap 4: de agent en de opstart lader opnieuw installeren

Door de meest recente versie van de agent en de opstart lader opnieuw te installeren, wordt de side-by-side-stack en de bewakings agent voor Genève automatisch geïnstalleerd. De agent en het laad programma voor opstarten opnieuw installeren:
1. Meld u bij uw virtuele machine aan als beheerder en gebruik de juiste versie van het installatie programma van de agent voor uw implementatie, afhankelijk van de versie van Windows die op uw virtuele machine wordt uitgevoerd. Als u een virtuele machine met Windows 10 hebt, volgt u de instructies in [virtuele machines registreren](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) om de **virtuele Windows-bureau blad-agent** en de bootloader van de **Windows Virtual Desktop agent** te downloaden. Als u een virtuele machine met Windows 7 hebt, volgt u stap 13-14 in [virtuele machines registreren](deploy-windows-7-virtual-machine.md#configure-a-windows-7-virtual-machine) om de **virtuele Windows-bureau blad-agent** en het **Windows Virtual Desktop agent-beheer** te downloaden.

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van de download pagina van de agent en de bootloader](media/download-agent.png)

2. Klik met de rechter muisknop op de agent-en opstart lader installatie Programma's die u hebt gedownload.
3. Selecteer **Eigenschappen**.
4. Selecteer **Blokkering opheffen**.
5. Selecteer **OK**.
6. Voer het installatieprogramma van de agent uit.
7. Wanneer het installatie programma u vraagt om het registratie token, plakt u de registratie sleutel van het klem bord. 

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van geplakte registratie token](media/pasted-agent-token.png)

8. Voer het installatie programma voor de opstart lader uit.
9. Start de VM opnieuw op. 
10. Ga naar de [Azure Portal](https://portal.azure.com) en open de **overzichts** pagina voor de hostgroep waartoe uw VM behoort.
11. Ga naar het tabblad **Session hosts** om de lijst met alle sessie-hosts in die hostgroep weer te geven.
12. U ziet nu dat de sessie-host die is geregistreerd in de hostgroep, de status **beschikbaar** heeft. 

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van beschik bare sessie-host](media/hostpool-portal.png)

## <a name="next-steps"></a>Volgende stappen

Als het probleem zich blijft voordoen, maakt u een ondersteunings aanvraag en neemt u gedetailleerde informatie op over het probleem dat u ondervindt en de acties die u hebt ondernomen om de oplossing te proberen. De volgende lijst bevat andere bronnen die u kunt gebruiken om problemen op te lossen in uw Windows-implementatie voor virtueel bureau blad.

- Zie [probleemoplossings overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van het oplossen van problemen met het virtuele bureau blad van Windows en de escalatie trajecten.
- Zie [omgeving en hostgroep maken](troubleshoot-set-up-issues.md)om problemen op te lossen tijdens het maken van een hostgroep in een virtuele Windows-desktop omgeving.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host.
- Zie [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md)(Engelstalig) voor het oplossen van problemen met Windows Virtual Desktop-Client verbindingen.
- Zie [problemen met de Extern bureaublad-client oplossen](troubleshoot-client.md)om problemen met extern bureaublad-clients op te lossen.
- Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md)(Engelstalig) voor informatie over het oplossen van problemen met het gebruik van Power shell met Windows virtueel bureau blad.
- Zie [Windows Virtual Desktop Environment](environment-setup.md)(Engelstalig) voor meer informatie over de service.
- Zie [zelf studie: problemen met implementaties van Resource Manager-sjablonen oplossen](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)om de zelf studie voor problemen oplossen op te lossen.
- Zie [bewerkingen controleren met Resource Manager](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over controle acties.
- Zie [implementatie bewerkingen weer geven](../azure-resource-manager/templates/deployment-history.md)voor meer informatie over acties om de fouten te bepalen tijdens de implementatie.
