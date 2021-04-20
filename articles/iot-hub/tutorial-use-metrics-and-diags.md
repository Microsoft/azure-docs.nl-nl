---
title: 'Zelfstudie: metrische gegevens en logboeken instellen en gebruiken met een Azure IoT-hub'
description: 'Zelfstudie: meer informatie over het instellen en gebruiken van metrische gegevens en logboeken met een Azure IoT-hub. Zo beschikt u over gegevens die u kunt analyseren om problemen met de hub op te sporen.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: 62958dc374598e6f530af398f722001e5ed51acd
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739688"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>Zelfstudie: Metrische gegevens en logboeken instellen en gebruiken met een IoT-hub

U kunt met Azure Monitor metrische gegevens en logboeken verzamelen voor uw IoT-hub, waarmee u de werking van uw oplossing kunt controleren en problemen kunt oplossen wanneer deze zich voordoen. In dit artikel leest u hoe u grafieken maakt op basis van metrische gegevens, waarschuwingen maakt die worden geactiveerd op basis van metrische gegevens, IoT Hub-bewerkingen en -fouten naar Azure Monitor-logboeken verzendt en de logboeken controleert op fouten.

In deze zelfstudie wordt gebruikgemaakt van het Azure-voorbeeld uit de [quickstart Telemetrie verzenden (.NET)](quickstart-send-telemetry-dotnet.md) om berichten te verzenden naar de IoT-hub. U kunt altijd een apparaat of een ander voorbeeld gebruiken om berichten te verzenden, maar mogelijk moet u in dat geval een paar stappen aanpassen.

Het is handig om enigszins bekend te zijn met Azure Monitor-concepten wanneer u met deze zelfstudie begint. Zie [Azure IoT Hub controleren](monitor-iot-hub.md) voor meer informatie. Zie [Naslaginformatie voor het controleren van gegevens](monitor-iot-hub-reference.md) voor meer informatie over de metrische gegevens en -resourcelogboeken die worden verzonden door IoT Hub.

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
>
> * Azure CLI gebruiken om een IoT-hub te maken, een gesimuleerd apparaat te registreren en een Log Analytics-werkruimte te maken.  
> * IoT Hub-verbindingen en -resourcelogboeken met apparaattelemetrie verzenden naar Azure Monitor-logboeken in de Log Analytics-werkruimte.
> * Metrics Explorer gebruiken om een diagram te maken op basis van de geselecteerde metrische gegevens en deze vastmaken aan uw dashboard.
> * Waarschuwingen voor metrische gegevens maken zodat u per e-mail kunt worden geïnformeerd wanneer er aan belangrijke voorwaarden wordt voldaan.
> * Een app downloaden en uitvoeren waarmee een IoT-apparaat wordt gesimuleerd dat berichten naar de IoT-hub verzendt.
> * De waarschuwingen weergeven wanneer aan de voorwaarden wordt voldaan.
> * Het diagram met metrische gegevens weergeven op uw dashboard.
> * IoT Hub-fouten en -bewerkingen weergeven in Azure Monitor-logboeken.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

- .NET Core SDK 2.1 of hoger moet zijn geïnstalleerd op uw ontwikkelcomputer. U kunt de .NET Core SDK voor meerdere platforms downloaden van [.NET](https://www.microsoft.com/net/download/all).

  Gebruik de volgende opdracht om de huidige versie van C# op uw ontwikkelcomputer te controleren:

  ```cmd/sh
  dotnet --version
  ```

- Een e-mailaccount dat e-mail kan ontvangen.

- Zorg ervoor dat de poort 8883 is geopend in de firewall. In het apparaatvoorbeeld in deze zelfstudie wordt het MQTT-protocol gebruikt, dat communiceert via poort 8883. Deze poort is in sommige netwerkomgevingen van bedrijven en onderwijsinstellingen mogelijk geblokkeerd. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub) voor meer informatie en manieren om dit probleem te omzeilen.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="set-up-resources"></a>Resources instellen

