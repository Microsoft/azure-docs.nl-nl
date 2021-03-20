---
title: GPU configureren voor virtueel bureau blad van Windows (klassiek)-Azure
description: Hoe u GPU-versnelde rendering en code ring kunt inschakelen in Windows virtueel bureau blad (klassiek).
author: gundarev
ms.topic: how-to
ms.date: 03/30/2020
ms.author: denisgun
ms.openlocfilehash: fa7b80b021e00d25dea4f96432ae922c15474058
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95023051"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-classic"></a>GPU-versnelling (graphics processing unit) configureren voor virtuele Windows-Bureau bladen (klassiek)

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../configure-vm-gpu.md) als u Azure Resource Manager Windows Virtual Desktop-objecten probeert te beheren.

Windows Virtual Desktop biedt ondersteuning voor GPU-versnelde rendering en code ring voor betere prestaties en schaal baarheid van apps. GPU-versnelling is vooral van cruciaal belang voor grafische intensieve apps.

Volg de instructies in dit artikel om een door GPU geoptimaliseerde virtuele machine voor Azure te maken, deze toe te voegen aan uw hostgroep en deze te configureren voor het gebruik van GPU-versnelling voor rendering en code ring. In dit artikel wordt ervan uitgegaan dat u al een virtueel bureau blad-Tenant voor Windows hebt geconfigureerd.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Selecteer een door GPU geoptimaliseerde grootte voor virtuele Azure-machines

Azure biedt een aantal [geoptimaliseerde virtuele machine-grootten voor GPU](../../virtual-machines/sizes-gpu.md). De juiste keuze voor uw hostgroep is afhankelijk van een aantal factoren, waaronder uw specifieke app-workloads, de gewenste kwaliteit van de gebruikers ervaring en de kosten. Over het algemeen bieden grotere en meer compatibele Gpu's een betere gebruikers ervaring met een bepaalde gebruikers dichtheid.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Een hostgroep maken, uw virtuele machine inrichten en een app-groep configureren

Maak een nieuwe hostgroep met behulp van een virtuele machine met de grootte die u hebt geselecteerd. Zie [zelf studie: een hostgroep maken met Azure Marketplace](../create-host-pools-azure-marketplace.md)voor instructies.

Het virtuele bureau blad van Windows ondersteunt de weer gave en code ring van GPU-versnellen in de volgende besturings systemen:

* Windows 10 versie 1511 of hoger
* Windows Server 2016 of hoger

U moet ook een app-groep configureren of de standaard bureau blad-app-groep (met de naam bureau blad-toepassings groep) gebruiken die automatisch wordt gemaakt wanneer u een nieuwe hostgroep maakt. Zie [zelf studie: app-groepen beheren voor virtuele Windows-Bureau bladen](../manage-app-groups.md)voor instructies.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Ondersteunde grafische Stuur Programma's installeren in uw virtuele machine

Als u gebruik wilt maken van de GPU-mogelijkheden van virtuele machines uit de Azure N-serie in Windows Virtual Desktop, moet u de juiste grafische Stuur Programma's installeren. Volg de instructies op de [ondersteunde besturings systemen en stuur Programma's](../../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers) om stuur Programma's te installeren van de juiste grafische leverancier, hetzij hand matig of via een Azure VM-extensie.

