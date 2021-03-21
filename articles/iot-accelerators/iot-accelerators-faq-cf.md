---
title: Veelgestelde vragen over Connected Factory-oplossingen-Azure | Microsoft Docs
description: In dit artikel worden de veelgestelde vragen over de Connected Factory Solution Accelerator beantwoord. Het bevat koppelingen naar de GitHub-opslag plaats.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: c6a695030d399cb1609c63ef5652419a9bef1bbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198979"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Veelgestelde vragen over de oplossings versneller Connected Factory

Zie ook de algemene [Veelgestelde vragen](iot-accelerators-faq.md) over IOT-oplossings versnellers.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Waar kan ik de bron code voor de oplossings versneller vinden?

De bron code wordt opgeslagen in de volgende GitHub-opslag plaats:

* [Oplossings versneller Connected Factory](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Wat is OPC UA?

OPC Unified Architecture (UA), uitgebracht in 2008, is een platform onafhankelijke, service gerichte interoperabiliteits standaard. OPC UA wordt gebruikt door verschillende industriële systemen en apparaten, zoals industriële Pc's, PLCs en Sens oren. OPC UA integreert de functionaliteit van de standaard OPC-specificaties in één uitbreidbaar Framework met ingebouwde beveiliging. Het is een standaard die wordt aangedreven door de OPC Foundation. De [OPC-basis](https://opcfoundation.org/) is een organisatie zonder winst oogmerk met meer dan 440 leden. Het doel van de organisatie is de OPC-specificaties te gebruiken om multi-vendor, meerdere platformen, veilige en betrouw bare interoperabiliteit mogelijk te maken via:

* Infrastructuur
* Specificaties
* Technologie
* Processen

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Waarom heeft micro soft OPC UA gekozen voor de oplossings versneller Connected Factory?

Micro soft koos OPC UA omdat het een open, niet-eigendom, onafhankelijk, door de branche herkend en bewezen standaard is. Het is een vereiste voor industrie 4,0 (RAMI 4.0) referentie architectuur oplossingen om interoperabiliteit te garanderen tussen een groot aantal productie processen en apparatuur. Micro soft ziet vraag van haar klanten om industrie 4,0-oplossingen te bouwen. Ondersteuning voor OPC UA helpt de barrière te verlagen zodat klanten hun doel stellingen kunnen bereiken en direct bedrijfs waarde kunnen leveren.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Hoe kan ik een openbaar IP-adres aan de simulatie-VM toevoegen?

U hebt twee opties om het IP-adres toe te voegen:

* Gebruik het Power shell-script `Simulation/Factory/Add-SimulationPublicIp.ps1` in de [opslag plaats](https://github.com/Azure/azure-iot-connected-factory). Geef uw implementatie naam als een para meter door. Gebruik voor een lokale implementatie `<your username>ConnFactoryLocal` . Het script drukt het IP-adres van de virtuele machine af.

* Zoek in de Azure Portal de resource groep van uw implementatie. Met uitzonde ring van een lokale implementatie heeft de resource groep de naam die u hebt opgegeven als oplossing of implementatie naam. Voor een lokale implementatie met behulp van het build-script is de naam van de resource groep `<your username>ConnFactoryLocal` . Voeg nu een nieuwe **open bare IP-adres** bron toe aan de resource groep.

> [!NOTE]
> Zorg ervoor dat u de meest recente patches installeert met behulp van de instructies op de [Ubuntu-website](https://wiki.ubuntu.com/Security/Upgrades). Zorg ervoor dat de installatie up-to-date blijft, zolang uw virtuele machine toegankelijk is via een openbaar IP-adres.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Wilt u het open bare IP-adres Hoe kan ik verwijderen naar de simulatie-VM?

U hebt twee opties om het IP-adres te verwijderen:

* Gebruik de Power shell-script simulatie/fabriek/Remove-SimulationPublicIp.ps1 van de [opslag plaats](https://github.com/Azure/azure-iot-connected-factory). Geef uw implementatie naam als een para meter door. Gebruik voor een lokale implementatie `<your username>ConnFactoryLocal` . Het script drukt het IP-adres van de virtuele machine af.

* Zoek in de Azure Portal de resource groep van uw implementatie. Met uitzonde ring van een lokale implementatie heeft de resource groep de naam die u hebt opgegeven als oplossing of implementatie naam. Voor een lokale implementatie met behulp van het build-script is de naam van de resource groep `<your username>ConnFactoryLocal` . Verwijder nu de **open bare IP-adres** resource uit de resource groep.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Hoe kan ik meldt u zich aan bij de simulatie-VM?

Het aanmelden bij de simulatie-VM wordt alleen ondersteund als u uw oplossing hebt geïmplementeerd met behulp van het Power shell-script `build.ps1` in de [opslag plaats](https://github.com/Azure/azure-iot-connected-factory).

Als u de oplossing van www.azureiotsolutions.com hebt geïmplementeerd, kunt u zich niet aanmelden bij de VM. U kunt zich niet aanmelden, omdat het wacht woord wille keurig is gegenereerd en u het niet opnieuw kunt instellen.

1. Voeg een openbaar IP-adres toe aan de virtuele machine. Zie [Hoe kan ik een openbaar IP-adres toevoegen aan de simulatie-VM?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Maak een SSH-sessie met uw virtuele machine met het IP-adres van de virtuele machine.
1. De gebruikers naam die moet worden gebruikt is: `docker` .
1. Het wacht woord dat moet worden gebruikt, is afhankelijk van de versie die u hebt gebruikt om te implementeren:
    * Voor oplossingen die zijn geïmplementeerd met behulp van het build.ps1-script vóór 1 juni 2017, is het wacht woord: `Passw0rd` .
    * Voor oplossingen die zijn geïmplementeerd met behulp van het build.ps1-script na 1 juni 2017, kunt u het wacht woord in het `<name of your deployment>.config.user` bestand vinden. Het wacht woord wordt opgeslagen in de **VmAdminPassword** -instelling. Het wacht woord wordt wille keurig gegenereerd tijdens de implementatie, tenzij u het opgeeft met de `build.ps1` para meter script `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Hoe kan ik alle docker-processen in de simulatie-VM stoppen en starten?

1. Meld u aan bij de simulatie-VM. Zie [Hoe kan ik aanmelden bij de simulatie-VM?](#how-do-i-sign-in-to-the-simulation-vm)
1. Als u wilt controleren welke containers actief zijn, voert u uit: `docker ps` .
1. Als u alle simulatie containers wilt stoppen, voert u uit: `./stopsimulation` .
1. Alle simulatie containers starten:
    * Exporteer een shell-variabele met de naam **IOTHUB_CONNECTIONSTRING**. Gebruik de waarde van de instelling **IotHubOwnerConnectionString** in het `<name of your deployment>.config.user` bestand. Bijvoorbeeld:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Voer `./startsimulation` uit.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Wilt u de simulatie in de VM Hoe kan ik bijwerken?

Als u wijzigingen in de simulatie hebt aangebracht, kunt u het Power shell-script gebruiken `build.ps1` in de [opslag plaats](https://github.com/Azure/azure-iot-connected-factory) met behulp van de `updatedimulation` opdracht. Met dit script worden alle simulatie onderdelen gebouwd, wordt de simulatie van de VM gestopt, worden de werk last geüpload, geïnstalleerd en gestart.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Hoe kan ik de connection string van de IoT-hub die wordt gebruikt door mijn oplossing?

Als u uw oplossing hebt geïmplementeerd met het `build.ps1` script in de [opslag plaats](https://github.com/Azure/azure-iot-connected-factory), is het Connection String de waarde van **IotHubOwnerConnectionString** in het `<name of your deployment>.config.user` bestand.

U kunt de connection string ook vinden met behulp van de Azure Portal. Zoek de connection string-instellingen op in de resource groep van uw implementatie IoT Hub.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Welke IoT Hub-apparaten wordt met de verbonden Factory-simulatie gebruikt?

Met de simulatie zelf registreert u de volgende apparaten:

* proxy. Beijing. Corp. contoso
* proxy. Capetown. Corp. contoso
* proxy. Mumbai. Corp. contoso
* proxy. munich0. Corp. contoso
* proxy. Rio. Corp. contoso
* proxy. Seattle. Corp. contoso
* Publisher. Peking. Corp. contoso
* Publisher. Capetown. Corp. contoso
* Publisher. Mumbai. Corp. contoso
* Publisher. munich0. Corp. contoso
* Publisher. Rio. Corp. contoso
* Publisher. Seattle. Corp. contoso

Met behulp van de [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/) of [de IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension) kunt u controleren welke apparaten zijn geregistreerd bij de IOT-hub die door uw oplossing wordt gebruikt. Als u Device Explorer wilt gebruiken, hebt u de connection string voor de IoT-hub nodig in uw implementatie. Als u de IoT-extensie voor Azure CLI wilt gebruiken, hebt u uw IoT Hub naam nodig.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Hoe kan ik logboek gegevens ophalen uit de simulatie onderdelen?

Alle onderdelen in de simulatie logboek gegevens in naar logboek bestanden. Deze bestanden vindt u in de virtuele machine in de map `home/docker/Logs` . Als u de logboeken wilt ophalen, kunt u het Power shell-script gebruiken `Simulation/Factory/Get-SimulationLogs.ps1` in de [opslag plaats](https://github.com/Azure/azure-iot-connected-factory).

Dit script moet zich aanmelden bij de VM. Mogelijk moet u referenties opgeven voor de aanmelding. Zie [Hoe kan ik meld u aan bij de simulatie-VM](#how-do-i-sign-in-to-the-simulation-vm) om de referenties te vinden.

Met het script wordt een openbaar IP-adres toegevoegd aan of verwijderd uit de virtuele machine, als deze nog niet is gemaakt en verwijderd. Het script plaatst alle logboek bestanden in een archief en downloadt het archief naar uw ontwikkel werkstation.

Meld u aan bij de virtuele machine via SSH en Inspecteer de logboek bestanden tijdens runtime.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Hoe kan ik controleren of de simulatie gegevens verzendt naar de Cloud?

Met de [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer) -of de [Azure IOT cli-uitbreidings monitor-gebeurtenissen](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) opdracht kunt u de gegevens die naar IOT hub worden verzonden, inspecteren vanaf bepaalde apparaten. Als u deze hulpprogram ma's wilt gebruiken, moet u de connection string voor de IoT-hub in uw implementatie weten. Zie [Hoe kan ik de Connection String van de IOT-hub die wordt gebruikt door mijn oplossing vinden?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Inspecteer de gegevens die worden verzonden door een van de uitgevers-apparaten:

* Publisher. Peking. Corp. contoso
* Publisher. Capetown. Corp. contoso
* Publisher. Mumbai. Corp. contoso
* Publisher. munich0. Corp. contoso
* Publisher. Rio. Corp. contoso
* Publisher. Seattle. Corp. contoso

Als er geen gegevens worden weer gegeven die naar IoT Hub worden verzonden, is er een probleem met de simulatie. Als eerste analyse stap moet u de logboek bestanden van de simulatie onderdelen analyseren. Zie [Hoe kan ik logboek gegevens ophalen uit de simulatie onderdelen?](#how-can-i-get-log-data-from-the-simulation-components) Probeer vervolgens de simulatie te stoppen en te starten. als er nog steeds geen gegevens worden verzonden, moet u de simulatie volledig bijwerken. Zie [Hoe kan ik de simulatie in de VM bijwerken?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Hoe kan ik een interactieve kaart in mijn verbonden fabrieks oplossing inschakelen?

Als u een interactieve kaart in uw verbonden fabrieks oplossing wilt inschakelen, moet u een Azure Maps-account hebben.

Bij het implementeren vanuit [www.azureiotsolutions.com](https://www.azureiotsolutions.com)voegt het implementatie proces een Azure Maps-account toe aan de resource groep die de services voor de oplossings versneller bevat.

Wanneer u implementeert met behulp van het `build.ps1` script in de GitHub-opslag plaats Connected Factory, stelt u de omgevings variabele `$env:MapApiQueryKey` in het build-venster in op de [sleutel van uw Azure Maps-account](../azure-maps/how-to-manage-account-keys.md). De interactieve toewijzing wordt vervolgens automatisch ingeschakeld.

U kunt na de implementatie ook een Azure Maps account sleutel toevoegen aan uw oplossings versneller. Ga naar de Azure Portal en open de App Service resource in uw verbonden fabrieks implementatie. Ga naar **Toepassings instellingen**, waar u een sectie **Toepassings instellingen** vindt. Stel de **MapApiQueryKey** in op de [sleutel van uw Azure Maps-account](../azure-maps/how-to-manage-account-keys.md). Sla de instellingen op en navigeer vervolgens naar **overzicht** en start de app service opnieuw.

### <a name="how-do-i-create-an-azure-maps-account"></a>Hoe kan ik een Azure Maps-account maken?

Zie [hoe u uw Azure Maps-account en-sleutels beheert](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>De sleutel van uw Azure Maps-account verkrijgen

Zie [hoe u uw Azure Maps-account en-sleutels beheert](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Hoe kan ik de interactieve kaart inschakelen tijdens het lokaal debuggen?

Stel de waarde van de instelling `MapApiQueryKey` in de bestanden `local.user.config` en `<yourdeploymentname>.user.config` in de hoofdmap van uw implementatie in op de waarde van de **query sleutel** die u eerder hebt gekopieerd als u de interactieve map wilt inschakelen terwijl u fouten opspoort.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Hoe kan ik een andere afbeelding gebruiken op de start pagina van mijn dash board?

Als u de statische afbeelding wilt wijzigen, weer gegeven io de start pagina van het dash board, vervangt u de afbeelding `WebApp\Content\img\world.jpg` . Vervolgens bouwt en implementeert u de WebApp opnieuw.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Wilt u niet-OPC UA-apparaten met verbonden Factory Hoe kan ik gebruiken?

Telemetriegegevens van niet-OPC UA-apparaten verzenden naar een verbonden Factory:

1. [Configureer een nieuw station in de topologie Connected Factory](iot-accelerators-connected-factory-configure.md) in het `ContosoTopologyDescription.json` bestand.

1. De telemetrie-gegevens opnemen in de met de Connected Factory compatibele JSON-indeling:

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

1. Start de verbonden Factory-App Service opnieuw op.

### <a name="next-steps"></a>Volgende stappen

U kunt ook enkele van de andere functies en mogelijkheden van de IoT-oplossingsversnellers bekijken:

* [Connected Factory Solution Accelerator implementeren](quickstart-connected-factory-deploy.md)
* [Fundamentele IoT-beveiliging](../iot-fundamentals/iot-security-ground-up.md)