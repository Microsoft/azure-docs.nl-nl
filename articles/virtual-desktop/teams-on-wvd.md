---
title: Micro soft teams op virtueel bureau blad van Windows-Azure
description: Micro soft teams gebruiken op het virtuele bureau blad van Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 11/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 85026092e513ecc9ec3c23e292a416297774a2e4
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217835"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Micro soft teams gebruiken op het virtuele bureau blad van Windows

>[!IMPORTANT]
>Media optimalisatie voor teams wordt ondersteund voor Microsoft 365 Government (GCC) en GCC-High omgevingen. Media optimalisatie voor teams wordt niet ondersteund voor GCC-High of DoD.

>[!NOTE]
>Media optimalisatie voor micro soft teams is alleen beschikbaar voor de Windows desktop-client op Windows 10-computers. Voor media optimalisaties is Windows desktop client versie 1.2.1026.0 of hoger vereist.

Micro soft teams op het virtuele bureau blad van Windows ondersteunt chatten en samen werking. Met media optimalisatie wordt ook functionaliteit voor bellen en vergaderingen ondersteund. Zie [teams voor gevirtualiseerde desktop infrastructuur voor](/microsoftteams/teams-for-vdi/)meer informatie over het gebruik van micro soft-teams in VDI-omgevingen (Virtual Desktop Infrastructure).

