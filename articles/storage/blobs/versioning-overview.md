---
title: BLOB-versie beheer
titleSuffix: Azure Storage
description: Met versie beheer van Blob-opslag blijven eerdere versies van een object automatisch behouden en worden ze geïdentificeerd met tijds tempels. U kunt een vorige versie van een BLOB herstellen om uw gegevens te herstellen als deze ten onrechte zijn gewijzigd of verwijderd.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f104b98c870fe6eee1d32fe656c0bba416cf3700
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259741"
---
# <a name="blob-versioning"></a>BLOB-versie beheer

U kunt versie beheer van Blob Storage inschakelen om automatisch eerdere versies van een object te onderhouden.  Wanneer BLOB-versie beheer is ingeschakeld, kunt u een eerdere versie van een BLOB herstellen om uw gegevens te herstellen als deze ten onrechte zijn gewijzigd of verwijderd.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="recommended-data-protection-configuration"></a>Aanbevolen configuratie voor gegevens beveiliging

BLOB-versie beheer maakt deel uit van een uitgebreide strategie voor gegevens beveiliging voor BLOB-gegevens. Micro soft raadt aan om de volgende functies voor gegevens bescherming in te scha kelen voor optimale beveiliging van uw BLOB-gegevens:

- BLOB-versie beheer om eerdere versies van een blob automatisch te onderhouden. Wanneer BLOB-versie beheer is ingeschakeld, kunt u een eerdere versie van een BLOB herstellen om uw gegevens te herstellen als deze ten onrechte zijn gewijzigd of verwijderd. Zie [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)voor meer informatie over het inschakelen van BLOB-versies.
- Container soft delete, om een verwijderde container te herstellen. Zie voor meer informatie over het inschakelen van de container Soft soft delete [voor containers, voorlopig verwijderen inschakelen en beheren](soft-delete-container-enable.md).
- Zacht verwijderen van BLOB, voor het herstellen van een blob, moment opname of versie die is verwijderd. Zie voor het inschakelen van het voorlopig verwijderen van blobs de optie [voorlopig verwijderen inschakelen en beheren voor blobs](soft-delete-blob-enable.md).

Zie [overzicht van gegevens beveiliging](data-protection-overview.md)voor meer informatie over de aanbevelingen van micro soft voor gegevens bescherming.

## <a name="how-blob-versioning-works"></a>Hoe BLOB versie beheer werkt

Een versie legt de status van een BLOB op een bepaald moment vast. Wanneer BLOB-versie beheer is ingeschakeld voor een opslag account, maakt Azure Storage automatisch een nieuwe versie van een BLOB wanneer die BLOB wordt gewijzigd.

Wanneer u een BLOB maakt waarvoor versie beheer is ingeschakeld, is de nieuwe BLOB de huidige versie van de BLOB (of de basis-blob). Als u deze BLOB vervolgens wijzigt, maakt Azure Storage een versie die de status van de BLOB vastlegt voordat deze is gewijzigd. De gewijzigde BLOB wordt de nieuwe huidige versie. Telkens wanneer u de BLOB wijzigt, wordt een nieuwe versie gemaakt.

In het volgende diagram ziet u hoe versies worden gemaakt voor schrijf bewerkingen en hoe een eerdere versie kan worden gepromoveerd tot de huidige versie:

:::image type="content" source="media/versioning-overview/blob-versioning-diagram.png" alt-text="Diagram waarin wordt weer gegeven hoe BLOB versie beheer werkt":::

Wanneer u een BLOB verwijdert waarvoor versie beheer is ingeschakeld, wordt de huidige versie van de BLOB een vorige versie en is er geen actuele versie meer. Eerdere versies van de BLOB blijven behouden.

BLOB-versies zijn onveranderbaar. U kunt de inhoud of meta gegevens van een bestaande BLOB-versie niet wijzigen.

Een groot aantal versies per Blob kan de latentie verhogen voor bewerkingen in BLOB-vermeldingen. Micro soft raadt aan om minder dan 1000 versies per BLOB te onderhouden. U kunt levenscyclus beheer gebruiken om oude versies automatisch te verwijderen. Zie [kosten optimaliseren door Azure Blob Storage Access-lagen te automatiseren](storage-lifecycle-management-concepts.md)voor meer informatie over levenscyclus beheer.

