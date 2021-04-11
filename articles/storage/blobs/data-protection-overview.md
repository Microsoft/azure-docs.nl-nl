---
title: Overzicht van gegevensbescherming
titleSuffix: Azure Storage
description: Met de opties voor gegevens beveiliging die beschikbaar zijn voor uw Blob Storage en Azure Data Lake Storage Gen2 gegevens kunt u uw gegevens beveiligen tegen verwijderen of overschreven. Als u gegevens moet herstellen die zijn verwijderd of overschreven, kan deze hand leiding u helpen bij het kiezen van de herstel optie die het meest geschikt is voor uw scenario.
services: storage
author: tamram
ms.service: storage
ms.date: 03/22/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: prishet
ms.subservice: common
ms.openlocfilehash: afd98e629500bc90cc9ddd1ed4ab2472f733e845
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803714"
---
# <a name="data-protection-overview"></a>Overzicht van gegevensbescherming

Azure Storage biedt gegevens beveiliging voor Blob Storage en Azure Data Lake Storage Gen2 om u te helpen bij het voorbereiden van scenario's waarin u gegevens moet herstellen die zijn verwijderd of overschreven. Het is belang rijk om te zien hoe u uw gegevens het beste kunt beschermen voordat een incident zich voordoet. Met deze hand leiding kunt u vooraf bepalen welke functies voor gegevens beveiliging voor uw scenario vereist zijn en hoe u deze implementeert. Als u gegevens moet herstellen die zijn verwijderd of overschreven, biedt dit overzicht ook richt lijnen over hoe u kunt door gaan, op basis van uw scenario.

