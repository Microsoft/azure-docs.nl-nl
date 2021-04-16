---
title: Een back-up van SQL Server-databases maken in Azure
description: In dit artikel wordt uitgelegd hoe u een back-up van SQL Server azure kunt maken. In het artikel wordt ook uitgelegd hoe u SQL Server kunt herstellen.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b6daf631248958948e799b20284d84a1e59e5dfe
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518861"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Over SQL Server-back-ups in virtuele Azure-machines

[Azure Backup](backup-overview.md) biedt een op stream gebaseerde, gespecialiseerde oplossing voor het maken van back-SQL Server uitgevoerd in Azure-VM's. Deze oplossing is afgestemd op Azure Backup voordelen van back-ups van een infrastructuur zonder infrastructuur, langetermijnretentie en centraal beheer. Het biedt bovendien de volgende voordelen specifiek voor SQL Server:

1. Workloadbewuste back-ups die ondersteuning bieden voor alle back-uptypen: volledig, differentieel en logboek
2. RPO van 15 minuten (recovery point objective) met regelmatige logboekback-ups
3. Herstel naar een bepaald tijdstip tot een seconde
4. Back-up en herstel op databaseniveau

Raadpleeg de ondersteuningsmatrix om de back-up- en herstelscenario's weer te geven die momenteel [worden ondersteund.](sql-support-matrix.md#scenario-support)

## <a name="backup-process"></a>Back-upproces

Deze oplossing maakt gebruik van de systeemeigen SQL-API's om back-ups van uw SQL-databases te maken.

* Zodra u de SQL Server-VM opgeeft die u wilt beveiligen en query's wilt uitvoeren voor de databases in de VM, installeert Azure Backup-service een back-upextensie voor workloads op de VM met de `AzureBackupWindowsWorkload` naamextensie.
* Deze extensie bestaat uit een coördinator en een SQL-invoegvoeging. Hoewel de coördinator verantwoordelijk is voor het activeren van werkstromen voor verschillende bewerkingen, zoals back-up, back-up en herstel configureren, is de invoeg-app verantwoordelijk voor de werkelijke gegevensstroom.
* Als u databases op deze VM wilt kunnen vinden, maakt Azure Backup account `NT SERVICE\AzureWLBackupPluginSvc` . Dit account wordt gebruikt voor back-up en herstel en vereist SQL sysadmin-machtigingen. Het `NT SERVICE\AzureWLBackupPluginSvc` account is een virtueel [serviceaccount](/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)en vereist dus geen wachtwoordbeheer. Azure Backup maakt gebruik van `NT AUTHORITY\SYSTEM` het account voor databasedetectie/-query's, dus dit account moet een openbare aanmelding bij SQL zijn. Als u de SQL Server-VM niet hebt gemaakt vanuit de Azure Marketplace, ontvangt u mogelijk het foutbericht **UserErrorSQLNoSysadminMembership**. Volg in dit geval [deze instructies](#set-vm-permissions).
* Zodra u de beveiliging voor de geselecteerde databases hebt geconfigureerd, stelt de back-upservice de coördinator in met de back-upschema's en andere beleidsdetails, die de extensie lokaal op de VM in de cache ophaalt.
* Op het geplande tijdstip communiceert de coördinator met de invoegserver en begint deze met het streamen van de back-upgegevens van de SQL-server met behulp van VDI.  
* De invoegserver verzendt de gegevens rechtstreeks naar de Recovery Services-kluis, waardoor een faseringslocatie niet meer nodig is. De gegevens worden versleuteld en opgeslagen door de Azure Backup-service in opslagaccounts.
* Wanneer de gegevensoverdracht is voltooid, bevestigt de coördinator de doorvoer met de back-upservice.

  ![SQL Backup-architectuur](./media/backup-azure-sql-database/azure-backup-sql-overview.png)

## <a name="before-you-start"></a>Voordat u begint

Controleer voordat u begint de volgende vereisten:

1. Zorg ervoor dat er een SQL Server-exemplaar wordt uitgevoerd in Azure. U kunt [snel een SQL Server-exemplaar maken](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md) in de marketplace.
2. Bekijk de [functieoverwegingen en](sql-support-matrix.md#feature-considerations-and-limitations) [ondersteuning voor scenario's.](sql-support-matrix.md#scenario-support)
3. [Lees de veelgestelde vragen](faq-backup-sql-server.yml) over dit scenario.

## <a name="set-vm-permissions"></a>VM-machtigingen instellen

  Wanneer u detectie op een SQL Server, Azure Backup het volgende:

* Voegt de extensie AzureBackupWindowsWorkload toe.
* Hiermee maakt u een NT SERVICE\AzureWLBackupPluginSvc-account om databases op de virtuele machine te ontdekken. Dit account wordt gebruikt voor back-up en herstel en vereist sql sysadmin-machtigingen.
* Detecteert databases die worden uitgevoerd op een virtuele Azure Backup gebruikt het NT AUTHORITY\SYSTEM-account. Dit account moet een openbare aanmelding bij SQL zijn.

Als u de SQL Server-VM niet hebt gemaakt in Azure Marketplace of als u SQL 2008 of 2008 R2 gebruikt, ontvangt u mogelijk de fout **UserErrorSQLNoSysadminMembership.**

Als u machtigingen wilt verlenen in het geval van **SQL 2008** en **2008 R2** in Windows 2008 R2, raadpleegt u [hier](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

Voor alle andere versies kunt u machtigingen herstellen met de volgende stappen:

  1. Meld u bij SQL Server Management Studio (SSMS) aan met een account met systeembeheerdersrechten voor SQL Server. Windows-verificatie zou moeten werken, tenzij u speciale machtigingen nodig hebt.
  2. Open de map **Security/Logins** op de SQL-server.

      ![De map Security/Logins openen om accounts te bekijken](./media/backup-azure-sql-database/security-login-list.png)

  3. Klik met de rechtermuisknop **op de map Aanmeldingen** en selecteer **Nieuwe aanmelding.** In **Aanmelding - Nieuw** selecteert u **Zoeken**.

      ![In het dialoogvenster Aanmelding - Nieuw selecteert u Zoeken](./media/backup-azure-sql-database/new-login-search.png)

  4. Het virtuele Windows-serviceaccount **NT SERVICE\AzureWLBackupPluginSvc** is gemaakt tijdens de registratie van de virtuele machine en de SQL-detectiefase. Geef de accountnaam op, zoals wordt weergegeven in **Objectnaam invoeren die moet worden geselecteerd**. Selecteer **Namen controleren** om de naam te herleiden. Selecteer **OK**.

      ![Namen controleren selecteren om de naam van de onbekende service te herleiden](./media/backup-azure-sql-database/check-name.png)

  5. Controleer of in **Serverrollen** de rol **sysadmin** is geselecteerd. Selecteer **OK**. De vereiste machtigingen moeten nu bestaan.

      ![Controleren of de serverfunctie sysadmin is geselecteerd](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Koppel de database nu aan de Recovery Services-kluis. Klik in de Azure-portal in de lijst **Beschermde servers** met de rechtermuisknop op de server met een foutstatus > **DB's opnieuw detecteren**.

      ![Controleren of de server de juiste machtigingen heeft](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Controleer de voortgang in het gebied **Meldingen**. Wanneer de geselecteerde databases zijn gevonden, wordt er een slagingsbericht weergegeven.

      ![Bericht dat de implementatie is geslaagd](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Als op uw SQL Server meerdere exemplaren van SQL Server zijn geïnstalleerd, moet u de machtiging sysadmin voor **NT Service\AzureWLBackupPluginSvc** toevoegen aan alle SQL-exemplaren.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>SQL sysadmin-machtigingen geven voor SQL 2008 en SQL 2008 R2

Voeg **NT AUTHORITY\SYSTEM en** **NT Service\AzureWLBackupPluginSvc-aanmeldingen** toe aan het SQL Server-exemplaar:

1. Ga naar SQL Server-exemplaar in objectverkenner.
2. Navigeer naar Security -> Logins
3. Klik met de rechtermuisknop op de aanmeldingen en selecteer *Nieuwe aanmelding...*

    ![Nieuwe aanmelding met SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Ga naar het tabblad Algemeen en voer **NT AUTHORITY\SYSTEM in** als de aanmeldingsnaam.

    ![Aanmeldingsnaam voor SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Ga naar *Serverfuncties* en kies *openbare* en *sysadmin-rollen.*

    ![Rollen kiezen in SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Ga naar *Status.* *Verleen de* machtiging om verbinding te maken met de database-engine en meld u aan *als Ingeschakeld.*

    ![Machtigingen verlenen in SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Selecteer OK.
8. Herhaal dezelfde reeks stappen (1-7 hierboven) om NT Service\AzureWLBackupPluginSvc-aanmelding toe te voegen aan het SQL Server exemplaar. Als de aanmelding al bestaat, moet u ervoor zorgen dat deze de serverrol sysadmin heeft en onder Status De machtiging verlenen om verbinding te maken met de database-engine en Aanmelden als Ingeschakeld.
9. Nadat u de machtiging hebt verleend, **herontdekt u** DEB's opnieuw in de portal: Workload van de back-upinfrastructuur van de kluis **->** in Azure **->** VM:

    ![TB's opnieuw ontdekken in Azure Portal](media/backup-azure-sql-database/sql-rediscover-dbs.png)

U kunt de machtigingen ook automatiseren door de volgende PowerShell-opdrachten uit te voeren in de beheermodus. De naam van het exemplaar is standaard ingesteld op MSSQLSERVER. Wijzig indien nodig het argument instance name in script:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over](backup-sql-server-database-azure-vms.md) het maken van back-SQL Server databases.
* [Meer informatie](restore-sql-database-azure-vm.md) over het herstellen van SQL Server-databases vanuit back-up.
* [Meer informatie](manage-monitor-sql-database-backup.md) over het beheren van SQL Server-databases vanuit back-up.
