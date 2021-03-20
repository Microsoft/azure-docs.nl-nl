---
title: Meer informatie over Azure File Sync Cloud lagen | Microsoft Docs
description: Meer informatie over Cloud lagen, een optionele functie Azure File Sync. Veelgebruikte bestanden worden lokaal opgeslagen in de cache op de server. andere lagen worden getierd op Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dcd58e966da7ca596a14ca1b2839cbeb6399a855
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104576455"
---
# <a name="cloud-tiering-overview"></a>Overzicht van Cloud lagen
Cloud lagen, een optionele functie van Azure File Sync, vermindert de hoeveelheid lokale opslag die nodig is om de prestaties van een on-premises Bestands server te behouden.

Wanneer deze functie is ingeschakeld, worden alleen bestanden met regel matig toegang (warme) op de lokale server opgeslagen. Bestanden die niet regel matig worden gebruikt, worden gesplitst in de naam ruimte (bestands-en mapstructuur) en bestands inhoud. De naam ruimte wordt lokaal opgeslagen en de bestands inhoud die is opgeslagen in een Azure-bestands share in de Cloud. 

Wanneer een gebruiker een gelaagd bestand opent, Azure File Sync naadloos de bestands gegevens van de bestands share in azure terugroepen.

## <a name="how-cloud-tiering-works"></a>Hoe Cloud lagen werken

### <a name="cloud-tiering-policies"></a>Beleid voor Cloud lagen
Wanneer u Cloud Tiering inschakelt, zijn er twee beleids regels die u kunt instellen om Azure File Sync te informeren wanneer u coole bestanden laag: het **volume beschik bare ruimte beleid** en het **datum beleid**. 

#### <a name="volume-free-space-policy"></a>Volume beschik bare ruimte beleid
Het **volume beschik bare ruimte beleid** vertelt Azure file sync dat er coole bestanden naar de cloud worden gelaagd wanneer een bepaalde hoeveelheid ruimte op de lokale schijf wordt opgeteld. 

Als uw lokale schijf capaciteit bijvoorbeeld 200 GB is en u wilt dat ten minste 40 GB van uw lokale schijf capaciteit altijd gratis blijft, moet u het volume beschik bare ruimte-beleid instellen op 20%. Volume beschik bare ruimte is van toepassing op volume niveau in plaats van op het niveau van afzonderlijke directory's of server eindpunten. 