In de Azure Storage-documentatie verwijst de *gegevens bescherming* naar strategieën voor het beveiligen van het opslag account en de gegevens erin dat ze kunnen worden verwijderd of gewijzigd, of voor het herstellen van gegevens nadat deze zijn verwijderd of gewijzigd. Azure Storage biedt ook opties voor *herstel na nood gevallen*, met inbegrip van meerdere niveaus van redundantie om uw gegevens te beschermen tegen service storingen als gevolg van hardwareproblemen of natuur rampen, en door de klant beheerde failover in het geval dat het Data Center in de primaire regio niet beschikbaar is. Zie [nood herstel](#disaster-recovery)voor meer informatie over hoe uw gegevens worden beveiligd tegen storingen in de service.

## <a name="recommendations-for-basic-data-protection"></a>Aanbevelingen voor basis gegevens beveiliging

Als u op zoek bent naar de basis dekking voor gegevens beveiliging voor uw opslag account en de gegevens die deze bevat, raadt micro soft aan de volgende stappen te nemen om te beginnen met:

- Configureer een Azure Resource Manager vergrendeling op het opslag account om het account te beveiligen tegen verwijdering of configuratie wijzigingen. [Meer informatie...](../common/lock-account-resource.md)
- Schakel het tijdelijk verwijderen van een container in voor het opslag account om een verwijderde container en de inhoud ervan te herstellen. [Meer informatie...](soft-delete-container-enable.md)
- Sla de status van een BLOB op regel matige intervallen op:
  - Schakel voor Blob Storage werk belastingen BLOB-versie beheer in om automatisch de status van uw gegevens op te slaan wanneer een BLOB wordt verwijderd of overschreven. [Meer informatie...](versioning-enable.md)
  - Maak voor Azure Data Lake Storage workloads hand matige moment opnamen om de status van uw gegevens op een bepaald moment op te slaan. [Meer informatie...](snapshots-overview.md)

Deze opties, evenals aanvullende opties voor gegevens beveiliging voor andere scenario's, worden uitgebreid beschreven in de volgende sectie.

Zie [overzicht van kosten overwegingen](#summary-of-cost-considerations)voor een overzicht van de kosten voor deze functies.

## <a name="overview-of-data-protection-options"></a>Overzicht van opties voor gegevens beveiliging

De volgende tabel bevat een overzicht van de beschik bare opties in Azure Storage voor veelvoorkomende scenario's voor gegevens bescherming. Kies de scenario's die van toepassing zijn op uw situatie voor meer informatie over de beschik bare opties. Houd er rekening mee dat niet alle functies op dit moment beschikbaar zijn voor opslag accounts waarvoor een hiërarchische naam ruimte is ingeschakeld.

| Scenario | Optie gegevens beveiliging | Aanbevelingen | Voor deel van beveiliging | Beschikbaar voor Data Lake Storage |
|--|--|--|--|--|
| Voor komen dat een opslag account wordt verwijderd of gewijzigd. | Azure Resource Manager Lock<br />[Meer informatie...](../common/lock-account-resource.md) | Vergrendel al uw opslag accounts met een Azure Resource Manager-vergren deling om te voor komen dat het opslag account wordt verwijderd. | Beveiligt het opslag account tegen verwijderings-of configuratie wijzigingen.<br /><br />Beveiligt geen containers of blobs in het account om te worden verwijderd of overschreven. | Yes |
| Voor komen dat een container en de bijbehorende blobs worden verwijderd of gewijzigd voor een interval dat u beheert. | Onveranderbaarheid-beleid op een container<br />[Meer informatie...](storage-blob-immutable-storage.md) | Stel een Onveranderbaarheid-beleid in op een container om bedrijfskritische documenten te beveiligen, bijvoorbeeld om te voldoen aan de wettelijke of reglementaire nalevings vereisten. | Beveiligt een container en de bijbehorende blobs uit alle verwijderingen en overschrijvingen.<br /><br />Wanneer een wettelijk beveiligde bewaring of een vergrendeld op tijd gebaseerd Bewaar beleid van kracht is, wordt het opslag account ook beveiligd tegen verwijdering. Containers waarvoor geen Onveranderbaarheid-beleid is ingesteld, worden niet beschermd tegen verwijdering. | Ja, in preview-versie |
| Een verwijderde container herstellen binnen een opgegeven interval. | Container zacht verwijderen (preview-versie)<br />[Meer informatie...](soft-delete-container-overview.md) | Schakel de optie voor het voorlopig verwijderen van containers in voor alle opslag accounts, met een minimale Bewaar periode van 7 dagen.<br /><br />Schakel BLOB-versie beheer en laad bare BLOB-verwijdering samen met de container soft delete om afzonderlijke blobs in een container te beveiligen.<br /><br />Bewaar containers waarvoor verschillende Bewaar perioden in afzonderlijke opslag accounts zijn vereist. | Een verwijderde container en de inhoud ervan kunnen worden hersteld binnen de retentie periode.<br /><br />Alleen bewerkingen op container niveau (bijvoorbeeld [container verwijderen](/rest/api/storageservices/delete-container)) kunnen worden hersteld. Met de optie voor het voorlopig verwijderen van een container kunt u geen afzonderlijke Blob in de container herstellen als deze blob is verwijderd. | Ja, in preview-versie |
| De status van een BLOB in een vorige versie automatisch opslaan wanneer deze wordt overschreven of verwijderd. | BLOB-versie beheer<br />[Meer informatie...](versioning-overview.md) | Schakel BLOB-versie beheer in, samen met de optie voor het voorlopig verwijderen van een container en het verwijderen van blobs, voor opslag accounts waarvoor u optimale beveiliging voor BLOB-gegevens nodig hebt.<br /><br />Gegevens van blobs opslaan waarvoor geen versie beheer is vereist in een apart account om de kosten te beperken. | Bij elke BLOB-overschrijving of verwijderings bewerking wordt een nieuwe versie gemaakt. Een BLOB kan worden hersteld vanuit een vorige versie als de BLOB wordt verwijderd of overschreven. | No |
| Een verwijderde BLOB of BLOB-versie binnen een opgegeven interval herstellen. | BLOB zacht verwijderen<br />[Meer informatie...](soft-delete-blob-overview.md) | Schakel de optie voor het voorlopig verwijderen van blobs in voor alle opslag accounts, met een minimale Bewaar periode van 7 dagen.<br /><br />Schakel BLOB-versie beheer en dynamisch verwijderen van containers samen met Blob zacht verwijderen voor optimale beveiliging van BLOB-gegevens.<br /><br />Store-blobs waarvoor verschillende Bewaar perioden in afzonderlijke opslag accounts zijn vereist. | Een verwijderde BLOB of BLOB-versie kan worden hersteld binnen de retentie periode. | No |
| Een set blok-blobs herstellen naar een eerder tijdstip. | Terugzetten naar eerder tijdstip<br />[Meer informatie...](point-in-time-restore-overview.md) | Als u herstel naar een eerder tijdstip wilt gebruiken om terug te gaan naar een eerdere status, moet u uw toepassing zo ontwerpen dat afzonderlijke blok-blobs worden verwijderd in plaats van dat containers worden verwijderd. | Een set blok-blobs kan worden teruggedraaid naar hun status op een specifiek punt in het verleden.<br /><br />Alleen bewerkingen die worden uitgevoerd op blok-blobs worden teruggedraaid. Bewerkingen die worden uitgevoerd op containers, pagina-blobs of toevoeg-blobs, worden niet teruggedraaid. | No |
| De status van een BLOB hand matig opslaan op een bepaald moment. | BLOB-moment opname<br />[Meer informatie...](snapshots-overview.md) | Wordt aanbevolen als een alternatief voor BLOB-versie beheer wanneer versie beheer niet geschikt is voor uw scenario, als gevolg van kosten of andere overwegingen, of als het opslag account een hiërarchische naam ruimte heeft ingeschakeld. | Een BLOB kan worden hersteld vanuit een moment opname als de BLOB wordt overschreven. Als de BLOB wordt verwijderd, worden er ook moment opnamen verwijderd. | Ja, in preview-versie |
| Een BLOB kan worden verwijderd of overschreven, maar de gegevens worden regel matig gekopieerd naar een tweede opslag account. | Implementeer uw eigen oplossing voor het kopiëren van gegevens naar een tweede account met behulp van Azure Storage object replicatie of een hulp programma zoals AzCopy of Azure Data Factory. | Wordt aanbevolen voor gemoeds rust tegen onverwachte opzettelijke acties of onvoorspelbare scenario's.<br /><br />Maak het tweede opslag account in dezelfde regio als het primaire account om te voor komen dat er kosten in rekening worden gebracht. | Gegevens kunnen worden hersteld vanuit het tweede opslag account als het primaire account op enigerlei wijze is aangetast. | AzCopy en Azure Data Factory worden ondersteund.<br /><br />Object replicatie wordt niet ondersteund. |

## <a name="data-protection-by-resource-type"></a>Gegevens beveiliging per resource type

De volgende tabel bevat een overzicht van de opties voor de Azure Storage gegevens beveiliging op basis van de resources die ze beveiligen.

| Optie gegevens beveiliging | Hiermee wordt een account beschermd tegen verwijdering | Hiermee wordt een container beschermd tegen verwijdering | Hiermee wordt een BLOB beschermd tegen verwijderen | Hiermee wordt een BLOB beschermd tegen overschrijvingen |
|--|--|--|--|--|
| Azure Resource Manager Lock | Yes | Geen<sup>1</sup> | Nee | Nee |
| Onveranderbaarheid-beleid op een container | Ja<sup>2</sup> | Ja | Ja | Ja |
| Container zacht verwijderen | Nee | Ja | Nee | Nee |
| BLOB-versie<sup>3</sup> | Nee | Nee | Ja | Ja |
| BLOB zacht verwijderen<sup>3</sup> | Nee | Nee | Ja | Ja |
| Herstel naar een bepaald tijdstip<sup>3</sup> | Nee | Nee | Ja | Ja |
| BLOB-moment opname | Nee | Nee | Nee | Ja |
| Implementeer uw eigen oplossing voor het kopiëren van gegevens naar een tweede account<sup>4</sup> | Nee | Ja | Ja | Ja |

<sup>1</sup> een Azure Resource Manager-vergren deling beveiligt de verwijdering van een container niet.<br />
<sup>2</sup> hoewel een geldige Bewaar periode of een vergrendeld op tijd gebaseerd beleid voor een container geldt, wordt het opslag account ook beveiligd tegen verwijdering.<br />
<sup>3</sup> wordt momenteel niet ondersteund voor data Lake Storage workloads.<br />
<sup>4</sup> AzCopy en Azure Data Factory zijn opties die worden ondersteund voor zowel Blob Storage als data Lake Storage workloads. Object replicatie wordt alleen ondersteund voor Blob Storage workloads.<br />

## <a name="recover-deleted-or-overwritten-data"></a>Verwijderde of overschreven gegevens herstellen

Als u gegevens moet herstellen die zijn overschreven of verwijderd, hangt af van de opties voor gegevens beveiliging die u hebt ingeschakeld en welke resource is beïnvloed. In de volgende tabel worden de acties beschreven die u kunt uitvoeren om gegevens te herstellen.

| Verwijderde of overschreven resource | Mogelijke herstel acties | Vereisten voor herstel |
|--|--|--|
| Storage-account | Poging om het verwijderde opslag account te herstellen<br />[Meer informatie...](../common/storage-account-recover.md) | Het opslag account is oorspronkelijk gemaakt met het Azure Resource Manager-implementatie model en is in de afgelopen 14 dagen verwijderd. Er is geen nieuw opslag account gemaakt met dezelfde naam nadat het oorspronkelijke account is verwijderd. |
| Container | De voorlopig verwijderde container en de inhoud ervan herstellen<br />[Meer informatie...](soft-delete-container-enable.md) | Het dynamisch verwijderen van de container is ingeschakeld en de Bewaar periode voor de tijdelijke verwijdering van de container nog niet is verlopen. |
| Containers en blobs | Gegevens uit een tweede opslag account herstellen | Alle container-en BLOB-bewerkingen zijn effectief gerepliceerd naar een tweede opslag account. |
| BLOB (elk type) | Een BLOB herstellen van een vorige versie<sup>1</sup><br />[Meer informatie...](versioning-enable.md) | BLOB-versie beheer is ingeschakeld en de BLOB heeft een of meer vorige versies. |
| BLOB (elk type) | Een voorlopig verwijderde BLOB herstellen<sup>1</sup><br />[Meer informatie...](soft-delete-blob-enable.md) | Zacht verwijderen van BLOB is ingeschakeld en het Bewaar interval voor zacht verwijderen is niet verlopen. |
| BLOB (elk type) | Een BLOB herstellen vanuit een moment opname<br />[Meer informatie...](snapshots-manage-dotnet.md) | De BLOB heeft een of meer moment opnamen. |
| Set blok-blobs | Een set blok-blobs herstellen naar hun status op een eerder tijdstip<sup>1</sup><br />[Meer informatie...](point-in-time-restore-manage.md) | Herstellen naar een bepaald tijdstip is ingeschakeld en het herstel punt bevindt zich binnen het Bewaar interval. Het opslag account is niet aangetast of beschadigd. |
| BLOB-versie | Een voorlopig verwijderde versie<sup>1</sup> herstellen<br />[Meer informatie...](soft-delete-blob-enable.md) | Zacht verwijderen van BLOB is ingeschakeld en het Bewaar interval voor zacht verwijderen is niet verlopen. |

<sup>1</sup> wordt momenteel niet ondersteund voor data Lake Storage workloads.

## <a name="summary-of-cost-considerations"></a>Overzicht van kosten overwegingen

De volgende tabel bevat een overzicht van de kosten voor de verschillende opties voor gegevens beveiliging die in deze hand leiding worden beschreven.

| Optie gegevens beveiliging | Kostenoverwegingen |
|-|-|
| Azure Resource Manager vergren delen voor een opslag account | Geen kosten voor het configureren van een vergren deling op een opslag account. |
| Onveranderbaarheid-beleid op een container | Geen kosten voor het configureren van een Onveranderbaarheid-beleid op een container. |
| Container zacht verwijderen | Geen kosten voor het inschakelen van een tijdelijke verwijdering van een container voor een opslag account. Gegevens in een voorlopig verwijderde container worden op hetzelfde tempo gefactureerd als actieve gegevens totdat de voorlopig verwijderde container permanent wordt verwijderd. |
| BLOB-versie beheer | Geen kosten voor het inschakelen van BLOB-versie beheer voor een opslag account. Nadat BLOB-versie beheer is ingeschakeld, maakt elke schrijf-of verwijder bewerking voor een BLOB in het account een nieuwe versie, die kan leiden tot verhoogde capaciteits kosten.<br /><br />Een BLOB-versie wordt gefactureerd op basis van unieke blokken of pagina's. De kosten worden daarom verhoogd als de basis-BLOB verschilt van een bepaalde versie. Het wijzigen van de laag van een BLOB of BLOB-versie kan een facturerings impact hebben. Zie [prijzen en facturering](versioning-overview.md#pricing-and-billing)voor meer informatie.<br /><br />Gebruik levenscyclus beheer om oudere versies zo nodig te verwijderen om de kosten te beheren. Zie [kosten optimaliseren door Azure Blob Storage Access-lagen te automatiseren](storage-lifecycle-management-concepts.md)voor meer informatie. |
| BLOB zacht verwijderen | Geen kosten om het voorlopig verwijderen van blobs in te scha kelen voor een opslag account. Gegevens in een zachte verwijderde BLOB worden op hetzelfde tempo gefactureerd als actieve gegevens totdat de zachte verwijderde BLOB permanent wordt verwijderd. |
| Terugzetten naar eerder tijdstip | Geen kosten voor het inschakelen van herstel naar een bepaald tijdstip voor een opslag account; het inschakelen van herstel naar een bepaald tijdstip zorgt er echter ook voor dat BLOB-versie beheer, zacht verwijderen en wijzigings feed, die elk kan leiden tot extra kosten.<br /><br />U wordt gefactureerd voor herstel naar een bepaald tijdstip wanneer u een herstel bewerking uitvoert. De kosten voor een herstel bewerking zijn afhankelijk van de hoeveelheid gegevens die wordt hersteld. Zie [prijzen en facturering](point-in-time-restore-overview.md#pricing-and-billing)voor meer informatie. |
| BLOB-moment opnamen | Gegevens in een moment opname worden gefactureerd op basis van unieke blokken of pagina's. De kosten nemen daarom toe als de basis-BLOB afwijkt van de moment opname. Het wijzigen van de laag van een BLOB of moment opname kan een facturerings impact hebben. Zie [prijzen en facturering](snapshots-overview.md#pricing-and-billing)voor meer informatie.<br /><br />Gebruik levenscyclus beheer om oudere moment opnamen te verwijderen als dat nodig is om de kosten te beheren. Zie [kosten optimaliseren door Azure Blob Storage Access-lagen te automatiseren](storage-lifecycle-management-concepts.md)voor meer informatie. |
| Gegevens kopiëren naar een tweede opslag account | Bij het beheren van gegevens in een tweede opslag account worden capaciteits-en transactie kosten in rekening gebracht. Als het tweede opslag account zich in een andere regio bevindt dan het bron account, worden er bij het kopiëren van gegevens naar het tweede account ook uitvoer kosten in rekening gebracht. |

## <a name="disaster-recovery"></a>Herstel na noodgeval

Azure Storage bewaart altijd meerdere kopieën van uw gegevens, zodat deze worden beschermd tegen geplande en niet-geplande gebeurtenissen, waaronder tijdelijke hardwarestoringen, netwerk-of energie storingen en enorme natuur rampen. Redundantie zorgt ervoor dat uw opslag account voldoet aan de beschik baarheid en duurzaamheids doelen, zelfs in het geval van storingen. Zie [Azure Storage redundantie](../common/storage-redundancy.md)voor meer informatie over het configureren van uw opslag account voor hoge Beschik baarheid.

Als er een fout optreedt in een Data Center, kunt u, als uw opslag account overbodig is in twee geografische regio's (geografisch redundant), u de optie voor het uitvoeren van een failover van uw account van de primaire regio naar de secundaire regio. Zie [nood herstel en failover van het opslag account](../common/storage-disaster-recovery-guidance.md)voor meer informatie.

Door de klant beheerde failover wordt momenteel niet ondersteund voor opslag accounts waarvoor een hiërarchische naam ruimte is ingeschakeld. Zie [Blob Storage-functies die beschikbaar zijn in azure data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-redundantie](../common/storage-redundancy.md)
- [Herstel na noodgeval en failover van opslagaccount](../common/storage-disaster-recovery-guidance.md)
