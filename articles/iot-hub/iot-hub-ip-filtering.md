---
title: Azure IoT Hub IP-filters | Microsoft Docs
description: IP-filtering gebruiken om verbindingen met specifieke IP-adressen voor uw Azure IoT-hub toe te staan.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: jlian
ms.openlocfilehash: 6f83421d4ee56d56875e13ffbdd8ac9dbbf4b6bb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656360"
---
# <a name="use-ip-filters"></a>IP-filters gebruiken

Beveiliging is een belang rijk aspect van elke IoT-oplossing op basis van Azure IoT Hub. Soms moet u expliciet de IP-adressen opgeven waarvan apparaten verbinding kunnen maken als onderdeel van uw beveiligingsconfiguratie. Met de *IP-filter* functie kunt u regels configureren voor het afwijzen of accepteren van verkeer van specifieke IPv4-adressen.

## <a name="when-to-use"></a>Wanneer gebruikt u dit?

Gebruik IP-filter om alleen verkeer van een opgegeven IP-adres bereik te ontvangen en om alles te weigeren. U gebruikt bijvoorbeeld uw IoT-hub met [Azure Express route](../expressroute/expressroute-faqs.md#supported-services) om particuliere verbindingen te maken tussen een IOT-hub en uw on-premises infra structuur.

## <a name="default-setting"></a>Standaardinstelling

Als u de pagina IP-filter instellingen wilt weer geven, selecteert u **netwerken**, **open bare toegang** en kiest u **geselecteerde IP-bereiken**:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="IoT Hub standaard instellingen voor IP-filter":::

Het **IP-filter** raster in de portal voor een IOT-hub is standaard leeg. Deze standaard instelling betekent dat de hub verbindingen van alle IP-adressen blokkeert. Deze standaard instelling is gelijk aan een regel die het `0.0.0.0/0` IP-adres bereik blokkeert.

## <a name="add-or-edit-an-ip-filter-rule"></a>Een IP-filterregel toevoegen of bewerken

Als u een IP-filterregel wilt toevoegen, selecteert u **+ IP-filterregel toevoegen**.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Een IP-filter regel toevoegen aan een IoT-hub":::

Vul de velden in nadat u **IP-filterregel toevoegen** hebt geselecteerd.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Nadat u IP-filterregel toevoegen hebt geselecteerd":::

* Geef een **naam** op voor de IP-filterregel. Deze naam moet een unieke, hoofdletter gevoelige, alfanumerieke teken reeks van Maxi maal 128 tekens lang zijn. Alleen de ASCII 7-bits alfanumerieke tekens plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` worden geaccepteerd.

* Geef één IPv4-adres op of een blok met IP-adressen in CIDR-notatie. Bijvoorbeeld in CIDR-notatie staat 192.168.100.0/22 voor de 1024 IPv4-adressen van 192.168.100.0 tot en met 192.168.103.255.

Nadat u de velden hebt ingevuld, selecteert u **Opslaan** om de regel op te slaan. U ziet een waarschuwing dat de update wordt uitgevoerd.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Melding over het opslaan van een IP-filterregel":::

De optie **Toevoegen** wordt uitgeschakeld wanneer u het maximum van 10 IP-filterregels bereikt.

Als u een bestaande regel wilt bewerken, selecteert u de gegevens die u wilt wijzigen, brengt u de wijziging aan en selecteert u **Opslaan** om uw bewerking op te slaan.

## <a name="delete-an-ip-filter-rule"></a>Een IP-filterregel verwijderen

Als u een IP-filterregel wilt verwijderen, selecteert u het prullenbakpictogram op die rij en selecteert u **Opslaan**. De regel wordt verwijderd en de wijziging wordt opgeslagen.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Een IoT Hub IP-filter regel verwijderen":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>IP-filter regels Toep assen op het ingebouwde Event hub-compatibele eind punt

Als u de IP-filter regels wilt Toep assen op het ingebouwde Event hub-compatibele eind punt, schakelt u het selectie vakje naast **IP-filters toep assen op het ingebouwde eind punt** in en selecteert u **Opslaan**.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="Afbeelding van de wissel knop voor het ingebouwde eind punt en opslaan":::

> [!NOTE]
> Deze optie is niet beschikbaar voor gratis (F1) IoT hubs. Als u IP-filter regels wilt Toep assen op het ingebouwde eind punt, gebruikt u een betaalde IoT-hub.

Als u deze optie inschakelt, worden uw IP-filter regels gerepliceerd naar het ingebouwde eind punt, zodat alleen vertrouwde IP-bereiken toegang hebben.

Als u deze optie uitschakelt, is het ingebouwde eind punt toegankelijk voor alle IP-adressen. Dit gedrag kan nuttig zijn als u wilt lezen van het eind punt met Services met het wijzigen van IP-adressen als Azure Stream Analytics. 

## <a name="how-filter-rules-are-applied"></a>Hoe filterregels worden toegepast

De IP-filter regels worden toegepast op het IoT Hub service niveau. Daarom zijn de IP-filter regels van toepassing op alle verbindingen van apparaten en back-end-apps met behulp van elk ondersteund protocol. U kunt ook kiezen of het [ingebouwde Event hub-compatibele eind punt](iot-hub-devguide-messages-read-builtin.md) (niet via de IOT hub Connection String) aan deze regels is gebonden. 

Een verbindings poging van een IP-adres dat niet expliciet is toegestaan, ontvangt een niet-geautoriseerde 401-status code en een beschrijving. De IP-regel wordt niet vermeld in het antwoordbericht. Het afwijzen van IP-adressen kan ertoe leiden dat andere Azure-Services, zoals Azure Stream Analytics, Azure Virtual Machines of het Device Explorer in Azure Portal, niet werken met de IoT-hub.

> [!NOTE]
> Als u Azure Stream Analytics (ASA) moet gebruiken om berichten te lezen van een IoT hub waarvoor IP-filter is ingeschakeld, **schakelt** u het selectie vakje **IP-filters toep assen op de ingebouwde eindpunt** optie uit en gebruikt u vervolgens de Event hub-compatibele naam en het eind punt van uw IOT-hub om hand matig een [Event hubs stream-invoer](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) toe te voegen in de ASA.

### <a name="ordering"></a>Ordenen

IP-filter regels zijn regels voor *toestaan* en worden toegepast zonder te best Ellen. Alleen IP-adressen die u toevoegt, kunnen verbinding maken met IoT Hub. 

Als u bijvoorbeeld adressen in het bereik wilt accepteren `192.168.100.0/22` en alles wilt weigeren, hoeft u slechts één regel in het raster met een adres bereik toe te voegen `192.168.100.0/22` .

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>IP-filters ophalen en bijwerken met behulp van Azure CLI

De IP-filters van uw IoT Hub kunnen worden opgehaald en bijgewerkt via [Azure cli](/cli/azure/).

Als u de huidige IP-filters van uw IoT Hub wilt ophalen, voert u de volgende handelingen uit:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Hiermee wordt een JSON-object geretourneerd waarin uw bestaande IP-filters worden weer gegeven onder de `properties.networkRuleSets` sleutel:

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

Als u een nieuw IP-filter voor uw IoT Hub wilt toevoegen, voert u de volgende handelingen uit:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

Als u een bestaand IP-filter in uw IoT Hub wilt verwijderen, voert u de volgende handelingen uit:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

Hier `<ipFilterIndexToRemove>` moet overeenkomen met de volg orde van IP-filters in de IOT hub `properties.networkRuleSets.ipRules` .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>IP-filters ophalen en bijwerken met behulp van Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De IP-filters van uw IoT Hub kunnen worden opgehaald en ingesteld via [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>IP-filter regels bijwerken met REST


U kunt ook het IP-filter van uw IoT Hub ophalen en wijzigen met behulp van het REST-eind punt van de Azure-resource provider. Zie `properties.networkRuleSets` in de [createorupdate-methode](/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-classic-retirement"></a>IP-filter (klassiek) buiten gebruik gesteld

Het klassieke IP-filter is buiten gebruik gesteld. Zie [IOT hub klassiek IP-filter en hoe u een upgrade uitvoert](iot-hub-ip-filter-classic.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [IoT Hub metrische gegevens](./monitor-iot-hub.md)
* [Ondersteuning voor virtuele netwerken IoT Hub met persoonlijke koppelingen en beheerde identiteit](virtual-network-support.md)
* [Open bare netwerk toegang beheren voor uw IoT-hub](iot-hub-public-network-access.md)
* [IoT Hub bewaken](monitor-iot-hub.md)