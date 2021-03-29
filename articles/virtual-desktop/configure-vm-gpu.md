---
title: GPU configureren voor virtueel bureau blad van Windows-Azure
description: Het inschakelen van GPU-versnelde rendering en code ring in Windows virtueel bureau blad.
author: gundarev
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: f95b9c1615cc58d9cc0589bad98c7315e571686e
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709460"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>GPU-versnelling (graphics processing unit) configureren voor Windows Virtual Desktop

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/configure-vm-gpu-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Windows Virtual Desktop biedt ondersteuning voor GPU-versnelde rendering en code ring voor betere prestaties en schaal baarheid van apps. GPU-versnelling is vooral van cruciaal belang voor grafische intensieve apps.

Volg de instructies in dit artikel om een door GPU geoptimaliseerde virtuele machine voor Azure te maken, deze toe te voegen aan uw hostgroep en deze te configureren voor het gebruik van GPU-versnelling voor rendering en code ring. In dit artikel wordt ervan uitgegaan dat u al een virtueel bureau blad-Tenant voor Windows hebt geconfigureerd.

## <a name="select-an-appropriate-gpu-optimized-azure-virtual-machine-size"></a>Selecteer een geschikte door GPU geoptimaliseerde grootte voor virtuele Azure-machines

Selecteer een van de VM-grootten van de [NV-serie](../virtual-machines/nv-series.md), de [NVv3](../virtual-machines/nvv3-series.md)of de [NVv4-serie](../virtual-machines/nvv4-series.md) . Deze zijn afgestemd op apps en bureau blad-virtualisatie, waardoor de meeste apps en de Windows-gebruikers interface GPU versnellen. De juiste keuze voor uw hostgroep is afhankelijk van een aantal factoren, waaronder uw specifieke app-workloads, de gewenste kwaliteit van de gebruikers ervaring en de kosten. Over het algemeen bieden grotere en meer compatibele Gpu's een betere gebruikers ervaring met een bepaalde gebruikers densiteit, terwijl kleinere en fractionele GPU-grootten meer nauw keurige controle over de kosten en kwaliteit toestaan.

>[!NOTE]
>De virtuele machines NC, NCv2, NCv3, ND en NDv2 Series van Azure zijn over het algemeen niet geschikt voor Windows-sessies met een virtueel bureau blad. Deze Vm's zijn afgestemd op gespecialiseerde, high-performance Compute-of machine learning-hulpprogram ma's, zoals die zijn gebouwd met NVIDIA CUDA. Ze bieden geen ondersteuning voor GPU-versnelling voor de meeste apps of de Windows-gebruikers interface.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Een hostgroep maken, uw virtuele machine inrichten en een app-groep configureren

Maak een nieuwe hostgroep met behulp van een virtuele machine met de grootte die u hebt geselecteerd. Zie [zelf studie: een hostgroep maken met de Azure Portal](./create-host-pools-azure-marketplace.md)voor instructies.

Het virtuele bureau blad van Windows ondersteunt de weer gave en code ring van GPU-versnellen in de volgende besturings systemen:

* Windows 10 versie 1511 of hoger
* Windows Server 2016 of hoger

U moet ook een app-groep configureren of de standaard bureau blad-app-groep (met de naam bureau blad-toepassings groep) gebruiken die automatisch wordt gemaakt wanneer u een nieuwe hostgroep maakt. Zie [zelf studie: app-groepen beheren voor virtuele Windows-Bureau bladen](./manage-app-groups.md)voor instructies.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Ondersteunde grafische Stuur Programma's installeren in uw virtuele machine

Als u gebruik wilt maken van de GPU-mogelijkheden van virtuele machines uit de Azure N-serie in Windows Virtual Desktop, moet u de juiste grafische Stuur Programma's installeren. Volg de instructies op de [ondersteunde besturings systemen en stuur Programma's](../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers) om stuur Programma's te installeren. Alleen stuur Programma's die door Azure worden gedistribueerd, worden ondersteund.

