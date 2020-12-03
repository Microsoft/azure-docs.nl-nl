---
title: Anonieme open bare Lees toegang voor containers en blobs voor komen
titleSuffix: Azure Storage
description: Meer informatie over het analyseren van anonieme aanvragen voor een opslag account en het voor komen van anonieme toegang voor het hele opslag account of voor een afzonderlijke container.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: f12a899d3b6daa3b233e6a799871afca1e24d046
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533741"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>Anonieme open bare Lees toegang voor containers en blobs voor komen

Anonieme openbare leestoegang tot containers en blobs in Azure Storage is een handige manier om gegevens te delen, maar kan ook een beveiligingsrisico opleveren. Het is belang rijk anonieme toegang zorgvuldig te beheren en te begrijpen hoe anonieme toegang tot uw gegevens kan worden geëvalueerd. Operationele complexiteit, menselijke fout of kwaad aardige aanvallen op basis van gegevens die openbaar toegankelijk zijn, kunnen leiden tot dure gegevens inbreuken. Micro soft raadt u aan anonieme toegang alleen in te scha kelen wanneer dit nodig is voor uw toepassings scenario.

Open bare toegang tot uw BLOB-gegevens is standaard altijd verboden. Met de standaard configuratie voor een opslag account kan een gebruiker met de juiste machtigingen echter de open bare toegang tot containers en blobs in een opslag account configureren. Voor een betere beveiliging kunt u niet alle open bare toegang tot het opslag account toestaan, ongeacht de instelling voor open bare toegang voor een afzonderlijke container. Door openbare toegang tot het opslagaccount te weigeren, voorkomt u dat een gebruiker openbare toegang voor een container in het account kan inschakelen. Microsoft raadt u aan om openbare toegang tot een opslagaccount alleen toe te staan als dit vereist is voor uw scenario. Door openbare toegang niet toe te staan, voorkomt u inbreuk op gegevens ten gevolge van ongewenste anonieme toegang.

Wanneer u toegang tot open bare blobs voor het opslag account niet toestaat, worden alle anonieme aanvragen voor dat account door Azure Storage geweigerd. Wanneer open bare toegang niet is toegestaan voor een account, kunnen containers in dat account niet later worden geconfigureerd voor open bare toegang. Alle containers die al zijn geconfigureerd voor open bare toegang, accepteren geen anonieme aanvragen meer. Zie [anonieme open bare Lees toegang voor containers en blobs configureren](anonymous-read-access-configure.md)voor meer informatie.

In dit artikel wordt beschreven hoe u een slepen-Framework (Detection-herstel-audit-governance) gebruikt om de open bare toegang voor uw opslag accounts voortdurend te beheren.

## <a name="detect-anonymous-requests-from-client-applications"></a>Anonieme aanvragen van client toepassingen detecteren

Als u open bare Lees toegang voor een opslag account niet toestaat, is het risico dat aanvragen worden afgewezen voor containers en blobs die momenteel zijn geconfigureerd voor open bare toegang. Het niet toestaan van open bare toegang voor een opslag account heeft voor rang op de instellingen voor open bare toegang voor afzonderlijke containers in dat opslag account. Wanneer open bare toegang niet is toegestaan voor het opslag account, zullen toekomstige anonieme aanvragen voor dat account mislukken.

Micro soft raadt u aan om logboek registratie en metrische gegevens voor dat account in te scha kelen en patronen van anonieme aanvragen gedurende een tijds interval te analyseren om te begrijpen hoe het niet toestaan van open bare toegang van invloed is op client toepassingen. Metrische gegevens gebruiken om het aantal anonieme aanvragen voor het opslag account te bepalen en Logboeken gebruiken om te bepalen welke containers anoniem worden gebruikt.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>Anonieme aanvragen controleren met Metrics Explorer

Als u anonieme aanvragen wilt bijhouden in een opslag account, gebruikt u Azure Metrics Explorer in de Azure Portal. Zie aan de slag [met Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)voor meer informatie over Metrics Explorer.

Volg deze stappen om een metriek te maken waarmee anonieme aanvragen worden bijgehouden:

1. Ga in Azure Portal naar uw opslagaccount. Selecteer **metrische gegevens** onder het gedeelte **bewaking** .
1. Selecteer **Metrische waarde toevoegen**. Geef in het dialoog venster **metrisch** de volgende waarden op:
    1. Zorg ervoor dat het veld bereik is ingesteld op de naam van het opslag account.
    1. Stel de **metrische naam ruimte** in op *BLOB*. Met deze metriek worden alleen aanvragen voor Blob-opslag gerapporteerd.
    1. Stel het veld **metrische waarde** in op *trans acties*.
    1. Stel het **aggregatie** veld in op *Sum*.

    Met de nieuwe metriek wordt de som weer gegeven van het aantal trans acties op Blob Storage over een bepaalde tijds interval. De resulterende metriek wordt weer gegeven, zoals wordt weer gegeven in de volgende afbeelding:

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="Scherm afbeelding die laat zien hoe u metrische gegevens kunt configureren voor het berekenen van BLOB-trans acties":::

1. Selecteer vervolgens de knop **filter toevoegen** om een filter te maken voor de metrische gegevens voor anonieme aanvragen.
1. Geef in het dialoog venster **filter** de volgende waarden op:
    1. Stel de waarde van de **eigenschap** in op *authenticatie*.
    1. Stel het **operator** veld in op het gelijkteken (=).
    1. Stel het veld **waarden** in op *anoniem*.
1. Selecteer in de rechter bovenhoek het tijds interval waarover u de metrische gegevens wilt weer geven. U kunt ook aangeven hoe gedetailleerd de aggregatie van aanvragen moet zijn, door intervallen van 1 minuut tot 1 maand op te geven.

Nadat u de metrische gegevens hebt geconfigureerd, worden anonieme aanvragen weer gegeven in de grafiek. De volgende afbeelding toont anonieme aanvragen die zijn samengevoegd in de afgelopen dertig minuten.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="Scherm opname van geaggregeerde anonieme aanvragen op Blob Storage":::

U kunt ook een waarschuwings regel configureren om u op de hoogte te stellen wanneer er een bepaald aantal anonieme aanvragen wordt gedaan voor uw opslag account. Zie [metrische waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../../azure-monitor/platform/alerts-metric.md)voor meer informatie.

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>Logboeken analyseren om containers te identificeren die anonieme aanvragen ontvangen

Azure Storage legt vastgelegde gegevens vast over aanvragen voor het opslag account, met inbegrip van de manier waarop een aanvraag is geautoriseerd. U kunt de logboeken analyseren om te bepalen welke containers anonieme aanvragen ontvangen.

Als u aanvragen wilt registreren voor uw Azure Storage-account om anonieme aanvragen te evalueren, kunt u Azure Storage logboek registratie gebruiken in Azure Monitor (preview). Zie [Azure Storage bewaken](./monitor-blob-storage.md)voor meer informatie.

Azure Storage logboek registratie in Azure Monitor ondersteunt het gebruik van logboek query's voor het analyseren van logboek gegevens. Als u een query wilt uitvoeren op Logboeken, kunt u een Azure Log Analytics-werk ruimte gebruiken. Zie [zelf studie: aan de slag met log Analytics query's](../../azure-monitor/log-query/log-analytics-tutorial.md)voor meer informatie over logboek query's.

> [!NOTE]
> De preview van Azure Storage logboek registratie in Azure Monitor wordt alleen ondersteund in de open bare Azure-Cloud. Overheids Clouds bieden geen ondersteuning voor logboek registratie voor Azure Storage met Azure Monitor.

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Een diagnostische instelling maken in de Azure Portal

Als u Azure Storage gegevens wilt registreren met Azure Monitor en deze wilt analyseren met Azure Log Analytics, moet u eerst een diagnostische instelling maken die aangeeft welke typen aanvragen en voor welke opslag Services u gegevens wilt registreren. Voer de volgende stappen uit om een diagnostische instelling te maken in de Azure Portal:

1. Schrijf u in [voor de Azure Storage registratie in azure monitor preview](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Maak een nieuwe Log Analytics-werk ruimte in het abonnement dat uw Azure Storage-account bevat. Nadat u logboek registratie voor uw opslag account hebt geconfigureerd, zijn de logboeken beschikbaar in de werk ruimte Log Analytics. Raadpleeg [Een Log Analytics-werkruimte maken in Azure Portal](../../azure-monitor/learn/quick-create-workspace.md) voor meer informatie.
1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer in de sectie controle de optie **Diagnostische instellingen (preview)**.
1. Selecteer **BLOB** om aanvragen te registreren die worden uitgevoerd op Blob Storage.
1. Selecteer **Diagnostische instellingen toevoegen**.
1. Geef een naam op voor de diagnostische instelling.
1. Kies onder **categorie Details** in de sectie **logboek** welke typen aanvragen moeten worden geregistreerd. Alle anonieme aanvragen worden Lees aanvragen, dus Selecteer **StorageRead** om anonieme aanvragen vast te leggen.
1. Selecteer onder **doel gegevens** **verzenden naar log Analytics**. Selecteer uw abonnement en de Log Analytics werk ruimte die u eerder hebt gemaakt, zoals wordt weer gegeven in de volgende afbeelding.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="Scherm afbeelding die laat zien hoe u een diagnostische instelling voor logboek registratie aanvragen maakt":::

Nadat u de diagnostische instelling hebt gemaakt, worden aanvragen voor het opslag account vervolgens geregistreerd volgens die instelling. Zie voor meer informatie [Diagnostische instelling maken om bron logboeken en metrische gegevens in azure te verzamelen](../../azure-monitor/platform/diagnostic-settings.md).

Zie [bron Logboeken (preview)](./monitor-blob-storage-reference.md#resource-logs-preview)voor een verwijzing van velden die beschikbaar zijn in azure Storage-Logboeken in azure monitor.

#### <a name="query-logs-for-anonymous-requests"></a>Query logboeken voor anonieme aanvragen

Azure Storage-Logboeken in Azure Monitor bevatten het type autorisatie dat is gebruikt voor het maken van een aanvraag voor een opslag account. Filter in uw logboek query op de eigenschap **AuthenticationType** om anonieme aanvragen weer te geven.

Als u Logboeken voor de afgelopen zeven dagen wilt ophalen voor anonieme aanvragen bij Blob Storage, opent u uw Log Analytics-werk ruimte. Plak vervolgens de volgende query in een nieuwe logboek query en voer deze uit:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

U kunt ook een waarschuwings regel configureren op basis van deze query om u te informeren over anonieme aanvragen. Zie [logboek waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../../azure-monitor/platform/alerts-log.md)voor meer informatie.

## <a name="remediate-anonymous-public-access"></a>Anonieme open bare toegang herstellen

Nadat u anonieme aanvragen hebt geëvalueerd voor containers en blobs in uw opslag account, kunt u actie ondernemen om open bare toegang te beperken of te voor komen. Als sommige containers in uw opslag account mogelijk beschikbaar moeten zijn voor open bare toegang, kunt u de instelling voor open bare toegang configureren voor elke container in uw opslag account. Deze optie biedt de meest gedetailleerde controle over open bare toegang. Zie [het niveau van open bare toegang instellen voor een container](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)voor meer informatie.

Voor een betere beveiliging kunt u open bare toegang voor het hele opslag account niet toestaan. De instelling voor open bare toegang voor een opslag account overschrijft de afzonderlijke instellingen voor containers in dat account. Als u open bare toegang voor een opslag account niet toestaat, zijn alle containers die zijn geconfigureerd voor het toestaan van open bare toegang, niet meer anoniem toegankelijk. Zie [open bare Lees toegang toestaan of weigeren voor een opslag account](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)voor meer informatie.

Als voor uw scenario vereist is dat bepaalde containers beschikbaar moeten zijn voor open bare toegang, kan het raadzaam zijn om die containers en de bijbehorende blobs te verplaatsen naar opslag accounts die zijn gereserveerd voor open bare toegang. Vervolgens kunt u open bare toegang voor andere opslag accounts weigeren.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Controleren of open bare toegang tot een BLOB niet is toegestaan

Als u wilt controleren of open bare toegang tot een specifieke BLOB niet is toegestaan, kunt u proberen om de BLOB te downloaden via de bijbehorende URL. Als het downloaden is geslaagd, is de BLOB nog steeds openbaar beschikbaar. Als de BLOB niet openbaar toegankelijk is omdat de open bare toegang niet is toegestaan voor het opslag account, wordt een fout bericht weer gegeven waarin staat dat open bare toegang niet is toegestaan voor dit opslag account.

In het volgende voor beeld ziet u hoe u Power shell gebruikt om een BLOB te downloaden via de bijbehorende URL. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>Controleren of het wijzigen van de instelling voor open bare toegang van de container niet is toegestaan

Als u wilt controleren of de instelling voor open bare toegang van een container niet kan worden gewijzigd nadat de open bare toegang voor het opslag account is niet toegestaan, kunt u proberen de instelling te wijzigen. Het wijzigen van de instelling voor open bare toegang van de container mislukt als de open bare toegang niet is toegestaan voor het opslag account.

In het volgende voor beeld ziet u hoe u Power shell kunt gebruiken om de instelling voor open bare toegang van een container te wijzigen. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>Controleer of het maken van een container waarvoor open bare toegang is ingeschakeld, niet is toegestaan

Als open bare toegang niet is toegestaan voor het opslag account, kunt u geen nieuwe container maken waarvoor open bare toegang is ingeschakeld. Als u wilt controleren, kunt u proberen een container te maken waarvoor open bare toegang is ingeschakeld.

In het volgende voor beeld ziet u hoe u Power shell gebruikt om een container te maken waarvoor open bare toegang is ingeschakeld. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>Controleer de instelling voor open bare toegang voor meerdere accounts

Als u de instelling voor open bare toegang wilt controleren over een set opslag accounts met optimale prestaties, kunt u de Azure resource Graph Explorer gebruiken in de Azure Portal. Voor meer informatie over het gebruik van de resource Graph Explorer raadpleegt u [Quick Start: uw eerste resource grafiek query uitvoeren met Azure resource Graph Explorer](../../governance/resource-graph/first-query-portal.md).

De eigenschap **AllowBlobPublicAccess** is niet standaard ingesteld voor een opslag account en retourneert geen waarde totdat u deze expliciet hebt ingesteld. Het opslag account staat open bare toegang toe wanneer de waarde van de eigenschap **Null** of **True** is.

Als u de volgende query uitvoert in de resource Graph Explorer, wordt een lijst met opslag accounts geretourneerd en wordt de instelling voor open bare toegang voor elk account weer gegeven:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

In de volgende afbeelding ziet u de resultaten van een query in een abonnement. Houd er rekening mee dat voor opslag accounts waarvan de eigenschap **AllowBlobPublicAccess** expliciet is ingesteld, deze in de resultaten wordt weer gegeven als **waar** of **Onwaar**. Als de eigenschap **AllowBlobPublicAccess** niet is ingesteld voor een opslag account, wordt deze weer gegeven als leeg (of null) in de query resultaten.

:::image type="content" source="media/anonymous-read-access-prevent/check-public-access-setting-accounts.png" alt-text="Scherm opname van query resultaten voor open bare toegangs instelling voor opslag accounts":::

## <a name="use-azure-policy-to-audit-for-compliance"></a>Azure Policy gebruiken om te controleren op naleving

Als u een groot aantal opslag accounts hebt, wilt u mogelijk een audit uitvoeren om ervoor te zorgen dat deze accounts zijn geconfigureerd om open bare toegang te voor komen. Gebruik Azure Policy om een set opslag accounts te controleren op naleving. Azure Policy is een service die u kunt gebruiken om beleid te maken, toe te wijzen en te beheren waarmee regels worden toegepast op Azure-resources. Azure Policy helpt u bij het houden van deze resources die voldoen aan uw bedrijfs normen en service overeenkomsten. Zie [Overzicht van Azure Policy](../../governance/policy/overview.md) voor meer informatie.

### <a name="create-a-policy-with-an-audit-effect"></a>Een beleid maken met een controle-effect

Azure Policy ondersteunt effecten die bepalen wat er gebeurt wanneer een beleids regel wordt geëvalueerd op basis van een resource. Het controle-effect maakt een waarschuwing wanneer een resource niet aan het nalevings beleid voldoet, maar de aanvraag niet stopt. Zie [Azure Policy-effecten begrijpen](../../governance/policy/concepts/effects.md)voor meer informatie over effecten.

Voer de volgende stappen uit om een beleid te maken met een controle-effect voor de open bare toegangs instelling voor een opslag account met de Azure Portal:

1. Ga in het Azure Portal naar de Azure Policy-service.
1. Selecteer in het gedeelte **ontwerpen** de optie **definities**.
1. Selecteer **beleids definitie toevoegen** om een nieuwe beleids definitie te maken.
1. Selecteer voor het veld **definitie locatie** de knop **meer** om op te geven waar de bron van het controle beleid zich bevindt.
1. Geef een naam op voor het beleid. U kunt desgewenst een beschrijving en categorie opgeven.
1. Voeg onder **beleids regel** de volgende beleids definitie toe aan de sectie **policyRule** .

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. U kunt nu het beleid opslaan.

### <a name="assign-the-policy"></a>Wijs het beleid toe

Wijs vervolgens het beleid toe aan een resource. Het bereik van het beleid komt overeen met die resource en eventuele resources daaronder. Zie [Azure Policy toewijzings structuur](../../governance/policy/concepts/assignment-structure.md)voor meer informatie over het toewijzen van beleid.

Voer de volgende stappen uit om het beleid toe te wijzen met de Azure Portal:

1. Ga in het Azure Portal naar de Azure Policy-service.
1. Selecteer in het gedeelte **ontwerpen** de optie **toewijzingen**.
1. Selecteer **beleid toewijzen** om een nieuwe beleids toewijzing te maken.
1. Selecteer in het veld **bereik** het bereik van de beleids toewijzing.
1. Selecteer de knop **meer** in het veld **beleids definitie** en selecteer vervolgens het beleid dat u in de vorige sectie hebt gedefinieerd in de lijst.
1. Geef een naam op voor de beleids toewijzing. De beschrijving is optioneel.
1. Zorg ervoor dat **beleids afdwinging** is ingesteld op *ingeschakeld*. Deze instelling heeft geen invloed op het controle beleid.
1. Selecteer **controleren + maken** om de toewijzing te maken.

### <a name="view-compliance-report"></a>Nalevings rapport weer geven

Nadat u het beleid hebt toegewezen, kunt u het nalevings rapport bekijken. Het nalevings rapport voor een controle beleid bevat informatie over de opslag accounts die niet voldoen aan het beleid. Zie [nalevings gegevens voor beleid ophalen](../../governance/policy/how-to/get-compliance-data.md)voor meer informatie.

Het kan enkele minuten duren voordat het nalevings rapport beschikbaar is nadat de beleids toewijzing is gemaakt.

Voer de volgende stappen uit om het nalevings rapport in de Azure Portal weer te geven:

1. Ga in het Azure Portal naar de Azure Policy-service.
1. Selecteer **naleving**.
1. Filter de resultaten voor de naam van de beleids toewijzing die u in de vorige stap hebt gemaakt. In het rapport wordt weer gegeven hoeveel resources niet voldoen aan het beleid.
1. U kunt inzoomen op het rapport voor meer informatie, met inbegrip van een lijst met opslag accounts die niet voldoen aan het beleid.

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="Scherm opname van nalevings rapport voor controle beleid voor open bare BLOB-toegang":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>Azure Policy gebruiken om gemachtigde toegang af te dwingen

Azure Policy ondersteunt Cloud governance door ervoor te zorgen dat Azure-resources voldoen aan de vereisten en standaarden. Om ervoor te zorgen dat opslag accounts in uw organisatie alleen geautoriseerde aanvragen toestaan, kunt u een beleid maken waarmee wordt voor komen dat een nieuw opslag account wordt gemaakt met een instelling voor open bare toegang waarmee anonieme aanvragen worden toegestaan. Dit beleid voor komt ook dat alle configuratie wijzigingen voor een bestaand account worden gemaakt als de instelling voor open bare toegang voor dat account niet aan het beleid voldoet.

Het afdwingings beleid gebruikt het deny-effect om te voor komen dat een aanvraag die een opslag account maakt of wijzigt om open bare toegang toe te staan. Zie [Azure Policy-effecten begrijpen](../../governance/policy/concepts/effects.md)voor meer informatie over effecten.

Als u een beleid wilt maken met een weigerings effect voor een open bare toegangs instelling die anonieme aanvragen toestaat, voert u de stappen uit die worden beschreven in [gebruik Azure Policy om te controleren op naleving](#use-azure-policy-to-audit-for-compliance), maar geeft u de volgende JSON op in de sectie **policyRule** van de beleids definitie:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Nadat u het beleid met het effect deny hebt gemaakt en aan een bereik hebt toegewezen, kan een gebruiker geen opslag account maken dat open bare toegang toestaat. Ook kan een gebruiker geen wijzigingen aanbrengen in de configuratie van een bestaand opslag account dat toegang tot het publiek toestaat. Als u dit doet, resulteert dit in een fout. De instelling voor open bare toegang voor het opslag account moet zijn ingesteld op **False** om door te gaan met het maken of configureren van accounts.

In de volgende afbeelding ziet u de fout die optreedt wanneer u probeert om een opslag account te maken dat open bare toegang (de standaard waarde voor een nieuw account) toestaat wanneer een beleid met een deny-effect vereist dat open bare toegang niet is toegestaan.

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="Scherm opname met de fout die optreedt bij het maken van een opslag account in schending van het beleid":::

## <a name="next-steps"></a>Volgende stappen

- [Anonieme open bare Lees toegang voor containers en blobs configureren](anonymous-read-access-configure.md)
- [Anoniem toegang krijgen tot open bare containers en blobs met .NET](anonymous-read-access-client.md)