Alleen stuur Programma's die worden gedistribueerd door Azure, worden ondersteund voor virtuele Windows-Bureau bladen. Extra, voor virtuele Azure-machines met NVIDIA-Gpu's, worden alleen [NVIDIA-raster Stuur Programma's](../../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers) ondersteund voor virtueel bureau blad van Windows.

Na installatie van het stuur programma is het opnieuw opstarten van de virtuele machine vereist. Gebruik de verificaties tappen in de bovenstaande instructies om te controleren of grafische Stuur Programma's zijn geïnstalleerd.

## <a name="configure-gpu-accelerated-app-rendering"></a>Rendering van GPU-versnellende apps configureren

Apps en Desk tops die worden uitgevoerd in configuraties met meerdere sessies worden standaard weer gegeven met de CPU en gebruiken geen beschik bare Gpu's voor rendering. Configureer groepsbeleid voor de sessiehost om GPU-versnelde rendering in te scha kelen:

1. Maak verbinding met het bureau blad van de virtuele machine met een account met lokale Administrator bevoegdheden.
2. Open het menu Start en typ ' gpedit. msc ' om de groepsbeleid editor te openen.
3. Navigeer in de structuur naar **computer configuratie**  >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **extern bureaublad-services**  >  **extern bureaublad Session Host**  >  **externe sessie omgeving**.
4. Beleid selecteren **Gebruik de standaard grafische hardware-adapter voor alle extern bureaublad-services sessies** en stel dit beleid in op **ingeschakeld** om GPU-rendering in de externe sessie in te scha kelen.

## <a name="configure-gpu-accelerated-frame-encoding"></a>GPU-versnelde frame codering configureren

Extern bureaublad codeert alle afbeeldingen die worden weer gegeven door apps en Desk tops (ongeacht of deze zijn gerenderd met GPU of met CPU) voor verzen ding naar Extern bureaublad-clients. Extern bureaublad maakt standaard geen gebruik van beschik bare Gpu's voor deze code ring. Configureer groepsbeleid voor de sessiehost om GPU-versnelde frame codering in te scha kelen. De bovenstaande stappen worden voortgezet:

1. Selecteer beleid **prioriteit van H. 264/avc 444 grafische modus voor extern bureaublad verbindingen** en stel dit beleid in op **ingeschakeld** om de H. 264/AVC 444-codec in de externe sessie af te dwingen.
2. Selecteer beleid **configureren H. 264/AVC-hardwarematige versleuteling voor extern bureaublad verbindingen** en stel dit beleid in op **ingeschakeld** om hardwarematige code ring in te scha kelen voor AVC/H. 264 in de externe sessie.

    >[!NOTE]
    >Stel in Windows Server 2016 voor keur voor de optie voor **AVC-hardwarematige code ring** in op **altijd poging**.

3. Nu het groeps beleid is bewerkt, dwingt u een update van groeps beleid af. Open de opdracht prompt en typ het volgende:

    ```batch
    gpupdate.exe /force
    ```

4. Meld u af bij de Extern bureaublad-sessie.

## <a name="verify-gpu-accelerated-app-rendering"></a>Rendering van GPU-versnelde apps controleren

Voer een van de volgende handelingen uit om te controleren of apps de GPU gebruiken voor Rendering:

* Voor virtuele Azure-machines met een NVIDIA GPU gebruikt u het `nvidia-smi` hulp programma zoals beschreven in [installatie van stuur programma controleren](../../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation) om te controleren op GPU-gebruik bij het uitvoeren van uw apps.
* In ondersteunde besturingssysteem versies kunt u taak beheer gebruiken om te controleren op GPU-gebruik. Selecteer de GPU op het tabblad prestaties om te zien of apps gebruikmaken van de GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>GPU-versneld frame codering controleren

Controleren of Extern bureaublad gebruikmaakt van GPU-versneld coderen:

1. Maak verbinding met het bureau blad van de virtuele machine met behulp van de Windows-client voor virtueel bureau blad.
2. Start de logboeken en ga naar het volgende knoop punt: **Logboeken toepassingen en services**  >  **micro soft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **operationeel**
3. Zoek naar gebeurtenis-ID 170 om te bepalen of GPU-versneld coderen wordt gebruikt. Als u ' AVC hardware encoder enabled: 1 ' ziet, wordt GPU-code ring gebruikt.
4. Zoek naar gebeurtenis-ID 162 om te bepalen of de AVC 444-modus wordt gebruikt. Als u ' AVC available: 1 eerste profiel: 2048 ' ziet, wordt het gebruik van AVC 444 gebruikt.

## <a name="next-steps"></a>Volgende stappen

Deze instructies moeten u uitvoeren met GPU-versnelling op één sessiehost (één virtuele machine). Enkele aanvullende overwegingen voor het inschakelen van GPU-versnelling in een grotere hostgroep:

* U kunt een [VM-extensie](../../virtual-machines/extensions/overview.md) gebruiken om de installatie van het stuur programma en updates van een aantal virtuele machines te vereenvoudigen. Gebruik de [uitbrei ding NVIDIA GPU-stuur programma](../../virtual-machines/extensions/hpccompute-gpu-windows.md) voor VM'S met NVIDIA-gpu's en gebruik de extensie van het AMD GPU-stuur programma voor VM'S met AMD-gpu's.
* Overweeg het gebruik van Active Directory groepsbeleid om de configuratie van groeps beleid te vereenvoudigen over een aantal Vm's. Zie [werken met Groepsbeleid-objecten](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11))voor meer informatie over het implementeren van Groepsbeleid in het Active Directory domein.