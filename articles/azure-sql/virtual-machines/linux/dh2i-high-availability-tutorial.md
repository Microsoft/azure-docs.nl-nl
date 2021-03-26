---
title: Altijd on-beschikbaarheids groep instellen met DH2i DxEnterprise die wordt uitgevoerd op op Linux gebaseerde Azure Virtual Machines
description: Gebruik DH2i DxEnterprise als cluster beheer voor hoge Beschik baarheid met een beschikbaarheids groep op SQL Server on Linux Azure Virtual Machines
ms.date: 03/04/2021
ms.service: virtual-machines-linux
ms.topic: tutorial
author: amvin87
ms.author: amitkh
ms.reviewer: vanto
ms.openlocfilehash: 07752eb5c7f18a8952c43e77afed78b06432aca6
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568529"
---
# <a name="tutorial---setup-a-three-node-always-on-availability-group-with-dh2i-dxenterprise-running-on-linux-based-azure-virtual-machines"></a>Zelf studie: een drie knoop punt altijd on-beschikbaarheids groep instellen met DH2i DxEnterprise die wordt uitgevoerd op op Linux gebaseerde Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In deze zelf studie wordt uitgelegd hoe u SQL Server always on-beschikbaarheids groep configureert met DH2i DxEnterprise die worden uitgevoerd op op Linux gebaseerde Azure-Virtual Machines (Vm's). 