Met media optimalisatie voor micro soft-teams verwerkt de Windows desktop-client de audio en video lokaal voor teams-aanroepen en vergaderingen. U kunt micro soft-teams nog steeds gebruiken op een virtueel Windows-bureau blad met andere clients zonder geoptimaliseerde aanroepen en vergaderingen. Teams en samenwerkings functies worden op alle platforms ondersteund. Als u lokale apparaten wilt omleiden in uw externe sessie, bekijkt u de [Eigenschappen voor het aanpassen van Remote Desktop Protocol voor een hostgroep](#customize-remote-desktop-protocol-properties-for-a-host-pool).

## <a name="prerequisites"></a>Vereisten

Voordat u micro soft teams kunt gebruiken op het virtuele bureau blad van Windows, moet u de volgende dingen doen:

- [Bereid uw netwerk](/microsoftteams/prepare-network/) voor op micro soft teams.
- Installeer de [Windows desktop-client](connect-windows-7-10.md) op een Windows 10-of Windows 10 IOT Enter prise-apparaat dat voldoet aan de hardwarevereisten voor teams van micro soft teams [op een Windows-PC](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/).
- Verbinding maken met een Windows 10-of Windows 10 Enter prise virtual machine (VM).

## <a name="install-the-teams-desktop-app"></a>De teams bureau blad-app installeren

In deze sectie wordt uitgelegd hoe u de teams bureau blad-app kunt installeren op uw Windows 10-VM-installatie kopie voor meerdere sessies of Windows 10 Enter prise. Ga voor meer informatie naar [de teams bureau blad-app installeren of bijwerken op VDI](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi).

### <a name="prepare-your-image-for-teams"></a>Uw installatie kopie voorbereiden voor teams

Als u media optimalisatie voor teams wilt inschakelen, stelt u de volgende register sleutel op de host in:

1. Voer in het menu Start **regedit** uit als Administrator. Navigeer naar **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams**. Maak de team sleutel als deze nog niet bestaat.

2. Maak de volgende waarde voor de sleutel teams:

| Naam             | Type   | Gegevens/waarde  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>De teams-WebSocket-service installeren

Installeer de meest recente [extern bureaublad WebRTC-Redirector-service](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4AQBt) op uw VM-installatie kopie. Als er een installatie fout optreedt, installeert u de [meest recente versie van micro soft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) en probeert u het opnieuw.

#### <a name="latest-websocket-service-versions"></a>Nieuwste versies van de WebSocket-service

De volgende tabel bevat de nieuwste versies van de WebSocket-service:

|Versie        |Releasedatum  |
|---------------|--------------|
|1.0.2006.11001 |07/28/2020    |
|0.11.0         |05/29/2020    |

#### <a name="updates-for-version-10200611001"></a>Updates voor versie 1.0.2006.11001

- Er is een probleem opgelost waarbij het minimaliseren van de app teams tijdens een gesprek of vergadering ertoe leidde dat inkomende video wordt verwijderd.
- Er is ondersteuning toegevoegd voor het selecteren van één monitor voor het delen van bureaublad sessies met meerdere monitors.

### <a name="install-microsoft-teams"></a>Micro soft teams installeren

U kunt de teams bureau blad-app implementeren met behulp van een installatie per machine of per gebruiker. Micro soft teams installeren in uw virtueel-bureaublad omgeving van Windows:

1. Down load het [MSI-pakket voor teams](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) dat overeenkomt met uw omgeving. Het is raadzaam om het 64-bits installatie programma te gebruiken op een 64-bits besturings systeem.

      > [!IMPORTANT]
      > De meest recente update van de bureau blad-client versie 1.3.00.21759 heeft een probleem opgelost waarbij teams de UTC-tijd zone in chat, channels en Calendar hebben getoond. De nieuwe versie van de client geeft de tijd zone van de externe sessie weer.

2. Voer een van de volgende opdrachten uit om de MSI te installeren op de host-VM:

    - Installatie per gebruiker

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name>
        ```

        Dit proces is de standaard installatie, waarmee teams worden geïnstalleerd in de map **% AppData%** gebruiker. Teams werken niet goed met installatie per gebruiker op een niet-permanente configuratie.

    - Installatie per computer

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1
        ```

        Hiermee worden teams geïnstalleerd in de map Program Files (x86) op een 32-bits besturings systeem en in de map Program Files op een 64-bits besturings systeem. Op dit moment is de installatie van de gouden installatie kopie voltooid. Het installeren van teams per computer is vereist voor niet-permanente Setup.

        Er zijn twee vlaggen die kunnen worden ingesteld bij het installeren van teams, **ALLUSER = 1** en **ALLUSERS = 1**. Het is belang rijk dat u begrijpt wat het verschil is tussen deze para meters. De para meter **ALLUSER = 1** wordt alleen gebruikt in VDI-omgevingen om een installatie per computer op te geven. De para meter **ALLUSERS = 1** kan worden gebruikt in niet-VDI-en VDI-omgevingen. Wanneer u deze para meter instelt, wordt het installatie programma van **Teams Machine-Wide** weer gegeven in Program Ma's en onderdelen in het configuratie scherm, evenals apps & functies in Windows-instellingen. Alle gebruikers met beheerders referenties op de computer kunnen teams verwijderen.

        > [!NOTE]
        > Gebruikers en beheerders kunnen de functie voor het automatisch starten van teams tijdens het aanmelden op dit moment niet uitschakelen.

3. Als u de MSI wilt verwijderen van de host-VM, voert u de volgende opdracht uit:

      ```powershell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      Hiermee verwijdert u teams uit de map programma bestanden (x86) of Program Files, afhankelijk van de besturingssysteem omgeving.

      > [!NOTE]
      > Wanneer u teams met de MSI-instelling ALLUSER = 1 installeert, worden automatische updates uitgeschakeld. Het is raadzaam om teams ten minste één keer per maand bij te werken. Voor meer informatie over het implementeren van de bureau blad-app teams, raadpleegt [u de bureau blad teams implementeren op de VM](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/).

### <a name="verify-media-optimizations-loaded"></a>Controleren of de media optimalisaties zijn geladen

Na de installatie van de WebSocket-service en de bureau blad-app teams, voert u de volgende stappen uit om te controleren of teams media optimalisaties zijn geladen:

1. Sluit de team toepassing en start deze opnieuw.

2. Selecteer de installatie kopie van uw gebruikers profiel en selecteer vervolgens **info**.

3. Selecteer **versie**.

      Als media optimalisaties zijn geladen, wordt in de banner weer gegeven dat u de **WVD-media hebt geoptimaliseerd**. Als in het vaandel wordt weer gegeven dat u **WVD media niet hebt verbonden**, sluit u de app teams en probeert u het opnieuw.

4. Selecteer de installatie kopie van uw gebruikers profiel en selecteer vervolgens **instellingen**.

      Als media optimalisaties zijn geladen, worden de audio apparaten en camera's die lokaal beschikbaar zijn, geïnventariseerd in het menu apparaat. Als in het menu **externe audio** wordt weer gegeven, sluit u de app teams en probeert u het opnieuw. Als de apparaten nog steeds niet worden weer gegeven in het menu, controleert u de privacy-instellingen op uw lokale PC. Zorg ervoor dat de machtigingen onder **instellingen**  >  **Privacy**-  >  **app** de instelling **toestaan dat apps toegang hebben tot uw microfoon** **in**-of uitgeschakeld. Verbreek de verbinding met de externe sessie en maak opnieuw verbinding en controleer de audio-en video apparaten opnieuw. Als u aanroepen en vergaderingen wilt samen voegen met video, moet u ook toestemming geven voor apps om toegang te krijgen tot uw camera.

      Als optimalisaties niet worden geladen, verwijdert u de teams opnieuw en voert u de controle opnieuw uit.

## <a name="known-issues-and-limitations"></a>Bekende problemen en beperkingen

Het gebruik van teams in een gevirtualiseerde omgeving verschilt van het gebruik van teams in een niet-gevirtualiseerde omgeving. Raadpleeg [teams voor gevirtualiseerde desktop infrastructuur](/microsoftteams/teams-for-vdi#known-issues-and-limitations)voor meer informatie over de beperkingen van teams in gevirtualiseerde omgevingen.

### <a name="client-deployment-installation-and-setup"></a>Client implementatie, installatie en installatie

- Met installatie per computer worden teams op VDI niet automatisch bijgewerkt op dezelfde manier als niet-VDI-teams. Als u de client wilt bijwerken, moet u de VM-installatie kopie bijwerken door een nieuwe MSI te installeren.
- Media optimalisatie voor teams wordt alleen ondersteund voor de Windows desktop-client op computers met Windows 10.
- Het gebruik van expliciete HTTP-proxy's die zijn gedefinieerd voor een eind punt, wordt niet ondersteund.

### <a name="calls-and-meetings"></a>Aanroepen en vergaderingen

- De teams bureau blad-client in Windows virtual desktop-omgevingen bieden geen ondersteuning voor het maken van Live-gebeurtenissen, maar u kunt wel Live gebeurtenissen toevoegen. Voor Taan raden we u aan om live-gebeurtenissen te maken op basis van de [web-client van teams](https://teams.microsoft.com) in uw externe sessie.
- Oproepen of vergaderingen bieden momenteel geen ondersteuning voor het delen van toepassingen. Bureaublad sessies bieden ondersteuning voor het delen van bureau blad.
- Besturings element geven en beheer overnemen wordt momenteel niet ondersteund.
- Teams op Windows virtueel bureau blad bieden alleen ondersteuning voor één binnenkomende video-invoer per keer. Dit betekent dat wanneer iemand het scherm probeert te delen, het scherm wordt weer gegeven in plaats van het scherm van de Voorzitter van de vergadering.
- Vanwege WebRTC-beperkingen is de omzetting van inkomende en uitgaande video stromen beperkt tot 720p.
- De app teams ondersteunt geen HID-knoppen of LED-besturings elementen met andere apparaten.

Voor teams bekende problemen die niet zijn gerelateerd aan gevirtualiseerde omgevingen raadpleegt [u ondersteunings teams in uw organisatie](/microsoftteams/known-issues)

## <a name="uservoice-site"></a>UserVoice-site

Feedback geven over micro soft-teams op het virtuele Windows-bureau blad op de site van het project [UserVoice](https://microsoftteams.uservoice.com/).

## <a name="collect-teams-logs"></a>Teams-logboeken verzamelen

Als u problemen ondervindt met de teams bureau blad-app in uw Windows Virtual Desktop-omgeving, verzamelt u client Logboeken onder **% AppData% \Microsoft\Teams\logs.txt** op de host-VM.

Als u problemen ondervindt met aanroepen en vergaderingen, kunt u de logboeken van teams via web-client verzamelen met de toetscombinatie **CTRL**  +  **Alt**  +  **SHIFT**  +  **1**. Logboeken worden geschreven naar het **%userprofile%\Downloads\MSTeams Diagnostics-logboek DATE_TIME.txt** op de host-VM.

## <a name="contact-microsoft-teams-support"></a>Contact opnemen met micro soft teams ondersteuning

Ga naar het [Microsoft 365-beheer centrum](/microsoft-365/admin/contact-support-for-business-products)om contact op te nemen met de ondersteuning van micro soft teams.

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Remote Desktop Protocol eigenschappen voor een hostgroep aanpassen

Als u de eigenschappen van de Remote Desktop Protocol (RDP) van een hostgroep wilt aanpassen, zoals de ervaring op meerdere monitors of het inschakelen van microfoon-en audio-omleiding, kunt u een optimale ervaring bieden voor uw gebruikers op basis van hun behoeften.

Het inschakelen van apparaatomleiding is niet vereist wanneer teams met media optimalisatie gebruiken. Als u teams zonder media optimalisatie gebruikt, stelt u de volgende RDP-eigenschappen in om de omleiding van de microfoon en camera in te scha kelen:

- `audiocapturemode:i:1` Hiermee wordt audio-opname van het lokale apparaat ingeschakeld en worden audio toepassingen omgeleid naar de externe sessie.
- `audiomode:i:0` Audio afspelen op de lokale computer.
- `camerastoredirect:s:*` alle camera's worden omgeleid.

Ga voor meer informatie naar [aanpassen Remote Desktop Protocol eigenschappen voor een hostgroep](customize-rdp-properties.md).
