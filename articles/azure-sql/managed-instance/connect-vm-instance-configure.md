---
title: Connectiviteit voor Azure-VM's configureren
titleSuffix: Azure SQL Managed Instance
description: Maak verbinding met Azure SQL Managed Instance met behulp van SQL Server Management Studio vanaf een Azure-VM.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: a1f496d59fa626dc8750493591128f7363afa40d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91620253"
---
# <a name="quickstart-configure-an-azure-vm-to-connect-to-azure-sql-managed-instance"></a>Quickstart: Een Azure-VM configureren om verbinding te maken met Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In deze quickstart wordt beschreven hoe u een Azure-VM configureert om verbinding te maken met Azure SQL Managed Instance met behulp van SQL Server Management Studio (SSMS). 


Zie daarentegen [Een punt-naar-site-verbinding configureren](point-to-site-p2s-configure.md) voor een quickstart over verbinding maken vanaf een on-premises clientcomputer met behulp van een punt-naar-site-verbinding.

## <a name="prerequisites"></a>Vereisten

Voor deze quickstart worden de resources die zijn gemaakt in [Een beheerde instantie maken](instance-create-quickstart.md) als uitgangspunt gebruikt.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-new-subnet-vnet"></a>Een nieuw subnet in het VNet maken

Met de volgende stappen maakt u een nieuw subnet in het SQL Managed Instance-VNet, zodat een virtuele Azure-VM verbinding kan maken met de beheerde instantie. Het SQL Managed Instance-subnet wordt toegewezen aan beheerde instanties. U kunt geen andere resources, zoals Azure-VM's, maken in dat subnet.

1. Open de resourcegroep voor de beheerde instantie die u hebt gemaakt in de quickstart [Een beheerde instantie maken](instance-create-quickstart.md). Selecteer het virtuele netwerk voor de beheerde instantie.

   ![SQL Managed Instance-resources](./media/connect-vm-instance-configure/resources.png)

2. Selecteer **Subnetten** en vervolgens **+ Subnet** om een nieuw subnet te maken.

   ![SQL Managed Instance-subnetten](./media/connect-vm-instance-configure/subnets.png)

3. Vul het formulier in met behulp van de informatie in de volgende tabel:

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ---------------- | ----------------- | ----------- |
   | **Naam** | Een geldige naam|Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige namen.|
   | **Adresbereik (CIDR-blok)** | Een geldig bereik | De standaardwaarde is geschikt voor deze quickstart.|
   | **Netwerkbeveiligingsgroep** | Geen | De standaardwaarde is geschikt voor deze quickstart.|
   | **Routetabel** | Geen | De standaardwaarde is geschikt voor deze quickstart.|
   | **Service-eindpunten** | 0 geselecteerd | De standaardwaarde is geschikt voor deze quickstart.|
   | **Delegatie van subnet** | Geen | De standaardwaarde is geschikt voor deze quickstart.|

   ![Nieuw SQL Managed Instance-subnet voor client-VM](./media/connect-vm-instance-configure/new-subnet.png)

4. Selecteer **OK** om dit aanvullende subnet te maken in het SQL Managed Instance-VNet.

## <a name="create-a-vm-in-the-new-subnet"></a>Een virtuele machine maken in het nieuwe subnet 

In de volgende stappen wordt uitgelegd hoe u een virtuele machine maakt in het nieuwe subnet om verbinding te maken met SQL Managed Instance.

## <a name="prepare-the-azure-virtual-machine"></a>De Azure-VM voorbereiden

Omdat SQL Managed Instance in uw persoonlijke virtuele netwerk wordt geplaatst, moet u een Azure-VM maken met een geïnstalleerd SQL-clienthulpprogramma, zoals SQL Server Management Studio of Azure Data Studio. Met dit hulpprogramma kunt u verbinding maken met SQL Managed Instance en query's uitvoeren. In deze snelstart wordt SQL Server Management Studio gebruikt.

De eenvoudigste manier om een client-VM te maken met alle benodigde hulpprogramma's is door de Azure Resource Manager-sjablonen te gebruiken.

1. Zorg ervoor dat u bent aangemeld bij Azure Portal in een ander browsertabblad. Selecteer vervolgens de volgende knop om een client-VM te maken en installeer SQL Server Management Studio:

   [![Afbeelding met een knop met het label Implementeren naar Azure.](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json)

2. Vul het formulier in met behulp van de informatie in de volgende tabel:

   | Instelling| Voorgestelde waarde | Beschrijving |
   | ---------------- | ----------------- | ----------- |
   | **Abonnement** | Een geldig abonnement | Dit moet een abonnement zijn waarmee u nieuwe resources mag maken. |
   | **Resourcegroep** |De resourcegroep die u hebt opgegeven in de quickstart [Een beheerde instantie van Azure SQL Managed Instance maken](instance-create-quickstart.md)|In deze resourcegroep moet het VNet zijn opgenomen.|
   | **Locatie** | De locatie voor de resourcegroep | Deze waarde wordt ingevuld op basis van de geselecteerde resourcegroep. |
   | **Naam van virtuele machine**  | Een geldige naam | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige namen.|
   |**Gebruikersnaam van beheerder**|Een geldige gebruikersnaam|Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige namen. Maak geen gebruik van 'serverbeheerder' aangezien dit een rol is die op serverniveau is gereserveerd.<br>U gebruikt deze gebruikersnaam steeds wanneer u [verbinding maakt met de VM](#connect-to-the-virtual-machine).|
   |**Wachtwoord**|Een geldig wachtwoord|Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>U gebruikt dit wachtwoord steeds wanneer u [verbinding maakt met de VM](#connect-to-the-virtual-machine).|
   | **Grootte van de virtuele machine** | Elke geldige grootte | De standaardinstelling in deze sjabloon van **Standard_B2s** is voldoende voor deze quickstart. |
   | **Locatie**|[resourceGroup().location].| Wijzig deze waarde niet. |
   | **Naam van het virtuele netwerk**|Het virtuele netwerk waarin u de beheerde instantie hebt gemaakt|
   | **Subnetnaam**|De naam van het subnet dat u hebt gemaakt in de vorige procedure| Kies niet het subnet waarin u de beheerde instantie hebt gemaakt.|
   | **Locatie van artefacten** | [deployment().properties.templateLink.uri] | Wijzig deze waarde niet. |
   | **SAS-token voor artefactlocatie** | Leeg laten | Wijzig deze waarde niet. |

   ![client-vm maken](./media/connect-vm-instance-configure/create-client-sql-vm.png)

   Als u de voorgestelde VNet-naam en het standaardsubnet in [Uw beheerde instantie van Azure SQL Managed Instance maken](instance-create-quickstart.md) hebt gebruikt, hoeft u de laatste twee parameters niet te wijzigen. Anders moet u deze waarden wijzigen in de waarden die u hebt ingevoerd bij het instellen van de netwerkomgeving.

3. Schakel het selectievakje **Ik ga akkoord met de bovenstaande voorwaarden** in.
4. Selecteer **Kopen** om de Azure-VM in uw netwerk te implementeren.
5. Selecteer het pictogram voor **Meldingen** om de status van de implementatie te bekijken.

> [!IMPORTANT]
> Ga pas ongeveer 15 minuten na het maken van de virtuele machine verder, zodat na het maken van de scripts voldoende tijd beschikbaar is om SQL Server Management Studio te installeren.

## <a name="connect-to-the-virtual-machine"></a>Verbinding maken met de virtuele machine

In de volgende stappen wordt uitgelegd hoe u verbinding maakt met uw nieuwe virtuele machine via een Extern bureaublad-verbinding.

1. Nadat de implementatie is voltooid, gaat u naar de virtuele machine-resource.

    ![Schermafbeelding waarin Azure Portal wordt getoond met de overzichtspagina voor een geselecteerde virtuele machine waarbij Verbinding maken is gemarkeerd.](./media/connect-vm-instance-configure/vm.png)  

2. Selecteer **Verbinding maken**.

   Een Remote Desktop Protocol-bestand (RDP-bestand) wordt weergegeven met het openbare IP-adres en het poortnummer voor de virtuele machine.

   ![RDP-formulier](./media/connect-vm-instance-configure/rdp.png)  

3. Selecteer **RDP-bestand downloaden**.

   > [!NOTE]
   > U kunt ook SSH gebruiken om verbinding te maken met uw virtuele machine.

4. Sluit het formulier **Verbinding maken met virtuele machine**.
5. Open het gedownloade RDP-bestand om verbinding met de VM te maken.
6. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Op een Mac hebt u een RDP-client nodig, zoals [deze Extern-bureaubladclient](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) uit de Mac App Store.

7. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine en selecteer vervolgens **OK**.

8. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Kies **Ja** of **Doorgaan** om door te gaan met de verbinding.

U bent verbonden met de virtuele machine in het Serverbeheer-dashboard.

## <a name="connect-to-sql-managed-instance"></a>Verbinding maken met beheerd SQL-exemplaar 

1. Open SQL Server Management Studio op de virtuele machine.

   Het duurt even voordat dit wordt geopend, omdat de configuratie moet worden uitgevoerd aangezien dit de eerste keer is dat SSMS is gestart.
2. Voer in het dialoogvenster **Verbinding maken met de server** in het vak **Servernaam** de volledig gekwalificeerde **hostnaam** in voor uw beheerde instantie. Selecteer **SQL Server-verificatie**, geef uw gebruikersnaam en wachtwoord op, en selecteer vervolgens **Verbinding maken**.

    ![SSMS verbinden](./media/connect-vm-instance-configure/ssms-connect.png)  

Nadat u verbinding hebt gemaakt, kunt u uw systeem en gebruikersdatabases bekijken in het knooppunt Databases. Daarnaast kunt u diverse objecten bekijken in de knooppunten Beveiliging, Server-objecten, Replicatie, Beheer, SQL Server Agent en XEvent Profiler.

## <a name="next-steps"></a>Volgende stappen

- Zie [Een punt-naar-site-verbinding configureren](point-to-site-p2s-configure.md) voor een quickstart over verbinding maken vanaf een on-premises clientcomputer met behulp van een punt-naar-site-verbinding.
- Zie [Uw toepassingen verbinden met SQL Managed Instance](connect-application-instance.md) voor een overzicht van de verbindingsopties voor toepassingen.
- Als u een bestaande SQL Server-database wilt herstellen van on-premises naar een beheerd exemplaar, kunt u [Azure Database Migration Service voor migratie](../../dms/tutorial-sql-server-to-managed-instance.md) of de [opdracht T-SQL RESTORE](restore-sample-database-quickstart.md) gebruiken om de database te herstellen vanuit een back-upbestand voor de database.