Zie [DH2i DxEnterprise](https://dh2i.com/dxenterprise-availability-groups/)voor meer informatie over DxEnterprise.

> [!NOTE]
> Micro soft ondersteunt het verplaatsen van gegevens, de beschikbaarheids groep en de SQL Server onderdelen. Neem contact op met DH2i voor ondersteuning met betrekking tot de documentatie van DH2i DxEnterprise cluster, voor het cluster en het quorum beheer.
 

Deze zelf studie bestaat uit de volgende stappen:

> [!div class="checklist"]
> * Installeer SQL Server op alle Azure virtual machines (Vm's) die deel zullen uitmaken van de beschikbaarheids groep.
> * Installeer DxEnterprise op alle Vm's en configureer het DxEnterprise-cluster.
> * Maak de virtuele hosts om failover-ondersteuning en hoge Beschik baarheid te bieden, voeg een beschikbaarheids groep en data base aan de beschikbaarheids groep toe.
> * De interne Azure Load Balancer maken voor de beschikbaarheids groep-listener (optioneel).
> * Een hand matige of automatische failover uitvoeren.

In deze zelf studie gaat u een DxEnterprise-cluster instellen met behulp van [DxAdmin-client gebruikersinterface](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/). U kunt eventueel ook het cluster instellen met behulp van de [DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/) -opdracht regel interface. In dit voor beeld hebben we vier virtuele machines gebruikt. Drie van deze virtuele machines worden uitgevoerd op Ubuntu 18,04 en maken deel uit van het cluster met drie knoop punten. Op de vierde VM wordt Windows 10 uitgevoerd met het DxAdmin-hulp programma voor het beheren en configureren van het cluster.

## <a name="prerequisites"></a>Vereisten

- Maak vier virtuele machines in Azure. Volg de [Snelstartgids: virtuele Linux-machine maken in azure Portal](../../../virtual-machines/linux/quick-create-portal.md) artikel voor het maken van virtuele machines op basis van Linux. Voor het maken van de op Windows gebaseerde virtuele machine volgt u ook de [Snelstartgids: een virtuele Windows-machine maken in het Azure Portal](../../../virtual-machines/windows/quick-create-portal.md) -artikel.
- Installeer .NET 3,1 op alle op Linux gebaseerde virtuele machines die deel gaan uitmaken van het cluster. Volg de instructies die [hier](/dotnet/core/install/linux) worden beschreven op basis van het Linux-besturings systeem dat u kiest.
- Een geldige DxEnterprise-licentie met beschikbaarheids groep-beheer functies is vereist. Zie [DxEnterprise Free Trial](https://dh2i.com/trial/) (Engelstalig) voor meer informatie over hoe u een gratis proef versie kunt verkrijgen.

## <a name="install-sql-server-on-all-the-azure-vms-that-will-be-part-of-the-availability-group"></a>SQL Server installeren op alle virtuele Azure-machines die deel zullen uitmaken van de beschikbaarheids groep

In deze zelf studie stelt u een Linux-cluster op basis van knoop punten in dat de beschikbaarheids groep uitvoert. Volg de documentatie voor [SQL Server installatie op Linux](/sql/linux/sql-server-linux-overview#install) op basis van de keuze van uw Linux-platform. U wordt ook aangeraden de [SQL Server-hulpprogram ma's](/sql/linux/sql-server-linux-setup-tools) voor deze zelf studie te installeren.
 
> [!NOTE]
> Zorg ervoor dat het Linux-besturings systeem dat u kiest een algemene distributie is die wordt ondersteund door zowel [DH2i DxEnterprise (Zie de sectie minimale systeem vereisten)](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) als [Microsoft SQL Server](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms).
>
> In dit voor beeld gebruiken we Ubuntu 18,04, dat wordt ondersteund door DH2i DxEnterprise en Microsoft SQL Server.

Voor deze zelf studie gaan we SQL Server op de Windows-VM niet installeren omdat dit knoop punt niet deel zal uitmaken van het cluster en alleen wordt gebruikt voor het beheren van het cluster met behulp van DxAdmin.

Nadat u deze stap hebt voltooid, moet u SQL Server en [SQL Server hulpprogram ma's](/sql/linux/sql-server-linux-setup-tools) (optioneel) geïnstalleerd op alle drie op Linux gebaseerde virtuele machines die deel uitmaken van de beschikbaarheids groep.
 
## <a name="install-dxenterprise-on-all-the-vms-and-configure-the-cluster"></a>DxEnterprise op alle Vm's installeren en het cluster configureren

In deze stap gaan we DH2i DxEnterprise voor Linux installeren op de drie Linux-Vm's. In de volgende tabel wordt de functie beschreven die elke server in het cluster speelt:

| Aantal VM's | DH2i DxEnterprise-rol | Replica functie Microsoft SQL Server-beschikbaarheids groep |
|--|--|--|
| 1 | Cluster knooppunt-Linux gebaseerd | Primair |
| 1 | Cluster knooppunt-Linux gebaseerd | Secundaire synchroon door voeren |
| 1 | Cluster knooppunt-Linux gebaseerd | Secundaire synchroon door voeren |
| 1 | DxAdmin-client | NA |


Als u DxEnterprise op de drie Linux-knoop punten wilt installeren, volgt u de DH2i DxEnterprise-documentatie op basis van het Linux-besturings systeem dat u kiest. Installeer DxEnterprise met een van de hieronder vermelde methoden.

- **Ubuntu**
    - [Snel starten gids voor opslag plaats-installatie](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-ubuntu-installation-quick-start-guide/)
    - [Gids voor uitbrei dingen Snel starten](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Gids voor installatie kopie van Marketplace Snel starten](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)
- **RHEL**
    - [Snel starten gids voor opslag plaats-installatie](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-rhel-centos-installation-quick-start-guide/)
    - [Gids voor uitbrei dingen Snel starten](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Gids voor installatie kopie van Marketplace Snel starten](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)

Als u alleen het DxAdmin-client hulpprogramma wilt installeren op de Windows-VM, volgt u de [DxAdmin-client gebruikersinterface snel starten gids](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/).

Na deze stap moet het DxEnterprise-cluster zijn gemaakt op de virtuele Linux-machines en DxAdmin-client geïnstalleerd op de Windows-client computer. 

> [!NOTE]
> U kunt ook een cluster met drie knoop punten maken waarbij een van de knoop punten wordt toegevoegd als de *modus alleen configuratie*, zoals [hier](/sql/database-engine/availability-groups/windows/availability-modes-always-on-availability-groups#SupportedAvModes) wordt beschreven om automatische failover in te scha kelen. 

## <a name="create-the-virtual-hosts-to-provide-failover-support-and-high-availability"></a>De virtuele hosts maken voor het bieden van failover-ondersteuning en hoge Beschik baarheid

In deze stap gaan we een virtuele host en een beschikbaarheids groep maken en vervolgens een Data Base toevoegen, allemaal met behulp van de DxAdmin-gebruikers interface.   

> [!NOTE]
> Tijdens deze stap worden de SQL Server-exemplaren opnieuw gestart om altijd in te scha kelen. 

Maak verbinding met de Windows-client machine met DxAdmin om verbinding te maken met het cluster dat u in de bovenstaande stap hebt gemaakt. Volg de stappen die zijn beschreven in [MSSQL-beschikbaarheids groepen met DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-mssql-availability-groups-with-dxadmin-quick-start-guide/) om altijd in te scha kelen en de virtuele host en beschikbaarheids groep te maken. 

> [!TIP]
> Zorg ervoor dat de data base is gemaakt en een back-up maakt op het primaire exemplaar van SQL Server voordat u de data bases toevoegt.  

## <a name="create-the-internal-azure-load-balancer-for-listener-optional"></a>De interne Azure Load Balancer voor de listener maken (optioneel)

In deze optionele stap kunt u de Azure Load Balancer maken en configureren die de IP-adressen voor de listeners van de beschikbaarheids groep bevat. Raadpleeg [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md)voor meer informatie over Azure Load Balancer. Voor het configureren van de listener voor Azure load balancer en beschikbaarheids groep met behulp van DxAdmin, volgt u de DxEnterprise- [hand leiding Azure Load Balancer snel starten](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-load-balancer-quick-start-guide/).

Na deze stap moet er een beschikbaarheids groep-listener zijn gemaakt en toegewezen aan de interne Azure-load balancer.

## <a name="test-manual-or-automatic-failover"></a>Hand matige of automatische failover testen

Voor de automatische testfailover kunt u de primaire replica (Schakel de virtuele machine uit de Azure Portal) door gaan. Hiermee wordt de onverwachte niet-beschik baarheid van het primaire knoop punt gerepliceerd. Het verwachte gedrag is:
- Cluster beheer bevordert een van de secundaire replica's in de beschikbaarheids groep naar de primaire replica.
- De mislukte primaire replica wordt automatisch toegevoegd aan het cluster nadat er een back-up is gemaakt. Cluster beheer bevordert het naar een secundaire replica.

 
U kunt ook een hand matige failover uitvoeren door de onderstaande stappen te volgen:

1. Verbinding maken met het cluster via DxAdmin   
1. De virtuele host voor de beschikbaarheids groep uitvouwen
1. Klik met de rechter muisknop op het doel knooppunt/de secundaire replica en selecteer **Hosting starten op lid** om de failover te initiëren 

Voor meer informatie over meer bewerkingen in DxEnterprise gaat u naar de [DxEnterprise-beheer handleiding](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) en de [DxEnterprise DxCLI-hand leiding](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beschikbaarheids groepen in Linux](/sql/linux/sql-server-linux-availability-group-overview)
- [Quick Start: virtuele Linux-machine maken in Azure Portal](../../../virtual-machines/linux/quick-create-portal.md)
- [Quickstart: Een virtuele Windows-machine maken in de Azure-portal](../../../virtual-machines/windows/quick-create-portal.md)
- [Ondersteunde platforms voor SQL Server 2019 in Linux](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms)