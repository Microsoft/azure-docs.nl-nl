---
title: OPC-Uitgever uitvoeren-Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u OPC Publisher kunt uitvoeren en opsporen. De oplossing biedt ook een oplossing voor prestatie-en geheugen overwegingen.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f7d6581a1892ebd74a1adba5c09c0af9d3cf9d43
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92079000"
---
# <a name="run-opc-publisher"></a>OPC Publisher uitvoeren

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

In dit artikel wordt beschreven hoe u de Publisher-fout opsporing OPC uitvoert. De oplossing biedt ook een oplossing voor prestatie-en geheugen overwegingen.

## <a name="command-line-options"></a>Opdrachtregelopties

Het gebruik van de toepassing wordt als volgt weer gegeven met behulp van de `--help` opdracht regel optie:

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
      --ot, --operationtimeout=VALUE
                              the operation timeout of the publisher OPC UA
                                client in ms.
                                Default: 120000
      --oi, --opcsamplinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds to request the servers to sample
                                the nodes with this interval
                                this value might be revised by the OPC UA
                                servers to a supported sampling interval.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a negative value will set the sampling interval
                                to the publishing interval of the subscription
                                this node is on.
                                0 will configure the OPC UA server to sample in
                                the highest possible resolution and should be
                                taken with care.
                                Default: 1000
      --op, --opcpublishinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds for the publishing interval setting
                                of the subscriptions established to the OPC UA
                                servers.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a value less than or equal zero will let the
                                server revise the publishing interval.
                                Default: 0
      --ct, --createsessiontimeout=VALUE
                              specify the timeout in seconds used when creating
                                a session to an endpoint. On unsuccessful
                                connection attemps a backoff up to 5 times the
                                specified timeout value is used.
                                Min: 1
                                Default: 10
      --ki, --keepaliveinterval=VALUE
                              specify the interval in seconds the publisher is
                                sending keep alive messages to the OPC servers
                                on the endpoints it is connected to.
                                Min: 2
                                Default: 2
      --kt, --keepalivethreshold=VALUE
                              specify the number of keep alive packets a server
                                can miss, before the session is disconneced
                                Min: 1
                                Default: 5
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

Normaal gesp roken geeft u de IoT Hub eigenaar connection string alleen op bij de eerste uitvoering van de toepassing. De connection string is versleuteld en opgeslagen in het platform certificaat archief. Bij latere uitvoeringen leest de toepassing het connection string uit het certificaat archief. Als u de connection string op elke uitvoering opgeeft, wordt het apparaat dat is gemaakt voor de toepassing in het IoT Hub apparaat-REGI ster verwijderd en opnieuw gemaakt.

## <a name="run-natively-on-windows"></a>Systeem eigen uitvoeren in Windows

Open het project **opcpublisher. SLN** met Visual Studio, bouw de oplossing en publiceer het. U kunt de toepassing als volgt starten in de **doelmap** die u hebt gepubliceerd:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Een zelf gemaakte container gebruiken

Bouw uw eigen container en start deze als volgt:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Een container van micro soft Container Registry gebruiken

Er is een vooraf ontwikkelde container beschikbaar in de micro soft-Container Registry. Start deze als volgt:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Controleer de [docker-hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) voor een overzicht van de ondersteunde besturings systemen en processor architecturen. Als uw OS-en CPU-architectuur wordt ondersteund, selecteert docker automatisch de juiste container.

## <a name="run-as-an-azure-iot-edge-module"></a>Als Azure IoT Edge-module uitvoeren

OPC Publisher is klaar om te worden gebruikt als [Azure IOT Edge](../iot-edge/index.yml) -module. Wanneer u OPC Publisher als IoT Edge module gebruikt, zijn de enige transport protocollen **Amqp_Tcp_Only** en **Mqtt_Tcp_Only**.

Als u OPC-uitgever als module wilt toevoegen aan uw IoT Edge-implementatie, gaat u naar de IoT Hub-instellingen in de Azure Portal en voert u de volgende stappen uit:

1. Ga naar **IOT Edge** en maak of selecteer uw IOT edge-apparaat.
1. Selecteer **modules instellen**.
1. Selecteer **toevoegen** onder **implementatie modules** en klik vervolgens op **IOT Edge module**.
1. Voer in het veld **naam** **Uitgever** in.
1. Voer in het veld **URI van installatie kopie**`mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. U kunt de beschik bare labels vinden op [docker hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)
1. Plak de volgende JSON in het veld met de optie voor het maken van een **container** :

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Met deze configuratie wordt IoT Edge geconfigureerd om een container met de naam **Publisher** te starten met behulp van de OPC Publisher-afbeelding. De hostnaam van het systeem van de container is ingesteld op **Uitgever**. OPC-uitgever wordt aangeroepen met het volgende opdracht regel argument: `--aa` . Met deze optie kan OPC uitgever de certificaten van de OPC UA-servers vertrouwt die verbinding maken met. U kunt alle OPC-opdracht regel opties voor Publisher gebruiken. De enige beperking is de grootte van de **container Create-opties** die worden ondersteund door IOT Edge.

1. Laat de overige instellingen ongewijzigd en selecteer **Opslaan**.
1. Als u de uitvoer van de OPC-Uitgever lokaal wilt verwerken met een andere IoT Edge module, gaat u terug naar de pagina met **set-modules** . Ga vervolgens naar het tabblad **routes opgeven** en voeg een nieuwe route toe die eruitziet als de volgende JSON:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. Klik op de pagina **modules instellen** op **volgende** totdat u de laatste pagina van de configuratie hebt bereikt.
1. Selecteer **verzenden** om uw configuratie naar IOT Edge te verzenden.
1. Wanneer u IoT Edge hebt gestart op uw edge-apparaat en de docker-container **Uitgever** wordt uitgevoerd, kunt u de logboek uitvoer van de OPC-Uitgever bekijken met `docker logs -f publisher` of door het logboek bestand te controleren. In het vorige voor beeld is het logboek bestand hierboven `d:\iiotegde\publisher-publisher.log` . U kunt ook het [hulp programma IOT-Edge-OPC-Uitgever-Diagnostics](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)gebruiken.

### <a name="make-the-configuration-files-accessible-on-the-host"></a>De configuratie bestanden toegankelijk maken op de host

Als u de configuratie bestanden voor de IoT Edge module toegankelijk wilt maken in het host bestands systeem, gebruikt u de volgende opties voor het maken van de **container**. Het volgende voor beeld is een implementatie met Linux-containers voor Windows:

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

Met deze opties wordt de OPC-Uitgever gelezen van de knoop punten die moeten worden gepubliceerd vanuit het bestand `./pn.json` en wordt de werkmap van de container ingesteld op `/appdata` bij het opstarten. Met deze instellingen, OPC Publisher leest het bestand `/appdata/pn.json` uit de container om de configuratie op te halen. Zonder deze `--pf` optie probeert OPC Publisher het standaard configuratie bestand te lezen `./publishednodes.json` .

Het logboek bestand, met de standaard naam `publisher-publisher.log` , wordt geschreven naar `/appdata` en de `CertificateStores` map wordt ook in deze map gemaakt.

Om ervoor te zorgen dat deze bestanden beschikbaar zijn in het bestands systeem van de host, is voor de container configuratie een bindings koppel volume vereist. De `d://iiotedge:/appdata` binding wijst de Directory `/appdata` , de huidige werkmap bij het opstarten van de container, toe aan de map host `d://iiotedge` . Zonder deze optie worden er geen bestands gegevens bewaard wanneer de container de volgende keer wordt gestart.

Als u Windows-containers uitvoert, is de syntaxis van de `Binds` para meter niet hetzelfde. Bij het opstarten van de container is de werkmap `c:\appdata` . Als u het configuratie bestand in de map `d:\iiotedge` op de host wilt plaatsen, geeft u de volgende toewijzing op in de `HostConfig` sectie:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Als u Linux-containers in Linux uitvoert, is de syntaxis van de `Binds` para meter opnieuw. Bij het opstarten van de container is de werkmap `/appdata` . Als u het configuratie bestand in de map `/iiotedge` op de host wilt plaatsen, geeft u de volgende toewijzing op in de `HostConfig` sectie:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Overwegingen bij het gebruik van een container

In de volgende secties staan enkele dingen die u moet onthouden wanneer u een container gebruikt:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Toegang tot de OPC Publisher OPC UA-server

De OPC Publisher OPC UA-server luistert standaard op poort 62222. Gebruik de volgende opdracht om deze binnenkomende poort beschikbaar te maken in een container:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Naam omzetting tussen meerdere containers inschakelen

Als u naam omzetting vanuit de container naar andere containers wilt inschakelen, maakt u een gebruiker die een docker-brug netwerk definieert en verbindt u de container met dit netwerk met behulp van de `--network` optie. Wijs ook de container een naam met behulp van de `--name` optie als volgt toe:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

De container is nu bereikbaar met behulp van de naam `publisher` door andere containers op hetzelfde netwerk.

### <a name="access-other-systems-from-within-the-container"></a>Toegang tot andere systemen vanuit de container

Andere containers kunnen worden bereikt met de para meters die in de vorige sectie worden beschreven. Als het besturings systeem waarop docker wordt gehost, DNS is ingeschakeld, is het mogelijk om toegang te krijgen tot alle systemen die bekend zijn bij DNS.

In netwerken die gebruikmaken van NetBIOS-naam omzetting, schakelt u toegang tot andere systemen in door de container te starten met de `--add-host` optie. Deze optie voegt effectief een vermelding toe aan het hostbestand van de container:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Een hostnaam toewijzen

OPC Publisher gebruikt de hostnaam van de computer waarop deze wordt uitgevoerd voor het genereren van certificaten en eind punten. Docker kiest een wille keurige hostnaam als deze niet is ingesteld met de `-h` optie. In het volgende voor beeld ziet u hoe u de interne hostnaam van de container instelt op `publisher` :

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Bindings koppelingen gebruiken (gedeeld bestands systeem)

In plaats van het container bestandssysteem te gebruiken, kunt u het host-bestands systeem kiezen om configuratie-informatie en logboek bestanden op te slaan. Als u deze optie wilt configureren, gebruikt u de `-v` optie `docker run` in de modus BIND koppelen.

## <a name="opc-ua-x509-certificates"></a>OPC UA X. 509-certificaten

OPC UA maakt gebruik van X. 509-certificaten om de OPC UA-client en-server te verifiëren wanneer ze een verbinding tot stand brengen en de communicatie tussen de clients te versleutelen. OPC Publisher gebruikt certificaat archieven die worden beheerd door de OPC UA-stack om alle certificaten te beheren. Bij het opstarten controleert OPC Publisher of er een certificaat voor zichzelf is. Als er zich geen certificaat in het certificaat archief bevindt en er één wordt door gegeven in de opdracht regel, wordt in OPC Publisher een zelfondertekend certificaat gemaakt. Zie de methode **InitApplicationSecurityAsync** in voor meer informatie `OpcApplicationConfigurationSecurity.cs` .

Zelfondertekende certificaten bieden geen beveiliging, omdat ze niet zijn ondertekend door een vertrouwde certificerings instantie.

OPC Publisher biedt opdracht regel opties voor het volgende:

- CSR-gegevens ophalen van het huidige toepassings certificaat dat wordt gebruikt door de OPC-Uitgever.
- De OPC-Uitgever inrichten met een door de certificerings instantie ondertekend certificaat.
- De OPC-Uitgever inrichten met een nieuw sleutel paar en overeenkomend ondertekend CA-certificaat.
- Certificaten toevoegen aan een vertrouwde peer of een certificaat archief van een vertrouwde uitgever.
- Een CRL toevoegen.
- Een certificaat verwijderen uit het certificaat archief van vertrouwde peer of vertrouwde verleners.

Met al deze opties kunt u para meters door geven met behulp van bestanden of base64-gecodeerde teken reeksen.

Het standaard opslaglocatie type voor alle certificaat archieven is het bestands systeem dat u kunt wijzigen met behulp van opdracht regel opties. Omdat de container geen permanente opslag in het bestands systeem biedt, moet u een ander opslag type kiezen. Gebruik de optie docker `-v` om de certificaat archieven in het host-bestands systeem of op een docker-volume te behouden. Als u een docker-volume gebruikt, kunt u certificaten door geven met base64-gecodeerde teken reeksen.

De runtime-omgeving heeft invloed op de persistentie van certificaten. Vermijd het maken van nieuwe certificaat archieven telkens wanneer u de toepassing uitvoert:

- Als u systeem eigen op Windows uitvoert, kunt u een certificaat archief van het type toepassing niet gebruiken, `Directory` omdat de toegang tot de persoonlijke sleutel mislukt. In dit geval gebruikt u de optie `--at X509Store` .
- Als Linux docker-container wordt uitgevoerd, kunt u de certificaat archieven toewijzen aan het bestands systeem van de host met de optie docker run `-v <hostdirectory>:/appdata` . Met deze optie wordt het certificaat persistent gemaakt voor alle toepassingen.
- Als Linux docker-container wordt uitgevoerd en u een x509-archief wilt gebruiken voor het toepassings certificaat, gebruikt u de optie docker run `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` en de optie toepassing `--at X509Store`

## <a name="performance-and-memory-considerations"></a>Overwegingen voor prestaties en geheugen

In deze sectie worden opties beschreven voor het beheren van geheugen en prestaties:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Opdracht regel parameters voor het beheren van prestaties en geheugen

Wanneer u OPC Publisher uitvoert, moet u rekening houden met de prestatie vereisten en de geheugen bronnen die beschikbaar zijn op uw host.

Het geheugen en de prestaties zijn afhankelijk van de configuratie van het aantal knoop punten dat u configureert om te publiceren. Zorg ervoor dat de volgende para meters aan uw vereisten voldoen:

- Interval voor verzenden van IoT Hub: `--si`
- Grootte van IoT Hub bericht (standaard `1` ): `--ms`
- Capaciteit van de wachtrij voor bewaakte items: `--mq`

De `--mq` para meter bepaalt de bovengrens van de capaciteit van de interne wachtrij, die alle OPC-wijzigings meldingen voor knooppunt waarden buffert. Als OPC Publisher berichten niet kan verzenden naar IoT Hub snel genoeg is, buffert deze wachtrij de meldingen. Met de para meter wordt het aantal meldingen ingesteld dat in de buffer kan worden opgeslagen. Als u het aantal items in deze wachtrij verg root in uw test uitvoeringen ziet, kunt u voor komen dat er berichten verloren gaan:

- Het interval voor het verzenden van IoT Hub verminderen
- De grootte van het IoT Hub bericht verg Roten

Met de `--si` para meter wordt de OPC-Uitgever gedwongen berichten te verzenden naar IOT hub met het opgegeven interval. OPC Publisher verzendt een bericht zodra de bericht grootte die is opgegeven door de `--ms` para meter is bereikt of zodra het interval dat is opgegeven door de `--si` para meter is bereikt. Als u de optie voor de bericht grootte wilt uitschakelen, gebruikt u `--ms 0` . In dit geval gebruikt OPC Publisher de grootst mogelijke IoT Hub bericht grootte van 256 kB aan batch gegevens.

`--ms`Met de para meter kunt u batch berichten verzenden naar IOT hub. Het protocol dat u gebruikt, bepaalt of de overhead van het verzenden van een bericht naar IoT Hub hoog is vergeleken met de werkelijke tijd van verzen ding van de nettolading. Als uw scenario latentie toestaat wanneer gegevens worden opgenomen door IoT Hub, configureert u OPC Publisher voor het gebruiken van de grootste bericht grootte van 256 kB.

Voordat u OPC Publisher in productie scenario's gebruikt, moet u het prestatie-en geheugen gebruik onder productie omstandigheden testen. U kunt de `--di` para meter gebruiken om het interval (in seconden) op te geven dat OPC Publisher diagnostische gegevens schrijft.

### <a name="test-measurements"></a>Test metingen

In de volgende voorbeeld diagnostiek worden metingen weer gegeven met verschillende waarden voor `--si` en `--ms` para meters die 500-knoop punten publiceren met een OPC-publicatie-interval van 1 seconde.  De test gebruikt een OPC Publisher debug build on Windows 10 native voor 120 seconden. Het IoT Hub-protocol was het standaard MQTT-protocol.

#### <a name="default-configuration---si-10---ms-262144"></a>Standaard configuratie (--si 10--MS 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Met de standaard configuratie worden gegevens verzonden naar IoT Hub elke tien seconden of wanneer 256 kB aan gegevens beschikbaar is voor het opnemen van IoT Hub. Deze configuratie voegt een gemiddelde latentie van ongeveer 10 seconden toe, maar heeft de laagste kans dat gegevens verloren gaan als gevolg van de grote bericht grootte. In de diagnostische gegevens worden weer gegeven dat er geen OPC-knooppunt updates verloren zijn gegaan: `monitored item notifications enqueue failure: 0` .

