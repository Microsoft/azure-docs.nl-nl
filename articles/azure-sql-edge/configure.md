---
title: Azure SQL Edge configureren
description: Meer informatie over het configureren van Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 0c49f5ab9f10456c32f7f8516cba0e851fa80e74
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392330"
---
# <a name="configure-azure-sql-edge"></a>Azure SQL Edge configureren

Azure SQL Edge ondersteunt configuratie via een van de volgende twee opties:

- Omgevingsvariabelen
- Een MSSQL. conf-bestand dat in de map/var/opt/MSSQL is geplaatst

> [!NOTE]
> Als omgevings variabelen worden ingesteld, worden de instellingen overschreven die zijn opgegeven in het bestand MSSQL. conf.

## <a name="configure-by-using-environment-variables"></a>Configureren met behulp van omgevings variabelen

In Azure SQL Edge worden verschillende omgevings variabelen weer gegeven die kunnen worden gebruikt voor het configureren van de SQL Edge-container. Deze omgevings variabelen zijn een subset van de beschik bare voor SQL Server on Linux. Zie [omgevings variabelen](/sql/linux/sql-server-linux-configure-environment-variables/)voor meer informatie over SQL Server on Linux omgevings variabelen.

De volgende nieuwe omgevings variabelen zijn toegevoegd aan de Azure SQL-rand. 

| Omgevingsvariabele | Beschrijving | Waarden |     
|-----|-----| ---------- | 
| **PlanId** | Hiermee geeft u de Azure SQL Edge-SKU op die moet worden gebruikt tijdens de initialisatie. Deze omgevings variabele is alleen vereist bij het implementeren van Azure SQL Edge met behulp van Azure IoT Edge. | **ASDE-Developer-on-IOT-Edge** of **ASDE-Premium-on-IOT-Edge** | 
| **MSSQL_TELEMETRY_ENABLED** | Het verzamelen van gebruiks-en diagnostische gegevens in-of uitschakelen. | TRUE of FALSE |  
| **MSSQL_TELEMETRY_DIR** | Hiermee stelt u de doel directory in voor de audit bestanden voor het verzamelen van gegevens over gebruik en diagnose. | Maplocatie binnen SQL Edge-container. Deze map kan worden toegewezen aan een host volume met behulp van koppel punten of gegevens volumes. | 
| **MSSQL_PACKAGE** | Hiermee geeft u de locatie op van het dacpac-of Bacpac-pakket dat moet worden geïmplementeerd. | Map, bestand of SAS-URL met de dacpac-of Bacpac-pakketten. Raadpleeg [Deploy SQL database DACPAC and BACPAC packages in SQL Edge](deploy-dacpac.md)voor meer informatie. |


De volgende SQL Server on Linux omgevings variabele wordt niet ondersteund voor Azure SQL Edge. Indien gedefinieerd, wordt deze omgevings variabele genegeerd tijdens het initialiseren van de container.

| Omgevingsvariabele | Beschrijving |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Beschikbaarheids groep inschakelen. Bijvoorbeeld: **1** is ingeschakeld en **0** is uitgeschakeld. |

> [!IMPORTANT]
> De omgevings variabele **MSSQL_PID** voor SQL Edge accepteert alleen **Premium** en **Developer** als geldige waarden. Azure SQL Edge biedt geen ondersteuning voor initialisatie met behulp van een product code.

### <a name="specify-the-environment-variables"></a>De omgevings variabelen opgeven

Geef omgevings variabelen voor SQL Edge op wanneer u de service implementeert via de [Azure Portal](deploy-portal.md). U kunt deze toevoegen in het gedeelte **omgevings variabelen** van de module-implementatie of als onderdeel van de opties voor het maken van de **container**.

Waarden toevoegen in **omgevings variabelen**.

![Instellen met behulp van omgevings variabelen lijst](media/configure/set-environment-variables.png)

Waarden toevoegen in de opties voor het **maken van containers**.

![Instellen met behulp van de optie voor het maken van containers](media/configure/set-environment-variables-using-create-options.png)

> [!NOTE]
> In de modus voor niet-verbonden implementatie kunnen omgevings variabelen worden opgegeven met de `-e` of `--env` of de `--env-file` optie van de `docker run` opdracht.

## <a name="configure-by-using-an-mssqlconf-file"></a>Configureren met behulp van een MSSQL. conf-bestand

