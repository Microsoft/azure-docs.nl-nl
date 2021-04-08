---
title: E-mail verzenden wanneer de deur wordt geopend met Azure Functions
description: Bewaak de magnetische sensor om te detecteren wanneer een deur wordt geopend en gebruik Azure Functions om een e-mail melding te verzenden.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: e3649ac786c07459695a1e6ca6ad52620e23d59f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148800"
---
# <a name="door-monitor----using-azure-functions-and-sendgrid-send-email-when-a-door-is-opened"></a>Monitor voor deuren: e-mail met Azure Functions en SendGrid verzenden wanneer een deur wordt geopend           

De MXChip IoT DevKit bevat een ingebouwde magnetische sensor. In dit project detecteert u de aanwezigheid of afwezigheid van een sterk magnetisch veld met een nabijgelegen niveau, in dit geval afkomstig van een kleine, permanente magneet.

## <a name="what-you-learn"></a>Wat u leert

In dit project leert u het volgende:
- De MXChip IoT DevKit-magneet sensor gebruiken om de verplaatsing van een nabijgelegen magneet te detecteren.
- Hoe u de SendGrid-service gebruikt om een melding te verzenden naar uw e-mail adres.

> [!NOTE]
> Voer de volgende taken uit voor een praktisch gebruik van dit project:
> - Een magneet aan de rand van een deur koppelen.
> - Koppel de DevKit aan de deur jamb dicht bij de magneet. Als de deur wordt geopend of gesloten, wordt de sensor geactiveerd, wat leidt tot een e-mail melding van de gebeurtenis.

## <a name="what-you-need"></a>Wat u nodig hebt

De aan de slag- [hand leiding](iot-hub-arduino-iot-devkit-az3166-get-started.md) volt ooien om:

* Uw DevKit verbonden met Wi-Fi
* De ontwikkelomgeving voorbereiden

Een actief Azure-abonnement. Als u er nog geen hebt, kunt u zich registreren via een van de volgende methoden:

* Activeer een [gratis Microsoft Azure-abonnement van 30 dagen](https://azure.microsoft.com/free/).
* Claim uw [Azure-tegoed](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) als u een MSDN-of Visual Studio-abonnee bent.

## <a name="deploy-the-sendgrid-service-in-azure"></a>De SendGrid-service in azure implementeren

[SendGrid](https://sendgrid.com/) is een e-mail bezorgings platform in de Cloud. Deze service wordt gebruikt om e-mail meldingen te verzenden.

> [!NOTE]
> Als u al een SendGrid-service hebt geïmplementeerd, kunt u direct door gaan met het [implementeren van IOT hub in azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>SendGrid-implementatie

Gebruik de knop **implementeren naar Azure** om Azure-Services in te richten. Met deze knop kunt u uw open-source projecten snel en eenvoudig implementeren om te Microsoft Azure.

Klik hieronder op de knop **implementeren naar Azure** . 

[![Implementeren in Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Als u nog niet bent aangemeld bij uw Azure-account, meldt u zich nu aan. 

U ziet nu het SendGrid-aanmeld formulier.

![SendGrid-implementatie](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Vul het formulier voor het aanmelden in:

   * **Resource groep**: Maak een resource groep om de SendGrid-service te hosten of gebruik een bestaande. Zie [resource groepen gebruiken om uw Azure-resources te beheren](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Naam**: de naam voor uw SendGrid-service. Kies een unieke naam die verschilt van andere services die u mogelijk hebt.

   * **Wacht woord**: de service vereist een wacht woord dat niet voor alles in dit project wordt gebruikt.

   * **E-mail**: de SendGrid-service stuurt verificatie naar dit e-mail adres.

Controleer de optie **vastmaken aan dash board** om deze toepassing in de toekomst gemakkelijker te kunnen vinden en klik vervolgens op **aanschaffen** om het aanmeld formulier in te dienen.
 
### <a name="sendgrid-api-key-creation"></a>SendGrid-API-sleutel maken

Nadat de implementatie is voltooid, klikt u erop en klikt u vervolgens op de knop **beheren** . Uw SendGrid-account pagina wordt weer gegeven, waar u uw e-mail adres moet verifiëren.

![SendGrid beheren](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Klik op de pagina SendGrid op **instellingen**  >  **API-sleutels**  >  **API-sleutel maken**.

![SendGrid eerst API maken](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Voer op de pagina **API-sleutel maken** de **naam** van de API-sleutel in en klik op **& weer gave maken**.

![SendGrid Create API Second](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Uw API-sleutel wordt slechts één keer weer gegeven. Zorg ervoor dat u deze veilig kopieert en opslaat, zoals in de volgende stap wordt gebruikt.

## <a name="deploy-iot-hub-in-azure"></a>IoT Hub implementeren in azure

Met de volgende stappen worden andere Azure IoT-gerelateerde services ingericht en worden Azure Functions voor dit project geïmplementeerd.

Klik hieronder op de knop **implementeren naar Azure** . 

[![Implementeren in Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Het formulier voor aanmelden wordt weer gegeven.

![IoTHub-implementatie](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Vul de velden in op het formulier voor registratie.

   * **Resource groep**: Maak een resource groep om de SendGrid-service te hosten of gebruik een bestaande. Zie [resource groepen gebruiken om uw Azure-resources te beheren](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **IOT-hub-naam**: de naam voor uw IOT-hub. Kies een unieke naam die verschilt van andere services die u mogelijk hebt.

   * **IOT hub SKU**: F1 (beperkt tot één per abonnement) is gratis. U kunt meer prijs informatie weer geven op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/iot-hub/).

   * **Van e-mail**: dit veld moet hetzelfde zijn als het e-mail adres dat u hebt gebruikt bij het instellen van de SendGrid-service.

Controleer de optie **vastmaken aan dash board** om deze toepassing in de toekomst gemakkelijker te kunnen vinden en klik vervolgens op **aanschaffen** wanneer u klaar bent om door te gaan naar de volgende stap.
 
## <a name="build-and-upload-the-code"></a>Bouw en upload de code

Laad vervolgens de voorbeeld code in VS code en richt de benodigde Azure-Services in.

### <a name="start-vs-code"></a>Begin VS code

- Zorg ervoor dat uw DevKit **niet** is verbonden met uw computer.
- Start VS Code.
- Verbind de DevKit met uw computer.

> [!NOTE]
> Wanneer u VS code start, wordt er mogelijk een fout bericht weer gegeven met de melding dat het Arduino IDE-of gerelateerde board-pakket niet kan worden gevonden. Als deze fout wordt weer gegeven, sluit u VS code, start u de Arduino IDE opnieuw en de VS code moet het Arduino IDE-pad correct vinden.

### <a name="open-arduino-examples-folder"></a>Map met voor beelden van Arduino openen

Vouw de sectie **ARDUINO-voor beelden** aan de linkerkant uit, blader naar voor **beelden voor MXCHIP AZ3166 > AzureIoT** en selecteer **DoorMonitor**. Met deze actie wordt een nieuw versus code venster geopend met daarin een projectmap.

![Mini-Solution-voor beelden](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

U kunt ook de voor beeld-app openen vanuit het opdracht palet. Gebruik `Ctrl+Shift+P` (macOS: `Cmd+Shift+P` ) om het opdracht palet te openen, typ **Arduino** en zoek en selecteer vervolgens **Arduino: voor beelden**.

### <a name="provision-azure-services"></a>Azure-Services inrichten

Voer in het oplossings venster de taak Cloud inrichting uit:
- Type `Ctrl+P` (macOS: `Cmd+P` ).
- Typ `task cloud-provision` in het opgegeven tekstvak.

In de VS code-terminal wordt u door een interactieve opdracht regel begeleid bij het inrichten van de vereiste Azure-Services. Selecteer alle items uit de lijst met vragen die u eerder hebt ingericht in [implementatie IOT hub in azure](#deploy-iot-hub-in-azure).

![Cloud inrichting](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Als de pagina in de laad status vastloopt wanneer u zich probeert aan te melden bij Azure, raadpleegt u de [sectie ' de pagina loopt vast wanneer u zich aanmeldt ' in de veelgestelde vragen over IOT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) om dit probleem op te lossen. 

### <a name="build-and-upload-the-device-code"></a>Bouw en upload de toestel code

Upload vervolgens de code voor het apparaat.

#### <a name="windows"></a>Windows

1. Gebruiken `Ctrl+P` om uit te voeren `task device-upload` .

2. De Terminal vraagt u de configuratie modus in te voeren. Als u dit wilt doen, houdt u de knop A ingedrukt en geeft u de knop opnieuw instellen op. In het scherm worden het DevKit-identificatie nummer en de woord *configuratie* weer gegeven.

#### <a name="macos"></a>macOS

1. De DevKit in de configuratie modus zetten: Houd de knop ingedrukt en druk vervolgens de knop Reset. In het scherm wordt ' configuratie ' weer gegeven.

2. Klik `Cmd+P` om uit te voeren `task device-upload` .

#### <a name="verify-upload-and-run-the-sample-app"></a>De voor beeld-app verifiëren, uploaden en uitvoeren

De connection string die wordt opgehaald uit de stap voor het [inrichten van Azure-Services](#provision-azure-services) is nu ingesteld. 

VS code controleert vervolgens de Arduino-schets en uploadt deze naar de DevKit.

![Scherm afbeelding toont Visual Studio code voor het controleren en uploaden van de Arduino-schets.](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

De DevKit wordt opnieuw opgestart en het uitvoeren van de code wordt gestart.

> [!NOTE]
> In sommige gevallen wordt het fout bericht ' fout: AZ3166: onbekend pakket ' weer gegeven. Deze fout treedt op wanneer de pakket index van het board niet correct wordt vernieuwd. Raadpleeg de [sectie ontwikkeling van de veelgestelde vragen over IOT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)om deze fout op te lossen.

## <a name="test-the-project"></a>Het project testen

Het programma wordt eerst geïnitialiseerd wanneer de DevKit zich in de aanwezigheid van een stabiel magnetisch veld bevindt.

Na de initialisatie `Door closed` wordt weer gegeven op het scherm. Als er een wijziging in het magnetische veld is, wordt de status gewijzigd in `Door opened` . Telkens wanneer de status van de deur verandert, ontvangt u een e-mail melding. (Het kan vijf minuten duren voordat deze e-mail berichten zijn ontvangen.)

![Magneten dicht bij de sensor: deur gesloten](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Magneten dicht bij de sensor: deur gesloten")

![De magneet is verwijderd van de sensor: de deur is geopend](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "De magneet is verwijderd van de sensor: de deur is geopend")

## <a name="problems-and-feedback"></a>Problemen en feedback

Als u problemen ondervindt, raadpleegt u de [Veelgestelde vragen over IOT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) of maakt u verbinding met behulp van de volgende kanalen:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u een DevKit-apparaat verbindt met uw oplossings versneller van Azure IoT voor externe controle en de SendGrid-service gebruikt om een e-mail te verzenden. Hier volgt de voorgestelde volgende stap:[overzicht van de Azure IOT-oplossing voor externe controle](/azure/iot-suite/)