#### <a name="constant-send-interval---si-1---ms-0"></a>Interval voor constante verzen ding (--Si 1--MS 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Wanneer de bericht grootte is ingesteld op 0, wordt OPC-Uitgever intern batch gegevens gebruikt met de grootste ondersteunde IoT Hub bericht grootte, 256 kB. De diagnostische uitvoer toont de gemiddelde bericht grootte is 115.019 bytes. In deze configuratie OPC-uitgever worden geen OPC-knooppunt waarden bijgewerkt, en vergeleken met de standaard waarde, en deze heeft een lagere latentie.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Elke OPC-knooppunt waarde Update verzenden (--si 0--MS 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Deze configuratie verzendt voor elke waarde van het OPC-knoop punt een bericht wijzigen in IoT Hub. De diagnostische gegevens tonen de gemiddelde bericht grootte is 234 bytes, wat klein is. Het voor deel van deze configuratie is dat de OPC-uitgever geen latentie toevoegt. Het aantal verloren OPC-knooppunt waarde-updates ( `monitored item notifications enqueue failure: 44624` ) is hoog, waardoor deze configuratie niet geschikt is voor scenario's met grote hoeveel heden telemetrie die moeten worden gepubliceerd.

### <a name="maximum-batching---si-0---ms-262144"></a>Maximum aantal batches (--si 0--MS 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Deze configuratie batches worden zo veel OPC-knooppunt waarden bijgewerkt. De maximale IoT Hub bericht grootte is 256 kB, dat hier wordt geconfigureerd. Er is geen verzend interval aangevraagd, wat betekent dat de hoeveelheid gegevens voor IoT Hub op opnemen de latentie bepaalt. Deze configuratie heeft de minste kans om OPC-knooppunt waarden te verliezen en is geschikt voor het publiceren van een groot aantal knoop punten. Wanneer u deze configuratie gebruikt, moet u ervoor zorgen dat er voor uw scenario geen hoge latentie is ingesteld als de bericht grootte van 256 kB niet is bereikt.

## <a name="debug-the-application"></a>Fouten in de toepassing opsporen

Als u fouten wilt opsporen in de toepassing, opent u het oplossings bestand **opcpublisher. SLN** met Visual Studio en gebruikt u de hulpprogram ma's voor fout opsporing in Visual Studio.

Als u toegang nodig hebt tot de OPC UA-server in de OPC-Uitgever, moet u ervoor zorgen dat uw firewall toegang verleent tot de poort waarop de server luistert. De standaard poort is: 62222.

## <a name="control-the-application-remotely"></a>De toepassing op afstand beheren

Het configureren van de knoop punten die moeten worden gepubliceerd, kan worden uitgevoerd met behulp van IoT Hub directe methoden.

OPC Publisher implementeert enkele aanvullende IoT Hub directe-methode aanroepen om te lezen:

- Algemene informatie.
- Diagnostische gegevens over OPC-sessies, abonnementen en bewaakte items.
- Diagnostische informatie over IoT Hub berichten en gebeurtenissen.
- Het opstart logboek.
- De laatste 100 regels van het logboek.
- Sluit de toepassing af.

De volgende GitHub-opslag plaatsen bevatten hulpprogram ma's voor [het configureren van de knoop punten om](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) [de diagnostische gegevens](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)te publiceren en te lezen. Beide hulpprogram ma's zijn ook beschikbaar als containers in docker hub.

## <a name="use-a-sample-opc-ua-server"></a>Een voor beeld van een OPC UA-server gebruiken

Als u geen echte OPC UA-server hebt, kunt u de voor [beeld OPC UA PLC](https://github.com/Azure-Samples/iot-edge-opc-plc) gebruiken om aan de slag te gaan. Deze voorbeeld-PLC is ook beschikbaar op docker hub.

Het implementeert een aantal Tags, waarmee wille keurige gegevens en tags met afwijkingen worden gegenereerd. U kunt het voor beeld uitbreiden als u extra label waarden wilt simuleren.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC Publisher kunt uitvoeren, kunt u het beste de volgende stappen volgen om meer te weten te komen over [OPC dubbele](overview-opc-twin.md) en [OPC-kluis](overview-opc-vault.md).