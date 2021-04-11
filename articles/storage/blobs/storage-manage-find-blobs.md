---
title: Azure Blob-gegevens beheren en vinden met Blob index Tags (preview)
description: Meer informatie over het gebruik van BLOB-index Tags voor het categoriseren, beheren en opvragen van blob-objecten.
author: twooley
ms.author: twooley
ms.date: 03/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: klaasl
ms.custom: references_regions
ms.openlocfilehash: 2188aaea0cf5a4616291d3fdad839aefb2dbc413
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280697"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags-preview"></a>Azure Blob-gegevens beheren en vinden met Blob index Tags (preview)

Als gegevens sets groter worden, kan het zoeken naar een specifiek object in een gegevens zee lastig zijn. BLOB-index Tags bieden mogelijkheden voor gegevens beheer en-detectie door gebruik te maken van kenmerken van index Tags met sleutel waarden. U kunt objecten categoriseren en zoeken binnen één container of in alle containers in uw opslag account. Als de gegevens vereisten veranderen, kunnen objecten dynamisch worden gecategoriseerd door hun index Tags bij te werken. Objecten kunnen in-place blijven met de huidige container organisatie.

Met Blob index Tags kunt u het volgende doen:

- Uw blobs dynamisch categoriseren met index Tags voor sleutel waarden
- Snel specifieke gecodeerde blobs vinden in een hele opslag account
- Voorwaardelijk gedrag voor BLOB-Api's opgeven op basis van de evaluatie van index Tags
- Index Tags gebruiken voor geavanceerde besturings elementen voor functies zoals het beheer van de [BLOB-levens cyclus](storage-lifecycle-management-concepts.md)

Houd rekening met een scenario waarin u miljoenen blobs in uw opslag account hebt, die toegankelijk zijn voor veel verschillende toepassingen. U wilt alle gerelateerde gegevens uit één project zoeken. U weet niet wat het bereik is omdat de gegevens kunnen worden verdeeld over meerdere containers met verschillende naam conventies. Uw toepassingen uploaden echter alle gegevens met tags op basis van het project. In plaats van miljoenen blobs te doorzoeken en namen en eigenschappen te vergelijken, kunt u gebruiken `Project = Contoso` als detectie criterium. Met Blob-index worden alle containers in het hele opslag account gefilterd, zodat u alleen de set van 50 blobs uit kunt vinden en retour neren `Project = Contoso` .

> [!IMPORTANT]
> Index Tags van blobs zijn momenteel beschikbaar als **Preview-versie** en kunnen in alle open bare regio's worden weer gegeven. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of nog niet beschikbaar zijn.

Zie [BLOB-index Tags gebruiken om gegevens te beheren en te zoeken](storage-blob-index-how-to.md)om aan de slag te gaan met voor beelden van het gebruik van BLOB-index.

## <a name="blob-index-tags-and-data-management"></a>Labels en gegevens beheer voor BLOB-indexen

Container-en BLOB-naam voorvoegsels zijn eendimensionale categorisaties. Met Blob-index Tags kunt u multi-dimensionale categorisatie voor [BLOB-gegevens typen (blok keren, toevoegen of pagina)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs). Multi-dimensionale categorisatie is systeem eigen geïndexeerd door Azure Blob Storage zodat u uw gegevens snel kunt vinden.

Houd rekening met de volgende vijf blobs in uw opslag account:

- *container1/transaction.csv*
- *container2/campaign.docx*
- *Foto's/bannerphoto.png*
- *archieven/voltooide/2019review.pdf*
- *Logboeken/2020/01/01/logfile.txt*

Deze blobs worden gescheiden met behulp van een voor voegsel van de naam van de *container/virtuele map/BLOB*. U kunt een index label kenmerk van `Project = Contoso` op deze vijf blobs instellen om ze samen te categoriseren en daarbij hun huidige voorvoegsel organisatie te onderhouden. Door index tags toe te voegen, hoeft u geen gegevens te verplaatsen door de mogelijkheid om gegevens te filteren en te zoeken met behulp van de index.

## <a name="setting-blob-index-tags"></a>Labels voor BLOB-index instellen

