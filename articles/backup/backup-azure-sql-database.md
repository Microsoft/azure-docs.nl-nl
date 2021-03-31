---
title: Een back-up van SQL Server-databases maken in Azure
description: In dit artikel wordt uitgelegd hoe u een back-up maakt van SQL Server naar Azure. In het artikel wordt ook uitgelegd hoe u SQL Server kunt herstellen.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 510d9637031928e31abaa5f82a5bf58c6ef44719
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91316835"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Over SQL Server-back-ups in virtuele Azure-machines

[Azure backup](backup-overview.md) biedt een op een stroom gebaseerde, gespecialiseerde oplossing voor het maken van back-ups SQL Server die worden uitgevoerd in virtuele machines van Azure. Deze oplossing wordt afgestemd op de voor delen van een back-up van nul-infra structuur, lange termijn retentie en Centraal beheer van Azure Backup. Het biedt ook de volgende voor delen voor SQL Server:

1. Werkbelasting bewuste back-ups die ondersteuning bieden voor alle back-uptypen-volledig, differentieel en logboek
2. 15 minuten RPO (Recovery Point Objective) met veelvuldige logboek back-ups
3. Herstel naar een bepaald tijdstip, tot een seconde
4. Back-up en herstel van afzonderlijke database niveau

Raadpleeg de [ondersteunings matrix](sql-support-matrix.md#scenario-support)voor het weer geven van de scenario's voor back-up en herstel die nu worden ondersteund.

## <a name="backup-process"></a>Back-upproces

Deze oplossing maakt gebruik van de SQL Native Api's om back-ups te maken van uw SQL-data bases.

* Wanneer u de SQL Server virtuele machine hebt opgegeven die u wilt beveiligen en query's wilt uitvoeren voor de data bases erin, Azure Backup service een back-upextensie voor werk belasting op de VM installeren door de extensie `AzureBackupWindowsWorkload` .
* Deze uitbrei ding bestaat uit een coördinator en een SQL-invoeg toepassing. Hoewel de coördinator verantwoordelijk is voor het activeren van werk stromen voor verschillende bewerkingen, zoals het configureren van back-ups, back-ups maken en herstellen, is de invoeg toepassing verantwoordelijk voor de werkelijke gegevens stroom.
* Om data bases op deze VM te kunnen detecteren, maakt Azure Backup het account `NT SERVICE\AzureWLBackupPluginSvc` . Dit account wordt gebruikt voor back-up en herstel en vereist SQL sysadmin-machtigingen. Het `NT SERVICE\AzureWLBackupPluginSvc` account is een [virtueel service account](/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)en vereist daarom geen wachtwoord beheer. Azure Backup gebruikt het `NT AUTHORITY\SYSTEM` account voor database detectie/-informatie, zodat dit account een open bare aanmelding voor SQL moet zijn. Als u de SQL Server-VM niet hebt gemaakt vanuit de Azure Marketplace, ontvangt u mogelijk het foutbericht **UserErrorSQLNoSysadminMembership**. Volg in dit geval [deze instructies](#set-vm-permissions).
* Zodra u de beveiliging configureren voor de geselecteerde data bases hebt geactiveerd, stelt de back-upservice de coördinator in met de back-upschemaën en andere beleids Details, die de uitbrei ding lokaal op de virtuele machine opslaat.
* Op het geplande tijdstip communiceert de coördinator met de invoeg toepassing en begint deze met het streamen van de back-upgegevens van de SQL Server met VDI.  
* De invoeg toepassing verzendt de gegevens rechtstreeks naar de Recovery Services kluis, waardoor er geen staging-locatie nodig is. De gegevens worden versleuteld en opgeslagen door de Azure Backup-service in opslag accounts.
* Wanneer de gegevens overdracht is voltooid, bevestigt de coördinator de door Voer met de back-upservice.

  ![SQL-back-uparchitectuur](./media/backup-azure-sql-database/azure-backup-sql-overview.png)

## <a name="before-you-start"></a>Voordat u begint

Controleer voordat u begint of aan de volgende vereisten wordt voldaan:

1. Zorg ervoor dat er een SQL Server-exemplaar wordt uitgevoerd in Azure. U kunt [snel een SQL Server-exemplaar maken](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md) in de marketplace.
2. Bekijk de [functie overwegingen](sql-support-matrix.md#feature-considerations-and-limitations) en [scenario ondersteuning](sql-support-matrix.md#scenario-support).
3. [Lees de veelgestelde vragen](faq-backup-sql-server.md) over dit scenario.

## <a name="set-vm-permissions"></a>VM-machtigingen instellen

  Wanneer u detectie uitvoert op een SQL Server, Azure Backup de volgende handelingen uit:

* Voegt de extensie AzureBackupWindowsWorkload toe.
* Hiermee maakt u een NT SERVICE\AzureWLBackupPluginSvc-account om data bases op de virtuele machine te detecteren. Dit account wordt gebruikt voor back-up en herstel en vereist SQL sysadmin-machtigingen.
* Hiermee worden data bases gedetecteerd die worden uitgevoerd op een virtuele machine, Azure Backup gebruikt het NT AUTHORITY\SYSTEM-account. Dit account moet een open bare aanmelding bij SQL zijn.

Als u de SQL Server virtuele machine niet hebt gemaakt in azure Marketplace of als u SQL 2008 of 2008 R2 gebruikt, kan er een **UserErrorSQLNoSysadminMembership** -fout optreden.

Zie [hier](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)voor meer informatie over het verlenen van machtigingen in het geval van **SQL 2008** en **2008 R2** die worden uitgevoerd op Windows 2008 R2.

Voor alle andere versies herstelt u de machtigingen met de volgende stappen:

  1. Meld u bij SQL Server Management Studio (SSMS) aan met een account met systeembeheerdersrechten voor SQL Server. Windows-verificatie zou moeten werken, tenzij u speciale machtigingen nodig hebt.
  2. Open de map **Security/Logins** op de SQL-server.

      ![De map Security/Logins openen om accounts te bekijken](./media/backup-azure-sql-database/security-login-list.png)

  3. Klik met de rechter muisknop op de map **logins** en selecteer **nieuwe login**. In **Aanmelding - Nieuw** selecteert u **Zoeken**.

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
> Als uw SQL Server meerdere exemplaren van SQL Server heeft geïnstalleerd, moet u de sysadmin-machtiging voor het **NT Service\AzureWLBackupPluginSvc** -account toevoegen aan alle SQL-exemplaren.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>SQL sysadmin-machtigingen verlenen voor SQL 2008 en SQL 2008 R2

**NT AUTHORITY\SYSTEM** -en **NT Service\AzureWLBackupPluginSvc** -aanmeldingen toevoegen aan het SQL Server-exemplaar:

1. Ga naar het SQL Server-exemplaar in object Verkenner.
2. Navigeer naar beveiliging-> aanmeldingen
3. Klik met de rechter muisknop op de aanmeldingen en selecteer *nieuwe aanmelding...*

    ![Nieuwe aanmelding met behulp van SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Ga naar het tabblad Algemeen en voer **NT AUTHORITY\SYSTEM** in als aanmeldings naam.

    ![Aanmeldings naam voor SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Ga naar *Server rollen* en kies *open bare* en *sysadmin* -rollen.

    ![Rollen kiezen in SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Ga naar *status*. *Verleen* de machtiging om verbinding te maken met data base-engine en meld u aan als *ingeschakeld*.

    ![Machtigingen verlenen in SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Selecteer OK.
8. Herhaal dezelfde reeks stappen (1-7 hierboven) om NT Service\AzureWLBackupPluginSvc-aanmelding toe te voegen aan het SQL Server-exemplaar. Als de aanmelding al bestaat, moet u ervoor zorgen dat deze de serverrol sysadmin heeft en onder status heeft de machtiging verlenen om verbinding te maken met de data base-engine en de aanmelding als ingeschakeld.
9. Nadat u een machtiging hebt verleend, **detecteert u db's** in de portal: kluis **->** back-upinfrastructuur **->** in azure VM:

    ![Db's in Azure Portal opnieuw detecteren](media/backup-azure-sql-database/sql-rediscover-dbs.png)

U kunt ook automatiseren om de machtigingen te verlenen door de volgende Power shell-opdrachten uit te voeren in de beheer modus. De naam van het exemplaar is standaard ingesteld op MSSQLserver. Wijzig het argument exemplaar naam in het script als dat nodig is:

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

* [Meer informatie over](backup-sql-server-database-azure-vms.md) het maken van back-ups van SQL server-data bases.
* [Meer informatie](restore-sql-database-azure-vm.md) over het herstellen van SQL Server-databases vanuit back-up.
* [Meer informatie](manage-monitor-sql-database-backup.md) over het beheren van SQL Server-databases vanuit back-up.
