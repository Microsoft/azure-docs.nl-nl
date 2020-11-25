---
title: Blobs voorlopig verwijderen
titleSuffix: Azure Storage
description: Met zacht verwijderen voor blobs worden uw gegevens beschermd, zodat u uw gegevens eenvoudiger kunt herstellen wanneer deze foutief worden gewijzigd of verwijderd door een toepassing of door een andere gebruiker van het opslag account.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: bb90c5776e67c1ba8fecdbf394a8098e96ca0652
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96022374"
---
# <a name="soft-delete-for-blobs"></a>Blobs voorlopig verwijderen

Met zacht verwijderen voor blobs wordt voor komen dat uw gegevens per ongeluk of onbedoeld worden gewijzigd of verwijderd. Als zacht verwijderen voor blobs is ingeschakeld voor een opslag account, kunnen blobs, Blob-versies en moment opnamen in dat opslag account worden hersteld nadat ze zijn verwijderd, binnen een Bewaar periode die u opgeeft.

Als er een kans is dat uw gegevens per ongeluk worden gewijzigd of verwijderd door een toepassing of een ander opslag account, raadt micro soft aan om de functie voor voorlopig verwijderen in te scha kelen. Zie voor meer informatie over het inschakelen van zacht verwijderen [voor het inschakelen en beheren van voorlopig verwijderen voor blobs](./soft-delete-blob-enable.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-soft-delete-for-blobs"></a>Over zacht verwijderen voor blobs

Als zacht verwijderen voor blobs is ingeschakeld voor een opslag account, kunt u objecten herstellen nadat ze zijn verwijderd binnen de opgegeven Bewaar periode voor gegevens. Deze beveiliging wordt uitgebreid naar alle blobs (blok-blobs, toevoeg-blobs of pagina-blobs) die worden gewist als gevolg van een overschrijving.

Als de gegevens in een bestaande BLOB of moment opname worden verwijderd terwijl BLOB zacht verwijderen is ingeschakeld, maar BLOB-versie beheer niet is ingeschakeld, wordt er een voorlopig verwijderde moment opname gegenereerd om de status van de overschreven gegevens op te slaan. Nadat de opgegeven Bewaar periode is verlopen, wordt het object definitief verwijderd.

Als blob-versie beheer en dynamisch verwijderen van BLOB zijn ingeschakeld voor het opslag account, wordt door het verwijderen van een BLOB een nieuwe versie gemaakt in plaats van een tijdelijke, verwijderde moment opname. De nieuwe versie wordt niet zacht verwijderd en wordt niet verwijderd wanneer de tijdelijke Bewaar periode voor het verwijderen is verlopen. Zacht verwijderde versies van een BLOB kunnen worden hersteld binnen de Bewaar periode door de bewerking voor het [ongedaan](/rest/api/storageservices/undelete-blob) maken van de BLOB aan te roepen. De Blob kan vervolgens worden hersteld vanuit een van de versies door de bewerking [BLOB kopiëren](/rest/api/storageservices/copy-blob) aan te roepen. Zie voor meer informatie over het samen voegen van BLOB-versie en zacht verwijderen, [BLOB-versie beheer en soft verwijderen](versioning-overview.md#blob-versioning-and-soft-delete).

Voorlopig verwijderde objecten zijn onzichtbaar, tenzij expliciet wordt vermeld.

Zacht verwijderen van BLOB is achterwaarts compatibel, zodat u geen wijzigingen hoeft aan te brengen in uw toepassingen om te kunnen profiteren van de beveiligingen die door deze functie worden geboden. Met [gegevens herstel](#recovery) wordt echter een nieuwe **undelete BLOB** API geïntroduceerd.

De functie voor het voorlopig verwijderen van blobs is beschikbaar voor zowel nieuwe als bestaande v2-, algemeen-en Blob Storage-accounts voor algemeen gebruik. Standaard-en Premium-account typen worden ondersteund. Zacht verwijderen voor blob is beschikbaar voor alle opslag lagen, waaronder hot, cool en Archive. Zacht verwijderen is beschikbaar voor niet-beheerde schijven, wat pagina-blobs zijn onder de voor vallen, maar niet beschikbaar is voor beheerde schijven.

### <a name="configuration-settings"></a>Configuratie-instellingen

Wanneer u een nieuw account maakt, is zacht verwijderen standaard uitgeschakeld. Voorlopig verwijderen is ook standaard uitgeschakeld voor bestaande opslag accounts. U kunt het voorlopig verwijderen van een opslag account op elk gewenst moment in-of uitschakelen.

Wanneer u zacht verwijderen inschakelt, moet u de retentie periode configureren. De retentie periode geeft de hoeveelheid tijd aan dat de gegevens die door zacht worden verwijderd worden opgeslagen en beschikbaar zijn voor herstel. Voor objecten die expliciet worden verwijderd, wordt de klok van de Bewaar periode gestart wanneer de gegevens worden verwijderd. Voor soft-verwijderde versies of moment opnamen die zijn gegenereerd door de functie voor voorlopig verwijderen wanneer gegevens worden overschreven, wordt de klok gestart wanneer de versie of moment opname wordt gegenereerd. De Bewaar periode kan tussen 1 en 365 dagen liggen.

U kunt de tijdelijke Bewaar periode voor verwijderen op elk gewenst moment wijzigen. Een bijgewerkte Bewaar periode is alleen van toepassing op nieuwe verwijderde gegevens. Eerder verwijderde gegevens verloopt op basis van de Bewaar periode die is geconfigureerd tijdens het verwijderen van de gegevens. Het verwijderen van een voorlopig verwijderd object heeft geen invloed op de verloop tijd.

Als u zacht verwijderen uitschakelt, kunt u door gaan met het openen en herstellen van dynamische verwijderde gegevens in uw opslag account dat is opgeslagen toen de functie werd ingeschakeld.

### <a name="saving-deleted-data"></a>Verwijderde gegevens worden opgeslagen

Met zacht verwijderen worden uw gegevens in veel gevallen bewaard, waarbij objecten worden verwijderd of overschreven.

Wanneer een BLOB wordt overschreven met behulp van **put-BLOB**, **put-blok lijst** of **Kopieer-BLOB**, wordt er automatisch een versie of moment opname van de status van de BLOB vóór de schrijf bewerking gegenereerd. Dit object is onzichtbaar, tenzij tijdelijke verwijderde objecten expliciet worden weer gegeven. Zie de sectie [herstel](#recovery) voor meer informatie over het weer geven van zachte verwijderde objecten.

![Een diagram waarin wordt getoond hoe moment opnamen van blobs worden opgeslagen wanneer ze worden overschreven met behulp van put-blob, put-blok lijst of kopiëren van BLOB.](media/soft-delete-blob-overview/storage-blob-soft-delete-overwrite.png)

*Zacht verwijderde gegevens zijn grijs, terwijl actieve gegevens blauw zijn. Meer recent geschreven gegevens worden onder oudere gegevens weer gegeven. Als B0 wordt overschreven met B1, wordt er een voorlopig verwijderde moment opname van B0 gegenereerd. Als B1 wordt overschreven met B2, wordt er een voorlopig verwijderde moment opname van B1 gegenereerd.*

> [!NOTE]  
> Bij zacht verwijderen wordt de beveiliging van de overschrijving voor kopieer bewerkingen alleen geboden wanneer deze is ingeschakeld voor het account van de doel-blob.

> [!NOTE]  
> Bij zacht verwijderen wordt de beveiliging van blobs in de opslaglaag niet overschreven. Als een BLOB in archief wordt overschreven door een nieuwe Blob in een wille keurige laag, wordt de overschreven BLOB permanent verlopen.

Wanneer **Delete BLOB** wordt aangeroepen voor een moment opname, wordt die moment opname gemarkeerd als zacht verwijderd. Er wordt geen nieuwe moment opname gegenereerd.

![Een diagram waarin wordt getoond hoe moment opnamen van blobs zacht worden verwijderd bij het gebruik van delete blob.](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Zacht verwijderde gegevens zijn grijs, terwijl actieve gegevens blauw zijn. Meer recent geschreven gegevens worden onder oudere gegevens weer gegeven. Als **moment opname-BLOB** wordt aangeroepen, wordt B0 een moment opname en B1 de actieve status van de blob. Wanneer de B0-moment opname wordt verwijderd, wordt deze als zacht verwijderd gemarkeerd.*

Wanneer **Delete BLOB** wordt aangeroepen op een basis-BLOB (een blob die niet zelf een moment opname is), wordt die BLOB gemarkeerd als zacht verwijderd. Consistent met het vorige gedrag: het aanroepen van **Delete BLOB** op een blob met actieve moment opnamen retourneert een fout. Als u **Delete BLOB** aanroept voor een blob met tijdelijke verwijderde moment opnamen, wordt er geen fout geretourneerd. U kunt nog steeds een BLOB en alle bijbehorende moment opnamen in één bewerking verwijderen wanneer de functie voor voorlopig verwijderen is ingeschakeld. Hiermee markeert u de basis-Blob en moment opnamen als zacht verwijderd.

![Een diagram waarin wordt weer gegeven wat er gebeurt wanneer blog verwijderen wordt aangeroepen op een basis-blob.](media/soft-delete-blob-overview/storage-blob-soft-delete-explicit-include.png)

*Zacht verwijderde gegevens zijn grijs, terwijl actieve gegevens blauw zijn. Meer recent geschreven gegevens worden onder oudere gegevens weer gegeven. Hier wordt een **Delete BLOB** -aanroep gemaakt om B2 en alle bijbehorende moment opnamen te verwijderen. De actieve blob, B2 en alle bijbehorende moment opnamen zijn gemarkeerd als zacht verwijderd.*

> [!NOTE]  
> Wanneer een zachte verwijderde BLOB wordt overschreven, wordt er automatisch een voorlopig verwijderde moment opname van de status van de BLOB vóór de schrijf bewerking gegenereerd. De nieuwe BLOB neemt de laag over van de overschreven blob.

Met zacht verwijderen worden uw gegevens niet opgeslagen in gevallen waarin containers of accounts worden verwijderd, noch wanneer meta gegevens en BLOB-eigenschappen worden overschreven. Als u een opslag account wilt beveiligen tegen foutieve verwijdering, kunt u een vergren deling configureren met behulp van de Azure Resource Manager. Zie voor meer informatie de Azure Resource Manager artikel [vergren delen resources om onverwachte wijzigingen te voor komen](../../azure-resource-manager/management/lock-resources.md).

De volgende tabel bevat details over het verwachte gedrag wanneer zacht verwijderen is ingeschakeld:

| REST API bewerking | Resourcetype | Description | Wijziging in gedrag |
|--------------------|---------------|-------------|--------------------|
| [Verwijderen](/rest/api/storagerp/StorageAccounts/Delete) | Account | Hiermee verwijdert u het opslag account, inclusief alle containers en blobs die het bevat.                           | Geen wijziging. Containers en blobs in het verwijderde account kunnen niet worden hersteld. |
| [Container verwijderen](/rest/api/storageservices/delete-container) | Container | Hiermee verwijdert u de container, inclusief alle blobs die deze bevat. | Geen wijziging. Blobs in de verwijderde container kunnen niet worden hersteld. |
| [BLOB plaatsen](/rest/api/storageservices/put-blob) | Blok-, toevoeg-en pagina-blobs | Hiermee maakt u een nieuwe BLOB of vervangt u een bestaande BLOB binnen een container | Als deze wordt gebruikt om een bestaande BLOB te vervangen, wordt automatisch een moment opname van de status van de BLOB vóór de aanroep gegenereerd. Dit geldt ook voor een eerder zachte verwijderde BLOB als en alleen als deze wordt vervangen door een BLOB van hetzelfde type (blok, toevoegen of pagina). Als deze wordt vervangen door een BLOB van een ander type, worden alle bestaande voorlopig verwijderde gegevens permanent verlopen. |
| [BLOB verwijderen](/rest/api/storageservices/delete-blob) | Blok-, toevoeg-en pagina-blobs | Hiermee wordt een BLOB-of BLOB-moment opname gemarkeerd voor verwijdering. De BLOB of moment opname wordt later verwijderd tijdens het opschonen van de verzameling | Als deze wordt gebruikt om een BLOB-moment opname te verwijderen, wordt die moment opname gemarkeerd als zacht verwijderd. Als deze wordt gebruikt om een BLOB te verwijderen, wordt die BLOB gemarkeerd als zacht verwijderd. |
| [BLOB kopiëren](/rest/api/storageservices/copy-blob) | Blok-, toevoeg-en pagina-blobs | Kopieert een bron-BLOB naar een bestemmings-Blob in hetzelfde opslag account of in een ander opslag account. | Als deze wordt gebruikt om een bestaande BLOB te vervangen, wordt automatisch een moment opname van de status van de BLOB vóór de aanroep gegenereerd. Dit geldt ook voor een eerder zachte verwijderde BLOB als en alleen als deze wordt vervangen door een BLOB van hetzelfde type (blok, toevoegen of pagina). Als deze wordt vervangen door een BLOB van een ander type, worden alle bestaande voorlopig verwijderde gegevens permanent verlopen. |
| [Blok keren](/rest/api/storageservices/put-block) | Blok-blobs | Hiermee maakt u een nieuw blok dat moet worden doorgevoerd als onderdeel van een blok-blob. | Als wordt gebruikt om een blok door te voeren op een blob die actief is, is er geen wijziging. Als wordt gebruikt om een blok door te voeren op een blob die zacht is verwijderd, wordt een nieuwe BLOB gemaakt en wordt automatisch een moment opname gegenereerd om de status van de zachte verwijderde BLOB vast te leggen. |
| [Blokkerings lijst plaatsen](/rest/api/storageservices/put-block-list) | Blok-blobs | Hiermee wordt een BLOB doorgevoerd door de set blok-Id's op te geven waaruit de blok-BLOB bestaat. | Als deze wordt gebruikt om een bestaande BLOB te vervangen, wordt automatisch een moment opname van de status van de BLOB vóór de aanroep gegenereerd. Dit geldt ook voor een eerder zachte verwijderde BLOB als en alleen als dit een blok-blob is. Als deze wordt vervangen door een BLOB van een ander type, worden alle bestaande voorlopig verwijderde gegevens permanent verlopen. |
| [Pagina plaatsen](/rest/api/storageservices/put-page) | Pagina-blobs | Schrijft een bereik van pagina's naar een pagina-blob. | Geen wijziging. Pagina-BLOB-gegevens die worden overschreven of gewist met deze bewerking, worden niet opgeslagen en kunnen niet worden hersteld. |
| [Blok toevoegen](/rest/api/storageservices/append-block) | Toevoeg-blobs | Schrijft een gegevens blok naar het einde van een toevoeg-BLOB | Geen wijziging. |
| [BLOB-eigenschappen instellen](/rest/api/storageservices/set-blob-properties) | Blok-, toevoeg-en pagina-blobs | Hiermee stelt u waarden in voor systeem eigenschappen die zijn gedefinieerd voor een blob. | Geen wijziging. Overschreven BLOB-eigenschappen kunnen niet worden hersteld. |
| [BLOB-meta gegevens instellen](/rest/api/storageservices/set-blob-metadata) | Blok-, toevoeg-en pagina-blobs | Hiermee stelt u door de gebruiker gedefinieerde meta gegevens voor de opgegeven Blob in als een of meer naam/waarde-paren. | Geen wijziging. Overschreven BLOB-meta gegevens kunnen niet worden hersteld. |

Het is belang rijk om te zien dat het aanroepen van de **pagina** voor het oproepen van een pagina-BLOB niet automatisch moment opnamen genereert. Virtuele-machine schijven worden ondersteund door pagina-blobs en de **put-pagina** gebruiken om gegevens te schrijven.

### <a name="recovery"></a>Herstel

Bij het aanroepen van de bewerking voor het [ongedaan](/rest/api/storageservices/undelete-blob) maken van de BLOB voor een voorlopig verwijderde basis-BLOB worden deze hersteld en alle gekoppelde tijdelijke verwijderde moment opnamen als actief. Aanroepen van de bewerking voor het **ongedaan** maken van een BLOB op een actieve basis-BLOB herstelt alle gekoppelde tijdelijke verwijderde moment opnamen als actief. Wanneer moment opnamen als actief worden teruggezet, zien ze eruit als door de gebruiker gegenereerde moment opnamen. de basis-BLOB wordt niet overschreven.

Als u een BLOB wilt herstellen naar een specifieke voorlopig verwijderde moment opname, kunt u de **BLOB undelete** aanroepen op de basis-blob. Vervolgens kunt u de moment opname kopiëren over de nu actieve blob. U kunt de moment opname ook kopiëren naar een nieuwe blob.

![Een diagram waarin wordt weer gegeven wat er gebeurt wanneer BLOB verwijderen wordt gebruikt.](media/soft-delete-blob-overview/storage-blob-soft-delete-recover.png)

*Zacht verwijderde gegevens zijn grijs, terwijl actieve gegevens blauw zijn. Meer recent geschreven gegevens worden onder oudere gegevens weer gegeven. Hier wordt de **verwijdering van BLOB ongedaan** gemaakt op BLOB B, waardoor de basis-blob, B1 en alle bijbehorende moment opnamen, hier net als actief, wordt hersteld. In de tweede stap wordt B0 gekopieerd over de basis-blob. Met deze Kopieer bewerking wordt een voorlopig verwijderde moment opname van B1 gegenereerd.*

Als u de voorlopig verwijderde blobs en BLOB-moment opnamen wilt weer geven, kunt u ervoor kiezen om verwijderde gegevens in **lijst-blobs** op te nemen. U kunt ervoor kiezen om alleen voorlopig verwijderde basis-blobs weer te geven, of om ook te voorzien in voorlopig verwijderde BLOB-moment opnamen. Voor alle voorlopig verwijderde gegevens kunt u de tijd bekijken waarop de gegevens zijn verwijderd, evenals het aantal dagen voordat de gegevens permanent verlopen.

### <a name="example"></a>Voorbeeld

Hieronder vindt u de console-uitvoer van een .NET-script dat uploadt, overschrijft, moment opnamen, verwijderen en terugzetten van een blob met de naam *HelloWorld* wanneer zacht verwijderen is ingeschakeld:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Zie de sectie [volgende stappen](#next-steps) voor een verwijzing naar de toepassing die deze uitvoer heeft geproduceerd.

## <a name="pricing-and-billing"></a>Prijzen en facturering

Alle voorlopig verwijderde gegevens worden gefactureerd tegen hetzelfde aantal als actieve gegevens. Er worden geen kosten in rekening gebracht voor gegevens die permanent worden verwijderd na de geconfigureerde Bewaar periode. Zie [informatie over het samen voegen van moment](./snapshots-overview.md)opnamen voor een diep gaande kennis van moment opnamen en hoe de kosten worden samengevoegd.

U wordt niet gefactureerd voor de trans acties die betrekking hebben op het automatisch genereren van moment opnamen. Er worden kosten in rekening gebracht voor het **verwijderen van BLOB** -trans acties tegen de snelheid voor schrijf bewerkingen.

Bekijk de [pagina met prijzen voor azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over de prijzen voor Azure Blob Storage in het algemeen.

Wanneer u voorlopig verwijderen voor het eerst inschakelt, wordt u aangeraden een korte Bewaar periode te gebruiken om beter te begrijpen hoe de functie uw factuur beïnvloedt.

Het inschakelen van zacht verwijderen voor vaak overschreven gegevens kan leiden tot hogere kosten voor opslag capaciteit en een grotere latentie bij het weer geven van blobs. U kunt deze extra kosten en latentie verminderen door de vaak overschreven gegevens op te slaan in een afzonderlijk opslag account, waar zacht verwijderen is uitgeschakeld.

## <a name="faq"></a>Veelgestelde vragen

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Kan ik de API set BLOB-laag gebruiken om blobs laag te maken met tijdelijke verwijderde moment opnamen?

Ja. De voorlopig verwijderde moment opnamen blijven in de oorspronkelijke laag, maar de basis-BLOB wordt verplaatst naar de nieuwe laag.

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Premium Storage-accounts hebben een limiet van 100 voor de BLOB-moment opname. Zijn er voorlopig verwijderde moment opnamen in de buurt van deze limiet?

Nee, tijdelijke verwijderde moment opnamen tellen niet mee aan deze limiet.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Als ik een hele account of container Verwijder waarvoor Soft zacht verwijderen is ingeschakeld, worden alle bijbehorende blobs opgeslagen?

Nee, als u een hele account of container verwijdert, worden alle gekoppelde blobs definitief verwijderd. Zie [resources vergren delen om onverwachte wijzigingen te voor komen](../../azure-resource-manager/management/lock-resources.md)voor meer informatie over het beveiligen van een opslag account tegen onbedoeld verwijderen.

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Kan ik de metrische gegevens voor de capaciteit weer geven voor verwijderde data?

Zacht verwijderde gegevens worden opgenomen als onderdeel van de totale capaciteit van de opslag account. Zie [Opslaganalyse](../common/storage-analytics.md)voor meer informatie over het volgen en bewaken van opslag capaciteit.

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Kan ik voorlopig verwijderde moment opnamen van mijn BLOB lezen en kopiëren?  

Ja, maar u moet eerst undelete aanroepen op de blob.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>Is er een tijdelijke verwijdering beschikbaar voor virtuele machine-schijven?  

Zacht verwijderen is beschikbaar voor zowel Premium als standaard niet-beheerde schijven. Dit zijn pagina-blobs onder de kaften. Met voorlopig verwijderen kunt u alleen gegevens herstellen die zijn verwijderd door **BLOB verwijderen**, **BLOB plaatsen**, **blokkerings lijst plaatsen** en BLOB-bewerkingen **kopiëren** . Gegevens die worden overschreven door een aanroep naar de **put-pagina** , kunnen niet worden hersteld.

Een virtuele machine van Azure schrijft naar een niet-beheerde schijf met behulp van aanroepen om de **pagina te plaatsen**. met de opdracht voorlopig verwijderen kunt u schrijf bewerkingen naar een niet-beheerde schijf van een Azure-VM ongedaan maken.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Moet ik mijn bestaande toepassingen wijzigen voor het gebruik van zacht verwijderen?

Het is mogelijk om te profiteren van de functie voor voorlopig verwijderen, ongeacht de API-versie die u gebruikt. Om voorlopig verwijderde blobs en BLOB-moment opnamen weer te geven en te herstellen, moet u versie 2017-07-29 van de [Azure Storage rest API](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) of hoger gebruiken. Micro soft adviseert altijd de nieuwste versie van de Azure Storage-API te gebruiken.

## <a name="next-steps"></a>Volgende stappen

- [Voorlopig verwijderen inschakelen voor blobs](./soft-delete-blob-enable.md)
- [BLOB-versie beheer](versioning-overview.md)