BLOB-versie beheer is beschikbaar voor algemeen gebruik v2-, blok-Blob-en Blob Storage-accounts. Opslag accounts met een hiërarchische naam ruimte die is ingeschakeld voor gebruik met Azure Data Lake Storage Gen2 worden momenteel niet ondersteund.

Versie 2019-10-10 en hoger van de Azure Storage REST API ondersteunt BLOB-versie beheer.

> [!IMPORTANT]
> BLOB-versie beheer kan u niet helpen bij het herstellen van een onbedoelde verwijdering van een opslag account of container. Als u onbedoeld verwijderen van het opslag account wilt voor komen, configureert u een vergren deling van de bron van het opslag account. Zie een [Azure Resource Manager Lock Toep assen op een opslag account](../common/lock-account-resource.md)voor meer informatie over het vergren delen van een opslag account.

### <a name="version-id"></a>Versie-ID

Elke BLOB-versie wordt geïdentificeerd met een versie-ID. De waarde van de versie-ID is het tijds tempel waarop de blob is bijgewerkt. De versie-ID wordt toegewezen op het moment dat de versie wordt gemaakt.

U kunt een lees-of verwijder bewerking uitvoeren op een specifieke versie van een BLOB door de versie-ID op te geven. Als u de versie-ID weglaat, wordt de bewerking uitgevoerd op basis van de huidige versie (de basis-blob).

Wanneer u een schrijf bewerking aanroept voor het maken of wijzigen van een blob, retourneert Azure Storage de *x-MS-versie-id* -header in het antwoord. Deze header bevat de versie-ID voor de huidige versie van de blob die is gemaakt door de schrijf bewerking.

De versie-ID blijft hetzelfde voor de levens duur van de versie.

### <a name="versioning-on-write-operations"></a>Versie beheer voor schrijf bewerkingen

Wanneer BLOB-versie beheer is ingeschakeld, maakt elke schrijf bewerking naar een BLOB een nieuwe versie. Schrijf bewerkingen zijn onder andere [put-BLOB](/rest/api/storageservices/put-blob), [blokkerings lijst plaatsen](/rest/api/storageservices/put-block-list), [BLOB kopiëren](/rest/api/storageservices/copy-blob)en [BLOB-meta gegevens instellen](/rest/api/storageservices/set-blob-metadata).

Als de schrijf bewerking een nieuwe BLOB maakt, is de resulterende BLOB de huidige versie van de blob. Als de schrijf bewerking een bestaande BLOB wijzigt, worden de nieuwe gegevens vastgelegd in de bijgewerkte blob, wat de huidige versie is, en maakt Azure Storage een versie die de vorige status van de BLOB opslaat.

Ter vereenvoudiging wordt de versie-ID als een eenvoudige integer weer gegeven in de diagrammen die in dit artikel worden weer gegeven. In werkelijkheid is de versie-ID een tijds tempel. De huidige versie wordt weer gegeven in blauw en eerdere versies worden grijs weer gegeven.

In het volgende diagram ziet u hoe schrijf bewerkingen van invloed zijn op BLOB-versies. Wanneer een BLOB wordt gemaakt, is die BLOB de huidige versie. Wanneer dezelfde BLOB wordt gewijzigd, wordt een nieuwe versie gemaakt om de vorige status van de BLOB op te slaan en wordt de bijgewerkte BLOB de huidige versie.

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="Diagram waarin wordt weer gegeven hoe schrijf bewerkingen van invloed zijn op blobs in versie.":::

> [!NOTE]
> Een blob die is gemaakt vóór het inschakelen van versie beheer voor het opslag account heeft geen versie-ID. Wanneer die BLOB wordt gewijzigd, wordt de gewijzigde BLOB de huidige versie en wordt er een versie gemaakt om de status van de BLOB op te slaan vóór de update. Aan de versie wordt een versie-ID toegewezen die de aanmaak tijd heeft.

Wanneer BLOB-versie beheer is ingeschakeld voor een opslag account, activeren alle schrijf bewerkingen op blok-blobs het maken van een nieuwe versie, met uitzonde ring van de [put-blok](/rest/api/storageservices/put-block) bewerking.

