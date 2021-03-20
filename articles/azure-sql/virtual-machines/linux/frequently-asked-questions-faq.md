---
title: Veelgestelde vragen over virtuele machines SQL Server on Linux | Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over het uitvoeren van SQL Server on Linux virtuele machines.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad991974df30060e552d21a44d5796cd2ba165e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792545"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-virtual-machines"></a>Veelgestelde vragen over SQL Server on Linux virtuele machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/frequently-asked-questions-faq.md)
> * [Linux](frequently-asked-questions-faq.md)

In dit artikel vindt u antwoorden op enkele van de meest voorkomende vragen over het uitvoeren van [SQL Server on Linux virtuele machines](sql-server-on-linux-vm-what-is-iaas-overview.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Installatie kopieën

1. **Wat SQL Server afbeeldingen van de galerie met virtuele machines zijn beschikbaar?**

   Azure beheert installatie kopieën van virtuele machines (VM) voor alle ondersteunde grote releases van SQL Server op alle edities van Linux en Windows. Zie de volledige lijst met [Linux VM-installatie](sql-server-on-linux-vm-what-is-iaas-overview.md#create) kopieën en [Windows VM-installatie kopieën](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)voor meer informatie.

1. **Zijn bestaande SQL Server afbeeldingen van de galerie met virtuele machines bijgewerkt?**

   SQL Server installatie kopieën in de galerie met virtuele machines worden elke twee maanden bijgewerkt met de nieuwste updates voor Linux en Windows. Voor Linux-installatie kopieën bevat dit de meest recente systeem updates. Voor Windows-installatie kopieën omvat dit alle updates die als belang rijk zijn gemarkeerd in Windows Update, met inbegrip van belang rijke SQL Server beveiligings updates en-service packs. SQL Server cumulatieve updates worden anders afgehandeld voor Linux en Windows. Voor Linux worden SQL Server cumulatieve updates ook opgenomen in het vernieuwen. Op dit moment worden Windows-Vm's niet bijgewerkt met SQL Server of cumulatieve updates voor Windows Server.

1. **Welke gerelateerde SQL Server pakketten worden ook geïnstalleerd?**

   Zie [geïnstalleerde pakketten](sql-server-on-linux-vm-what-is-iaas-overview.md#packages)voor een overzicht van de SQL Server pakketten die standaard worden geïnstalleerd op SQL Server on Linux vm's.

1. **Kunnen SQL Server installatie kopieën van virtuele machines worden verwijderd uit de galerie?**

   Ja. In azure wordt slechts één installatie kopie per primaire versie en editie bewaard. Wanneer bijvoorbeeld een nieuw SQL Server Service Pack wordt uitgebracht, voegt Azure een nieuwe installatie kopie toe aan de galerie voor dat Service Pack. De SQL Server-installatie kopie voor het vorige Service Pack wordt direct verwijderd uit de Azure Portal. Het is echter nog steeds beschikbaar voor het inrichten van Power shell voor de volgende drie maanden. Na drie maanden is de vorige installatie kopie van het Service Pack niet meer beschikbaar. Dit verwijderings beleid is ook van toepassing als een SQL Server-versie niet wordt ondersteund wanneer het einde van de levens cyclus wordt bereikt.

## <a name="creation"></a>Maken

1. **Hoe kan ik een virtuele Linux-machine met SQL Server maken?**

   De eenvoudigste oplossing is het maken van een virtuele Linux-machine die SQL Server bevat. Zie [een virtuele Linux-machine met SQL server inrichten in de Azure Portal](sql-vm-create-portal-quickstart.md)voor een zelf studie over het aanmelden voor Azure en het maken van een SQL Server-VM vanuit de portal. U hebt ook de mogelijkheid om SQL Server hand matig te installeren op een virtuele machine met een gratis licentie (ontwikkelaar of Express) of door een on-premises licentie opnieuw te gebruiken. Als u uw eigen licentie meebrengt, moet u beschikken over [License Mobility via Software Assurance op Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Waarom kan ik geen RHEL of SLES SQL Server VM inrichten met een Azure-abonnement dat een bestedings limiet heeft?**

   Voor RHEL-en SLES-virtuele machines is een abonnement vereist waarvoor geen bestedings limiet en een geverifieerde betalings methode (meestal een credit card) aan het abonnement is gekoppeld. Als u een RHEL-of SLES-VM inricht zonder de bestedings limiet te verwijderen, wordt uw abonnement uitgeschakeld en worden alle Vm's/services gestopt. Als u in deze status uitvoert om het abonnement opnieuw in te scha kelen, [verwijdert u de bestedings limiet](https://account.windowsazure.com/subscriptions). Uw resterende tegoed wordt hersteld voor de huidige facturerings cyclus, maar een RHEL of SLES VM-afbeeldings toeslag gaat door op uw credit card als u ervoor kiest om deze opnieuw te starten en uit te voeren.

## <a name="licensing"></a>Licentieverlening

1. **Hoe kan ik mijn gelicentieerd exemplaar van SQL Server installeren op een Azure-VM?**

   Maak eerst een virtuele machine met alleen een Linux-besturings systeem. Voer vervolgens de [SQL Server installatie stappen](/sql/linux/sql-server-linux-setup#platforms) voor uw Linux-distributie uit. Tenzij u een van de gratis gelicentieerde edities van SQL Server installeert, moet u ook een SQL Server licentie en [License Mobility via Software Assurance op Azure](https://azure.microsoft.com/pricing/license-mobility/)hebben.

1. **Zijn er installatie kopieën van de virtuele Linux-machine (BYOL) van uw eigen licentie voor SQL Server?**

   Op dit moment zijn er geen installatie kopieën voor virtuele BYOL Linux-machines voor SQL Server. U kunt SQL Server echter hand matig installeren op een virtuele Linux-machine zoals beschreven in de vorige vragen.

1. **Kan ik een VM wijzigen zodat mijn eigen SQL Server-licentie wordt gebruikt, wanneer de VM is gemaakt vanuit een van de Betalen per gebruik-installatiekopieën uit de galerie?**

   Nee. U kunt niet overstappen van een licentie per seconde voor het gebruik van uw eigen licentie. U moet een nieuwe virtuele Linux-machine maken, SQL Server installeren en uw gegevens migreren. Zie de vorige vraag voor meer informatie over het nemen van uw eigen licentie.

## <a name="administration"></a>Beheer

1. **Kan ik een virtuele Linux-machine met SQL Server met SQL Server Management Studio (SSMS) beheren?**

   Ja, maar SSMS is momenteel een Windows-hulp programma. U moet op afstand verbinding maken vanaf een Windows-computer om SSMS te gebruiken met Linux-Vm's met SQL Server. Lokaal op Linux kunnen met het nieuwe hulp programma [MSSQL-conf](/sql/linux/sql-server-linux-configure-mssql-conf) veel beheer taken worden uitgevoerd. Zie [Azure Data Studio](/sql/azure-data-studio/what-is)voor een hulp programma voor het beheer van meerdere platforms voor data bases.

1. **Kan ik SQL Server volledig verwijderen van VM met SQL Server?**

   Ja, maar er worden nog steeds kosten in rekening gebracht voor uw SQL Server virtuele machine, zoals beschreven in [prijs informatie voor SQL Server virtuele machines van Azure](../windows/pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Als u de SQL Server niet langer nodig hebt, kunt u een nieuwe virtuele machine implementeren en de gegevens en toepassingen migreren naar de nieuwe virtuele machine. Vervolgens kunt u de virtuele SQL Server-machine verwijderen.

## <a name="updating-and-patching"></a>Updates en patches

1. **Hoe kan ik upgrade naar een nieuwe versie/editie van de SQL Server in een Azure VM?**

   er wordt momenteel geen upgrade uitgevoerd voor SQL Server in een virtuele machine van Azure. Maak een nieuwe virtuele Azure-machine met de gewenste SQL Server versie/editie en migreer vervolgens uw data bases naar de nieuwe server met behulp van [standaard technieken voor gegevens migratie](/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Algemeen

1. **Worden SQL Server oplossingen met hoge Beschik baarheid ondersteund op virtuele machines van Azure?**

   Momenteel niet. Voor AlwaysOn-beschikbaarheids groepen en failover clustering is zowel een clustering oplossing in Linux vereist, zoals pacemaker. De ondersteunde Linux-distributies voor SQL Server bieden geen ondersteuning voor de invoeg toepassingen met een hoge Beschik baarheid in de Cloud.

## <a name="resources"></a>Resources

**Virtuele Linux-machines**:

* [Overzicht van SQL Server op een Linux-VM](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [SQL Server inrichten op een Linux-VM](sql-vm-create-portal-quickstart.md)
* [Documentatie voor SQL Server op Linux](/sql/linux/sql-server-linux-overview)

**Windows-vm's**:

* [Overzicht van SQL Server op een Windows-VM](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server inrichten op een Windows-VM](../windows/sql-vm-create-portal-quickstart.md)
* [Veelgestelde vragen (Windows)](../windows/frequently-asked-questions-faq.md)