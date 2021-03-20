---
title: Updates en onderdeel upgrades in Azure Site Recovery
description: Hierin wordt een overzicht gegeven van Azure Site Recovery service-updates en onderdeel upgrades.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: ramamill
ms.openlocfilehash: a1ea8b6fb9800d796670161288be0d86ce6ffc42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89424936"
---
# <a name="service-updates-in-site-recovery"></a>Service-updates in Site Recovery

In dit artikel vindt u een overzicht van [Azure site Recovery](site-recovery-overview.md) updates en wordt beschreven hoe u site Recovery-onderdelen bijwerkt.

Site Recovery service-updates regel matig te publiceren. Updates bevatten nieuwe functies, ondersteuning voor verbeteringen, onderdeel updates en oplossingen voor oplossingen. Als u de nieuwste functies en oplossingen wilt gebruiken, raden we u aan om de nieuwste versies van Site Recovery-onderdelen uit te voeren. 
 
 
## <a name="updates-support"></a>Ondersteuning voor updates

### <a name="support-statement-for-azure-site-recovery"></a>Ondersteunings verklaring voor Azure Site Recovery

U wordt aangeraden altijd een upgrade uit te voeren naar de nieuwste versie van het onderdeel:

**Bij elke nieuwe versie ' n ' van een Azure site Recovery onderdeel dat wordt uitgebracht, worden alle versies onder N-4 als niet-ondersteund beschouwd**. 

> [!IMPORTANT]
> Officiële ondersteuning is voor het upgraden van > N-4-versie naar een N-versie. Als u bijvoorbeeld werkt met N-6, moet u eerst upgraden naar N-4 en vervolgens upgraden naar N.


### <a name="links-to-currently-supported-update-rollups"></a>Koppelingen naar momenteel ondersteunde update pakketten

 Bekijk het meest recente update pakket (versie N) in [dit artikel](site-recovery-whats-new.md). Houd er rekening mee dat Site Recovery ondersteuning biedt voor N-4-versies.



## <a name="component-expiry"></a>Verval datum onderdeel

Site Recovery waarschuwt u voor verlopen onderdelen (of bijna verlopen) via e-mail (als u zich hebt geabonneerd op e-mail meldingen) of in het kluis dashboard in de portal.

- Wanneer er updates beschikbaar zijn, wordt er in de infrastructuur weergave voor uw scenario in de portal ook een knop **Update beschikbaar** weer gegeven naast het onderdeel. Met deze knop wordt u omgeleid naar een koppeling voor het downloaden van de nieuwste versie van het onderdeel.
-  Waarschuwingen voor het dash board zijn niet beschikbaar als u virtuele Hyper-V-machines repliceert. 

E-mail meldingen worden als volgt verzonden.

**Tijd** | **Frequentie**
--- | ---
60 dagen voor het verval van het onderdeel | Eenmaal per twee weken
Volgende 53 dagen | Eenmaal per week
Afgelopen 7 dagen | Eenmaal per dag
Na verloop datum | Eenmaal per twee weken


### <a name="upgrading-outside-official-support"></a>Upgrade uitvoeren buiten officiële ondersteuning

Als het verschil tussen de versie van uw onderdeel en de meest recente release versie meer dan vier zijn, wordt dit beschouwd als niet-ondersteund. In dit geval moet u de volgende upgrade uitvoeren: 

1. Voer een upgrade uit van het momenteel geïnstalleerde onderdeel naar uw huidige versie plus vier. Als uw versie bijvoorbeeld 9,16 is, voert u een upgrade uit naar 9,20.
2. Voer vervolgens een upgrade uit naar de volgende compatibele versie. In het voor beeld, na de upgrade van 9,16 naar 9,20, voert u een upgrade uit naar 9,24. 

Volg hetzelfde proces voor alle relevante onderdelen.

### <a name="support-for-latest-operating-systemskernels"></a>Ondersteuning voor de nieuwste besturings systemen/kernels

> [!NOTE]
> Als u een onderhouds venster hebt gepland en de computer opnieuw wordt opgestart, wordt u aangeraden eerst Site Recovery onderdelen bij te werken en vervolgens door te gaan met de overige geplande activiteiten in het onderhouds venster.

1. Controleer voordat u de versies van het besturings systeem/de kernel bijwerkt of de doel versie wordt ondersteund Site Recovery. 

    - Ondersteuning voor [Azure VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) .
    - Ondersteuning voor [VMware/fysieke server](vmware-physical-azure-support-matrix.md#replicated-machines)
    - Ondersteuning voor [Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) .
2. Bekijk de [beschik bare updates](site-recovery-whats-new.md) voor informatie over wat u wilt upgraden.
3. Voer een upgrade uit naar de nieuwste versie van Site Recovery.
4. Voer een upgrade uit voor het besturings systeem/de kernel naar de vereiste versies.
5. Voltooid.


Dit proces zorgt ervoor dat het besturings systeem/de kernel van de machine wordt bijgewerkt naar de nieuwste versie en dat de meest recente Site Recovery wijzigingen die nodig zijn om de nieuwe versie te ondersteunen, zijn geladen op de computer.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Herstel na noodgevallen van virtuele Azure-machines naar Azure

In dit scenario wordt u ten zeerste aangeraden [Automatische updates in te scha kelen](azure-to-azure-autoupdate.md). U kunt Site Recovery als volgt toestaan om updates te beheren:

- Tijdens het inschakelen van het replicatie proces.
- Door de instellingen voor de extensie-update in de kluis in te stellen.

Als u updates hand matig wilt beheren, kunt u een van de volgende opties kiezen:

1. Wanneer een nieuwe agent update beschikbaar is, geeft Site Recovery een melding in de kluis aan de bovenkant van de pagina. Klik in de kluis > **gerepliceerde items** op deze melding aan de bovenkant van het scherm: 
    
    **Er is een nieuwe Site Recovery update voor de replicatie agent beschikbaar. Klik om te installeren->** <br/><br/>Selecteer de Vm's waarvoor u de update wilt Toep assen en klik vervolgens op **OK**.

2. Op de pagina overzicht van VM nood herstel vindt u het veld ' agent status ', waarmee ' essentiële upgrade ' wordt weer gegeven als de agent verloopt. Klik erop en volg de volgende instructies om de virtuele machine hand matig bij te werken.

## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>Herstel na nood geval voor virtuele VMware-machines en fysieke servers naar Azure

1. Op basis van uw huidige versie en de [ondersteunings verklaring](#support-statement-for-azure-site-recovery)installeert u de update eerst op de on-premises configuratie server met behulp van [deze instructies](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). 
2. Als u scale-out proces servers hebt, moet u deze nu bijwerken met behulp van [deze instructies](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Raadpleeg [Dit](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) artikel voor het bijwerken van de Mobility-agent op elke beveiligde computer.

### <a name="reboot-after-mobility-service-upgrade"></a>Opnieuw opstarten na de upgrade van de Mobility-service

Een herstart wordt aanbevolen na elke upgrade van de Mobility-service om ervoor te zorgen dat alle meest recente wijzigingen worden geladen op de bron machine.

Opnieuw opstarten is niet verplicht, tenzij het verschil tussen de agent versie tijdens de laatste keer opnieuw opstarten en de huidige versie groter is dan vier.

In het voor beeld in de tabel ziet u hoe dit werkt.

|**Agent versie (laatste keer opnieuw opstarten)** | **Upgrade naar** | **Verplicht opnieuw opstarten?**|
|---------|---------|---------|
|9,16 |  9,18 | Niet verplicht|
|9,16 | 9,19 | Niet verplicht|
| 9,16 | 9,20 | Niet verplicht
 | 9,16 | 9,21 | Verplicht.<br/><br/> Voer een upgrade uit naar 9,20 en start het systeem opnieuw op voordat u een upgrade uitvoert naar 9,21.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Herstel na noodgevallen van virtuele Hyper-V-machines naar Azure

### <a name="between-a-hyper-v-site-and-azure"></a>Tussen een Hyper-V-site en Azure

1. Down load de update voor de provider van de Microsoft Azure-Site Recovery.
2. Installeer de provider op elke Hyper-V-server die is geregistreerd in Site Recovery. Als u een cluster uitvoert, voert u een upgrade uit op alle cluster knooppunten.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Tussen een on-premises VMM-site en Azure
1. Down load de update voor de provider van de Microsoft Azure-Site Recovery.
2. Installeer de provider op de VMM-server. Als VMM in een cluster is geïmplementeerd, installeert u de provider op alle cluster knooppunten.
3. Installeer de nieuwste Microsoft Azure Recovery Services-agent op alle Hyper-V-hosts of cluster knooppunten.


## <a name="between-two-on-premises-vmm-sites"></a>Tussen twee on-premises VMM-sites
1. Down load de meest recente update voor de provider van de Microsoft Azure-Site Recovery.
2. Installeer de meest recente provider op de VMM-server die de secundaire herstel site beheert. Als VMM in een cluster is geïmplementeerd, installeert u de provider op alle cluster knooppunten.
3. Nadat de herstel site is bijgewerkt, installeert u de provider op de VMM-server die de primaire site beheert.

## <a name="next-steps"></a>Volgende stappen

Volg onze pagina met [Azure-updates](https://azure.microsoft.com/updates/?product=site-recovery) om nieuwe updates en releases bij te houden.