* Voor virtuele machines uit de Azure NV-of NVv3-serie worden alleen NVIDIA-raster Stuur Programma's en geen NVIDIA CUDA-Stuur Programma's ondersteund voor de meeste apps en de Windows-gebruikers interface. Als u ervoor kiest om stuur Programma's hand matig te installeren, moet u de raster Stuur Programma's installeren. Als u ervoor kiest om stuur Programma's te installeren met behulp van de Azure VM-extensie, worden er automatisch raster Stuur Programma's geïnstalleerd voor deze VM-grootten.
* Voor virtuele machines uit de Azure NVv4-serie installeert u de AMD-Stuur Programma's die beschikbaar zijn gesteld door Azure. U kunt ze automatisch installeren met behulp van de Azure VM-extensie, maar u kunt ze ook hand matig installeren.

Na installatie van het stuur programma is het opnieuw opstarten van de virtuele machine vereist. Gebruik de verificaties tappen in de bovenstaande instructies om te controleren of grafische Stuur Programma's zijn geïnstalleerd.

## <a name="configure-gpu-accelerated-app-rendering"></a>Rendering van GPU-versnellende apps configureren

Apps en Desk tops die worden uitgevoerd in configuraties met meerdere sessies worden standaard weer gegeven met de CPU en gebruiken geen beschik bare Gpu's voor rendering. Configureer groepsbeleid voor de sessiehost om GPU-versnelde rendering in te scha kelen:

1. Maak verbinding met het bureau blad van de virtuele machine met een account met lokale Administrator bevoegdheden.
2. Open het menu Start en typ ' gpedit. msc ' om de groepsbeleid editor te openen.
3. Navigeer in de structuur naar **computer configuratie**  >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **extern bureaublad-services**  >  **extern bureaublad Session Host**  >  **externe sessie omgeving**.
4. Selecteer beleid **hardware grafische adapters gebruiken voor alle extern bureaublad-services-sessies** en stel dit beleid in op **ingeschakeld** om GPU-rendering in de externe sessie in te scha kelen.

## <a name="configure-gpu-accelerated-frame-encoding"></a>GPU-versnelde frame codering configureren

Extern bureaublad codeert alle afbeeldingen die worden weer gegeven door apps en Desk tops (ongeacht of deze zijn gerenderd met GPU of met CPU) voor verzen ding naar Extern bureaublad-clients. Wanneer een deel van het scherm regel matig wordt bijgewerkt, wordt dit deel van het scherm gecodeerd met een videocodec (H. 264/AVC). Extern bureaublad maakt standaard geen gebruik van beschik bare Gpu's voor deze code ring. Configureer groepsbeleid voor de sessiehost om GPU-versnelde frame codering in te scha kelen. De bovenstaande stappen worden voortgezet:

>[!NOTE]
>GPU-versneld frame codering is niet beschikbaar in virtuele machines uit de NVv4-serie.

1. Selecteer beleid **configureren H. 264/AVC-hardwarematige versleuteling voor extern bureaublad verbindingen** en stel dit beleid in op **ingeschakeld** om hardwarematige code ring in te scha kelen voor AVC/H. 264 in de externe sessie.

    >[!NOTE]
    >Stel in Windows Server 2016 voor keur voor de optie voor **AVC-hardwarematige code ring** in op **altijd poging**.

2. Nu het groeps beleid is bewerkt, dwingt u een update van groeps beleid af. Open de opdracht prompt en typ het volgende:

    ```cmd
    gpupdate.exe /force
    ```

3. Meld u af bij de Extern bureaublad-sessie.

## <a name="configure-fullscreen-video-encoding"></a>Video codering op volledig scherm configureren

