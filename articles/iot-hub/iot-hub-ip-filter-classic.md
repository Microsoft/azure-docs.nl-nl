---
title: IoT Hub klassieke IP-filter van Azure (afgeschaft) | Microsoft Docs
description: Een upgrade uitvoeren van een klassiek IP-filter en wat zijn de voor delen
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jlian
ms.openlocfilehash: 70cea7a388c07bee9caa2e25e4061a3d3bb2b460
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633901"
---
# <a name="iot-hub-classic-ip-filter-and-how-to-upgrade"></a>IoT Hub klassiek IP-filter en hoe een upgrade 

Het bijgewerkte IP-filter voor IoT Hub beveiligt het ingebouwde eind punt en is standaard beveiligd. We streven ernaar nooit wijzigingen door te voeren, het verbeterde beveiligings model van het bijgewerkte IP-filter is incompatibel met het klassieke IP-filter, waardoor we zijn buiten gebruik stellen. Zie [what's New](#whats-new) en [IOT hub IP filters](iot-hub-ip-filtering.md)voor meer informatie over het nieuwe bijgewerkte IP-filter.

Om te voor komen dat de service wordt onderbroken, moet u de begeleide upgrade uitvoeren vóór de deadline voor de migratie, op dat moment dat de upgrade automatisch wordt uitgevoerd. Zie [Azure update](https://aka.ms/ipfilterv2azupdate)voor meer informatie over de migratie tijdlijn.

## <a name="how-to-upgrade"></a>Een upgrade uitvoeren

1.  Ga naar Azure Portal
2.  Ga naar uw IoT-hub.
3.  Selecteer **netwerken** in het menu aan de linkerkant.
4.  Er wordt een banner weer gegeven waarin u wordt gevraagd uw IP-filter bij te werken naar het nieuwe model. Selecteer **Ja** ter bevestiging.
    :::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-upgrade-banner.png" alt-text="Afbeelding van de banner prompt voor de upgrade van het klassieke IP-filter":::
5.  Omdat het nieuwe IP-filter standaard alle IP-adressen blokkeert, verwijdert de upgrade uw afzonderlijke regels voor weigeren, maar krijgt u de mogelijkheid om deze te bekijken voordat u opslaat. Controleer de regels aandachtig om er zeker van te zijn dat ze voor u werken.
6.  Volg de aanwijzingen om de upgrade te volt ooien.

## <a name="whats-new"></a>Nieuwe functies

### <a name="secure-by-default"></a>Standaardbeveiliging

Met het klassieke IP-filter kunnen alle IP-adressen standaard verbinding maken met de IoT Hub, die niet goed wordt uitgelijnd met de meest voorkomende scenario's voor netwerk beveiliging. Normaal gesp roken wilt u alleen vertrouwde IP-adressen kunnen verbinding maken met uw IoT-hub en alle andere gegevens weigeren. Om dit doel te verzorgen met het klassieke IP-filter, is het een proces dat uit meerdere stappen bestaat. Als u bijvoorbeeld alleen verkeer van wilt accepteren `192.168.100.0/22` , moet u

1. Configureer één regel voor *toestaan* voor `192.168.100.0/22` .
1. Configureer een andere *blokkerings* regel voor `0.0.0.0/0` (de regel ' alles blok keren ')
1. Zorg ervoor dat de regels correct zijn gerangschikt, waarbij de regel toestaan boven de blokkerings regel is geordend.

In de praktijk zorgt dit proces voor meerdere stappen voor Verwar ring. Gebruikers hebben de regel ' alles blok keren ' niet geconfigureerd of de regels zijn niet correct geordend, wat leidt tot onbedoelde bloot stelling. 

Met het nieuwe IP-filter worden standaard alle IP-adressen geblokkeerd. Alleen de IP-bereiken die u expliciet toevoegt, kunnen verbinding maken met IoT Hub. In het bovenstaande voor beeld zijn stap 2 en 3 niet meer nodig. Dit nieuwe gedrag vereenvoudigt configuratie en houdt zich aan door het [standaard principe veilig](https://wikipedia.org/wiki/Secure_by_default).

### <a name="protect-the-built-in-event-hub-compatible-endpoint"></a>Het ingebouwde Event hub-compatibele eind punt beveiligen

Klassiek IP-filter kan niet worden toegepast op het ingebouwde eind punt. Deze beperking houdt in dat, gebeurtenis met het blok keren van alle regels (blok keren `0.0.0.0/0` ), het ingebouwde eind punt nog steeds toegankelijk is vanaf elk IP-adres.

Het nieuwe IP-filter biedt een optie om regels toe te passen op het ingebouwde eind punt, waardoor Risico's voor netwerk beveiliging worden verminderd.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-built-in-endpoint.png" alt-text="Afbeelding van de wissel knop die moet worden toegepast op het ingebouwde eind punt":::

> [!NOTE]
> Deze optie is niet beschikbaar voor gratis (F1) IoT hubs. Als u IP-filter regels wilt Toep assen op het ingebouwde eind punt, gebruikt u een betaalde IoT-hub.

### <a name="api-impact"></a>API-impact

Het bijgewerkte IP-filter is beschikbaar in IoT Hub resource-API van `2020-08-31` ( `2020-08-31-preview` en) en vervolgens. Het klassieke IP-filter is nog steeds beschikbaar in alle API-versies, maar wordt verwijderd in een toekomstige API-versie bij de deadline voor de migratie. Zie [Azure update](https://aka.ms/ipfilterv2azupdate)voor meer informatie over de migratie tijdlijn.

## <a name="tip-try-the-changes-before-they-apply"></a>Tip: Probeer de wijzigingen voordat ze van toepassing zijn

Omdat het nieuwe IP-filter standaard alle IP-adressen blokkeert, zijn afzonderlijke blok regels niet meer compatibel. Het begeleide upgrade proces verwijdert daarom deze afzonderlijke blokkerings regels. 

Als u wilt proberen de wijziging in met het klassieke IP-filter:

1. Ga naar het tabblad **netwerken** in uw IOT-hub
1. Noteer de bestaande IP-filter configuratie (klassiek), voor het geval u wilt terugdraaien
1. Selecteer het prullenbak pictogram naast regels with **Block** om ze te verwijderen
1. Voeg onderaan een andere regel toe `0.0.0.0/0` en kies **blok keren**
1. Selecteer **Opslaan**.

Deze configuratie simuleert hoe het nieuwe IP-filter zich gedraagt nadat de upgrade van klassiek is uitgevoerd. Een uitzonde ring is de ingebouwde Endpoint Protection. Dit is niet mogelijk om het klassieke IP-filter te gebruiken. Deze functie is echter optioneel, dus u hoeft deze niet te gebruiken als u denkt dat deze iets kan verstoren.

## <a name="tip-check-diagnostic-logs-for-all-ip-connections-to-your-iot-hub"></a>Tip: Raadpleeg de diagnostische logboeken voor alle IP-verbindingen met uw IoT-hub

Controleer uw Diagnostische logboeken onder de categorie verbindingen om te zorgen voor een soepele overgang. Zoek naar de `maskedIpAddress` eigenschap om te zien of de bereiken naar verwachting zijn. Denk eraan: met het nieuwe IP-filter worden alle IP-adressen geblokkeerd die niet expliciet zijn toegevoegd.

## <a name="iot-hub-classic-ip-filter-documentation-retired"></a>IoT Hub klassieke IP-filter documentatie (buiten gebruik gesteld)

> [!IMPORTANT]
> Hieronder vindt u de oorspronkelijke documentatie voor het klassieke IP-filter. dit wordt buiten gebruik gesteld.

Beveiliging is een belang rijk aspect van elke IoT-oplossing op basis van Azure IoT Hub. Soms moet u expliciet de IP-adressen opgeven waarvan apparaten verbinding kunnen maken als onderdeel van uw beveiligingsconfiguratie. Met de *IP-filter* functie kunt u regels configureren voor het afwijzen of accepteren van verkeer van specifieke IPv4-adressen.

### <a name="when-to-use"></a>Wanneer gebruikt u dit?

Er zijn twee specifieke use-cases wanneer het handig is om de IoT Hub-eind punten voor bepaalde IP-adressen te blok keren:

* Uw IoT-hub mag alleen verkeer ontvangen van een opgegeven bereik van IP-adressen en alle andere gegevens weigeren. U gebruikt bijvoorbeeld uw IoT-hub met [Azure Express route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) om particuliere verbindingen te maken tussen een IOT-hub en uw on-premises infra structuur.

* U moet het verkeer afwijzen van IP-adressen die zijn geïdentificeerd als verdacht door de IoT hub-beheerder.

### <a name="how-filter-rules-are-applied"></a>Hoe filterregels worden toegepast

De IP-filter regels worden toegepast op het IoT Hub service niveau. Daarom zijn de IP-filter regels van toepassing op alle verbindingen van apparaten en back-end-apps met behulp van elk ondersteund protocol. Clients die rechtstreeks vanuit het [ingebouwde Event hub-compatibele eind punt](iot-hub-devguide-messages-read-builtin.md) lezen (niet via de IOT hub Connection String), zijn echter niet gebonden aan de IP-filter regels. 

Elke verbindings poging van een IP-adres dat overeenkomt met een afwijzings-IP-regel in uw IoT-hub ontvangt een niet-geautoriseerde 401-status code en een beschrijving. De IP-regel wordt niet vermeld in het antwoordbericht. Het afwijzen van IP-adressen kan ertoe leiden dat andere Azure-Services, zoals Azure Stream Analytics, Azure Virtual Machines of het Device Explorer in Azure Portal, niet werken met de IoT-hub.

> [!NOTE]
> Als u Azure Stream Analytics (ASA) moet gebruiken om berichten te lezen van een IoT hub waarvoor IP-filter is ingeschakeld, gebruikt u de Event Hub compatibele naam en het eind punt van uw IoT-hub om hand matig een [Event hubs stream-invoer](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) toe te voegen aan de ASA.

### <a name="default-setting"></a>Standaardinstelling

Het **IP-filter** raster in de portal voor een IOT-hub is standaard leeg. Deze standaard instelling betekent dat uw hub verbindingen van elk IP-adres accepteert. Deze standaardinstelling komt overeen met een regel die het IP-adresbereik 0.0.0.0/0 accepteert.

Als u de pagina IP-filter instellingen wilt weer geven, selecteert u **netwerken**, **open bare toegang** en kiest u **geselecteerde IP-bereiken**:

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-default.png" alt-text="IoT Hub standaard instellingen voor IP-filter":::

### <a name="add-or-edit-an-ip-filter-rule"></a>Een IP-filterregel toevoegen of bewerken

Als u een IP-filterregel wilt toevoegen, selecteert u **+ IP-filterregel toevoegen**.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-add-rule.png" alt-text="Een IP-filter regel toevoegen aan een IoT-hub":::

Vul de velden in nadat u **IP-filterregel toevoegen** hebt geselecteerd.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-after-selecting-add.png" alt-text="Nadat u IP-filterregel toevoegen hebt geselecteerd":::

* Geef een **naam** op voor de IP-filterregel. Dit moet een unieke, niet-hoofdlettergevoelige tekenreeks van maximaal 128 tekens zijn. Alleen de ASCII 7-bits alfanumerieke tekens plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` worden geaccepteerd.

* Geef één IPv4-adres op of een blok met IP-adressen in CIDR-notatie. Bijvoorbeeld in CIDR-notatie staat 192.168.100.0/22 voor de 1024 IPv4-adressen van 192.168.100.0 tot en met 192.168.103.255.

* Selecteer **Toestaan** of **Blokkeren** als de **actie** voor de IP-filterregel.

Nadat u de velden hebt ingevuld, selecteert u **Opslaan** om de regel op te slaan. U ziet een waarschuwing dat de update wordt uitgevoerd.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-save-new-rule.png" alt-text="Melding over het opslaan van een IP-filterregel":::

De optie **Toevoegen** wordt uitgeschakeld wanneer u het maximum van 10 IP-filterregels bereikt.

Als u een bestaande regel wilt bewerken, selecteert u de gegevens die u wilt wijzigen, brengt u de wijziging aan en selecteert u **Opslaan** om uw bewerking op te slaan.

### <a name="delete-an-ip-filter-rule"></a>Een IP-filterregel verwijderen

Als u een IP-filterregel wilt verwijderen, selecteert u het prullenbakpictogram op die rij en selecteert u **Opslaan**. De regel wordt verwijderd en de wijziging wordt opgeslagen.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-delete-rule.png" alt-text="Een IoT Hub IP-filter regel verwijderen":::

### <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>IP-filters ophalen en bijwerken met behulp van Azure CLI

De IP-filters van uw IoT Hub kunnen worden opgehaald en bijgewerkt via [Azure cli](https://docs.microsoft.com/cli/azure/).

Als u de huidige IP-filters van uw IoT Hub wilt ophalen, voert u de volgende handelingen uit:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Hiermee wordt een JSON-object geretourneerd waarin uw bestaande IP-filters worden weer gegeven onder de `properties.ipFilterRules` sleutel:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Als u een nieuw IP-filter voor uw IoT Hub wilt toevoegen, voert u de volgende handelingen uit:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Als u een bestaand IP-filter in uw IoT Hub wilt verwijderen, voert u de volgende handelingen uit:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Houd er rekening mee dat `<ipFilterIndexToRemove>` moet overeenkomen met de volg orde van IP-filters in de IOT hub `properties.ipFilterRules` .

### <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>IP-filters ophalen en bijwerken met behulp van Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De IP-filters van uw IoT Hub kunnen worden opgehaald en ingesteld via [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

### <a name="update-ip-filter-rules-using-rest"></a>IP-filter regels bijwerken met REST

U kunt ook het IP-filter van uw IoT Hub ophalen en wijzigen met behulp van het REST-eind punt van de Azure-resource provider. Zie `properties.ipFilterRules` in de [createorupdate-methode](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

### <a name="ip-filter-rule-evaluation"></a>Evaluatie van IP-filterregel

IP-filterregels worden op volgorde toegepast en de eerste regel die overeenkomt met het IP-adres bepaalt de actie accepteren of afwijzen.

Als u bijvoorbeeld adressen in het bereik 192.168.100.0/22 wilt accepteren en de rest wilt afwijzen, moet de eerste regel in het raster het adresbereik 192.168.100.0/22 accepteren. De volgende regel moet alle adressen afwijzen met behulp van het bereik 0.0.0.0/0.

U kunt de volgorde van de IP-filterregels in het raster wijzigen door te klikken op de drie verticale puntjes aan het begin van een rij en met slepen en neerzetten.

Als u de nieuwe IP-filterregel wilt opslaan, klikt u op **Opslaan**.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-rule-order.png" alt-text="De volg orde van de IoT Hub IP-filter regels wijzigen":::

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [IP-filters gebruiken](iot-hub-ip-filtering.md)