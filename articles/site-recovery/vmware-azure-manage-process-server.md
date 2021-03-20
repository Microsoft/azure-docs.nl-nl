---
title: Een proces server voor virtuele VMware-machines en fysieke server herstel na nood gevallen beheren in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u een proces server beheert voor herstel na nood gevallen van virtuele VMware-machines of fysieke servers met behulp van Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: a547a874c42d06d8453b154847561d8b5f0dabb8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019180"
---
# <a name="manage-process-servers"></a>Processervers beheren

In dit artikel worden algemene taken voor het beheren van de Site Recovery proces server beschreven.

De proces server wordt gebruikt om replicatie gegevens te ontvangen, te optimaliseren en te verzenden naar Azure. Er wordt ook een push-installatie uitgevoerd van de Mobility-service op VMware-Vm's en fysieke servers die u wilt repliceren, en voert automatische detectie van on-premises machines uit. Voor het repliceren van on-premises virtuele VMware-machines of fysieke servers naar Azure, wordt de proces server standaard geïnstalleerd op de computer met de configuratie server. 

- Voor grote implementaties hebt u mogelijk extra on-premises proces servers nodig om capaciteit te schalen.
- Voor failback van Azure naar on-premises moet u een tijdelijke proces server instellen in Azure. U kunt deze virtuele machine verwijderen wanneer de failback is voltooid. 

Meer informatie over de proces server.


## <a name="upgrade-a-process-server"></a>Een proces server bijwerken

Wanneer u een proces server on-premises implementeert of als een Azure-VM voor failback, wordt de meest recente versie van de proces server geïnstalleerd. De versies van de Site Recovery teams kunnen regel matig worden uitgebreid en uitgebreid. Wij raden u aan de proces servers up-to-date te houden. U kunt als volgt een upgrade uitvoeren voor een proces server:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Vm's verplaatsen om de belasting van de proces server te verdelen

U kunt als volgt de belasting verdelen door Vm's tussen twee proces servers te verplaatsen:

1. Klik in de kluis onder **beheren** op **site Recovery-infra structuur**. Klik onder **voor VMware & fysieke machines** op **configuratie servers**.
2. Klik op de configuratie server waarmee de proces servers zijn geregistreerd.
3. Klik op de proces server waarvoor u het verkeer wilt verdelen.

    ![Scherm afbeelding toont een proces server waarvoor u verkeer kunt verdelen.](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Klik op **taak verdeling**, selecteer de doel proces server waarnaar u machines wilt verplaatsen. Klik vervolgens op **OK**

    ![Scherm afbeelding toont het deel venster taak verdeling met de optie doel proces server selecteren geselecteerd.](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Klik op **machines selecteren** en kies de computers die u van de huidige naar de doel proces server wilt verplaatsen. Details van de gemiddelde gegevens wijziging worden voor elke virtuele machine weer gegeven. Klik vervolgens op **OK**. 
3. Controleer in de kluis de voortgang van de taak onder **bewaking**  >  **site Recovery taken**.

Het duurt ongeveer 15 minuten voordat wijzigingen worden weer gegeven in de portal. [Vernieuw de configuratie server](vmware-azure-manage-configuration-server.md#refresh-configuration-server)voor een snellere werking.

## <a name="switch-an-entire-workload-to-another-process-server"></a>Een hele werk belasting overschakelen naar een andere proces server

Verplaats de hele werk belasting die door een proces server wordt verwerkt, als volgt naar een andere proces server:

1. Klik in de kluis onder **beheren** op **site Recovery-infra structuur**. Klik onder **voor VMware & fysieke machines** op **configuratie servers**.
2. Klik op de configuratie server waarmee de proces servers zijn geregistreerd.
3. Klik op de proces server van waaruit u de werk belasting wilt wijzigen.
4. Klik op **Switch**, selecteer de doel proces server waarnaar u de werk belasting wilt verplaatsen. Klik vervolgens op **OK**

    ![Scherm afbeelding toont het deel venster doel proces server selecteren.](media/vmware-azure-manage-process-server/Switch.PNG)

5. Controleer in de kluis de voortgang van de taak onder **bewaking**  >  **site Recovery taken**.

Het duurt ongeveer 15 minuten voordat wijzigingen worden weer gegeven in de portal. [Vernieuw de configuratie server](vmware-azure-manage-configuration-server.md#refresh-configuration-server)voor een snellere werking.

## <a name="register-a-master-target-server"></a>Een hoofddoel server registreren

De hoofddoel server bevindt zich op de configuratie server en scale-out proces servers. Het moet zijn geregistreerd bij de configuratie server. Als er een fout is opgetreden in deze registratie, kan dit van invloed zijn op de status van beveiligde items. Als u de Master doel server wilt registreren bij de configuratie server, meldt u zich aan bij de specifieke configuratie server/scale-out proces server waarop de registratie is vereist. Navigeer naar map **%PROGRAMDATA%\ASR\Agent** en voer het volgende uit op de opdracht prompt van de Administrator.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>Een proces server opnieuw registreren

Registreer een proces server die on-premises of op een virtuele machine van Azure wordt uitgevoerd met de configuratie server als volgt opnieuw:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Nadat u de instellingen hebt opgeslagen, gaat u als volgt te werk:

1. Open een beheerders opdracht prompt op de proces server.
2. Blader naar map **%PROGRAMDATA%\ASR\Agent** en voer de volgende opdracht uit:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Proxy-instellingen voor een on-premises proces server wijzigen

Als een on-premises proces server een proxy gebruikt om verbinding te maken met Azure, kunt u de proxy-instellingen als volgt wijzigen:

1. Meld u aan bij de computer met de proces server. 
2. Open een Power shell-opdracht venster voor beheerders en voer de volgende opdracht uit:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Blader naar map **%PROGRAMDATA%\ASR\Agent** en voer deze opdracht uit:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Een proces server verwijderen

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Mappen uitsluiten van antivirus software

Als antivirus software wordt uitgevoerd op een scale-out proces server (of hoofddoel server), sluit u de volgende mappen uit van anti-virus bewerkingen:


- C:\Program Files\Microsoft Azure Recovery Services-agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Installatie directory van de proces server. Bijvoorbeeld: C:\Program Files (x86) \Microsoft Azure Site Recovery