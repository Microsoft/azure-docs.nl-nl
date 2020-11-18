---
title: Onveranderbare Blob Storage-Azure Storage
description: Azure Storage biedt WORM (eenmaal schrijven, lezen) voor Blob-opslag (object) die gebruikers in staat stelt om gegevens op te slaan in een niet-bewerk bare, niet-aanpas bare status voor een opgegeven interval.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: 39fdde572e269bb4f5648e91bf85539d02236ff6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658550"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Bedrijfs kritieke blobgegevens opslaan met onveranderlijke opslag

Onveranderbare opslag voor Azure Blob-opslag stelt gebruikers in staat om bedrijfskritische gegevens objecten op te slaan in een WORM (eenmaal schrijven, gelezen). Met deze status worden de gegevens niet-kan worden gewist en niet kunnen worden gewijzigd voor een door de gebruiker opgegeven interval. Voor de duur van het retentie-interval kunnen blobs worden gemaakt en gelezen, maar niet worden gewijzigd of verwijderd. Onveranderbare opslag is beschikbaar voor de algemene v1-, BlobStorage-en BlockBlobStorage-accounts voor algemeen gebruik in alle Azure-regio's.

Zie [Onveranderbaarheid-beleid instellen en beheren voor Blob Storage](storage-blob-immutability-policies-manage.md)voor meer informatie over het instellen en wissen van juridische bewaringen of het maken van een op tijd gebaseerd Bewaar beleid met behulp van de Azure Portal, Power shell of Azure cli.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="about-immutable-blob-storage"></a>Over onveranderbare Blob Storage

Onveranderbare opslag helpt organisaties in de gezondheids zorg, financiële instellingen en gerelateerde branches, &mdash; met name Broker handelaars &mdash; om gegevens veilig op te slaan. Onveranderbare opslag kan ook worden gebruikt in elk scenario om kritieke gegevens te beschermen tegen wijziging of verwijdering.

Typische toepassingen zijn onder andere:

- **Naleving van regelgeving**: onveranderbare opslag voor Azure Blob-opslag helpt organisaties bij het adresseren van 17a-4 (f), CFTC 1.31 (d), FINRA en andere voor Schriften. Een technisch document met Cohasset Associates bevat informatie over hoe onveranderbare opslag adressen deze regelgeving kan worden gedownload via de [micro soft-portal voor service vertrouwen](https://aka.ms/AzureWormStorage). De [Vertrouwenscentrum van Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) bevat gedetailleerde informatie over onze nalevings certificeringen.

- **Bewaren van beveiligde documenten**: onveranderbare opslag voor Azure Blob-opslag zorgt ervoor dat gegevens niet kunnen worden gewijzigd of verwijderd door een gebruiker, met inbegrip van gebruikers met beheerders bevoegdheden voor het account.

- **Juridische bewaring**: onveranderbare opslag voor Azure Blob-opslag biedt gebruikers de mogelijkheid gevoelige gegevens op te slaan die van cruciaal belang zijn voor rechts zaken of voor het gebruik van een onrecht matige afsluitings status voor de gewenste duur totdat de bewaring wordt verwijderd. Deze functie is niet beperkt tot juridische use-cases, maar kan ook worden beschouwd als een op gebeurtenissen gebaseerde bewaring of een Enter prise Lock, waarbij de nood zaak om gegevens te beveiligen op basis van gebeurtenis triggers of bedrijfs beleid vereist is.

Onveranderbare opslag ondersteunt de volgende functies:

- **[Ondersteuning voor Bewaar beleid op basis van tijd](#time-based-retention-policies)**: gebruikers kunnen beleid instellen om gegevens op te slaan voor een opgegeven interval. Wanneer een Bewaar beleid op basis van tijd is ingesteld, kunnen blobs worden gemaakt en gelezen, maar niet gewijzigd of verwijderd. Nadat de Bewaar periode is verlopen, kunnen blobs worden verwijderd, maar niet worden overschreven.

- **[Ondersteuning voor beleid voor juridische bewaring](#legal-holds)**: als het Bewaar interval niet bekend is, kunnen gebruikers juridische bewaringen instellen om onveranderbare gegevens op te slaan totdat de juridische bewaring is gewist.  Als er een beleid voor juridische bewaring is ingesteld, kunnen blobs worden gemaakt en gelezen, maar niet gewijzigd of verwijderd. Elke juridische bewaring is gekoppeld aan een door de gebruiker gedefinieerde alfanumerieke code (zoals een case-ID, gebeurtenis naam enzovoort) die wordt gebruikt als een id-teken reeks. 

- **Ondersteuning voor alle BLOB-lagen**: het virus beleid is onafhankelijk van de Azure Blob Storage-laag en is van toepassing op alle lagen: Hot, cool en Archive. Gebruikers kunnen gegevens verplaatsen naar de laag die het meest rendabel is voor hun werkbelastingen, met behoud van de onveranderbaarheid van gegevens.

- **Configuratie op container niveau**: gebruikers kunnen op het niveau van de Bewaar termijn op basis van een op tijd gebaseerd beleid voor het bewaren van inhoud en juridische bewarings codes configureren. Door eenvoudige instellingen op containerniveau te gebruiken, kunnen gebruikers retentiebeleid op basis van tijd maken en vergrendelen, retentieperioden verlengen, juridische bewaring instellen en opheffen enzovoort. Deze beleidsregels zijn van toepassing op alle blobs in de container - zowel op bestaande als nieuwe blobs.

- **Ondersteuning voor de controle logboek registratie**: elke container bevat een beleids audit logboek. Het bevat Maxi maal zeven op tijd gebaseerde Bewaar opdrachten voor vergrendeld Bewaar beleid op basis van tijd en bevat de gebruikers-ID, het type opdracht, de tijds tempels en het Bewaar interval. Voor juridische bewaring bevat het logboek de gebruikers-id, het opdrachttype, tijdstempels en de labels voor juridische bewaring. Dit logboek wordt bewaard gedurende de levens duur van het beleid, overeenkomstig de richt lijnen van de 17a-4 (f). In het [activiteiten logboek van Azure](../../azure-monitor/platform/platform-logs-overview.md) wordt een uitgebreidere logboek van alle activiteiten van het controle vlak weer gegeven. bij het inschakelen van [Azure-resource logboeken](../../azure-monitor/platform/platform-logs-overview.md) worden de gegevenslaag bewerkingen bewaard en weer gegeven. Het is de verantwoordelijkheid van de gebruiker om deze logboeken permanent op te slaan, wat mogelijk vereist is voor andere doeleinden.

## <a name="how-it-works"></a>Uitleg

Onveranderbare opslag voor Azure Blobs ondersteunt twee soorten WORM-beleidsregels of beleidsregels voor onveranderbare opslag: retentie op basis van tijd en juridische bewaring. Wanneer een Bewaar beleid op basis van tijd of wettelijk geblokkeerd wordt toegepast op een container, worden alle bestaande blobs in minder dan 30 seconden omgezet in een onveranderbare WORM status. Alle nieuwe blobs die zijn geüpload naar die beleids beveiligde container, worden ook verplaatst naar een onveranderlijke status. Zodra alle Blobs een onveranderbare status hebben, wordt het onveranderbare beleid bevestigd en kunnen de overschrijvings-of verwijderings bewerkingen in de onveranderbare container niet worden toegestaan.

Het verwijderen van containers en opslag accounts is ook niet toegestaan als er blobs in een container zijn die zijn beveiligd met een juridische bewaring of een vergrendeld op tijd gebaseerd beleid. Een juridisch bewarings beleid kan worden beveiligd tegen het verwijderen van een blob, container en opslag account. Zowel ontgrendeld als vergrendeld op tijd gebaseerd beleid zorgt ervoor dat het verwijderen van blobs gedurende de opgegeven tijd wordt beschermd. Zowel ontgrendeld als vergrendeld op tijd gebaseerd beleid voor het verwijderen van de container wordt alleen beveiligd als er ten minste één Blob in de container bestaat. Alleen een container met *vergrendeld* op tijd gebaseerd beleid wordt beschermd tegen het verwijderen van het opslag account; containers met niet-vergrendelde op tijd gebaseerde beleids regels bieden geen bescherming tegen het verwijderen van opslag accounts en naleving.

Zie [Onveranderbaarheid-beleid instellen en beheren voor Blob Storage](storage-blob-immutability-policies-manage.md)voor meer informatie over het instellen en vergren delen van op tijd gebaseerd Bewaar beleid.

## <a name="time-based-retention-policies"></a>Bewaar beleid op basis van tijd

> [!IMPORTANT]
> Een Bewaar beleid op basis van tijd moet worden *vergrendeld* om de BLOB een compatibele onveranderlijke status (schrijven en verwijderen beveiligd) te hebben voor SEC 17a-4 (f) en andere naleving van regelgeving. We raden u aan het beleid binnen een redelijke hoeveelheid tijd te vergren delen, meestal minder dan 24 uur. De initiële status van een toegepast Bewaar beleid op basis van tijd is *ontgrendeld*, zodat u de functie kunt testen en wijzigingen kunt aanbrengen in het beleid voordat u het vergrendelt. Hoewel de status *ontgrendeld* Onveranderbaarheid beveiliging biedt, is het niet raadzaam om de *Ontgrendelde* status te gebruiken voor andere doel einden dan kortings functies. 

Wanneer een Bewaar beleid op basis van tijd op een container wordt toegepast, blijven alle blobs in de container onveranderbaar voor de duur van de *daad werkelijke* Bewaar periode. De daad werkelijke Bewaar periode voor blobs is gelijk aan het verschil tussen de **aanmaak tijd** van de BLOB en de door de gebruiker opgegeven Bewaar periode. Omdat gebruikers het retentie-interval kunnen verlengen, gebruikt onveranderlijke opslag de meest recente waarde van het opgegeven retentie-interval om de effectieve retentieperiode te berekenen.

Stel bijvoorbeeld dat een gebruiker een Bewaar beleid op basis van tijd maakt met een Bewaar interval van vijf jaar. Een bestaande Blob in die container, _testblob1_, is één jaar geleden gemaakt. Daarom is de daad werkelijke Bewaar periode voor _testblob1_ vier jaar. Wanneer een nieuwe blob, _testblob2_, wordt geüpload naar de container, is de daad werkelijke Bewaar periode voor de _testblob2_ vijf jaar vanaf het moment dat deze wordt gemaakt.

Een niet-vergrendeld op tijd gebaseerd Bewaar beleid wordt alleen aanbevolen voor het testen van functies en een beleid moet worden vergrendeld om compatibel te zijn met SEC 17a-4 (f) en andere naleving van regelgeving. Wanneer een Bewaar beleid op basis van tijd is vergrendeld, kan het beleid niet worden verwijderd en zijn Maxi maal vijf verhogingen van de ingangs periode toegestaan.

De volgende limieten gelden voor het Bewaar beleid:

- Voor een opslag account is het maximum aantal containers met vergrendeld op tijd gebaseerd onveranderbaar beleid 10.000.
- Het minimale Bewaar interval is 1 dag. De maximum waarde is 146.000 dagen (400 jaar).
- Voor een container is het maximum aantal bewerkingen voor het uitbreiden van een Bewaar interval voor vergrendelde, op tijd gebaseerde onveranderlijke beleids regels 5.
- Voor een container worden Maxi maal zeven op tijd gebaseerde controle logboeken voor Bewaar beleid bewaard voor een vergrendeld beleid.

### <a name="allow-protected-append-blobs-writes"></a>Schrijven van beveiligde toevoeg-blobs toestaan

Toevoeg-blobs bestaan uit blokken gegevens en zijn geoptimaliseerd voor bewerkingen voor het toevoegen van gegevens die vereist zijn voor controle-en logboek registratie scenario's. Door middel van toevoeg-blobs kunnen nieuwe blokken alleen worden toegevoegd aan het einde van de blob. Ongeacht de Onveranderbaarheid is het niet toegestaan om bestaande blokken in een toevoeg-BLOB te wijzigen of te verwijderen. Zie [informatie over toevoeg](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)-blobs voor meer informatie over toevoeg-blobs.

Alleen op tijd gebaseerd Bewaar beleid heeft een `allowProtectedAppendWrites` instelling waarmee nieuwe blokken naar een toevoeg-BLOB kunnen worden geschreven terwijl Onveranderbaarheid-beveiliging en-naleving behouden blijven. Als deze instelling is ingeschakeld, kunt u rechtstreeks een toevoeg-Blob in de door beleid beveiligde container maken en nieuwe gegevens blokken toevoegen aan het einde van bestaande toevoeg-blobs met behulp van de *AppendBlock* -API. Alleen nieuwe blokken kunnen worden toegevoegd en bestaande blokken kunnen niet worden gewijzigd of verwijderd. Er wordt nog steeds een tijd-retentie Onveranderbaarheid-beveiliging toegepast, waardoor de toevoeg-BLOB niet kan worden verwijderd totdat de juiste Bewaar periode is verstreken. Het inschakelen van deze instelling heeft geen invloed op het gedrag van de Onveranderbaarheid van blok-blobs of pagina-blobs.

Omdat deze instelling deel uitmaakt van een Bewaar beleid op basis van tijd, blijven de toevoeg-blobs nog steeds in de onveranderbare status voor de duur van de *daad werkelijke* Bewaar periode. Omdat er nieuwe gegevens kunnen worden toegevoegd na het maken van de toevoeg-blob, is er een enigszins verschil in de manier waarop de Bewaar periode wordt bepaald. De doel matige Bewaar periode is het verschil tussen de **laatste wijzigings tijd** van de BLOB en de door de gebruiker opgegeven Bewaar periode. Op dezelfde manier als het Bewaar interval uitgebreid is, gebruikt onveranderbare opslag de meest recente waarde van de door de gebruiker opgegeven Bewaar termijn om de ingangs periode te berekenen.

Stel bijvoorbeeld dat een gebruiker een Bewaar beleid op basis van tijd maakt met `allowProtectedAppendWrites` ingeschakeld en een Bewaar interval van 90 dagen. Een toevoeg-blob, _logblob1_, wordt vandaag gemaakt in de container. nieuwe logboeken worden nog steeds toegevoegd aan de toevoeg-BLOB voor de volgende 10 dagen. de meest efficiënte Bewaar periode voor de _logblob1_ is dus 100 dagen vanaf vandaag (de tijd van de laatste append + 90 dagen).

Met een niet-vergrendeld op tijd gebaseerd Bewaar beleid kunt `allowProtectedAppendWrites` u de instelling op elk gewenst moment inschakelen en uitschakelen. Wanneer het Bewaar beleid op basis van tijd is vergrendeld, kan de `allowProtectedAppendWrites` instelling niet worden gewijzigd.

Wettelijk Bewaar beleid kan niet worden ingeschakeld `allowProtectedAppendWrites` en de eigenschap ' allowProtectedAppendWrites ' wordt door alle juridische bewaringen opheffen. Als er een geldige blok kering wordt toegepast op een op tijd gebaseerd Bewaar beleid met `allowProtectedAppendWrites` ingeschakeld, mislukt de *AppendBlock* -API totdat de juridische bewaring is opgeheven.

## <a name="legal-holds"></a>Juridische bewaring

Juridische bewaringen zijn tijdelijke bewaringen die kunnen worden gebruikt voor juridisch onderzoek of algemeen beveiligings beleid. Elk juridisch bewarings beleid moet worden gekoppeld aan een of meer tags. Tags worden gebruikt als een benoemde id, zoals een case-ID of gebeurtenis, om het doel van de blok kering te categoriseren en te beschrijven.

Een container kan op hetzelfde moment zowel een juridische bewaring als een op tijd gebaseerd Bewaar beleid hebben. Alle blobs in die container blijven onveranderbaar totdat alle juridische bewaringen zijn opgeheven, zelfs als hun effectieve retentieperiode is verlopen. Een blob blijft daarentegen onveranderbaar totdat de effectieve retentieperiode is verlopen, zelfs als alle juridische blokkeringen zijn gewist.

De volgende limieten gelden voor juridische bewaringen:

- Voor een opslag account is het maximum aantal containers met een geldige Hold instelling 10.000.
- Voor een container is het maximum aantal juridische bewarings codes 10.
- De minimale lengte van een geldige Hold-tag is drie alfanumerieke tekens. De maximale lengte is 23 alfanumerieke tekens.
- Voor een container worden voor de duur van het beleid Maxi maal 10 controle logboeken voor juridische bewaring bewaard.

## <a name="scenarios"></a>Scenario's

De volgende tabel bevat de typen Blob Storage-bewerkingen die zijn uitgeschakeld voor de verschillende onveranderbare scenario's. Zie de [Azure Blob Service rest API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) -documentatie voor meer informatie.

| Scenario | BLOB-status | BLOB-bewerkingen geweigerd | Container-en account beveiliging |
|--|--|--|--|
| Effectieve retentieperiode voor de blob is nog niet verlopen en/of wettelijke bewaring is ingesteld | Onveranderbaar: beveiligd tegen verwijderen en schrijven | Put-BLOB<sup>1</sup>, put blok<sup>1</sup>, blok lijst<sup>1</sup>, verwijderen container, Blob verwijderen, Blob-meta gegevens instellen, pagina plaatsen, Blob-eigenschappen instellen, moment opname-blob, incrementele Kopieer-blob, toevoeg blok<sup>2</sup> | Verwijderen van container is geweigerd; Het verwijderen van het opslag account is geweigerd |
| Effectief Bewaar interval voor de blob is verlopen en er is geen geldige blok kering ingesteld | Alleen beveiligd tegen schrijven (verwijderen is toegestaan) | Put-BLOB<sup>1</sup>, blok<sup>1</sup>plaatsen, blok lijst<sup>1</sup>plaatsen, Blob-meta gegevens instellen, pagina plaatsen, Blob-eigenschappen instellen, moment opname-blob, incrementele Kopieer-blob, toevoeg blok<sup>2</sup> | Verwijderen van container is geweigerd als er ten minste één Blob in een beveiligde container bestaat; Het verwijderen van een opslag account is alleen geweigerd voor *vergrendeld* op tijd gebaseerd beleid |
| Er is geen WORM beleid toegepast (geen Bewaar periode op basis van tijd en geen geldige bewarings code) | Veranderlijk | Geen | Geen |

<sup>1</sup> met de BLOB-service kunnen deze bewerkingen een nieuwe Blob maken. Alle volgende overschrijvings bewerkingen op een bestaand BLOB-pad in een onveranderbare container zijn niet toegestaan.

<sup>2</sup> toevoeg blok is alleen toegestaan voor op tijd gebaseerd Bewaar beleid waarvoor de eigenschap is `allowProtectedAppendWrites` ingeschakeld. Zie de sectie [schrijf bewerkingen voor beveiligde toevoeg-blobs toestaan](#allow-protected-append-blobs-writes) voor meer informatie.

> [!IMPORTANT]
> Sommige werk belastingen, zoals [SQL back-up naar URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url), maken een BLOB en voegen deze vervolgens toe. Als de container een actief Bewaar beleid op basis van tijd of rechts geblokkeerd heeft, zal dit patroon niet slagen.

## <a name="pricing"></a>Prijzen

Er worden geen extra kosten in rekening gebracht voor het gebruik van deze functie. Onveranderbare gegevens worden op dezelfde manier geprijsd als geveranderde gegevens. Zie de [pagina met prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/)voor prijs informatie over Azure Blob-opslag.

## <a name="faq"></a>Veelgestelde vragen

**Kunt u documentatie voor WORM compatibiliteit opgeven?**

Ja. Om naleving te documenteren, heeft micro soft een toonaangevende onafhankelijke beoordelings onderneming gehouden die is gespecialiseerd in record beheer en Information governance, Cohasset Associates, om onveranderlijke Blob-opslag te evalueren en te voldoen aan de vereisten die specifiek zijn voor de financiële dienst verleners. Cohasset valideerde dat onveranderbare Blob Storage, wanneer deze wordt gebruikt om op tijd gebaseerde blobs in een WORM te bewaren, voldoet aan de relevante opslag vereisten van CFTC regel 1.31 (c)-(d), FINRA regel 4511 en seconde regel 17a-4. Micro soft heeft deze regels als doel gesteld, omdat deze de meest beschrijvende richt lijnen voor het bewaren van records voor financiële instellingen vertegenwoordigen. Het Cohasset-rapport is beschikbaar in het [vertrouwens centrum van micro soft-Services](https://aka.ms/AzureWormStorage). Neem contact op met de ondersteuning van Azure als u een verklaring van micro soft wilt aanvragen over de Onveranderbaarheid-naleving van WORMen.

**Is de functie alleen van toepassing op blok-blobs en toevoeg-blobs of op pagina-blobs?**

Onveranderbare opslag kan worden gebruikt in combi natie met een BLOB-type dat is ingesteld op het niveau van de container, maar we raden u aan om WORM te gebruiken voor containers die hoofd zakelijk blok-blobs en toevoeg-blobs bevatten. Bestaande blobs in een container worden beveiligd door een nieuw ingesteld WORM-beleid. Er moeten echter nieuwe pagina-blobs worden gemaakt buiten de WORM-container en vervolgens worden gekopieerd in. Nadat het is gekopieerd naar een WORM container, zijn geen verdere wijzigingen in een pagina-BLOB toegestaan. Het instellen van een WORM beleid voor een container waarin Vhd's worden opgeslagen (pagina-blobs) voor actieve virtuele machines wordt afgeraden, omdat hiermee de VM-schijf wordt vergrendeld. We raden u aan de documentatie grondig te bekijken en uw scenario's te testen voordat u op tijd gebaseerd beleid vergrendelt.

**Moet ik een nieuw opslag account maken om deze functie te gebruiken?**

Nee, u kunt onveranderbare opslag gebruiken met bestaande of nieuwe, voor algemeen gebruik v2-, BlobStorage-of BlockBlobStorage-accounts voor algemeen gebruik. V1-opslag accounts voor algemeen gebruik worden ondersteund, maar we raden u aan om een upgrade uit te voeren naar de v2 voor algemeen gebruik, zodat u kunt profiteren van meer functies. Zie [een opslag account upgraden](../common/storage-account-upgrade.md)voor meer informatie over het upgraden van een bestaand v1-opslag account voor algemeen gebruik.

**Kan ik zowel een rechts Bewaar beleid op basis van een wacht tijd Toep assen?**

Ja, een container kan zowel een juridische bewaring als een op tijd gebaseerd Bewaar beleid hebben. de instelling ' allowProtectedAppendWrites ' is echter niet van toepassing totdat de juridische bewaring is gewist. Alle blobs in die container blijven onveranderbaar totdat alle juridische bewaringen zijn opgeheven, zelfs als hun effectieve retentieperiode is verlopen. Een blob blijft daarentegen onveranderbaar totdat de effectieve retentieperiode is verlopen, zelfs als alle juridische blokkeringen zijn gewist. 

**Zijn beleids regels voor juridische bewaring alleen voor juridische procedures of zijn er andere scenario's voor het gebruik?**

Nee, juridisch geblokkeerd is alleen de algemene term die wordt gebruikt voor een Bewaar beleid waarvoor geen tijd is gebaseerd. Het hoeft niet alleen te worden gebruikt voor procedures met betrekking tot rechts zaken. Beleid voor juridische bewaring is handig voor het uitschakelen van overschrijving en verwijderingen voor het beveiligen van belang rijke gegevens van de bedrijfs WORM, waarbij de Bewaar periode onbekend is. U kunt dit als bedrijfs beleid gebruiken voor het beveiligen van de workloads van uw missie kritieke WORM of voor het gebruik van het beleid als stagingbeleid voordat een aangepaste gebeurtenis trigger het gebruik van een op tijd gebaseerd Bewaar beleid vereist. 

**Kan ik een _vergrendeld_ op tijd gebaseerd Bewaar beleid of juridische bewaring verwijderen?**

Alleen een niet-vergrendeld op tijd gebaseerd Bewaar beleid kan uit een container worden verwijderd. Wanneer een Bewaar beleid op basis van tijd is vergrendeld, kan het niet meer worden verwijderd. alleen efficiënte Bewaar periode-uitbrei dingen zijn toegestaan. Codes voor juridische bewaring kunnen worden verwijderd. Wanneer alle juridische Tags worden verwijderd, wordt de juridische bewaring verwijderd.

**Wat gebeurt er als ik een container probeer te verwijderen met een op tijd gebaseerd Bewaar beleid of een juridische bewaring?**

De bewerking voor het verwijderen van een container mislukt als er ten minste één BLOB bestaat in de container met een vergrendeld of ontgrendeld Bewaar beleid of als de container een juridische bewaring heeft. De Verwijder container bewerking wordt alleen uitgevoerd als er geen blobs in de container bestaan en er geen geldige bewaringen zijn. 

**Wat gebeurt er als ik probeer een opslag account te verwijderen met een container die een Bewaar beleid op basis van tijd of een juridische bewaring heeft?**

Het verwijderen van het opslag account mislukt als er ten minste één container met een geldige blok kering of een **vergrendeld** op tijd gebaseerd beleid is ingesteld. Een container met een niet-vergrendeld op tijd gebaseerd beleid biedt geen bescherming tegen het verwijderen van een opslag account. U moet alle juridische bewaringen verwijderen en alle **vergrendelde** containers verwijderen voordat u het opslag account kunt verwijderen. Zie de vorige vraag voor meer informatie over het verwijderen van een container. U kunt ook verdere verwijderings beveiligingen voor uw opslag account Toep assen met [Azure Resource Manager-vergren delingen](../../azure-resource-manager/management/lock-resources.md).

**Kan ik de gegevens verplaatsen tussen verschillende BLOB-lagen (hot, cool, Archive) wanneer de BLOB de status onveranderbaar heeft?**

Ja, u kunt de opdracht BLOB-laag instellen gebruiken om gegevens over de BLOB-lagen te verplaatsen terwijl de gegevens in de status compatibel onveranderlijk blijven. Onveranderbare opslag wordt ondersteund op de BLOB-lagen hot, cool en Archive.

**Wat gebeurt er als ik niet meer betaal en mijn retentieperiode niet is verlopen?**

In het geval van een niet-betaling, geldt het normale Bewaar beleid voor gegevens, zoals is vastgelegd in de voor waarden van uw contract met micro soft. Zie [gegevens beheer bij micro soft](https://www.microsoft.com/en-us/trust-center/privacy/data-management)voor algemene informatie. 

**Biedt u ook een evaluatie- of respijtperiode voor de zojuist beschreven functie?**

Ja. Wanneer een Bewaar beleid op basis van tijd voor het eerst wordt gemaakt, heeft dit een status die niet is *vergrendeld* . In deze status kunt u elke gewenste wijziging aanbrengen in het Bewaar interval, zoals verg Roten of verkleinen en zelfs het beleid verwijderen. Nadat het beleid is vergrendeld, blijft het vergrendeld totdat het retentie-interval verloopt. Met dit vergrendelde beleid voor komt u dat het Bewaar interval wordt verwijderd en gewijzigd. We raden u ten zeerste aan de *Ontgrendelde* status alleen te gebruiken voor proef doeleinden en het beleid binnen een periode van 24 uur te vergren delen. Deze procedures helpen u te voldoen aan de beschik over de SEC 17a-4 (f) en andere voor Schriften.

**Kan ik zacht verwijderen naast onveranderbaar BLOB-beleid gebruiken?**

Ja, als uw nalevings vereisten toestaan dat zacht verwijderen is ingeschakeld. [Zacht verwijderen voor Azure Blob Storage](storage-blob-soft-delete.md) is van toepassing op alle containers in een opslag account, ongeacht of het Bewaar beleid geldig is of op basis van tijd. We raden u aan om voorlopig verwijderen in te scha kelen voor extra beveiliging voordat onveranderbaar WORM beleid wordt toegepast en bevestigd.

## <a name="next-steps"></a>Volgende stappen

- [Onveranderbaarheid-beleid instellen en beheren voor Blob Storage](storage-blob-immutability-policies-manage.md)
- [Regels instellen voor het automatisch laag maken en verwijderen van BLOB-gegevens met levenscyclus beheer](storage-lifecycle-management-concepts.md)
- [Voorlopig verwijderen voor Azure Storage-blobs](../blobs/storage-blob-soft-delete.md)
- [Beveilig abonnementen, resource groepen en resources met Azure Resource Manager sloten](../../azure-resource-manager/management/lock-resources.md).
