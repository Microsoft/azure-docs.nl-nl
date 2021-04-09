---
title: Verbinding maken met een SQL Server virtuele machine (Resource Manager) | Microsoft Docs
description: Meer informatie over hoe u verbinding maakt met uw SQL Server virtuele machine in Azure. In dit onderwerp wordt gebruikgemaakt van het klassieke implementatie model. De scenario's variëren afhankelijk van de netwerk configuratie en de locatie van de client.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37cb8f5a2ff0916f53ae50f5750664204ab1ba75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737486"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Verbinding maken met een SQL Server virtuele machine in azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

## <a name="overview"></a>Overzicht

In dit onderwerp wordt beschreven hoe u verbinding maakt met uw SQL op een virtuele Azure-machine (VM). Het omvat enkele [algemene verbindings scenario's](#connection-scenarios) en geeft vervolgens [de stappen in de portal voor het wijzigen van connectiviteits instellingen](#change). Zie de [hand matige configuratie](#manual) aan het einde van dit onderwerp als u een verbinding met een externe-portal wilt oplossen of als u deze wilt configureren. 

Zie [een SQL Server virtuele machine inrichten in azure](create-sql-vm-portal.md)als u liever een volledig overzicht van zowel het inrichten als de connectiviteit zou hebben.

## <a name="connection-scenarios"></a>Verbindings scenario's

De manier waarop een client verbinding maakt met een SQL Server VM verschilt, afhankelijk van de locatie van de client en de netwerk configuratie.

Als u in de Azure Portal een SQL Server virtuele machine inricht, hebt u de mogelijkheid om het type **SQL-verbinding** op te geven.

![Open bare SQL-connectiviteits optie tijdens inrichten](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity.png)

De opties voor connectiviteit zijn onder andere:

| Optie | Beschrijving |
|---|---|
| **Openbaar** | Verbinding maken met SQL Server via internet. |
| **Privé** | Verbinding maken met SQL Server in hetzelfde virtuele netwerk. |
| **Lokale** | Maak verbinding met SQL Server lokaal op dezelfde virtuele machine. | 

In de volgende secties worden de **open bare** en **persoonlijke** opties uitvoeriger beschreven.

## <a name="connect-to-sql-server-over-the-internet"></a>Via Internet verbinding maken met SQL Server

Als u via Internet verbinding wilt maken met uw SQL Server data base-engine, selecteert u **openbaar** voor het **SQL-verbindings** type in de portal tijdens het inrichten. De portal voert automatisch de volgende stappen uit:

* Hiermee schakelt u het TCP/IP-protocol in voor SQL Server.
* Hiermee configureert u een firewall regel voor het openen van de SQL Server TCP-poort (standaard 1433).
* Hiermee schakelt u SQL Server verificatie in, die is vereist voor open bare toegang.
* Hiermee configureert u de netwerk beveiligings groep op de virtuele machine naar alle TCP-verkeer op de SQL Server poort.

> [!IMPORTANT]
> Het TCP/IP-protocol wordt niet automatisch ingeschakeld voor de installatie kopieën van de virtuele machine voor de SQL Server Developer-en Express-edities. Voor ontwikkel aars en Express-edities moet u SQL Server Configuration Manager gebruiken om [het TCP/IP-protocol hand matig in te scha kelen](#manualtcp) nadat de virtuele machine is gemaakt.

Elke client met Internet toegang kan verbinding maken met het SQL Server-exemplaar door ofwel het open bare IP-adres van de virtuele machine of een DNS-label op te geven dat aan dat IP-adres is toegewezen. Als de SQL Server poort 1433 is, hoeft u deze niet op te geven in de connection string. Met de volgende connection string maakt u verbinding met een SQL-VM met een DNS-label voor `sqlvmlabel.eastus.cloudapp.azure.com` het gebruik van SQL-verificatie (u kunt ook het open bare IP-adres gebruiken).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Hoewel met deze teken reeks connectiviteit voor clients via internet is ingeschakeld, betekent dit niet dat iedereen verbinding kan maken met uw SQL Server-exemplaar. Externe clients moeten de juiste gebruikers naam en wacht woord gebruiken. Voor extra beveiliging kunt u echter de bekende poort 1433 voor komen. Als u bijvoorbeeld SQL Server wilt configureren om te Luis teren naar poort 1500 en de juiste firewall-en netwerk beveiligings groeps regels wilt instellen, kunt u verbinding maken door het poort nummer toe te voegen aan de server naam. In het volgende voor beeld wordt het vorige gewijzigd door een aangepast poort nummer, **1500**, toe te voegen aan de server naam:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Wanneer u via internet een query uitvoert op SQL Server op de virtuele machine, gelden voor alle uitgaande gegevens van het Azure-Data Center normale [prijzen voor uitgaande gegevens overdracht](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Verbinding maken met SQL Server binnen een virtueel netwerk

Wanneer u **privé** kiest voor het **SQL-verbindings** type in de portal, configureert Azure de meeste instellingen die identiek zijn aan **openbaar**. Het enige verschil is dat er geen regel voor de netwerk beveiligings groep is voor het toestaan van buiten verkeer op de SQL Server poort (standaard 1433).

> [!IMPORTANT]
> Het TCP/IP-protocol wordt niet automatisch ingeschakeld voor de installatie kopieën van de virtuele machine voor de SQL Server Developer-en Express-edities. Voor ontwikkel aars en Express-edities moet u SQL Server Configuration Manager gebruiken om [het TCP/IP-protocol hand matig in te scha kelen](#manualtcp) nadat de virtuele machine is gemaakt.

Particuliere verbindingen worden vaak gebruikt in combi natie met een [virtueel netwerk](../../../virtual-network/virtual-networks-overview.md), waardoor verschillende scenario's mogelijk zijn. U kunt virtuele machines in hetzelfde virtuele netwerk verbinden, zelfs als deze Vm's in verschillende resource groepen bestaan. En met een [site-naar-site-VPN](../../../vpn-gateway/tutorial-site-to-site-portal.md)kunt u een hybride architectuur maken waarmee vm's worden verbonden met on-premises netwerken en machines.

Met virtuele netwerken kunt u ook uw Azure-Vm's toevoegen aan een domein. Dit is de enige manier om Windows-verificatie te gebruiken om SQL Server. De andere verbindings scenario's vereisen SQL-verificatie met gebruikers namen en wacht woorden.

Ervan uitgaande dat u DNS in uw virtuele netwerk hebt geconfigureerd, kunt u verbinding maken met uw SQL Server-exemplaar door de SQL Server VM-computer naam op te geven in de connection string. In het volgende voor beeld wordt ook aangenomen dat Windows-verificatie is geconfigureerd en dat de gebruiker toegang heeft gekregen tot het SQL Server exemplaar.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a> SQL-Verbindings instellingen wijzigen

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U kunt de connectiviteits instellingen voor uw SQL Server virtuele machine wijzigen in de Azure Portal.

1. Selecteer in de Azure Portal **virtuele SQL-machines**.

2. Selecteer uw SQL Server-VM.

3. Selecteer bij **instellingen** de optie **beveiliging**.

4. Wijzig het **SQL-connectiviteits niveau** in uw vereiste instelling. U kunt dit gebied eventueel gebruiken om de SQL Server poort of de instellingen voor SQL-verificatie te wijzigen.

   ![SQL-connectiviteit wijzigen](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity-change.png)

5. Wacht enkele minuten totdat de update is voltooid.

   ![Update melding voor SQL-VM](./media/ways-to-connect-to-sql/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a> TCP/IP inschakelen voor ontwikkel aars-en Express-edities

Wanneer u SQL Server connectiviteits instellingen wijzigt, schakelt Azure het TCP/IP-protocol niet automatisch in voor SQL Server Developer-en Express-edities. In de onderstaande stappen wordt uitgelegd hoe u TCP/IP handmatig kunt inschakelen, zodat u op afstand via een IP-adres verbinding kunt maken.

Maak eerst verbinding met de virtuele machine van SQL Server met extern bureau blad.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Schakel vervolgens het TCP/IP-protocol in met **SQL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Verbinden met SSMS

De volgende stappen laten zien hoe u een optioneel DNS-label maakt voor uw Azure-VM en vervolgens verbinding maakt met SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a> Hand matige configuratie en probleem oplossing

Hoewel de portal opties biedt voor het automatisch configureren van connectiviteit, is het handig om te weten hoe u de connectiviteit hand matig kunt configureren. Meer informatie over de vereisten kan ook problemen oplossen.

De volgende tabel bevat de vereisten om verbinding te maken met SQL Server op Azure VM.

| Vereiste | Beschrijving |
|---|---|
| [Verificatie modus SQL Server inschakelen](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | SQL Server-verificatie is vereist om extern verbinding te maken met de virtuele machine tenzij u Active Directory hebt geconfigureerd op een virtueel netwerk. |
| [Een SQL-aanmelding maken](/sql/relational-databases/security/authentication-access/create-a-login) | Als u SQL-verificatie gebruikt, hebt u een SQL-aanmelding nodig met een gebruikers naam en wacht woord die ook machtigingen voor uw doel database hebben. |
| [TCP/IP-protocol inschakelen](#manualtcp) | SQL Server moet verbindingen via TCP toestaan. |
| [Firewall regel inschakelen voor de SQL Server poort](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | De firewall op de virtuele machine moet binnenkomend verkeer op de SQL Server poort toestaan (standaard 1433). |
| [Een regel voor de netwerk beveiligings groep maken voor TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | U moet de virtuele machine toestaan verkeer te ontvangen op de SQL Server poort (standaard 1433) als u verbinding wilt maken via internet. Voor lokale en virtuele netwerk verbindingen is dit niet vereist. Dit is de enige stap die is vereist in de Azure Portal. |

> [!TIP]
> De stappen in de voor gaande tabel worden voor u uitgevoerd wanneer u connectiviteit in de portal configureert. Gebruik deze stappen alleen om uw configuratie te bevestigen of om connectiviteit hand matig in te stellen voor SQL Server.

## <a name="next-steps"></a>Volgende stappen

Zie [een SQL Server virtuele machine inrichten in azure voor informatie over het inrichten van instructies in](create-sql-vm-portal.md)combi natie met deze connectiviteits stappen.

Zie [SQL Server op virtuele machines van Azure](sql-server-on-azure-vm-iaas-what-is-overview.md)voor andere onderwerpen met betrekking tot het uitvoeren van SQL Server op Azure-vm's.