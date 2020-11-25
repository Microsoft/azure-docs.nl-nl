---
title: BLOB-versie beheer
titleSuffix: Azure Storage
description: Versie beheer van Blob-opslag houdt automatisch eerdere versies van een object bij en identificeert deze met tijds tempels. U kunt eerdere versies van een BLOB herstellen om uw gegevens te herstellen als deze ten onrechte zijn gewijzigd of verwijderd.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e2d74519b9adf9a74e5af180a3da28918a9a8dab
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96001878"
---
# <a name="blob-versioning"></a>BLOB-versie beheer

U kunt versie beheer van Blob Storage inschakelen om automatisch eerdere versies van een object te onderhouden.  Wanneer BLOB-versie beheer is ingeschakeld, kunt u een eerdere versie van een BLOB herstellen om uw gegevens te herstellen als deze ten onrechte zijn gewijzigd of verwijderd.

BLOB-versie beheer is ingeschakeld voor het opslag account en is van toepassing op alle blobs in het opslag account. Nadat u BLOB-versie beheer voor een opslag account hebt ingeschakeld, onderhoudt Azure Storage automatisch versies voor elke Blob in het opslag account.

Micro soft raadt aan om BLOB-versie beheer te gebruiken om eerdere versies van een BLOB voor superieure gegevens beveiliging te behouden. Gebruik, indien mogelijk, Blob-versie beheer in plaats van BLOB-moment opnamen om vorige versies te behouden. BLOB-moment opnamen bieden vergelijk bare functionaliteit als ze eerdere versies van een BLOB behouden, maar moment opnamen moeten hand matig worden bewaard door uw toepassing.

Zie [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)voor meer informatie over het inschakelen van BLOB-versies.

> [!IMPORTANT]
> BLOB-versie beheer kan u niet helpen bij het herstellen van een onbedoelde verwijdering van een opslag account of container. Als u onbedoeld verwijderen van het opslag account wilt voor komen, configureert u een **CannotDelete** -vergren deling voor de bron van het opslag account. Zie [resources vergren delen om onverwachte wijzigingen](../../azure-resource-manager/management/lock-resources.md)te voor komen voor meer informatie over het vergren delen van Azure-resources.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-blob-versioning-works"></a>Hoe BLOB versie beheer werkt

Een versie legt de status van een BLOB op een bepaald moment vast. Wanneer BLOB-versie beheer is ingeschakeld voor een opslag account, maakt Azure Storage automatisch een nieuwe versie van een BLOB wanneer die BLOB wordt gewijzigd of verwijderd.

Wanneer u een BLOB maakt waarvoor versie beheer is ingeschakeld, is de nieuwe BLOB de huidige versie van de BLOB (of de basis-blob). Als u deze BLOB vervolgens wijzigt, maakt Azure Storage een versie die de status van de BLOB vastlegt voordat deze is gewijzigd. De gewijzigde BLOB wordt de nieuwe huidige versie. Telkens wanneer u de BLOB wijzigt, wordt een nieuwe versie gemaakt.

Een groot aantal versies per Blob kan de latentie verhogen voor bewerkingen in BLOB-vermeldingen. Micro soft raadt aan om minder dan 1000 versies per BLOB te onderhouden. U kunt levenscyclus beheer gebruiken om oude versies automatisch te verwijderen. Zie [kosten optimaliseren door Azure Blob Storage Access-lagen te automatiseren](storage-lifecycle-management-concepts.md)voor meer informatie over levenscyclus beheer.

Wanneer u een BLOB verwijdert waarvoor versie beheer is ingeschakeld, wordt door Azure Storage een versie gemaakt die de status van de BLOB vastlegt voordat deze werd verwijderd. De huidige versie van de BLOB wordt vervolgens verwijderd, maar de versies van de BLOB blijven behouden, zodat deze indien nodig opnieuw kunnen worden gemaakt. 

BLOB-versies zijn onveranderbaar. U kunt de inhoud of meta gegevens van een bestaande BLOB-versie niet wijzigen.

BLOB-versie beheer is beschikbaar voor algemeen gebruik v2-, blok-Blob-en Blob Storage-accounts. Opslag accounts met een hiërarchische naam ruimte die is ingeschakeld voor gebruik met Azure Data Lake Storage Gen2 worden momenteel niet ondersteund.

Versie 2019-10-10 en hoger van de Azure Storage REST API ondersteunt BLOB-versie beheer.

### <a name="version-id"></a>Versie-ID

Elke BLOB-versie wordt geïdentificeerd met een versie-ID. De waarde van de versie-ID is het tijds tempel waarop de blob is geschreven of bijgewerkt. De versie-ID wordt toegewezen op het moment dat de versie wordt gemaakt.

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

### <a name="versioning-on-delete-operations"></a>Versie beheer voor Delete-bewerkingen

Wanneer u een BLOB verwijdert, wordt de huidige versie van de BLOB een vorige versie en wordt de basis-BLOB verwijderd. Alle bestaande vorige versies van de BLOB blijven behouden wanneer de BLOB wordt verwijderd.

Als u de bewerking [Delete BLOB](/rest/api/storageservices/delete-blob) aanroept zonder een versie-id, wordt de basis-BLOB verwijderd. Als u een specifieke versie wilt verwijderen, geeft u de ID voor die versie op in de Verwijder bewerking.

In het volgende diagram ziet u het effect van een Verwijder bewerking op een blob met een versie:

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="Diagram waarin de verwijdering van de versie van de BLOB wordt verwijderd.":::

