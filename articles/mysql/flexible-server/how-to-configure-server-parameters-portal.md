---
title: Server parameters configureren-Azure Portal-Azure Database for MySQL flexibele server
description: In dit artikel wordt beschreven hoe u MySQL-server parameters configureert in Azure Database for MySQL flexibele server met behulp van de Azure Portal.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 7733a6211363b4f1c9e9006f757b4d152c7af7f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94489553"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Configureer Server parameters in Azure Database for MySQL-flexibele server met behulp van de Azure Portal

> [!IMPORTANT] 
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

U kunt Azure Database for MySQL flexibele server configuratie beheren met server parameters. De server parameters worden geconfigureerd met de standaard-en aanbevolen waarde bij het maken van de server.  

In dit artikel wordt beschreven hoe u server parameters kunt weer geven en configureren met behulp van de Azure Portal. Op de Blade Server parameters op Azure Portal worden zowel de para meter wijzigen die kan worden gewijzigd en niet kan worden gewijzigd. De niet-aanpas bare server parameters worden grijs weer gegeven.

>[!Note]
> Server parameters kunnen globaal worden bijgewerkt op server niveau, gebruik dan [Azure cli](./how-to-configure-server-parameters-cli.md) of [Azure Portal](./how-to-configure-server-parameters-portal.md).

## <a name="configure-server-parameters"></a>Parameters van de server configureren

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en zoek vervolgens uw flexibele Azure database for mysql-server.
2. Klik onder de sectie **instellingen** op **server parameters** om de pagina server parameters voor de Azure database for MySQL flexibele server te openen.
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Pagina Azure Portal Server parameters":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. Zoek een wille keurige server parameter op die u wilt aanpassen. Bekijk de kolom **Beschrijving** om inzicht te krijgen in het doel en de toegestane waarden.
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="Vervolg keuzelijst opsommen":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. Klik op  **Opslaan** om uw wijzigingen op te slaan.
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="Wijzigingen opslaan of negeren":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. De statische para meters die vereist zijn voor het opnieuw opstarten van de server, moeten van kracht worden. Als u de statische para meter wijzigt, wordt u gevraagd de **computer nu opnieuw** op te starten of **later opnieuw** op te starten.
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="Opnieuw opstarten bij statisch opslaan van para meters":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. Als u nieuwe waarden voor de para meters hebt opgeslagen, kunt u altijd terugkeren naar de standaard waarden door **alles opnieuw instellen op de standaard** waarde te selecteren.
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="Alles opnieuw instellen op standaard waarden":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## <a name="setting-non-modifiable-server-parameters"></a>Niet-aanpas bare server parameters instellen

Als de para meter van de server die u wilt bijwerken niet kan worden gewijzigd, kunt u eventueel de para meter instellen op het verbindings niveau met `init_connect` . Hiermee stelt u de server parameters in voor elke client die verbinding maakt met de server. 

1. Klik onder de sectie **instellingen** op **server parameters** om de pagina server parameters voor de Azure database for mysql-server te openen.
2. Zoeken naar `init_connect`
3. Voeg de server parameters toe met de volgende indeling: `SET parameter_name=YOUR_DESIRED_VALUE` in waarde de kolom waarde.

    U kunt bijvoorbeeld de tekenset van uw server wijzigen door `init_connect` in te stellen op `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;`
4. Klik op **Opslaan** om uw wijzigingen op te slaan.

>[!Note]
> `init_connect` kan worden gebruikt om parameters te wijzigen die geen SUPER-bevoegdhed(en) op sessieniveau vereisen. Als u wilt weten of u de parameter kunt instellen met `init_connect`, voert u de opdracht `set session parameter_name=YOUR_DESIRED_VALUE;` uit. Als er fouten optreden met het foutbericht **Toegang geweigerd; u hebt SUPER-privileges(s) nodig**, dan kunt u de parameter niet instellen met init_connect.

## <a name="working-with-the-time-zone-parameter"></a>Werken met de para meter tijd zone

### <a name="populating-the-time-zone-tables"></a>De tijd zone tabellen worden gevuld

De tijdzone tabellen op uw server kunnen worden gevuld door de opgeslagen procedure aan te roepen `mysql.az_load_timezone` vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench.

> [!NOTE]
> Als u de opdracht uitvoert `mysql.az_load_timezone` vanuit MySQL Workbench, moet u de veilige update modus mogelijk eerst uitschakelen met `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> U moet de server opnieuw opstarten om te controleren of de tabellen van de tijd zone juist zijn ingevuld.<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

Voer de volgende opdracht uit om de beschik bare tijd zone waarden weer te geven:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>De tijd zone van het globale niveau instellen

De tijd zone op het globale niveau kan worden ingesteld op de pagina **server parameters** in de Azure Portal. In het onderstaande voor de globale tijd zone wordt de waarde ' VS/Pacific ' ingesteld.

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="De para meter voor de tijd zone instellen":::](./media/how-to-server-parameters/timezone.png#lightbox)

### <a name="setting-the-session-level-time-zone"></a>De tijd zone op sessie niveau instellen

De tijd zone op sessie niveau kan worden ingesteld door de `SET time_zone` opdracht uit te voeren vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench. In het volgende voor beeld wordt de tijd zone ingesteld op de **Amerikaanse/Pacific-** tijd zone.

```sql
SET time_zone = 'US/Pacific';
```

Raadpleeg de MySQL-documentatie voor [datum-en tijd functies](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).

## <a name="next-steps"></a>Volgende stappen

- [Server parameters configureren in azure cli](./how-to-configure-server-parameters-cli.md)
