---
title: Kosten optimaliseren door Azure Blob Storage Access-lagen te automatiseren
description: Maak automatische regels voor het verplaatsen van gegevens tussen de lagen hot, cool en Archive.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/29/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: 1b568687ffe646a91544c1bb75d26d552a23f49c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005279"
---
# <a name="optimize-costs-by-automating-azure-blob-storage-access-tiers"></a>Kosten optimaliseren door Azure Blob Storage Access-lagen te automatiseren

Gegevens sets hebben een unieke levens cyclus. In de loop van de levens cyclus hebben mensen vaak toegang tot bepaalde gegevens. Maar de nood zaak van de toegang tot de gegevens valt aanzienlijk. Sommige gegevens blijven niet actief in de Cloud en worden zelden gebruikt wanneer deze zijn opgeslagen. Sommige gegevens verlopen dagen of maanden na het maken, terwijl andere gegevens sets actief worden gelezen en gewijzigd gedurende hun levens duur. Azure Blob Storage levenscyclus beheer biedt een rijk, op regels gebaseerd beleid voor GPv2-en Blob Storage-accounts. Gebruik het beleid om uw gegevens over te zetten naar de juiste toegangs lagen of verloopt aan het einde van de levens cyclus van de gegevens.

Met het levenscyclus beheer beleid kunt u:

- Overgangs-blobs van koud naar warm onmiddellijk als dit wordt geopend voor optimale prestaties 
- Overgangs-blobs, Blob-versies en BLOB-moment opnamen naar een koele opslaglaag (warm naar koud, Hot-to-Archive of koud om te archiveren) als ze gedurende een bepaalde tijd niet worden geopend of gewijzigd om de kosten te optimaliseren
- Blobs, Blob-versies en BLOB-moment opnamen aan het einde van hun levens duur verwijderen
- Regels definiëren die één keer per dag moeten worden uitgevoerd op het niveau van het opslag account
- Regels Toep assen op containers of een subset van blobs (met behulp van naam voorvoegsels of [BLOB-index Tags](storage-manage-find-blobs.md) als filters)

Houd rekening met een scenario waarbij gegevens veelvuldig toegankelijk zijn tijdens de vroege fase van de levens cyclus, maar af en toe slechts af en toe na twee weken. Na de eerste maand wordt de gegevensset zelden geopend. In dit scenario is hot Storage het beste tijdens de eerste fasen. Cool Storage is het meest geschikt voor incidentele toegang. Archief opslag is de beste laag optie nadat de gegevens gedurende een maand zijn verouderd. Door opslag lagen aan te passen ten opzichte van de leeftijd van gegevens, kunt u de minst dure opslag opties voor uw behoeften ontwerpen. Voor deze overgang zijn levenscyclus beheer beleids regels beschikbaar om verouderde gegevens naar koele lagen te verplaatsen.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

>[!NOTE]
>Als u gegevens nodig hebt om Lees baarheid te blijven, bijvoorbeeld wanneer deze worden gebruikt door StorSimple, moet u geen beleid instellen om blobs naar de laag van het archief te verplaatsen.

## <a name="availability-and-pricing"></a>Beschik baarheid en prijzen

De functie levenscyclus beheer is beschikbaar in alle Azure-regio's voor Algemeen v2-accounts (GPv2), Blob Storage-accounts, Premium Block Blob Storage-accounts en Azure Data Lake Storage Gen2 accounts. In de Azure Portal kunt u een bestaand Algemeen-account (GPv1) upgraden naar een GPv2-account. Zie [overzicht van Azure Storage-account](../common/storage-account-overview.md)voor meer informatie over opslag accounts.

