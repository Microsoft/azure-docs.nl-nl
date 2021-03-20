---
title: Bron machines voorbereiden voor het installeren van de Mobility-service via een push-installatie voor nood herstel van virtuele VMware-machines en fysieke servers naar Azure | Microsoft Docs
description: Informatie over het voorbereiden van uw server voor het installeren van Mobility agent via push-installatie voor nood herstel van virtuele VMware-machines en fysieke servers naar Azure met behulp van de Azure Site Recovery-service.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 78fddb5b4512883f8e78d6ed53f6e3dbbeba0e4f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90524994"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Bron machine voorbereiden voor de push-installatie van de Mobility-agent

Wanneer u herstel na nood gevallen instelt voor virtuele VMware-machines en fysieke servers met behulp van [Azure site Recovery](site-recovery-overview.md), installeert u de [site Recovery Mobility-service](vmware-physical-mobility-service-overview.md) op elke on-premises VMware-VM en fysieke server.  De Mobility-service legt gegevens op de computer vast en stuurt deze door naar de Site Recovery-proces server.

## <a name="install-on-windows-machine"></a>Installeren op Windows-machine

Ga als volgt te werk op elke Windows-computer die u wilt beveiligen:

1. Zorg ervoor dat er een netwerk verbinding is tussen de computer en de proces server. Als u geen afzonderlijke proces server hebt ingesteld, wordt deze standaard uitgevoerd op de configuratie server.
1. Maak een account dat op de processerver kan worden gebruikt voor toegang tot de computer. Het account moet beheerders rechten, lokaal of domein, hebben. Gebruik dit account alleen voor de push-installatie en voor agent-updates.
2. Als u geen domein account gebruikt, schakelt u toegangs beheer voor externe gebruikers op de lokale computer als volgt uit:
    - Voeg onder HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System register sleutel een nieuwe DWORD toe: **LocalAccountTokenFilterPolicy**. Stel de waarde in op **1**.
    -  Voer de volgende opdracht uit vanaf een opdracht prompt:
    
       ```
       REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f
       ```

3. Selecteer in Windows Firewall op de computer die u wilt beveiligen, **een app of onderdeel toestaan via de firewall**. Schakel **Bestands-en printer deling** en **Windows Management Instrumentation (WMI)** in. Voor computers die tot een domein behoren, kunt u de firewall instellingen configureren met behulp van een groepsbeleid-object (GPO).

   ![Firewallinstellingen](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Voeg het account toe dat u hebt gemaakt in CSPSConfigtool. Hiertoe meldt u zich aan bij uw configuratie server.
5. Open **cspsconfigtool.exe**. Het is beschikbaar als snelkoppeling op het bureau blad en in de map%ProgramData%\ASR\home\svsystems\bin.
6. Klik op het tabblad **accounts beheren** op **account toevoegen**.
7. Voeg het account toe dat u hebt gemaakt.
8. Voer de referenties in die u gebruikt wanneer u replicatie voor een computer inschakelt.

## <a name="install-on-linux-machine"></a>Installeren op Linux-computer

Ga als volgt te werk op elke Linux-computer die u wilt beveiligen:

1. Zorg ervoor dat er een netwerk verbinding is tussen de Linux-computer en de proces server.
2. Maak een account dat op de processerver kan worden gebruikt voor toegang tot de computer. Het account moet een **rootgebruiker** zijn op de Linux-bronserver. Gebruik dit account alleen voor de push-installatie en voor updates.
3. Controleer of het bestand /etc/hosts op de Linux-bronserver vermeldingen bevat die de lokale hostnaam toewijzen aan IP-adressen die zijn gekoppeld aan alle netwerkadapters.
4. Installeer de meest recente openssh, openssh-server en openssl-pakketten op de computer die u wilt repliceren.
5. Zorg ervoor dat SSH (Secure Shell) is ingeschakeld en wordt uitgevoerd op poort 22.
4. Schakel het SFTP-subsysteem en de wachtwoord verificatie in het sshd_config bestand in. Als u dit wilt doen, meldt u zich aan als **root**.
5. Zoek in het bestand **/etc/ssh/sshd_config** de regel die begint met **PasswordAuthentication**.
6. Verwijder de opmerking bij de regel en wijzig de waarde in **Ja**.
7. Zoek de regel die begint met **subsysteem** en verwijder de opmerking over de regel.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Start de service **sshd** opnieuw.
9. Voeg het account toe dat u hebt gemaakt in CSPSConfigtool. Hiertoe meldt u zich aan bij uw configuratie server.
10. Open **cspsconfigtool.exe**. Het is beschikbaar als snelkoppeling op het bureau blad en in de map%ProgramData%\home\svsystems\bin.
11. Klik op het tabblad **accounts beheren** op **account toevoegen**.
12. Voeg het account toe dat u hebt gemaakt.
13. Voer de referenties in die u gebruikt wanneer u replicatie voor een computer inschakelt.
1. Extra stap voor het bijwerken of beveiligen van SUSE Linux Enterprise Server 11 SP3-of RHEL 5-of CentOS 5-of Debian 7-machines. [Zorg ervoor dat de meest recente versie beschikbaar is op de configuratie server](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server).

## <a name="anti-virus-on-replicated-machines"></a>Anti-virus op gerepliceerde computers

Als de computers die u wilt repliceren, actieve antivirus software hebben, moet u de installatiemap van de Mobility-service uitsluiten van anti-virus bewerkingen (*C:\ProgramData\ASR\agent*). Dit zorgt ervoor dat de replicatie werkt zoals verwacht.

## <a name="next-steps"></a>Volgende stappen

Nadat de Mobility-service is geïnstalleerd, selecteert u in de Azure Portal **+ repliceren** om te beginnen met het beveiligen van deze vm's. Meer informatie over het inschakelen van replicatie voor [virtuele VMware-machines](vmware-azure-enable-replication.md) en [fysieke servers](physical-azure-disaster-recovery.md#enable-replication).


