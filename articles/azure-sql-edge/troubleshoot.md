---
title: Problemen met Azure SQL Edge-implementaties oplossen
description: Meer informatie over mogelijke fouten bij het implementeren van Azure SQL Edge
keywords: SQL-rand, probleem oplossing, implementatie fouten
services: sql-edge
ms.service: sql-edge
ms.topic: troubleshooting
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 517fed0dd9eb1736344546bde9f79e52ee17182f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333100"
---
# <a name="troubleshooting-azure-sql-edge-deployments"></a>Problemen met Azure SQL Edge-implementaties oplossen 

Dit artikel bevat informatie over mogelijke fouten die zijn opgetreden bij het implementeren en gebruiken van Azure SQL Edge-containers en biedt probleemoplossings technieken om deze problemen op te lossen. 

Azure SQL Edge ondersteunt twee implementatie modellen: 
- Verbonden implementatie via Azure IoT Edge: Azure SQL Edge is beschikbaar op de Azure Marketplace en kan worden geïmplementeerd als een module voor [Azure IOT Edge](../iot-edge/about-iot-edge.md). Zie [Azure SQL Edge implementeren](deploy-portal.md)voor meer informatie.<br>

- Niet-verbonden implementatie: Azure SQL Edge-container installatie kopieën kunnen worden opgehaald uit de docker-hub en worden geïmplementeerd als een zelfstandige docker-container of een kubernetes-cluster. Zie [Azure SQL Edge implementeren met docker](disconnected-deployment.md) en [een Azure SQL Edge-container implementeren in Kubernetes](deploy-kubernetes.md)voor meer informatie.

## <a name="troubleshooting-iot-edge-device-and-deployments"></a>Problemen met IoT Edge-apparaten en-implementaties oplossen

Als er een fout optreedt bij het implementeren van SQL Edge via Azure IoT Edge, moet u ervoor zorgen dat de `iotedge` service juist is geconfigureerd en wordt uitgevoerd. De volgende documenten kunnen nuttig zijn bij het oplossen van problemen met betrekking tot Azure IoT Edge:
- [Veelvoorkomende problemen en oplossingen voor Azure IOT Edge](../iot-edge/troubleshoot-common-errors.md).
- [Problemen met uw IoT Edge apparaat oplossen](../iot-edge/troubleshoot.md)

## <a name="docker-command-errors"></a>Fouten van docker-opdracht

Als er fouten voor alle opdrachten worden weer geven, controleert u `docker` of de docker-service wordt uitgevoerd en probeert u uit te voeren met verhoogde machtigingen.

In Linux kan bijvoorbeeld de volgende fout worden weer geven bij het uitvoeren van `docker` opdrachten:

```output
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
```

Als deze fout op Linux wordt weer geven, voert u dezelfde opdrachten uit die zijn voorafgegaan door `sudo` . Als dat niet lukt, controleert u of de docker-service wordt uitgevoerd en start u deze indien nodig.

```bash
sudo systemctl status docker
sudo systemctl start docker
```

Controleer in Windows of u Power shell of de opdracht prompt als beheerder wilt starten.

## <a name="azure-sql-edge-container-startup-errors"></a>Opstart fouten van de Azure SQL-Edge-container

Als de SQL Edge-container niet kan worden uitgevoerd, voert u de volgende tests uit:

- Als u Azure IoT Edge gebruikt, moet u ervoor zorgen dat de module installatie kopieën zijn gedownload en dat de omgevings variabelen en de opties voor het maken van de container correct zijn opgegeven in het module manifest.

- Als u gebruikmaakt van docker of implementatie op basis van kubernetes, moet u ervoor zorgen dat de `docker run` opdracht juist is gevormd. Zie [Azure SQL Edge implementeren met docker](disconnected-deployment.md) en [een Azure SQL Edge-container implementeren in Kubernetes](deploy-kubernetes.md)voor meer informatie.

- Als er een fout optreedt zoals `failed to create endpoint CONTAINER_NAME on network bridge. Error starting proxy: listen tcp 0.0.0.0:1433 bind: address already in use.` , probeert u de container poort 1433 toe te wijzen aan een poort die al in gebruik is. Dit kan gebeuren als u SQL Edge lokaal uitvoert op de hostmachine. Dit kan ook gebeuren als u twee SQL Edge-containers start en probeert ze beide toe te wijzen aan dezelfde host-poort. Als dit het geval is, gebruikt u de `-p` para meter om de container poort 1433 toe te wijzen aan een andere host-poort. Bijvoorbeeld: 

    ```bash
    sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge-developer.
    ```

- Als er een fout optreedt zoals `Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.30tdout=1&tail=all: dial unix /var/run/docker.sock: connect: permission denied` bij het starten van een container, voegt u de gebruiker toe aan de docker-groep in Ubuntu. Meld u vervolgens af en meld u opnieuw aan, omdat deze wijziging van invloed is op nieuwe sessies. 

   ```bash
    usermod -aG docker $USER
   ```

- Controleer of er fout berichten van de container worden weer geven.

   ```bash
   docker logs e69e056c702d
   ```

- Als u container beheer software gebruikt, moet u ervoor zorgen dat container processen worden ondersteund die als root worden uitgevoerd. Het SQLSERVR-proces in de container wordt uitgevoerd als root.

