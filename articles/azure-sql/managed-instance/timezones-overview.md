---
title: Tijd zones van Azure SQL Managed instance
description: Meer informatie over de tijdzone specificaties van Azure SQL Managed instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 10/12/2020
ms.openlocfilehash: b4ba5f50f5c66b404ec76128a828a1060328f81c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690988"
---
# <a name="time-zones-in-azure-sql-managed-instance"></a>Tijd zones in Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Coordinated Universal Time (UTC) is de aanbevolen tijd zone voor de gegevenslaag van cloud oplossingen. Azure SQL Managed instance biedt ook een keuze aan tijd zones om te voldoen aan de behoeften van bestaande toepassingen die datum-en tijd waarden opslaan en datum-en tijd functies aanroepen met een impliciete context van een specifieke tijd zone.

T-SQL-functies [, zoals getdate ()](/sql/t-sql/functions/getdate-transact-sql) of CLR-code, observeren de tijd zone die is ingesteld op instantie niveau. SQL Server Agent taken volgen ook schema's volgens de tijd zone van het exemplaar.

  > [!NOTE]
  > Azure SQL Database ondersteunt geen tijd zone-instellingen; de waarde wordt altijd UTC. Gebruik [op tijd zone](/sql/t-sql/queries/at-time-zone-transact-sql) in SQL database als u datum-en tijd gegevens in een niet-UTC-tijd zone wilt interpreteren.

## <a name="supported-time-zones"></a>Ondersteunde tijd zones

Een set ondersteunde tijd zones wordt overgenomen van het onderliggende besturings systeem van het beheerde exemplaar. Het wordt regel matig bijgewerkt om nieuwe tijdzone definities op te halen en wijzigingen aan te brengen in de bestaande.

Met het [beleid voor zomer tijd/tijd zone wijzigingen](/troubleshoot/windows-client/system-management-components/daylight-saving-time-help-support) wordt de historische nauw keurigheid van 2010 voorwaarts gegarandeerd.

Een lijst met namen van de ondersteunde tijd zones wordt weer gegeven via de [sys.time_zone_info](/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) systeem weergave.

## <a name="set-a-time-zone"></a>Een tijd zone instellen

Een tijd zone van een beheerd exemplaar kan alleen worden ingesteld tijdens het maken van een exemplaar. De standaardtijd zone is UTC.

  >[!NOTE]
  > De tijd zone van een bestaand beheerd exemplaar kan niet worden gewijzigd.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Stel de tijd zone in met behulp van de Azure Portal

Wanneer u para meters voor een nieuw exemplaar opgeeft, selecteert u een tijd zone in de lijst met ondersteunde tijd zones.
  
