---
title: Een mini maal vereiste versie van Transport Layer Security (TLS) afdwingen voor binnenkomende aanvragen
titleSuffix: Azure Storage
description: Configureer een opslag account om een minimale versie van Transport Layer Security (TLS) te vereisen voor clients die aanvragen indienen tegen Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/09/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: b6c75bc13bf26510ee72968c5a27407b6b7bfee6
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937488"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>Een mini maal vereiste versie van Transport Layer Security (TLS) afdwingen voor aanvragen van een opslag account

De communicatie tussen een client toepassing en een Azure Storage account wordt versleuteld met behulp van Transport Layer Security (TLS). TLS is een standaard cryptografie protocol dat privacy en gegevens integriteit tussen clients en services via internet waarborgt. Zie [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)voor meer informatie over TLS.

Azure Storage ondersteunt momenteel drie versies van het TLS-protocol: 1,0, 1,1 en 1,2. Azure Storage gebruikt TLS 1,2 voor open bare HTTPS-eind punten, maar TLS 1,0 en TLS 1,1 worden nog steeds ondersteund voor compatibiliteit met eerdere versies.

Standaard kunnen clients met Azure Storage-accounts gegevens verzenden en ontvangen met de oudste versie van TLS, TLS 1,0 en hoger. Als u strengere beveiligings maatregelen wilt afdwingen, kunt u uw opslag account configureren om te vereisen dat clients gegevens verzenden en ontvangen met een nieuwere versie van TLS. Als een opslag account een minimum versie van TLS vereist, kunnen aanvragen met een oudere versie niet worden uitgevoerd.

In dit artikel wordt beschreven hoe u een slepen-Framework (Detection-herstel-audit-governance) kunt gebruiken voor het voortdurend beheren van beveiligde TLS voor uw opslag accounts.

Zie [configure Transport Layer Security (TLS) (Engelstalig) voor een client toepassing](transport-layer-security-configure-client-version.md)voor informatie over het opgeven van een bepaalde versie van TLS bij het verzenden van een aanvraag van een client toepassing.

## <a name="detect-the-tls-version-used-by-client-applications"></a>De TLS-versie detecteren die door client toepassingen wordt gebruikt

Wanneer u een minimum versie van TLS afdwingt voor uw opslag account, is het risico dat aanvragen worden afgewezen van clients die gegevens verzenden met een oudere versie van TLS. Om te begrijpen hoe het configureren van de minimale TLS-versie van invloed kan zijn op client toepassingen, raadt micro soft u aan om logboek registratie in te scha kelen voor uw Azure Storage-account en de logboeken te analyseren na een tijds interval om te detecteren welke versies van TLS-client toepassingen worden gebruikt.

Als u aanvragen wilt registreren voor uw Azure Storage-account en de TLS-versie wilt bepalen die wordt gebruikt door de client, kunt u Azure Storage logboek registratie gebruiken in Azure Monitor (preview). Zie [Azure Storage bewaken](../blobs/monitor-blob-storage.md)voor meer informatie.

Azure Storage logboek registratie in Azure Monitor ondersteunt het gebruik van logboek query's voor het analyseren van logboek gegevens. Als u een query wilt uitvoeren op Logboeken, kunt u een Azure Log Analytics-werk ruimte gebruiken. Zie [zelf studie: aan de slag met log Analytics query's](../../azure-monitor/log-query/log-analytics-tutorial.md)voor meer informatie over logboek query's.

Als u Azure Storage gegevens wilt registreren met Azure Monitor en deze wilt analyseren met Azure Log Analytics, moet u eerst een diagnostische instelling maken die aangeeft welke typen aanvragen en voor welke opslag Services u gegevens wilt registreren. Voer de volgende stappen uit om een diagnostische instelling te maken in de Azure Portal:

1. Schrijf u in [voor de Azure Storage registratie in azure monitor preview](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Maak een nieuwe Log Analytics-werk ruimte in het abonnement dat uw Azure Storage-account bevat. Nadat u logboek registratie voor uw opslag account hebt geconfigureerd, zijn de logboeken beschikbaar in de werk ruimte Log Analytics. Raadpleeg [Een Log Analytics-werkruimte maken in Azure Portal](../../azure-monitor/learn/quick-create-workspace.md) voor meer informatie.
1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer in de sectie controle de optie **Diagnostische instellingen (preview)**.
1. Selecteer de Azure Storage-service waarvoor u aanvragen wilt registreren. Kies bijvoorbeeld **BLOB** om aanvragen te registreren bij Blob Storage.
1. Selecteer **Diagnostische instellingen toevoegen**.
1. Geef een naam op voor de diagnostische instelling.
1. Kies onder **categorie Details** in de sectie **logboek** welke typen aanvragen moeten worden geregistreerd. U kunt aanvragen voor lezen, schrijven en verwijderen vastleggen in een logboek. Als u bijvoorbeeld **StorageRead** en **StorageWrite** kiest, worden de lees-en schrijf aanvragen geregistreerd voor de geselecteerde service.
1. Selecteer onder **doel gegevens** **verzenden naar log Analytics**. Selecteer uw abonnement en de Log Analytics werk ruimte die u eerder hebt gemaakt, zoals wordt weer gegeven in de volgende afbeelding.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Scherm afbeelding die laat zien hoe u een diagnostische instelling voor logboek registratie aanvragen maakt":::

Nadat u de diagnostische instelling hebt gemaakt, worden aanvragen voor het opslag account vervolgens geregistreerd volgens die instelling. Zie voor meer informatie [Diagnostische instelling maken om bron logboeken en metrische gegevens in azure te verzamelen](../../azure-monitor/platform/diagnostic-settings.md).

Zie [bron Logboeken (preview)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview)voor een verwijzing van velden die beschikbaar zijn in azure Storage-Logboeken in azure monitor.

### <a name="query-logged-requests-by-tls-version"></a>Query's in vastgelegde aanvragen per TLS-versie

Azure Storage Logboeken in Azure Monitor bevatten de TLS-versie die wordt gebruikt om een aanvraag te verzenden naar een opslag account. Gebruik de eigenschap **TlsVersion** om de TLS-versie van een geregistreerde aanvraag te controleren.

Open uw Log Analytics-werk ruimte om te bepalen hoeveel aanvragen zijn gedaan voor Blob Storage met verschillende versies van TLS in de afgelopen zeven dagen. Plak vervolgens de volgende query in een nieuwe logboek query en voer deze uit. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

De resultaten geven het aantal aanvragen weer dat is gemaakt met elke versie van TLS:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Scherm opname van de log Analytics-query voor het retour neren van een TLS-versie":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Query vastgelegde aanvragen op IP-adres van beller en de header van de gebruikers agent

Azure Storage-Logboeken in Azure Monitor omvatten ook het IP-adres van de beller en de header van de gebruikers agent, zodat u kunt evalueren welke client toepassingen toegang hebben tot het opslag account. U kunt deze waarden analyseren om te bepalen of client toepassingen moeten worden bijgewerkt voor gebruik van een nieuwere versie van TLS, of dat het acceptabel is om een aanvraag van een client te laten mislukken als deze niet wordt verzonden met de minimale TLS-versie.

Plak de volgende query in een nieuwe logboek query en voer deze uit om te bepalen welke clients aanvragen hebben gedaan met een TLS-versie die ouder is dan TLS 1,2 in de afgelopen zeven dagen. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>Beveiligings Risico's herstellen met een minimum versie van TLS

Wanneer u zeker weet dat verkeer van clients met oudere versies van TLS mini maal is, of dat het acceptabel is voor mislukte aanvragen met een oudere versie van TLS, kunt u beginnen met het afdwingen van een minimale TLS-versie in uw opslag account. Vereisen dat clients een minimum versie van TLS gebruiken om aanvragen voor een opslag account te maken, deel uitmaakt van een strategie om beveiligings Risico's voor uw gegevens te minimaliseren.

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>De minimale TLS-versie configureren voor een opslag account

Als u de minimale TLS-versie voor een opslag account wilt configureren, stelt u de **MinimumTlsVersion** -versie voor het account in. Deze eigenschap is beschikbaar voor alle opslag accounts die zijn gemaakt met het Azure Resource Manager-implementatie model. Zie [overzicht van opslag accounts](storage-account-overview.md)voor meer informatie over het Azure Resource Manager-implementatie model.

De eigenschap **MinimumTlsVersion** is niet standaard ingesteld en retourneert geen waarde voordat u deze expliciet hebt ingesteld.  Als de waarde van de eigenschap **Null** is, worden aanvragen die zijn verzonden met TLS-versie 1,0 of hoger door het opslag account toegestaan.

# <a name="portal"></a>[Portal](#tab/portal)

Wanneer u een opslag account met de Azure Portal maakt, wordt de minimale TLS-versie standaard ingesteld op 1,2.

Voer de volgende stappen uit om de minimale TLS-versie voor een bestaand opslag account te configureren met de Azure Portal:

1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer de **configuratie** -instelling.
1. Gebruik onder **minimum versie van TLS** de vervolg keuzelijst om de minimale versie van TLS te selecteren die vereist is voor toegang tot gegevens in dit opslag account, zoals wordt weer gegeven in de volgende afbeelding.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Scherm afbeelding die laat zien hoe u de minimale versie van TLS configureert in de Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u de minimale TLS-versie wilt configureren voor een opslag account met Power shell, installeert u [Azure PowerShell versie 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) of hoger. Configureer vervolgens de eigenschap **MinimumTLSVersion** voor een nieuw of bestaand opslag account. Geldige waarden voor **MinimumTlsVersion** zijn `TLS1_0` , `TLS1_1` en `TLS1_2` .

In het volgende voor beeld wordt een opslag account gemaakt en wordt de **MinimumTLSVersion** ingesteld op TLS 1,1, wordt het account bijgewerkt en wordt de **MINIMUMTLSVERSION** ingesteld op TLS 1,2. In het voor beeld wordt ook de waarde van de eigenschap in elk geval opgehaald. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS `
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de minimale TLS-versie wilt configureren voor een opslag account met Azure CLI, installeert u Azure CLI versie 2.9.0 of hoger. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie. Configureer vervolgens de eigenschap **minimumTlsVersion** voor een nieuw of bestaand opslag account. Geldige waarden voor **minimumTlsVersion** zijn `TLS1_0` , `TLS1_1` en `TLS1_2` .

In het volgende voor beeld wordt een opslag account gemaakt en wordt de **minimumTLSVersion** ingesteld op TLS 1,1. Vervolgens wordt het account bijgewerkt en wordt de eigenschap **minimumTLSVersion** ingesteld op TLS 1,2. In het voor beeld wordt ook de waarde van de eigenschap in elk geval opgehaald. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[Sjabloon](#tab/template)

Als u de minimum versie TLS wilt configureren voor een opslag account met een sjabloon, maakt u een sjabloon waarvoor de eigenschap **MinimumTLSVersion** is ingesteld op `TLS1_0` , `TLS1_1` , of `TLS1_2` . In de volgende stappen wordt beschreven hoe u een sjabloon maakt in de Azure Portal.

1. Kies in het Azure Portal **een resource maken**.
1. Typ in **de Marketplace zoeken de** **sjabloon implementatie** en druk vervolgens op **Enter**.
1. Kies **Sjabloonimlementatie (implementeren met aangepaste sjablonen) (preview)**, kies **maken** en kies vervolgens **uw eigen sjabloon bouwen in de editor**.
1. Plak in de sjabloon editor in de volgende JSON om een nieuw account te maken en stel de minimale TLS-versie in op TLS 1,2. Vergeet niet om de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Sla de sjabloon op.
1. Geef de para meter van de resource groep op en kies vervolgens de knop **evalueren + maken** om de sjabloon te implementeren en een opslag account te maken met de eigenschap **MinimumTLSVersion** geconfigureerd.

---

> [!NOTE]
> Nadat u de minimale TLS-versie voor het opslag account hebt bijgewerkt, kan het tot 30 seconden duren voordat de wijziging volledig is door gegeven.

Voor het configureren van de minimale TLS-versie is versie 2019-04-01 of hoger van de Azure Storage Resource provider vereist. Zie [Azure Storage Resource Provider rest API](/rest/api/storagerp/)voor meer informatie.

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>De mini maal vereiste TLS-versie voor meerdere accounts controleren

Als u de mini maal vereiste TLS-versie wilt controleren in een reeks opslag accounts met optimale prestaties, kunt u de Azure resource Graph Explorer gebruiken in de Azure Portal. Voor meer informatie over het gebruik van de resource Graph Explorer raadpleegt u [Quick Start: uw eerste resource grafiek query uitvoeren met Azure resource Graph Explorer](../../governance/resource-graph/first-query-portal.md).

Als u de volgende query uitvoert in de resource Graph Explorer, wordt een lijst met opslag accounts geretourneerd en wordt de minimale TLS-versie voor elk account weer gegeven:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>De minimale TLS-versie van een client testen

Als u wilt testen of de mini maal vereiste TLS-versie voor een opslag account aanroepen met een oudere versie verbiedt, kunt u een client configureren voor het gebruik van een oudere versie van TLS. Zie [configure Transport Layer Security (TLS) (Engelstalig) voor een client toepassing](transport-layer-security-configure-client-version.md)voor meer informatie over het configureren van een client voor het gebruik van een specifieke versie van TLS.

Wanneer een client een opslag account opent met een TLS-versie die niet voldoet aan de minimale TLS-versie die voor het account is geconfigureerd, retourneert Azure Storage fout code 400 fout (ongeldige aanvraag) en een bericht dat aangeeft dat de TLS-versie die is gebruikt, niet is toegestaan voor het maken van aanvragen voor dit opslag account.

## <a name="use-azure-policy-to-audit-for-compliance"></a>Azure Policy gebruiken om te controleren op naleving

Als u een groot aantal opslag accounts hebt, wilt u mogelijk een audit uitvoeren om ervoor te zorgen dat alle accounts zijn geconfigureerd voor de minimale versie van TLS die uw organisatie nodig heeft. Gebruik Azure Policy om een set opslag accounts te controleren op naleving. Azure Policy is een service die u kunt gebruiken om beleid te maken, toe te wijzen en te beheren waarmee regels worden toegepast op Azure-resources. Azure Policy helpt u bij het houden van deze resources die voldoen aan uw bedrijfs normen en service overeenkomsten. Zie [Overzicht van Azure Policy](../../governance/policy/overview.md) voor meer informatie.

### <a name="create-a-policy-with-an-audit-effect"></a>Een beleid maken met een controle-effect

Azure Policy ondersteunt effecten die bepalen wat er gebeurt wanneer een beleids regel wordt geëvalueerd op basis van een resource. Het controle-effect maakt een waarschuwing wanneer een resource niet aan het nalevings beleid voldoet, maar de aanvraag niet stopt. Zie [Azure Policy-effecten begrijpen](../../governance/policy/concepts/effects.md)voor meer informatie over effecten.

Voer de volgende stappen uit om een beleid te maken met een controle-effect voor de minimale TLS-versie met de Azure Portal:

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
              "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
              "equals": "TLS1_2"
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

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="Scherm opname van nalevings rapport voor controle beleid voor minimale TLS-versie":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>Azure Policy gebruiken om de minimum versie van TLS af te dwingen

Azure Policy ondersteunt Cloud governance door ervoor te zorgen dat Azure-resources voldoen aan de vereisten en standaarden. Voor het afdwingen van een minimale TLS-versie vereisten voor de opslag accounts in uw organisatie, kunt u een beleid maken waarmee wordt voor komen dat een nieuw opslag account wordt gemaakt waarmee de minimale TLS-vereiste wordt ingesteld op een oudere versie van TLS dan de waarde die door het beleid wordt bepaald. Dit beleid voor komt ook dat alle configuratie wijzigingen voor een bestaand account worden voor komen als de minimale TLS-versie-instelling voor dat account niet aan het beleid voldoet.

Het afdwingings beleid maakt gebruik van het deny-effect om te voor komen dat een aanvraag waarbij een opslag account wordt gemaakt of gewijzigd, zodat de minimale TLS-versie niet meer voldoet aan de standaarden van uw organisatie. Zie [Azure Policy-effecten begrijpen](../../governance/policy/concepts/effects.md)voor meer informatie over effecten.

Als u een beleid wilt maken met een weigering van een minimum versie van TLS die kleiner is dan TLS 1,2, voert u de stappen uit die worden beschreven in [gebruik Azure Policy om te controleren op naleving](#use-azure-policy-to-audit-for-compliance), maar geeft u de volgende JSON op in de sectie **policyRule** van de beleids definitie:

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
          "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
          "equals": "TLS1_2"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Nadat u het beleid met het effect deny hebt gemaakt en aan een bereik hebt toegewezen, kan een gebruiker geen opslag account maken met een minimale TLS-versie die ouder is dan 1,2. Ook kan een gebruiker geen wijzigingen aanbrengen in de configuratie van een bestaand opslag account dat momenteel een minimum versie van TLS vereist die ouder is dan 1,2. Als u dit doet, resulteert dit in een fout. De vereiste minimale TLS-versie voor het opslag account moet zijn ingesteld op 1,2 om door te gaan met het maken of configureren van accounts.

In de volgende afbeelding ziet u de fout die optreedt als u probeert een opslag account te maken met de minimale TLS-versie die is ingesteld op TLS 1,0 (de standaard waarde voor een nieuw account) wanneer een beleid met een deny-effect vereist dat de minimum versie van TLS wordt ingesteld op TLS 1,2.

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="Scherm opname met de fout die optreedt bij het maken van een opslag account in schending van het beleid":::

## <a name="permissions-necessary-to-require-a-minimum-version-of-tls"></a>Benodigde machtigingen voor het vereisen van een minimum versie van TLS

Als u de eigenschap **MinimumTlsVersion** voor het opslag account wilt instellen, moet een gebruiker gemachtigd zijn om opslag accounts te maken en te beheren. Azure RBAC-rollen (op rollen gebaseerd toegangs beheer) die deze machtigingen bieden, zijn onder andere de actie **micro soft. Storage/Storage accounts/write** of **micro \* soft. Storage/Storage accounts/* _. Ingebouwde rollen met deze actie zijn onder andere:

- De rol van Azure Resource Manager [eigenaar](../../role-based-access-control/built-in-roles.md#owner)
- De rol [inzender](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- De rol [Inzender voor opslag accounts](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Deze rollen bieden geen toegang tot gegevens in een opslag account via Azure Active Directory (Azure AD). Ze bevatten echter de _ * micro soft. Storage/Storage accounts/listkeys ophalen/Action * *, waarmee toegang wordt verleend aan de toegangs sleutels voor het account. Met deze machtiging kan een gebruiker de toegangs sleutels voor het account gebruiken om toegang te krijgen tot alle gegevens in een opslag account.

Roltoewijzingen moeten zijn gericht op het niveau van het opslag account of hoger, zodat een gebruiker een minimum versie van TLS vereist voor het opslag account. Zie [inzicht in het bereik voor Azure RBAC](../../role-based-access-control/scope-overview.md)voor meer informatie over het gebruikersrol bereik.

Zorg ervoor dat u alleen de toewijzing van deze rollen beperkt tot degenen die de mogelijkheid moeten hebben om een opslag account te maken of om de eigenschappen ervan bij te werken. Gebruik het principe van minimale bevoegdheden om ervoor te zorgen dat gebruikers de minste machtigingen hebben die ze nodig hebben om hun taken uit te voeren. Zie [Aanbevolen procedures voor Azure RBAC](../../role-based-access-control/best-practices.md)voor meer informatie over het beheren van toegang met Azure RBAC.

> [!NOTE]
> De service beheerder rollen van de klassieke abonnements beheerder en Co-Administrator omvatten het equivalent van de Azure Resource Manager [eigenaar](../../role-based-access-control/built-in-roles.md#owner) van de rol. De rol **eigenaar** omvat alle acties, dus een gebruiker met een van deze beheerders rollen kan ook opslag accounts maken en beheren. Zie [Klassieke abonnementsbeheerdersrollen, Azure-rollen en Azure AD-beheerdersrollen](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles) voor meer informatie.

## <a name="network-considerations"></a>Overwegingen voor het netwerk

Wanneer een client een aanvraag naar het opslag account verzendt, brengt de client eerst een verbinding tot stand met het open bare eind punt van het opslag account voordat aanvragen worden verwerkt. De instelling minimale TLS-versie wordt gecontroleerd nadat de verbinding tot stand is gebracht. Als voor de aanvraag een eerdere versie van TLS wordt gebruikt dan die is opgegeven door de instelling, blijft de verbinding slagen, maar zal de aanvraag uiteindelijk mislukken. Zie [resource-URI-syntaxis](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax)voor meer informatie over open bare eind punten voor Azure Storage.

## <a name="next-steps"></a>Volgende stappen

- [Transport Layer Security (TLS) configureren voor een client toepassing](transport-layer-security-configure-client-version.md)
- [Beveiligings aanbevelingen voor Blob Storage](../blobs/security-recommendations.md)