---
title: Eindpunt quotum verhogen-LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) biedt de mogelijkheid om het quotum voor eindpunt aanvragen te verhogen buiten het quotum van een enkele sleutel. Dit doet u door meer sleutels te maken voor LUIS en deze toe te voegen aan de LUIS-toepassing op de pagina **publiceren** in de sectie **resources en sleutels** .
manager: nitinme
ms.custom: seodec18, devx-track-js, devx-track-azurepowershell
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/20/2019
ms.openlocfilehash: 37c4bd2af080a76e93bc9599f06e4d502985979f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609648"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Microsoft Azure Traffic Manager gebruiken voor het beheren van eindpunt quotum voor sleutels
Language Understanding (LUIS) biedt de mogelijkheid om het quotum voor eindpunt aanvragen te verhogen buiten het quotum van een enkele sleutel. Dit doet u door meer sleutels te maken voor LUIS en deze toe te voegen aan de LUIS-toepassing op de pagina **publiceren** in de sectie **resources en sleutels** .

De client toepassing moet het verkeer over de sleutels beheren. LUIS doet dat niet.

In dit artikel wordt uitgelegd hoe u het verkeer beheert over sleutels met Azure [Traffic Manager][traffic-manager-marketing]. U moet al een getrainde en gepubliceerde LUIS-app hebben. Als u er nog geen hebt, volgt u de vooraf ontwikkelde domein- [Quick](luis-get-started-create-app.md)start.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Verbinding maken met Power shell in de Azure Portal
Open in [Azure][azure-portal] Portal het Power shell-venster. Het pictogram voor het Power shell-venster is de **>_** in de bovenste navigatie balk. Als u Power shell gebruikt vanuit de portal, beschikt u over de meest recente versie van Power shell en bent u geverifieerd. Voor Power shell in de portal is een [Azure Storage](https://azure.microsoft.com/services/storage/) -account vereist.

![Scherm afbeelding van Azure Portal met Power shell-venster geopend](./media/traffic-manager/azure-portal-powershell.png)

In de volgende secties worden [Traffic Manager Power shell-cmdlets](/powershell/module/az.trafficmanager/#traffic_manager)gebruikt.

## <a name="create-azure-resource-group-with-powershell"></a>Een Azure-resource groep maken met Power shell
Voordat u de Azure-resources maakt, maakt u een resource groep om alle resources te bevatten. Geef de resource groep een naam `luis-traffic-manager` en gebruik de regio `West US` . De regio van de resource groep slaat meta gegevens over de groep op. De resources worden niet langzamer als ze zich in een andere regio bevinden.

Een resource groep maken met de cmdlet **[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** :

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>LUIS-sleutels maken om het totale eindpunt quotum te verg Roten
1. Maak in de Azure Portal twee **Language Understanding** -sleutels, één in de `West US` en een in de `East US` . Gebruik de bestaande resource groep die is gemaakt in de vorige sectie met de naam `luis-traffic-manager` .

    ![Scherm opname van Azure Portal met twee LUIS-sleutels in de resource groep Luis-Traffic-Manager](./media/traffic-manager/luis-keys.png)

2. Op de [Luis][LUIS] -website, in de sectie **beheren** op de pagina **Azure-resources** , wijst u sleutels toe aan de app en publiceert u de app opnieuw door de knop **publiceren** te selecteren in het menu rechtsboven.

    De voor beeld-URL in de kolom **endpoint** gebruikt een GET-aanvraag met de eindpunt sleutel als een query parameter. Kopieer de twee nieuwe sleutels Url's van eind punten. Deze worden gebruikt als onderdeel van de Traffic Manager configuratie verderop in dit artikel.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>LUIS-eindpunt aanvragen beheren op verschillende sleutels met Traffic Manager
Traffic Manager maakt een nieuw DNS-toegangs punt voor uw eind punten. Het fungeert niet als gateway of proxy, maar is strikt het DNS-niveau. In dit voor beeld worden geen DNS-records gewijzigd. Er wordt een DNS-bibliotheek gebruikt om te communiceren met Traffic Manager om het juiste eind punt voor die specifieke aanvraag te verkrijgen. Voor _elke_ aanvraag die is bedoeld voor Luis moet eerst een Traffic Manager aanvraag worden vastgesteld om te bepalen welk Luis-eind punt moet worden gebruikt.

### <a name="polling-uses-luis-endpoint"></a>Polling maakt gebruik van het LUIS-eind punt
Traffic Manager pollt de eind punten regel matig om te controleren of het eind punt nog steeds beschikbaar is. De aangevraagde Traffic Manager URL moet toegankelijk zijn met een GET-aanvraag en een 200 retour neren. De eind punt-URL op de pagina **publiceren** is dit. Aangezien elke eindpunt sleutel een andere route en query teken reeks parameters heeft, moet voor elke eindpunt sleutel een ander polling-pad worden opgegeven. Elke keer dat Traffic Manager polls, kost het een quotum aanvraag. De query teken reeks parameter **q** van het Luis-eind punt is de utterance die naar Luis wordt verzonden. Deze para meter, in plaats van een utterance te verzenden, wordt gebruikt om Traffic Manager polling toe te voegen aan het LUIS-eindpunt logboek als een techniek voor fout opsporing tijdens het ophalen van Traffic Manager geconfigureerd.

Omdat elk LUIS-eind punt een eigen pad nodig heeft, heeft het een eigen Traffic Manager profiel nodig. Als u meerdere profielen wilt beheren, maakt u een [ _geneste_ Traffic Manager](../../traffic-manager/traffic-manager-nested-profiles.md) architectuur. Een bovenliggend profiel wijst naar de onderliggende profielen en beheert het verkeer hierin.

Nadat de Traffic Manager is geconfigureerd, moet u het pad wijzigen om de query teken reeks parameter = false te gebruiken, zodat uw logboek niet wordt ingevuld met polling.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Traffic Manager met geneste profielen configureren
In de volgende secties worden twee onderliggende profielen gemaakt: één voor de Oost-LUIS en een voor de West LUIS-sleutel. Vervolgens wordt er een bovenliggend profiel gemaakt en worden de twee onderliggende profielen toegevoegd aan het bovenliggende profiel.

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Het Traffic Manager-profiel VS-Oost maken met Power shell
Als u het Amerikaanse Traffic Manager-profiel wilt maken, zijn er verschillende stappen: profiel maken, eind punt toevoegen en eind punt instellen. Een Traffic Manager profiel kan een groot aantal eind punten hebben, maar elk eind punt heeft hetzelfde validerings pad. Omdat de Url's van het LUIS-eind punt voor de Oost-en West-abonnementen verschillend zijn als gevolg van de regio en de eindpunt sleutel, moet elk LUIS-eind punt één eind punt in het profiel zijn.

1. Profiel maken met de cmdlet **[New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)**

    Gebruik de volgende cmdlet om het profiel te maken. Zorg ervoor dat u de `appIdLuis` en wijzigt `subscriptionKeyLuis` . De subscriptionKey is voor de Amerikaanse LUIS-sleutel. Als het pad niet juist is, met inbegrip van de LUIS-App-ID en de eindpunt sleutel, is de Traffic Manager polling de status `degraded` omdat het verkeer niet kan worden aangevraagd door het Luis-eind punt. Zorg ervoor dat de waarde van `q` is `traffic-manager-east` , zodat u deze waarde kunt zien in de Luis-eindpunt Logboeken.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```

    In deze tabel wordt elke variabele in de cmdlet uitgelegd:

    |Configuratie parameter|Naam of waarde van variabele|Doel|
    |--|--|--|
    |-Name|Luis-profiel-Oost-Timor|Traffic Manager naam in Azure Portal|
    |-ResourceGroupName|Luis-Traffic-Manager|Gemaakt in de vorige sectie|
    |-TrafficRoutingMethod|Prestaties|Zie [Traffic Manager routerings methoden][routing-methods]voor meer informatie. Bij het gebruik van prestaties moet de URL-aanvraag voor de Traffic Manager afkomstig zijn uit de regio van de gebruiker. Als u een chatbot of een andere toepassing gaat door lopen, is het de verantwoordelijkheid van de chatbot om de regio in de aanroep van de Traffic Manager te simuleren. |
    |-RelativeDnsName|Luis-DNS-oostus|Dit is het subdomein voor de service: luis-dns-eastus.trafficmanager.net|
    |-TTL|30|Polling-interval, 30 seconden|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Poort en protocol voor LUIS is HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Vervang `<appIdLuis>` en `<subscriptionKeyLuis>` door uw eigen waarden.|

    Een geslaagde aanvraag heeft geen reactie.

2. Onderstaand Oost-eind punt toevoegen met de cmdlet **[add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    In deze tabel wordt elke variabele in de cmdlet uitgelegd:

    |Configuratie parameter|Naam of waarde van variabele|Doel|
    |--|--|--|
    |-Eind puntnaam|Luis-Oost-eind punt|De naam van het eind punt wordt weer gegeven onder het profiel|
    |-TrafficManagerProfile|$eastprofile|Profiel object gebruiken dat u in stap 1 hebt gemaakt|
    |-Type|ExternalEndpoints|Zie [Traffic Manager-eind punt][traffic-manager-endpoints] voor meer informatie. |
    |-Doel|eastus.api.cognitive.microsoft.com|Dit is het domein voor het LUIS-eind punt.|
    |-EndpointLocation|eastus|Regio van het eind punt|
    |-EndpointStatus|Ingeschakeld|Eind punt inschakelen wanneer deze wordt gemaakt|

    Het geslaagde antwoord ziet er als volgt uit:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. Het VS-eind punt voor Oost instellen met de cmdlet **[set-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Een geslaagde reactie is hetzelfde als die van stap 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Het Traffic Manager profiel voor de VS-West maken met Power shell
Voer de volgende stappen uit om het profiel vs-West Traffic Manager te maken: Maak een profiel, voeg een eind punt toe en stel een eind punt in.

1. Profiel maken met de cmdlet **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    Gebruik de volgende cmdlet om het profiel te maken. Zorg ervoor dat u de `appIdLuis` en wijzigt `subscriptionKeyLuis` . De subscriptionKey is voor de Amerikaanse LUIS-sleutel. Als het pad niet juist is, met inbegrip van de LUIS-App-ID en de eindpunt sleutel, is de Traffic Manager polling de status van `degraded` omdat verkeer beheren het Luis-eind punt niet kan aanvragen. Zorg ervoor dat de waarde van `q` is `traffic-manager-west` , zodat u deze waarde kunt zien in de Luis-eindpunt Logboeken.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```

    In deze tabel wordt elke variabele in de cmdlet uitgelegd:

    |Configuratie parameter|Naam of waarde van variabele|Doel|
    |--|--|--|
    |-Name|Luis-profiel-westus|Traffic Manager naam in Azure Portal|
    |-ResourceGroupName|Luis-Traffic-Manager|Gemaakt in de vorige sectie|
    |-TrafficRoutingMethod|Prestaties|Zie [Traffic Manager routerings methoden][routing-methods]voor meer informatie. Bij het gebruik van prestaties moet de URL-aanvraag voor de Traffic Manager afkomstig zijn uit de regio van de gebruiker. Als u een chatbot of een andere toepassing gaat door lopen, is het de verantwoordelijkheid van de chatbot om de regio in de aanroep van de Traffic Manager te simuleren. |
    |-RelativeDnsName|Luis-DNS-westus|Dit is het subdomein voor de service: luis-dns-westus.trafficmanager.net|
    |-TTL|30|Polling-interval, 30 seconden|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Poort en protocol voor LUIS is HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Vervang `<appId>` en `<subscriptionKey>` door uw eigen waarden. Onthoud dat deze eindpunt sleutel anders is dan de Oost-eindpunt sleutel|

    Een geslaagde aanvraag heeft geen reactie.

2. Een eind punt voor de VS West toevoegen met de cmdlet **[add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    In deze tabel wordt elke variabele in de cmdlet uitgelegd:

    |Configuratie parameter|Naam of waarde van variabele|Doel|
    |--|--|--|
    |-Eind puntnaam|Luis-West-eind punt|De naam van het eind punt wordt weer gegeven onder het profiel|
    |-TrafficManagerProfile|$westprofile|Profiel object gebruiken dat u in stap 1 hebt gemaakt|
    |-Type|ExternalEndpoints|Zie [Traffic Manager-eind punt][traffic-manager-endpoints] voor meer informatie. |
    |-Doel|westus.api.cognitive.microsoft.com|Dit is het domein voor het LUIS-eind punt.|
    |-EndpointLocation|westus|Regio van het eind punt|
    |-EndpointStatus|Ingeschakeld|Eind punt inschakelen wanneer deze wordt gemaakt|

    Het geslaagde antwoord ziet er als volgt uit:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. Het eind punt voor de Verenigde Staten instellen met de cmdlet **[set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Een geslaagde reactie is hetzelfde als die van stap 2.

### <a name="create-parent-traffic-manager-profile"></a>Bovenliggend Traffic Manager profiel maken
Maak het bovenliggende Traffic Manager profiel en koppel twee onderliggende Traffic Manager profielen aan het bovenliggende object.

1. Bovenliggend profiel maken met de cmdlet **[New-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    In deze tabel wordt elke variabele in de cmdlet uitgelegd:

    |Configuratie parameter|Naam of waarde van variabele|Doel|
    |--|--|--|
    |-Name|Luis-profiel-bovenliggend item|Traffic Manager naam in Azure Portal|
    |-ResourceGroupName|Luis-Traffic-Manager|Gemaakt in de vorige sectie|
    |-TrafficRoutingMethod|Prestaties|Zie [Traffic Manager routerings methoden][routing-methods]voor meer informatie. Bij het gebruik van prestaties moet de URL-aanvraag voor de Traffic Manager afkomstig zijn uit de regio van de gebruiker. Als u een chatbot of een andere toepassing gaat door lopen, is het de verantwoordelijkheid van de chatbot om de regio in de aanroep van de Traffic Manager te simuleren. |
    |-RelativeDnsName|Luis-DNS-Parent|Dit is het subdomein voor de service: luis-dns-parent.trafficmanager.net|
    |-TTL|30|Polling-interval, 30 seconden|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Poort en protocol voor LUIS is HTTPS/443|
    |-MonitorPath|`/`|Dit pad is niet van belang omdat er in plaats daarvan de onderliggende eindpunt paden worden gebruikt.|

    Een geslaagde aanvraag heeft geen reactie.

2. Onderliggend Oost-onderliggend profiel toevoegen aan bovenliggend item met **[het type add-AzTrafficManagerEndpointConfig](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** en **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    In deze tabel wordt elke variabele in de cmdlet uitgelegd:

    |Configuratie parameter|Naam of waarde van variabele|Doel|
    |--|--|--|
    |-Eind puntnaam|onderliggend-eind punt-useast|Oost-profiel|
    |-TrafficManagerProfile|$parentprofile|Profiel waaraan u dit eind punt wilt toewijzen|
    |-Type|NestedEndpoints|Zie [add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig)voor meer informatie. |
    |-TargetResourceId|$eastprofile. Id|ID van het onderliggende profiel|
    |-EndpointStatus|Ingeschakeld|Status van eind punt na toevoegen aan bovenliggend element|
    |-EndpointLocation|eastus|[Naam](https://azure.microsoft.com/global-infrastructure/regions/) van de Azure-regio van de resource|
    |-MinChildEndpoints|1|Minimum aantal naar onderliggend eind punt|

    Het geslaagde antwoord ziet er als volgt uit en bevat het nieuwe `child-endpoint-useast` eind punt:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. Onderliggend West-onderliggend profiel toevoegen aan bovenliggend item met de cmdlet **[add-AzTrafficManagerEndpointConfig en het](/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** **NestedEndpoints** -type

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    In deze tabel wordt elke variabele in de cmdlet uitgelegd:

    |Configuratie parameter|Naam of waarde van variabele|Doel|
    |--|--|--|
    |-Eind puntnaam|onderliggend-eind punt-uswest|Westelijke profiel|
    |-TrafficManagerProfile|$parentprofile|Profiel waaraan u dit eind punt wilt toewijzen|
    |-Type|NestedEndpoints|Zie [add-AzTrafficManagerEndpointConfig](/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig)voor meer informatie. |
    |-TargetResourceId|$westprofile. Id|ID van het onderliggende profiel|
    |-EndpointStatus|Ingeschakeld|Status van eind punt na toevoegen aan bovenliggend element|
    |-EndpointLocation|westus|[Naam](https://azure.microsoft.com/global-infrastructure/regions/) van de Azure-regio van de resource|
    |-MinChildEndpoints|1|Minimum aantal naar onderliggend eind punt|

    Het geslaagde antwoord ziet eruit als het vorige `child-endpoint-useast` eind punt en het nieuwe `child-endpoint-uswest` eind punt:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. Eind punten instellen met de cmdlet **[set-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Een geslaagde reactie is hetzelfde als die van stap 3.

### <a name="powershell-variables"></a>Power shell-variabelen
In de vorige secties zijn drie Power shell-variabelen gemaakt: `$eastprofile` , `$westprofile` , `$parentprofile` . Deze variabelen worden gebruikt naar het einde van de Traffic Manager configuratie. Als u ervoor hebt gekozen om de variabelen niet te maken of verg eten of het Power shell-venster een time-out heeft, kunt u de Power shell-cmdlet **[Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** gebruiken om het profiel opnieuw op te halen en toe te wijzen aan een variabele.

Vervang de items tussen punt haken, `<>` met de juiste waarden voor elk van de drie profielen die u nodig hebt.

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Controleren of Traffic Manager werkt
Om te controleren of de Traffic Manager profielen werken, moeten de profielen de status van `Online` deze status hebben op basis van het polling pad van het eind punt.

### <a name="view-new-profiles-in-the-azure-portal"></a>Nieuwe profielen weer geven in de Azure Portal
U kunt controleren of alle drie de profielen zijn gemaakt door te kijken naar de resources in de `luis-traffic-manager` resource groep.

![Scherm opname van de Azure-resource groep Luis-Traffic-Manager](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Controleer of de profiel status online is
De Traffic Manager pollt het pad van elk eind punt om er zeker van te zijn dat het online is. Als deze online is, is de status van de onderliggende profielen `Online` . Deze wordt weer gegeven in het **overzicht** van elk profiel.

![Scherm afbeelding van het overzicht van Azure Traffic Manager-profiel met de monitor status online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Controleren of Traffic Manager polling werkt
Een andere manier om de polling van Traffic Manager te valideren werkt met de LUIS-eindpunt Logboeken. Op de pagina lijst met [Luis][LUIS] website-apps exporteert u het eindpunt logboek voor de toepassing. Omdat Traffic Manager polls vaak voor de twee eind punten, zijn er vermeldingen in de logboeken, zelfs als ze slechts een paar minuten zijn. Vergeet niet om te zoeken naar vermeldingen waarbij de query begint met `traffic-manager-` .

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>DNS-reactie van Traffic Manager werken valideren
Om te controleren of het DNS-antwoord een LUIS-eind punt retourneert, vraagt u het verkeer het bovenliggende profiel DNS te beheren met behulp van een DNS-client bibliotheek. De DNS-naam voor het bovenliggende profiel is `luis-dns-parent.trafficmanager.net` .

Met de volgende Node.js code wordt een aanvraag voor het bovenliggende profiel gemaakt en wordt een LUIS-eind punt geretourneerd:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

Het geslaagde antwoord met het LUIS-eind punt is:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com',
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Het Traffic Manager bovenliggende profiel gebruiken
Als u verkeer tussen eind punten wilt beheren, moet u een aanroep naar de Traffic Manager DNS invoegen om het LUIS-eind punt te vinden. Deze aanroep wordt uitgevoerd voor elke LUIS-eindpunt aanvraag en moet de geografische locatie van de gebruiker van de LUIS-client toepassing simuleren. Voeg de DNS-antwoord code toe tussen uw LUIS-client toepassing en de aanvraag voor het LUIS voor de eindpunt voorspelling.

## <a name="resolving-a-degraded-state"></a>Een gedegradeerde status oplossen

Schakel [Diagnostische logboeken](../../traffic-manager/traffic-manager-diagnostic-logs.md) in voor Traffic Manager om te zien waarom de eind punt status is gedegradeerd.

## <a name="clean-up"></a>Opschonen
Verwijder de twee LUIS-eindpunt sleutels, de drie Traffic Manager profielen en de resource groep die deze vijf resources bevat. Dit wordt gedaan vanuit de Azure Portal. U verwijdert de vijf resources uit de lijst met resources. Verwijder vervolgens de resource groep.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [middleware](/azure/bot-service/bot-builder-create-middleware?tabs=csaddmiddleware%252ccsetagoverwrite%252ccsmiddlewareshortcircuit%252ccsfallback%252ccsactivityhandler) -opties in BotFramework v4 om te begrijpen hoe deze Traffic Management-code kan worden toegevoegd aan een BotFramework-bot.

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: ../../traffic-manager/index.yml
[LUIS]: ./luis-reference-regions.md#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: ../../traffic-manager/traffic-manager-routing-methods.md
[traffic-manager-endpoints]: ../../traffic-manager/traffic-manager-endpoint-types.md