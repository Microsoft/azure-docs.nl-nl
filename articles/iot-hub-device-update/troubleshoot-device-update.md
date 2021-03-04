---
title: Veelvoorkomende problemen met updates voor Azure IoT Hub oplossen | Microsoft Docs
description: In dit document vindt u een lijst met tips en trucs voor het oplossen van veel mogelijke problemen die u mogelijk ondervindt met het bijwerken van het apparaat voor IoT Hub.
author: lichris
ms.author: lichris
ms.date: 2/17/2021
ms.topic: troubleshooting
ms.service: iot-hub-device-update
ms.openlocfilehash: 3c1f60b214397b1f97e0157b5beca32d504102d6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030627"
---
# <a name="device-update-for-iot-hub-troubleshooting-guide"></a>Gids voor het oplossen van problemen met updates voor IoT Hub

In dit document vindt u enkele veelgestelde vragen en problemen die gebruikers van het apparaat bijwerken hebben gerapporteerd. Wanneer het bijwerken van het apparaat wordt uitgevoerd via een open bare preview, wordt deze probleemoplossings handleiding regel matig bijgewerkt met nieuwe vragen en oplossingen. Als u een probleem ondervindt dat niet wordt weer gegeven in deze hand leiding, raadpleegt u de sectie [contact opnemen met Microsoft ondersteuning](#contact) om uw situatie te documenteren.

## <a name="importing-updates"></a><a name="import"></a>Updates importeren

### <a name="q-im-having-trouble-connecting-my-device-update-instance-to-my-iot-hub-instance"></a>V: Ik ondervind problemen bij het verbinden van mijn update-exemplaar van apparaat aan mijn IoT Hub-exemplaar.
_Zorg ervoor dat de route ring van de IoT Hub-berichten juist is geconfigureerd, conform de documentatie over het bijwerken van het [apparaat](./device-update-resources.md) ._

### <a name="q-im-encountering-a-role-related-error-error-message-in-azure-portal-or-a-403-api-error"></a>V: er is een fout opgetreden met betrekking tot een rol (fout bericht in Azure Portal of een 403 API-fout).
_Mogelijk hebt u geen juiste toegangs machtigingen geconfigureerd. Zorg ervoor dat u de juiste toegangs machtigingen hebt geconfigureerd volgens de documentatie voor [toegangs beheer](./device-update-control-access.md) voor het apparaat._

### <a name="q-im-encountering-a-500-type-error-when-importing-content-to-the-device-update-service"></a>V: er treedt een 500-fout op bij het importeren van inhoud naar de update service voor het apparaat.
_Een fout code in het 500-bereik kan duiden op een probleem met de service voor het bijwerken van het apparaat. Wacht 5 minuten en probeer het opnieuw. Als dezelfde fout zich blijft voordoen, volgt u de instructies in de sectie [contact opnemen met Microsoft ondersteuning](#contact) om een ondersteunings aanvraag bij micro soft te kunnen indienen._

### <a name="q-im-encountering-an-error-code-when-importing-content-and-would-like-to-parse-it"></a>V: Ik heb een fout code aangetroffen bij het importeren van inhoud en wil deze parseren.
_Raadpleeg de documentatie over [fout codes](./device-update-error-codes.md) voor het bijwerken van het apparaat voor meer informatie over het parseren van fout codes._

## <a name="device-failures"></a><a name="device-failure"></a>Storingen in apparaten

### <a name="q-how-can-i-ensure-my-device-is-connected-to-device-update-for-iot-hub"></a>V: hoe kan ik ervoor zorgen dat mijn apparaat is verbonden met een update van het apparaat voor IoT Hub?
_U kunt controleren of het apparaat is verbonden met het bijwerken van het apparaat door te controleren of het wordt weer gegeven in de sectie ' niet-gegroepeerde ' apparaten in de weer gave naleving van Azure Portal._

### <a name="q-one-or-more-of-my-devices-is-failing-to-update"></a>V: een of meer van mijn apparaten kunnen niet worden bijgewerkt.
_Er zijn veel mogelijke hoofd oorzaken voor het bijwerken van het apparaat. Controleer of het apparaat: 1) is verbonden met uw IoT Hub exemplaar, 2) is verbonden met het update-exemplaar van uw apparaat en 3) de Delivery Optimization-Service (DO) wordt uitgevoerd. Als alle drie van toepassing zijn op uw apparaat, volgt u de instructies in de sectie [contact opnemen met Microsoft ondersteuning](#contact) om een ondersteunings aanvraag bij micro soft te doen._

## <a name="deploying-an-update"></a><a name="deploy"></a> Een update implementeren

### <a name="q-ive-deployed-an-update-to-my-devices-but-the-compliance-status-says-it-isnt-on-the-latest-update-what-should-i-do"></a>V: Ik heb een update geïmplementeerd op mijn apparaat (en), maar de nalevings status geeft aan dat deze niet is geïnstalleerd op de laatste update. Wat moet ik doen?
_Het kan tot vijf minuten duren om de nalevings status van het apparaat te vernieuwen. Wacht een ogen blik en probeer het vervolgens opnieuw._
### <a name="q-my-devices-deployment-status-shows-incompatible-what-should-i-do"></a>V: de implementatie status van mijn apparaat geeft incompatibel, wat moet ik doen?
_De fabrikant en model eigenschappen van een doel apparaat zijn mogelijk gewijzigd nadat het apparaat is verbonden met IoT Hub, waardoor het apparaat nu als incompatibel wordt beschouwd met de update-inhoud van de huidige implementatie._

_Controleer de [Adu core-interface](./device-update-plug-and-play.md) om te zien welke fabrikant en welk model uw apparaat rapporteert aan de Device Update-service en zorg ervoor dat het overeenkomt met de fabrikant en het model dat u hebt opgegeven in het [import manifest](./import-concepts.md) van de update-inhoud die wordt geïmplementeerd. U kunt deze eigenschappen voor een bepaald apparaat wijzigen met behulp van het [configuratie bestand](./device-update-configuration-file.md)voor het bijwerken van het apparaat._

### <a name="q-i-see-my-deployment-is-in-active-stage-but-none-of-my-devices-are-in-progress-with-the-update-what-should-i-do"></a>V: Ik zie mijn implementatie in de fase actief, maar geen van mijn apparaten wordt uitgevoerd met de update. Wat moet ik doen?
_Zorg ervoor dat de start datum van de implementatie niet in de toekomst is ingesteld. Wanneer u een nieuwe implementatie maakt, wordt de begin datum van de implementatie standaard ingesteld op de volgende dag als beveiliging, tenzij u deze expliciet wijzigt. U kunt wachten tot de start datum van de implementatie is bereikt, of de doorlopende implementatie annuleren en een nieuwe implementatie met de gewenste begin datum maken._

### <a name="q-im-trying-to-group-my-devices-but-i-dont-see-the-tag-in-the-drop-down-when-creating-a-group"></a>V: ik probeer mijn apparaten te groeperen, maar de tag wordt niet weer geven in de vervolg keuzelijst bij het maken van een groep.
_Zorg ervoor dat u de bericht routes op de juiste wijze hebt geconfigureerd in uw IoT Hub volgens de documentatie over het bijwerken van het [apparaat](./device-update-resources.md) . U moet uw apparaat opnieuw labelen nadat u de route hebt geconfigureerd._

_Een andere hoofd oorzaak is mogelijk dat u de tag hebt toegepast voordat u uw apparaat verbindt met het apparaat bijwerken voor IoT Hub. Zorg ervoor dat het apparaat al is verbonden met het bijwerken van het apparaat. U kunt controleren of het apparaat is verbonden met het bijwerken van het apparaat voor IoT Hub door te controleren of het wordt weer gegeven onder ' niet-gegroepeerde ' apparaten in de weer gave naleving. Voeg tijdelijk een tag van een andere waarde toe en voeg vervolgens het gewenste label opnieuw toe zodra het apparaat is verbonden._

_Als u gebruikmaakt van de Device Provisioning Service (DPS), moet u ervoor zorgen dat u uw apparaten labelt nadat ze zijn ingericht en niet tijdens het proces voor het maken van het apparaat. Als u uw apparaat al hebt gelabeld tijdens de stap voor het maken van het apparaat, moet u uw apparaat tijdelijk labelen met een andere waarde nadat het is ingericht en vervolgens de gewenste tag opnieuw toevoegen._

### <a name="q-my-deployment-completed-successfully-but-some-devices-failed-to-update"></a>V: mijn implementatie is voltooid, maar sommige apparaten zijn niet bijgewerkt.
_Dit kan zijn veroorzaakt door een fout aan de client zijde van de apparaten die zijn mislukt. Raadpleeg het gedeelte apparaten met een fout in deze hand leiding voor probleem oplossing._

### <a name="q-i-encountered-an-error-in-the-ux-when-trying-to-initiate-a-deployment"></a>V: er is een fout opgetreden in de UX bij het initiëren van een implementatie.
_Dit kan zijn veroorzaakt door een service/UX-fout of door een probleem met de API-machtigingen. Volg de instructies in de sectie [contact opnemen met Microsoft ondersteuning](#contact) voor het indienen van een ondersteunings aanvraag bij micro soft._

### <a name="q-i-started-a-deployment-but-it-isnt-reaching-an-end-state"></a>V: Ik heb een implementatie gestart, maar het heeft geen eind status.
_Dit kan zijn veroorzaakt door een probleem met de service prestaties, een service fout of een client fout. Voer de implementatie na 10 minuten opnieuw uit. Als u hetzelfde probleem ondervindt, kunt u de logboeken van uw apparaten opsporen en verwijzen naar het gedeelte apparaat fouten in deze hand leiding voor probleem oplossing. Als hetzelfde probleem zich blijft voordoen, volgt u de instructies in de sectie [contact opnemen met Microsoft ondersteuning](#contact) om een ondersteunings aanvraag bij micro soft te verzenden._

## <a name="downloading-updates-onto-devices"></a><a name="download"></a> Updates downloaden op apparaten

### <a name="q-how-do-i-resume-a-download-when-a-device-has-reconnected-after-a-period-of-disconnection"></a>V: Hoe kan ik een down load hervatten wanneer een apparaat na een bepaalde periode opnieuw verbinding heeft gemaakt?
_De down load wordt automatisch hervat wanneer de connectiviteit binnen een periode van 24 uur wordt hersteld. Na 24 uur moet de down load opnieuw worden gestart door de gebruiker._
## <a name="using-microsoft-connected-cache-mcc"></a><a name="mcc"></a> Micro soft Connected cache (MCC) gebruiken

### <a name="q-i-am-encountering-an-issue-when-attempting-to-deploy-the-mcc-module-on-my-iot-edge-device"></a>V: er treedt een probleem op bij het implementeren van de MCC-module op mijn IoT Edge-apparaat.
_Raadpleeg de [documentatie van IOT Edge]() voor het implementeren van Edge-modules op IOT edge apparaten. U kunt controleren of de MCC-module correct wordt uitgevoerd op uw IoT Edge-apparaat door te navigeren naar http://localhost:5100/Summary ._
### <a name="q-one-of-my-iot-devices-is-attempting-to-download-an-update-through-mcc-but-is-failing"></a>V: een van mijn IoT-apparaten probeert een update te downloaden via MCC, maar is mislukt.
_Er zijn verschillende problemen die ertoe kunnen leiden dat een IoT-apparaat geen verbinding kan maken met MCC. Om het probleem vast te stellen, moet u de DO client-en nginx-logboeken verzamelen van het niet-werkende apparaat (Zie de sectie [contact opnemen met Microsoft ondersteuning](#contact) voor instructies over het verzamelen van client Logboeken)._

_Het is mogelijk dat uw apparaat geen inhoud kan ophalen van Internet om door te geven aan de MCC-module omdat de URL die wordt gebruikt, niet is toegestaan. Als u wilt bepalen of dit het geval is, moet u uw IoT Edge omgevings variabelen controleren in Azure Portal._
## <a name="contacting-microsoft-support"></a><a name="contact"></a> Contact opnemen met Microsoft Ondersteuning

Als u problemen ondervindt die niet kunnen worden opgelost met de bovenstaande Veelgestelde vragen, kunt u een ondersteunings aanvraag indienen met Microsoft Ondersteuning via de Azure Portal-interface. Afhankelijk van de categorie waartoe u het probleem aanmeldt, wordt u mogelijk gevraagd om aanvullende gegevens te verzamelen en te delen om Microsoft Ondersteuning te helpen bij het onderzoeken van uw probleem. 

Hieronder vindt u instructies voor het verzamelen van elk gegevens type. U kunt [getDevices]() gebruiken om te controleren op aanvullende informatie in het payload-antwoord van de API.

Daarnaast kunt u de volgende informatie gebruiken om de hoofd oorzaak van het probleem te beperken:
* Welk type apparaat u probeert bij te werken (Azure percept, IoT Edge gateway, overige)
* Welk type apparaat-client bijwerken u gebruikt (op installatie kopie gebaseerd, op pakket gebaseerd, Simulator)
* Welk besturings systeem op uw apparaat wordt uitgevoerd
* Details over de architectuur van uw apparaat
* Of u het apparaat bijwerken hebt gebruikt om een apparaat bij te werken

Als u een van de bovenstaande beschik bare gegevens hebt, neemt u deze op in uw beschrijving van het probleem.

### <a name="collecting-client-logs"></a>Client logboeken verzamelen

* Op het Raspberry Pi-apparaat zijn er twee sets logboeken gevonden:

    ```markdown
    /adu/logs
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* Voor de verpakte client zijn de logboeken hier te vinden:

    ```markdown
    /var/log/adu
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* Voor de Simulator vindt u de logboeken hier:

    ```markdown
    /tmp/aduc-logs
    ```

### <a name="error-codes"></a>Foutcodes
U wordt mogelijk gevraagd om fout codes op te geven bij het rapporteren van een probleem met betrekking tot het importeren van een update, het mislukken van een apparaat of het implementeren van een update.

Fout codes kunnen worden verkregen door te kijken naar de [ADUCoreInterface](./device-update-plug-and-play.md) -interface. Raadpleeg de documentatie over [fout codes](./device-update-error-codes.md) voor het bijwerken van het apparaat voor informatie over het parseren van fout codes voor zelf diagnose en probleem oplossing.

### <a name="trace-id"></a>Tracerings-ID
U wordt mogelijk gevraagd een tracerings-ID op te geven bij het rapporteren van een probleem met betrekking tot het importeren of implementeren van een update.

De tracerings-ID voor een bepaalde gebruikers actie kan worden gevonden in de API-reactie of in de sectie Geschiedenis importeren van de gebruikers interface van Azure Portal. 

Op dit moment zijn Tracer-Id's voor implementatie acties alleen toegankelijk via de API-reactie.

### <a name="deployment-id"></a>Implementatie-ID
U wordt mogelijk gevraagd een implementatie-ID op te geven bij het rapporteren van een probleem met betrekking tot de implementatie van een update.

De implementatie-ID wordt gemaakt door de gebruiker bij het aanroepen van de API om een implementatie te initiëren.

Op dit moment worden implementatie-Id's voor implementaties gestart vanuit de Azure Portal gebruikers interface automatisch gegenereerd en niet geoppereerd aan de gebruiker.

### <a name="iot-hub-instance-name"></a>Naam van IoT Hub-exemplaar
Mogelijk wordt u gevraagd om de naam van uw IoT Hub-exemplaar op te geven bij het rapporteren van een probleem met betrekking tot storingen in apparaten of het implementeren van een update.

De naam van de IoT Hub wordt door de gebruiker gekozen wanneer deze voor het eerst is ingericht.

### <a name="device-update-account-name"></a>Account naam voor het bijwerken van het apparaat
Mogelijk wordt u gevraagd om de naam van de update account van het apparaat op te geven bij het rapporteren van een probleem met betrekking tot het importeren van een update, het mislukken van apparaten of het implementeren van een update.

De naam van het update account van het apparaat wordt gekozen door de gebruiker wanneer u zich voor het eerst aanmeldt voor de service. Meer informatie vindt u in de documentatie over het [bijwerken van apparaten](./device-update-resources.md) .

### <a name="device-update-instance-name"></a>Naam van update-exemplaar van apparaat
Mogelijk wordt u gevraagd om de naam van het update-exemplaar van het apparaat op te geven bij het rapporteren van een probleem met betrekking tot het importeren van een update, het mislukken van apparaten of het implementeren van een update.

De naam van het apparaat-update-exemplaar wordt gekozen door de gebruiker wanneer deze voor het eerst is ingericht. Meer informatie vindt u in de documentatie over het [bijwerken van apparaten](./device-update-resources.md) .

### <a name="device-id"></a>Apparaat-ID
U wordt mogelijk gevraagd om een apparaat-ID op te geven bij het rapporteren van een probleem met betrekking tot storingen in apparaten of het implementeren van een update.

De apparaat-ID wordt door de klant gedefinieerd wanneer het apparaat voor het eerst wordt ingericht. Het kan ook worden opgehaald vanaf het apparaat van het apparaat.

### <a name="update-id"></a>Update-ID
U wordt mogelijk gevraagd een update-ID op te geven voor het melden van een probleem met betrekking tot de implementatie van een update.

De update-ID wordt door de klant gedefinieerd tijdens het initiëren van een implementatie.

### <a name="nginx-logs"></a>Nginx-logboeken
U wordt mogelijk gevraagd om nginx-logboeken te verstrekken bij het rapporteren van een probleem met betrekking tot micro soft Connected cache.

### <a name="adu-conftxt"></a>ADU-conf.txt
Mogelijk wordt u gevraagd het configuratie bestand voor het update-apparaat ("adu-conf.txt") op te geven bij het melden van een probleem met betrekking tot de implementatie van een update.

Het configuratie bestand is optioneel en wordt gemaakt door de gebruiker, gevolgd door de instructies in de documentatie over het bijwerken van het [apparaat](./device-update-configuration-file.md) .

### <a name="import-manifest"></a>Manifest importeren
Mogelijk wordt u gevraagd uw manifest bestand voor importeren op te geven bij het rapporteren van een probleem met betrekking tot het importeren of implementeren van een update.

Het import manifest is een bestand dat door de klant is gemaakt bij het importeren van update-inhoud naar de Device Update-service.

**[Volgende stap: meer informatie over fout codes bij het bijwerken van het apparaat](.\device-update-error-codes.md)**