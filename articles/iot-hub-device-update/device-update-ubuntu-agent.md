---
title: Zelf studie over het bijwerken van apparaten voor Azure IoT Hub met behulp van de Ubuntu Server 18,04 x64-pakket agent | Microsoft Docs
description: Ga aan de slag met het bijwerken van het apparaat voor Azure IoT Hub met behulp van de Ubuntu Server 18,04 x64 package agent.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 751e9337d74210d238be079e8fcd1bb973937846
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936849"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-package-agent-on-ubuntu-server-1804-x64"></a>Zelf studie over het bijwerken van apparaten voor Azure IoT Hub met behulp van de pakket agent op Ubuntu Server 18,04 x64

Update van het apparaat voor IoT Hub ondersteunt twee soorten updates: op installatie kopie gebaseerd en op basis van een pakket.

Updates op basis van pakketten zijn gerichte updates waarmee alleen een specifiek onderdeel of een specifieke toepassing op het apparaat wordt gewijzigd. Dit leidt tot een lager verbruik van de band breedte en vermindert de tijd voor het downloaden en installeren van de update. Pakket updates bieden doorgaans minder downtime van apparaten bij het Toep assen van een update en vermijden de overhead van het maken van installatie kopieën.

In deze end-to-end zelf studie wordt uitgelegd hoe u Azure IoT Edge op Ubuntu Server 18,04 x64 bijwerkt met behulp van de update pakket agent van het apparaat. Hoewel in de zelf studie wordt gedemonstreerd hoe u de update IoT Edge, met soort gelijke stappen die u kunt gebruiken om andere pakketten bij te werken zoals de container engine die wordt gebruikt.

De hulpprogram ma's en concepten in deze zelf studie zijn nog steeds van toepassing, zelfs als u van plan bent een andere besturingssysteem platform configuratie te gebruiken. Voltooi deze inleiding tot een end-to-end update proces en kies vervolgens uw voorkeurs formulier voor het bijwerken en het besturings systeem om de details te bekijken.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * De Update Agent en de bijbehorende afhankelijkheden van het apparaat downloaden en installeren
> * Een tag toevoegen aan uw apparaat
> * Een update importeren
> * Een apparaatgroep maken
> * Een pakket update implementeren
> * De update-implementatie controleren

## <a name="prerequisites"></a>Vereisten

