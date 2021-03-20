---
title: Privé-eind punten gebruiken met spraak Services
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van spraak Services met privé-eind punten van de persoonlijke Azure-koppeling
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: alexeyo
ms.openlocfilehash: c9af0cda14261e8eab7f1ecc05c50a289d7ddfdb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99559661"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>Spraak Services gebruiken via een persoonlijk eind punt

Met de [persoonlijke Azure-koppeling](../../private-link/private-link-overview.md) kunt u verbinding maken met Services in azure met behulp van een [persoonlijk eind punt](../../private-link/private-endpoint-overview.md). Een persoonlijk eind punt is een privé-IP-adres dat alleen toegankelijk is binnen een specifiek [virtueel netwerk](../../virtual-network/virtual-networks-overview.md) en subnet.

In dit artikel wordt uitgelegd hoe u persoonlijke koppelingen en privé-eind punten instelt en gebruikt met spraak Services in azure Cognitive Services.

> [!NOTE]
> Lees [hoe u virtuele netwerken gebruikt met Cognitive Services](../cognitive-services-virtual-networks.md)voordat u doorgaat.

In dit artikel wordt ook beschreven [hoe u privé-eind punten later kunt verwijderen, maar nog steeds de spraak resource kunt gebruiken](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).

## <a name="create-a-custom-domain-name"></a>Een aangepaste domein naam maken

Voor privé-eind punten is een [aangepaste subdomeinnaam vereist voor Cognitive Services](../cognitive-services-custom-subdomains.md). Gebruik de volgende instructies om een voor uw spraak bron te maken.

