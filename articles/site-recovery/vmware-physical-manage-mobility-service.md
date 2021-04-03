---
title: De Mobility-agent voor VMware/fysieke servers beheren met Azure Site Recovery
description: Beheer de Mobility Service-agent voor nood herstel van virtuele VMware-machines en fysieke servers naar Azure met behulp van de Azure Site Recovery-service.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: d921bddf90c415cb244e2cc9ad98354392a537ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530145"
---
# <a name="manage-the-mobility-agent"></a>De Mobility-agent beheren 

U kunt Mobility agent op uw server instellen wanneer u Azure Site Recovery gebruikt voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers naar Azure. Mobiliteits agent coördineert de communicatie tussen uw beveiligde computer, configuratie server/scale-out proces server en beheert gegevens replicatie. In dit artikel vindt u een overzicht van algemene taken voor het beheren van de Mobility-agent nadat deze is geïmplementeerd.

>[!TIP]
>Raadpleeg de [volgende](vmware-physical-mobility-service-overview.md#locate-installer-files)informatie om het installatie programma voor een specifiek besturings systeem/Linux distributie te downloaden. Als u automatisch wilt bijwerken vanuit de portal, hoeft u het installatie programma niet te downloaden. [ASR haalt automatisch het installatie programma op van de configuratie server en werkt de agent bij](#update-mobility-service-from-azure-portal).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>De Mobility-service bijwerken vanuit Azure Portal

1. Voordat u begint, moet u ervoor zorgen dat de configuratie server, scale-out proces servers en alle Master doel servers die deel uitmaken van uw implementatie, worden bijgewerkt voordat u de Mobility-service op beveiligde computers bijwerkt.
    1. Vanaf 9,36-versie gaat u voor SUSE Linux Enterprise Server 11 SP3, RHEL 5, CentOS 5, Debian 7 controleren of het meest recente installatie programma [beschikbaar is op de configuratie server en de scale-out proces server](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server).
1. Open de kluis > **gerepliceerde items** in de portal.
1. Als de configuratie server de meest recente versie is, wordt er een melding weer gegeven dat de nieuwe site Recovery-agent update beschikbaar is. Klik om te installeren. "

     ![Venster gerepliceerde items](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Klik op de melding en selecteer bij **agent update** de computers waarop u de Mobility-service wilt bijwerken. Klik vervolgens op **OK**.

     ![VM-lijst van gerepliceerde items](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. De taak Mobility service bijwerken wordt gestart voor elk van de geselecteerde machines. De Mobility-agent is bijgewerkt naar de versie van de configuratie server. Als de configuratie server zich bijvoorbeeld op versie 9,33 bevindt, wordt de Mobility-agent op een beveiligde virtuele machine ook bijgewerkt naar 9,33-versie.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Mobility service bijwerken met het Power shell-script op Windows Server

Voordat u begint, moet u ervoor zorgen dat de configuratie server, scale-out proces servers en alle Master doel servers die deel uitmaken van uw implementatie, worden bijgewerkt voordat u de Mobility-service op beveiligde computers bijwerkt.

Het volgende script gebruiken voor het bijwerken van de Mobility-service op een server met de Power shell-cmdlet

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>Mobility service hand matig bijwerken op elke beveiligde server

1. Voordat u begint, moet u ervoor zorgen dat de configuratie server, scale-out proces servers en alle Master doel servers die deel uitmaken van uw implementatie, worden bijgewerkt voordat u de Mobility-service op beveiligde computers bijwerkt.

2. [Zoek het installatie programma van de agent](vmware-physical-mobility-service-overview.md#locate-installer-files) op basis van het besturings systeem van de server.

>[!IMPORTANT]
> Als u Azure IaaS VM van de ene Azure-regio naar de andere repliceert, gebruikt u deze methode niet. Raadpleeg [onze richt lijnen](azure-to-azure-autoupdate.md) voor meer informatie over alle beschik bare opties.

3. Kopieer het installatie bestand op de beveiligde computer en voer het uit om de Mobility-agent bij te werken.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Update account dat wordt gebruikt voor de push-installatie van de Mobility-service

Wanneer u Site Recovery hebt geïmplementeerd, kunt u een push-installatie van de Mobility-service inschakelen door een account op te geven dat door de Site Recovery proces server wordt gebruikt voor toegang tot de computers en de service te installeren wanneer replicatie is ingeschakeld voor de machine. Als u de referenties voor dit account wilt bijwerken, volgt u [deze instructies](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Mobility-service verwijderen

### <a name="on-a-windows-machine"></a>Op een Windows-computer

Verwijder de gebruikers interface of een opdracht prompt.

- In de **gebruikers interface**: Selecteer in het configuratie scherm van de computer **Program ma's**. Selecteer **Microsoft Azure site Recovery Mobility service/Master doel server**  >  **verwijderen**.
- **Vanaf een opdracht prompt**: Open een opdracht prompt venster als beheerder op de computer. Voer de volgende opdracht uit: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>Op een Linux-computer
1. Meld u op de Linux-computer aan als een **hoofd** gebruiker.
2. Ga in een terminal naar/usr/local/ASR
3. Voer de volgende opdracht uit:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Site Recovery VSS-provider installeren op de bron machine

Azure Site Recovery VSS-provider is vereist op de bron machine om toepassings consistentie punten te genereren. Als de installatie van de provider niet is geslaagd via de push-installatie, volgt u de onderstaande richt lijnen om deze hand matig te installeren.

1. Open de admin CMD-venster.
2. Navigeer naar de installatie locatie van de Mobility-service. (Bijvoorbeeld: C:\Program Files (x86) \Microsoft Azure site Recovery\agent.)
3. Voer het script InMageVSSProvider_Uninstall. cmd uit. Hiermee wordt de service verwijderd als deze al bestaat.
4. Voer het script InMageVSSProvider_Install. cmd uit om de VSS-provider hand matig te installeren.

## <a name="next-steps"></a>Volgende stappen

- [Herstel na nood geval instellen voor VMware-Vm's](vmware-azure-tutorial.md)
- [Herstel na nood geval instellen voor fysieke servers](physical-azure-disaster-recovery.md)
