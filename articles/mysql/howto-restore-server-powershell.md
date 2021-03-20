---
title: Backup en Restore-Azure PowerShell-Azure Database for MySQL
description: Meer informatie over het maken van een back-up en het herstellen van een server in Azure Database for MySQL met behulp van Azure PowerShell.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurepowershel
ms.topic: how-to
ms.date: 4/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 43ce39a1fc05c8ffedd1ae8404cc20c1a498a73f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94539004"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-mysql-server-using-powershell"></a>Een back-up van Azure Database for MySQL-server maken en deze terugzetten met behulp van PowerShell

Er wordt regel matig een back-up van Azure Database for MySQL servers gemaakt om herstel functies in te scha kelen. Met deze functie kunt u de server en alle bijbehorende data bases naar een eerder tijdstip herstellen op een nieuwe server.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

- De [AZ Power shell-module](/powershell/azure/install-az-ps) die lokaal is geïnstalleerd of [Azure Cloud shell](https://shell.azure.com/) in de browser
- Een [Azure database for mysql server](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Zo lang de PowerShell-module Az.MySQL in preview is, moet u deze afzonderlijk van de Az-module van PowerShell installeren met behulp van de volgende opdracht: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Zodra de PowerShell-module Az.MySQL algemeen beschikbaar is, wordt het onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

Als u Power shell lokaal wilt gebruiken, maakt u verbinding met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>Back-upconfiguratie instellen

Bij het maken van de server maakt u de keuze tussen het configureren van uw server voor lokaal redundante of geografisch redundante back-ups.

> [!NOTE]
> Nadat een server is gemaakt, is het soort redundantie die het heeft, geografisch redundant versus lokaal redundant en kan niet worden gewijzigd.

Tijdens het maken van een server via de `New-AzMySqlServer` opdracht, bepaalt de para meter **GeoRedundantBackup** de optie voor de redundantie van back-ups. Als deze functie is **ingeschakeld**, worden er geo redundante back-ups gemaakt. Als deze functie is **uitgeschakeld**, worden er lokaal redundante back-ups gemaakt.

De Bewaar periode voor back-ups is ingesteld met de para meter **BackupRetentionDay** .

Zie [een Azure database for mysql-server maken met Power shell](quickstart-create-mysql-server-database-using-azure-powershell.md)voor meer informatie over het instellen van deze waarden tijdens het maken van een server.

De Bewaar periode van een back-up van een server kan als volgt worden gewijzigd:

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

In het vorige voor beeld wordt de Bewaar periode voor back-ups van mydemoserver gewijzigd in 10 dagen.

De Bewaar periode voor back-ups bepaalt hoe ver terug een herstel naar een bepaald tijdstip kan worden opgehaald, omdat het is gebaseerd op beschik bare back-ups. Herstel naar een bepaald tijdstip wordt verderop in de volgende sectie beschreven.

## <a name="server-point-in-time-restore"></a>Server herstel naar een bepaald tijdstip

U kunt de server naar een eerder tijdstip herstellen. De herstelde gegevens worden gekopieerd naar een nieuwe server. De bestaande server blijft ongewijzigd. Als een tabel bijvoorbeeld per ongeluk wordt verwijderd, kunt u de server herstellen naar een tijdstip net voordat de tabel werd verwijderd. Vervolgens kunt u de ontbrekende tabel en gegevens ophalen van de herstelde kopie van de server.

Als u de server wilt herstellen, gebruikt u de PowerShell-cmdlet `Restore-AzMySqlServer`.

### <a name="run-the-restore-command"></a>De herstelopdracht uitvoeren

Voer het volgende voorbeeld uit in PowerShell uit om de server te herstellen.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

De para meters set **PointInTimeRestore** van de `Restore-AzMySqlServer` cmdlet vereist de volgende para meters:

| Instelling | Voorgestelde waarde | Beschrijving  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  De resource groep waar de bron server zich bevindt.  |
| Name | mydemoserver-restored | De naam van de nieuwe server die door de opdracht restore is gemaakt. |
| RestorePointInTime | 2020-03-13T13:59:00Z | Selecteer een punt in de tijd om te herstellen. Deze datum en tijd moet binnen de back-upretentieperiode van de bronserver vallen. Gebruik de ISO8601 datum-en tijd notatie. U kunt bijvoorbeeld uw eigen lokale tijd zone gebruiken, zoals **2020-03-13T05:59:00-08:00**. U kunt ook de UTC Zulu-indeling gebruiken, bijvoorbeeld **2018-03-13T13:59:00Z**. |
| UsePointInTimeRestore | `<SwitchParameter>` | Gebruik de punt-in-time-modus om te herstellen. |

Wanneer u een server naar een eerder tijdstip herstelt, wordt er een nieuwe server gemaakt. De oorspronkelijke server en de bijbehorende databases van het opgegeven tijdstip worden naar de nieuwe server gekopieerd.

De locatie en prijscategorie van de herstelde server zijn hetzelfde als die van de oorspronkelijke server.

Nadat het herstelproces is voltooid, zoekt u de nieuwe server en controleert u of de gegevens correct zijn hersteld. De aanmeldingsnaam en het wachtwoord voor de nieuwe server zijn hetzelfde als voor de bestaande server op het moment dat de herstelbewerking werd gestart. Het wachtwoord kan worden gewijzigd op de pagina **Overzicht** van de nieuwe server.

De nieuwe server die is gemaakt tijdens een herstelbewerking, bevat niet de VNet-service-eindpunten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor de nieuwe server. Firewallregels van de oorspronkelijke server worden hersteld.

## <a name="geo-restore"></a>Geo-herstel

Als u uw server voor geografisch redundante back-ups hebt geconfigureerd, kan een nieuwe server worden gemaakt op basis van de back-up van de bestaande server. Deze nieuwe server kan worden gemaakt in elke regio die Azure Database for MySQL beschikbaar is.

Als u een server wilt maken met behulp van een geo-redundante back-up, gebruikt u de `Restore-AzMySqlServer` opdracht met de para meter **UseGeoRestore** .

> [!NOTE]
> Wanneer een server voor het eerst wordt gemaakt, is deze mogelijk niet onmiddellijk beschikbaar voor geo Restore. Het kan enkele uren duren voordat de benodigde meta gegevens zijn gevuld.

Als u geo-server wilt herstellen, voert u het volgende voor beeld uit in Power shell:

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

In dit voor beeld wordt een nieuwe-server met de naam **mydemoserver-restored** gemaakt in de regio VS-Oost die hoort bij **myresourcegroup**. Het is een Algemeen, Gen 5-server met 8 vCores. De-server wordt gemaakt op basis van de geo-redundante back-up van **mydemoserver**, ook in de resource groep **myresourcegroup**.

Als u de nieuwe server in een andere resource groep wilt maken op basis van de bestaande server, geeft u de naam van de nieuwe resource groep op met behulp van de para meter **ResourceGroupName** , zoals weer gegeven in het volgende voor beeld:

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Voor de para meters set **Restore** van de `Restore-AzMySqlServer` cmdlet zijn de volgende para meters vereist:

| Instelling | Voorgestelde waarde | Beschrijving  |
| --- | --- | --- |
|ResourceGroupName | myResourceGroup | De naam van de resource groep waartoe de nieuwe server behoort.|
|Name | mydemoserver-geoterugzet bewerking | De naam van de nieuwe server. |
|Locatie | eastus | De locatie van de nieuwe server. |
|UseGeoRestore | `<SwitchParameter>` | Gebruik de geo-modus om te herstellen. |

Wanneer u een nieuwe server maakt met behulp van geo Restore, neemt deze dezelfde opslag grootte en prijs categorie als de bron server op, tenzij de **SKU** -para meter is opgegeven.

Nadat het herstelproces is voltooid, zoekt u de nieuwe server en controleert u of de gegevens correct zijn hersteld. De aanmeldingsnaam en het wachtwoord voor de nieuwe server zijn hetzelfde als voor de bestaande server op het moment dat de herstelbewerking werd gestart. Het wachtwoord kan worden gewijzigd op de pagina **Overzicht** van de nieuwe server.

De nieuwe server die is gemaakt tijdens een herstelbewerking, bevat niet de VNet-service-eindpunten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server. Firewallregels van de oorspronkelijke server worden hersteld.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Azure Database for MariaDB-verbindingsreeks genereren met PowerShell](howto-connection-string-powershell.md)