Voor deze zelfstudie hebt u een IoT-hub, een Log Analytics-werkruimte en een gesimuleerd IoT-apparaat nodig. Deze resources kunnen worden gemaakt met Azure CLI of Azure PowerShell. Gebruik dezelfde resourcegroep en -locatie voor alle resources. Wanneer u klaar bent met de zelfstudie, kunt u alles in één stap verwijderen door de resourcegroep te verwijderen.

Hier volgen de vereiste stappen.

1. Maak een [resourcegroep](../azure-resource-manager/management/overview.md).

2. Maak een IoT-hub.

3. Maak een Log Analytics-werkruimte.

4. Maak een apparaat-id voor het gesimuleerde apparaat dat berichten naar uw IoT-hub verzendt. Sla de apparaatverbindingstekenreeks op die u gebruikt om het gesimuleerde apparaat te configureren.

### <a name="set-up-resources-using-azure-cli"></a>Resources instellen met behulp van Azure CLI

Kopieer en plak dit script in Cloud Shell. Ervan uitgaande dat u al bent aangemeld, wordt het script met één regel tegelijk uitgevoerd. Het uitvoeren van sommige opdrachten kan enige tijd in beslag nemen. De nieuwe resources worden gemaakt in de resourcegroep *ContosoResources*.

De naam van sommige resources moet uniek zijn binnen Azure. Het script genereert een willekeurige waarde met de functie `$RANDOM` en slaat deze op in een variabele. Voor deze resources voegt het script deze willekeurige waarde toe aan een basisnaam voor de resource, waardoor de resourcenaam uniek is.

Er is per abonnement maar één gratis IoT-hub toegestaan. Als u al een gratis IoT-hub hebt in uw abonnement, verwijdert u deze voordat u het script uitvoert of wijzigt u het script zodat gebruik wordt gemaakt van uw gratis IoT-hub of van een IoT-hub die gebruikmaakt van de servicelaag Standard of Basic.

Met het script wordt het volgende weergegeven: de naam van de IoT-hub, de naam van de Log Analytics-werkruimte en de verbindingstekenreeks voor het apparaat dat wordt geregistreerd. Noteer deze gegevens, want u hebt ze later in dit artikel nodig.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device
randomValue=$RANDOM

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier. Partition count must be 2.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --partition-count 2 \
    --sku F1 --location $location

# The Log Analytics workspace name must be globally unique, so add a random number to the end.
workspaceName=contoso-la-workspace$randomValue
echo "Log Analytics workspace name = " $workspaceName


# Create the Log Analytics workspace
az monitor log-analytics workspace create --resource-group $resourceGroup \
    --workspace-name $workspaceName --location $location

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the primary connection string for the device identity, then copy it to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show-connection-string --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Bij het maken van de apparaat-id krijgt u mogelijk de volgende foutmelding: *Geen sleutels gevonden voor beleid iothubowner van IoT Hub ContosoTestHub*. U kunt deze fout corrigeren door de Azure CLI IoT-extensie bij te werken en vervolgens de laatste twee opdrachten in het script opnieuw uit te voeren. 
>
>Hier volgt de opdracht voor het bijwerken van de extensie. Voer deze opdracht uit in uw Cloud Shell-instantie.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>Logboeken voor verbindingen en apparaattelemetrie verzamelen