- Azure SQL Edge-containers worden standaard uitgevoerd als een niet-hoofd gebruiker met de naam `mssql` . Als u koppel punten of gegevens volumes gebruikt om gegevens te behouden, moet u ervoor zorgen dat de `mssql` gebruiker over de juiste machtigingen voor het volume beschikt. Zie [uitvoeren als niet-hoofd gebruiker](configure.md#run-azure-sql-edge-as-non-root-user) en [gegevens persistent](configure.md#persist-your-data)maken voor meer informatie.

- Als uw SQL Edge docker-container onmiddellijk na het starten wordt afgesloten, controleert u de docker-Logboeken. Als u Power shell gebruikt in Windows met de `docker run` opdracht, gebruikt u dubbele aanhalings tekens in plaats van enkele aanhalings tekens. Gebruik voor Power shell core enkele aanhalings tekens.

- Controleer de [fouten logboeken van SQL-rand](#errorlogs).

## <a name="sql-edge-connection-failures"></a>Verbindings fouten voor SQL Edge

Als u geen verbinding kunt maken met de SQL Edge-instantie die in uw container wordt uitgevoerd, voert u de volgende tests uit:

- Controleer of de SQL Edge-container wordt uitgevoerd door te kijken naar de kolom **status** van de `docker ps -a` uitvoer. Als dat niet het geval is, gebruikt `docker start <Container ID>` u om het te starten.

- Als u bent toegewezen aan een niet-standaard poort voor de host (niet 1433), moet u ervoor zorgen dat u de poort in uw connection string opgeeft. U kunt de poort toewijzing weer geven in de kolom **poorten** van de `docker ps -a` uitvoer. Raadpleeg voor meer informatie over het maken van verbinding met Azure SQL Edge [verbinding maken en query's uitvoeren voor Azure SQL Edge](connect.md)

- Als u de SQL-rand eerder hebt geïmplementeerd met toegewezen gegevens volume of container gegevens volume en nu gebruikmaakt van de bestaande toegewezen gegevens volume of gegevens volume container, negeert SQL Edge de waarde van `MSSQL_SA_PASSWORD` omgevings variabele. In plaats daarvan wordt het eerder geconfigureerde SA-gebruikers wachtwoord gebruikt. Dit gebeurt omdat SQL Edge de bestaande master data base-bestanden in de container toegewezen volume of gegevens volume opnieuw gebruikt. Als u dit probleem ondervindt, kunt u de volgende opties gebruiken:

    - Maak verbinding met het eerder gebruikte wacht woord als het nog steeds beschikbaar is.
    - Configureer SQL Edge om een ander toegewezen volume of gegevens volume container te gebruiken.
    - Verwijder de bestaande master-database bestanden (Master. MDF en Mastlog. MDF) van de toegewezen volume-of gegevens volume container.

- Controleer de [fouten logboeken van SQL-rand](#errorlogs).

## <a name="sql-edge-setup-and-error-logs"></a><a id="errorlogs"></a> Setup van SQL Edge en fouten logboeken

SQL Edge-fout logboeken zijn standaard aanwezig in de map **/var/opt/MSSQL/log** in de container en kunnen worden geopend via een van de volgende manieren:

- Als u tijdens het maken van de container een host-map aan **/var/opt/MSSQL** hebt gekoppeld, kunt u in plaats daarvan zoeken in de submap **logboek** op het toegewezen pad op de host.
- Met een interactieve opdracht prompt om verbinding te maken met de container. Als de container niet wordt uitgevoerd, start u de container eerst. Gebruik vervolgens een interactieve opdracht prompt om de logboeken te controleren. U kunt de container-ID ophalen door de opdracht uit te voeren `docker ps` .

    ```bash
    docker start <ContainerID>
    docker exec -it <ContainerID> "/bin/bash"
    ```

    Voer de volgende opdrachten uit vanuit de bash-sessie in uw container:

    ```bash
    cd /var/opt/mssql/log
    cat errorlog
    ```
- Als de SQL Edge-container actief is en u verbinding kunt maken met het exemplaar met behulp van client hulpprogramma's, kunt u de opgeslagen procedure gebruiken `sp_readerrorlog` om de inhoud van het fouten logboek van SQL Edge te lezen.

## <a name="execute-commands-in-a-container"></a>Opdrachten uitvoeren in een container

Als u een actieve container hebt, kunt u opdrachten binnen de container uitvoeren vanuit een host-terminal.

De uit te voeren container-ID ophalen:

```bash
docker ps -a
```

Ga als volgt te werk om een bash-Terminal te starten in de container die wordt uitgevoerd:

```bash
docker exec -it <Container ID> /bin/bash
```

U kunt nu opdrachten uitvoeren alsof u ze uitvoert op de terminal in de container. Wanneer u klaar bent, typt u `exit` . Dit wordt afgesloten in de interactieve opdracht sessie, maar de container blijft actief.

### <a name="enabling-verbose-logging"></a>Uitgebreide logboek registratie inschakelen

Als het standaard logboek niveau voor de Streaming-Engine niet voldoende informatie biedt, kan de logboek registratie van fouten voor de Streaming-Engine worden ingeschakeld in SQL-rand. Als u logboek registratie voor fout opsporing wilt inschakelen, voegt `RuntimeLogLevel=debug` u de omgevings variabele toe aan de implementatie van SQL Edge. Nadat u logboek registratie voor fout opsporing hebt ingeschakeld, probeert u het probleem te reproduceren en raadpleegt u de logboeken voor relevante berichten of uitzonde ringen. 

> [!NOTE]
> De optie uitgebreide logboek registratie mag alleen worden gebruikt voor het oplossen van problemen en niet voor reguliere productie werk belastingen. 


## <a name="next-steps"></a>Volgende stappen

- [Machine Learning en kunst matige intelligentie met ONNX in SQL Edge](onnx-overview.md)
- [Gegevensstreaming in Azure SQL Edge](stream-data.md)
- [Gegevens bewaren en opschonen](data-retention-overview.md)
- [De tijds intervallen voor het invullen en de ontbrekende waarden worden toegerekend](imputing-missing-values.md)