Voor pagina-blobs en toevoeg-blobs wordt alleen een subset van schrijf bewerkingen geactiveerd waarbij een versie wordt gemaakt. Deze bewerkingen zijn onder andere:

- [Blob plaatsen](/rest/api/storageservices/put-blob)
- [Blokkerings lijst plaatsen](/rest/api/storageservices/put-block-list)
- [BLOB-meta gegevens instellen](/rest/api/storageservices/set-blob-metadata)
- [Blob kopiëren](/rest/api/storageservices/copy-blob)

Met de volgende bewerkingen wordt het maken van een nieuwe versie niet geactiveerd. Als u wijzigingen van deze bewerkingen wilt vastleggen, moet u een hand matige moment opname maken:

- [Put-pagina](/rest/api/storageservices/put-page) (pagina-blob)
- [Blok toevoegen](/rest/api/storageservices/append-block) (toevoeg-blob)

Alle versies van een BLOB moeten van hetzelfde type BLOB zijn. Als een BLOB vorige versies heeft, kunt u een BLOB van een type niet overschrijven met een ander type, tenzij u eerst de BLOB en alle versies ervan verwijdert.

### <a name="versioning-on-delete-operations"></a>Versie beheer voor Delete-bewerkingen

Wanneer u de bewerking [BLOB verwijderen](/rest/api/storageservices/delete-blob) aanroept zonder een versie-id op te geven, wordt de huidige versie een eerdere versie en is er geen actuele versie meer. Alle bestaande vorige versies van de BLOB blijven behouden.

In het volgende diagram ziet u het effect van een Verwijder bewerking op een blob met een versie:

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="Diagram waarin de verwijdering van de versie van de BLOB wordt verwijderd.":::

Als u een specifieke versie van een BLOB wilt verwijderen, geeft u de ID voor die versie op in de Verwijder bewerking. Als de functie voor het voorlopig verwijderen van blobs ook is ingeschakeld voor het opslag account, wordt de versie in het systeem bewaard totdat de tijdelijke Bewaar periode voor verwijderen is verstreken.

Door nieuwe gegevens naar de BLOB te schrijven, maakt u een nieuwe huidige versie van de blob. Eventuele bestaande versies worden niet beïnvloed, zoals wordt weer gegeven in het volgende diagram.

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="Diagram van het opnieuw maken van de versie-BLOB na verwijdering.":::

### <a name="access-tiers"></a>Toegangslagen

U kunt alle versies van een blok-blob, met inbegrip van de huidige versie, verplaatsen naar een andere blob-toegangs laag door de bewerking [set BLOB tier](/rest/api/storageservices/set-blob-tier) aan te roepen. U kunt profiteren van de prijzen met een lagere capaciteit door oudere versies van een BLOB te verplaatsen naar de cool-of Archive-laag. Zie [Azure Blob Storage: warme, cool en archief toegangs lagen](storage-blob-storage-tiers.md)voor meer informatie.

Gebruik het levenscyclus beheer van de blob om het proces van het verplaatsen van blok-blobs naar de juiste laag te automatiseren. Zie [de levens cyclus van Azure Blob-opslag beheren](storage-lifecycle-management-concepts.md)voor meer informatie over levenscyclus beheer.

## <a name="enable-or-disable-blob-versioning"></a>BLOB-versie beheer in-of uitschakelen

Zie [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)voor meer informatie over het in-of uitschakelen van BLOB-versies.

Als u BLOB-versie beheer uitschakelt, worden bestaande blobs, versies of moment opnamen niet verwijderd. Wanneer u BLOB-versie beheer uitschakelt, blijven alle bestaande versies toegankelijk in uw opslag account. Er worden vervolgens geen nieuwe versies gemaakt.

Als een blob is gemaakt of gewijzigd nadat het maken van de versie is uitgeschakeld op het opslag account, wordt door het overschrijven van de BLOB een nieuwe versie gemaakt. De bijgewerkte blob is niet langer de huidige versie en heeft geen versie-ID. Bij alle volgende updates van de BLOB worden de gegevens overschreven zonder dat de vorige status wordt opgeslagen.