IoT Hub verzendt resourcelogboeken voor verschillende categorieën bewerkingen. Als u deze logboeken wilt bekijken, moet u echter een diagnostische instelling maken om deze naar een bestemming te verzenden. Een van deze bestemmingen is Azure Monitor-logboeken die worden verzameld in een Log Analytics-werkruimte. IoT Hub-resourcelogboeken zijn gegroepeerd in verschillende categorieën. U kunt in de diagnostische instelling selecteren welke categorieën u wilt verzenden naar Azure Monitor-logboeken. In dit artikel verzamelen we logboeken voor bewerkingen en fouten die zich voordoen met verbindingen en apparaattelemetrie. Zie [IoT Hub-resourcelogboeken](monitor-iot-hub-reference.md#resource-logs) voor een volledige lijst met categorieën die worden ondersteund voor IoT Hub.

Voer de volgende stappen uit om een diagnostische instelling te maken om IoT Hub-resourcelogboeken te verzenden naar Azure Monitor-logboeken:

1. Als u nog niet in uw hub in de portal bent, selecteert u eerst **Resourcegroepen** en vervolgens de resourcegroep ContosoResources. Selecteer uw IoT-hub in de lijst met resources die wordt weergegeven.

1. Zoek de sectie **Bewaking** in de IoT Hub-blade. Selecteer **Diagnostische instellingen**. Selecteer vervolgens **Diagnostische instelling toevoegen**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="Schermopname waarin Diagnostische instellingen in de sectie Bewaking is gemarkeerd.":::

1. Geef in het deelvenster **Diagnostische instelling** een beschrijvende naam aan uw instelling, zoals 'Verbindingen en telemetrie naar logboeken verzenden'.

1. Selecteer onder **Categoriegegevens** de opties **Verbindingen** en **Apparaattelemetrie**.

1. Selecteer onder **Bestemmingsgegevens** de optie **Verzenden naar Log Analytics** en gebruik vervolgens de Log Analytics-werkruimtekiezer om de werkruimte te selecteren die u eerder hebt genoteerd. Wanneer u klaar bent, moet de diagnostische instelling er ongeveer uitzien als in de volgende schermopname:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="Schermafbeelding met de uiteindelijke diagnostische-logboekinstellingen.":::

1. Selecteer **Opslaan** om de instellingen op te slaan. Sluit het deelvenster **Diagnostische instelling**. De nieuwe instelling wordt nu weergegeven in de lijst met diagnostische instellingen.

## <a name="set-up-metrics"></a>Metrische gegevens instellen

We gaan nu Metrics Explorer gebruiken om een diagram te maken waarin de metrische gegevens worden weergegeven die u wilt bijhouden. U kunt dit diagram vastmaken aan uw standaarddashboard in Azure Portal.

1. Selecteer in het linkerdeelvenster van uw IoT-hub, in de sectie **Controle** de optie **Metrische gegevens**.

1. Selecteer bovenaan het scherm de optie **Afgelopen 24 uur (automatisch)** . Selecteer in de vervolgkeuzelijst die wordt weergegeven **Afgelopen 4 uur** voor **Tijdsbereik**, stel **Tijdsgranulariteit** in op **1 minuut** en selecteer **Lokaal** voor **Tijd weergeven als**. Selecteer **Toepassen** om deze instellingen op te slaan. De instelling moet nu zijn **Lokale tijd: afgelopen 4 uur (1 minuut)** .

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="Schermafbeelding van de tijdinstellingen voor de metrische gegevens.":::

1. In het diagram wordt een gedeeltelijke metrische instelling weergegeven die binnen het bereik van uw IoT-hub ligt. Laat de standaardwaarden voor **Bereik** en **Naamruimte metrisch gegevens** ongewijzigd. Selecteer de instelling **Metrisch gegeven** en typ 'Telemetrie', en selecteer vervolgens **Verzonden telemetrieberichten** in de vervolgkeuzelijst. **Aggregatie** wordt automatisch ingesteld op **SUM**. U ziet dat de titel van uw diagram ook verandert.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="Schermopname waarin het toevoegen van het metrische gegeven Verzonden telemetrieberichten aan het diagram wordt weergegeven.":::

1. Selecteer nu **Metrisch gegeven toevoegen** om nog een metrisch gegeven aan het diagram toe te voegen. Selecteer onder **Metrische waarde**, selecteer **Totaal aantal gebruikte berichten**. **Aggregatie** wordt automatisch ingesteld op **Avg**. U ziet dat de titel van het diagram is gewijzigd en nu dit metrische gegeven bevat.

   Nu wordt op uw scherm de geminimaliseerde waarde weergegeven voor *Verzonden telemetrieberichten*, plus de nieuwe metrische waarde voor *Totaal aantal gebruikte berichten*.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="Schermopname waarin het toevoegen van het metrische gegeven Totaal aantal gebruikte berichten aan het diagram wordt weergegeven.":::

1. Selecteer **Vastmaken aan dashboard** in de rechterbovenhoek van het diagram.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="Schermopname met de knop Vastmaken aan dashboard gemarkeerd.":::

1. Selecteer in het deelvenster **Vastmaken aan dashboard** het tabblad **Bestaand**. Selecteer **Persoonlijk** en vervolgens **Dashboard** in de vervolgkeuzelijst voor Dashboard. Selecteer ten slotte **Vastmaken** om het diagram vast te maken aan uw standaarddashboard in Azure Portal. Als u uw diagram niet vastmaakt aan een dashboard, blijven uw instellingen niet behouden wanneer u Metrics Explorer afsluit.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="Schermopname met instellingen voor Vastmaken aan dashboard.":::

## <a name="set-up-metric-alerts"></a>Waarschuwingen voor metrische gegevens instellen

We gaan nu waarschuwingen instellen die moeten worden geactiveerd voor twee metrische gegevens: *Verzonden telemetrieberichten* en *Totaal aantal gebruikte berichten*.

*Verzonden telemetrieberichten* is een goed metrisch gegeven om de doorvoer van berichten te controleren en te voorkomen dat er vertragingen optreden. Voor een IoT-hub in de gratis servicelaag is de beperkingslimiet 100 berichten per seconde. Met één apparaat kunnen we die doorvoer niet realiseren. Daarom wordt de waarschuwing zo ingesteld dat deze wordt geactiveerd als het aantal berichten groter is dan 1000 berichten in vijf minuten. In een productieomgeving kunt u het signaal instellen op een significantere waarde op basis van de servicelaag, de editie en het aantal eenheden van uw IoT-hub.

*Totaal aantal gebruikte berichten* houdt het dagelijkse aantal gebruikte berichten bij. Dit metrische gegeven wordt elke dag om 00:00 UTC teruggezet op nul. Als u een bepaalde drempelwaarde voor het dagelijkse quotum overschrijdt, worden er door uw IoT-hub geen berichten meer geaccepteerd. Voor een IoT-hub in de gratis servicelaag is het dagelijkse quotum voor berichten 8000. We stellen de waarschuwing zo in dat deze wordt geactiveerd wanneer het totaalaantal berichten de 4000, 50% van het quotum, overschrijdt. In de praktijk stelt u dit percentage waarschijnlijk op een hogere waarde in. De waarde voor het dagelijkse quotum is afhankelijk van de servicelaag, de editie en het aantal eenheden van uw IoT-hub.

Zie [Quota en beperkingen](iot-hub-devguide-quotas-throttling.md) voor meer informatie over quotum- en beperkingslimieten in IoT Hub.

U stelt als volgt waarschuwingen voor metrische gegevens in:

1. Ga naar uw IoT-hub in Azure Portal.

1. Selecteer **Waarschuwingen** onder **Controle**. Selecteer vervolgens **Nieuwe waarschuwingsregel**.  Het deelvenster **Waarschuwingsregel maken** wordt geopend.

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="Schermopname van het deelvenster Waarschuwingsregel maken.":::

    Het deelvenster **Waarschuwingsregel maken** omvat vier secties:

    * **Bereik** is al ingesteld op uw IoT-hub. We slaan deze sectie dus over.
    * Met **Voorwaarde** worden het signaal en de voorwaarden ingesteld op basis waarvan de waarschuwing wordt geactiveerd.
    * Met **Acties** wordt geconfigureerd wat er gebeurt wanneer de waarschuwing wordt geactiveerd.
    * Met **Waarschuwingsregelgegevens** kunt u een naam en beschrijving voor de waarschuwing instellen.

1. Configureer eerst de voorwaarde op basis waarvan de waarschuwing wordt geactiveerd.

    1. Selecteer **onder Voorwaarde** de optie Voorwaarde **toevoegen.** Typ in het deelvenster **Signaallogica configureren** de tekst 'Telemetrie' in het zoekvak en selecteer **Verzonden telemetrieberichten**.

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="Schermopname waarin het selecteren van het metrische gegeven wordt weergegeven.":::

    1. Stel in het deelvenster **Signaallogica configureren** de volgende velden onder **Waarschuwingslogica** in of bevestig deze (u kunt het diagram negeren):

       **Drempel**:  *statisch*.

       **Operator**: *groter dan*.

       **Type aggregatie**: *totaal*.

       **Drempelwaarde**: 1000.

       **Aggregatiegranulariteit (periode)** : *5 minuten*.

       **Frequentie van evaluatie**: *elke minuut*

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="Schermopname met de instellingen voor de waarschuwingsvoorwaarden.":::

       Met deze instellingen wordt het signaal ingesteld op het totaalaantal berichten gedurende een periode van vijf minuten. Dit totaal wordt elke minuut geëvalueerd. Als het totaal voor de voorafgaande 5 minuten de 1000 berichten overschrijdt, wordt de waarschuwing geactiveerd.

       Selecteer **Gereed** om de signaallogica op te slaan.

1. Configureer nu de actie voor de waarschuwing.

    1. Selecteer in het **deelvenster Waarschuwingsregel maken** onder **Acties** de optie **Actiegroepen toevoegen.** Selecteer in het deelvenster **Een actiegroep selecteren om aan deze waarschuwingsregel toe te voegen** de optie **Actiegroep maken**.

    1. Geef op het tabblad **Basisgegevens** in het deelvenster **Actiegroep maken** een naam en een weergavenaam op voor de actiegroep.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="Schermopname van het tabblad Basisgegevens van het deelvenster Actiegroep maken.":::

    1. Selecteer het tabblad **Meldingen**. Selecteer voor **Meldingstype** de optie **E-mail/sms-bericht/pushmelding/spraakbericht** in de vervolgkeuzelijst. Het deelvenster **E-mail/sms-bericht/pushmelding/spraakbericht** wordt geopend.

    1. Selecteer in het deelvenster **E-mail/sms-bericht/pushmelding/spraakbericht** de optie voor e-mail, voer uw e-mailadres in en selecteer vervolgens **OK**.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="Schermopname met de e-mailadresinstelling.":::

    1. Voer in het deelvenster **Meldingen** een naam in voor de melding.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="Schermopname van het voltooide deelvenster Meldingen.":::

    1. (Optioneel) Als u het tabblad **Acties** selecteert en vervolgens de vervolgkeuzelijst **Actietype** selecteert, worden de soorten acties weergegeven die u kunt activeren bij een waarschuwing. Voor dit artikel worden alleen meldingen gebruikt, zodat u de instellingen op dit tabblad kunt negeren.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="Schermopname met de beschikbare actietypen in het deelvenster Acties.":::

    1. Selecteer het tabblad **Controleren en maken**, controleer uw instellingen en selecteer **Maken**.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="Schermopname van het deelvenster Controleren en maken.":::

    1. In het deelvenster **Waarschuwingsregel maken** is nu de nieuwe actiegroep toegevoegd aan de acties voor de waarschuwing.  

1. Configureer ten slotte de waarschuwingsregelgegevens en sla de waarschuwingsregel op.

    1. Voer in het deelvenster **Waarschuwingsregel maken** onder Waarschuwingsregelgegevens een naam en een beschrijving in voor de waarschuwing. Bijvoorbeeld 'Waarschuwen bij meer dan 1000 berichten in 5 minuten'. Zorg ervoor dat **Waarschuwingsregel inschakelen bij het maken** is ingeschakeld. De voltooide waarschuwingsregel ziet er ongeveer uit als op deze schermopname.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="Schermopname van het voltooide deelvenster Waarschuwingsregel maken.":::

    1. Selecteer **Waarschuwingsregel maken** om de nieuwe regel op te slaan.

1. Stel nu een andere waarschuwing in voor het *totale aantal gebruikte berichten*. Dit metrische gegeven is handig als u een waarschuwing wilt verzenden wanneer het aantal gebruikte berichten bijna het dagelijkse quotum voor de IoT-hub heeft bereikt. Op dat moment zal de hub namelijk berichten gaan weigeren. Voer de stappen uit die u eerder hebt uitgevoerd, met de volgende verschillen.

    * Selecteer voor het signaal de optie **Totaal aantal gebruikte berichten** in het deelvenster **Signaallogica configureren**.

    * Stel in het deelvenster **Signaallogica configureren** de volgende velden in of bevestig deze (u kunt het diagram negeren):

       **Drempel**:  *statisch*.

       **Operator**: *groter dan*.

       **Type aggregatie**: *maximum*.

       **Drempelwaarde**: 4000.

       **Aggregatiegranulariteit (periode)** : *1 minuut*.

       **Frequentie van evaluatie**: *elke minuut*

       Met deze instellingen wordt het signaal geactiveerd wanneer het aantal berichten de 4000 bereikt. Het metrische gegeven wordt elke minuut geëvalueerd.

    * Wanneer u de actie voor uw waarschuwingsregel opgeeft, selecteert u de actiegroep die u eerder hebt gemaakt.

    * Kies voor de waarschuwingsgegevens een andere naam en beschrijving dan u eerder hebt gedaan.

1. Selecteer **Waarschuwingen** onder **Controle** in het linkerdeelvenster van uw IoT-hub. Selecteer nu **Waarschuwingsregels beheren** in het menu bovenaan het deelvenster **Waarschuwingen**. Het deelvenster **Regels** wordt geopend. Uw twee waarschuwingen moeten nu worden weergegeven:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="Schermopname van het deelvenster Regels met de nieuwe waarschuwingsregels.":::

1. Sluit het deelvenster **Regels**.

Met deze instellingen wordt een waarschuwing geactiveerd en ontvangt u een e-mailmelding wanneer er meer dan 1000 berichten worden verzonden binnen een tijdsbestek van vijf minuten. De waarschuwing wordt ook geactiveerd wanneer het totale aantal gebruikte berichten groter is dan 4000 (50% van het dagelijkse quotum voor een IoT-hub in de gratis servicelaag).

## <a name="run-the-simulated-device-app"></a>De app voor een gesimuleerd apparaat uitvoeren

In de sectie [Resources instellen](#set-up-resources) hebt u een apparaat-id geregistreerd voor het simuleren van een IoT-apparaat. In deze sectie downloadt u een .NET-console-app die een apparaat simuleert dat berichten van apparaat naar cloud verzendt naar een IoT-hub. Configureer de app zodat deze berichten naar uw IoT-hub verzendt en voer de app vervolgens uit.

> [!IMPORTANT]
>
> Het kan tot tien minuten duren voordat waarschuwingen volledig zijn geconfigureerd en ingeschakeld door IoT Hub. Wacht minimaal tien minuten tussen het moment waarop u de laatste waarschuwing hebt geconfigureerd en het moment waarop u de gesimuleerde apparaat-app uitvoert.

Download de oplossing voor de [IoT-apparaatsimulatie](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Met deze koppeling downloadt u een opslagplaats die meerdere apps bevat. De app die u zoekt, bevindt zich in iot-hub/Quickstarts/simulated-device/.

1. Navigeer in een lokaal terminalvenster naar de hoofdmap van de oplossing. Navigeer vervolgens naar de map **iot-hub\Quickstarts\simulated-device**.

1. Open het bestand **SimulatedDevice.cs** in een teksteditor van uw keuze.

    1. Vervang de waarde van de variabele `s_connectionString` door de apparaatverbindingstekenreeks die u hebt genoteerd toen u het script voor het instellen van de resources hebt uitgevoerd.

    1. Wijzig in de methode `SendDeviceToCloudMessagesAsync` de `Task.Delay` van 1000 in 1, waardoor de hoeveelheid tijd tussen het verzenden van berichten wordt gewijzigd van 1 seconde in 0,001 seconden. Door deze vertraging te verkorten, wordt het aantal verzonden berichten vergroot. (U bereikt waarschijnlijk geen berichtfrequentie van 100 berichten per seconde.)

        ```csharp
        await Task.Delay(1);
        ```

    1. Sla de wijzigingen op in **SimulatedDevice.cs**.

1. Voer in het lokale terminalvenster de volgende opdracht uit om de vereiste pakketten te installeren voor de app voor het gesimuleerde apparaat:

    ```cmd/sh
    dotnet restore
    ```

1. Voer in het lokale terminalvenster de volgende opdracht uit om de toepassing voor het gesimuleerde apparaat te compileren en uit te voeren:

    ```cmd/sh
    dotnet run
    ```

    In de volgende schermafbeelding ziet u de uitvoer op het moment dat de toepassing voor het gesimuleerde apparaat telemetriegegevens naar uw IoT-hub verzendt:

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="Schermopname met de uitvoer van het gesimuleerde apparaat.":::

Zorg dat de app ten minste 10-15 minuten wordt uitgevoerd. U kunt de app het beste laten werken totdat het verzenden van berichten wordt gestopt (na ongeveer 20-30 minuten). Dit gebeurt wanneer u het dagelijkse quotum voor berichten voor uw IoT-hub hebt overschreden en deze geen berichten meer accepteert.

> [!NOTE]
> Als u de apparaat-app gedurende langere tijd laat werken nadat het verzenden van berichten is gestopt, kan er een uitzondering optreden. U kunt deze uitzondering gerust negeren en het app-venster sluiten.

## <a name="view-metrics-chart-on-your-dashboard"></a>Het diagram met metrische gegevens weergeven op uw dashboard

1. Open in de linkerbovenhoek van Azure Portal het portalmenu en selecteer vervolgens **Dashboard**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="Schermopname waarin wordt getoond hoe u het dashboard selecteert.":::

1. Ga naar het diagram dat u eerder hebt vastgemaakt en klik ergens op de tegel buiten de diagramgegevens om het uit te vouwen. In het diagram worden de verzonden telemetrieberichten en het totale aantal gebruikte berichten weergegeven. De meest recente cijfers worden onderaan in het diagram weergegeven. U kunt de cursor in het diagram verplaatsen om de metrische waarden voor specifieke tijdstippen te bekijken. U kunt ook de tijdwaarde en de granulariteit boven in het diagram wijzigen om de gegevens te beperken tot of uit te breiden naar een bepaalde periode.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="Schermopname van het diagram met metrische gegevens.":::

   In dit scenario is de berichtendoorvoer van het gesimuleerde apparaat niet groot genoeg om ervoor te zorgen dat IoT Hub de berichtenstroom beperkt. In een scenario waarbij sprake is van beperking, kan het voorkomen dat het aantal verzonden telemetrieberichten de beperkingslimiet voor uw IoT-hub gedurende een beperkte periode overschrijdt. Op die manier wordt een grote toename van het verkeer veroorzaakt. Zie [Verkeer vormen](iot-hub-devguide-quotas-throttling.md#traffic-shaping) voor meer informatie.

## <a name="view-the-alerts"></a>De waarschuwingen weergeven

Wanneer het aantal verzonden berichten de limiet overschrijdt die u in de waarschuwingsregels hebt ingesteld, begint u e-mailwaarschuwingen te ontvangen.

Als u wilt bekijken of er actieve waarschuwingen zijn, selecteert u **Waarschuwingen** onder **Controle** in het linkerdeelvenster van uw IoT-hub. In het deelvenster **Waarschuwingen** wordt het aantal waarschuwingen dat is geactiveerd op ernst gesorteerd voor het opgegeven tijdsbereik.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="Schermopname met het overzicht van waarschuwingen.":::

Selecteer de rij voor ernst Sev 3. Het deelvenster **Alle waarschuwingen** wordt geopend en bevat de Sev 3-waarschuwingen die zijn geactiveerd.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="Schermopname met het deelvenster Alle waarschuwingen.":::

Selecteer een van de waarschuwingen om de details van de waarschuwing te bekijken.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="Schermopname met details van de waarschuwing.":::

Controleer of u in uw Postvak IN e-mails hebt ontvangen van Microsoft Azure. Op de onderwerpregel wordt de waarschuwing vermeld die is geactiveerd. Bijvoorbeeld *Azure: geactiveerde ernst: 3, waarschuwing bij meer dan 1000 berichten in 5 minuten*. De hoofdtekst moet er ongeveer uitzien als op deze afbeelding:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="Schermafbeelding van het e-mailbericht dat laat zien dat de waarschuwingen zijn geactiveerd.":::

## <a name="view-azure-monitor-logs"></a>Azure Monitor-logboeken weergeven

In de sectie [Logboeken voor verbindingen en apparaattelemetrie verzamelen](#collect-logs-for-connections-and-device-telemetry) hebt u een diagnostische instelling gemaakt om resourcelogboeken te verzenden die door uw IoT-hub naar Azure Monitor-logboeken worden verzonden voor verbindings- en apparaattelemetriebewerkingen. In deze sectie voert u een Kusto-query uit op Azure Monitor-logboeken om na te gaan of er fouten zijn opgetreden.

1. Selecteer onder **Controle** in het linkerdeelvenster van uw IoT-hub in Azure Portal de optie **Logboeken**. Sluit het eerste venster **Query's** als dit wordt geopend.

1. Selecteer in het deelvenster Nieuwe query het tabblad **Query's** en vouw vervolgens **IoT Hub** uit om de lijst met standaardquery's weer te geven.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="Schermopname met IoT Hub-standaardquery's.":::

1. Selecteer de query *Foutenoverzicht*. De query wordt weergegeven in het deelvenster Query-editor. Selecteer **Uitvoeren** in het deelvenster Editor en bekijk de queryresultaten. Vouw een van de rijen uit om de details te bekijken.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="Schermopname van de logboeken die worden geretourneerd door de query Foutenoverzicht.":::

   > [!NOTE]
   > Als er geen fouten worden weergegeven, kunt u de query *Recent verbonden apparaten* uitvoeren. Hiermee wordt een rij geretourneerd voor het gesimuleerde apparaat.

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle resources die u in deze zelfstudie hebt gemaakt, wilt verwijderen, verwijdert u de resourcegroep. Hiermee verwijdert u ook alle resources in de groep. In dit geval worden de IoT-hub, de Log Analytics-werkruimte en de resourcegroep zelf verwijderd. Als u diagrammen met metrische gegevens aan het dashboard hebt vastgemaakt, moet u die handmatig verwijderen door op de drie puntjes in de rechterbovenhoek van elk diagram te klikken en **Verwijderen** te selecteren. Zorg ervoor dat u de wijzigingen opslaat nadat u de diagrammen hebt verwijderd.

U kunt de resourcegroep verwijderen met de opdracht [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd metrische gegevens en logboeken in IoT Hub te gebruiken door de volgende taken uit te voeren:

> [!div class="checklist"]
>
> * Azure CLI gebruiken om een IoT-hub te maken, een gesimuleerd apparaat te registreren en een Log Analytics-werkruimte te maken.  
> * IoT Hub-verbindingen en -resourcelogboeken met apparaattelemetrie verzenden naar Azure Monitor-logboeken in de Log Analytics-werkruimte.
> * Metrics Explorer gebruiken om een diagram te maken op basis van de geselecteerde metrische gegevens en deze vastmaken aan uw dashboard.
> * Waarschuwingen voor metrische gegevens maken zodat u per e-mail kunt worden geïnformeerd wanneer er aan belangrijke voorwaarden wordt voldaan.
> * Een app downloaden en uitvoeren waarmee een IoT-apparaat wordt gesimuleerd dat berichten naar de IoT-hub verzendt.
> * De waarschuwingen weergeven wanneer aan de voorwaarden wordt voldaan.
> * Het diagram met metrische gegevens weergeven op uw dashboard.
> * IoT Hub-fouten en -bewerkingen weergeven in Azure Monitor-logboeken.

Ga door naar de volgende zelfstudie voor informatie over het beheren van de toestand van een IoT-apparaat. 

> [!div class="nextstepaction"]
> [Uw apparaten configureren vanaf een back-endservice](tutorial-device-twins.md)