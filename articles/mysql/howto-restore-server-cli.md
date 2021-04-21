---
title: Back-up en herstel - Azure CLI - Azure Database for MySQL
description: Meer informatie over het maken van een back-up en het herstellen van een server in Azure Database for MySQL met behulp van de Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8c8b0f37729ea20a62838d736dbed59f05c584c6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780385"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Een back-up maken van een server in Azure Database for MySQL azure CLI

Azure Database for MySQL-servers worden periodiek back-up gemaakt om herstelfuncties in te kunnenschakelen. Met deze functie kunt u de server en alle databases op een eerder tijdstip op een nieuwe server herstellen.

## <a name="prerequisites"></a>Vereisten

U kunt deze handleiding als volgende voltooien:

- U hebt een [Azure Database for MySQL server en database nodig.](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Voor dit artikel is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="set-backup-configuration"></a>Back-upconfiguratie instellen

U kunt kiezen tussen het configureren van uw server voor lokaal redundante back-ups of geografisch redundante back-ups bij het maken van de server. 

> [!NOTE]
> Nadat een server is gemaakt, kan het soort redundantie dat deze heeft, geografisch redundant versus lokaal redundant, niet worden omgeschakeld.
>

Tijdens het maken van een server via `az mysql server create` de opdracht, `--geo-redundant-backup` bepaalt de parameter uw optie voor back-up redundantie. Als `Enabled` , worden geografisch redundante back-ups gemaakt. Of als `Disabled` lokaal redundante back-ups worden gemaakt. 

De bewaarperiode voor back-ups wordt ingesteld met de parameter `--backup-retention` . 

Zie voor meer informatie over het instellen van deze waarden tijdens het maken Azure Database for MySQL [CLI-snelstart.](quickstart-create-mysql-server-database-using-azure-cli.md)

De bewaarperiode voor back-ups van een server kan als volgt worden gewijzigd:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

In het voorgaande voorbeeld wordt de bewaarperiode voor back-ups van mydemoserver gewijzigd in 10 dagen.

De bewaarperiode voor back-ups bepaalt hoe ver terug in de tijd een herstel naar een bepaald tijdstip kan worden opgehaald, omdat deze is gebaseerd op beschikbare back-ups. Herstel naar een bepaald tijdstip wordt verder beschreven in de volgende sectie.

## <a name="server-point-in-time-restore"></a>Herstel naar een bepaald tijdstip van de server
U kunt de server herstellen naar een eerder tijdstip. De herstelde gegevens worden gekopieerd naar een nieuwe server en de bestaande server wordt in de huidige staat gelaten. Als een tabel bijvoorbeeld per ongeluk om twaalf uur 's middags wordt verwijderd, kunt u deze herstellen naar de tijd net vóór 12 uur 's middags. Vervolgens kunt u de ontbrekende tabel en gegevens ophalen van de herstelde kopie van de server. 

Gebruik de Azure CLI-opdracht [az mysql server restore om](/cli/azure/mysql/server#az_mysql_server_restore) de server te herstellen.

### <a name="run-the-restore-command"></a>De herstelopdracht uitvoeren

Voer bij de Azure CLI-opdrachtprompt de volgende opdracht in om de server te herstellen:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

Voor `az mysql server restore` de opdracht zijn de volgende parameters vereist:

| Instelling | Voorgestelde waarde | Beschrijving  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  De resourcegroep waar de bronserver zich bevindt.  |
| naam | mydemoserver-restored | De naam van de nieuwe server die door de opdracht restore is gemaakt. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Selecteer een tijdstip om naar te herstellen. Deze datum en tijd moet binnen de back-upretentieperiode van de bronserver vallen. Gebruik de iso8601-datum- en tijdnotatie. U kunt bijvoorbeeld uw eigen lokale tijdzone gebruiken, zoals `2018-03-13T05:59:00-08:00` . U kunt ook de UTC Zulu-indeling gebruiken, bijvoorbeeld `2018-03-13T13:59:00Z` . |
| source-server | mydemoserver | De naam of ID van de bronserver voor het herstellen. |

Wanneer u een server naar een eerder tijdstip herstelt, wordt er een nieuwe server gemaakt. De oorspronkelijke server en de databases van het opgegeven tijdstip worden gekopieerd naar de nieuwe server.

De locatie en prijscategorie van de herstelde server zijn hetzelfde als die van de oorspronkelijke server. 

Nadat het herstelproces is voltooid, zoekt u de nieuwe server en controleert u of de gegevens correct zijn hersteld. De nieuwe server heeft dezelfde aanmeldingsnaam en hetzelfde wachtwoord voor de serverbeheerder die geldig was voor de bestaande server op het moment dat het herstel werd gestart. Het wachtwoord kan worden gewijzigd op de pagina **Overzicht** van de nieuwe server.

Nadat de herstelbewerking is uitgevoerd, zijn er bovendien twee serverparameters die na de herstelbewerking opnieuw worden ingesteld op de standaardwaarden (en niet van de primaire server worden gekopieerd)
*   time_zone: deze waarde moet worden ingesteld op STANDAARDwaarde **SYSTEEM**
*   event_scheduler: de event_scheduler is ingesteld op **UIT** op de herstelde server

U moet de waarde van de primaire server kopiëren en instellen op de herstelde server door de [serverparameter opnieuw te configureren](howto-server-parameters.md)

De nieuwe server die is gemaakt tijdens een herstelbewerking, bevat niet de VNet-service-eindpunten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server. Firewallregels van de oorspronkelijke server worden hersteld.

## <a name="geo-restore"></a>Geo-herstel
Als u uw server hebt geconfigureerd voor geografisch redundante back-ups, kan er een nieuwe server worden gemaakt op de back-up van die bestaande server. Deze nieuwe server kan worden gemaakt in elke regio Azure Database for MySQL beschikbaar is.  

Gebruik de Azure CLI-opdracht om een server te maken met behulp van een geografisch redundante `az mysql server georestore` back-up.

> [!NOTE]
> Wanneer een server voor het eerst wordt gemaakt, is deze mogelijk niet onmiddellijk beschikbaar voor geo-herstel. Het kan enkele uren duren voordat de benodigde metagegevens zijn ingevuld.
>

Voer bij de Azure CLI-opdrachtprompt de volgende opdracht in om de server geo-herstel uit te voeren:

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8 
```
Met deze opdracht maakt u een nieuwe server met de naam *mydemoserver-georestored* in VS - oost die bij *myresourcegroup hoort.* Het is een Algemeen Gen 5-server met 8 vCores. De server wordt gemaakt vanuit de geografisch redundante back-up *van mydemoserver*, die zich ook in de resourcegroep *myresourcegroup*

Als u de nieuwe server wilt maken in een andere resourcegroep dan de bestaande server, moet u in de parameter de servernaam kwalificeren zoals `--source-server` in het volgende voorbeeld:

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

Voor `az mysql server georestore` de opdracht zijn de volgende parameters vereist:

| Instelling | Voorgestelde waarde | Beschrijving  |
| --- | --- | --- |
|resource-group| myResourceGroup | De naam van de resourcegroep waar de nieuwe server bij hoort.|
|naam | mydemoserver-georestored | De naam van de nieuwe server. |
|source-server | mydemoserver | De naam van de bestaande server waarvan geografisch redundante back-ups worden gebruikt. |
|location | eastus | De locatie van de nieuwe server. |
|sku-name| GP_Gen5_8 | Met deze parameter stelt u de prijscategorie, het genereren van berekeningen en het aantal vCores van de nieuwe server in. GP_Gen5_8 is een Algemeen Gen 5-server met 8 vCores.|

Wanneer u een nieuwe server maakt op basis van geo-herstel, neemt deze dezelfde opslaggrootte en prijscategorie over als de bronserver. Deze waarden kunnen niet worden gewijzigd tijdens het maken. Nadat de nieuwe server is gemaakt, kan de opslaggrootte omhoog worden geschaald.

Nadat het herstelproces is voltooid, zoekt u de nieuwe server en controleert u of de gegevens correct zijn hersteld. De nieuwe server heeft dezelfde aanmeldingsnaam en hetzelfde wachtwoord als de aanmeldingsgegevens van de serverbeheerder die geldig waren voor de bestaande server op het moment dat het herstel werd gestart. Het wachtwoord kan worden gewijzigd op de pagina **Overzicht** van de nieuwe server.

De nieuwe server die is gemaakt tijdens een herstelbewerking, bevat niet de VNet-service-eindpunten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor deze nieuwe server. Firewallregels van de oorspronkelijke server worden hersteld.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de back-ups [van de service](concepts-backup.md)
- Meer informatie over [replica's](concepts-read-replicas.md)
- Meer informatie over opties [voor bedrijfscontinuïteit](concepts-business-continuity.md)