U kunt versies lezen of verwijderen met de versie-ID nadat versie beheer is uitgeschakeld. U kunt ook de versies van een BLOB weer geven nadat versie beheer is uitgeschakeld.

In het volgende diagram ziet u hoe het wijzigen van een BLOB nadat versie beheer is uitgeschakeld, wordt een BLOB gemaakt die geen versie heeft. Eventuele bestaande versies die aan de BLOB zijn gekoppeld, blijven behouden.

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="Diagram waarin de basis-blob is gewijzigd nadat de versie is uitgeschakeld.":::

## <a name="blob-versioning-and-soft-delete"></a>BLOB-versie en zacht verwijderen

Micro soft raadt u aan om de functie voor het voorlopig verwijderen van versies en blobs voor uw opslag accounts in te scha kelen voor optimale gegevens bescherming. Zie voor meer informatie over het verwijderen van blobs [voorlopig verwijderen voor Azure Storage-blobs](./soft-delete-blob-overview.md).

### <a name="overwriting-a-blob"></a>Een BLOB overschrijven

Als blob-versie beheer en dynamisch verwijderen van BLOB zijn ingeschakeld voor een opslag account, wordt door het overschrijven van een blob automatisch een nieuwe versie gemaakt. De nieuwe versie wordt niet zacht verwijderd en wordt niet verwijderd wanneer de tijdelijke Bewaar periode voor het verwijderen is verlopen. Er zijn geen voorlopig verwijderde moment opnamen gemaakt.

### <a name="deleting-a-blob-or-version"></a>Een BLOB of versie verwijderen

Als versie beheer en voorlopig verwijderen beide zijn ingeschakeld voor een opslag account, wordt de huidige versie van de BLOB een vorige versie wanneer u een BLOB verwijdert. Er wordt geen nieuwe versie gemaakt en er worden geen tijdelijke verwijderde moment opnamen gemaakt. De tijdelijke Bewaar periode voor verwijderen is niet van toepassing op de verwijderde blob.

Zacht verwijderen biedt extra beveiliging voor het verwijderen van BLOB-versies. Wanneer u een vorige versie van de BLOB verwijdert, wordt deze versie zacht verwijderd. De zachte verwijderde versie wordt bewaard tot de tijdelijke verwijderings periode is verstreken, waardoor deze permanent wordt verwijderd.

Als u een vorige versie van een BLOB wilt verwijderen, roept u de bewerking **BLOB verwijderen** aan en geeft u de versie-id op.

In het volgende diagram ziet u wat er gebeurt wanneer u een BLOB of een BLOB-versie verwijdert.

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="Diagram van het verwijderen van een versie waarvoor zacht verwijderen is ingeschakeld.":::

### <a name="restoring-a-soft-deleted-version"></a>Een voorlopig verwijderde versie herstellen

U kunt de bewerking [BLOB verwijderen](/rest/api/storageservices/undelete-blob) gebruiken om met zachte verwijderde versies te herstellen tijdens de tijdelijke verwijderings periode. Met de bewerking **BLOB verwijderen** worden altijd alle voorlopig verwijderde versies van de BLOB hersteld. Het is niet mogelijk om slechts één zacht verwijderde versie te herstellen.

Als u de voorlopig verwijderde versies herstelt met de bewerking voor het **ongedaan** maken van de blob, wordt een versie niet gepromoveerd tot de huidige versie. Als u de huidige versie wilt herstellen, moet u eerst alle voorlopig verwijderde versies herstellen en vervolgens de bewerking [BLOB kopiëren](/rest/api/storageservices/copy-blob) gebruiken om een vorige versie naar een nieuwe huidige versie te kopiëren.

In het volgende diagram ziet u hoe u de Soft verwijderde BLOB-versies herstelt met de bewerking **BLOB verwijderen** en hoe u de huidige versie van de BLOB herstelt met de bewerking **BLOB kopiëren** .

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="Diagram waarin wordt getoond hoe u met Soft verwijderde versies herstelt.":::

Nadat de Bewaar periode voor de tijdelijke verwijdering is verstreken, worden de door soft verwijderde BLOB-versies definitief verwijderd.

## <a name="blob-versioning-and-blob-snapshots"></a>BLOB-versie beheer en BLOB-moment opnamen