![Een tijd zone instellen tijdens het maken van een instantie](./media/timezones-overview/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

Geef de eigenschap timezoneId op in uw [Resource Manager-sjabloon](./create-template-quickstart.md) om de tijd zone in te stellen tijdens het maken van een exemplaar.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

Aan het einde van dit artikel vindt u een lijst met ondersteunde waarden voor de eigenschap timezoneId.

Als u niets opgeeft, wordt de tijd zone ingesteld op UTC.

## <a name="check-the-time-zone-of-an-instance"></a>De tijd zone van een exemplaar controleren

De functie [CURRENT_TIMEZONE](/sql/t-sql/functions/current-timezone-transact-sql) retourneert een weergave naam van de tijd zone van het exemplaar.

## <a name="cross-feature-considerations"></a>Overwegingen voor meerdere functies

### <a name="restore-and-import"></a>Herstellen en importeren

U kunt een back-upbestand herstellen of gegevens importeren naar een beheerd exemplaar vanuit een exemplaar of een server met verschillende instellingen voor de tijd zone. Zorg er daarom voor dat u dit doet. Analyseer het toepassings gedrag en de resultaten van de query's en rapporten, net als bij het overdragen van gegevens tussen twee SQL Server exemplaren met verschillende instellingen voor de tijd zone.

### <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip

Wanneer u een herstel naar een bepaald tijdstip uitvoert, wordt de tijd om te herstellen, geïnterpreteerd als UTC-tijd. Op deze manier worden wille keurige ambiguïteiten veroorzaakt door zomer tijd en zijn de mogelijke wijzigingen worden vermeden.

### <a name="auto-failover-groups"></a>Automatische failover-groepen

Het gebruik van dezelfde tijd zone in een primair en secundair exemplaar in een failovergroep wordt niet afgedwongen, maar we raden dit ten zeerste aan.

  >[!WARNING]
  > We raden u ten zeerste aan dezelfde tijd zone te gebruiken voor het primaire en secundaire exemplaar in een failovergroep. Als gevolg van bepaalde zeldzame use-cases kan dezelfde tijd zone niet worden toegepast op de primaire en secundaire instanties. Het is belang rijk om te begrijpen dat in het geval van een hand matige of automatische failover de oorspronkelijke tijd zone wordt bewaard.

## <a name="limitations"></a>Beperkingen

- De tijd zone van het bestaande beheerde exemplaar kan niet worden gewijzigd. Als tijdelijke oplossing maakt u een nieuw beheerd exemplaar met de juiste tijd zone en voert u vervolgens hand matig een back-up-en herstel bewerking uit. u kunt ook het beste een [herstel punt voor meerdere exemplaren](./point-in-time-restore.md?tabs=azure-portal#restore-an-existing-database)uitvoeren.
- Externe processen die vanuit de SQL Server Agent taken worden gestart, hebben geen tijd zone van het exemplaar.

## <a name="list-of-supported-time-zones"></a>Lijst met ondersteunde tijd zones

| **Tijd zone-ID** | **Weergave naam van de tijd zone** |
| --- | --- |
| Datum grens (standaard tijd) | (UTC-12:00) Internationale westelijke datum grens |
| UTC-11 | (UTC-11:00) Coordinated Universal Time-11 |
| Aleoeten standaard tijd) | (UTC-10:00) Aleoetenman eilanden |
| Hawaï (standaard tijd) | (UTC-10:00) Hawaï |
| Marquesas standaard tijd) | (UTC-09:30) Marquesasman eilanden |
| Alaska (standaard tijd) | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Coordinated Universal Time-09 |
| Pacific (standaard tijd, Mexico) | (UTC-08:00) Baja Californië |
| UTC-08 | (UTC-08:00) Coordinated Universal Time-08 |
| Pacific (standaard tijd) | (UTC-08:00) Pacific Time (VS & Canada) |
| US Mountain (standaard tijd) | (UTC-07:00) Arizona |
| Mountain (standaard tijd, Mexico) | (UTC-07:00) Chihuahua, La Paz, Mazatlán |
| Mountain (standaard tijd) | (UTC-07:00) Mountain Time (VS & Canada) |
| Centraal-Amerika (standaard tijd) | (UTC-06:00) Centraal-Amerika |
| Central (standaard tijd) | (UTC-06:00) Central Time (VS & Canada) |
| PaaS eiland (standaard tijd) | (UTC-06:00) PaaS eiland |
| Central (standaard tijd, Mexico) | (UTC-06:00) Guadalajara, Mexico City, Monterrey |
| Canada-centraal standaard tijd | (UTC-06:00) Saskatchewan |
| SA Pacific (standaard tijd) | (UTC-05:00) Bogotá, Lima, Quito, Rio Branco |
| Eastern (standaard tijd, Mexico) | (UTC-05:00) Chetumal |
| Eastern (standaard tijd) | (UTC-05:00) Eastern Time (VS & Canada) |
| Haïti (standaard tijd) | (UTC-05:00) Haïti |
| Cuba (standaard tijd) | (UTC-05:00) Havana |
| VS Eastern (standaard tijd) | (UTC-05:00) Indiana (Oost) |
| Turks-en Caicoseilanden (standaard tijd) | (UTC-05:00) Turks-en Caicos |
| Paraguay (standaard tijd) | (UTC-04:00) Asunción |
| Atlantic (standaard tijd) | (UTC-04:00) Atlantische tijd (Canada) |
| Venezuela (standaard tijd) | (UTC-04:00) Caracas |
| Centraal Brazilië (standaard tijd) | (UTC-04:00) Cuiaba |
| SA Western (standaard tijd) | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Pacific SA (standaard tijd) | (UTC-04:00) Santiago |
| Newfoundland (standaard tijd) | (UTC-03:30) Newfoundland |
| Tocantins (standaard tijd) | (UTC-03:00) Araguaina |
| E. Zuid-Amerika (standaard tijd) | (UTC-03:00) Brasilia |
| SA Eastern (standaard tijd) | (UTC-03:00) Cayenne, Fortaleza |
| Argentinië (standaard tijd) | (UTC-03:00) Buenos Aires |
| Groen land (standaard tijd) | (UTC-03:00) Heilbot |
| Monte video standaard tijd) | (UTC-03:00) Monte video |
| Magallanes standaard tijd) | (UTC-03:00) Punta Arenas |
| Saint-Pierre (standaard tijd) | (UTC-03:00) Saint-Pierre en Miquelon |
| Bahia standaard tijd) | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Coordinated Universal Time-02 |
| Mid-Atlantic standaard tijd | (UTC-02:00) Mid-Atlantic-oud |
| Azoren (standaard tijd) | (UTC-01:00) Der |
| Cabo Verde (standaard tijd) | (UTC-01:00) Cabo Verde |
| UTC | (UTC) Coordinated Universal Time |
| GMT (standaard tijd) | (UTC + 00:00) Dublin, Edinburgh, Lissabon, Londen |
| Greenwich (standaard tijd) | (UTC + 00:00) Monrovia, Reykjavik |
| G. Europa (standaard tijd) | (UTC + 01:00) Amsterdam, Berlijn, Bern, Rome, Stockholm, Wenen |
| Centraal-Europa (standaard tijd) | (UTC + 01:00) Belgrado, Bratislava, Boedapest, Ljubljana, Praag |
| Romaans (standaard tijd) | (UTC + 01:00) Brussel, kopen Hagen, Madrid, Parijs |
| Marokko (standaard tijd) | (UTC + 01:00) Casablanca |
| Sao Tomé (standaard tijd) | (UTC + 01:00) Sao Tomé |
| Centraal-Europa (standaard tijd) | (UTC + 01:00) Sarajevo, Skopje, Warschau, Zagreb |
| G. Centraal-Afrika (standaard tijd) | (UTC + 01:00) West-Centraal-Afrika |
| Jordanië (standaard tijd) | (UTC + 02:00) Amman |
| GTB (standaard tijd) | (UTC + 02:00) Athene, Boekarest |
| Midden-Oosten (standaard tijd) | (UTC + 02:00) Beiroet |
| Egypte (standaard tijd) | (UTC + 02:00) Cairo |
| E. Europa (standaard tijd) | (UTC + 02:00) Chisinau |
| Syrië standaard tijd | (UTC + 02:00) Damascus |
| West Bank (standaard tijd) | (UTC + 02:00) Gazastrook, Hebron |
| Zuid-Afrika (standaard tijd) | (UTC + 02:00) Harare, Pretoria |
| FLE (standaard tijd) | (UTC + 02:00) Helsinki, Kiev, Riga, Sofia, Tallinn, Vilnius |
| Israël (standaard tijd) | (UTC + 02:00) Jeruzalem |
| Kaliningrad (standaard tijd) | (UTC + 02:00) Kaliningrad |
| Soedan (standaard tijd) | (UTC + 02:00) Khartoem |
| Libië (standaard tijd) | (UTC + 02:00) Tripoli |
| Namibië (standaard tijd) | (UTC + 02:00) Wind hoek |
| Arabische standaard tijd | (UTC + 03:00) Bagdad |
| Turkije (standaard tijd) | (UTC + 03:00) Istanboel |
| Arabische standaard tijd | (UTC + 03:00) Koeweit, Riyad |
| Belarus (standaard tijd) | (UTC + 03:00) Minsk |
| Rusland (standaard tijd) | (UTC + 03:00) Moskou, St. Peters Burg |
| E. Afrika (standaard tijd) | (UTC + 03:00) Nairobi |
| Iran (standaard tijd) | (UTC + 03:30) Teheran |
| Arabisch (standaard tijd) | (UTC + 04:00) Abu Dhabi, Muscat |
| Astrachan standaard tijd) | (UTC + 04:00) Astrachan, Oeljanovsk |
| Azerbeidzjan (standaard tijd) | (UTC + 04:00) Bakoe |
| Rusland-tijd Zone 3 | (UTC + 04:00) Izjevsk, Samara |
| Mauritius (standaard tijd) | (UTC + 04:00) Poort Louis |
| Saratov standaard tijd) | (UTC + 04:00) Saratov |
| Georgië (standaard tijd) | (UTC + 04:00) Tbilisi |
| Volgograd (standaard tijd) | (UTC + 04:00) Volgograd |
| Kaukasus (standaard tijd) | (UTC + 04:00) Jerevan |
| Afghanistan (standaard tijd) | (UTC + 04:30) Kaboel |
| West Asia (standaard tijd) | (UTC + 05:00) Asjchabad, Tasjkent |
| Jekaterinenburg standaard tijd) | (UTC + 05:00) Jekaterinenburg |
| Pakistan (standaard tijd) | (UTC + 05:00) Islamabad, Karachi |
| India (standaard tijd) | (UTC + 05:30) Chennai, Kolkata, Mumbai, New Delhi |
| Sri Lanka (standaard tijd) | (UTC + 05:30) Sri Jayawardenepura |
| Nepal (standaard tijd) | (UTC + 05:45) Kathmandu |
| Centraal-Azië (standaard tijd) | (UTC + 06:00) Astana |
| Bangladesh (standaard tijd) | (UTC + 06:00) Dhaka |
| Omsk standaard tijd) | (UTC + 06:00) Omsk |
| Myanmar (standaard tijd) | (UTC + 06:30) Rangoon (Yangon) |
| Zuidoost-Azië (standaard tijd) | (UTC + 07:00) Bangkok, Hanoi, Jakarta |
| Altaj (standaard tijd) | (UTC + 07:00) Barnaoel, Gorno-Altaysk |
| G. Mongolië (standaard tijd) | (UTC + 07:00) Hovd |
| North Asia (standaard tijd) | (UTC + 07:00) Krasnoyarsk |
| Nvt. Centraal-Azië (standaard tijd) | (UTC + 07:00) Novosibirsk |
| Tomsk (standaard tijd) | (UTC + 07:00) Tomsk ( |
| China (standaard tijd) | (UTC + 08:00) Peking, Chongqing, Hongkong, Urumqi |
| Noord-Azië Oost (standaard tijd) | (UTC + 08:00) Irkoetsk |
| Singapore (standaard tijd) | (UTC + 08:00) Kuala Lumpur, Singapore |
| G. Australië (standaard tijd) | (UTC + 08:00) Perth |
| Taiwan (standaard tijd) | (UTC + 08:00) Taipei |
| Ulaanbaatar standaard tijd) | (UTC + 08:00) Ulaanbaatar |
| Australië Central W. (standaard tijd) | (UTC + 08:45) Eucla |
| Transbaikal (standaard tijd) | (UTC + 09:00) Tsjita |
| Tokyo (standaard tijd) | (UTC + 09:00) Osaka, Sapporo, Tokio |
| Noord-Korea (standaard tijd) | (UTC + 09:00) Pyongyang |
| Korea (standaard tijd) | (UTC + 09:00) Seoul |
| Jakoetsk standaard tijd) | (UTC + 09:00) Jakoetsk |
| Centraal. Australië (standaard tijd) | (UTC + 09:30) Adel aide |
| Centraal-Australië (standaard tijd) | (UTC + 09:30) Darwin |
| E. Australië (standaard tijd) | (UTC + 10:00) Brisbane |
| AUS Eastern (standaard tijd) | (UTC + 10:00) Canberra, Melbourne, Sydney |
| West Pacific (standaard tijd) | (UTC + 10:00) Guam, Port Moresby |
| Tasmanië (standaard tijd) | (UTC + 10:00) Hobart |
| Wladiwostok (standaard tijd) | (UTC + 10:00) Wladiwostok |
| Lord Howe (standaard tijd) | (UTC + 10:30) Lord Howe-eiland |
| Bougainville standaard tijd) | (UTC + 11:00) Bougainville-eiland |
| Rusland-tijd zone 10 | (UTC + 11:00) Chokurdakh |
| Magadan (standaard tijd) | (UTC + 11:00) Magadan |
| Norfolk (standaard tijd) | (UTC + 11:00) Norfolk eiland |
| Sakhalin standaard tijd) | (UTC + 11:00) Sakhalin |
| Central Pacific (standaard tijd) | (UTC + 11:00) Salomons eilanden, Nieuw-Caledonië |
| Rusland-tijd zone 11 | (UTC + 12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Nieuw-Zeeland (standaard tijd) | (UTC + 12:00) Auckland, Wellington |
| UTC + 12 | (UTC + 12:00) Coordinated Universal Time + 12 |
| Fiji (standaard tijd) | (UTC + 12:00) Fiji |
| Kamtsjatka (standaard tijd) | (UTC + 12:00) Petropavlovsk-Kamchatsky-oud |
| Chatham Eilanden (standaard tijd) | (UTC + 12:45) Chathamman eilanden |
| UTC + 13 | (UTC + 13:00) Coordinated Universal Time + 13 |
| Tonga (standaard tijd) | (UTC + 13:00) Nuku'alofa |
| Samoa (standaard tijd) | (UTC + 13:00) Samoa |
| Line-eilanden (standaard tijd) | (UTC + 14:00) Kiritimati-eiland |

## <a name="see-also"></a>Zie ook 

- [CURRENT_TIMEZONE (Transact-SQL)](/sql/t-sql/functions/current-timezone-transact-sql)
- [CURRENT_TIMEZONE_ID (Transact-SQL)](/sql/t-sql/functions/current-timezone-id-transact-sql)
- [OP tijd ZONE (Transact-SQL)](/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)