BLOB-index Tags zijn sleutel-waardeparen die kunnen worden toegepast op nieuwe of bestaande objecten in uw opslag account. U kunt index Tags tijdens het upload proces opgeven met behulp van [put-BLOB](/rest/api/storageservices/put-blob), [blokkerings lijst plaatsen](/rest/api/storageservices/put-block-list)of BLOB-bewerkingen [kopiëren](/rest/api/storageservices/copy-blob) en de optionele `x-ms-tags` koptekst. Als u al blobs in uw opslag account hebt, roept u de [set BLOB-Tags](/rest/api/storageservices/set-blob-tags) aan die een opgemaakt XML-document door geven met de index Tags in de hoofd tekst van de aanvraag.

> [!IMPORTANT]
> Het instellen van BLOB-index Tags kan worden uitgevoerd door de eigenaar van de [opslag-BLOB](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) en door iedereen met een Shared Access Signature die gemachtigd is om toegang te krijgen tot de labels van de BLOB (de `t` SAS-machtiging).
>
> Daarnaast kunnen RBAC-gebruikers met de `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` machtiging deze bewerking uitvoeren.

U kunt één tag op uw BLOB Toep assen om te beschrijven wanneer de verwerking van uw gegevens is voltooid.

> "processedDate" = ' 2020-01-01 '

U kunt meerdere labels op uw BLOB Toep assen om de gegevens beter te beschrijven.

> "Project" = ' Contoso '  
> ' Ingedeeld ' = ' True '  
> "Status" = ' niet verwerkt '  
> "Priority" = "01"

Als u de bestaande kenmerken van de index code wilt wijzigen, haalt u de bestaande label kenmerken op, wijzigt u de code kenmerken en vervangt u door de bewerking [BLOB Tags instellen](/rest/api/storageservices/set-blob-tags) . Als u alle index Tags uit de BLOB wilt verwijderen, roept u de `Set Blob Tags` bewerking aan zonder label kenmerken opgegeven. Als blob-index Tags zijn een subresource voor de inhoud van de BLOB-gegevens, worden `Set Blob Tags` onderliggende inhoud niet gewijzigd en wordt de laatste wijzigings tijd of eTag van de BLOB niet gewijzigd. U kunt index Tags maken of wijzigen voor alle huidige basis-blobs en eerdere versies. Tags op moment opnamen of zachte verwijderde blobs kunnen echter niet worden gewijzigd.

De volgende limieten gelden voor BLOB-index Tags:

- Elke Blob kan Maxi maal 10 BLOB-index Tags hebben
- Tags sleutels moeten tussen de 1 en 128 tekens lang zijn
- Label waarden moeten tussen 0 en 256 tekens lang zijn
- Code sleutels en waarden zijn hoofdletter gevoelig
- Code sleutels en waarden bieden alleen ondersteuning voor teken reeks gegevens typen. Getallen, datums, tijden of speciale tekens worden opgeslagen als teken reeksen
- Code sleutels en waarden moeten voldoen aan de volgende naamgevings regels:
  - Alfanumerieke tekens:
    - **a** t/m **z** (kleine letters)
    - **A** t/m **Z** (hoofd letters)
    - **0** t/m **9** (cijfers)
  - Geldige speciale tekens: spatie, plus teken, minteken, punt, dubbele punt, gelijkteken, onderstrepings teken, schuine streep ( ` +-.:=_/` )

## <a name="getting-and-listing-blob-index-tags"></a>Labels voor BLOB-indexen ophalen en weer geven

BLOB-index Tags worden opgeslagen als subresource naast de blobgegevens en kunnen onafhankelijk van de onderliggende BLOB-gegevens inhoud worden opgehaald. BLOB-index Tags voor één BLOB kunnen worden opgehaald met de bewerking [BLOB-labels ophalen](/rest/api/storageservices/get-blob-tags) . Met de bewerking [lijst-blobs](/rest/api/storageservices/list-blobs) met de `include:tags` para meter worden ook alle blobs in een container en de bijbehorende BLOB-index Tags geretourneerd.

