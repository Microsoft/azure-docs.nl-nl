---
title: 'Zelfstudie: De omgeving instellen - Machine learning op Azure IoT Edge'
description: 'Zelfstudie: Uw omgeving voorbereiden voor ontwikkeling en implementatie van modules voor machine learning aan de rand.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3a3518ba68c9474fc4a34390e6fd9a7d1e88f6c6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959591"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Zelfstudie: Een omgeving instellen voor machine learning op IoT Edge

In dit artikel leert u hoe u uw omgeving voorbereidt voor ontwikkeling en implementatie. Stel eerst een ontwikkelingsmachine in met alle hulpprogramma's die u nodig hebt. Maak vervolgens de benodigde cloudresources in Azure.

## <a name="prerequisites"></a>Vereisten

Dit artikel maakt deel uit van een reeks voor een zelfstudie over het gebruik van Azure Machine Learning in IoT Edge. Elk artikel in de reeks is gebaseerd op het werk in het vorige artikel. Als u rechtstreeks bij dit artikel terecht bent gekomen, gaat u naar de [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks.

## <a name="set-up-the-development-vm"></a>De virtuele machine voor ontwikkeling instellen

Deze stap wordt doorgaans uitgevoerd door een cloudontwikkelaar. Een deel van de software is wellicht ook handig voor gegevenswetenschappers.

We hebben een PowerShell-script gemaakt waarmee een virtuele Azure-machine wordt gemaakt waarop veel van de vereiste onderdelen al zijn geconfigureerd. Met de virtuele machine die we maken, moet [geneste virtualisatie](../virtual-machines/windows/nested-virtualization.md) kunnen worden gewerkt. Daarom kiezen we voor de standaardapparaatgrootte [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md).

De virtuele machine voor ontwikkeling wordt ingesteld met:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker Desktop voor Windows](https://www.docker.com/products/docker-desktop)
* [Git voor Windows](https://gitforwindows.org/)
* [Git Credential Manager voor Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core-SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](/powershell/azure/?view=azps-1.1.0)
* [VS Code-extensies](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

De virtuele machine voor ontwikkeling is niet per se noodzakelijk; alle ontwikkelingshulpprogramma's kunnen worden uitgevoerd op een lokale machine. U wordt echter ten zeerste aangeraden om de virtuele machine te gebruiken om een gelijkmatig werkgebied te hebben.

Het duurt langer dan 30 minuten om de virtuele machine te maken en configureren.

1. Kloon of download de voorbeeldopslagplaats [Machine Learning en IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) naar uw lokale computer.

1. Open PowerShell als beheerder en ga naar de **\IoTEdgeAndMlSample\DevVM**-map onder de hoofdmap waarin u de code hebt gedownload. De hoofdmap voor uw bron wordt `srcdir` genoemd.

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   De DevVM-map bevat de bestanden die nodig zijn om een virtuele Azure-machine te maken die geschikt is voor het voltooien van deze zelfstudie.

1. Voer de volgende opdracht uit om de uitvoering van scripts toe te staan. Kies **Ja op alles** wanneer u daarom wordt gevraagd.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Voer Create-AzureDevVM.ps1 uit.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    Geef de volgende informatie op wanneer u daarom wordt gevraagd:

    * **Azure-abonnements-id**: uw abonnements-id, die u kunt vinden bij [Azure-abonnementen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de portal.
    * **Naam resourcegroep**: de naam van een nieuwe of bestaande resourcegroep in Azure.
    * **Locatie**: kies een Azure-locatie waarin de virtuele machine wordt gemaakt, zoals US - west 2 of Europa - noord. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/) voor meer informatie.
    * **Gebruikersnaam**: geef een makkelijk te onthouden naam voor het beheerdersaccount voor de virtuele machine.
    * **Wachtwoord**: stel een wachtwoord voor het beheerdersaccount voor de virtuele machine in.

   Het script wordt enkele minuten uitgevoerd terwijl de volgende stappen worden uitgevoerd:

    1. hiermee wordt de [Az-module van Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) geïnstalleerd.
    1. U wordt gevraagd zich aan te melden bij Azure.
    1. Hiermee wordt de informatie voor het maken van uw virtuele machine bevestigd. Druk op **y** of **Enter** om door te gaan.
    1. Hiermee wordt de resourcegroep gemaakt als deze niet bestaat.
    1. Hiermee wordt de virtuele machine geïmplementeerd.
    1. Hiermee wordt Hyper-V ingeschakeld op de virtuele machine.
    1. Hiermee wordt software geïnstalleerd die nodig is voor ontwikkeling en de voorbeeldopslagplaats gekloond.
    1. Hiermee start u de virtuele machine op.
    1. Hiermee wordt een RDP-bestand op uw desktop gemaakt om verbinding te maken met de virtuele machine.

   Als u om de naam van de virtuele machine wordt gevraagd zodat deze opnieuw kan worden opgestart, kunt u de naam kopiëren vanuit de scriptuitvoer. In de uitvoer wordt ook het pad naar het RDP-bestand weergegeven om verbinding te maken met de virtuele machine.

### <a name="set-auto-shutdown-schedule"></a>Een planning voor automatisch afsluiten instellen

Voor het verlagen van de kosten is de virtuele machine voor ontwikkeling gemaakt met een planning voor automatisch afsluiten die is ingesteld op 1900 PST. Mogelijk moet u deze instelling bijwerken, afhankelijk van uw locatie en planning. U werkt de planning voor afsluiten als volgt bij:

1. Ga in Azure Portal naar de virtuele machine dat door het script is gemaakt.

1. Selecteer in het menu in het linkerdeelvenster onder **Bewerkingen** de optie **Automatisch afsluiten**.

1. Pas de **Geplande afsluiting** en **Tijdzone** aan uw wensen aan en selecteer **Opslaan**.

## <a name="connect-to-the-development-vm"></a>Verbinding maken met de virtuele machine voor ontwikkeling

Nu we een virtuele machine hebben gemaakt, moeten we de installatie van de software die nodig is voor het afronden van de zelfstudie voltooien.

1. Dubbelklik op het RDP-bestand dat door het script is gemaakt op uw desktop.

1. Er wordt een dialoogvenster weergegeven met de mededeling dat de uitgever van de externe verbinding niet bekend is. Dit is acceptabel, dus selecteer **Verbinden**.

1. Geef het beheerderswachtwoord op dat u hebt opgegeven om de virtuele machine te maken en klik op **OK**.

1. U wordt gevraagd om het certificaat voor de virtuele machine te accepteren. Selecteer **Ja**.

## <a name="install-visual-studio-code-extensions"></a>Visual Studio Code-extensies installeren

Nu u verbinding hebt gemaakt met de virtuele machine voor ontwikkeling, voegt u een aantal nuttige extensies toe aan Visual Studio Code om de ontwikkelingservaring eenvoudiger te maken.

1. Maak verbinding met de virtuele machine voor ontwikkeling, open een PowerShell-venster en ga naar de map **C:\source\IoTEdgeAndMlSample\DevVM**. Deze map is gemaakt door het script waarmee de virtuele machine is gemaakt.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Voer de volgende opdracht uit om de uitvoering van scripts toe te staan. Kies **Ja op alles** wanneer u daarom wordt gevraagd.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Voer het script voor Visual Studio Code-extensies uit.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. Het script wordt gedurende een aantal minuten uitgevoerd. Hierdoor worden VS Code-extensies geïnstalleerd:

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>IoT Hub en Storage instellen

Deze stappen worden doorgaans uitgevoerd door een cloudontwikkelaar.

Azure IoT Hub is het hart van elke IoT-toepassing omdat hiermee een veilige communicatie tussen IoT-apparaten en de cloud wordt verwerkt. Dit is het belangrijkste coördinatiepunt voor de werking van de IoT Edge machine learning-oplossing.

* In IoT Hub worden routes gebruikt om inkomende gegevens van IoT-apparaten naar andere downstreamservices om te leiden. Wij gebruiken IoT Hub-routes om apparaatgegevens naar Azure Storage te verzenden. In Azure Storage worden de apparaatgegevens door Azure Machine Learning gebruikt voor het trainen van onze RUL-classificatie (Remaining Useful Life).

* Later in de zelfstudie gebruiken we IoT Hub om ons Azure IoT Edge-apparaat te configureren en beheren.

In deze sectie gebruikt u een script om een Azure IoT-hub en een Azure Storage-account te maken. In Azure Portal configureert u vervolgens een route waardoor gegevens die door de hub zijn ontvangen, worden doorgestuurd naar een Azure Storage-container. Het uitvoeren van deze stappen duurt circa 10 minuten.

1. Maak verbinding met de virtuele machine voor ontwikkeling, open een PowerShell-venster en ga naar de **IoTHub**-map.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Voer het aanmaakscript uit. Gebruik dezelfde waarden voor de abonnements-id, locatie en resourcegroep als voor het maken van de virtuele machine voor ontwikkeling.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * U wordt gevraagd zich aan te melden bij Azure.
    * Het script bevestigt de informatie voor het maken van uw hub en opslagaccount. Druk op **y** of **Enter** om door te gaan.

Het uitvoeren van het script duurt circa twee minuten. Na voltooiing van het script worden de naam van de IoT-hub en het opslagaccount weergegeven.

## <a name="review-route-to-storage-in-iot-hub"></a>De route voor opslag in IoT Hub controleren

Als onderdeel van het maken van de IoT-hub worden ook een aangepast eindpunt en een route gemaakt door het script dat we in de vorige sectie hebben uitgevoerd. IoT Hub-routes bestaan uit een query-expressie en een eindpunt. Als een bericht overeenkomt met de expressie, worden de gegevens via de route naar het gekoppelde eindpunt verzonden. Eindpunten kunnen Event Hubs, Service Bus-wachtrijen en Onderwerpen zijn. In dit geval is het eindpunt een blobcontainer in een opslagaccount. We gebruiken Azure Portal om de route te controleren die door ons script is gemaakt.

1. Open [Azure Portal](https://portal.azure.com) en ga naar de resourcegroep die u voor deze zelfstudie gebruikt.

1. Selecteer in de lijst met resources de IoT-hub dat door het script is gemaakt. De naam hiervan eindigt op willekeurige tekens zoals `IotEdgeAndMlHub-jrujej6de6i7w`.

1. In het menu in het linkerdeelvenster selecteert u onder **Berichten** de optie **Routering bericht**.

1. Op de pagina **Berichtroutering** selecteert u het tabblad **Aangepaste eindpunten**.

1. Vouw de sectie **Opslag** uit:

   ![Controleren of turbofanDeviceStorage in de lijst met aangepaste eindpunten staat](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   We zien dat **turbofanDeviceStorage** in de lijst met aangepaste eindpunten staat. Let op de volgende kenmerken over dit eindpunt:

   * Het verwijst naar de blob-opslagcontainer die u hebt gemaakt met de naam `devicedata`, aangegeven door **Containernaam**.
   * De **Bestandsnaamindeling** bevat als laatste element in de naam een partitie. Deze indeling is handiger voor de bestandsbewerkingen die we later in de zelfstudie met Azure Notebooks zullen uitvoeren.
   * De **Status** moet In orde zijn.

1. Selecteer het tabblad **Routes**.

1. Selecteer de route met de naam **turbofanDeviceDataToStorage**.

1. U ziet op de pagina **Details van routes** dat het eindpunt van de route het eindpunt **turbofanDeviceStorage** is.

   ![Details over de turbofanDeviceDataToStorage-route controleren](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Bekijk de **Routeringsquery**. Deze is ingesteld op **true**. Deze instelling betekent dat alle apparaattelemetrieberichten met deze route overeenkomen en dat alle berichten daarom worden verzonden naar het **turbofanDeviceStorage**-eindpunt.

1. U kunt deze pagina gewoon sluiten omdat er geen bewerkingen zijn uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we een IoT-hub gemaakt en hebben we een route geconfigureerd naar een Azure Storage-account. Hierna verzenden we gegevens van een set gesimuleerde apparaten via de IoT-hub naar het opslagaccount. Later in de zelfstudie nadat we onze IoT Edge-apparaat en modules hebben geconfigureerd, kijken we opnieuw naar routes en besteden we meer aandacht aan de routeringsquery.

Voor meer informatie over de stappen die in dit deel van de zelfstudie Machine learning op IoT Edge worden behandeld, raadpleegt u:

* [Basisprincipes van Azure IoT](../iot-fundamentals/index.yml)
* [Berichtroutering configureren met IoT Hub](../iot-hub/tutorial-routing.md)
* [Een IoT-hub maken met behulp van Azure Portal](../iot-hub/iot-hub-create-through-portal.md)

Ga verder naar het volgende artikel om een gesimuleerd apparaat dat u wilt bewaken, te maken.

> [!div class="nextstepaction"]
> [Apparaatgegevens genereren](tutorial-machine-learning-edge-03-generate-data.md)