Een BLOB-moment opname is een alleen-lezen kopie van een blob die wordt uitgevoerd op een specifiek punt in de tijd. BLOB-moment opnamen en BLOB-versies zijn vergelijkbaar, maar er wordt hand matig een moment opname gemaakt door u of uw toepassing, terwijl er automatisch een BLOB-versie wordt gemaakt bij een schrijf-of verwijder bewerking wanneer BLOB-versie beheer is ingeschakeld voor uw opslag account.

> [!IMPORTANT]
> Micro soft adviseert dat nadat u BLOB-versie beheer hebt ingeschakeld, uw toepassing ook bijwerkt om het maken van moment opnamen van blok-blobs te stoppen. Als versie beheer is ingeschakeld voor uw opslag account, worden alle blok-BLOB-updates en verwijderingen vastgelegd en bewaard door versies. Het maken van moment opnamen biedt geen extra beveiligingen voor uw blok-BLOB-gegevens als blob-versie beheer is ingeschakeld en kan de kosten en de complexiteit van de toepassing verhogen.

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>Moment opname maken van een BLOB wanneer versie beheer is ingeschakeld

Hoewel het niet wordt aangeraden, kunt u een moment opname van een BLOB maken die ook is geversied. Als u uw toepassing niet meer kunt gebruiken om moment opnamen van blobs te maken wanneer u versie beheer inschakelt, kan uw toepassing zowel moment opnamen als versies ondersteunen.

Wanneer u een moment opname van een blob met een versie maakt, wordt er een nieuwe versie gemaakt op het moment dat de moment opname wordt gemaakt. Er wordt ook een nieuwe huidige versie gemaakt wanneer u een moment opname maakt.

In het volgende diagram ziet u wat er gebeurt wanneer u een moment opname van een blob met een versie maakt. In het diagram bevatten de BLOB-versies en moment opnamen met versie 2 en 3 identieke gegevens.

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="Diagram waarin moment opnamen van een blob met versie nummer worden weer gegeven.":::

## <a name="authorize-operations-on-blob-versions"></a>Bewerkingen op BLOB-versies autoriseren

U kunt toegang tot BLOB-versies toestaan met een van de volgende benaderingen:

- Door Azure RBAC (op rollen gebaseerd toegangs beheer) te gebruiken om machtigingen te verlenen aan een Azure Active Directory (Azure AD)-beveiligingsprincipal. Micro soft raadt u aan Azure AD te gebruiken voor superieure beveiliging en gebruiks gemak. Zie [toegang tot blobs en wacht rijen toestaan met Azure Active Directory](../common/storage-auth-aad.md)voor meer informatie over het gebruik van Azure AD met Blob-bewerkingen.
- Door gebruik te maken van een Shared Access Signature (SAS) om de toegang tot BLOB-versies te delegeren. Geef de versie-ID voor het ondertekende bron type op `bv` , die een BLOB-versie vertegenwoordigt, om een SAS-token te maken voor bewerkingen op een specifieke versie. Zie voor meer informatie over gedeelde toegangs handtekeningen [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](../common/storage-sas-overview.md).
- Door de toegangs sleutels voor het account te gebruiken om bewerkingen te autoriseren op BLOB-versies met gedeelde sleutel. Zie [autoriseren met gedeelde sleutel](/rest/api/storageservices/authorize-with-shared-key)voor meer informatie.

BLOB-versie beheer is ontworpen om uw gegevens te beschermen tegen onbedoeld of kwaad aardige verwijdering. Voor het verbeteren van de beveiliging zijn speciale machtigingen vereist voor het verwijderen van een BLOB-versie. In de volgende secties worden de machtigingen beschreven die nodig zijn voor het verwijderen van een BLOB-versie.

### <a name="azure-rbac-action-to-delete-a-blob-version"></a>Azure RBAC-actie voor het verwijderen van een BLOB-versie

In de volgende tabel ziet u welke Azure RBAC-acties ondersteuning bieden voor het verwijderen van een BLOB of een BLOB-versie.

