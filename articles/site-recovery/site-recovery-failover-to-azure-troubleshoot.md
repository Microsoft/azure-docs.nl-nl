---
title: Problemen oplossen met failover naar Azure-fouten | Microsoft Docs
description: In dit artikel worden manieren beschreven om veelvoorkomende fouten bij het uitvoeren van een failover naar Azure op te lossen
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: d2a0444483c382da7c54accf7dca49d097671771
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371984"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Problemen oplossen bij het uitvoeren van een failover van een virtuele VMware-machine of fysieke machine naar Azure

Mogelijk wordt een van de volgende fouten weer gegeven tijdens het uitvoeren van een failover van een virtuele machine naar Azure. Als u problemen wilt oplossen, gebruikt u de beschreven stappen voor elke fout voorwaarde.

## <a name="failover-failed-with-error-id-28031"></a>Failover is mislukt met fout-ID 28031

Site Recovery kan geen virtuele machine met een failover in azure maken. Dit kan een van de volgende oorzaken hebben:

* Er is onvoldoende quotum beschikbaar voor het maken van de virtuele machine: u kunt het beschik bare quotum controleren door naar abonnement te gaan > gebruik en quota's. U kunt een [nieuwe ondersteunings aanvraag](https://aka.ms/getazuresupport) openen om het quotum te verhogen.

* U probeert een failover uit te voeren voor virtuele machines van verschillende grootte families in dezelfde beschikbaarheidsset. Zorg ervoor dat u dezelfde grootte familie kiest voor alle virtuele machines in dezelfde beschikbaarheidsset. Wijzig grootte door naar reken-en netwerk instellingen van de virtuele machine te gaan en voer de failover vervolgens opnieuw uit.

* Er is een beleid op het abonnement waarmee wordt voor komen dat een virtuele machine wordt gemaakt. Wijzig het beleid om het maken van een virtuele machine toe te staan en voer de failover vervolgens opnieuw uit.

## <a name="failover-failed-with-error-id-28092"></a>Failover is mislukt met fout-ID 28092

Site Recovery kan geen netwerk interface maken voor de virtuele machine waarvoor een failover is uitgevoerd. Zorg ervoor dat er voldoende quota beschikbaar zijn voor het maken van netwerk interfaces in het abonnement. U kunt het beschik bare quotum controleren door te gaan naar abonnement-> gebruik en quota's. U kunt een [nieuwe ondersteunings aanvraag](https://aka.ms/getazuresupport) openen om het quotum te verhogen. Als u voldoende quota hebt, kan dit een onregelmatig probleem zijn. Probeer de bewerking opnieuw uit te voeren. Als het probleem zich blijft voordoen, zelfs na nieuwe pogingen, verlaat u een opmerking aan het einde van dit document.  

## <a name="failover-failed-with-error-id-70038"></a>Failover is mislukt met fout-ID 70038

Site Recovery kan geen klassieke virtuele machine in azure maken. Dit kan gebeuren omdat:

* Een van de bronnen, zoals een virtueel netwerk dat vereist is voor de virtuele machine die moet worden gemaakt, bestaat niet. Maak het virtuele netwerk zoals wordt beschreven in de reken-en netwerk instellingen van de virtuele machine of wijzig de instelling in een virtueel netwerk dat al bestaat en voer de failover vervolgens opnieuw uit.

## <a name="failover-failed-with-error-id-170010"></a>Failover is mislukt met fout-ID 170010

Site Recovery kan geen virtuele machine met een failover in azure maken. Dit kan gebeuren omdat een interne activiteit van Hydration is mislukt voor de on-premises virtuele machine.

Voor het beschikbaar maken van elke machine in azure, moeten sommige Stuur Programma's in de Azure-omgeving worden opgestart en moeten de services, zoals DHCP, actief zijn in de modus automatisch starten. Daarom converteert Hydration-activiteit, op het moment van de failover, het opstart type van **ATAPI-, Intelide-, storflt-, vmbus-en storvsc-Stuur Programma's** om de opstart procedure te starten. Ook wordt het opstart type van een paar Services, zoals DHCP, geconverteerd naar automatisch starten. Deze activiteit kan mislukken vanwege specifieke omgevings problemen. 

Volg de onderstaande stappen om het opstart type van Stuur Programma's voor **Windows-gast besturingssystemen** hand matig te wijzigen:

1. [Down load](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) het script no-Hydration en voer dit als volgt uit. Met dit script wordt gecontroleerd of Hydration vereist is voor de VM.

    `.\Script-no-hydration.ps1`

    Dit resulteert in het volgende resultaat als Hydration vereist is:

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    This system doesn't meet no-hydration requirement.
    ```

    Als de virtuele machine voldoet aan de niet-Hydration vereiste, geeft het script het resultaat ' dit systeem voldoet aan no-Hydration-vereiste '. In dit geval zijn alle Stuur Programma's en services in de status die is vereist door Azure en Hydration op de VM is niet vereist.

2. Voer het script no-Hydration-set als volgt uit als de virtuele machine niet voldoet aan een niet-Hydration vereiste.

    `.\Script-no-hydration.ps1 -set`
    
    Hiermee wordt het opstart type van Stuur Programma's geconverteerd en wordt het resultaat als hieronder weer gegeven:

    ```output
    REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

    Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0

    This system is now no-hydration compatible.
    ```

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Kan geen verbinding maken/RDP/SSH met de virtuele machine waarvoor een failover is uitgevoerd omdat de knop verbinding maken op de virtuele machine grijs wordt weer gegeven

Raadpleeg [de documentatie voor](../virtual-machines/troubleshooting/troubleshoot-rdp-connection.md)gedetailleerde instructies voor het oplossen van problemen met RDP.

Raadpleeg [de documentatie voor](../virtual-machines/troubleshooting/troubleshoot-ssh-connection.md)gedetailleerde instructies voor het oplossen van problemen met SSH.

Als de knop **verbinden** op de virtuele machine waarvoor een failover is uitgevoerd in azure grijs is en u niet met Azure bent verbonden via een snelle route of een site-naar-site-VPN-verbinding, dan,

1. Ga naar   >  **netwerken** van de virtuele machine en klik op de naam van de vereiste netwerk interface.  ![Scherm afbeelding toont de netwerk pagina voor een virtuele machine waarvoor de netwerk interface naam is geselecteerd.](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Navigeer naar **IP-configuraties** en klik vervolgens op het veld naam van de vereiste IP-configuratie. ![Scherm afbeelding toont de pagina I P-configuraties voor de netwerk interface waarvoor de configuratie naam I P is geselecteerd.](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Als u openbaar IP-adres wilt inschakelen, klikt u op **inschakelen**. ![IP inschakelen](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Klik op **vereiste instellingen configureren**  >  **nieuwe maken**. ![Nieuwe maken](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Voer de naam van het open bare adres in, kies de standaard opties voor **SKU** en **toewijzing** en klik vervolgens op **OK**.
6. Klik nu op **Opslaan** om de gemaakte wijzigingen op te slaan.
7. Sluit de deel Vensters en navigeer naar **overzicht** sectie van de virtuele machine om verbinding te maken/RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Kan geen verbinding maken/RDP/SSH: er is een VM-verbinding beschikbaar

Als de knop **verbinden** op de virtuele machine waarvoor een failover is uitgevoerd in azure beschikbaar is (niet grijs), controleert u de **Diagnostische gegevens over opstarten** op de VM en controleert u op fouten zoals vermeld in [dit artikel](../virtual-machines/troubleshooting/boot-diagnostics.md).

1. Als de virtuele machine niet is gestart, kunt u een failover uitvoeren naar een ouder herstel punt.
2. Als de toepassing in de virtuele machine niet actief is, kunt u een failover uitvoeren naar een toepassings consistent herstel punt.
3. Als de virtuele machine lid is van een domein, zorgt u ervoor dat de domein controller nauw keurig werkt. U kunt dit doen door de onderstaande stappen te volgen:

    a. Maak een nieuwe virtuele machine in hetzelfde netwerk.

    b.  Zorg ervoor dat deze kan deel nemen aan hetzelfde domein waarop de virtuele machine waarvoor een failover is uitgevoerd, naar verwachting kan worden uitgevoerd.

    c. Als de domein controller **niet** nauw keurig werkt, probeert u zich aan te melden bij de virtuele machine waarvoor een failover is uitgevoerd met behulp van een lokaal beheerders account.
4. Als u een aangepaste DNS-server gebruikt, moet u ervoor zorgen dat deze bereikbaar is. U kunt dit doen door de onderstaande stappen te volgen:

    a. Maak een nieuwe virtuele machine in hetzelfde netwerk en

    b. Controleren of de virtuele machine naam omzetting kan uitvoeren met de aangepaste DNS-server

>[!Note]
>Als een andere instelling dan de diagnostische gegevens over opstarten is ingeschakeld, moet de Azure VM-agent op de virtuele machine zijn geïnstalleerd voordat de failover

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>Kan seriële console niet openen na een failover van een UEFI-computer naar Azure

Als u verbinding kunt maken met de computer met behulp van RDP, maar u kunt geen seriële console openen, volgt u de onderstaande stappen:

* Als het besturings systeem van de machine Red Hat of Oracle Linux 7. */8.0 is, voert u de volgende opdracht uit op de failover Azure-VM met hoofd machtigingen. Start de VM opnieuw op na de opdracht.

  ```console
  grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
  ```

* Als het besturings systeem van de machine CentOS 7. * is, voert u de volgende opdracht uit op de failover Azure VM met hoofd machtigingen. Start de VM opnieuw op na de opdracht.

  ```console
  grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg
  ```

## <a name="unexpected-shutdown-message-event-id-6008"></a>Onverwacht afsluit bericht (gebeurtenis-ID 6008)

Als er een onverwacht afsluit bericht wordt weer gegeven op de herstelde virtuele machine, geeft dit aan dat er geen VM-afsluit status is vastgelegd in het herstel punt dat wordt gebruikt voor failover. Dit gebeurt wanneer u herstelt naar een punt wanneer de virtuele machine niet volledig is afgesloten.

Dit is doorgaans geen oorzaak van bezorgdheid en kan meestal worden genegeerd voor niet-geplande failovers. Als de failover is gepland, moet u ervoor zorgen dat de virtuele machine correct wordt afgesloten voordat de failover wordt uitgevoerd en voldoende tijd hebben om on-premises replicatie gegevens naar Azure te verzenden. Gebruik vervolgens de **laatste** optie op het [failover-scherm](site-recovery-failover.md#run-a-failover) zodat alle in behandeling zijnde gegevens in Azure worden verwerkt in een herstel punt, dat vervolgens wordt gebruikt voor VM-failover.

## <a name="unable-to-select-the-datastore"></a>Kan de gegevens opslag niet selecteren

Dit probleem wordt aangegeven wanneer het gegevens archief in azure niet kan worden weer gegeven bij het opnieuw beveiligen van de virtuele machine die een failover heeft ondergaan. Dit komt doordat het hoofd doel niet wordt herkend als een virtuele machine onder vCenter toegevoegd aan Azure Site Recovery.

Voor meer informatie over het opnieuw beveiligen van een virtuele machine raadpleegt [u machines opnieuw beveiligen en failback naar een on-premises site na een failover naar Azure](vmware-azure-reprotect.md).

Het probleem oplossen:

Maak het hoofd doel hand matig in de vCenter die uw bron machine beheert. De gegevens opslag is beschikbaar na de volgende vCenter-bewerkingen voor het maken en vernieuwen van infrastructuur resources.

> [!Note]
> 
> Het volt ooien van de detectie-en vernieuwings infrastructuur bewerkingen kan tot wel 30 minuten duren. 

## <a name="linux-master-target-registration-with-cs-fails-with-a-tls-error-35"></a>De Linux-hoofddoel registratie met CS mislukt met een TLS-fout 35 

De Azure Site Recovery-hoofddoel registratie bij de configuratie server mislukt omdat de geverifieerde proxy is ingeschakeld op het hoofd doel. 
 
Deze fout wordt aangegeven door de volgende teken reeksen in het installatie logboek: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

Het probleem oplossen:
 
1. Open een opdracht prompt op de configuratie Server-VM en controleer de proxy-instellingen met behulp van de volgende opdrachten:

    kat/etc/Environment ECHO $http _proxy ECHO $https _proxy 

2. Als de uitvoer van de vorige opdrachten laat zien dat de instellingen http_proxy of https_proxy zijn gedefinieerd, gebruikt u een van de volgende methoden om de hoofd doel communicatie met de configuratie server te deblokkeren:
   
   - Down load het [PsExec-hulp programma](/sysinternals/downloads/psexec).
   - Gebruik het hulp programma om toegang te krijgen tot de context van het systeem gebruikers en te bepalen of het proxy adres is geconfigureerd. 
   - Als de proxy is geconfigureerd, opent u Internet Explorer in een systeem gebruikers context met behulp van het hulp programma PsExec.
  
     **PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Om ervoor te zorgen dat de hoofddoel server kan communiceren met de configuratie server:
  
     - Wijzig de proxy-instellingen in Internet Explorer om het IP-adres van de hoofddoel server via de proxy over te slaan.   
     of
     - Schakel de proxy op de hoofddoel server uit. 


## <a name="next-steps"></a>Volgende stappen
- Problemen met de [RDP-verbinding met de Windows VM](../virtual-machines/troubleshooting/troubleshoot-rdp-connection.md) oplossen
- Problemen met [SSH-verbinding met Linux VM](../virtual-machines/troubleshooting/detailed-troubleshoot-ssh-connection.md) oplossen

Als u meer hulp nodig hebt, plaatst u uw query op [micro soft Q&een vraag pagina voor site Recovery](/answers/topics/azure-site-recovery.html) of verlaat u een opmerking aan het einde van dit document. We hebben een actieve community die u kan helpen.