Als u vaak toepassingen gebruikt die een hoogwaardige inhouds opleveren, zoals 3D-modellen, CAD/CAM-en video toepassingen, kunt u ervoor kiezen om een inkomende video codering in te scha kelen voor een externe sessie. Het video profiel volledig scherm biedt een hogere frame snelheid en een betere gebruikers ervaring voor dergelijke toepassingen op kosten van netwerk bandbreedte en zowel sessie-host-en client bronnen. Het is raadzaam GPU-versnelde frame codering te gebruiken voor een video codering met een volledig scherm. Configureer groepsbeleid voor de sessiehost voor het inschakelen van de video codering op volledig scherm. De bovenstaande stappen worden voortgezet:

1. Selecteer beleid **prioriteit van H. 264/avc 444 grafische modus voor extern bureaublad verbindingen** en stel dit beleid in op **ingeschakeld** om de H. 264/AVC 444-codec in de externe sessie af te dwingen.
2. Nu het groeps beleid is bewerkt, dwingt u een update van groeps beleid af. Open de opdracht prompt en typ het volgende:

    ```cmd
    gpupdate.exe /force
    ```

3. Meld u af bij de Extern bureaublad-sessie.
## <a name="verify-gpu-accelerated-app-rendering"></a>Rendering van GPU-versnelde apps controleren

Voer een van de volgende handelingen uit om te controleren of apps de GPU gebruiken voor Rendering:

* Voor virtuele Azure-machines met een NVIDIA GPU gebruikt u het `nvidia-smi` hulp programma zoals beschreven in [installatie van stuur programma controleren](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation) om te controleren op GPU-gebruik bij het uitvoeren van uw apps.
* In ondersteunde besturingssysteem versies kunt u taak beheer gebruiken om te controleren op GPU-gebruik. Selecteer de GPU op het tabblad prestaties om te zien of apps gebruikmaken van de GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>GPU-versneld frame codering controleren

Controleren of Extern bureaublad gebruikmaakt van GPU-versneld coderen:

1. Maak verbinding met het bureau blad van de virtuele machine met behulp van de Windows-client voor virtueel bureau blad.
2. Start de logboeken en ga naar het volgende knoop punt: **Logboeken toepassingen en services**  >  **micro soft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **operationeel**
3. Zoek naar gebeurtenis-ID 170 om te bepalen of GPU-versneld coderen wordt gebruikt. Als u ' AVC hardware encoder enabled: 1 ' ziet, wordt GPU-code ring gebruikt.

## <a name="verify-fullscreen-video-encoding"></a>Video codering op volledig scherm controleren

U kunt als volgt controleren of Extern bureaublad gebruikmaakt van de video codering voor volledig scherm:

1. Maak verbinding met het bureau blad van de virtuele machine met behulp van de Windows-client voor virtueel bureau blad.
2. Start de logboeken en ga naar het volgende knoop punt: **Logboeken toepassingen en services**  >  **micro soft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **operationeel**
3. Zoek naar gebeurtenis-ID 162 om te bepalen of de fullscreen video-code ring wordt gebruikt. Als u ' AVC available: 1 eerste profiel: 2048 ' ziet, wordt het gebruik van AVC 444 gebruikt.

## <a name="next-steps"></a>Volgende stappen

Deze instructies moeten u uitvoeren met GPU-versnelling op één sessiehost (één virtuele machine). Enkele aanvullende overwegingen voor het inschakelen van GPU-versnelling in een grotere hostgroep:

* U kunt een [VM-extensie](../virtual-machines/extensions/overview.md) gebruiken om de installatie van het stuur programma en updates van een aantal virtuele machines te vereenvoudigen. Gebruik de [uitbrei ding NVIDIA GPU-stuur programma](../virtual-machines/extensions/hpccompute-gpu-windows.md) voor VM'S met NVIDIA-gpu's en gebruik de extensie van het [AMD GPU-stuur programma](../virtual-machines/extensions/hpccompute-amd-gpu-windows.md) voor Vm's met AMD-gpu's.
* Overweeg het gebruik van Active Directory groepsbeleid om de configuratie van groeps beleid te vereenvoudigen over een aantal Vm's. Zie [werken met Groepsbeleid-objecten](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11))voor meer informatie over het implementeren van Groepsbeleid in het Active Directory domein.