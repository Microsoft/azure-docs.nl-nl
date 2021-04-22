---
title: Veelgestelde vragen over oplossingen voor verbonden factory's - Azure | Microsoft Docs
description: In dit artikel vindt u antwoorden op de veelgestelde vragen over de oplossingsversneller voor verbonden factory's. Het bevat koppelingen naar de GitHub-opslagplaats.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 038403743caf13087655066f4acbec4dcee598c7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874203"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Veelgestelde vragen over de oplossingsversneller voor verbonden factory's

Zie ook de algemene veelgestelde [vragen](iot-accelerators-faq.md) over IoT-oplossingsversnellers.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Waar vind ik de broncode voor de oplossingsversneller?

De broncode wordt opgeslagen in de volgende GitHub-opslagplaats:

* [Oplossingsversneller voor verbonden factory's](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Wat is OPC UA?

OPC Unified Architecture (UA), uitgebracht in 2008, is een platform-onafhankelijke, servicegerichte interoperabiliteitsstandaard. OPC UA wordt gebruikt door verschillende industriële systemen en apparaten, zoals pc's in de branche, PLC's en sensoren. OPC UA integreert de functionaliteit van de klassieke OPC-specificaties in één extensible framework met ingebouwde beveiliging. Het is een standaard die wordt aangestuurd door de OPC Foundation. De [OPC Foundation](https://opcfoundation.org/) is een not-for-profitorganisatie met meer dan 440 leden. Het doel van de organisatie is om OPC-specificaties te gebruiken om interoperabiliteit met meerdere leveranciers, meerdere platforms, veilige en betrouwbare interoperabiliteit mogelijk te maken via:

* Infrastructuur
* Specificaties
* Technologie
* Processen

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Waarom heeft Microsoft gekozen voor OPC UA als oplossingsversneller voor verbonden factory's?

Microsoft heeft gekozen voor OPC UA omdat het een open, niet-bedrijfseigen, platform-onafhankelijke, door de branche erkende en bewezen standaard is. Het is een vereiste voor referentiearchitectuuroplossingen voor Industrie 4.0 ( ARCHITECTURE4.0) die interoperabiliteit garanderen tussen een breed scala aan productieprocessen en apparatuur. Microsoft ziet de vraag van haar klanten om Industrie 4.0-oplossingen te bouwen. Ondersteuning voor OPC UA helpt klanten de barrière te verlagen om hun doelen te bereiken en biedt hen direct bedrijfswaarde.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Hoe kan ik een openbaar IP-adres toevoegen aan de simulatie-VM?

U hebt twee opties om het IP-adres toe te voegen:

* Gebruik het PowerShell-script `Simulation/Factory/Add-SimulationPublicIp.ps1` in de [opslagplaats](https://github.com/Azure/azure-iot-connected-factory). Geef de naam van uw implementatie door als parameter. Gebruik voor een lokale `<your username>ConnFactoryLocal` implementatie. Met het script wordt het IP-adres van de VM afgedrukt.

* Zoek in Azure Portal de resourcegroep van uw implementatie. Met uitzondering van een lokale implementatie heeft de resourcegroep de naam die u hebt opgegeven als oplossing of implementatienaam. Voor een lokale implementatie met behulp van het buildscript is de naam van de resourcegroep `<your username>ConnFactoryLocal` . Voeg nu een nieuwe resource **voor een openbaar IP-adres** toe aan de resourcegroep.

> [!NOTE]
> In beide gevallen moet u ervoor zorgen dat u de nieuwste patches installeert door de instructies op de [Ubuntu-website te volgen.](https://wiki.ubuntu.com/Security/Upgrades) Houd de installatie up-to-date zolang uw VM toegankelijk is via een openbaar IP-adres.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Hoe kan ik het openbare IP-adres naar de simulatie-VM verwijderen?

U hebt twee opties om het IP-adres te verwijderen:

* Gebruik het PowerShell-script Simulation/Factory/Remove-SimulationPublicIp.ps1 van de [opslagplaats](https://github.com/Azure/azure-iot-connected-factory). Geef de naam van uw implementatie door als parameter. Gebruik voor een lokale `<your username>ConnFactoryLocal` implementatie. Met het script wordt het IP-adres van de VM afgedrukt.

* Zoek in Azure Portal de resourcegroep van uw implementatie. Met uitzondering van een lokale implementatie heeft de resourcegroep de naam die u hebt opgegeven als oplossing of implementatienaam. Voor een lokale implementatie met behulp van het buildscript is de naam van de resourcegroep `<your username>ConnFactoryLocal` . Verwijder nu de **resource Openbaar IP-adres** uit de resourcegroep.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Hoe kan ik aanmelden bij de simulatie-VM?

Aanmelden bij de simulatie-VM wordt alleen ondersteund als u uw oplossing hebt geïmplementeerd met behulp van het PowerShell-script `build.ps1` in de [opslagplaats](https://github.com/Azure/azure-iot-connected-factory).

Als u de oplossing hebt geïmplementeerd vanuit www.azureiotsolutions.com, kunt u zich niet aanmelden bij de VM. U kunt zich niet aanmelden, omdat het wachtwoord willekeurig wordt gegenereerd en u het niet opnieuw kunt instellen.

1. Voeg een openbaar IP-adres toe aan de VM. Zie [Hoe kan ik een openbaar IP-adres toevoegen aan de simulatie-VM?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Maak een SSH-sessie naar uw VM met behulp van het IP-adres van de VM.
1. De gebruikersnaam die moet worden gebruikt, is: `docker` .
1. Het wachtwoord dat moet worden gebruikt, is afhankelijk van de versie die u hebt gebruikt om te implementeren:
    * Voor oplossingen die vóór 1 juni 2017 zijn geïmplementeerd met behulp van build.ps1 script, is het wachtwoord: `Passw0rd` .
    * Voor oplossingen die zijn geïmplementeerd met behulp build.ps1 script na 1 juni 2017, vindt u het wachtwoord in het `<name of your deployment>.config.user` bestand. Het wachtwoord wordt opgeslagen in de **instelling VmAdminPassword.** Het wachtwoord wordt willekeurig gegenereerd tijdens de implementatie, tenzij u het opgeeft met behulp van de `build.ps1` scriptparameter `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Hoe kan ik alle Docker-processen in de simulatie-VM stoppen en starten?

1. Meld u aan bij de simulatie-VM. Zie [Hoe kan ik aanmelden bij de simulatie-VM?](#how-do-i-sign-in-to-the-simulation-vm)
1. Voer uit om te controleren welke containers actief zijn: `docker ps` .
1. Voer uit om alle simulatiecontainers te stoppen: `./stopsimulation` .
1. Alle simulatiecontainers starten:
    * Exporteert u een shellvariabele met de **naam IOTHUB_CONNECTIONSTRING**. Gebruik de waarde van de **instelling IotHubOwnerConnectionString** in het `<name of your deployment>.config.user` bestand. Bijvoorbeeld:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Voer `./startsimulation` uit.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Hoe kan ik de simulatie in de VM bijwerken?

Als u wijzigingen in de simulatie hebt aangebracht, kunt u het PowerShell-script in de opslagplaats gebruiken `build.ps1` met behulp van de opdracht [](https://github.com/Azure/azure-iot-connected-factory) `updatedimulation` . Met dit script worden alle simulatieonderdelen gebouwd, wordt de simulatie in de VM gestopt, geüpload, geïnstalleerd en gestart.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Hoe kan ik de connection string van de IoT-hub die door mijn oplossing wordt gebruikt?

Als u uw oplossing hebt geïmplementeerd met het script in de opslagplaats , is de connection string de waarde van `build.ps1` **IotHubOwnerConnectionString** in het [](https://github.com/Azure/azure-iot-connected-factory) `<name of your deployment>.config.user` bestand.

U kunt de connection string ook vinden met behulp van Azure Portal. Zoek in IoT Hub resource in de resourcegroep van uw implementatie de connection string instellingen.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Welke IoT Hub gebruikt de Connected Factory-simulatie?

De simulatie registreert zelf de volgende apparaten:

* proxy.proxy.corp.contoso
* proxy.capeproxy.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.m proxy0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.publisher.corp.contoso
* publisher.cape corporation.contoso
* publisher.mumbai.corp.contoso
* publisher.m weergave0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Met behulp [van DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/) of het [hulpprogramma IoT-extensie](https://github.com/Azure/azure-iot-cli-extension) voor Azure CLI kunt u controleren welke apparaten zijn geregistreerd bij de IoT-hub die uw oplossing gebruikt. Als u Device Explorer wilt gebruiken, hebt u de connection string voor de IoT-hub in uw implementatie nodig. Als u de IoT-extensie voor Azure CLI wilt gebruiken, hebt u IoT Hub naam nodig.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Hoe kan ik logboekgegevens van de simulatieonderdelen krijgen?

Alle onderdelen in de simulatie melden zich aan bij logboekbestanden. Deze bestanden vindt u in de VM in de map `home/docker/Logs` . U kunt het PowerShell-script in de opslagplaats gebruiken om de logboeken `Simulation/Factory/Get-SimulationLogs.ps1` op [te halen.](https://github.com/Azure/azure-iot-connected-factory)

Dit script moet zich aanmelden bij de VM. Mogelijk moet u referenties voor de aanmelding verstrekken. Zie [Hoe kan ik aanmelden bij de simulatie-VM?](#how-do-i-sign-in-to-the-simulation-vm) om de referenties te vinden.

Met het script wordt een openbaar IP-adres aan de VM toegevoegd of verwijderd, als dit nog niet is en wordt het verwijderd. Het script plaatst alle logboekbestanden in een archief en downloadt het archief naar uw ontwikkelwerkstation.

U kunt zich ook aanmelden bij de VM via SSH en de logboekbestanden tijdens runtime inspecteren.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Hoe kan ik controleren of de simulatie gegevens naar de cloud stuurt?

Met de [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) of de monitorgebeurtenissen van de [Azure IoT CLI-extensie](/cli/azure/iot/hub#az_iot_hub_monitor_events) kunt u de gegevens inspecteren die vanaf bepaalde apparaten naar IoT Hub verzonden. Als u deze hulpprogramma's wilt gebruiken, moet u de connection string voor de IoT-hub in uw implementatie kennen. Zie Hoe kan ik de connection string van de [IoT-hub](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution) die door mijn oplossing wordt gebruikt?

Inspecteer de gegevens die worden verzonden door een van de uitgeversapparaten:

* publisher.publisher.corp.contoso
* publisher.cape corporation.contoso
* publisher.mumbai.corp.contoso
* publisher.m weergave0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Als er geen gegevens naar de IoT Hub verzonden, is er een probleem met de simulatie. Als eerste analysestap moet u de logboekbestanden van de simulatieonderdelen analyseren. Zie [Hoe kan ik logboekgegevens van de simulatieonderdelen krijgen?](#how-can-i-get-log-data-from-the-simulation-components) Probeer vervolgens de simulatie te stoppen en te starten. Als er nog steeds geen gegevens worden verzonden, moet u de simulatie volledig bijwerken. Zie [Hoe kan ik de simulatie in de VM bijwerken?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Hoe kan ik interactieve kaart inschakelen in mijn Connected Factory-oplossing?

Als u een interactieve kaart in uw Oplossing voor verbonden factory's wilt inschakelen, moet u een Azure Maps account.

Bij het implementeren [vanuit www.azureiotsolutions.com](https://www.azureiotsolutions.com)voegt het implementatieproces een Azure Maps-account toe aan de resourcegroep die de services van de oplossingsversneller bevat.

Wanneer u implementeert met behulp van het script in de GitHub-opslagplaats connected factory, stelt u de omgevingsvariabele `build.ps1` `$env:MapApiQueryKey` in het build-venster in op de sleutel [van Azure Maps account](../azure-maps/how-to-manage-account-keys.md). De interactieve kaart wordt vervolgens automatisch ingeschakeld.

U kunt na de implementatie ook een Azure Maps-accountsleutel toevoegen aan uw oplossingsversneller. Navigeer naar de Azure Portal en ga naar App Service resource in uw Connected Factory-implementatie. Navigeer **naar Toepassingsinstellingen,** waar u een sectie **Toepassingsinstellingen vindt.** Stel **MapApiQueryKey in** op de [sleutel van uw Azure Maps account](../azure-maps/how-to-manage-account-keys.md). Sla de instellingen op, navigeer naar **Overzicht** en start de App Service.

### <a name="how-do-i-create-an-azure-maps-account"></a>Hoe kan ik een account Azure Maps maken?

Zie How [to manage your Azure Maps account and keys (Uw](../azure-maps/how-to-manage-account-keys.md)account en sleutels beheren).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Uw accountsleutel Azure Maps verkrijgen

Zie How [to manage your Azure Maps account and keys (Uw](../azure-maps/how-to-manage-account-keys.md)account en sleutels beheren).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Hoe kunt u de interactieve kaart inschakelen tijdens lokaal debuggen?

Als u de interactieve kaart wilt inschakelen terwijl u lokaal debuggen bent, stelt u de waarde van de instelling in de bestanden en in de hoofdmap van uw implementatie in op de waarde van de QueryKey die u eerder hebt `MapApiQueryKey` `local.user.config` `<yourdeploymentname>.user.config` gekopieerd. 

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Hoe kan ik een andere afbeelding op de startpagina van mijn dashboard gebruiken?

Als u de statische afbeelding wilt wijzigen die wordt weergegeven op de startpagina van het dashboard, vervangt u de afbeelding `WebApp\Content\img\world.jpg` . Bouw vervolgens de web-app opnieuw enployer deze opnieuw.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Hoe kan ik niet-OPC UA-apparaten gebruiken met Verbonden factory?

Telemetriegegevens van niet OPC UA-apparaten verzenden naar verbonden factory:

1. [Configureer een nieuw station in de topologie van de verbonden](iot-accelerators-connected-factory-configure.md) factory in het `ContosoTopologyDescription.json` bestand.

1. De telemetriegegevens opnemen in de JSON-indeling die compatibel is met Connected Factory:

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. De indeling van `<timestamp>` is: `2017-12-08T19:24:51.886753Z`

1. Start de connected factory opnieuw App Service.

### <a name="next-steps"></a>Volgende stappen

U kunt ook enkele van de andere functies en mogelijkheden van de IoT-oplossingsversnellers bekijken:

* [Oplossingsversneller voor verbonden factory's implementeren](quickstart-connected-factory-deploy.md)
* [Fundamentele IoT-beveiliging](../iot-fundamentals/iot-security-ground-up.md)