Voor meer informatie over hoe het volume beschik bare ruimte beleid bestanden in eerste instantie heeft gedownload wanneer een nieuw server eindpunt wordt toegevoegd, (Zie de sectie [synchronisatie beleid die van invloed is op Cloud lagen](#sync-policies-that-affect-cloud-tiering)).

#### <a name="date-policy"></a>Datum beleid
Met het **datum beleid** worden coole bestanden naar de Cloud gelaagd als ze niet zijn geopend (dat wil zeggen, gelezen of beschreven) voor x aantal dagen. Als u bijvoorbeeld hebt gezien dat bestanden die meer dan 15 dagen zonder toegang worden gebruikt meestal archief bestanden zijn, moet u uw datum beleid instellen op 15 dagen. 

Zie Azure File Sync-beleid voor [Cloud lagen kiezen](storage-sync-choose-cloud-tiering-policies.md)voor meer voor beelden van de manier waarop het beleid voor datum beleid en volume beschik bare ruimte samen werken.

### <a name="windows-server-data-deduplication"></a>Windows Server-gegevensontdubbeling
Gegevensontdubbeling wordt ondersteund op volumes waarop Cloud Tiering is ingeschakeld vanaf Windows Server 2016. Zie [planning voor een Azure file sync-implementatie](./storage-sync-files-planning.md#data-deduplication)voor meer informatie.

### <a name="cloud-tiering-heatmap"></a>Heatmap Cloud lagen
Azure File Sync bewaakt bestands toegang (lees-en schrijf bewerkingen) in de loop van de tijd en, op basis van hoe vaak en recente toegang is, wijst aan elk bestand een hitte Score toe. Deze scores worden gebruikt om een "heatmap" van uw naam ruimte op elk server eindpunt te maken. Deze heatmap is een lijst met alle synchronisatie bestanden op een locatie waarvoor Cloud lagen zijn ingeschakeld, geordend op basis van hun hitte Score. Veelgebruikte bestanden die onlangs zijn geopend, worden beschouwd als hot, terwijl bestanden die nauwelijks werden gerakend en gedurende enige tijd niet toegankelijk zijn, als koud worden beschouwd. 

Om de relatieve positie van een afzonderlijk bestand in die heatmap te bepalen, gebruikt het systeem het maximum van de tijds tempels, in de volgende volg orde: MAX (tijd van laatste toegang, tijd van laatste wijziging, aanmaak tijd). 

Normaal gesp roken wordt de tijd van laatste toegang bijgehouden en beschikbaar. Als er echter een nieuwe server-eind punt wordt gemaakt, wordt er niet genoeg tijd door gegeven aan de toegang tot het bestand als er een Cloud niveau is ingeschakeld. Als er geen geldige tijd van laatste toegang is, wordt in plaats daarvan de laatst gewijzigde tijd gebruikt om de relatieve positie in de heatmap te evalueren.  

Het datum beleid werkt op dezelfde manier. Zonder tijd van laatste toegang wordt het datum beleid toegepast op het tijdstip waarop het voor het laatst is gewijzigd. Als dat niet beschikbaar is, wordt het terugvallen op de aanmaak tijd van een bestand. Na verloop van tijd ziet het systeem meer en meer aanvragen voor bestands toegang en wordt automatisch begonnen met het gebruik van de zelf getraceerde tijd van laatste toegang.

> [!Note]
> Cloud lagen zijn niet afhankelijk van de NTFS-functie voor het bijhouden van de tijd van de laatste toegang. Deze NTFS-functie is standaard uitgeschakeld en vanwege prestatie overwegingen wordt u niet aangeraden deze functie hand matig in te scha kelen. Met Cloud lagen wordt de tijd van laatste toegang afzonderlijk bijgehouden.

### <a name="sync-policies-that-affect-cloud-tiering"></a>Beleids regels synchroniseren die van invloed zijn op Cloud lagen

Met Azure File Sync agent versie 11 zijn er twee aanvullende Azure File Sync-beleids regels die van invloed zijn op Cloud lagen: **eerste down load** en **proactieve intrekken**.

#### <a name="initial-download"></a>Eerste down load

Wanneer een server verbinding maakt met een Azure-bestands share met bestanden, kunt u nu bepalen hoe u wilt dat de server de bestands share gegevens oorspronkelijk downloadt. Als Cloud lagen zijn ingeschakeld, hebt u twee opties. 

![Een scherm opname van de eerste down load wanneer Cloud lagen zijn ingeschakeld](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-3.png)  

De eerste optie is om eerst de naam ruimte in te trekken en vervolgens de bestands inhoud op het tijdstip van de laatste wijziging op te halen totdat de lokale schijf capaciteit is bereikt. Als er voldoende schijf ruimte is en u weet dat bestanden die het laatst zijn gewijzigd, lokaal in de cache moeten worden opgeslagen, is deze optie ideaal. Bestanden die niet lokaal kunnen worden opgeslagen omdat er geen schijf ruimte meer beschikbaar is, worden getierd.        

De tweede optie is om eerst de naam ruimte in te trekken en de bestands inhoud alleen in te trekken wanneer deze wordt geopend. Deze optie is het beste als u de capaciteit die op uw lokale schijf moet worden gebruikt, wilt minimaliseren en wilt dat gebruikers bepalen welke bestanden lokaal in de cache moeten worden opgeslagen. Alle bestanden worden als gelaagde bestanden met deze optie gestart.

![Een scherm opname van de eerste down load wanneer Cloud lagen zijn uitgeschakeld](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-4.png)

Als Cloud lagen zijn uitgeschakeld, hebt u een derde optie, waarmee u alle gelaagde bestanden helemaal kunt voor komen. In dit geval worden bestanden alleen weer gegeven op de server nadat ze volledig zijn gedownload. Dit is ideaal als u toepassingen hebt waarvoor volledige bestanden aanwezig moeten zijn en geen gelaagde bestanden in de naam ruimte kunnen verdragen.      

#### <a name="proactive-recalling"></a>Proactief wordt ingetrokken

Wanneer een bestand wordt gemaakt of gewijzigd, kunt u proactief een bestand intrekken naar servers die u opgeeft. Hierdoor is het nieuwe of gewijzigde bestand direct beschikbaar voor gebruik in elke opgegeven server. 

Een wereld wijd gedistribueerd bedrijf heeft bijvoorbeeld filialen in de Verenigde Staten en in India. In de ochtend (Amerikaanse tijd) informatie medewerkers maken we een nieuwe map en nieuwe bestanden voor een gloed nieuw project en werken ze allemaal op de hele dag. Azure File Sync synchroniseert de map en bestanden naar de Azure-bestands share (Cloud-eind punt). Informatie medewerkers in India blijven werken aan het project in hun tijd zone. Wanneer deze 's morgens arriveren, moeten de lokale Azure File Sync ingeschakelde server in India deze nieuwe bestanden lokaal beschikbaar stellen, zodat het India-team op een efficiënte manier op een lokale cache kan werken. Als u deze modus inschakelt, voor komt u dat de initiële bestands toegang langzamer is vanwege on-demand intrekken en kan de server de bestanden proactief terughalen zodra ze zijn gewijzigd of gemaakt in de Azure-bestands share.

Als bestanden die naar de server worden teruggeroepen niet echt lokaal nodig zijn, kan het uitvallen van het uitgaande verkeer en de kosten worden verhoogd. Daarom kunt u proactieve intrekken alleen inschakelen wanneer u weet dat het vooraf invullen van de cache van een server met recente wijzigingen in de Cloud een positief effect heeft op gebruikers of toepassingen die gebruikmaken van de bestanden op die server. 

Het inschakelen van proactieve oproepen kan ook leiden tot een groter bandbreedte gebruik op de server en kan ertoe leiden dat andere relatief nieuwe inhoud op de lokale server agressief wordt gelaagd als gevolg van de toename van de bestanden die worden ingetrokken. Op zijn beurt kan dit ertoe leiden dat de bestanden die worden getierd, worden beschouwd als dynamisch door servers. 

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Een afbeelding met het Download gedrag van de Azure-bestands share voor een server eindpunt dat momenteel actief is en een knop om een menu te openen waarmee het kan worden gewijzigd.":::

Zie [Deploy Azure file sync](storage-sync-files-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share)voor meer informatie over proactieve oproepen.

## <a name="tiered-vs-locally-cached-file-behavior"></a>Gedrag van gelaagde versus lokaal opgeslagen bestanden

Cloud lagen zijn de schei ding tussen de naam ruimte (de bestands-en maphiërarchie, evenals bestands eigenschappen) en de bestands inhoud. 

#### <a name="tiered-file"></a>Gelaagd bestand

Voor gelaagde bestanden is de grootte op schijf nul omdat de bestands inhoud zelf niet lokaal wordt opgeslagen. Wanneer een bestand wordt getierd, vervangt het Azure File Sync File System filter (StorageSync.sys) het bestand lokaal door een pointer (reparsepunt). Het reparsepunt vertegenwoordigt een URL naar het bestand in de Azure-bestands share. Een gelaagd bestand heeft zowel het kenmerk ' offline ' als het kenmerk FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS in NTFS ingesteld, zodat toepassingen van derden veilig gelaagde bestanden kunnen identificeren.   

![Een scherm afbeelding van de eigenschappen van een bestand wanneer deze alleen in een trapsgewijs gelaagde naam ruimte wordt weer gave.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)    

#### <a name="locally-cached-file"></a>Lokaal in cache opgeslagen bestand

Aan de andere kant, voor een bestand dat is opgeslagen op een on-premises Bestands server, is de grootte op schijf ongeveer gelijk aan de logische grootte van het bestand, aangezien het hele bestand (bestands kenmerken en bestands inhoud) lokaal wordt opgeslagen.     

![Een scherm afbeelding van de eigenschappen van een bestand wanneer deze niet is gelaagd-naam ruimte en bestands inhoud.](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png) 

Het is ook mogelijk dat een bestand gedeeltelijk wordt gelaagd (of gedeeltelijk wordt ingetrokken). In een gedeeltelijk gelaagd bestand bevindt zich een deel van het bestand op schijf. Dit kan gebeuren wanneer bestanden gedeeltelijk worden gelezen door toepassingen als multimedia spelers of zip-hulpprogram ma's die ondersteuning bieden voor streaming-toegang tot bestanden. Azure File Sync is slim en roept alleen de aangevraagde informatie op van de verbonden Azure-bestands share.

> [!NOTE]
> Grootte staat voor de logische grootte van het bestand. Grootte op schijf staat voor de fysieke grootte van de bestands stroom die op de schijf is opgeslagen.

## <a name="next-steps"></a>Volgende stappen
* [Kies Azure File Sync beleid voor Cloud lagen](storage-sync-choose-cloud-tiering-policies.md)
* [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)