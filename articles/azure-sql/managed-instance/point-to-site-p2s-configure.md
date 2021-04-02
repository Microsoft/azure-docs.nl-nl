---
title: Punt-naar-site-connectiviteit configureren met behulp van SSMS
titleSuffix: Azure SQL Managed Instance
description: Maak verbinding met Azure SQL Managed Instance met behulp van SSMS (SQL Server Management Studio) via een punt-naar-site-verbinding vanaf een on-premises clientcomputer.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 3baf2d7ed7c326895ae40948fc2d0a4cc03021f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788363"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>Quickstart: Een punt-naar-site-verbinding naar Azure SQL Managed Instance configureren vanuit on-premises
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Deze quickstart laat zien hoe u verbinding kunt maken met Azure SQL Managed Instance met behulp van [SSMS](/sql/ssms/sql-server-management-studio-ssms) (SQL Server Management Studio) vanaf een on-premises clientcomputer, via een punt-naar-site-verbinding. Raadpleeg [Over punt-naar-site-VPN](../../vpn-gateway/point-to-site-about.md) voor informatie over punt-naar-site-verbindingen.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstartgids geldt het volgende:

- Gebruikt als uitgangspunt de resources die zijn gemaakt in [Een beheerd exemplaar maken](instance-create-quickstart.md).
- Vereist PowerShell 5.1 en Azure PowerShell 1.4.0 of later op uw on-premises clientcomputer. Zie, indien nodig, de instructies voor het [installeren van de Azure PowerShell-module](/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Vereist de nieuwste versie van [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) op uw on-premises clientcomputer.

## <a name="attach-a-vpn-gateway-to-a-virtual-network"></a>Een VPN-gateway koppelen aan een virtueel netwerk

1. Open PowerShell op uw on-premises clientcomputer.

2. Kopieer dit PowerShell-script. Met dit script wordt een VPN-gateway gekoppeld aan het virtuele netwerk van SQL Managed Instance dat u hebt gemaakt in de quickstart [Een beheerd exemplaar maken](instance-create-quickstart.md). Dit script maakt gebruik van de Azure PowerShell Az-module en doet het volgende voor hosts die zijn gebaseerd op Windows of Linux:

   - Maakt en installeert certificaten op een clientcomputer
   - Berekent het toekomstige IP-bereik voor het VPN-gatewaysubnet
   - Maakt het gatewaysubnet
   - Implementeert de Azure Resource Manager-sjabloon waarmee de VPN-gateway wordt gekoppeld aan het VPN-subnet

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

3. Plak het script in het PowerShell-venster en geef de vereiste parameters op. De waarden voor `<subscriptionId>`, `<resourceGroup>` en `<virtualNetworkName>` moeten overeenkomen met de waarden die u hebt gebruikt voor de quickstart [Een beheerd exemplaar maken](instance-create-quickstart.md). De waarde voor `<certificateNamePrefix>` kan een tekenreeks naar uw keuze zijn.

4. Voer het PowerShell-script uit.

> [!IMPORTANT]
> Ga pas verder wanneer het PowerShell-script is voltooid.

## <a name="create-a-vpn-connection"></a>Een VPN-verbinding maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Open de resourcegroep waarin u de virtuele netwerkgateway hebt gemaakt, en open de virtuele netwerkgateway.
3. Selecteer **Punt-naar-site-configuratie** en selecteer vervolgens **VPN-client downloaden**.

    ![VPN-client downloaden](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. Pak de bestanden uit het ZIP-bestand uit op de on-premises clientcomputer, en open de map met de uitgepakte bestanden.
5. Open de map **WindowsAmd64** en open het bestand **VpnClientSetupAmd64.exe**.
6. Als u het bericht **Uw pc wordt beschermd** ontvangt, klikt u op **Meer info** en klikt u vervolgens op **Toch uitvoeren**.

    ![VPN-client installeren](./media/point-to-site-p2s-configure/vpn-client-defender.png)
7. Klik in het dialoogvenster Gebruikersaccountbeheer op **Ja** om door te gaan.
8. Selecteer in het dialoogvenster dat verwijst naar uw virtuele netwerk, de optie **Ja** om de VPN-client voor uw virtuele netwerk te installeren.

## <a name="connect-to-the-vpn-connection"></a>Verbinding maken met de VPN-verbinding

1. Ga naar **VPN** in **Netwerk en internet** op uw on-premises clientcomputer, en selecteer uw virtuele netwerk van SQL Managed Instance om een verbinding tot stand te brengen met dit VNet. In de volgende afbeelding is **MyNewVNet** de naam van het VNet.

    ![VPN-verbinding](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. Selecteer **Verbinding maken**.
3. Selecteer **Verbinding maken** in het dialoogvenster.

    ![Schermopname met de knop Verbinding maken gemarkeerd.](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. Als u een melding ontvangt dat Verbindingsbeheer verhoogde bevoegdheden nodig heeft om de routeringstabel bij te werken, kiest u **Doorgaan**.
5. Selecteer **Ja** in het dialoogvenster Gebruikersaccountbeheer om door te gaan.

   U hebt een VPN-verbinding met uw VNet van SQL Managed Instance tot stand gebracht.

    ![Schermopname die het verbonden bericht markeert wanneer u de verbinding tot stand hebt gebracht.](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>Verbinden met SSMS

1. Open SQL Server Management Studio op de on-premises clientcomputer.
2. Voer in het dialoogvenster **Verbinding maken met de server** in het vak **Servernaam** de volledig gekwalificeerde **hostnaam** in voor uw beheerde instantie.
3. Selecteer **SQL Server-verificatie**, geef uw gebruikersnaam en wachtwoord op, en selecteer vervolgens **Verbinding maken**.

    ![SSMS verbinden](./media/point-to-site-p2s-configure/ssms-connect.png)  

Nadat u verbinding hebt gemaakt, kunt u uw systeem- en gebruikersdatabases weergeven op het knooppunt Databases. U kunt ook verschillende objecten weergeven op de knooppunten Beveiliging, Serverobjecten, Replicatie, Beheer, SQL Server Agent, en XEvent Profiler.

## <a name="next-steps"></a>Volgende stappen

- Zie [Een punt-naar-site-verbinding configureren](point-to-site-p2s-configure.md) voor een quickstart over verbinding maken vanaf een virtuele Azure-machine.
- Zie [Uw toepassingen verbinden met SQL Managed Instance](connect-application-instance.md) voor een overzicht van de verbindingsopties voor toepassingen.
- Als u een bestaande SQL Server-database wilt herstellen van on-premises naar een beheerd exemplaar, kunt u [Azure Database Migration Service voor migratie](../../dms/tutorial-sql-server-to-managed-instance.md) of de [opdracht T-SQL RESTORE](restore-sample-database-quickstart.md) gebruiken om de database te herstellen vanuit een back-upbestand voor de database.