> [!IMPORTANT]
> Het ophalen en weer geven van BLOB-index Tags kan worden uitgevoerd door de eigenaar van de gegevens van de [opslag-BLOB](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) en door iedereen met een Shared Access Signature die gemachtigd is om toegang te krijgen tot de labels van de BLOB (de `t` SAS-machtiging).
>
> Daarnaast kunnen RBAC-gebruikers met de `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` machtiging deze bewerking uitvoeren.

Voor alle blobs met ten minste één BLOB-index label `x-ms-tag-count` wordt de geretourneerd in de [lijst blobs](/rest/api/storageservices/list-blobs), [ophalen BLOB](/rest/api/storageservices/get-blob)en [Eigenschappen van BLOB](/rest/api/storageservices/get-blob-properties) -bewerkingen ophalen, waarmee het aantal index Tags op de BLOB wordt aangegeven.

## <a name="finding-data-using-blob-index-tags"></a>Gegevens zoeken met behulp van BLOB-index Tags

Met de indexerings engine worden uw sleutel waarden kenmerken in een multi-dimensionale index weer gegeven. Nadat u de index Tags hebt ingesteld, bestaan deze in de BLOB en kunnen deze direct worden opgehaald. Het kan enige tijd duren voordat de BLOB-index wordt bijgewerkt. Nadat de BLOB-index is bijgewerkt, kunt u de systeem eigen query-en detectie mogelijkheden van Blob Storage gebruiken.

Met de bewerking [blobs per Tags zoeken](/rest/api/storageservices/find-blobs-by-tags) kunt u een gefilterde set blobs ophalen waarvan de index Tags overeenkomen met een bepaalde query-expressie. `Find Blobs by Tags` ondersteunt filteren op alle containers in uw opslag account of u kunt het filteren op slechts één container bereiken. Omdat alle index code sleutels en-waarden teken reeksen zijn, gebruiken relationele Opera tors een lexografische-sortering.

> [!IMPORTANT]
> Het zoeken van gegevens met behulp van BLOB-index Tags kan worden uitgevoerd door de eigenaar van de gegevens van de [opslag-BLOB](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) en door iedereen met een Shared Access Signature die gemachtigd is om blobs te vinden op basis van tags (de `f` SAS-machtiging).
>
> Daarnaast kunnen RBAC-gebruikers met de `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` machtiging deze bewerking uitvoeren.

De volgende criteria zijn van toepassing op filtering van BLOB-index:

- Code sleutels moeten tussen dubbele aanhalings tekens worden geplaatst (")
- Label waarden en container namen moeten tussen enkele aanhalings tekens worden geplaatst (')
- Het teken @ mag alleen worden gefilterd op een specifieke container naam (bijvoorbeeld `@container = 'ContainerName'` )
- Filters worden toegepast met lexografische sorteren op teken reeksen
- Identieke bereik bewerkingen met dezelfde sleutel zijn ongeldig (bijvoorbeeld `"Rank" > '10' AND "Rank" >= '15'` )
- Als u REST gebruikt voor het maken van een filter expressie, moeten de tekens URI-code ring zijn

In de onderstaande tabel ziet u alle geldige Opera tors voor `Find Blobs by Tags` :

|  Operator  |  Beschrijving  | Voorbeeld |
|------------|---------------|---------|
|     =      |     Is gelijk aan     | `"Status" = 'In Progress'` |
|     >      |  Groter dan | `"Date" > '2018-06-18'` |
|     >=     |  Groter dan of gelijk aan | `"Priority" >= '5'` |
|     <      |  Kleiner dan   | `"Age" < '32'` |
|     <=     |  Kleiner dan of gelijk aan  | `"Company" <= 'Contoso'` |
|    AND     |  Logische en  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | Bereik naar een specifieke container | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> Zorg dat u bekend bent met lexicographical-volg orde bij het instellen en uitvoeren van query's op Tags.
>
> - Getallen worden vóór letters gesorteerd. Getallen worden gesorteerd op basis van het eerste cijfer.
> - Hoofd letters worden gesorteerd vóór kleine letters.
> - Symbolen zijn niet standaard. Sommige symbolen worden vóór numerieke waarden gesorteerd. Andere symbolen worden vóór of na letters gesorteerd.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Voorwaardelijke BLOB-bewerkingen met Blob index Tags

In REST versies 2019-10-10 en hoger ondersteunen de meeste [BLOB service-api's](/rest/api/storageservices/operations-on-blobs) nu een voorwaardelijke header, `x-ms-if-tags` waardoor de bewerking alleen slaagt als aan de opgegeven voor waarde voor de BLOB-index is voldaan. Als niet aan de voor waarde wordt voldaan, krijgt u `error 412: The condition specified using HTTP conditional header(s) is not met` .

De `x-ms-if-tags` header kan worden gecombineerd met de andere bestaande HTTP-voorwaardelijke headers (if-match, if-none-match, enzovoort). Als er meerdere voorwaardelijke headers in een aanvraag worden gegeven, moeten ze allemaal de waarde True evalueren voordat de bewerking kan worden voltooid. Alle voorwaardelijke headers worden in feite gecombineerd met logische en.

De onderstaande tabel bevat de geldige Opera tors voor voorwaardelijke bewerkingen:

|  Operator  |  Beschrijving  | Voorbeeld |
|------------|---------------|---------|
|     =      |     Is gelijk aan     | `"Status" = 'In Progress'` |
|     <>     |   Is niet gelijk aan   | `"Status" <> 'Done'` |
|     >      |  Groter dan | `"Date" > '2018-06-18'` |
|     >=     |  Groter dan of gelijk aan | `"Priority" >= '5'` |
|     <      |  Kleiner dan   | `"Age" < '32'` |
|     <=     |  Kleiner dan of gelijk aan  | `"Company" <= 'Contoso'` |
|    AND     |  Logische en  | `"Rank" >= '010' AND "Rank" < '100'` |
|     OF     | Logische of   | `"Status" = 'Done' OR "Priority" >= '05'` |

> [!NOTE]
> Er zijn twee extra Opera Tors, niet gelijk aan en logische of, die zijn toegestaan in de voorwaardelijke `x-ms-if-tags` koptekst voor BLOB-bewerkingen, maar die niet aanwezig zijn in de `Find Blobs by Tags` bewerking.

## <a name="platform-integrations-with-blob-index-tags"></a>Platform integraties met Blob index Tags

Met Blob-index Tags kunt u niet alleen uw blobgegevens categoriseren, beheren en zoeken, maar ook integratie met andere Blob Storage functies, zoals [levenscyclus beheer](storage-lifecycle-management-concepts.md).

### <a name="lifecycle-management"></a>Levenscyclus beheer

Met het `blobIndexMatch` filter als regel in levenscyclus beheer kunt u gegevens verplaatsen naar koele lagen of gegevens verwijderen op basis van de index Tags die zijn toegepast op uw blobs. U kunt nauw keuriger in uw regels en alleen blobs verplaatsen of verwijderen als ze overeenkomen met de opgegeven label criteria.

U kunt een overeenkomende BLOB-index instellen als een zelfstandig filter dat is ingesteld in een levenscyclus regel om acties op gelabelde gegevens toe te passen. U kunt ook een voor voegsel en een BLOB-index combi neren zodat deze overeenkomen met meer specifieke gegevens sets. Het opgeven van meerdere filters in een levenscyclus regel is van toepassing op een logische en-bewerking. De actie is alleen van toepassing als *alle* filter criteria overeenkomen.

De volgende voor beeld-levenscyclus beheer regel is van toepassing op blok-blobs in een container met de naam *videofiles*. De regel lagen blobs om opslag alleen te archiveren als de gegevens overeenkomen met de criteria van de BLOB-index codes van `"Status" == 'Processed' AND "Source" == 'RAW'` .

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Voor beeld van een overeenkomende regel voor een BLOB-index voor levenscyclus beheer in Azure Portal](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```

---

## <a name="permissions-and-authorization"></a>Machtigingen en autorisatie

U kunt toegang tot BLOB-index Tags toestaan met een van de volgende benaderingen:

- Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om machtigingen te verlenen aan een Azure Active Directory (Azure AD)-beveiligingsprincipal. Gebruik Azure AD voor superieure beveiliging en gebruiks gemak. Zie [toegang tot blobs en wacht rijen toestaan met Azure Active Directory](../common/storage-auth-aad.md)voor meer informatie over het gebruik van Azure AD met Blob-bewerkingen.
- Een Shared Access Signature (SAS) gebruiken om de toegang tot de BLOB-index te delegeren. Zie voor meer informatie over gedeelde toegangs handtekeningen [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](../common/storage-sas-overview.md).
- De toegangs sleutels voor het account gebruiken om bewerkingen met gedeelde sleutel te autoriseren. Zie [autoriseren met gedeelde sleutel](/rest/api/storageservices/authorize-with-shared-key)voor meer informatie.

BLOB-index Tags zijn een subresource voor de BLOB-gegevens. Een gebruiker met machtigingen of een SAS-token om blobs te lezen of te schrijven heeft mogelijk geen toegang tot de index Tags van de blob.

### <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Aanroepers die gebruikmaken van een [Azure AD-identiteit](../common/storage-auth-aad.md) kunnen de volgende machtigingen krijgen voor het werken met Blob-index Tags.

| Bewerkingen van BLOB-index Tags                                          | Azure RBAC-actie                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [BLOB-Tags instellen](/rest/api/storageservices/set-blob-tags)           | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/Tags/schrijven    |
| [BLOB-labels ophalen](/rest/api/storageservices/get-blob-tags)           | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/tags/lezen     |
| [Blobs zoeken op labels](/rest/api/storageservices/find-blobs-by-tags) | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/filter/actie |

Aanvullende machtigingen, gescheiden van de onderliggende BLOB-gegevens, zijn vereist voor bewerkingen voor index Tags. De rol [opslag-BLOB-gegevens eigenaar](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) wordt machtigingen verleend voor alle drie de bewerkingen van de BLOB-index Tags. Er worden alleen machtigingen voor en bewerkingen verleend aan de [gegevens lezer](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) van de opslag-BLOB `Find Blobs by Tags` `Get Blob Tags` .

### <a name="sas-permissions"></a>SAS-machtigingen

Aanroepers die gebruikmaken van een [Shared Access Signature (SAS),](../common/storage-sas-overview.md) kunnen machtigingen met een bereik krijgen voor het uitvoeren van BLOB-index Tags.

#### <a name="blob-sas"></a>BLOB SAS

De volgende machtigingen kunnen worden verleend in een BLOB-SAS om toegang tot BLOB-index tags toe te staan. Lees-en schrijf machtigingen van blobs zijn alleen voldoende om de index Tags te mogen lezen of schrijven.

| Machtiging | URI-symbool | Toegestane bewerkingen                |
|------------|------------|-----------------------------------|
| Index Tags |     t      | Index Tags voor een BLOB ophalen en instellen |

#### <a name="container-sas"></a>Container SAS

De volgende machtigingen kunnen worden verleend in een container-SAS om filteren op BLOB-tags toe te staan. De `Blob List` machtiging is niet genoeg om filter-blobs toe te staan op index Tags.

| Machtiging | URI-symbool | Toegestane bewerkingen         |
|------------|------------|----------------------------|
| Index Tags |     f      | Blobs met index Tags zoeken |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Kiezen tussen de meta gegevens-en BLOB-index Tags

Zowel BLOB index Tags als meta gegevens bieden de mogelijkheid om wille keurige, door de gebruiker gedefinieerde sleutel waarden op te slaan naast een BLOB-resource. Beide kunnen worden opgehaald en direct worden ingesteld, zonder de inhoud van de BLOB te retour neren of te wijzigen. Het is mogelijk om meta gegevens en index Tags te gebruiken.

Alleen index Tags worden automatisch geïndexeerd en doorzoekbaar gemaakt door de systeem eigen Blob Storage-service. Meta gegevens kunnen niet systeem eigen worden geïndexeerd of doorzocht. U moet een afzonderlijke service gebruiken, zoals [Azure Search](../../search/search-blob-ai-integration.md). BLOB-index Tags hebben extra machtigingen voor lezen, filteren en schrijven die gescheiden zijn van de onderliggende BLOB-gegevens. Meta gegevens gebruiken dezelfde machtigingen als de BLOB en worden geretourneerd als HTTP-headers door de bewerkingen [Get BLOB](/rest/api/storageservices/get-blob) en [Eigenschappen van BLOB ophalen](/rest/api/storageservices/get-blob-properties) . BLOB index Tags worden op rest versleuteld met een door [micro soft beheerde sleutel](../common/storage-service-encryption.md). Meta gegevens worden op rest versleuteld met behulp van dezelfde versleutelings sleutel die is opgegeven voor BLOB-gegevens.

De volgende tabel bevat een overzicht van de verschillen tussen de index Tags voor meta gegevens en blob:

|              |   Metagegevens   |   Index Tags voor BLOB  |
|--------------|--------------|--------------------|
| **Limieten**      | Geen numerieke limiet, 8 KB totaal, hoofdletter gevoelig | 10 Tags per BLOB Max, 768 bytes per tag, hoofdletter gevoelig |
| **Updates**    | Niet toegestaan op de archief laag, `Set Blob Metadata` vervangt alle bestaande meta gegevens, `Set Blob Metadata` wijzigt de laatste wijzigings tijd van de BLOB | Voor alle toegangs lagen is toegestaan, worden `Set Blob Tags` alle bestaande tags vervangen, `Set Blob Tags` wordt de laatste wijziging van de BLOB niet gewijzigd |
| **Storage**     | Opgeslagen met de BLOB-gegevens | Subresource van de BLOB-gegevens |
| **& Query's indexeren** | Moet een afzonderlijke service gebruiken, zoals Azure Search | Indexerings-en query functies die zijn ingebouwd in Blob Storage |
| **Versleuteling** | Versleuteld op rest met dezelfde versleutelings sleutel die wordt gebruikt voor BLOB-gegevens | Versleuteld op rest met een door micro soft beheerde versleutelings sleutel |
| **Prijzen** | De grootte van meta gegevens is opgenomen in de opslag kosten voor een BLOB | Vaste kosten per index code |
| **Antwoord voor koptekst** | Meta gegevens die als kopteksten zijn geretourneerd in `Get Blob` en `Get Blob Properties` | Het aantal labels dat `Get Blob` is geretourneerd door of `Get Blob Properties` , tags die alleen worden geretourneerd door `Get Blob Tags` en `List Blobs` |
| **Machtigingen**  | Lees-of schrijf machtigingen voor BLOB-gegevens worden uitgebreid naar meta gegevens | Er zijn aanvullende machtigingen vereist voor het lezen, filteren of schrijven van index Tags |
| **Naamgeving** | Namen van meta gegevens moeten voldoen aan de naamgevings regels voor C#-id's | BLOB index Tags bieden ondersteuning voor een groter aantal alfanumerieke tekens |

## <a name="pricing"></a>Prijzen

Prijzen voor BLOB-index zijn beschikbaar in open bare preview-versie en kunnen worden gewijzigd voor algemene Beschik baarheid. Er worden kosten in rekening gebracht voor het gemiddelde maandelijkse aantal index Tags in een opslag account. Er zijn geen kosten voor de indexerings engine. Aanvragen voor `Set Blob Tags` , `Get Blob Tags` en `Find Blobs by Tags` worden in rekening gebracht op basis van hun respectieve bewerkings typen. Zie [prijzen voor blok-BLOB voor meer informatie](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability-and-storage-account-support"></a>Regionale Beschik baarheid en ondersteuning voor opslag accounts

BLOB-index Tags zijn alleen beschikbaar voor Algemeen v2-accounts (GPv2) met een hiërarchische naam ruimte (HNS) is uitgeschakeld. Algemeen-accounts (GPV1) worden niet ondersteund, maar u kunt een upgrade uitvoeren voor een GPv1-account naar een GPv2-account.

Index Tags worden niet ondersteund voor Premium Storage-accounts. Zie [overzicht van Azure Storage-account](../common/storage-account-overview.md)voor meer informatie over opslag accounts.

BLOB index Tags zijn momenteel beschikbaar in alle open bare regio's.

Zie [BLOB-index Tags gebruiken om gegevens te beheren en te zoeken](storage-blob-index-how-to.md)om aan de slag te gaan.

> [!IMPORTANT]
> U moet uw abonnement registreren voordat u de preview-versie van de BLOB-index in uw opslag accounts kunt gebruiken. Zie de sectie [voor waarden en bekende problemen](#conditions-and-known-issues) in dit artikel.

### <a name="register-your-subscription-preview"></a>Uw abonnement registreren (preview-versie)

Omdat de labels van de BLOB-index alleen in de open bare preview zijn, moet u uw abonnement registreren voordat u de functie kunt gebruiken. Voer de volgende Power shell-of CLI-opdrachten uit om een aanvraag in te dienen.

#### <a name="register-by-using-powershell"></a>Registreren met behulp van Power shell

```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Registreren met behulp van Azure CLI

```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues"></a>Voor waarden en bekende problemen

In deze sectie worden bekende problemen en voor waarden voor de open bare preview van BLOB-index Tags beschreven. Deze functie mag niet worden gebruikt voor productie werkbelastingen totdat de algemene Beschik baarheid (GA) is bereikt. Dit kan veranderen.

- Voor de preview moet u eerst uw abonnement registreren voordat u de BLOB-index voor uw opslag account in de preview-regio's kunt gebruiken.
- Alleen GPv2-accounts worden ondersteund in preview. BLOB-, BlockBlobStorage-en HNS-DataLake Gen2-accounts worden niet ondersteund. GPv1-accounts worden niet ondersteund.
- Als u pagina-blobs met index Tags uploadt, blijven de Tags verloren. Stel de Tags na het uploaden van een pagina-Blob in.
- Wanneer het bereik van het filter is beperkt tot één container, `@container` kan de eigenschap alleen worden door gegeven als alle index Tags in de filter expressie gelijkheids controles zijn (Key = waarde).
- Wanneer u de operator Range gebruikt met de `AND` voor waarde, kunt u alleen dezelfde index code sleutel naam ( `"Age" > '013' AND "Age" < '100'` ) opgeven.
- Versie beheer en BLOB-index worden niet ondersteund. BLOB-index Tags blijven behouden voor versies, maar worden niet door gegeven aan de BLOB-index engine.
- Er is geen API om te bepalen of index Tags worden geïndexeerd.
- Failover van account wordt niet ondersteund. De BLOB-index wordt mogelijk niet goed bijgewerkt na een failover.
- Levenscyclus beheer ondersteunt alleen gelijkheids controles met overeenkomende BLOB-index.
- `Copy Blob` kopieert geen blob-index Tags van de bron-BLOB naar de nieuwe doel-blob. U kunt de labels opgeven die u wilt Toep assen op de doel-BLOB tijdens de Kopieer bewerking.
- `Copy Blob` (Asynchroon kopiëren) vanuit een ander opslag account met toegepaste labels op de doel-BLOB zorgt ervoor dat de BLOB-index engine de BLOB en de bijbehorende labels in de Filterset niet retourneert. Gebruik `Copy Blob` van URL (gesynchroniseerde kopie).
- Tags blijven behouden bij het maken van moment opnamen. Het promo veren van een moment opname wordt echter niet ondersteund en kan resulteren in een lege tag-set.

## <a name="faq"></a>Veelgestelde vragen

**Kan de BLOB-index Help me filteren en inhoud in mijn blobs doorzoeken?**

Nee, als u in uw blobgegevens wilt zoeken, gebruikt u query versnelling of Azure Search.

**Zijn er vereisten voor index label waarden?**

BLOB-index Tags bieden alleen ondersteuning voor teken reeks gegevens typen en query's retour neren resultaten met lexicographical-volg orde. Voor getallen is het pad nul. Voor datums en tijden slaat u op als een ISO 8601-compatibele indeling.

**Zijn er labels voor BLOB-indexen en Azure Resource Manager labels?**

Nee, Resource Manager-Labels helpen beheer vlak resources zoals abonnementen, resource groepen en opslag accounts te organiseren. Index Tags bieden het beheer en detectie van blobs op het gegevens vlak.

## <a name="next-steps"></a>Volgende stappen

Zie [BLOB-index gebruiken om gegevens te beheren en te zoeken](storage-blob-index-how-to.md)voor een voor beeld van het gebruik van een BLOB-index.

Meer informatie over [levenscyclus beheer](storage-lifecycle-management-concepts.md) en het instellen van een regel met overeenkomende BLOB-index.