Azure SQL Edge bevat niet het [configuratie hulpprogramma MSSQL-conf,](/sql/linux/sql-server-linux-configure-mssql-conf/) zoals SQL Server on Linux wel. U moet het bestand MSSQL. conf hand matig configureren en het in het permanente opslag station plaatsen dat is gekoppeld aan de map/var/opt/MSSQL/in de SQL-Edge-module. Wanneer u de SQL-rand vanuit Azure Marketplace implementeert, wordt deze toewijzing opgegeven als de optie **koppelen** in de opties voor het maken van de **container**.

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

De volgende nieuwe opties voor MSSQL. conf zijn toegevoegd voor de Azure SQL-rand. 

|Optie|Beschrijving|
|:---|:---|
|**customerfeedback** | Kies of SQL Server feedback naar micro soft verzendt. Zie [gebruiks-en diagnostische gegevens verzameling uitschakelen](usage-and-diagnostics-data-configuration.md#disable-usage-and-diagnostic-data-collection) voor meer informatie.|      
|**userrequestedlocalauditdirectory** | Hiermee stelt u de doel directory in voor de audit bestanden voor het verzamelen van gegevens over gebruik en diagnose. Zie voor meer informatie [lokale controle van gebruik en het verzamelen van diagnostische gegevens](usage-and-diagnostics-data-configuration.md#local-audit-of-usage-and-diagnostic-data-collection) |        

De volgende MSSQL. conf-opties zijn niet van toepassing op SQL Edge:

|Optie|Beschrijving|
|:---|:---|
|**Klantenfeedback** | Kies of SQL Server feedback naar micro soft verzendt. |
|**Data base mail-profiel** | Stel het standaard-e-mail profiel voor de data base in voor SQL Server on Linux. |
|**Hoge beschikbaarheid** | Beschikbaarheids groepen inschakelen. |
|**Micro soft gedistribueerde transactie** | MSDTC op Linux configureren en problemen oplossen. Aanvullende configuratie opties voor gedistribueerde trans acties worden niet ondersteund voor SQL-rand. Zie [Configure MSDTC](/sql/linux/sql-server-linux-configure-mssql-conf#msdtc)(Engelstalig) voor meer informatie over deze aanvullende configuratie opties. |
|**EULA voor ML Services** | Accepteer R-en python-gebruiksrecht overeenkomsten voor Azure Machine Learning-pakketten. Is alleen van toepassing op SQL Server 2019.|
|**outboundnetworkaccess** |Schakel uitgaande netwerk toegang in voor [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) R-, python-en Java-extensies.|

Het volgende voor beeld van het bestand MSSQL. conf werkt voor SQL Edge. Zie [MSSQL. conf-indeling](/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format)voor meer informatie over de indeling van een MSSQL. conf-bestand.

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="run-azure-sql-edge-as-non-root-user"></a>Azure SQL Edge als niet-hoofd gebruiker uitvoeren

De Azure SQL Edge-containers worden standaard uitgevoerd met een niet-hoofd gebruiker/groep. Als er een andere gebruiker/groep is opgegeven via Azure Marketplace (of met behulp van docker-uitvoering), wordt de SQL Edge-container als de MSSQL-gebruiker (niet-root) gestart. Als u tijdens de implementatie een andere niet-hoofd gebruiker wilt opgeven, voegt u het sleutel-waardepaar toe onder Opties voor het maken van de `*"User": "<name|uid>[:<group|gid>]"*` container. In het onderstaande voor beeld is de SQL-rand geconfigureerd om te starten als de gebruiker `*IoTAdmin*` .

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

Als u wilt dat de niet-hoofd gebruiker toegang krijgt tot database bestanden die zich op gekoppelde volumes bevinden, moet u ervoor zorgen dat de gebruiker/groep waarmee u de container uitvoert, lees & schrijf machtigingen heeft voor de permanente bestands opslag. In het onderstaande voor beeld stellen we de niet-hoofd gebruiker met user_id 10001 in als de eigenaar van de bestanden. 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>Upgraden van eerdere CTP-releases

Eerdere CTPs van Azure SQL Edge zijn geconfigureerd om te worden uitgevoerd als de hoofd gebruikers. De volgende opties zijn beschikbaar wanneer u een upgrade uitvoert van een eerdere versie van CTPs.

- Ga door met de hoofd gebruiker: als u de hoofd gebruiker wilt blijven gebruiken, voegt u het sleutel-waardepaar toe onder Opties voor het maken van de `*"User": "0:0"*` container.
- De standaard-MSSQL-gebruiker gebruiken: Voer de volgende stappen uit om de standaard MSSQL-gebruiker te gebruiken
  - Voeg een gebruiker met de naam MSSQL toe op de docker-host. In het onderstaande voor beeld voegen we een gebruiker MSSQL met ID 10001. Deze gebruiker wordt ook toegevoegd aan de hoofd groep.
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - Wijzig de machtiging voor het Directory/mount-volume waarop het database bestand zich bevindt 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- Gebruik een ander niet-hoofd gebruikers account: als u een ander niet-hoofd gebruikers account wilt gebruiken
  - Werk de opties voor het maken van de container bij om `*"User": "user_name | user_id*` sleutel-waardepaar toevoegen onder Opties voor container maken op te geven. Vervang user_name of user_id met een daad werkelijk user_name of user_id van uw docker-host. 
  - Wijzig de machtigingen voor het Directory/mount-volume.

## <a name="persist-your-data"></a>Uw gegevens behouden

De wijzigingen in de configuratie van Azure SQL Edge en database bestanden blijven behouden in de container, zelfs als u de container opnieuw opstart met `docker stop` en `docker start` . Als u de container echter verwijdert met `docker rm` , wordt alles in de container verwijderd, inclusief Azure SQL Edge en uw data bases. In de volgende sectie wordt uitgelegd hoe u **gegevens volumes** kunt gebruiken om uw database bestanden te bewaren, zelfs als de bijbehorende containers worden verwijderd.

> [!IMPORTANT]
> Voor Azure SQL Edge is het essentieel dat u de gegevens persistentie in docker begrijpt. Naast de discussie in deze sectie raadpleegt u docker-documentatie over [het beheren van gegevens in docker-containers](https://docs.docker.com/engine/tutorials/dockervolumes/).

### <a name="mount-a-host-directory-as-data-volume"></a>Een host-Directory als gegevens volume koppelen

De eerste optie is het koppelen van een directory op uw host als een gegevens volume in de container. Gebruik hiervoor de `docker run` opdracht met de `-v <host directory>:/var/opt/mssql` vlag. Hierdoor kunnen de gegevens worden teruggezet tussen container uitvoeringen.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

Met deze techniek kunt u ook de bestanden op de host buiten docker delen en weer geven.

> [!IMPORTANT]
> Toewijzing van host-volume voor **docker op Windows** biedt momenteel geen ondersteuning voor het toewijzen van de volledige `/var/opt/mssql` Directory. U kunt echter een submap toewijzen, zoals `/var/opt/mssql/data` aan uw hostmachine.

> [!IMPORTANT]
> De toewijzing van het hostvolume voor **docker op Mac** met de installatie kopie van de Azure SQL-rand wordt op dit moment niet ondersteund. Gebruik in plaats daarvan gegevens volume containers. Deze beperking is specifiek voor de `/var/opt/mssql` Directory. Het lezen van een gekoppelde map werkt prima. U kunt bijvoorbeeld een host-Directory koppelen met-v op Mac en een back-up terugzetten vanuit een. bak-bestand dat zich op de host bevindt.

### <a name="use-data-volume-containers"></a>Gegevens volume containers gebruiken

De tweede optie is het gebruik van een gegevens volume container. U kunt een gegevens volume container maken door een volume naam op te geven in plaats van een host Directory met de `-v` para meter. In het volgende voor beeld wordt een gedeeld gegevens volume gemaakt met de naam **sqlvolume**.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

> [!NOTE]
> Deze techniek voor het impliciet maken van een gegevens volume in de opdracht uitvoeren werkt niet met oudere versies van docker. In dat geval gebruikt u de expliciete stappen die worden beschreven in de docker-documentatie, het [maken en koppelen van een gegevens volume container](https://docs.docker.com/engine/tutorials/dockervolumes/#creating-and-mounting-a-data-volume-container).

Zelfs als u deze container stopt en verwijdert, blijft het gegevens volume behouden. U kunt deze weer geven met de `docker volume ls` opdracht.

```bash
docker volume ls
```

Als u vervolgens een andere container met dezelfde volume naam maakt, gebruikt de nieuwe container dezelfde Azure SQL Edge-gegevens die in het volume zijn opgenomen.

Als u een gegevens volume container wilt verwijderen, gebruikt u de `docker volume rm` opdracht.

> [!WARNING]
> Als u de gegevens volume container verwijdert, worden alle gegevens van de Azure SQL-Edge in de container *permanent* verwijderd.


## <a name="next-steps"></a>Volgende stappen

- [Verbinding maken met Azure SQL Edge](connect.md)
- [Een end-to-end IoT-oplossing bouwen met SQL Edge](tutorial-deploy-azure-resources.md)