> [!WARNING]
> Een spraak bron waarvoor een aangepaste domein naam is ingeschakeld, maakt gebruik van een andere manier om te communiceren met spraak Services. Mogelijk moet u uw toepassings code aanpassen voor beide scenario's: [persoonlijk eind punt is ingeschakeld](#use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled) en [ *er geen* persoonlijk eind punt is ingeschakeld](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).
>
> Wanneer u een aangepaste domein naam inschakelt, kan de bewerking [niet onomkeerbaar](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name)zijn. De enige manier om terug te gaan naar de [regionale naam](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) is het maken van een nieuwe spraak resource.
>
> Als uw spraak bron een groot aantal gekoppelde aangepaste modellen en projecten heeft die zijn gemaakt via [Speech Studio](https://speech.microsoft.com/), raden we u ten zeerste aan de configuratie te proberen met een test resource voordat u de resource wijzigt die in de productie omgeving wordt gebruikt.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u een aangepaste domein naam wilt maken met behulp van de Azure Portal, voert u de volgende stappen uit:

1. Ga naar [Azure Portal](https://portal.azure.com/) en meld u aan bij uw account.
1. Selecteer de vereiste spraak resource.
1. Selecteer in de **resource beheer** groep in het linkerdeel venster **netwerken**.
1. Selecteer op het tabblad **firewalls en virtuele netwerken** de optie **aangepaste domein naam genereren**. Er wordt een nieuw rechts venster weer gegeven met instructies voor het maken van een uniek aangepast subdomein voor uw resource.
1. Voer in het deel venster **aangepaste domein naam genereren** een aangepaste domein naam in. Uw volledige aangepaste domein ziet er als volgt uit: `https://{your custom name}.cognitiveservices.azure.com` . 
    
    Houd er rekening mee dat nadat u een aangepaste domein naam hebt gemaakt, deze _niet meer kan_ worden gewijzigd.
    
    Nadat u de aangepaste domein naam hebt ingevoerd, selecteert u **Opslaan**.
1. Nadat de bewerking is voltooid, selecteert u in de **resource beheer** groep de optie **sleutels en eind punt**. Controleer of de nieuwe eindpunt naam van uw resource op deze manier wordt gestart: `https://{your custom name}.cognitiveservices.azure.com` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een aangepaste domein naam wilt maken met behulp van Power shell, controleert u of uw computer Power shell versie 7. x of hoger met de Azure PowerShell module versie 5.1.0 of hoger heeft. Voer de volgende stappen uit om de versies van deze hulpprogram ma's weer te geven:

1. Voer in een Power shell-venster het volgende in:

    `$PSVersionTable`

    Bevestig dat de `PSVersion` waarde 7. x of hoger is. Als u Power shell wilt bijwerken, volgt u de instructies bij het [installeren van verschillende versies van Power shell](/powershell/scripting/install/installing-powershell).

1. Voer in een Power shell-venster het volgende in:

    `Get-Module -ListAvailable Az`

    Als niets wordt weer gegeven of als de versie van de Azure PowerShell-module ouder is dan 5.1.0, volgt u de instructies op [de Azure PowerShell-module installeren om een](/powershell/azure/install-Az-ps) upgrade uit te voeren.

Voordat u doorgaat, voert `Connect-AzAccount` u uit om een verbinding te maken met Azure.

## <a name="verify-that-a-custom-domain-name-is-available"></a>Controleer of er een aangepaste domein naam beschikbaar is

Controleer of het aangepaste domein dat u wilt gebruiken, beschikbaar is. Met de volgende code wordt bevestigd dat het domein beschikbaar is via de bewerking [domein beschikbaarheid controleren](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) in het Cognitive Services rest API.

> [!TIP]
> De volgende code werkt *niet* in azure Cloud shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare the OAuth token to use in the request to the Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to the Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
Als de gewenste naam beschikbaar is, ziet u een antwoord als volgt:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Als de naam al wordt gebruikt, ziet u het volgende antwoord:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>Uw aangepaste domein naam maken

Als u een aangepaste domein naam voor de geselecteerde spraak resource wilt inschakelen, gebruikt u de cmdlet [set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) .

> [!WARNING]
> Nadat de volgende code is uitgevoerd, maakt u een aangepaste domein naam voor uw spraak resource. Houd er rekening mee dat deze naam *niet kan* worden gewijzigd.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

Deze sectie vereist de nieuwste versie van de Azure CLI. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="verify-that-the-custom-domain-name-is-available"></a>Controleer of de aangepaste domein naam beschikbaar is

Controleer of het aangepaste domein dat u wilt gebruiken, vrij is. Gebruik de methode [domein beschikbaarheid controleren](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) vanuit het Cognitive Services rest API.

Kopieer het volgende code blok, plaats de aangepaste domein naam van uw voor keur en sla deze op in het bestand `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Kopieer het bestand naar de huidige map of upload het naar Azure Cloud Shell en voer de volgende opdracht uit. Vervang `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` door de id van uw Azure-abonnement.

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Als de gewenste naam beschikbaar is, ziet u een antwoord als volgt:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Als de naam al wordt gebruikt, ziet u het volgende antwoord:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enable-a-custom-domain-name"></a>Een aangepaste domein naam inschakelen

Als u een aangepaste domein naam voor de geselecteerde spraak resource wilt inschakelen, gebruikt u de opdracht [AZ cognitiveservices account update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) .

Selecteer het Azure-abonnement met de spraak resource. Als uw Azure-account slechts één actief abonnement heeft, kunt u deze stap overs Laan. Vervang `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` door de id van uw Azure-abonnement.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Stel de aangepaste domein naam in op de geselecteerde resource. Vervang de waarden van de voor beeld-para meters door de daad werkelijk en voer de volgende opdracht uit.

> [!WARNING]
> Nadat u de volgende opdracht hebt uitgevoerd, maakt u een aangepaste domein naam voor uw spraak resource. Houd er rekening mee dat deze naam *niet kan* worden gewijzigd.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="enable-private-endpoints"></a>Privé-eind punten inschakelen

Het is raadzaam om de [privé-DNS-zone](../../dns/private-dns-overview.md) die aan het virtuele netwerk is gekoppeld, te gebruiken met de vereiste updates voor de persoonlijke eind punten. Tijdens het inrichtings proces maakt u standaard een privé-DNS-zone. Als u uw eigen DNS-server gebruikt, moet u mogelijk ook uw DNS-configuratie wijzigen. 

Beslis of u een DNS-strategie hebt *voordat* u persoonlijke eind punten inricht voor een spraak bron voor productie. En test uw DNS-wijzigingen, met name als u uw eigen DNS-server gebruikt.

Gebruik een van de volgende artikelen om persoonlijke eind punten te maken. In deze artikelen wordt een web-app als voorbeeld bron gebruikt om persoonlijke eind punten in te scha kelen.

- [Een persoonlijk eind punt maken met behulp van de Azure Portal](../../private-link/create-private-endpoint-portal.md)
- [Een persoonlijk eind punt maken met behulp van Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Een persoonlijk eind punt maken met behulp van Azure CLI](../../private-link/create-private-endpoint-cli.md)

Gebruik deze para meters in plaats van de para meters in het artikel dat u hebt gekozen:

| Instelling             | Waarde                                    |
|---------------------|------------------------------------------|
| Resourcetype       | **Micro soft. CognitiveServices/accounts** |
| Resource            | **\<your-speech-resource-name>**         |
| Stel subresource in | **organisatieaccount**                              |

**DNS voor privé-eind punten:** Bekijk de algemene principes van [DNS voor privé-eind punten in cognitive services-resources](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Controleer vervolgens of de DNS-configuratie correct werkt door de controles uit te voeren die worden beschreven in de volgende secties.

### <a name="resolve-dns-from-the-virtual-network"></a>DNS omzetten vanuit het virtuele netwerk

Deze controle is *vereist*.

Volg deze stappen om de aangepaste DNS-vermelding te testen vanuit uw virtuele netwerk:

1. Meld u aan bij een virtuele machine die zich bevindt in het virtuele netwerk waaraan u het persoonlijke eind punt hebt gekoppeld. 
1. Open een Windows-opdracht prompt of een bash-shell, voer uit `nslookup` en bevestig dat de aangepaste domein naam van uw resource is opgelost.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. Controleer of het IP-adres overeenkomt met het IP-adres van uw persoonlijke eind punt.

### <a name="resolve-dns-from-other-networks"></a>DNS van andere netwerken oplossen

Voer deze controle alleen uit als u de optie **alle netwerken** of de optie toegang tot de **geselecteerde netwerken en persoonlijke eind punten** hebt ingeschakeld in de sectie **netwerken** van uw resource. 

Als u van plan bent om toegang te krijgen tot de resource door alleen een persoonlijk eind punt te gebruiken, kunt u deze sectie overs Laan.

1. Meld u aan bij een computer die is verbonden met een netwerk dat toegang heeft tot de bron.
2. Open een Windows-opdracht prompt of bash-shell, voer uit `nslookup` en bevestig dat de aangepaste domein naam van uw resource is opgelost.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  fe80::1

   Non-authoritative answer:
   Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
   Address:  13.69.67.71
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
             my-private-link-speech.privatelink.cognitiveservices.azure.com
             westeurope.prod.vnet.cog.trafficmanager.net
   ```

> [!NOTE]
> Het omgezette IP-adres wijst naar een eind punt van een virtuele netwerk proxy, waarmee het netwerk verkeer wordt verzonden naar het persoonlijke eind punt voor de Cognitive Services bron. Het gedrag verschilt voor een resource met een aangepaste domein naam, maar *zonder* persoonlijke eind punten. Zie [deze sectie](#dns-configuration) voor meer informatie.

## <a name="adjust-existing-applications-and-solutions"></a>Bestaande toepassingen en oplossingen aanpassen

Een spraak bron waarvoor een aangepast domein is ingeschakeld, maakt gebruik van een andere manier om te communiceren met spraak Services. Dit geldt voor een op een domein ingeschakelde spraak resource met en zonder persoonlijke eind punten. De informatie in deze sectie is van toepassing op beide scenario's.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled"></a>Een spraak bron gebruiken waarvoor een aangepaste domein naam en een persoonlijk eind punt zijn ingeschakeld

Een spraak bron met een aangepaste domein naam en een persoonlijk eind punt is ingeschakeld, maakt gebruik van een andere manier om te communiceren met spraak Services. In deze sectie wordt uitgelegd hoe u een dergelijke resource gebruikt met de speech Services REST-Api's en de [Speech-SDK](speech-sdk.md).

> [!NOTE]
> Een spraak bron zonder persoonlijke eind punten maar waarbij een aangepaste domein naam is ingeschakeld, heeft ook een speciale manier om te communiceren met spraak Services. Deze manier wijkt af van het scenario van een spraak resource met persoonlijke eind punten. Zie de sectie [een spraak resource met een aangepaste domein naam en zonder persoonlijke eind punten gebruiken](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints)als u een dergelijke resource hebt (bijvoorbeeld als u een resource met persoonlijke eind punten hebt die u vervolgens hebt gekozen).

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>Spraak bron met een aangepaste domein naam en een persoonlijk eind punt: gebruik met de REST Api's

We gebruiken `my-private-link-speech.cognitiveservices.azure.com` als een voor beeld van een DNS-naam voor een spraak bron (aangepast domein) voor deze sectie.

Spraak Services hebben REST-Api's voor [spraak naar tekst](rest-speech-to-text.md) en [tekst naar spraak](rest-text-to-speech.md). Houd rekening met de volgende informatie voor het scenario met het persoonlijke eind punt.

Spraak-naar-tekst heeft twee REST-Api's. Elke API heeft een ander doel, maakt gebruik van verschillende eind punten en vereist een andere benadering wanneer u deze gebruikt in het scenario met het persoonlijke eind punt.

De REST-to-text-Api's zijn:
- [Spraak-naar-tekst rest API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30), die wordt gebruikt voor [Batch transcriptie](batch-transcription.md) en [Custom speech](custom-speech-overview.md). v 3.0 is een [opvolger van v 2.0](./migrate-v2-to-v3.md)
- [Spraak-naar-tekst rest API voor korte audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio), die wordt gebruikt voor online transcriptie 

Het gebruik van de spraak-naar-tekst REST API voor korte audio en het REST API van tekst naar spraak in het scenario met het persoonlijke eind punt is hetzelfde. Dit komt overeen met de [Speech SDK-situatie](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) die verderop in dit artikel wordt beschreven. 

Spraak-naar-tekst-REST API v 3.0 maakt gebruik van een andere set eind punten. hiervoor is een andere benadering vereist voor het scenario met het persoonlijke endpoint-functionaliteit.

In de volgende subsecties worden beide gevallen beschreven.

##### <a name="speech-to-text-rest-api-v30"></a>Spraak-naar-tekst REST API v 3.0

Spraak bronnen gebruiken meestal [Cognitive Services regionale eind punten](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) voor communicatie met de [spraak-naar-tekst rest API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Deze resources hebben de volgende naamgevings indeling: <p/>`{region}.api.cognitive.microsoft.com`.

Dit is een voor beeld van een aanvraag-URL:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```

> [!NOTE]
> Zie [dit artikel](sovereign-clouds.md) voor Azure Government en Azure China-eind punten.

Nadat u een aangepast domein hebt ingeschakeld voor een spraak bron (die nodig is voor persoonlijke eind punten), gebruikt die resource het volgende DNS-naam patroon voor het basis REST API-eind punt: <p/>`{your custom name}.cognitiveservices.azure.com`.

Dat betekent dat in het volgende voor beeld de naam van het REST API-eind punt is: <p/>`my-private-link-speech.cognitiveservices.azure.com`.

En de URL van de voorbeeld aanvraag moet worden geconverteerd naar:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Deze URL moet bereikbaar zijn vanuit het virtuele netwerk waaraan het persoonlijke eind punt is gekoppeld (met de [juiste DNS-omzetting](#resolve-dns-from-the-virtual-network)).

Nadat u een aangepaste domein naam voor een spraak resource hebt ingeschakeld, vervangt u de hostnaam in alle aanvraag-Url's doorgaans door de nieuwe naam van het aangepaste domein. Alle andere onderdelen van de aanvraag (zoals het pad `/speechtotext/v3.0/transcriptions` in het vorige voor beeld) blijven hetzelfde.

> [!TIP]
> Sommige klanten ontwikkelen toepassingen die gebruikmaken van het gebied deel van de DNS-naam van het regionale eind punt (bijvoorbeeld om de aanvraag te verzenden naar de spraak bron die in de desbetreffende Azure-regio is geïmplementeerd).
>
> Een aangepast domein voor een spraak resource bevat *geen* informatie over de regio waarin de resource is geïmplementeerd. De eerder beschreven toepassings logica werkt dus *niet* en moet worden gewijzigd.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Spraak-naar-tekst REST API voor korte audio en tekst naar spraak REST API

De [spraak-naar-tekst rest API voor korte audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) en de [tekst-naar-spraak-rest API](rest-text-to-speech.md) twee typen eind punten gebruiken:
- [Cognitive Services regionale eind punten](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) voor de communicatie met de Cognitive Services rest API voor het verkrijgen van een autorisatie token
- Speciale eind punten voor alle andere bewerkingen

> [!NOTE]
> Zie [dit artikel](sovereign-clouds.md) voor Azure Government en Azure China-eind punten.

De gedetailleerde beschrijving van de speciale eind punten en hoe hun URL moet worden getransformeerd voor een spraak bron met privé-eind punt, is in [Deze subsectie](#construct-endpoint-url) opgenomen over gebruik met de spraak-SDK. Hetzelfde principe dat voor de SDK wordt beschreven, is van toepassing op de REST API voor spraak naar tekst voor korte audio en de tekst naar spraak REST API.

Raadpleeg het volgende voor beeld om vertrouwd te raken met het materiaal in de Subsectie in de vorige alinea. In het voor beeld wordt de REST API van tekst naar spraak beschreven. Het gebruik van de spraak-naar-tekst REST API voor korte audio is volledig gelijkwaardig.

> [!NOTE]
> Wanneer u gebruikmaakt van de spraak-naar-tekst REST API voor korte audio-en tekst-naar-spraak-REST API in scenario's met een persoonlijk eind punt, gebruikt u een abonnements sleutel die wordt door gegeven via de `Ocp-Apim-Subscription-Key` koptekst. (Zie de Details voor [spraak naar tekst rest API voor korte audio](rest-speech-to-text.md#request-headers) en [tekst naar spraak rest API](rest-text-to-speech.md#request-headers))
>
> Het gebruik van een autorisatie token en het door geven aan het speciale eind punt via de `Authorization` header werkt *alleen* als u de optie **alle netwerken** hebt ingeschakeld in de sectie **netwerken** van uw spraak bron. In andere gevallen krijgt u een `Forbidden` `BadRequest` fout melding wanneer u probeert een autorisatie token te verkrijgen.

**Voor beeld van het gebruik van tekst naar spraak REST API**

We gebruiken Europa-west als een voor beeld van een Azure `my-private-link-speech.cognitiveservices.azure.com` -regio en als een voor beeld van een DNS-naam voor een spraak bron (aangepast domein). De aangepaste domein naam `my-private-link-speech.cognitiveservices.azure.com` in ons voor beeld maakt deel uit van de spraak bron die in de Europa-West regio is gemaakt.

Als u de lijst met stemmen die in de regio worden ondersteund, wilt ophalen, voert u de volgende aanvraag uit:

```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
Meer informatie vindt u in de [tekst-naar-spraak-rest API documentatie](rest-text-to-speech.md).

Voor de spraak resource met persoonlijk eind punt is ingeschakeld, moet de eind punt-URL voor dezelfde bewerking worden gewijzigd. Dezelfde aanvraag ziet er als volgt uit:

```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
Zie een gedetailleerde uitleg in de Subsectie URL van het [Construct-eind punt](#construct-endpoint-url) voor de spraak-SDK.

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>Spraak bron met een aangepaste domein naam en een persoonlijk eind punt: gebruik met de spraak-SDK

Voor het gebruik van de Speech SDK met een aangepaste domein naam en spraak bronnen met persoonlijke eind punten moet u uw toepassings code controleren en waarschijnlijk wijzigen.

We gebruiken `my-private-link-speech.cognitiveservices.azure.com` als een voor beeld van een DNS-naam voor een spraak bron (aangepast domein) voor deze sectie.

##### <a name="construct-endpoint-url"></a>Eind punt-URL maken

Doorgaans in SDK-scenario's (en in de spraak-naar-tekst REST API voor korte audio-en tekst-naar-spraak-REST API scenario's), gebruiken spraak bronnen de toegewezen regionale eind punten voor verschillende service aanbiedingen. De indeling van de DNS-naam voor deze eind punten is:

`{region}.{speech service offering}.speech.microsoft.com`

Een voor beeld van een DNS-naam is:

`westeurope.stt.speech.microsoft.com`

Alle mogelijke waarden voor de regio (eerste element van de DNS-naam) worden weer gegeven in [regio's met spraak Services](regions.md)die worden ondersteund. (Zie [dit artikel](sovereign-clouds.md) voor eind punten van Azure Government en Azure China.) De volgende tabel bevat de mogelijke waarden voor de speech services-aanbieding (tweede element van de DNS-naam):

| DNS-naam waarde | Spraak service aanbieding                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Aangepaste opdrachten](custom-commands.md)                       |
| `convai`       | [Gesprekstranscriptie](conversation-transcription.md) |
| `s2s`          | [Speech Translation](speech-translation.md)                 |
| `stt`          | [Spraak naar tekst](speech-to-text.md)                         |
| `tts`          | [Tekst-naar-spraak](text-to-speech.md)                         |
| `voice`        | [Aangepaste stem](how-to-custom-voice.md)                      |

Het vorige voor beeld ( `westeurope.stt.speech.microsoft.com` ) staat voor een spraak-naar-tekst-eind punt in Europa-West.

Eind punten met persoonlijke eind punten communiceren met spraak Services via een speciale proxy. Als gevolg hiervan *moet u de url's van de eindpunt verbinding wijzigen*. 

Een ' standaard ' eind punt-URL ziet er als volgt uit: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

De URL van een privé-eind punt ziet er als volgt uit: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Voor beeld 1.** Een toepassing communiceert met behulp van de volgende URL (spraak herkenning met het basis model voor Engels (Verenigde Staten) in Europa-west):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Als u de aangepaste domein naam van de spraak resource wilt gebruiken in het scenario met het persoonlijke eind punt `my-private-link-speech.cognitiveservices.azure.com` , moet u de URL als volgt wijzigen:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Let op de details:

- De hostnaam `westeurope.stt.speech.microsoft.com` wordt vervangen door de naam van het aangepaste domein `my-private-link-speech.cognitiveservices.azure.com` .
- Het tweede element van de oorspronkelijke DNS-naam ( `stt` ) wordt het eerste element van het URL-pad en gaat vooraf aan het oorspronkelijke pad. De oorspronkelijke URL `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` wordt dus `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**Voor beeld 2.** Een toepassing gebruikt de volgende URL om spraak te maken in Europa-west met behulp van een aangepast spraak model:
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

De volgende equivalente URL maakt gebruik van een persoonlijk eind punt dat is ingeschakeld, waarbij de aangepaste domein naam van de spraak bron `my-private-link-speech.cognitiveservices.azure.com` :

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Hetzelfde principe in voor beeld 1 wordt toegepast, maar het sleutel element is deze tijd `voice` .

##### <a name="modifying-applications"></a>Toepassingen wijzigen

Volg deze stappen om uw code te wijzigen:

1. De URL van het eind punt van de toepassing bepalen:

   - [Schakel logboek registratie in voor uw toepassing](how-to-use-logging.md) en voer deze uit om activiteiten te registreren.
   - Zoek in het logboek bestand naar `SPEECH-ConnectionUrl` . In overeenkomende regels bevat de `value` para meter de volledige URL die uw toepassing gebruikt om spraak services te bereiken.

   Voorbeeld:

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   De URL die de toepassing heeft gebruikt in dit voor beeld is:

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. Een exemplaar maken met `SpeechConfig` behulp van een volledige eind punt-URL:

   1. Wijzig het eind punt dat u zojuist hebt bepaald, zoals beschreven in de sectie voor de vorige URL voor het maken van een [eind punt](#construct-endpoint-url) .

   1. Wijzig de manier waarop u het exemplaar maakt `SpeechConfig` . Het is waarschijnlijk dat uw toepassing er ongeveer als volgt gebruikt:
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      Dit werkt niet voor een spraak resource met persoonlijke eind punten vanwege de hostnaam en URL-wijzigingen die we in de vorige secties hebben beschreven. Als u de bestaande toepassing probeert uit te voeren zonder dat u wijzigingen hoeft aan te brengen met behulp van de sleutel van een persoonlijke-eindpunt bron, krijgt u een verificatie fout (401).

      Om het werk te laten werken, wijzigt u hoe u het exemplaar `SpeechConfig` van de klasse maakt en gebruikt u ' vanaf eind punt '/' met eind punt-initialisatie. Stel dat we de volgende twee variabelen hebben gedefinieerd:
      - `subscriptionKey` bevat de sleutel van de spraak resource met het persoonlijke eind punt ingeschakeld.
      - `endPoint` bevat de volledige *gewijzigde* eind punt-URL (met het type dat is vereist voor de bijbehorende programmeer taal). In ons voor beeld moet deze variabele het volgende bevatten:
        ```
        wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
        ```

      Een `SpeechConfig` instantie maken:
      ```csharp
      var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
      ```
      ```cpp
      auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
      ```
      ```java
      SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
      ```
      ```python
      import azure.cognitiveservices.speech as speechsdk
      speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
      ```
      ```objectivec
      SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
      ```

> [!TIP]
> De query parameters die zijn opgegeven in de eindpunt-URI worden niet gewijzigd, zelfs niet als ze zijn ingesteld door andere Api's. Als de herkennings taal bijvoorbeeld is gedefinieerd in de URI als query parameter `language=en-US` en ook is ingesteld op `ru-RU` via de bijbehorende eigenschap, wordt de taal instelling in de URI gebruikt. De juiste taal is dan `en-US` .
>
> De para meters die in de URI van het eind punt zijn ingesteld, hebben altijd prioriteit. Andere Api's kunnen alleen para meters overschrijven die niet zijn opgegeven in de URI van het eind punt.

Na deze wijziging moet uw toepassing samen werken met de spraak bronnen met het persoonlijke eind punt ingeschakeld. We werken aan een naadloze ondersteuning van scenario's voor persoonlijke eind punten.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints"></a>Een spraak bron gebruiken met een aangepaste domein naam en zonder persoonlijke eind punten

In dit artikel hebben we verschillende keren geduurd dat het inschakelen van een aangepast domein voor een spraak resource *onomkeerbaar* is. Een dergelijke resource gebruikt een andere manier om te communiceren met spraak Services, vergeleken met de bronnen die gebruikmaken van [regionale eindpunt namen](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

In deze sectie wordt uitgelegd hoe u een spraak bron gebruikt met een ingeschakelde aangepaste domein naam, maar *zonder* persoonlijke eind punten met de speech Services rest-Api's en [spraak-SDK](speech-sdk.md). Dit kan een resource zijn die eenmaal is gebruikt in een scenario met een persoonlijk eind punt, maar vervolgens de persoonlijke eind punten had verwijderd.

#### <a name="dns-configuration"></a>DNS-configuratie

Onthoud hoe een aangepast domein-DNS-naam van de spraak bron met persoonlijke eind punten wordt [omgezet vanuit open bare netwerken](#resolve-dns-from-other-networks). In dit geval wijst het IP-adres dat wordt omgezet naar een proxy-eind punt voor een virtueel netwerk. Dit eind punt wordt gebruikt voor het verzenden van het netwerk verkeer naar de Cognitive Services resource met het persoonlijke eind punt ingeschakeld.

Als *alle* persoonlijke eind punten van een resource echter worden verwijderd (of direct na het inschakelen van de aangepaste domein naam), wordt de CNAME-record van de spraak bron opnieuw ingericht. Het wijst nu naar het IP-adres van het bijbehorende [Cognitive Services regionale eind punt](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

De uitvoer van de `nslookup` opdracht ziet er als volgt uit:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
Vergelijk het met de uitvoer van [deze sectie](#resolve-dns-from-other-networks).

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>Spraak bron met een aangepaste domein naam en zonder persoonlijke eind punten: gebruik met de REST-Api's

##### <a name="speech-to-text-rest-api-v30"></a>Spraak-naar-tekst REST API v 3.0

Het gebruik van spraak-naar-tekst REST API v 3.0 is volledig gelijk aan het geval van [spraak bronnen met persoonlijke eind punten](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Spraak-naar-tekst REST API voor korte audio en tekst naar spraak REST API

In dit geval is het gebruik van de spraak-naar-tekst REST API voor korte audio en het gebruik van de tekst-naar-spraak-REST API geen verschillen van het algemene geval, met één uitzonde ring. (Zie de volgende opmerking.) U moet beide Api's gebruiken zoals beschreven in de [rest API voor spraak naar tekst voor korte audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) en [tekst naar spraak rest API](rest-text-to-speech.md) documentatie.

> [!NOTE]
> Wanneer u de spraak-naar-tekst REST API gebruikt voor korte audio-en tekst-naar-spraak-REST API in aangepaste domein scenario's, gebruikt u een abonnements sleutel die wordt door gegeven via de `Ocp-Apim-Subscription-Key` koptekst. (Zie de Details voor [spraak naar tekst rest API voor korte audio](rest-speech-to-text.md#request-headers) en [tekst naar spraak rest API](rest-text-to-speech.md#request-headers))
>
> Het gebruik van een autorisatie token en het door geven aan het speciale eind punt via de `Authorization` header werkt *alleen* als u de optie **alle netwerken** hebt ingeschakeld in de sectie **netwerken** van uw spraak bron. In andere gevallen krijgt u een `Forbidden` `BadRequest` fout melding wanneer u probeert een autorisatie token te verkrijgen.

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>Spraak bron met een aangepaste domein naam en zonder persoonlijke eind punten: gebruik met de spraak-SDK

Het gebruik van de Speech SDK met aangepaste, door het domein ingeschakelde spraak bronnen *zonder* persoonlijke eind punten is gelijk aan het algemene geval zoals beschreven in de [Speech SDK-documentatie](speech-sdk.md).

Als u uw code hebt gewijzigd voor het gebruik van een [spraak bron met privé-eind punt](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk), moet u rekening houden met het volgende.

In het gedeelte over [spraak bronnen met een persoonlijk eind punt](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)is een uitleg over het bepalen van de eind punt-URL, het wijzigen ervan en het maken van een eind punt voor de initialisatie van het `SpeechConfig` klasse-exemplaar.

Als u echter dezelfde toepassing probeert uit te voeren nadat alle persoonlijke eind punten zijn verwijderd (waardoor enige tijd voor het opnieuw inrichten van de bijbehorende DNS-record is toegestaan), krijgt u een interne service fout (404). De [DNS-record](#dns-configuration) verwijst nu naar het regionale Cognitive Services-eind punt in plaats van de virtuele netwerk proxy en de URL-paden `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` worden daar niet gevonden.

U moet uw toepassing terugdraaien naar de standaard instantie van `SpeechConfig` in de stijl van de volgende code:

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

## <a name="pricing"></a>Prijzen

Zie [prijzen van Azure Private Link](https://azure.microsoft.com/pricing/details/private-link) voor meer informatie over prijzen.

## <a name="learn-more"></a>Meer informatie

* [Azure Private Link](../../private-link/private-link-overview.md)
* [Speech-SDK](speech-sdk.md)
* [REST API voor spraak-naar-tekst](rest-speech-to-text.md)
* [REST API voor tekst-naar-spraak](rest-text-to-speech.md)