* Als u dit nog niet hebt gedaan, maakt u een [Update account en-exemplaar](create-device-update-account.md)voor het apparaat, met inbegrip van het configureren van een IOT hub.
* De [Connection String voor een IOT edge apparaat](../iot-edge/how-to-register-device.md?view=iotedge-2020-11&preserve-view=true#view-registered-devices-and-retrieve-connection-strings).

## <a name="prepare-a-device"></a>Een apparaat voorbereiden
### <a name="using-the-automated-deploy-to-azure-button"></a>De knop automatisch implementeren naar Azure gebruiken

Voor het gemak maakt deze zelf studie gebruik van een [Azure Resource Manager sjabloon](../azure-resource-manager/templates/overview.md) die is gebaseerd op [Cloud-init](../virtual-machines/linux/using-cloud-init.md), waarmee u snel een virtuele machine met Ubuntu 18,04 LTS kunt instellen. De service installeert zowel de Azure IoT Edge runtime als de update pakket agent van het apparaat en configureert vervolgens automatisch het apparaat met inrichtings informatie met behulp van het apparaat connection string voor een IoT Edge apparaat (vereiste) dat u opgeeft. Zo voor komt u dat er een SSH-sessie moet worden gestart om de installatie te volt ooien.

1. Klik op de onderstaande knop om te beginnen:

   [![De knop Implementeren naar Azure voor iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fdevice-update-tutorial%2FedgeDeploy.json)

1. Vul in het venster Nieuw gestart de beschik bare formulier velden in:

    > [!div class="mx-imgBorder"]
    > [![Schermopname van de sjabloon iotedge-vm-deploy](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Abonnement**: het actieve Azure-abonnement voor het implementeren van de virtuele machine in.

    **Resource groep**: een bestaande of nieuwe resource groep die de virtuele machine en de bijbehorende resources bevat.

    **DNS-label voorvoegsel**: een vereiste waarde van uw keuze voor het voor voegsel van de hostnaam van de virtuele machine.

    **Gebruikers naam beheerder**: een gebruikers naam, die machtigingen voor het hoofd niveau van de implementatie krijgt.

    **Verbindings reeks voor apparaat**: een [apparaat Connection String](../iot-edge/how-to-register-device.md) voor een apparaat dat is gemaakt in uw beoogde [IOT hub](../iot-hub/about-iot-hub.md).

    **VM-grootte**: de [grootte](../cloud-services/cloud-services-sizes-specs.md) van de virtuele machine die moet worden geïmplementeerd

    **Versie van Ubuntu-besturings systeem**: de versie van het Ubuntu-besturings systeem dat op de virtuele basis machine moet worden geïnstalleerd. Laat de standaard waarde ongewijzigd, omdat deze wordt ingesteld op Ubuntu 18,04-LTS al.

    **Locatie**: de [geografische regio](https://azure.microsoft.com/global-infrastructure/locations/) waarin de virtuele machine moet worden geïmplementeerd. deze waarde wordt standaard ingesteld op de locatie van de geselecteerde resource groep.

    **Verificatie type**: Kies **sshPublicKey** of **wacht woord** , afhankelijk van uw voor keur.

    **Beheerders wachtwoord of-sleutel**: de waarde van de open bare SSH-sleutel of de waarde van het wacht woord, afhankelijk van de gekozen verificatie type.

    Wanneer alle velden zijn ingevuld, schakelt u het selectie vakje onder aan de pagina in om de voor waarden te accepteren en selecteert u **kopen** om de implementatie te starten.

1. Controleer of de implementatie correct is voltooid. Wacht enkele minuten nadat de implementatie is voltooid voor de installatie van IoT Edge en de Update Agent van het pakket.

   Er moet een virtuele-machineresource zijn geïmplementeerd in de geselecteerde resourcegroep.  Noteer de naam van de computer. dit moet de volgende indeling hebben `vm-0000000000000` . Noteer ook de bijbehorende **DNS-naam**, die de indeling `<dnsLabelPrefix>``<location>`.cloudapp.azure.com moet hebben.

    De **DNS-naam** kan worden verkregen in het gedeelte **Overzicht** van de zojuist geïmplementeerde virtuele machine in Azure Portal.

    > [!div class="mx-imgBorder"]
    > [![Scherm opname van de DNS-naam van de iotedge-VM](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

   > [!TIP]
   > Als u na de installatie SSH wilt gebruiken in deze VM, gebruikt u de bijbehorende **DNS-naam** met de opdracht: `ssh <adminUsername>@<DNS_Name>`

### <a name="optional-manually-prepare-a-device"></a>Beschrijving Een apparaat hand matig voorbereiden
De volgende hand matige stappen voor het installeren en configureren van het apparaat zijn gelijk aan die van dit [script voor Cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/1.2.0-rc4/cloud-init.txt). Ze kunnen worden gebruikt om een fysiek apparaat voor te bereiden.

1. Volg de instructies voor [het installeren van de Azure IOT Edge runtime](../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true).
   > [!NOTE]
   > De update pakket agent van het apparaat is niet afhankelijk van IoT Edge. Maar dit is afhankelijk van de IoT Identity service-daemon die is geïnstalleerd met IoT Edge (1.2.0 en hoger) om een identiteit te verkrijgen en verbinding te maken met IoT Hub.
   >
   > Hoewel deze zelf studie niet wordt behandeld, [kan de IOT Identity service-daemon zelfstandig op op Linux gebaseerde IOT-apparaten worden geïnstalleerd](https://azure.github.io/iot-identity-service/packaging.html). De reeks installatie kwesties. De update pakket agent voor het apparaat moet worden geïnstalleerd _na_ de IOT-identiteits service. Anders wordt de pakket agent niet geregistreerd als een geautoriseerd onderdeel om verbinding te maken met IoT Hub.

1. Installeer vervolgens de Update Agent voor het apparaat. deb-pakketten.

   ```bash
   sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt 
   ```

Voor het bijwerken van apparaten voor Azure IoT Hub-software pakketten gelden de volgende licentie voorwaarden:
  * [Update van het apparaat voor IoT Hub licentie](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
  * [Delivery Optimization-client licentie](https://github.com/microsoft/do-client/blob/main/LICENSE)

Lees de licentie voorwaarden voordat u een pakket gebruikt. Uw installatie en het gebruik van een pakket zijn uw acceptatie van deze voor waarden. Als u niet akkoord gaat met de licentie voorwaarden, mag u dat pakket niet gebruiken.

## <a name="add-a-tag-to-your-device"></a>Een tag toevoegen aan uw apparaat

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en navigeer naar de IOT hub.

2. Zoek in het navigatie deel venster van IoT Edge in het linkernavigatievenster het IoT Edge-apparaat op en navigeer naar het dubbele apparaat.

3. Verwijder in het dubbele apparaat alle bestaande waarde voor het bijwerken van het apparaat door ze in te stellen op null.

4. Voeg een nieuwe waarde voor het update label van het apparaat toe, zoals hieronder wordt weer gegeven.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            },
```

## <a name="import-update"></a>Update importeren

1. Ga in github naar updates voor het [apparaat](https://github.com/Azure/iot-hub-device-update/releases) en klik op de vervolg keuzelijst activa.

3. Down load de `Edge.package.update.samples.zip` door erop te klikken.

5. Pak de inhoud van de map uit om een update voorbeeld en de bijbehorende import manifesten te detecteren. 

2. Selecteer in Azure Portal de optie apparaat bijwerken onder Automatische Apparaatbeheer in de navigatie balk aan de linkerkant van uw IoT Hub.

3. Selecteer het tabblad updates.

4. Selecteer + nieuwe update importeren.

5. Selecteer het mappictogram of het tekstvak onder ' Selecteer een manifest bestand voor importeren '. U ziet een dialoog venster voor het kiezen van een bestand. Selecteer het `sample-1.0.1-aziot-edge-importManifest.json` import manifest in de map die u eerder hebt gedownload. Selecteer vervolgens het mappictogram of het tekstvak onder ' Selecteer een of meer update bestanden '. U ziet een dialoog venster voor het kiezen van een bestand. Selecteer het `sample-1.0.1-aziot-edge-apt-manifest.json` apt-manifest update bestand in de map die u eerder hebt gedownload.
Met deze update worden de `aziot-identity-service` en de `aziot-edge` pakketten bijgewerkt naar versie 1.2.0 ~ RC4-1 op het apparaat.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Scherm opname van de selectie van update bestanden." lightbox="media/import-update/select-update-files.png":::

6. Selecteer het mappictogram of tekstvak onder "een opslag container selecteren". Selecteer vervolgens het gewenste opslag account.

7. Als u al een container hebt gemaakt, kunt u deze opnieuw gebruiken. (Selecteer anders "+ container" om een nieuwe opslag container voor updates te maken.).  Selecteer de container die u wilt gebruiken en klik op selecteren.

   :::image type="content" source="media/import-update/container.png" alt-text="Scherm opname van container selectie." lightbox="media/import-update/container.png":::

8. Selecteer verzenden om het import proces te starten.

9. Het import proces wordt gestart en het scherm wordt gewijzigd in de sectie import geschiedenis. Selecteer vernieuwen om de voortgang weer te geven totdat het import proces is voltooid. Afhankelijk van de grootte van de update, kan dit binnen enkele minuten worden voltooid, maar dit kan langer duren.

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Scherm opname met update-import volgorde." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Wanneer de status kolom aangeeft dat het importeren is geslaagd, selecteert u de header gereed om te implementeren. U ziet nu de geïmporteerde update in de lijst.

[Meer informatie](import-update.md) over het importeren van updates.

## <a name="create-update-group"></a>Update groep maken

1. Ga naar de IoT Hub die u eerder hebt verbonden met het update-exemplaar van uw apparaat.

1. Selecteer de optie apparaat bijwerken onder Automatische Apparaatbeheer in de navigatie balk aan de linkerkant.

1. Selecteer het tabblad groepen boven aan de pagina.

1. Selecteer de knop toevoegen om een nieuwe groep te maken.

1. Selecteer in de lijst het IoT Hub label dat u in de vorige stap hebt gemaakt. Selecteer update groep maken.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Scherm opname van label selectie." lightbox="media/create-update-group/select-tag.PNG":::

[Meer informatie](create-update-group.md) over het toevoegen van tags en het maken van update groepen

## <a name="deploy-update"></a>Update implementeren

1. Zodra de groep is gemaakt, ziet u een nieuwe update die beschikbaar is voor uw apparaatgroep, met een koppeling naar de update in de kolom _beschik bare updates_ . Mogelijk moet u één keer vernieuwen.

1. Klik op de koppeling naar de beschik bare update.

1. Bevestig dat de juiste groep is geselecteerd als de doel groep en plan uw implementatie

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Update selecteren" lightbox="media/deploy-update/select-update.png":::

   > [!TIP]
   > Standaard is de begin datum/-tijd 24 uur vanaf uw huidige tijd. Zorg ervoor dat u een andere datum/tijd selecteert als u wilt dat de implementatie eerder begint.

1. Selecteer update implementeren.

1. De compliance-grafiek weer geven. U ziet dat de update nu wordt uitgevoerd. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="De update wordt uitgevoerd" lightbox="media/deploy-update/update-in-progress.png":::

1. Nadat het apparaat is bijgewerkt, ziet u dat uw nalevings diagram en de update van de implementatie gegevens overeenkomen. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Update geslaagd" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Een update-implementatie bewaken

1. Selecteer het tabblad implementaties boven aan de pagina.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Tabblad implementaties" lightbox="media/deploy-update/deployments-tab.png":::

1. Selecteer de implementatie die u hebt gemaakt om de details van de implementatie weer te geven.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Implementatiedetails" lightbox="media/deploy-update/deployment-details.png":::

1. Selecteer vernieuwen om de meest recente status gegevens weer te geven. Herhaal dit proces totdat de status is gewijzigd in geslaagd.

U hebt nu een voltooide end-to-end pakket update voltooid met update voor het bijwerken van het apparaat voor IoT Hub op een Ubuntu Server 18,04 x64-apparaat. 

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, moet u uw update account voor het apparaat, het exemplaar, de IoT Hub en het IoT Edge apparaat opschonen (als u de virtuele machine hebt gemaakt via de knop implementeren in Azure). U kunt dit doen door naar elke afzonderlijke resource te gaan en verwijderen te selecteren. Houd er rekening mee dat u een update-exemplaar voor het apparaat moet opschonen voordat u het update account voor het apparaat opschoont.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Image update op Raspberry Pi 3 B + zelf studie](device-update-raspberry-pi.md)