De functie levenscyclus beheer is gratis. Klanten betalen de normale bewerkings kosten voor de API-aanroepen van de [BLOB-laag](/rest/api/storageservices/set-blob-tier) . De verwijderings bewerking is gratis. Zie [prijzen voor blok-BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over prijzen.

## <a name="add-or-remove-a-policy"></a>Een beleid toevoegen of verwijderen

U kunt een beleid toevoegen, bewerken of verwijderen met een van de volgende methoden:

* [Azure-portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure-CLI](/cli/azure/install-azure-cli)
* [REST-API's](/rest/api/storagerp/managementpolicies)

Een beleid kan volledig worden gelezen of geschreven. Gedeeltelijke updates worden niet ondersteund. 

> [!NOTE]
> Als u firewall regels inschakelt voor uw opslag account, kunnen aanvragen voor levenscyclus beheer worden geblokkeerd. U kunt deze aanvragen deblokkeren door uitzonderingen op te geven voor vertrouwde Microsoft-services. Zie de sectie uitzonde ringen in [firewalls en virtuele netwerken configureren](../common/storage-network-security.md#exceptions)voor meer informatie.

In dit artikel wordt uitgelegd hoe u beleid beheert met behulp van de portal-en Power shell-methoden.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Er zijn twee manieren om een beleid toe te voegen via de Azure Portal. 

* [Lijst weergave Azure Portal](#azure-portal-list-view)
* [Code weergave Azure Portal](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Lijst weergave Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek en selecteer uw opslag account in de Azure Portal. 

1. Selecteer onder **BLOB service** **levenscyclus beheer** om uw regels te bekijken of te wijzigen.

1. Selecteer het tabblad **lijst weergave** .

1. Selecteer **een regel toevoegen** en geef uw regel een naam op het **detail** formulier. U kunt ook het **regel bereik**, het **type BLOB** en de **subtype** waarden van BLOB instellen. In het volgende voor beeld wordt het bereik ingesteld op het filteren van blobs. Dit zorgt ervoor dat het tabblad **filter sets** wordt toegevoegd.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-details.png" alt-text="Levenscyclus beheer een pagina met regel Details toevoegen in Azure Portal":::

1. Selecteer **basis-blobs** om de voor waarden voor de regel in te stellen. In het volgende voor beeld worden blobs verplaatst naar koude opslag als deze 30 dagen niet zijn gewijzigd.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-base-blobs.png" alt-text="Pagina levenscyclus beheer basis-blobs in Azure Portal":::

   De **laatst geopende** optie is beschikbaar in de preview-versie in de volgende regio's:

    - Frankrijk - centraal
    - Canada - oost
    - Canada - midden

   > [!IMPORTANT]
   > De tijd voor het bijhouden van de laatste keer dat u toegang hebt, is alleen voor niet-productie gebruik. Service Level Agreements (Sla's) op het niveau van de productie zijn momenteel niet beschikbaar.
   
   Als u de optie **laatst benaderd** wilt gebruiken, selecteert u **toegangs tracering ingeschakeld** op de pagina **levenscyclus beheer** in het Azure Portal. Zie [gegevens verplaatsen op basis van datum van laatste toegang (preview)](#move-data-based-on-last-accessed-date-preview)voor meer informatie over de optie **Laatst geopend** .

1. Als u **blobs beperken met filters** hebt geselecteerd op de pagina **Details** , selecteert u **filter sets** om een optioneel filter toe te voegen. In het volgende voor beeld wordt gefilterd op blobs in de *mylifecyclecontainer* -container die beginnen met ' Log '.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-filter-set.png" alt-text="Pagina met levenscyclus beheer filters instellen in Azure Portal":::

1. Selecteer **toevoegen** om het nieuwe beleid toe te voegen.

#### <a name="azure-portal-code-view"></a>Code weergave Azure Portal
1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek en selecteer uw opslag account in de Azure Portal.

1. Selecteer onder **BLOB service** **levenscyclus beheer** om het beleid weer te geven of te wijzigen.

1. De volgende JSON is een voor beeld van een beleid dat op het tabblad **code weergave** kan worden geplakt.

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "mylifecyclecontainer/log"
             ]
           }
         }
       }
     ]
   }
   ```

1. Selecteer **Opslaan**.

1. Zie de secties [beleid](#policy) en [regels](#rules) voor meer informatie over dit JSON-voor beeld.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Het volgende Power shell-script kan worden gebruikt om een beleid toe te voegen aan uw opslag account. De `$rgname` variabele moet worden geïnitialiseerd met de naam van de resource groep. De `$accountName` variabele moet worden geïnitialiseerd met de naam van uw opslag account.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="template"></a>[Sjabloon](#tab/template)

U kunt levenscyclus beheer definiëren met behulp van Azure Resource Manager sjablonen. Hier volgt een voorbeeld sjabloon voor het implementeren van een RA-GRS GPv2-opslag account met een levenscyclus beheer beleid.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

---

## <a name="policy"></a>Beleid

Een levenscyclus beheer beleid is een verzameling regels in een JSON-document:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

Een beleid is een verzameling regels:

| Parameternaam | Parametertype | Notities |
|----------------|----------------|-------|
| `rules`        | Een matrix van regel objecten | Er is ten minste één regel vereist in een beleid. U kunt Maxi maal 100 regels definiëren in een beleid.|

Elke regel in het beleid heeft verschillende para meters:

| Parameternaam | Parametertype | Notities | Vereist |
|----------------|----------------|-------|----------|
| `name`         | Tekenreeks |De naam van een regel kan Maxi maal 256 alfanumerieke tekens bevatten. De regel naam is hoofdletter gevoelig. Het moet uniek zijn binnen een beleid. | Waar |
| `enabled`      | Booleaans | Een optionele Booleaanse waarde waarmee een regel tijdelijk kan worden uitgeschakeld. De standaard waarde is True als deze niet is ingesteld. | Niet waar | 
| `type`         | Een Enum-waarde | Het huidige geldige type is `Lifecycle` . | Waar |
| `definition`   | Een object dat de levenscyclus regel definieert | Elke definitie bestaat uit een set filters en een Actieset. | Waar |

## <a name="rules"></a>Regels

Elke regel definitie bevat een set filters en een Actieset. Met de [Filterset](#rule-filters) worden regel acties beperkt tot een bepaalde set objecten binnen een container of object namen. Met de [Actieset](#rule-actions) worden de lagen of verwijder acties toegepast op de gefilterde set met objecten.

### <a name="sample-rule"></a>Voorbeeld regel

Met de volgende voorbeeld regel filtert u het account om de acties uit te voeren op objecten die zich in `container1` en beginnen met `foo` .

>[!NOTE]
>- Levenscyclus beheer ondersteunt blok-Blob en typen toevoeg-blobs.<br>
>- Levenscyclus beheer heeft geen invloed op systeem containers zoals $logs en $web.

- Laag-BLOB naar cool laag 30 dagen na laatste wijziging
- Laag-BLOB naar archief laag 90 dagen na laatste wijziging
- BLOB 2.555 dagen verwijderen (zeven jaar) na laatste wijziging
- Vorige BLOB-versies 90 dagen na het maken verwijderen

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "rulefoo",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "delete": {
              "daysAfterCreationGreaterThan": 90
            }
          },
          "baseBlob": {
            "tierToCool": {
              "daysAfterModificationGreaterThan": 30
            },
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 2555
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "container1/foo"
          ]
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Regel filters

Filters beperken regel acties voor een subset van blobs binnen het opslag account. Als er meer dan één filter is gedefinieerd, is dit een logische `AND` uitvoering op alle filters.

Filters omvatten:

| Bestandsnaam | Filtertype | Notities | Is vereist |
|-------------|-------------|-------|-------------|
| blobTypes   | Een matrix met vooraf gedefinieerde Enum-waarden. | De huidige versie ondersteunt `blockBlob` en `appendBlob` . Alleen verwijderen wordt ondersteund voor `appendBlob` , set-laag wordt niet ondersteund. | Yes |
| prefixMatch | Een matrix met teken reeksen voor voor voegsels die moeten worden vergeleken. Elke regel kan Maxi maal 10 voor voegsels definiëren. Een voorvoegsel teken reeks moet beginnen met een container naam. Als u bijvoorbeeld wilt zoeken naar alle blobs onder `https://myaccount.blob.core.windows.net/container1/foo/...` een regel, is de prefixMatch `container1/foo` . | Als u prefixMatch niet definieert, is de regel van toepassing op alle blobs in het opslag account. | No |
| blobIndexMatch | Een matrix met woordenlijst waarden die bestaan uit BLOB-index Tags sleutel en waarden die moeten worden vergeleken. Elke regel kan Maxi maal 10 BLOB-index code voorwaarde definiëren. Als u bijvoorbeeld alle blobs wilt vergelijken met `Project = Contoso` onder `https://myaccount.blob.core.windows.net/` voor een regel, is de blobIndexMatch `{"name": "Project","op": "==","value": "Contoso"}` . | Als u blobIndexMatch niet definieert, is de regel van toepassing op alle blobs in het opslag account. | No |

> [!NOTE]
> BLOB-index bevindt zich in de open bare preview en is beschikbaar in de regio's **Canada-centraal**, **Canada-Oost**, **Frankrijk-centraal** en **Frankrijk-Zuid** . Zie voor meer informatie over deze functie, samen met bekende problemen en beperkingen, [gegevens beheren en zoeken op Azure Blob Storage met Blob-index (preview)](storage-manage-find-blobs.md).

### <a name="rule-actions"></a>Regel acties

Acties worden toegepast op de gefilterde blobs wanneer wordt voldaan aan de voor waarde run.

Levenscyclus beheer ondersteunt het trapsgewijs maken en verwijderen van blobs, eerdere BLOB-versies en BLOB-moment opnamen. Definieer ten minste één actie voor elke regel op basis-blobs, eerdere BLOB-versies of BLOB-moment opnamen.

| Bewerking                      | Basis-BLOB                                  | Momentopname      | Versie
|-----------------------------|--------------------------------------------|---------------|---------------|
| tierToCool                  | Ondersteund voor `blockBlob`                  | Ondersteund     | Ondersteund     |
| enableAutoTierToHotFromCool | Ondersteund voor `blockBlob`                  | Niet ondersteund | Niet ondersteund |
| tierToArchive               | Ondersteund voor `blockBlob`                  | Ondersteund     | Ondersteund     |
| delete                      | Ondersteund voor `blockBlob` en `appendBlob` | Ondersteund     | Ondersteund     |

>[!NOTE]
>Als u meer dan één actie op dezelfde BLOB definieert, past levenscyclus beheer de minst dure actie toe op de blob. Actie `delete` is bijvoorbeeld goed koper dan actie `tierToArchive` . Actie `tierToArchive` is goed koper dan actie `tierToCool` .

De uitvoerings voorwaarden zijn gebaseerd op leeftijd. Basis-blobs maken gebruik van de laatst gewijzigd tijd, Blob-versies gebruiken de aanmaak tijd van de versie en de BLOB-moment opnamen maken gebruik van de moment opname voor het bijhouden van leeftijd.

| Voor waarde voor actie uitvoeren               | Waarde voor waarde                          | Description                                                                      |
|------------------------------------|------------------------------------------|----------------------------------------------------------------------------------|
| daysAfterModificationGreaterThan   | Geheel getal dat de leeftijd in dagen aangeeft | De voor waarde voor basis-BLOB-acties                                              |
| daysAfterCreationGreaterThan       | Geheel getal dat de leeftijd in dagen aangeeft | De voor waarde voor de acties voor BLOB-versie-en BLOB-moment opnamen                         |
| daysAfterLastAccessTimeGreaterThan | Geheel getal dat de leeftijd in dagen aangeeft | evaluatie De voor waarde voor basis-BLOB-acties wanneer tijd van laatste toegang is ingeschakeld |

## <a name="examples"></a>Voorbeelden

De volgende voor beelden laten zien hoe u veelvoorkomende scenario's met levenscyclus beleids regels kunt aanpakken.

### <a name="move-aging-data-to-a-cooler-tier"></a>Verouderde gegevens naar een koele laag verplaatsen

In dit voor beeld ziet u hoe blok-blobs worden voorafgegaan door `container1/foo` of `container2/bar` . Het beleid overschakelt blobs die gedurende meer dan 30 dagen niet zijn gewijzigd naar koude opslag en blobs die niet zijn gewijzigd in 90 dagen naar de laag van het archief:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="move-data-based-on-last-accessed-date-preview"></a>Gegevens verplaatsen op basis van datum van laatste toegang (preview)

U kunt het bijhouden van tijd van laatste toegang inschakelen om een record bij te houden wanneer de BLOB voor het laatst is gelezen of geschreven. U kunt tijd van laatste toegang gebruiken als filter voor het beheren van lagen en het bewaren van BLOB-gegevens.

De **laatst geopende** optie is beschikbaar in de preview-versie in de volgende regio's:

 - Frankrijk - centraal
 - Canada - oost
 - Canada - midden

> [!IMPORTANT]
> De tijd voor het bijhouden van de laatste keer dat u toegang hebt, is alleen voor niet-productie gebruik. Service Level Agreements (Sla's) op het niveau van de productie zijn momenteel niet beschikbaar.

Als u de optie **laatst benaderd** wilt gebruiken, selecteert u **toegangs tracering ingeschakeld** op de pagina **levenscyclus beheer** in het Azure Portal.

#### <a name="how-last-access-time-tracking-works"></a>De werking van tijd bijhouden van de laatste toegang

Wanneer de tijd voor het bijhouden van de laatste toegang is ingeschakeld, wordt de BLOB-eigenschap met de naam `LastAccessTime` bijgewerkt wanneer een BLOB wordt gelezen of geschreven. Een [Get BLOB](/rest/api/storageservices/get-blob) -bewerking wordt beschouwd als een toegangs bewerking. Het [ophalen van BLOB-eigenschappen](/rest/api/storageservices/get-blob-properties), het ophalen van BLOB- [meta gegevens](/rest/api/storageservices/get-blob-metadata)en het [ophalen van BLOB-labels](/rest/api/storageservices/get-blob-tags) zijn geen toegangs bewerkingen. de tijd van de laatste toegang kan daarom niet worden bijgewerkt.

Om de impact op latentie van lees toegang te minimaliseren, wordt alleen de eerste Lees bewerking van de laatste 24 uur de tijd van de laatste toegang bijgewerkt. Volgende Lees bewerkingen in dezelfde periode van 24 uur worden de tijd van laatste toegang niet bijgewerkt. Als een BLOB wordt gewijzigd tussen lees bewerkingen, is de tijd van laatste toegang de recentere waarde van de twee waarden.

In het volgende voor beeld worden blobs verplaatst naar koude opslag als ze gedurende 30 dagen niet zijn geopend. De `enableAutoTierToHotFromCool` eigenschap is een Booleaanse waarde die aangeeft of een blob automatisch moet worden gelaagd van koud naar warm als deze opnieuw wordt geopend nadat deze is getierd naar cool.

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

#### <a name="storage-account-support"></a>Ondersteuning voor opslag accounts

Het bijhouden van tijd van laatste toegang is beschikbaar voor de volgende typen opslag accounts:

 - V2-opslag accounts voor algemeen gebruik
 - Blob Storage-accounts blok keren
 - Blob Storage-accounts

Als uw opslag account een algemeen v1-account is, gebruikt u de Azure Portal om te upgraden naar een v2-account voor algemeen gebruik.

Opslag accounts met een hiërarchische naam ruimte die is ingeschakeld voor gebruik met Azure Data Lake Storage Gen2 worden nu ondersteund.

#### <a name="pricing-and-billing"></a>Prijzen en facturering

Elke tijd voor het bijwerken van laatste toegang wordt als een [andere bewerking](https://azure.microsoft.com/pricing/details/storage/blobs/)beschouwd.

### <a name="archive-data-after-ingest"></a>Gegevens archiveren na opname

Sommige gegevens blijven niet actief in de Cloud en worden zelden, indien ooit, geopend. Het volgende levenscyclus beleid is geconfigureerd om gegevens te archiveren zodra deze zijn opgenomen. In dit voor beeld worden blok-blobs in het opslag account in de container `archivecontainer` omgezet in een Archive-laag. De overgang wordt uitgevoerd door op blobs 0 dagen na de laatste wijzigings tijd te handelen:

> [!NOTE] 
> Het is raadzaam om uw blobs rechtstreeks naar de archief laag te uploaden om efficiënter te zijn. U kunt de header x-MS-Access-tier gebruiken voor [PutBlob](/rest/api/storageservices/put-blob) of [putblock List](/rest/api/storageservices/put-block-list) met rest versie 2018-11-09 en nieuwere of onze meest recente client bibliotheken voor Blob Storage. 

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Gegevens laten verlopen op basis van leeftijd

Sommige gegevens worden verwacht dagen of maanden na het maken. U kunt een levenscyclus beheer beleid configureren om gegevens te laten verlopen door te verwijderen op basis van de gegevens leeftijd. In het volgende voor beeld ziet u een beleid waarmee alle blok-blobs die ouder zijn dan 365 dagen worden verwijderd.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-data-with-blob-index-tags"></a>Gegevens verwijderen met Blob-index Tags
Sommige gegevens mogen alleen worden verlopen als deze expliciet zijn gemarkeerd voor verwijdering. U kunt een levenscyclus beheer beleid configureren om gegevens te laten verlopen die zijn gelabeld met kenmerken sleutel/waarde van BLOB-index. In het volgende voor beeld ziet u een beleid waarmee alle blok-blobs worden verwijderd die zijn gelabeld met `Project = Contoso` . Zie voor meer informatie over de BLOB-index [gegevens beheren en zoeken op Azure Blob Storage met Blob-index (preview)](storage-manage-find-blobs.md).

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="manage-versions"></a>Versies beheren

Voor gegevens die regel matig worden gewijzigd en geopend gedurende de levens duur, kunt u versie beheer van Blob Storage inschakelen om automatisch eerdere versies van een object te onderhouden. U kunt een beleid maken om eerdere versies uit te lagen of te verwijderen. De versie leeftijd wordt bepaald door de aanmaak tijd van de versie te evalueren. Met deze beleids regel maakt u een aantal eerdere versies van de container `activedata` die 90 dagen of ouder zijn nadat het maken van de versie is gemaakt naar cool laag en verwijdert u eerdere versies die 365 dagen of ouder zijn.

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "versionrule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "tierToCool": {
              "daysAfterCreationGreaterThan": 90
            },
            "delete": {
              "daysAfterCreationGreaterThan": 365
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "activedata"
          ]
        }
      }
    }
  ]
}
```

## <a name="faq"></a>Veelgestelde vragen

**Ik heb een nieuw beleid gemaakt, waarom worden de acties niet onmiddellijk uitgevoerd?**

Het platform voert het levenscyclus beleid eenmaal per dag uit. Nadat u een beleid hebt geconfigureerd, kan het tot 24 uur duren voordat bepaalde acties voor de eerste keer worden uitgevoerd.

**Hoe lang duurt het om de acties uit te voeren als ik een bestaand beleid bijwerk?**

Het bijgewerkte beleid duurt Maxi maal 24 uur. Zodra het beleid van kracht is, kan het tot 24 uur duren voordat de acties zijn uitgevoerd. Daarom kan het tot 48 uur duren voordat de beleids acties zijn voltooid.

**Ik heb een gearchiveerde BLOB hand matig opnieuw gehydrateerd, hoe voorkom ik dat deze tijdelijk weer naar de laag van het archief wordt verplaatst?**

Wanneer een BLOB wordt verplaatst van de ene toegangs laag naar een andere, verandert de tijd van de laatste wijziging niet. Als u een gearchiveerde BLOB hand matig opnieuw hebt gehydrateerd naar een warme laag, zou deze terug worden verplaatst naar de archief laag door de levenscyclus beheer-engine. Schakel de regel die van invloed is op deze BLOB tijdelijk uit om te voor komen dat deze opnieuw wordt gearchiveerd. Schakel de regel opnieuw in wanneer de BLOB veilig kan worden verplaatst naar de laag van het archief. U kunt ook de BLOB naar een andere locatie kopiëren als deze permanent of koud laag moet blijven.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het herstellen van gegevens na onbedoeld verwijderen:

- [Voorlopig verwijderen voor Azure Storage-blobs](./soft-delete-blob-overview.md)

Meer informatie over het beheren en zoeken van gegevens met Blob-index:

- [Gegevens in Azure Blob Storage beheren en zoeken met blobindex](storage-manage-find-blobs.md)