| Description | Blob service bewerking | Azure RBAC-gegevens actie vereist | Ondersteuning voor ingebouwde rollen van Azure |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| De huidige versie verwijderen | Blob verwijderen | **Micro soft. Storage/Storage accounts/blobServices/containers/blobs/verwijderen** | Inzender voor Storage Blob-gegevens |
| Een vorige versie verwijderen | Blob verwijderen | **Micro soft. Storage/Storage accounts/blobServices/containers/blobs/deleteBlobVersion/Action** | Eigenaar van opslagblobgegevens |

### <a name="shared-access-signature-sas-parameters"></a>Para meters voor Shared Access Signature (SAS)

De ondertekende resource voor een BLOB-versie is `bv` . Zie [een service-SAS maken](/rest/api/storageservices/create-service-sas) of [een gebruikers delegering sa's maken](/rest/api/storageservices/create-user-delegation-sas)voor meer informatie.

De volgende tabel bevat de vereiste machtiging voor een SAS om een BLOB-versie te verwijderen.

| **Machtiging** | **URI-symbool** | **Toegestane bewerkingen** |
|----------------|----------------|------------------------|
| Verwijderen         | x              | Een BLOB-versie verwijderen. |

## <a name="pricing-and-billing"></a>Prijzen en facturering

Het inschakelen van BLOB-versie beheer kan leiden tot extra kosten voor gegevens opslag voor uw account. Bij het ontwerpen van uw toepassing is het belang rijk om te weten hoe deze kosten kunnen toenemen, zodat u de kosten kunt minimaliseren.

BLOB-versies, zoals BLOB-moment opnamen, worden in rekening gebracht tegen hetzelfde tarieven als actieve gegevens. Hoe versies worden gefactureerd, is afhankelijk van of u de laag expliciet hebt ingesteld voor de basis-BLOB of voor een van de versies (of moment opnamen). Zie [Azure Blob Storage: warme, cool en archief toegangs lagen](storage-blob-storage-tiers.md)voor meer informatie over blob-lagen.

Als u de laag van een BLOB of versie niet hebt gewijzigd, worden er kosten in rekening gebracht voor unieke gegevens blokken voor die blob, de bijbehorende versies en eventuele moment opnamen. Zie [Facturering wanneer de BLOB-laag niet expliciet is ingesteld](#billing-when-the-blob-tier-has-not-been-explicitly-set)voor meer informatie.

Als u de laag van een BLOB of versie hebt gewijzigd, wordt u gefactureerd voor het hele object, ongeacht of de BLOB en versie uiteindelijk weer in dezelfde laag staan. Zie [Facturering wanneer de BLOB-laag expliciet is ingesteld](#billing-when-the-blob-tier-has-been-explicitly-set)voor meer informatie.

> [!NOTE]
> Het inschakelen van versie beheer voor gegevens die regel matig worden overschreven, kan leiden tot hogere kosten voor de opslag capaciteit en een verhoogde latentie tijdens het aanbieden van bewerkingen. Sla vaak overschreven gegevens op in een afzonderlijk opslag account waarvoor versie beheer is uitgeschakeld om deze problemen te verhelpen.

Zie [BLOB-moment opnamen](snapshots-overview.md)voor meer informatie over de facturerings gegevens voor BLOB-moment opnamen.

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Facturering wanneer de BLOB-laag niet expliciet is ingesteld

Als u de BLOB-laag niet expliciet hebt ingesteld voor een basis-BLOB of een van de bijbehorende versies, worden er unieke blokken of pagina's in rekening gebracht voor de blob, de bijbehorende versies en eventuele moment opnamen. Gegevens die worden gedeeld via een BLOB en de versies ervan worden slechts één keer in rekening gebracht. Wanneer een BLOB wordt bijgewerkt, worden gegevens in een basis-BLOB afwijkt van de gegevens die zijn opgeslagen in de versie en worden de unieke gegevens per blok of pagina in rekening gebracht.

Wanneer u een blok in een blok-BLOB vervangt, wordt dat blok vervolgens als een uniek blok in rekening gebracht. Dit geldt ook als het blok dezelfde blok-ID en dezelfde gegevens bevat als in de vorige versie. Nadat het blok opnieuw is doorgevoerd, is het afwijkend van de tegen hanger in de vorige versie en worden er kosten in rekening gebracht voor de gegevens. Hetzelfde geldt voor een pagina in een pagina-blob die wordt bijgewerkt met identieke gegevens.

Blob-opslag is niet van een manier om te bepalen of twee blokken identieke gegevens bevatten. Elk blok dat wordt geüpload en doorgevoerd, wordt behandeld als uniek, zelfs als het dezelfde gegevens en dezelfde blok-ID heeft. Omdat kosten toenemen voor unieke blokken, is het belang rijk om te onthouden dat het bijwerken van een BLOB wanneer versie beheer is ingeschakeld, extra unieke blokken en extra kosten oplevert.

Wanneer BLOB-versie beheer is ingeschakeld, moet u update bewerkingen aanroepen op blok-blobs zodat ze het minst mogelijke aantal blokken bijwerken. De schrijf bewerkingen die nauw keurige controle over blokken toestaan, zijn [blok keren](/rest/api/storageservices/put-block) en [blokkerings lijst plaatsen](/rest/api/storageservices/put-block-list). De [put-BLOB](/rest/api/storageservices/put-blob) -bewerking, daarentegen, vervangt de volledige inhoud van een BLOB en kan dus leiden tot extra kosten.

De volgende scenario's laten zien hoe kosten worden toegerekend voor een blok-Blob en de bijbehorende versies wanneer de BLOB-laag niet expliciet is ingesteld.

#### <a name="scenario-1"></a>Scenario 1

In scenario 1 heeft de BLOB een vorige versie. De blob is niet bijgewerkt sinds de versie is gemaakt. Daarom worden er alleen kosten in rekening gebracht voor unieke blokken 1, 2 en 3.

![Diagram 1 met facturering voor unieke blokken in de basis-Blob en de vorige versie.](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenario 2

In scenario 2 is één blok (3 in het diagram) in de BLOB bijgewerkt. Hoewel het bijgewerkte blok dezelfde gegevens en dezelfde ID bevat, is dit niet hetzelfde als blok 3 in de vorige versie. Als gevolg hiervan wordt het account in rekening gebracht voor vier blokken.

![Diagram 2 met facturering voor unieke blokken in de basis-Blob en de vorige versie.](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenario 3

In scenario 3 is de BLOB bijgewerkt, maar de versie niet. Blok 3 is vervangen door blok 4 in de basis-blob, maar de vorige versie komt nog steeds overeen met blok 3. Als gevolg hiervan wordt het account in rekening gebracht voor vier blokken.

![Diagram 3 met facturering voor unieke blokken in de basis-Blob en de vorige versie.](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenario 4

In scenario 4 is de basis-BLOB volledig bijgewerkt en bevat deze geen van de oorspronkelijke blokken. Als gevolg hiervan wordt het account in rekening gebracht voor alle acht unieke blokken &mdash; vier in de basis-Blob en vier in de vorige versie. Dit scenario kan zich voordoen als u naar een BLOB schrijft met de [put-BLOB](/rest/api/storageservices/put-blob) -bewerking, omdat deze de volledige inhoud van de basis-BLOB vervangt.

![Diagram 4 met facturering voor unieke blokken in de basis-Blob en de vorige versie.](./media/versioning-overview/versions-billing-scenario-4.png)

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Facturering wanneer de BLOB-laag expliciet is ingesteld

Als u de BLOB-laag voor een BLOB of versie (of een moment opname) expliciet hebt ingesteld, worden er kosten in rekening gebracht voor de volledige inhouds lengte van het object in de nieuwe laag, ongeacht of het wordt geblokkeerd door een object in de oorspronkelijke laag. Ook worden er kosten in rekening gebracht voor de volledige inhouds lengte van de oudste versie in de oorspronkelijke laag. Alle andere eerdere versies of moment opnamen die in de oorspronkelijke laag blijven, worden in rekening gebracht voor unieke blokken die ze kunnen delen, zoals wordt beschreven in [Facturering wanneer de BLOB-laag niet expliciet is ingesteld](#billing-when-the-blob-tier-has-not-been-explicitly-set).

#### <a name="moving-a-blob-to-a-new-tier"></a>Een BLOB verplaatsen naar een nieuwe laag

In de volgende tabel wordt het facturerings gedrag voor een BLOB of versie beschreven wanneer het wordt verplaatst naar een nieuwe laag.

| Wanneer de BLOB-laag expliciet is ingesteld... | Vervolgens wordt u gefactureerd voor... |
|-|-|
| Een basis-blob met een vorige versie | De basis-Blob in de nieuwe laag en de oudste versie in de oorspronkelijke laag, plus eventuele unieke blokken in andere versies. <sup>1</sup> |
| Een basis-blob met een vorige versie en een moment opname | De basis-Blob in de nieuwe laag, de oudste versie in de oorspronkelijke laag en de oudste moment opname in de oorspronkelijke laag, plus eventuele unieke blokken in andere versies of moment opnamen<sup>1</sup>. |
| Een vorige versie | De versie in de nieuwe laag en de basis-Blob in de oorspronkelijke laag, plus eventuele unieke blokken in andere versies. <sup>1</sup> |

<sup>1</sup> Als er andere eerdere versies of moment opnamen zijn die niet zijn verplaatst uit de oorspronkelijke laag, worden deze versies of moment opnamen in rekening gebracht op basis van het aantal unieke blokken dat ze bevatten, zoals wordt beschreven in [Facturering wanneer de BLOB-laag niet expliciet is ingesteld](#billing-when-the-blob-tier-has-not-been-explicitly-set).

In het volgende diagram ziet u hoe objecten worden gefactureerd wanneer een blob met een versie wordt verplaatst naar een andere laag.

:::image type="content" source="media/versioning-overview/versioning-billing-tiers.png" alt-text="Diagram waarin wordt getoond hoe objecten worden gefactureerd wanneer een versie van een BLOB expliciet wordt gelaagd.":::

Het expliciet instellen van de laag voor een blob, versie of moment opname kan niet ongedaan worden gemaakt. Als u een BLOB naar een nieuwe laag verplaatst en vervolgens weer naar de oorspronkelijke laag verplaatst, worden er kosten in rekening gebracht voor de volledige lengte van de inhoud van het object, zelfs als er blokken met andere objecten in de oorspronkelijke laag worden gedeeld.

Bewerkingen die de laag van een blob, versie of moment opname expliciet instellen, zijn onder andere:

- [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (Blob-laag instellen)
- [BLOB](/rest/api/storageservices/put-blob) met opgegeven laag plaatsen
- [Blokkerings lijst](/rest/api/storageservices/put-block-list) met opgegeven laag plaatsen
- [BLOB kopiëren](/rest/api/storageservices/copy-blob) met opgegeven laag

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Een BLOB verwijderen wanneer de functie voor voorlopig verwijderen is ingeschakeld

Als de functie voor het voorlopig verwijderen van blobs is ingeschakeld, wordt de volledige inhouds lengte in rekening gebracht als u een basis-BLOB verwijdert of overschrijft waarvan de laag expliciet is ingesteld. Zie voor meer informatie over hoe BLOB versie en soft delete samen werken, [BLOB-versie beheer en zacht verwijderen](#blob-versioning-and-soft-delete).

In de volgende tabel wordt het facturerings gedrag voor een verwijderde BLOB beschreven, afhankelijk van het feit of versie beheer is ingeschakeld of uitgeschakeld. Wanneer versie beheer is ingeschakeld, wordt er een versie gemaakt wanneer een BLOB zacht wordt verwijderd. Wanneer versie beheer is uitgeschakeld, wordt door het zacht verwijderen van een BLOB een tijdelijke moment opname verwijderd.

| Wanneer u een basis-BLOB overschrijft waarbij de laag expliciet is ingesteld... | Vervolgens wordt u gefactureerd voor... |
|-|-|
| Als de functie voor het voorlopig verwijderen van blobs en versie beheer beide zijn ingeschakeld | Alle bestaande versies met volledige inhouds lengte, ongeacht de laag. |
| Als de functie voor het voorlopig verwijderen van BLOB is ingeschakeld, maar versie beheer is uitgeschakeld | Alle bestaande tijdelijke moment opnamen met een volledige inhouds lengte, ongeacht de laag, worden verwijderd. |

## <a name="see-also"></a>Zie ook

- [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)
- [Een moment opname van een BLOB maken](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Zacht verwijderen voor Azure Storage-blobs](./soft-delete-blob-overview.md)