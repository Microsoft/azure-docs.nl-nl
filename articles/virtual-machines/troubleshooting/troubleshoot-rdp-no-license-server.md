---
title: De Extern bureaublad licentie server is niet beschikbaar wanneer u verbinding maakt met een Azure VM | Microsoft Docs
description: Meer informatie over het oplossen van problemen met RDP-problemen omdat er geen Extern bureaublad licentie server beschikbaar is | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 6b5864d40d2a4d8f8d6cf404df29f909a73f04e2
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832040"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Extern bureaublad licentie server is niet beschikbaar wanneer u verbinding maakt met een Azure VM

Dit artikel helpt u bij het oplossen van het probleem wanneer u geen verbinding kunt maken met een virtuele machine van Azure (VM), omdat er geen Extern bureaublad licentie server beschikbaar is om een licentie te bieden.

## <a name="symptoms"></a>Symptomen

Wanneer u probeert verbinding te maken met een virtuele machine (VM), treden de volgende scenario's op:

- In de VM-scherm afbeelding ziet u dat het besturings systeem volledig is geladen en wordt gewacht op referenties.
- U ontvangt de volgende fout berichten wanneer u een RDP-verbinding (Microsoft Extern bureaublad Protocol) wilt maken:

  - De externe sessie is beëindigd omdat er geen Extern bureaublad licentie servers beschikbaar zijn om een licentie te bieden.

  - Er is geen Extern bureaublad licentie server beschikbaar. Extern bureaublad-services werkt niet meer omdat de respijt periode van deze computer niet is bereikt en geen contact heeft gemaakt met ten minste een geldige Windows Server 2008-licentie server. Selecteer dit bericht om de configuratie van RD Session Host-servers te openen voor het gebruik van licentie controle.

U kunt echter wel verbinding maken met de virtuele machine met behulp van een beheer sessie:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Oorzaak

Dit probleem doet zich voor als een Extern bureaublad licentie server niet beschikbaar is om een licentie te bieden om een externe sessie te starten. Dit kan worden veroorzaakt door verschillende scenario's, zelfs als een Extern bureaublad sessiehost is ingesteld op de virtuele machine:

- Er is nooit een Extern bureaublad-licentie functie in de omgeving en de respijt periode is 180 dagen.
- Er is een Extern bureaublad licentie in de omgeving geïnstalleerd, maar deze is nooit geactiveerd.
- Een Extern bureaublad licentie in de omgeving heeft geen Client Access Licenses (Cal's) die zijn geïnjecteerd voor het instellen van de verbinding.
- Er is een Extern bureaublad licentie in de omgeving geïnstalleerd. Er zijn beschik bare Cal's, maar deze zijn niet goed geconfigureerd.
- Een Extern bureaublad licentie heeft Cal's en is geactiveerd. Er zijn echter andere problemen op de Extern bureaublad licentie server die voor komen dat deze licenties in de omgeving bieden.

## <a name="solution"></a>Oplossing

U kunt dit probleem oplossen door [een back-up te maken van de besturingssysteem schijf en de](../windows/snapshot-copy-managed-disk.md) volgende stappen uit te voeren:

1. Verbinding maken met de virtuele machine met behulp van een beheer sessie:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Als u geen verbinding kunt maken met de virtuele machine met behulp van een beheer sessie, kunt u de VM- [seriële console op Azure](serial-console-windows.md) als volgt gebruiken om toegang te krijgen tot de vm's:

    1. Open de seriële console door ondersteuning te selecteren **& probleem oplossing**  >  **seriële console (preview)**. Als de functie is ingeschakeld op de virtuele machine, kunt u verbinding maken met de virtuele machine.

    2. Een nieuw kanaal maken voor een CMD-exemplaar. Voer **cmd** in om het kanaal te starten en de kanaal naam op te halen.

    3. Schakel over naar het kanaal waarop het CMD-exemplaar wordt uitgevoerd. In dit geval moet het kanaal 1 zijn:

       ```
       ch -si 1
       ```

    4. Selecteer opnieuw **invoeren** en voer een geldige gebruikers naam en wacht woord, lokale of domein-id in voor de virtuele machine.

2. Controleer of er een Extern bureaublad Session Host-rol is ingeschakeld op de VM. Als de functie is ingeschakeld, controleert u of deze goed werkt. Open een met verhoogde bevoegdheden een CMD-exemplaar en voer de volgende stappen uit:

    1. Gebruik de volgende opdracht om de status van de Extern bureaublad Session Host-functie te controleren:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Als met deze opdracht de waarde 0 wordt geretourneerd, betekent dit dat de rol is uitgeschakeld en dat u naar stap 3 kunt gaan.

    2. Gebruik de volgende opdracht om het beleid te controleren en indien nodig opnieuw te configureren:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode

        reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Als de waarde voor **LicensingMode** is ingesteld op een andere waarde dan 4, per gebruiker, stelt u de waarde in op 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Als de waarde voor **SpecifiedLicenseServers** niet bestaat of onjuiste licentie server gegevens heeft, wijzigt u deze als volgt:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Nadat u wijzigingen in het REGI ster hebt aangebracht, start u de VM opnieuw op.

    4. Als u geen Cal's hebt, verwijdert u de functie sessiehost van Extern bureaublad. Vervolgens wordt de RDP-configuratie teruggezet naar normaal. Er zijn slechts twee gelijktijdige RDP-verbindingen met de virtuele machine toegestaan:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Als de virtuele machine de Extern bureaublad-licentie functie heeft en niet wordt gebruikt, kunt u die rol ook verwijderen:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Zorg ervoor dat de virtuele machine verbinding kan maken met de Extern bureaublad licentie server. U kunt de connectiviteit testen op poort 135 tussen de virtuele machine en de licentie server: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Als er geen Extern bureaublad licentie server in de omgeving is en u er een wilt maken, kunt u [een extern bureaublad Licensing-functie service installeren](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc731765(v=ws.11)). [Configureer vervolgens de RDS-licentie verlening](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383).

4. Als een Extern bureaublad licentie server is geconfigureerd en in orde is, moet u ervoor zorgen dat de Extern bureaublad licentie server wordt geactiveerd met Cal's.

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u nog hulp nodig hebt, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om het probleem op te lossen.
