---
title: Ontwerpen en runtime-sleutels gebruiken-LUIS
description: Wanneer u LUIS voor het eerst gebruikt, hoeft u geen ontwerp sleutel te maken. Wanneer u de app wilt publiceren en vervolgens uw runtime-eind punt wilt gebruiken, moet u de runtime sleutel maken en toewijzen aan de app.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 168833ea0a451913f4ed019cba832a16207e0d9c
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988154"
---
# <a name="create-luis-resources"></a>LUIS-resources maken

De runtime bronnen voor het ontwerpen en opvragen van query's bieden authenticatie voor uw Language Understanding-app (LUIS) en het Voorspellings eindpunt.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUIS-resources

Met LUIS kunt u drie typen Azure-resources en één niet-Azure-resource maken:

|Resource|Doel|Cognitieve service `kind`|Cognitieve service `type`|
|--|--|--|--|
|Resource ontwerpen|Met kunt u uw toepassingen maken, beheren, trainen, testen en publiceren. [Maak een Luis-ontwerp bron](luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) als u Luis-apps programmatisch of vanuit de Luis-Portal wilt ontwerpen. U moet [uw Luis-account migreren](luis-migration-authoring.md#what-is-migration) voordat u uw Azure-ontwerp resources aan uw toepassing koppelt. U kunt machtigingen voor de bewerkings resource beheren door personen [de rol Inzender](#contributions-from-other-authors)toe te wijzen. <br><br> Er is één laag beschikbaar voor de LUIS-ontwerp Bron:<br> <ul> <li>**Gratis F0 authoring resource**, waarmee u 1.000.000 gratis bewerkings transacties en 1.000 gratis test eindpunt aanvragen voor het voor Speld kunt. |`LUIS.Authoring`|`Cognitive Services`|
|Voorspellingsresource| Nadat u uw LUIS-toepassing hebt gepubliceerd, gebruikt u de Voorspellings resource/sleutel om te vragen om een query voor voor spelling-eindpunt aanvragen. Maak een LUIS-Voorspellings bron voordat u de voor spellingen van de client-app voorspelde aanvragen 1.000 overschrijdt die zijn opgegeven door de auteur of de eerste bron. <br><br> Er zijn twee lagen beschikbaar voor de Voorspellings resource:<br><ul> <li> **Gratis F0-Voorspellings resource**, die maandelijks gratis Voorspellings eindpunt aanvragen 10.000 ontvangt.<br> <li> **Standaard S0-Voorspellings resource**, de betaalde laag. [Meer informatie over prijzen.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Resource voor starter/proef versie|Met kunt u uw toepassingen maken, beheren, trainen, testen en publiceren. Deze resource wordt standaard gemaakt als u de optie starter resource kiest wanneer u zich voor het eerst aanmeldt bij LUIS. De start sleutel wordt uiteindelijk afgeschaft. Alle LUIS-gebruikers moeten [hun accounts migreren](luis-migration-authoring.md#what-is-migration) en hun Luis-toepassingen koppelen aan een ontwerp bron. In tegens telling tot de ontwerp bron biedt deze resource u geen machtigingen voor toegangs beheer op basis van rollen. <br><br> Net als bij de bewerkings resource biedt de eerste resource u 1.000.000 gratis bewerkings transacties en zijn er Voorspellings eindpunt aanvragen van 1.000 gratis getest.|-|Geen Azure-resource.|
|[Cognitive Services bron sleutel voor de service](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Vraag Voorspellings eindpunt aanvragen die worden gedeeld met LUIS en andere ondersteunde cognitieve Services.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> LUIS biedt twee soorten resources (gratis laag): één voor het ontwerpen van trans acties en één voor Voorspellings transacties. Als u gratis quota voor Voorspellings transacties hebt, moet u ervoor zorgen dat u de F0 prediction-resource gebruikt, waarmee u maandelijks gratis trans acties van 10.000 kunt uitvoeren, en niet de bewerkings resource, waarmee u 1.000 Voorspellings transacties maandelijks ontvangt.

Wanneer het proces voor het maken van de Azure-resource is voltooid, [wijst u de resource](#assign-a-resource-to-an-app) toe aan de app in de Luis-Portal.

> [!important]
> U moet LUIS-apps schrijven in de [regio's](luis-reference-regions.md#publishing-regions) waar u wilt publiceren en query's uitvoeren.

## <a name="resource-ownership"></a>Eigendom van resources

Een Azure-resource, zoals een LUIS-resource, is het eigendom van het abonnement dat de resource bevat.

Als u het eigendom van een resource wilt wijzigen, kunt u een van de volgende acties uitvoeren:
* Het [eigendom](../../cost-management-billing/manage/billing-subscription-transfer.md) van het abonnement overdragen.
* Exporteer de LUIS-app als een bestand en importeer de app vervolgens op een ander abonnement. Exporteren is beschikbaar op de pagina **mijn apps** in de Luis-Portal.

## <a name="resource-limits"></a>Bronlimieten

### <a name="authoring-key-creation-limits"></a>Limieten voor het maken van sleutels ontwerpen

U kunt Maxi maal tien ontwerp sleutels per regio per abonnement maken. Publicatie regio's verschillen van het ontwerpen van regio's. Zorg ervoor dat u een app maakt in de ontwerp regio die overeenkomt met het publicatie gebied waar u uw client toepassing wilt plaatsen. Zie voor meer informatie over de manier waarop de ontwerp regio's aan publicatie regio's worden toegewezen, de [regio's ontwerpen en publiceren](luis-reference-regions.md). 

Zie [sleutel limieten](luis-limits.md#key-limits)voor meer informatie over sleutel limieten.

### <a name="errors-for-key-usage-limits"></a>Fouten met betrekking tot de limieten voor het sleutel gebruik

Gebruiks limieten zijn gebaseerd op de prijs categorie.

Als u het quotum voor transacties per seconde (TPS) overschrijdt, ontvangt u een HTTP 429-fout. Als u het quotum voor trans acties per maand (TPM) overschrijdt, ontvangt u een HTTP 403-fout.


### <a name="reset-an-authoring-key"></a>Een ontwerp sleutel opnieuw instellen

Voor [gemigreerde resource](luis-migration-authoring.md) -apps voor ontwerpen: als uw ontwerp sleutel is aangetast, stelt u de sleutel in de Azure Portal op de pagina **sleutels** voor de ontwerp bron in.

Voor apps die nog niet zijn gemigreerd: de sleutel wordt opnieuw ingesteld voor al uw apps in de LUIS-Portal. Als u uw apps via de ontwerp-Api's ontwerpt, moet u de waarde van `Ocp-Apim-Subscription-Key` in de nieuwe sleutel wijzigen.

### <a name="regenerate-an-azure-key"></a>Een Azure-sleutel opnieuw genereren

U kunt een Azure-sleutel opnieuw genereren op de pagina **sleutels** in de Azure Portal.


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Eigendom van apps, toegang en beveiliging

Een app wordt gedefinieerd door de Azure-resources die worden bepaald door het abonnement van de eigenaar.

U kunt uw LUIS-app verplaatsen. Gebruik de volgende bronnen om u te helpen met behulp van de Azure Portal of Azure CLI:

* [Een app verplaatsen tussen LUIS-ontwerp resources](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Een resource verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Een resource verplaatsen binnen hetzelfde abonnement of tussen abonnementen](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Bijdragen van andere auteurs

Voor [gemigreerde resource](luis-migration-authoring.md) -apps voor ontwerpen: u kunt _inzenders_ voor een bewerkings resource beheren in de Azure Portal met behulp van de pagina **toegangs beheer (IAM)** . Meer informatie [over het toevoegen van een gebruiker](luis-how-to-collaborate.md) met behulp van het e-mail adres van de samen werker en de rol Inzender.

Voor apps die nog niet zijn gemigreerd: u kunt alle mede werkers _beheren op de_ pagina werk **nemers beheren** in de Luis >-Portal.

### <a name="query-prediction-access-for-private-and-public-apps"></a>Query Voorspellings toegang voor persoonlijke en open bare apps

Voor persoonlijke apps is het uitvoeren van een query Voorspellings runtime beschikbaar voor eigen aren en inzenders. Voor open bare apps is runtime toegang beschikbaar voor gebruikers die hun eigen Azure [cognitieve service](../cognitive-services-apis-create-account.md) of [Luis](#create-resources-in-the-azure-portal) -runtime resource en de id van de open bare app hebben.

Er is momenteel geen catalogus met open bare apps.

### <a name="authoring-permissions-and-access"></a>Machtigingen en toegang ontwerpen
Toegang tot een app vanuit de [Luis](luis-reference-regions.md#luis-website) -portal of de [ontwerp-api's](https://go.microsoft.com/fwlink/?linkid=2092087) wordt bepaald door de Azure authoring-resource.

De eigenaar en alle mede werkers hebben toegang tot het schrijven van de app.

|Het ontwerpen van de toegang omvat:|Notities|
|--|--|
|Eindpunt sleutels toevoegen of verwijderen||
|Versie exporteren||
|Eindpunt logboeken exporteren||
|Versie importeren||
|App openbaar maken|Wanneer een app openbaar is, kan iedereen met een auteur of een eindpunt sleutel een query uitvoeren op de app.|
|Model wijzigen|
|Publiceren|
|Endpoint uitingen voor [actief leren](luis-how-to-review-endpoint-utterances.md) bekijken|
|Trainen|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Runtime-toegang voor voor Spellings eindpunt

Toegang voor het uitvoeren van query's op het Voorspellings eindpunt wordt bepaald door een instelling op de pagina **toepassings gegevens** in de sectie **beheren** .

|[Persoonlijk eind punt](#runtime-security-for-private-apps)|[Openbaar eindpunt](#runtime-security-for-public-apps)|
|:--|:--|
|Beschikbaar voor eigenaar en mede werkers|Beschikbaar voor eigenaar, mede werkers en anderen die de App-ID kent|

U kunt bepalen wie uw LUIS runtime-sleutel kan zien door deze aan te roepen in een server-naar-server-omgeving. Als u LUIS gebruikt vanuit een bot, is de verbinding tussen de bot en LUIS al veiliger. Als u het LUIS-eind punt rechtstreeks aanroept, moet u een API aan de server zijde maken (zoals een Azure- [functie](https://azure.microsoft.com/services/functions/)) met beheerde toegang (via iets zoals [Azure AD](https://azure.microsoft.com/services/active-directory/)). Wanneer de API aan de server zijde wordt aangeroepen en geverifieerd en autorisatie is geverifieerd, geeft u de aanroep door aan LUIS. Deze strategie voor komt niet dat man-in-the-middle-aanvallen. Maar het maakt niet uit uw sleutel en eind punt-URL van uw gebruikers, zodat u de toegang kunt bijhouden en u kunt vastleggen van eindpunt Reacties toevoegen (zoals [Application Insights](https://azure.microsoft.com/services/application-insights/)).

### <a name="runtime-security-for-private-apps"></a>Runtime-beveiliging voor persoonlijke apps

De runtime van een persoonlijke app is alleen beschikbaar voor de volgende sleutels:

|Sleutel en gebruiker|Uitleg|
|--|--|
|Bewerkings sleutel van eigenaar| Maxi maal 1.000 eindpunt treffers|
|Ontwerp sleutels voor samen werker/Inzender| Maxi maal 1.000 eindpunt treffers|
|Een sleutel die is toegewezen aan LUIS door een auteur of samen werker/bijdrager|Gebaseerd op de laag van het sleutel gebruik|

### <a name="runtime-security-for-public-apps"></a>Runtime-beveiliging voor open bare apps

Wanneer uw app als openbaar is geconfigureerd, kan elke geldige LUIS-ontwerp sleutel of LUIS-eindpunt sleutel _een_ query uitvoeren, mits de sleutel niet het volledige eindpunt quotum heeft gebruikt.

Een gebruiker die geen eigenaar of mede werker is, kan alleen toegang krijgen tot de runtime van een open bare app als de App-ID is opgegeven. LUIS heeft geen open bare markt of enige andere manier om gebruikers te zoeken naar een open bare app.

Een open bare app wordt in alle regio's gepubliceerd. Een gebruiker met een op regio gebaseerde LUIS-bron sleutel heeft dus toegang tot de app in welke regio is gekoppeld aan de resource sleutel.


### <a name="control-access-to-your-query-prediction-endpoint"></a>Toegang tot uw query Voorspellings eindpunt beheren

U kunt bepalen wie uw LUIS prediction runtime-eindpunt sleutel kan zien door deze aan te roepen in een server-naar-server-omgeving. Als u LUIS gebruikt vanuit een bot, is de verbinding tussen de bot en LUIS al veiliger. Als u het LUIS-eind punt rechtstreeks aanroept, moet u een API aan de server zijde maken (zoals een Azure- [functie](https://azure.microsoft.com/services/functions/)) met beheerde toegang (via iets zoals [Azure AD](https://azure.microsoft.com/services/active-directory/)). Wanneer de API aan de server zijde wordt aangeroepen en verificatie en autorisatie zijn geverifieerd, geeft u de aanroep door aan LUIS. Deze strategie voor komt niet dat man-in-the-middle-aanvallen. Maar het maakt niet uit uw gebruikers, zodat u de toegang kunt bijhouden, en u kunt de logboek registratie van eindpunt antwoorden toevoegen (zoals [Application Insights](https://azure.microsoft.com/services/application-insights/)).

<a name="starter-key"></a>

## <a name="sign-in-to-the-luis-portal-and-begin-authoring"></a>Meld u aan bij de LUIS-Portal en begin met ontwerpen

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai) en ga akkoord met de gebruiks voorwaarden.
1. Begin met het ontwerpen van uw LUIS-app door uw Azure LUIS-ontwerp sleutel te kiezen:

   ![Scherm opname van het welkomst scherm.](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Wanneer u klaar bent met het resource selectie proces, [maakt u een nieuwe app](luis-how-to-start-new-app.md#create-new-app-in-luis).


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-the-azure-cli"></a>Resources maken in de Azure CLI

Gebruik de [Azure cli](/cli/azure/install-azure-cli) om elke resource afzonderlijk te maken.

Resource `kind` :

* Ontwerp `LUIS.Authoring`
* Voorspellings `LUIS`

1. Meld u aan bij de Azure CLI:

    ```azurecli
    az login
    ```

    Met deze opdracht wordt een browser geopend, zodat u het juiste account kunt selecteren en verificatie kunnen opgeven.

1. Maak een LUIS-ontwerp bron van het type met de `LUIS.Authoring` naam `my-luis-authoring-resource` . Maak deze in de _bestaande_ resource groep met `my-resource-group` de naam voor de `westus` regio.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Maak een LUIS-Voorspellings eindpunt bron van het type, met de `LUIS` naam `my-luis-prediction-resource` . Maak deze in de _bestaande_ resource groep met `my-resource-group` de naam voor de `westus` regio. Als u een hogere door voer wilt dan de gratis laag biedt, wijzigt `F0` u in `S0` . [Meer informatie over prijs categorieën en door voer.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Deze sleutels worden niet gebruikt door de Luis-Portal totdat ze zijn toegewezen op de  >  pagina **Azure-resources** beheren in de Luis-Portal.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resources-in-the-luis-portal"></a>Resources toewijzen in de LUIS-Portal

U kunt een ontwerp bron toewijzen voor één app of voor alle apps in LUIS. Met de volgende procedure worden alle apps aan één ontwerp bron toegewezen.

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai).
1. Selecteer uw gebruikers account in de rechter bovenhoek en selecteer vervolgens **instellingen**.
1. Selecteer op de pagina **gebruikers instellingen** de optie **ontwerp resource toevoegen** en selecteer vervolgens een bestaande ontwerp bron. Selecteer **Opslaan**.

## <a name="assign-a-resource-to-an-app"></a>Een resource toewijzen aan een app

>[!NOTE]
>Als u geen Azure-abonnement hebt, kunt u geen nieuwe resource toewijzen of maken. U moet een [gratis Azure-account](https://azure.microsoft.com/en-us/free/) maken en vervolgens terugkeren naar Luis om een nieuwe resource te maken vanuit de portal.

U kunt deze procedure gebruiken voor het maken van een ontwerp-of Voorspellings bron of het toewijzen van een resource aan een toepassing: 

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai). Selecteer een app in de lijst **Mijn apps**.
1. Ga naar **beheer**  >  **Azure-resources**:

    ![Scherm opname van de pagina met Azure-resources.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Selecteer op het tabblad voor **Spelling** of bron voor het **ontwerpen** de knop **Voorspellings resource toevoegen** of maken van de resource **toevoegen** .
1. Gebruik de velden in het formulier om de juiste resource te zoeken en selecteer vervolgens **Opslaan**.
1. Als u geen bestaande resource hebt, kunt u er een maken door **een nieuwe Luis-resource maken** te selecteren onder aan het venster.


### <a name="assign-a-query-prediction-runtime-resource-without-using-the-luis-portal"></a>Een runtime-resource voor de voor spelling van query's toewijzen zonder de LUIS-portal te gebruiken

Voor geautomatiseerde processen zoals CI/CD-pijp lijnen wilt u mogelijk de toewijzing van een LUIS-runtime resource automatiseren in een LUIS-app. Voer hiervoor de volgende stappen uit:

1. Een Azure Resource Manager-Token ophalen van [deze website](https://resources.azure.com/api/token?plaintext=true). Dit token verloopt, dus gebruik het meteen. De aanvraag retourneert een Azure Resource Manager-token.

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
    
    ![Scherm opname waarin de website wordt weer gegeven voor het aanvragen van een Azure Resource Manager token.](./media/luis-manage-keys/get-arm-token.png)

1. Gebruik het token om de LUIS-runtime resources voor meerdere abonnementen aan te vragen. Gebruik de [API Get Luis Azure accounts](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), waartoe uw gebruikers account toegang heeft.

    Deze bericht-API vereist de volgende waarden:

    |Header|Waarde|
    |--|--|
    |`Authorization`|De waarde van `Authorization` is `Bearer {token}` . De token waarde moet worden voorafgegaan door het woord `Bearer` en een spatie.|
    |`Ocp-Apim-Subscription-Key`|Uw ontwerp sleutel.|

    De API retourneert een matrix van JSON-objecten die uw LUIS-abonnementen vertegenwoordigen. Geretourneerde waarden zijn de abonnements-ID, resource groep en resource naam, geretourneerd als `AccountName` . Zoek het item in de matrix die de LUIS-resource is die u wilt toewijzen aan de LUIS-app.

1. Wijs het token toe aan de LUIS-resource met behulp van de [toewijzing van een Luis Azure-accounts aan een toepassings](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) -API.

    Deze bericht-API vereist de volgende waarden:

    |Type|Instelling|Waarde|
    |--|--|--|
    |Header|`Authorization`|De waarde van `Authorization` is `Bearer {token}` . De token waarde moet worden voorafgegaan door het woord `Bearer` en een spatie.|
    |Header|`Ocp-Apim-Subscription-Key`|Uw ontwerp sleutel.|
    |Header|`Content-type`|`application/json`|
    |String|`appid`|De id van de LUIS-app.
    |Hoofdtekst||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "ResourceGroup-2",<br>"AccountName": "Luis-uswest-S0-2"}|

    Wanneer deze API is voltooid, wordt geretourneerd `201 - created status` .

## <a name="unassign-a-resource"></a>Toewijzing van een resource opheffen

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai)en selecteer vervolgens een app uit de lijst **mijn apps** .
1. Ga naar **beheer**  >  **Azure-resources**.
1. Selecteer op het tabblad voor **spellingen** van resources of **ontwerpen** de knop **resource niet toewijzen** voor de resource.

Wanneer u de toewijzing van een resource ongedaan maakt, wordt deze niet verwijderd uit Azure. Het is alleen ontkoppeld van LUIS.


## <a name="delete-an-account"></a>Een account verwijderen

Zie [gegevens opslag en verwijderen](luis-concept-data-storage.md#accounts) voor meer informatie over welke gegevens worden verwijderd wanneer u uw account verwijdert.

## <a name="change-the-pricing-tier"></a>De prijs categorie wijzigen

1.  Zoek en selecteer uw LUIS-abonnement in [de Azure Portal](https://portal.azure.com):

    ![Scherm afbeelding met een LUIS-abonnement in de Azure Portal.](./media/luis-usage-tiers/find.png)
1.  Selecteer de **prijs categorie** om de beschik bare prijs categorieën te bekijken:

    ![Scherm opname van het menu-item prijs categorie.](./media/luis-usage-tiers/subscription.png)
1.  Selecteer de prijs categorie en klik vervolgens op **selecteren** om de wijziging op te slaan:

    ![Scherm afbeelding die laat zien hoe u een prijs categorie selecteert en opslaat.](./media/luis-usage-tiers/plans.png)

    Wanneer de prijs wijziging is voltooid, controleert een pop-upvenster de update van de prijs categorie:

    ![Scherm opname van het pop-upvenster dat de prijs bijwerking verifieert.](./media/luis-usage-tiers/updated.png)
1. [Wijs deze eindpunt sleutel toe](#assign-a-resource-to-an-app) op de pagina **publiceren** en gebruik deze in alle eindpunt query's.

## <a name="view-azure-resource-metrics"></a>Metrische gegevens van Azure-resource weer geven

### <a name="view-a-summary-of-azure-resource-usage"></a>Een samen vatting van het gebruik van Azure-resources weer geven
U kunt LUIS-gebruiks gegevens weer geven in de Azure Portal. Op de **overzichts** pagina wordt een overzicht weer gegeven, met inbegrip van recente oproepen en fouten. Als u een LUIS-eindpunt aanvraag maakt, moet u Maxi maal vijf minuten toestaan voordat de wijziging wordt weer gegeven.

![Scherm opname van de pagina overzicht.](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Grafieken voor Azure resource usage aanpassen
Op de pagina **metrische** gegevens vindt u een gedetailleerdere weer gave van de informatie:

![Scherm opname van de pagina metrische gegevens.](./media/luis-usage-tiers/metrics-default.png)

U kunt uw metrische grafieken configureren voor een specifieke tijds periode en metrisch type:

![Scherm opname waarin een aangepaste grafiek wordt weer gegeven.](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Waarschuwing drempel waarde voor totaal trans acties
Als u wilt weten wanneer u een bepaalde transactie drempel bereikt, bijvoorbeeld 10.000 trans acties, kunt u een waarschuwing maken:

![Scherm opname van de pagina met waarschuwings regels.](./media/luis-usage-tiers/alert-default.png)

Voeg een metrische waarschuwing toe voor de totale waarde van **aanroepen** voor een bepaalde tijds periode. Voeg e-mail adressen toe van alle personen die de melding moeten ontvangen. Voeg webhooks toe voor alle systemen die de waarschuwing moeten ontvangen. U kunt ook een logische app uitvoeren wanneer de waarschuwing wordt geactiveerd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het gebruik van versies](luis-how-to-manage-versions.md) om de levens cyclus van uw app te beheren.
* Migreer naar de nieuwe [ontwerp bron](luis-migration-authoring.md).
