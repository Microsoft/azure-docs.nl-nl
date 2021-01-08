---
title: Privé-eind punten gebruiken met de spraak service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de spraak service met privé-eind punten van de persoonlijke Azure-koppeling
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: alexeyo
ms.openlocfilehash: d5822b6eeecfc61a5092519618ddfcaf88a625ae
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018527"
---
# <a name="use-speech-service-through-a-private-endpoint"></a>Speech Service gebruiken via een persoonlijk eind punt

Met de [persoonlijke Azure-koppeling](../../private-link/private-link-overview.md) kunt u verbinding maken met Services in azure met behulp van een [persoonlijk eind punt](../../private-link/private-endpoint-overview.md).
Een persoonlijk eind punt is een privé-IP-adres dat alleen toegankelijk is binnen een specifiek [virtueel netwerk](../../virtual-network/virtual-networks-overview.md) en subnet.

In dit artikel wordt uitgelegd hoe u persoonlijke koppelingen en privé-eind punten instelt en gebruikt met Azure cognitieve speech Services.

> [!NOTE]
> In dit artikel worden de specifieke informatie over het instellen en gebruiken van een persoonlijke koppeling met Azure cognitieve speech Services beschreven. Lees hoe u [virtuele netwerken gebruikt met Cognitive Services](../cognitive-services-virtual-networks.md)voordat u doorgaat.

Voer de volgende taken uit om een spraak service te gebruiken via een persoonlijk eind punt:

1. [Aangepaste domein naam voor de spraak bron maken](#create-a-custom-domain-name)
2. [Privé-eind punt (en) maken en configureren](#enable-private-endpoints)
3. [Bestaande toepassingen en oplossingen aanpassen](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)

Als u later persoonlijke eind punten wilt verwijderen, maar nog steeds de spraak resource wilt gebruiken, voert u de taken uit die in [deze sectie](#use-speech-resource-with-custom-domain-name-without-private-endpoints)worden gevonden.

## <a name="create-a-custom-domain-name"></a>Een aangepaste domein naam maken

Persoonlijke eind punten vereisen een [Cognitive Services aangepaste subdomeinnaam](../cognitive-services-custom-subdomains.md). Volg de onderstaande instructies om een voor uw spraak bron te maken.

> [!WARNING]
> Een spraak bron waarvoor een aangepaste domein naam is ingeschakeld, maakt gebruik van een andere manier om te communiceren met de spraak service.
> U moet waarschijnlijk uw toepassings code aanpassen voor zowel het [persoonlijke eind punt ingeschakeld](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) als scenario's waarvoor [ **geen** persoonlijke eind punten zijn ingeschakeld](#use-speech-resource-with-custom-domain-name-without-private-endpoints) .
>
> Wanneer u een aangepaste domein naam inschakelt, kan de bewerking [**niet onomkeerbaar**](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name)zijn. De enige manier om terug te gaan naar de [regionale naam](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) is het maken van een nieuwe spraak resource.
>
> Als uw spraak bron een groot aantal gekoppelde aangepaste modellen en projecten heeft die zijn gemaakt via [Speech Studio](https://speech.microsoft.com/) , wordt u **aangeraden de** configuratie met een test bron te proberen voordat u de resource die wordt gebruikt in de productie omgeving wijzigt.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Als u een aangepaste domein naam met Azure Portal wilt maken, voert u de volgende stappen uit:

1. Ga naar [Azure Portal](https://portal.azure.com/) en meld u aan bij uw Azure-account.
1. Selecteer de vereiste spraak resource.
1. Klik in de groep **resource beheer** in het navigatie deel venster links op **netwerken**.
1. Klik op het tabblad **firewalls en virtuele netwerken** op **aangepaste domein naam genereren**. Er wordt een nieuw rechts venster weer gegeven met instructies voor het maken van een uniek aangepast subdomein voor uw resource.
1. Voer in het deel venster aangepaste domein naam genereren een aangepast domein naam gedeelte in. Uw volledige aangepaste domein ziet er als volgt uit: `https://{your custom name}.cognitiveservices.azure.com` . 
    **Nadat u een aangepaste domein naam hebt gemaakt, _kan deze niet meer_ worden gewijzigd. Lees de bovenstaande waarschuwing opnieuw.** Nadat u de aangepaste domein naam hebt ingevoerd, klikt u op **Opslaan**.
1. Nadat de bewerking is voltooid, klikt u in de groep **resource beheer** op **sleutels en eind punt**. Bevestig dat de nieuwe eindpunt naam van de resource op deze manier begint:

    `https://{your custom name}.cognitiveservices.azure.com`

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een aangepaste domein naam met behulp van Power shell wilt maken, controleert u of uw computer Power shell versie 7. x of hoger met de Azure PowerShell module versie 5.1.0 of hoger heeft. Voer de volgende stappen uit om de versies van deze hulpprogram ma's weer te geven:

1. Typ in een Power shell-venster:

    `$PSVersionTable`

    Bevestig dat de waarde van PSVersion groter is dan 7. x. Volg de instructies bij het [installeren van verschillende versies van Power shell](/powershell/scripting/install/installing-powershell) om een upgrade uit te voeren voor Power shell.

1. Typ in een Power shell-venster:

    `Get-Module -ListAvailable Az`

    Als er niets wordt weer gegeven of als Azure PowerShell module versie lager is dan 5.1.0, volgt u de instructies op de [Azure PowerShell module installeren](/powershell/azure/install-Az-ps) om een upgrade uit te voeren.

Voordat u doorgaat, voert `Connect-AzAccount` u uit om een verbinding te maken met Azure.

## <a name="verify-custom-domain-name-is-available"></a>Controleer of de aangepaste domein naam beschikbaar is

Controleer of het aangepaste domein dat u wilt gebruiken, beschikbaar is. Volg deze stappen om te bevestigen dat het domein beschikbaar is via de bewerking [domein beschikbaarheid controleren](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) in het Cognitive Services rest API.

> [!TIP]
> De onderstaande code werkt **niet** in azure Cloud shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare OAuth token to use in request to Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to Cognitive Services REST API.
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
Als de naam al wordt gebruikt, wordt het volgende antwoord weer gegeven:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>Uw aangepaste domein naam maken

We gebruiken de cmdlet [set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) om de aangepaste domein naam voor de geselecteerde spraak resource in te scha kelen.

> [!WARNING]
> Nadat de onderstaande code is uitgevoerd, maakt u een aangepaste domein naam voor uw spraak resource.
> Deze naam **kan niet** worden gewijzigd. Zie de **waarschuwing** hierboven voor meer informatie.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Deze sectie vereist de nieuwste versie van de Azure CLI. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="verify-the-custom-domain-name-is-available"></a>Controleer of de aangepaste domein naam beschikbaar is

Controleer of het aangepaste domein dat u wilt gebruiken, gratis is. We gebruiken de [Beschik baarheid](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) van de domein-methode controleren van Cognitive Services rest API.

Kopieer het onderstaande code blok, plaats de aangepaste domein naam van uw voor keur en sla deze op in het bestand `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Kopieer het bestand naar de huidige map of upload het naar Azure Cloud Shell en voer de volgende opdracht uit. (Vervang door `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` de id van uw Azure-abonnement).

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

Als de naam al wordt gebruikt, wordt het volgende antwoord weer gegeven:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enable-custom-domain-name"></a>Aangepaste domein naam inschakelen

Als u een aangepaste domein naam wilt inschakelen voor de geselecteerde spraak bron, gebruikt u de opdracht [AZ cognitiveservices account update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) .

Selecteer het Azure-abonnement met de spraak resource. Als uw Azure-account slechts één actief abonnement heeft, kunt u deze stap overs Laan. (Vervang door `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` de id van uw Azure-abonnement).
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Stel de aangepaste domein naam in op de geselecteerde resource. Vervang de waarden van de voor beeld-para meters door de feitelijke items en voer de onderstaande opdracht uit.

> [!WARNING]
> Nadat u de opdracht hieronder hebt uitgevoerd, maakt u een aangepaste domein naam voor uw spraak bron. Deze naam **kan niet** worden gewijzigd. Meer informatie vindt u in de waarschuwing waarschuwings bericht hierboven.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

**_

## <a name="enable-private-endpoints"></a>Privé-eind punten inschakelen

U kunt het beste de [privé-DNS-zone](../../dns/private-dns-overview.md) die aan het virtuele netwerk is gekoppeld, gebruiken met de vereiste updates voor de persoonlijke eind punten die tijdens het inrichtings proces standaard worden gemaakt. Als u echter uw eigen DNS-server gebruikt, moet u mogelijk ook uw DNS-configuratie wijzigen, zoals wordt weer gegeven in _DNS voor persoonlijke eind punten_. Beslis of u een persoonlijke eind punt (en) wilt gebruiken voor de DNS-*strategie _ voor* een inrichtings-en test-of-omgeving voor een productie-spraak bron, met name als u uw eigen DNS-server gebruikt.

Gebruik een van de volgende artikelen om een persoonlijk eind punt (en) te maken. In de artikelen wordt een web-app als voorbeeld bron gebruikt om persoonlijke eind punten in te scha kelen. U gebruikt deze para meters in plaats van die in het artikel:

| Instelling             | Waarde                                    |
|---------------------|------------------------------------------|
| Resourcetype       | **Micro soft. CognitiveServices/accounts** |
| Resource            | **\<your-speech-resource-name>**         |
| Stel subresource in | **organisatieaccount**                              |

- [Een privé-eindpunt maken met behulp van de Azure-portal](../../private-link/create-private-endpoint-portal.md)
- [Een persoonlijk eind punt maken met Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Een persoonlijk eind punt maken met behulp van Azure CLI](../../private-link/create-private-endpoint-cli.md)

**DNS voor privé-eind punten:** Bekijk de algemene principes van [DNS voor privé-eind punten in cognitive services-resources](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Controleer vervolgens of de DNS-configuratie correct werkt door de volgende controles uit te voeren:

### <a name="resolve-dns-from-the-virtual-network"></a>DNS omzetten vanuit het virtuele netwerk

Deze controle is **vereist**.

Volg deze stappen om de aangepaste DNS-vermelding te testen vanuit uw virtuele netwerk.

1. Meld u aan bij een virtuele machine die zich bevindt in het virtuele netwerk waaraan u het persoonlijke eind punt hebt gekoppeld. 
1. Open Windows-opdracht prompt of bash-shell, voer uit `nslookup` en bevestig dat de aangepaste domein naam van de resource is omgezet.

```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  168.63.129.16

Non-authoritative answer:
Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
Address:  172.28.0.10
Aliases:  my-private-link-speech.cognitiveservices.azure.com
```

3. Controleer of het IP-adres overeenkomt met het IP-adres van uw persoonlijke eind punt.

### <a name="resolve-dns-from-other-networks"></a>DNS van andere netwerken oplossen

Voer deze controle alleen uit als u van plan bent om uw persoonlijke eind punt met spraak bronnen in hybride modus te gebruiken, waarbij u **alle netwerken** of **geselecteerde netwerken en** de optie toegang tot privé-eind punten hebt ingeschakeld in de sectie **netwerken** van uw bron. Als u van plan bent om de resource te openen door alleen een persoonlijk eind punt te gebruiken, kunt u deze sectie overs Laan.

1. Meld u aan bij een computer die is verbonden met een netwerk dat toegang heeft tot de bron.
2. Open Windows-opdracht prompt of bash-shell, voer uit `nslookup` en bevestig dat de aangepaste domein naam van de resource is omgezet.

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

3. Controleer of het IP-adres overeenkomt met het IP-adres van uw persoonlijke eind punt.

> [!NOTE]
> Het omgezette IP-adres wijst naar een eind punt van een virtuele netwerk proxy, waarmee het netwerk verkeer wordt verzonden naar het persoonlijke eind punt voor de Cognitive Services bron. Het gedrag verschilt voor een resource met een aangepaste domein naam, maar *zonder* persoonlijke eind punten. Zie [deze sectie](#dns-configuration) voor meer informatie.

## <a name="adjust-existing-applications-and-solutions"></a>Bestaande toepassingen en oplossingen aanpassen

Een spraak bron waarvoor een aangepast domein is ingeschakeld, maakt gebruik van een andere manier om te communiceren met spraak Services. Dit geldt voor een aangepast, voor het domein ingeschakelde spraak resource met en zonder persoonlijke eind punten. De informatie in deze sectie is van toepassing op beide scenario's.

### <a name="use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled"></a>Een spraak bron gebruiken waarvoor een aangepaste domein naam en een persoonlijk eind punt zijn ingeschakeld

Een spraak bron waarvoor een aangepaste domein naam en een persoonlijk eind punt is ingeschakeld, gebruikt een andere manier om te communiceren met spraak Services. In deze sectie wordt uitgelegd hoe u deze resource kunt gebruiken met spraak Services REST API en [spraak-SDK](speech-sdk.md).

> [!NOTE]
> Houd er rekening mee dat een spraak bron zonder privé-eind punten, maar waarvoor een **aangepaste domein naam** is ingeschakeld, ook een speciale manier heeft om te communiceren met spraak Services, maar deze methode wijkt af van het scenario van een op persoonlijke eind punt ingeschakelde spraak resource. Als u een dergelijke resource hebt (Stel dat u een resource hebt met privé-eind punten, maar vervolgens besluit om deze te verwijderen), moet u vertrouwd raken met de [sectie correspondent](#use-speech-resource-with-custom-domain-name-without-private-endpoints).

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-rest-api"></a>Spraak bron met aangepaste domein naam en persoonlijk eind punt. Gebruik met REST API

We gebruiken `my-private-link-speech.cognitiveservices.azure.com` als een voor beeld van een DNS-naam voor een spraak bron (aangepast domein) voor deze sectie.

##### <a name="note-on-speech-services-rest-api"></a>Opmerking over speech Services REST API

Spraak Services heeft REST API voor [spraak naar tekst](rest-speech-to-text.md) en [tekst naar spraak](rest-text-to-speech.md). Het volgende moet worden overwogen voor het scenario met het persoonlijke eind punt dat is ingeschakeld.

Spraak-naar-tekst heeft twee verschillende REST-Api's. Elke API heeft een ander doel, maakt gebruik van verschillende eind punten en vereist een andere benadering wanneer een combi natie van een persoonlijk eind punt is ingeschakeld.

De REST-to-text-Api's zijn:
- [Spraak-naar-tekst rest API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) wordt gebruikt voor [batch-transcriptie](batch-transcription.md) en [Custom speech](custom-speech-overview.md). v 3.0 is een [opvolger van v 2.0](/azure/cognitive-services/speech-service/migrate-v2-to-v3).
- [Spraak-naar-tekst rest API voor korte audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) wordt gebruikt voor online-transcriptie. 

Het gebruik van spraak-naar-tekst REST API voor korte audio-en tekst-naar-spraak-REST API in het scenario met het persoonlijke eind punt is dezelfde en gelijkwaardig aan de [Speech SDK-situatie](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) die verderop in dit artikel wordt beschreven. 

Spraak-naar-tekst REST API v 3.0 gebruikt een andere set eind punten en vereist daarom een andere benadering voor het scenario met het persoonlijke eind punt dat is ingeschakeld.

Beide gevallen worden beschreven in de volgende subsecties.


##### <a name="speech-to-text-rest-api-v30"></a>Spraak-naar-tekst REST API v 3.0

Doorgaans gebruiken spraak resources [Cognitive Services regionale eind punten](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) voor communicatie met de [spraak-naar-tekst rest API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Deze resources hebben de volgende naamgevings indeling: <p/>`{region}.api.cognitive.microsoft.com`

Dit is een voor beeld van een aanvraag-URL:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
Na het inschakelen van een aangepast domein voor een spraak bron (die nodig is voor persoonlijke eind punten), gebruikt deze resource het volgende DNS-naam patroon voor het basis REST API-eind punt: <p/>`{your custom name}.cognitiveservices.azure.com`

Dit betekent dat in ons voor beeld de naam van het REST API-eind punt is: <p/>`my-private-link-speech.cognitiveservices.azure.com`

En de bovenstaande voorbeeld aanvraag-URL moet worden geconverteerd naar:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Deze URL moet bereikbaar zijn vanuit het virtuele netwerk waaraan het persoonlijke eind punt is gekoppeld (met de [juiste DNS-omzetting](#resolve-dns-from-the virtual-network)).

Normaal gesp roken moet u, nadat u een aangepaste domein naam voor een spraak bron hebt ingeschakeld, de hostnaam in alle aanvraag-Url's vervangen door de nieuwe hostnaam van het aangepaste domein. Alle andere onderdelen van de aanvraag (zoals het pad `/speechtotext/v3.0/transcriptions` in bovenstaand voor beeld) blijven hetzelfde.

> [!TIP]
> Sommige klanten hebben toepassingen ontwikkeld die gebruikmaken van het gebied deel van de regionale endpoint-DNS-naam (bijvoorbeeld om de aanvraag te verzenden naar de spraak bron die in de desbetreffende Azure-regio is geïmplementeerd).
>
> De aangepaste domein naam van de spraak resource bevat **geen** informatie over de regio waarin de resource is geïmplementeerd. De hierboven beschreven toepassings logica werkt dus **niet** en moet worden gewijzigd.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Spraak-naar-tekst REST API voor korte audio en tekst naar spraak REST API

[Spraak-naar-tekst rest API voor korte audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) en [tekst naar spraak rest API](rest-text-to-speech.md) twee typen eind punten gebruiken:
- [Cognitive Services regionale eind punten](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) voor de communicatie met de Cognitive Services rest API voor het verkrijgen van een autorisatie token
- Speciale eind punten voor alle andere bewerkingen

De gedetailleerde beschrijving van de speciale eind punten en hoe hun URL moet worden getransformeerd voor een privé-eind punt waarvoor een spraak resource is ingeschakeld, wordt in [Deze subsectie van het](#general-principle) gedeelte gebruik met Speech SDK beschreven. Hetzelfde principe dat wordt beschreven voor SDK, is van toepassing op de REST API voor spraak naar tekst v 1.0 en tekst naar spraak.

Raadpleeg het volgende voor beeld om vertrouwd te raken met het materiaal in de Subsectie in de vorige alinea. (In het voor beeld wordt tekst naar spraak REST API beschreven; het gebruik van spraak naar tekst REST API voor korte audio is volledig gelijkwaardig)

> [!NOTE]
> Wanneer u **spraak-naar-tekst rest API gebruikt voor korte audio** in scenario's met een persoonlijk eind punt, gebruikt u een autorisatie token dat is [door gegeven via](rest-speech-to-text.md#request-headers) de `Authorization` [header](rest-speech-to-text.md#request-headers). Het is niet mogelijk om de code van het spraak abonnement door te geven aan het speciale eind punt via de `Ocp-Apim-Subscription-Key` header en genereert fout 401. 

**Voor beeld van het gebruik van tekst naar spraak REST API.**

We gebruiken Europa-west als een voor beeld van een Azure `my-private-link-speech.cognitiveservices.azure.com` -regio en als een voor beeld van een DNS-naam voor een spraak bron (aangepast domein). De aangepaste domein naam `my-private-link-speech.cognitiveservices.azure.com` in ons voor beeld maakt deel uit van de spraak resource die in Europa-West regio is gemaakt.

Voor een lijst met de stemmen die in de regio worden ondersteund, moet de volgende twee bewerkingen worden uitgevoerd:

- Autorisatie token verkrijgen:
```http
https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
```
- Gebruik het token om de lijst met stemmen op te halen:
```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
(Zie voor meer informatie over de bovenstaande stappen in [tekst naar spraak rest API documentatie](rest-text-to-speech.md))

Voor de spraak bron voor het persoonlijke eind punt is ingeschakeld, moeten de eindpunt-Url's voor dezelfde bewerkings reeks worden gewijzigd. Dezelfde volg orde ziet er als volgt uit:
- Verificatie token verkrijgen via
```http
https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
```
(Zie gedetailleerde uitleg in Subsectie [voor spraak naar tekst rest API v 3.0](#speech-to-text-rest-api-v30) hierboven)
- Het verkregen token gebruiken Haal de lijst met stemmen op via
```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
(Zie de sectie gedetailleerde uitleg in de Subsectie [algemene principes](#general-principle) van het gedeelte gebruik met spraak-SDK)

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk"></a>Spraak bron met aangepaste domein naam en persoonlijk eind punt. Gebruik met Speech SDK

Voor het gebruik van Speech SDK met de aangepaste domein naam en het persoonlijke eind punt ingeschakelde spraak bronnen moeten de controle en waarschijnlijke wijzigingen in de toepassings code worden gecontroleerd.

We gebruiken `my-private-link-speech.cognitiveservices.azure.com` als een voor beeld van een DNS-naam voor een spraak bron (aangepast domein) voor deze sectie.

##### <a name="general-principle"></a>Algemeen principe

Normaal gesp roken worden in SDK-scenario's (en in de tekst naar spraak REST API scenario's) spraak bronnen gebruikmaken van de toegewezen regionale eind punten voor verschillende service aanbiedingen. De indeling van de DNS-naam voor deze eind punten is: </p>`{region}.{speech service offering}.speech.microsoft.com`

Voorbeeld: </p>`westeurope.stt.speech.microsoft.com`

Alle mogelijke waarden voor de regio (het eerste element van de DNS-naam) worden [hier](regions.md) vermeld. deze tabel bevat de mogelijke waarde voor de speech services-aanbieding (tweede element van de DNS-naam):

| DNS-naam waarde | Aanbieding met spraak Services                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Aangepaste opdrachten](custom-commands.md)                       |
| `convai`       | [Gesprekstranscriptie](conversation-transcription.md) |
| `s2s`          | [Speech Translation](speech-translation.md)                 |
| `stt`          | [Spraak naar tekst](speech-to-text.md)                         |
| `tts`          | [Tekst-naar-spraak](text-to-speech.md)                         |
| `voice`        | [Aangepaste stem](how-to-custom-voice.md)                      |

Het bovenstaande voor beeld ( `westeurope.stt.speech.microsoft.com` ) staat voor het spraak-naar-tekst-eind punt in Europa-West.

Eind punten met een persoonlijk eind punt communiceren met spraak Services via een speciale proxy en daarom **moet u de url's van de eindpunt verbinding wijzigen**. 

Een ' standaard ' eind punt-URL ziet er als volgt uit: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

De URL van een privé-eind punt ziet er als volgt uit: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Voor beeld 1.** De toepassing communiceert met behulp van de volgende URL (spraak herkenning met het basis model voor Engels (Verenigde Staten) in Europa-west):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Als u de aangepaste domein naam van de spraak resource wilt gebruiken in het persoonlijke eind punt, `my-private-link-speech.cognitiveservices.azure.com` moet u de URL als volgt wijzigen:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Let op de details:

- `westeurope.stt.speech.microsoft.com`De hostnaam wordt vervangen door de hostnaam van het aangepaste domein `my-private-link-speech.cognitiveservices.azure.com` .
- Het tweede element van de oorspronkelijke DNS-naam ( `stt` ) wordt het eerste element van het URL-pad en gaat vooraf aan het oorspronkelijke pad. De oorspronkelijke URL `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` wordt dus `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**Voor beeld 2.** De toepassing gebruikt de volgende URL om spraak te maken in Europa-west met behulp van een aangepast spraak model):
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Hieronder volgt een equivalente URL die gebruikmaakt van een persoonlijk eind punt dat is ingeschakeld, waarbij de aangepaste domein naam van de spraak bron `my-private-link-speech.cognitiveservices.azure.com` :

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Hetzelfde principe als in voor beeld 1 wordt toegepast, maar het sleutel element is deze tijd `voice` .

##### <a name="modify-applications"></a>Toepassingen wijzigen

Volg deze stappen om uw code te wijzigen:

**1. de URL van het toepassings eindpunt bepalen**

- [Schakel logboek registratie in voor uw toepassing](how-to-use-logging.md) en voer deze uit om activiteiten te registreren.
- Zoek in het logboek bestand naar `SPEECH-ConnectionUrl` . In overeenkomende regels bevat de `value` para meter de volledige URL van uw toepassing die wordt gebruikt om de spraak service te bereiken.

Voorbeeld:

```
(114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
```

De URL die door de toepassing wordt gebruikt in dit voor beeld is:

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

**2. `SpeechConfig` instantie maken met volledige eind punt-URL**

Wijzig het eind punt dat u in de vorige sectie hebt bepaald, zoals beschreven in het bovenstaande [algemene principe](#general-principle) .

Wijzig nu hoe u het exemplaar van maakt `SpeechConfig` . Waarschijnlijk gebruikt de toepassing van vandaag een soort gelijke manier:
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
Dit werkt niet voor spraak bronnen met een privé-eind punt vanwege de wijzigingen in hostnaam en URL die we in de vorige secties hebben beschreven. Als u de bestaande toepassing probeert uit te voeren zonder dat u wijzigingen aanbrengt met de sleutel van een persoonlijke eindpunt resource, ontvangt u een verificatie fout (401).

Om het werk te laten werken, wijzigt u hoe u een `SpeechConfig` klasse maakt en gebruikt u ' vanaf eind punt '/' met eind punt-initialisatie. Stel dat we de volgende twee variabelen hebben gedefinieerd:
- `subscriptionKey` met de sleutel van het persoonlijke eind punt ingeschakelde spraak resource
- `endPoint` met de volledige **gewijzigde** eind punt-URL (met het type dat is vereist voor de correspondent programmeer taal). In ons voor beeld moet deze variabele bevatten
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Maak vervolgens een `SpeechConfig` instantie:
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
> De query parameters die zijn opgegeven in de eindpunt-URI worden niet gewijzigd, zelfs niet als ze zijn ingesteld door andere Api's. Als de herkennings taal bijvoorbeeld is gedefinieerd in de URI als query parameter ' taal = en-US ', en ook is ingesteld op ' ru-RU ' via de eigenschap correspondent, wordt de taal instelling in de URI gebruikt en is de juiste taal ' en-US '. De para meters die in de URI van het eind punt zijn ingesteld, nemen altijd precidence. Alleen para meters die niet zijn opgegeven in de URI van het eind punt kunnen worden overschreven door andere Api's.

Na deze wijziging moet uw toepassing samen werken met de persoonlijke ingeschakelde spraak bronnen. We werken aan meer naadloze ondersteuning van het scenario voor een persoonlijk eind punt.

### <a name="use-speech-resource-with-custom-domain-name-without-private-endpoints"></a>Een spraak bron gebruiken met een aangepaste domein naam zonder persoonlijke eind punten

In dit artikel hebben we verschillende keren geduurd, waardoor het inschakelen van een aangepast domein voor een spraak resource **onomkeerbaar** is en een andere manier wordt gebruikt om te communiceren met spraak Services, vergeleken met de ' gebruikelijke ' items (dat wil zeggen, die gebruikmaken van [regionale namen van eind punten](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)).

In deze sectie wordt uitgelegd hoe u een spraak bron kunt gebruiken waarvoor een aangepaste domein naam is ingeschakeld, maar **zonder** persoonlijke eind punten met spraak Services rest API en [spraak-SDK](speech-sdk.md). Dit kan een resource zijn die eenmaal is gebruikt in een scenario met een privé-eind punt, maar er zijn een of meer persoonlijke eind punten verwijderd.

#### <a name="dns-configuration"></a>DNS-configuratie

Onthoud hoe een aangepaste DNS-naam van het persoonlijke eind punt dat spraak resource heeft ingeschakeld, wordt [omgezet vanuit open bare netwerken](#resolve-dns-from-other-networks). In dit geval worden IP-adressen omgezet naar een VNet-proxy-eind punt, dat wordt gebruikt voor het verzenden van het netwerk verkeer naar het persoonlijke eind punt dat is ingeschakeld Cognitive Services bron.

Wanneer **alle** persoonlijke eind punten van een resource echter worden verwijderd (of direct nadat de aangepaste domein naam is ingeschakeld), wordt de CNAME-record van de spraak bron opnieuw ingericht en wordt het IP-adres van de correspondent [Cognitive Services regionaal eind punt](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

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

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-rest-api"></a>Een spraak bron met een aangepaste domein naam zonder persoonlijke eind punten. Gebruik met REST API

##### <a name="speech-to-text-rest-api-v30"></a>Spraak-naar-tekst REST API v 3.0

Het gebruik van spraak-naar-tekst REST API v 3.0 is volledig gelijk aan het geval van [spraak bronnen die zijn ingeschakeld voor privé-eind punten](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>Spraak-naar-tekst REST API voor korte audio en tekst naar spraak REST API

In dit geval worden spraak-naar-tekst REST API voor korte audio-en tekst-naar-spraak-REST API gebruik geen verschillen met het algemene geval met één uitzonde ring voor spraak-naar-tekst REST API voor korte audio (zie opmerking hieronder). Beide Api's moeten worden gebruikt, zoals beschreven in [spraak naar tekst rest API voor korte audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) en [tekst naar spraak rest API](rest-text-to-speech.md) documentatie.

> [!NOTE]
> Gebruik bij het gebruik van **spraak-naar-tekst rest API voor korte audio** in scenario's met aangepaste domeinen een autorisatie token dat is [door gegeven via](rest-speech-to-text.md#request-headers) een `Authorization` [header](rest-speech-to-text.md#request-headers). Het is niet mogelijk om de code van het spraak abonnement door te geven aan het speciale eind punt via de `Ocp-Apim-Subscription-Key` header en genereert fout 401. 

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-speech-sdk"></a>Een spraak bron met een aangepaste domein naam zonder persoonlijke eind punten. Gebruik met Speech SDK

Voor het gebruik van Speech SDK met aangepaste domein naam ingeschakelde spraak bronnen **zonder** persoonlijke eind punten zijn de controle en waarschijnlijke wijzigingen van de toepassings code vereist. Houd er rekening mee dat deze wijzigingen **verschillen** met het geval van een spraak resource die is ingeschakeld voor een [persoonlijk eind punt](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk). We werken aan een naadloze ondersteuning van het scenario voor persoonlijk eind punt/aangepast domein.

We gebruiken `my-private-link-speech.cognitiveservices.azure.com` als een voor beeld van een DNS-naam voor een spraak bron (aangepast domein) voor deze sectie.

In het gedeelte over het [persoonlijke eind punt ingeschakelde spraak resource](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) wordt uitgelegd hoe u de eind punt-URL kunt bepalen, wijzigt u deze en maakt u deze via ' van eind punt '/' met eind punt ' initialisatie van het `SpeechConfig` klasse-exemplaar.

Als u echter dezelfde toepassing probeert uit te voeren nadat alle persoonlijke eind punten zijn verwijderd (waardoor enige tijd aan het opnieuw inrichten van de correspondent DNS-record is toegestaan), ontvangt u een interne service fout (404). De reden hiervoor is dat de [DNS-record](#dns-configuration) die nu verwijst naar het regionale Cognitive Services-eind punt in plaats van de VNet-proxy, en dat de URL-paden `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` daar niet worden gevonden, dus de fout melding is niet gevonden (404).

Als u de toepassing terugdraait naar de ' standaard ', instantiëring `SpeechConfig` in de stijl van
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
uw toepassing wordt beëindigd met de volgende verificatie fout (401).

##### <a name="modifying-applications"></a>Toepassingen wijzigen

Voer de volgende stappen uit om ervoor te zorgen dat uw toepassing een spraak bron gebruikt met een aangepaste domein naam en zonder persoonlijke eind punten:

**1. aanvraag autorisatie token van de Cognitive Services REST API**

In [dit artikel](../authentication.md#authenticate-with-an-authentication-token) wordt uitgelegd hoe u het token kunt ophalen met behulp van de Cognitive Services rest API.

Gebruik uw aangepaste domein naam in de eind punt-URL, die in ons voor beeld deze URL is:
```http
https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
```
> [!TIP]
> U kunt deze URL vinden in Azure Portal. Selecteer op de pagina met de spraak bronnen onder de groep **resource beheer** de optie **sleutels en eind punt**.

**2. Maak een `SpeechConfig` instantie met behulp van de methode ' van autorisatie token '/' met autorisatie token '.**

Maak een `SpeechConfig` exemplaar met behulp van het autorisatie token dat u in de vorige sectie hebt verkregen. Stel dat de volgende variabelen zijn gedefinieerd:

- `token`: het autorisatie token dat is verkregen in de vorige sectie
- `azureRegion`: de naam van de resource [regio](regions.md) voor spraak (bijvoorbeeld: `westeurope` )
- `outError`: (alleen voor de [doel stelling C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) )

Maak vervolgens een `SpeechConfig` instantie:

```csharp
var config = SpeechConfig.FromAuthorizationToken(token, azureRegion);
```
```cpp
auto config = SpeechConfig::FromAuthorizationToken(token, azureRegion);
```
```java
SpeechConfig config = SpeechConfig.fromAuthorizationToken(token, azureRegion);
```
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(auth_token=token, region=azureRegion)
```
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithAuthorizationToken:token region:azureRegion error:outError];
```
> [!NOTE]
> De aanroeper moet ervoor zorgen dat het autorisatie token geldig is.
> Voordat het autorisatie token verloopt, moet de aanroeper het vernieuwen door deze Setter aan te roepen met een nieuwe geldige token.
> Als configuratie waarden worden gekopieerd bij het maken van een nieuwe Recognizer of synthesizer, is de nieuwe token waarde niet van toepassing op recognizers of synthesizers die al zijn gemaakt.
> Stel hiervoor het autorisatie token van de bijbehorende Recognizer of synthesizer in om het token te vernieuwen.
> Als u het token niet vernieuwt, zal de herkenner of synthesizer tijdens het werken fouten tegen komen.

Na deze wijziging moet uw toepassing werken met spraak bronnen die gebruikmaken van een aangepaste domein naam zonder persoonlijke eind punten.

## <a name="pricing"></a>Prijzen

Zie [prijzen van Azure Private Link](https://azure.microsoft.com/pricing/details/private-link) voor meer informatie over prijzen.

## <a name="learn-more"></a>Meer informatie

* [Azure Private Link](../../private-link/private-link-overview.md)
* [Speech-SDK](speech-sdk.md)
* [REST API voor spraak-naar-tekst](rest-speech-to-text.md)
* [REST API voor tekst-naar-spraak](rest-text-to-speech.md)
