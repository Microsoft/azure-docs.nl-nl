---
title: Aanbevolen procedures voor het gebruik van Azure Data Lake Storage Gen2 | Microsoft Docs
description: Meer informatie over de aanbevolen procedures voor gegevens opname, datum beveiliging en prestaties met betrekking tot het gebruik van Azure Data Lake Storage Gen2 (voorheen bekend als Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: 937048ce14b9b05b55cd8d76e7a8c1fd67c63e4d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933705"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Aanbevolen procedures voor het gebruik van Azure Data Lake Storage Gen2

In dit artikel vindt u informatie over de aanbevolen procedures en overwegingen voor het werken met Azure Data Lake Storage Gen2. In dit artikel vindt u informatie over beveiliging, prestaties, tolerantie en bewaking voor Data Lake Storage Gen2. Voordat Data Lake Storage Gen2 werken met echt big data in services zoals Azure HDInsight was complex. U moest gegevens in meerdere Blob Storage-accounts Shard, zodat de PETA byte-opslag en optimale prestaties op die schaal kunnen worden bereikt. Data Lake Storage Gen2 ondersteunt afzonderlijke bestands grootten als Maxi maal 190,7 TiB en de meeste vaste limieten voor prestaties zijn verwijderd. Er zijn echter nog enkele overwegingen die in dit artikel worden behandeld, zodat u de beste prestaties kunt verkrijgen met Data Lake Storage Gen2.

## <a name="security-considerations"></a>Beveiligingsoverwegingen

Azure Data Lake Storage Gen2 biedt POSIX-toegangs controles voor Azure Active Directory (Azure AD)-gebruikers,-groepen en-service-principals. Deze toegangs elementen kunnen worden ingesteld op bestaande bestanden en mappen. De toegangs elementen kunnen ook worden gebruikt om standaard machtigingen te maken die automatisch kunnen worden toegepast op nieuwe bestanden of mappen. Meer informatie over Data Lake Storage Gen2 Acl's vindt u [in azure data Lake Storage Gen2 voor toegangs beheer](./data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Beveiligings groepen gebruiken versus afzonderlijke gebruikers

Bij het werken met big data in Data Lake Storage Gen2 is het waarschijnlijk dat een service-principal wordt gebruikt om services als Azure HDInsight te laten werken met de gegevens. Er kunnen echter gevallen zijn waarin individuele gebruikers ook toegang tot de gegevens nodig hebben. In alle gevallen kunt u het beste Azure Active Directory- [beveiligings groepen](../common/storage-auth-aad.md) gebruiken in plaats van afzonderlijke gebruikers toe te wijzen aan mappen en bestanden.

Als aan een beveiligings groep machtigingen zijn toegewezen, moeten er geen updates meer Data Lake Storage Gen2 worden toegevoegd aan of verwijderd uit de groep. Dit zorgt er ook voor dat u het maximum aantal vermeldingen voor toegangs beheer per toegangs beheer lijst (ACL) niet overschrijdt. Op dit moment is dat aantal 32, (inclusief de vier Acl's voor POSIX-stijlen die altijd zijn gekoppeld aan elk bestand en elke map): de gebruiker die eigenaar is, de groep die eigenaar is, het masker en andere. Elke directory kan twee typen ACL, de toegangs-ACL en de standaard-ACL hebben, voor een totaal van 64-vermeldingen voor toegangs beheer. Zie [toegangs beheer in azure data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over deze acl's.

### <a name="security-for-groups"></a>Beveiliging voor groepen

Wanneer u of uw gebruikers toegang nodig hebben tot gegevens in een opslag account waarvoor een hiërarchische naam ruimte is ingeschakeld, kunt u het beste Azure Active Directory-beveiligings groepen gebruiken. Enkele aanbevolen groepen om te beginnen met kunnen **ReadOnlyUsers**, **WriteAccessUsers** en **FullAccessUsers** zijn voor de hoofdmap van de container, en zelfs voor sleutel submappen. Als er andere groepen gebruikers zijn die later kunnen worden toegevoegd, maar die nog niet zijn geïdentificeerd, kunt u overwegen om dummy beveiligings groepen te maken die toegang tot bepaalde mappen hebben. Door gebruik te maken van een beveiligings groep, zorgt u ervoor dat u lange verwerkings tijd kunt voor komen bij het toewijzen van nieuwe machtigingen aan duizenden bestanden.

### <a name="security-for-service-principals"></a>Beveiliging voor service-principals

Azure Active Directory service-principals worden meestal gebruikt door services als Azure Databricks om toegang te krijgen tot gegevens in Data Lake Storage Gen2. Voor veel klanten kan een enkele Azure Active Directory Service-Principal voldoende zijn en kan deze volledige machtigingen hebben in de hoofdmap van de Data Lake Storage Gen2-container. Andere klanten hebben mogelijk meerdere clusters met verschillende service-principals, waarbij één cluster volledige toegang tot de gegevens heeft, en een ander cluster met alleen lees toegang. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>De Data Lake Storage Gen2 firewall inschakelen met toegang tot de Azure-service

Data Lake Storage Gen2 ondersteunt de mogelijkheid om een firewall in te scha kelen en de toegang alleen te beperken tot Azure-Services. dit wordt aanbevolen om de vector van externe aanvallen te beperken. De firewall kan worden ingeschakeld voor een opslag account in de Azure portal via **firewall**  >  **firewall inschakelen (ingeschakeld)**  >  **toegang tot opties voor Azure-Services toestaan** .

Om toegang te krijgen tot uw opslag account vanuit Azure Databricks, implementeert u Azure Databricks naar uw virtuele netwerk en voegt u dat virtuele netwerk vervolgens toe aan uw firewall. Zie [Azure Storage firewalls en virtuele netwerken configureren](../common/storage-network-security.md).

## <a name="resiliency-considerations"></a>Overwegingen voor tolerantie

Bij het ontwerpen van een systeem met Data Lake Storage Gen2 of een Cloud service, moet u rekening houden met uw beschikbaarheids vereisten en hoe u kunt reageren op mogelijke onderbrekingen in de service. Een probleem kan worden gelokaliseerd voor het specifieke exemplaar of zelfs voor de hele regio, zodat een plan voor beide belang rijk is. Afhankelijk van de beoogde herstel tijd en de Recovery Point Objective-Sla's voor uw workload, kunt u een meer of minder agressieve strategie kiezen voor hoge Beschik baarheid en herstel na nood gevallen.

### <a name="high-availability-and-disaster-recovery"></a>Hoge beschikbaarheid en herstel na noodgevallen

Hoge Beschik baarheid (HA) en herstel na nood gevallen (DR) kunnen soms samen worden gecombineerd, hoewel elk een iets andere strategie heeft, met name wanneer het gaat om gegevens. Data Lake Storage Gen2 heeft reeds 3x-replicatie op de schermen afgehandeld tegen de beveiliging tegen gelokaliseerde hardwarestoringen. Daarnaast kunt u met andere replicatie opties, zoals ZRS of GZRS, de HA verbeteren, terwijl GRS & RA-GRS DR verbeteren. Bij het bouwen van een plan voor HA moet de werk belasting, in het geval van een onderbreking van de service, zo snel mogelijk toegang hebben tot de meest recente gegevens door over te scha kelen naar een afzonderlijk gerepliceerd exemplaar lokaal of in een nieuwe regio.

In een DR-strategie is het belang rijk dat de gegevens die worden gerepliceerd naar een andere regio met GRS of RA-GRS-replicatie, kunnen worden voor bereid voor het onwaarschijnlijke geval van een ernstige storing van een regio. U moet ook rekening houden met uw vereisten voor rand gevallen, zoals beschadiging van gegevens, waarbij u mogelijk periodieke moment opnamen wilt maken om terug te vallen op. Afhankelijk van het belang en de grootte van de gegevens, kunt u het beste Delta momentopnamen van 1-, 6-en 24-uurs periode op basis van risico toleranties.

Voor gegevens tolerantie met Data Lake Storage Gen2, wordt het aanbevolen geo-replicatie uit te laten op basis van uw gegevens via GRS of RA-GRS die voldoet aan uw HA/DR-vereisten. Daarnaast moet u voor de toepassing rekening houden met behulp van Data Lake Storage Gen2 om automatisch een failover uit te geven naar de secundaire regio via bewakings triggers of de duur van mislukte pogingen, of om ten minste een melding aan beheerders te verzenden voor hand matige tussen komst. Houd er bij het afnemen van een afweging van de failover uit en wacht totdat een service weer online komt.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Distcp gebruiken voor het verplaatsen van gegevens tussen twee locaties

Kort voor gedistribueerde kopieën is DistCp een Linux-opdracht regel programma dat wordt meegeleverd met Hadoop en waarmee gedistribueerde gegevens tussen twee locaties kunnen worden verplaatst. De twee locaties kunnen Data Lake Storage Gen2, HDFS of S3 zijn. Dit hulp programma maakt gebruik van MapReduce-taken in een Hadoop-cluster (bijvoorbeeld HDInsight) om uit te schalen op alle knoop punten. Distcp wordt beschouwd als de snelste manier om big data te verplaatsen zonder speciale netwerk compressie-apparaten. Distcp biedt ook een optie om verschillen tussen twee locaties bij te werken, automatische nieuwe pogingen te verwerken, evenals dynamische schaling van compute. Deze aanpak is zeer efficiënt wanneer het gaat om het repliceren van dingen als Hive/Spark-tabellen die veel grote bestanden in één map kunnen hebben en u alleen wilt kopiëren over de gewijzigde gegevens. Daarom is Distcp het meest aanbevolen hulp middel voor het kopiëren van gegevens tussen big data winkels.

Kopieer taken kunnen worden geactiveerd door Apache Oozie-werk stromen met behulp van frequentie-of gegevens triggers, evenals Linux cron-taken. Voor intensieve replicatie taken is het raadzaam om een afzonderlijk HDInsight Hadoop-cluster op te zetten dat specifiek voor de Kopieer taken kan worden afgestemd en geschaald. Dit zorgt ervoor dat het kopiëren van taken geen problemen veroorzaakt met kritieke taken. Als replicatie op een brede frequentie wordt uitgevoerd, kan het cluster zelfs tussen elke taak worden afgesloten. Als er een failover wordt uitgevoerd naar een secundaire regio, zorgt u ervoor dat een ander cluster ook in de secundaire regio wordt weer gegeven om nieuwe gegevens te repliceren naar het primaire Data Lake Storage Gen2-account zodra er een back-up is gemaakt. Zie [Distcp gebruiken om gegevens te kopiëren tussen Azure Storage blobs en data Lake Storage Gen2](../blobs/data-lake-storage-use-distcp.md)voor voor beelden van het gebruik van Distcp.

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Azure Data Factory gebruiken om Kopieer taken te plannen

Azure Data Factory kan ook worden gebruikt voor het plannen van Kopieer taken met behulp van een Kopieer activiteit en kan zelfs worden ingesteld op basis van een frequentie via de wizard kopiëren. Houd er rekening mee dat Azure Data Factory een limiet van DMUs (Cloud data units) heeft en uiteindelijk de door Voer/Compute voor grote gegevens workloads optekent. Daarnaast biedt Azure Data Factory momenteel geen Delta-updates tussen Data Lake Storage Gen2-accounts, waardoor mappen als Hive-tabellen een volledige kopie nodig hebben om te repliceren. Raadpleeg het [Data Factory artikel](../../data-factory/load-azure-data-lake-storage-gen2.md) voor meer informatie over het kopiëren met Data Factory.

## <a name="monitoring-considerations"></a>Bewakings overwegingen

Data Lake Storage Gen2 biedt metrische gegevens in de Azure Portal onder het Data Lake Storage Gen2-account en in Azure Monitor. De beschik baarheid van Data Lake Storage Gen2 wordt weer gegeven in de Azure Portal. Als u de meest recente Beschik baarheid van een Data Lake Storage Gen2 account wilt krijgen, moet u uw eigen synthetische tests uitvoeren om Beschik baarheid te valideren. Andere metrische gegevens, zoals het totale opslag gebruik, lees-en schrijf aanvragen, en binnenkomen/uitgaand zijn beschikbaar om te worden gebruikt door toepassingen te bewaken en kunnen ook waarschuwingen activeren wanneer drempel waarden (bijvoorbeeld gemiddelde latentie of aantal fouten per minuut) worden overschreden.

## <a name="directory-layout-considerations"></a>Overwegingen voor Directory-indeling

Wanneer gegevens naar een Data Lake worden gelandd, is het belang rijk om de structuur van de gegevens vooraf te plannen, zodat de beveiliging, partitionering en verwerking effectief kunnen worden gebruikt. Veel van de volgende aanbevelingen zijn van toepassing op alle big data workloads. Elke workload heeft verschillende vereisten voor de manier waarop de gegevens worden verbruikt, maar hieronder volgen enkele algemene indelingen waarmee u rekening moet houden bij het werken met IoT-en batch-scenario's.

### <a name="iot-structure"></a>IoT-structuur

In IoT-workloads kunnen er grote hoeveel heden gegevens in het gegevens archief worden gelandd dat over talloze producten, apparaten, organisaties en klanten kan worden verspreid. Het is belang rijk om vooraf de Directory-indeling te plannen voor de organisatie, beveiliging en efficiënte verwerking van de gegevens voor gebruikers van de andere stroom. Een algemene sjabloon waarmee u rekening moet houden, kan de volgende indeling hebben:

*{Region}/{SubjectMatter (s)}/{yyyy}/{mm}/{dd}/{hh}/*

Bijvoorbeeld, telemetrie voor een vliegtuig motor binnen het Verenigd Konink rijk kan er als volgt uitzien:

*GB/abonnementen/BA1293/Engine1/2017/08/11/12/*

Er is een belang rijke reden om de datum toe te voegen aan het einde van de mapstructuur. Als u bepaalde regio's of onderwerpen wilt vergren delen voor gebruikers/groepen, kunt u dit eenvoudig doen met de POSIX-machtigingen. Als er anders een bepaalde beveiligings groep moet worden beperkt om alleen de UK-gegevens of bepaalde abonnementen weer te geven, is de datum structuur voor een afzonderlijke machtiging vereist voor talloze mappen onder elke map uur. Als de datum structuur op de voor grond ligt, zou het aantal directory's tijdens het tijdstip exponentieel worden verhoogd.

### <a name="batch-jobs-structure"></a>Structuur batch taken

Vanuit een hoog niveau is een veelgebruikte aanpak in batch verwerking het gebruik van gegevens in een map in. Zodra de gegevens zijn verwerkt, plaatst u de nieuwe gegevens in een out-map voor downstream-processen die u wilt gebruiken. Deze mapstructuur is soms ook bedoeld voor taken die verwerking op afzonderlijke bestanden vereisen en mogelijk geen zeer parallelle verwerking vereist voor grote gegevens sets. Net zoals de IoT-structuur die hierboven wordt aanbevolen, beschikt een goede Directory structuur over de bovenliggende mappen voor zaken als regio en onderwerp (bijvoorbeeld organisatie, product/producent). Deze structuur helpt bij het beveiligen van de gegevens in uw organisatie en het betere beheer van de gegevens in uw workloads. Denk bovendien na over de datum en tijd in de structuur om een betere organisatie, gefilterde Zoek opdrachten, beveiliging en automatisering in de verwerking mogelijk te maken. Het granulatie niveau voor de datum structuur wordt bepaald door het interval waarop de gegevens worden geüpload of verwerkt, zoals elk uur, dagelijks of zelfs maandelijks.

Soms wordt bestands verwerking niet geslaagd als gevolg van gegevens beschadiging of onverwachte notaties. In dergelijke gevallen kan de mapstructuur van een **/Bad** -map de bestanden naar aanleiding van de nieuwe inspectie verplaatsen. De batch-taak kan ook de rapportage of melding van deze *beschadigde* bestanden verwerken voor hand matige tussen komst. Houd rekening met de volgende sjabloon structuur:

*{Region}/{SubjectMatter (s)}/in/{yyyy}/{mm}/{dd}/{hh}/*\
*{Region}/{SubjectMatter (s)}/out/{yyyy}/{mm}/{dd}/{hh}/*\
*{Region}/{SubjectMatter (s)}/bad/{yyyy}/{mm}/{dd}/{hh}/*

Zo ontvangt een marketing bedrijf dagelijks gegevens uittreksels van klant updates van hun clients in Noord-Amerika. Het kan er voor en na het verwerken van het volgende fragment uitzien:

*N.V.T./extracten/ACMEPaperCo/in/2017/08/14/updates_08142017.csv*\
*N.V.T./extracten/ACMEPaperCo/uit/2017/08/14/processed_updates_08142017.csv*

In het gemeen schappelijke geval van batch gegevens die rechtstreeks in data bases worden verwerkt, zoals Hive of traditionele SQL-data bases, is er geen behoefte aan een **/in** -of **/out** -map omdat de uitvoer al naar een afzonderlijke map voor de Hive-tabel of externe data base gaat. Dagelijkse uittreksels van klanten zijn bijvoorbeeld in hun respectieve mappen gelandd, en met de indeling van een probleem zoals Azure Data Factory, Apache Oozie of Apache-lucht flow wordt een dagelijkse Hive-of Spark-taak geactiveerd om de gegevens in een Hive-tabel te verwerken en te schrijven.