Door nieuwe gegevens naar de BLOB te schrijven, maakt u een nieuwe versie van de blob. Eventuele bestaande versies worden niet beïnvloed, zoals wordt weer gegeven in het volgende diagram.

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="Diagram van het opnieuw maken van de versie-BLOB na verwijdering.":::

### <a name="blob-types"></a>Blob-typen

Wanneer BLOB-versie beheer is ingeschakeld voor een opslag account, activeren alle schrijf-en verwijder bewerkingen op blok-blobs het maken van een nieuwe versie, met uitzonde ring van de [put-blok](/rest/api/storageservices/put-block) bewerking.

Voor pagina-blobs en toevoeg-blobs wordt alleen een subset van schrijf-en verwijder bewerkingen geactiveerd waarmee een versie wordt gemaakt. Deze bewerkingen zijn onder andere:

- [BLOB plaatsen](/rest/api/storageservices/put-blob)
- [Blokkerings lijst plaatsen](/rest/api/storageservices/put-block-list)
- [BLOB verwijderen](/rest/api/storageservices/delete-blob)
- [BLOB-meta gegevens instellen](/rest/api/storageservices/set-blob-metadata)
- [BLOB kopiëren](/rest/api/storageservices/copy-blob)

Met de volgende bewerkingen wordt het maken van een nieuwe versie niet geactiveerd. Als u wijzigingen van deze bewerkingen wilt vastleggen, moet u een hand matige moment opname maken:

- [Put-pagina](/rest/api/storageservices/put-page) (pagina-blob)
- [Blok toevoegen](/rest/api/storageservices/append-block) (toevoeg-blob)

Alle versies van een BLOB moeten van hetzelfde type BLOB zijn. Als een BLOB vorige versies heeft, kunt u een BLOB van een type niet overschrijven met een ander type, tenzij u eerst de BLOB en alle versies ervan verwijdert.

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

BLOB-versie beheer en dynamische BLOB-verwijdering werken samen om u te voorzien van optimale gegevens beveiliging. Wanneer u zacht verwijderen inschakelt, geeft u op hoe lang Azure Storage een voorlopig verwijderde BLOB moet bewaren. Een met Soft verwijderde BLOB-versie blijft in het systeem en kan worden verwijderd binnen de tijdelijke verwijderings periode. Zie voor meer informatie over het verwijderen van blobs [voorlopig verwijderen voor Azure Storage-blobs](./soft-delete-blob-overview.md).

### <a name="deleting-a-blob-or-version"></a>Een BLOB of versie verwijderen

Zacht verwijderen biedt extra beveiliging voor het verwijderen van BLOB-versies. Als versie beheer en voorlopig verwijderen zijn ingeschakeld op het opslag account, maakt Azure Storage een nieuwe versie wanneer u een BLOB verwijdert om de status van de BLOB direct vóór het verwijderen op te slaan en wordt de huidige versie verwijderd. De nieuwe versie wordt niet zacht verwijderd en wordt niet verwijderd wanneer de tijdelijke Bewaar periode voor het verwijderen is verlopen.

Wanneer u een vorige versie van de BLOB verwijdert, wordt de versie zacht verwijderd. De zachte verwijderde versie wordt behouden gedurende de Bewaar periode die is opgegeven in de instellingen voor voorlopig verwijderen voor het opslag account en wordt permanent verwijderd wanneer de tijdelijke verwijderings periode voor verwijderen is verlopen.

Als u een vorige versie van een BLOB wilt verwijderen, verwijdert u deze expliciet door de versie-ID op te geven.

In het volgende diagram ziet u wat er gebeurt wanneer u een BLOB of een BLOB-versie verwijdert.

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="Diagram van het verwijderen van een versie waarvoor zacht verwijderen is ingeschakeld.":::

Als versie beheer en voorlopig verwijderen zijn ingeschakeld voor een opslag account, wordt er geen voorlopig verwijderde moment opname gemaakt wanneer een BLOB of BLOB-versie wordt gewijzigd of verwijderd.

### <a name="restoring-a-soft-deleted-version"></a>Een voorlopig verwijderde versie herstellen

U kunt een voorlopig verwijderde BLOB-versie herstellen door de bewerking voor het [ongedaan](/rest/api/storageservices/undelete-blob) maken van de BLOB op de versie aan te roepen terwijl de Bewaar periode voor de tijdelijke verwijdering van kracht is. Met de bewerking **BLOB verwijderen** worden alle voorlopig verwijderde versies van de BLOB hersteld.

Als u de voorlopig verwijderde versies herstelt met de bewerking voor het **ongedaan** maken van de blob, wordt een versie niet gepromoveerd tot de huidige versie. Als u de huidige versie wilt herstellen, moet u eerst alle voorlopig verwijderde versies herstellen en vervolgens de bewerking [BLOB kopiëren](/rest/api/storageservices/copy-blob) gebruiken om een vorige versie te kopiëren om de BLOB te herstellen.

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
| De huidige versie van de BLOB verwijderen | BLOB verwijderen | **Micro soft. Storage/Storage accounts/blobServices/containers/blobs/verwijderen** | Inzender voor Storage Blob-gegevens |
| Een versie verwijderen | BLOB verwijderen | **Micro soft. Storage/Storage accounts/blobServices/containers/blobs/deleteBlobVersion/Action** | Eigenaar van opslagblobgegevens |

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