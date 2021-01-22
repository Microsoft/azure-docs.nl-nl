---
title: Server parameters configureren-Azure Portal-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u MariaDB-server parameters in Azure Database for MariaDB kunt configureren met behulp van de Azure Portal.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: cda6b2bd3d49bd71b4c57d5d459c07b61addb493
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664797"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-the-azure-portal"></a>Configureer Server parameters in Azure Database for MariaDB met behulp van de Azure Portal

Azure Database for MariaDB ondersteunt de configuratie van sommige server parameters. In dit artikel wordt beschreven hoe u deze para meters configureert met behulp van de Azure Portal. Niet alle server parameters kunnen worden aangepast.

>[!Note]
> Serverparameters kunnen globaal worden bijgewerkt op serverniveau via de [Azure CLI](./howto-configure-server-parameters-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md) of [Azure Portal](./howto-server-parameters.md).

## <a name="configure-server-parameters"></a>Parameters van de server configureren

1. Meld u aan bij de Azure Portal en zoek vervolgens de Azure Database for MariaDB-server.
2. Klik onder de sectie **instellingen** op **server parameters** om de pagina server parameters voor de Azure database for MariaDB-server te openen.
![Pagina Azure Portal Server parameters](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Zoek alle instellingen die u moet aanpassen. Bekijk de kolom **Beschrijving** om inzicht te krijgen in het doel en de toegestane waarden.
![Vervolg keuzelijst opsommen](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klik op  **Opslaan** om uw wijzigingen op te slaan.
![Wijzigingen opslaan of negeren](./media/howto-server-parameters/4-save_parameters.png)
5. Als u nieuwe waarden voor de para meters hebt opgeslagen, kunt u altijd terugkeren naar de standaard waarden door **alles opnieuw instellen op de standaard** waarde te selecteren.
![Alles opnieuw instellen op standaard waarden](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="setting-parameters-not-listed"></a>Ingestelde para meters niet vermeld

Als de server parameter die u wilt bijwerken niet wordt weer gegeven in de Azure Portal, kunt u eventueel de para meter instellen op het verbindings niveau met `init_connect` . Hiermee stelt u de server parameters in voor elke client die verbinding maakt met de server. 

1. Klik onder de sectie **instellingen** op **server parameters** om de pagina server parameters voor de Azure database for MariaDB-server te openen.
2. Zoeken naar `init_connect`
3. Voeg de server parameters toe met de volgende indeling: `SET parameter_name=YOUR_DESIRED_VALUE` in waarde de kolom waarde.

    U kunt bijvoorbeeld de tekenset van uw server wijzigen door `init_connect` in te stellen op `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Klik op **Opslaan** om uw wijzigingen op te slaan.

## <a name="working-with-the-time-zone-parameter"></a>Werken met de para meter tijd zone

### <a name="populating-the-time-zone-tables"></a>De tijd zone tabellen worden gevuld

De tijdzone tabellen op uw server kunnen worden gevuld door de opgeslagen procedure aan te roepen `mysql.az_load_timezone` vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench.

> [!NOTE]
> Als u de opdracht uitvoert `mysql.az_load_timezone` vanuit MySQL Workbench, moet u de veilige update modus mogelijk eerst uitschakelen met `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> U moet de server opnieuw opstarten om te controleren of de tabellen van de tijd zone juist zijn ingevuld. Gebruik de [Azure Portal](howto-restart-server-portal.md) of [cli](howto-restart-server-cli.md)om de server opnieuw op te starten.
Voer de volgende opdracht uit om de beschik bare tijd zone waarden weer te geven:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>De tijd zone van het globale niveau instellen

De tijd zone op het globale niveau kan worden ingesteld op de pagina **server parameters** in de Azure Portal. In het onderstaande voor de globale tijd zone wordt de waarde ' VS/Pacific ' ingesteld.

![De para meter voor de tijd zone instellen](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>De tijd zone op sessie niveau instellen

De tijd zone op sessie niveau kan worden ingesteld door de `SET time_zone` opdracht uit te voeren vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench. In het volgende voor beeld wordt de tijd zone ingesteld op de **Amerikaanse/Pacific-** tijd zone.

```sql
SET time_zone = 'US/Pacific';
```

Raadpleeg de MariaDB-documentatie voor [datum-en tijd functies](https://mariadb.com/kb/en/library/convert_tz/).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [server parameters](concepts-server-parameters.md)
