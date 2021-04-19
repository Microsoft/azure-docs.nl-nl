---
title: Migratie van de StorSimple 8000-serie naar Azure File Sync
description: Meer informatie over het migreren van een StorSimple 8100- of 8600-apparaat naar Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7bde8fe404e0839bf14500bff4fb92ce8cc4ea04
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717347"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>Migratie van StorSimple 8100 en 8600 naar Azure File Sync

De StorSimple 8000-serie wordt vertegenwoordigd door de fysieke 8100- of 8600-apparaten en hun cloudserviceonderdelen. Het is mogelijk om de gegevens van een van deze apparaten te migreren naar een Azure File Sync omgeving. Azure File Sync is de standaard- en strategische Azure-service voor de lange termijn waar StorSimple-apparaten naar kunnen worden gemigreerd.

De StorSimple 8000-serie bereikt [in](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) december 2022 het einde van de levensduur. Het is belangrijk dat u zo snel mogelijk begint met het plannen van uw migratie. Dit artikel bevat de benodigde achtergrondkennis en migratiestappen voor een geslaagde migratie naar Azure File Sync.

## <a name="phase-1-prepare-for-migration"></a>Fase 1: voorbereiden op migratie

Deze sectie bevat de stappen die u moet uitvoeren aan het begin van de migratie van StorSimple-volumes naar Azure-bestands shares.

### <a name="inventory"></a>Inventaris

Wanneer u begint met het plannen van de migratie, moet u eerst alle StorSimple-apparaten en -volumes identificeren die u moet migreren. Nadat u dat hebt gedaan, kunt u bepalen wat het beste migratiepad voor u is.

* Fysieke StorSimple-apparaten (8000-serie) maken gebruik van deze migratiehandleiding.
* Virtuele apparaten, [de StorSimple 1200-serie, gebruiken een andere migratiehandleiding.](storage-files-migration-storsimple-1200.md)

### <a name="migration-cost-summary"></a>Overzicht van migratiekosten

Migraties naar Azure-bestands shares van StorSimple-volumes via migratietaken in een StorSimple Data Manager-resource zijn gratis. Er kunnen andere kosten in rekening worden gebracht tijdens en na een migratie:

* **Netwerkegressie:** Uw StorSimple-bestanden staan in een opslagaccount binnen een specifieke Azure-regio. Als u de Azure-bestands shares inrichten die u migreert naar een opslagaccount dat zich in dezelfde Azure-regio bevindt, treden er geen kosten op voor het uitvoeren van gegevens. U kunt uw bestanden als onderdeel van deze migratie verplaatsen naar een opslagaccount in een andere regio. In dat geval zijn de kosten voor uit te gaan op u van toepassing.
* **Azure-bestands sharetransacties:** Wanneer bestanden worden gekopieerd naar een Azure-bestands share (als onderdeel van een migratie of erbuiten), zijn transactiekosten van toepassing wanneer bestanden en metagegevens worden geschreven. Als best practice kunt u uw Azure-bestands share tijdens de migratie starten in de laag die is geoptimaliseerd voor transacties. Schakel over naar de gewenste laag nadat de migratie is voltooid. In de volgende fasen wordt dit op het juiste moment aanroepen.
* **Een Azure-bestands sharelaag wijzigen:** Het wijzigen van de laag van een Azure-bestands share kost transacties. In de meeste gevallen is het rendabeler om het advies van het vorige punt te volgen.
* **Opslagkosten:** Wanneer deze migratie begint met het kopiëren van bestanden naar een Azure-bestands share, wordt Azure Files opslag verbruikt en gefactureerd. Gemigreerde back-ups worden [momentopnamen van Azure-bestands delen.](storage-snapshots-files.md) Momentopnamen van bestands share verbruiken alleen opslagcapaciteit voor de verschillen die ze bevatten.
* **StorSimple:** Totdat u de StorSimple-apparaten en opslagaccounts kunt verwijderen, blijven de StorSimple-kosten voor opslag, back-ups en apparaten bestaan.

### <a name="direct-share-access-vs-azure-file-sync"></a>Direct-share-access vs. Azure File Sync

Azure-bestands shares bieden een geheel nieuwe wereld aan mogelijkheden voor het structureren van de implementatie van uw bestandsservices. Een Azure-bestands share is slechts een SMB-share in de cloud die u kunt instellen om gebruikers rechtstreeks toegang te geven via het SMB-protocol met de vertrouwde Kerberos-verificatie en bestaande NTFS-machtigingen (bestands- en map-ACL's) die systeemeigen werken. Meer informatie over [op identiteit gebaseerde toegang tot Azure-bestands shares.](storage-files-active-directory-overview.md)

Een alternatief voor directe toegang is [Azure File Sync.](./storage-sync-files-planning.md) Azure File Sync is een directe analogie voor de mogelijkheid van StorSimple om veelgebruikte bestanden on-premises in de cache op te slaan.

Azure File Sync is een Microsoft-cloudservice, op basis van twee hoofdonderdelen:

* Bestandssynchronisatie en cloudopslaglagen om een cache voor prestatietoegang te maken op elke Windows-server.
* Bestands shares als native opslag in Azure die toegankelijk zijn via meerdere protocollen, zoals SMB en file REST.

Azure-bestands shares behouden belangrijke aspecten van bestandstrouwheid voor opgeslagen bestanden, zoals kenmerken, machtigingen en tijdstempels. Met Azure-bestands shares is het niet meer nodig dat een toepassing of service de bestanden en mappen interpreteert die zijn opgeslagen in de cloud. U hebt standaard toegang tot deze protocollen via vertrouwde protocollen en clients, zoals Windows Verkenner. Met Azure-bestands shares kunt u algemene bestandsservergegevens en toepassingsgegevens opslaan in de cloud. Het maken van back-ups van een Azure-bestands share is een ingebouwde functionaliteit die verder kan worden uitgebreid door Azure Backup.

Dit artikel is gericht op de migratiestappen. Als u meer wilt weten over de Azure File Sync migreren, bekijkt u de volgende artikelen:

* [Azure File Sync overzicht](./storage-sync-files-planning.md "Overzicht")
* [Azure File Sync implementatiehandleiding](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>Gegevensversleutelingssleutel van de StorSimple-service

Toen u uw StorSimple-apparaat voor het eerst in stelde, werd er een 'servicegegevensversleutelingssleutel' gegenereerd en werd u geïnstrueerd de sleutel veilig op te slaan. Deze sleutel wordt gebruikt voor het versleutelen van alle gegevens in het bijbehorende Azure-opslagaccount waarin uw bestanden worden opgeslagen op het StorSimple-apparaat.

De versleutelingssleutel voor servicegegevens is nodig voor een geslaagde migratie. Dit is een goed moment om deze sleutel op te halen uit uw records, één voor elk van de apparaten in uw inventaris.

Als u de sleutels in uw records niet kunt vinden, kunt u een nieuwe sleutel genereren op basis van het apparaat. Elk apparaat heeft een unieke versleutelingssleutel.

#### <a name="change-the-service-data-encryption-key"></a>De versleutelingssleutel voor servicegegevens wijzigen

[!INCLUDE [storage-files-migration-generate-key](../../../includes/storage-files-migration-generate-key.md)]

> [!CAUTION]
> Houd rekening met het volgende wanneer u besluit hoe u verbinding maakt met uw StorSimple-apparaat:
>
> * Verbinding maken via een HTTPS-sessie is de veiligste en aanbevolen optie.
> * Rechtstreeks verbinding maken met de seriële console van het apparaat is veilig, maar verbinding maken met de seriële console via netwerkswitches is dat niet.
> * HTTP-sessieverbindingen zijn een optie, maar *zijn niet versleuteld.* Ze worden niet aanbevolen, tenzij ze worden gebruikt in een gesloten, vertrouwd netwerk.

### <a name="storsimple-volume-backups"></a>StorSimple-volumeback-ups

StorSimple biedt differentiële back-ups op volumeniveau. Azure-bestands shares hebben deze mogelijkheid ook wel share-momentopnamen genoemd.
Uw migratietaken kunnen alleen back-ups verplaatsen, niet gegevens van het livevolume. De meest recente back-up moet dus altijd in de lijst staan met back-ups die tijdens een migratie zijn verplaatst.

Bepaal of u oudere back-ups moet verplaatsen tijdens de migratie.
De best practice is om deze lijst zo klein mogelijk te houden, zodat uw migratietaken sneller worden voltooid.

Als u kritieke back-ups wilt identificeren die moeten worden gemigreerd, maakt u een controlelijst van uw back-upbeleid. Bijvoorbeeld:
* De meest recente back-up. (Opmerking: De meest recente back-up moet altijd deel uitmaken van deze lijst.)
* Eén back-up per maand voor 12 maanden.
* Eén back-up per jaar voor drie jaar. 

Later, wanneer u uw migratietaken maakt, kunt u deze lijst gebruiken om de exacte StorSimple-volumeback-ups te identificeren die moeten worden gemigreerd om te voldoen aan de vereisten in uw lijst.

> [!CAUTION]
> Het selecteren van **meer dan 50** StorSimple-volumeback-ups wordt niet ondersteund.
> Uw migratietaken kunnen alleen back-ups verplaatsen, nooit gegevens van het livevolume. De meest recente back-up is daarom het dichtst bij de livegegevens en moet dus altijd deel uitmaken van de lijst met back-ups die in een migratie moeten worden verplaatst.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Uw bestaande StorSimple-volumes aan Azure-bestands shares toevoegen

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Aantal opslagaccounts

Uw migratie zal waarschijnlijk profiteren van een implementatie van meerdere opslagaccounts die elk een kleiner aantal Azure-bestands shares hebben.

Als uw bestands shares zeer actief zijn (gebruikt door veel gebruikers of toepassingen), kunnen twee Azure-bestands shares de prestatielimiet van uw opslagaccount bereiken. Daarom is het best practice om te migreren naar meerdere opslagaccounts, elk met hun eigen afzonderlijke bestands shares, en meestal niet meer dan twee of drie shares per opslagaccount.

Een best practice is het implementeren van opslagaccounts met elk één bestands share. U kunt meerdere Azure-bestands shares in hetzelfde opslagaccount groepen, als u er archiverings shares in hebt.

Deze overwegingen zijn meer van toepassing [op directe cloudtoegang](#direct-share-access-vs-azure-file-sync) (via een Azure-VM of -service) dan op Azure File Sync. Als u van plan bent om alleen Azure File Sync op deze shares te gebruiken, is het prima om meerdere shares te groeperen in één Azure-opslagaccount. In de toekomst wilt u mogelijk een app naar de cloud verplaatsen die vervolgens rechtstreeks toegang zou krijgen tot een bestands share. Dit scenario zou profiteren van een hogere IOPS en doorvoer. U kunt ook een service in Azure gaan gebruiken die ook kan profiteren van een hogere IOPS en doorvoer.

Als u een lijst met uw shares hebt gemaakt, moet u elke share toe te kennen aan het opslagaccount waar deze zich bevindt.

> [!IMPORTANT]
> Bepaal een Azure-regio en zorg ervoor dat elk opslagaccount en elke resource Azure File Sync overeenkomt met de regio die u hebt geselecteerd.
> Configureer nu geen netwerk- en firewallinstellingen voor de opslagaccounts. Het maken van deze configuraties op dit moment zou een migratie onmogelijk maken. Configureer deze Azure-opslaginstellingen nadat de migratie is voltooid.

### <a name="phase-1-summary"></a>Samenvatting fase 1

Aan het einde van fase 1:

* U hebt een goed overzicht van uw StorSimple-apparaten en -volumes.
* De Data Manager-service is klaar voor toegang tot uw StorSimple-volumes in de cloud, omdat u uw 'servicegegevensversleutelingssleutel' voor elk StorSimple-apparaat hebt opgehaald.
* U hebt een plan voor welke volumes en back-ups (indien van plan) moeten worden gemigreerd.
* U weet hoe u uw volumes kunt koppelen aan het juiste aantal Azure-bestands shares en opslagaccounts.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>Fase 2: Azure-opslag- en migratie-resources implementeren

In deze sectie worden overwegingen besproken voor het implementeren van de verschillende resourcetypen die nodig zijn in Azure. Sommige bevatten uw gegevens na de migratie en sommige zijn alleen nodig voor de migratie. Begin pas met het implementeren van resources als u uw implementatieplan hebt afgerond. Het is moeilijk, soms onmogelijk, om bepaalde aspecten van uw Azure-resources te wijzigen nadat deze zijn geïmplementeerd.

### <a name="deploy-storage-accounts"></a>Opslagaccounts implementeren

Waarschijnlijk moet u verschillende Azure-opslagaccounts implementeren. Elk bestand bevat een kleiner aantal Azure-bestands shares, overeenkomstig uw implementatieplan, dat is voltooid in de vorige sectie van dit artikel. Ga naar de Azure Portal uw [geplande opslagaccounts te implementeren.](../common/storage-account-create.md#create-a-storage-account) U kunt zich houden aan de volgende basisinstellingen voor elk nieuw opslagaccount.

> [!IMPORTANT]
> Configureer nu geen netwerk- en firewallinstellingen voor uw opslagaccounts. Het maken van deze configuraties op dit moment zou een migratie onmogelijk maken. Configureer deze Azure-opslaginstellingen nadat de migratie is voltooid.

#### <a name="subscription"></a>Abonnement

U kunt hetzelfde abonnement gebruiken dat u hebt gebruikt voor uw StorSimple-implementatie of een ander abonnement. De enige beperking is dat uw abonnement zich in dezelfde tenant Azure Active Directory als het StorSimple-abonnement. Overweeg het StorSimple-abonnement te verplaatsen naar de juiste tenant voordat u een migratie start. U kunt alleen het hele abonnement verplaatsen. Afzonderlijke StorSimple-resources kunnen niet worden verplaatst naar een andere tenant of een ander abonnement.

#### <a name="resource-group"></a>Resourcegroep

Resourcegroepen helpen bij de organisatie van resources en beheermachtigingen. Meer informatie over [resourcegroepen in Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

#### <a name="storage-account-name"></a>Naam van het opslagaccount

De naam van uw opslagaccount wordt onderdeel van een URL en heeft bepaalde tekenbeperkingen. Houd er in uw naamconventie rekening mee dat namen van opslagaccounts wereldwijd uniek moeten zijn, alleen kleine letters en cijfers mogen bevatten, tussen de 3 en 24 tekens nodig hebben en geen speciale tekens zoals afbreekstreepingen of onderstrepingstekens toestaan. Zie Naamgevingsregels voor [Azure Storage-resources voor meer informatie.](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage)

#### <a name="location"></a>Locatie

De locatie of Azure-regio van een opslagaccount is erg belangrijk. Als u Azure File Sync, moeten al uw opslagaccounts zich in dezelfde regio als uw opslagsynchronisatieserviceresources. De Azure-regio die u kiest, moet dicht bij uw lokale servers en gebruikers staan of centraal staan. Nadat uw resource is geïmplementeerd, kunt u de regio ervan niet meer wijzigen.

U kunt een andere regio kiezen waar uw StorSimple-gegevens (opslagaccount) zich momenteel bevinden.

> [!IMPORTANT]
> Als u een andere regio kiest uit uw huidige [](https://azure.microsoft.com/pricing/details/bandwidth) StorSimple-opslagaccountlocatie, worden er tijdens de migratie kosten voor het uitstromen van gegevens in rekening gebracht. Gegevens verlaten de StorSimple-regio en voeren uw nieuwe opslagaccountregio in. Er zijn geen bandbreedtekosten van toepassing als u binnen dezelfde Azure-regio blijft.

#### <a name="performance"></a>Prestaties

U hebt de mogelijkheid om Premium Storage (SSD) te kiezen voor Azure-bestands shares of standard-opslag. Standard Storage bevat [verschillende lagen voor een bestands share](storage-how-to-create-file-share.md#change-the-tier-of-an-azure-file-share). Standaardopslag is de juiste optie voor de meeste klanten die migreren van StorSimple.

Nog steeds niet zeker?

* Kies Premium Storage als u de prestaties van een [Premium Azure-bestands share nodig hebt.](understanding-billing.md#provisioned-model)
* Kies standaardopslag voor algemene bestandsserverworkloads, waaronder hot-gegevens en archiefgegevens. Kies ook standaardopslag als de enige workload op de share in de cloud wordt Azure File Sync.

#### <a name="account-kind"></a>Soort account

* Voor standaardopslag kiest u *StorageV2 (algemeen gebruik v2).*
* Voor Premium-bestands shares kiest *u BestandOpslag.*

#### <a name="replication"></a>Replicatie

Er zijn verschillende replicatie-instellingen beschikbaar. Meer informatie over de verschillende replicatietypen.

Kies uit een van de volgende twee opties:

* *Lokaal redundante opslag (LRS).*
* *Zone-redundante opslag (ZRS),* die niet beschikbaar is in alle Azure-regio's.

> [!NOTE]
> Alleen LRS- en ZRS-redundantietypen zijn compatibel met de grote Azure-bestands shares met een capaciteit van 100 TiB.

Geografisch redundante opslag (GRS) in alle variaties wordt momenteel niet ondersteund. U kunt uw redundantietype later overschakelen naar GRS wanneer er ondersteuning voor is in Azure.

#### <a name="enable-100-tib-capacity-file-shares"></a>Bestands shares met een capaciteit van 100 TiB inschakelen

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Een afbeelding met het tabblad Geavanceerd in de Azure Portal voor het maken van een opslagaccount.":::
    :::column-end:::
    :::column:::
        Onder de **sectie Geavanceerd** van de wizard nieuw opslagaccount in de Azure Portal kunt u ondersteuning voor grote bestands **shares** inschakelen in dit opslagaccount. Als deze optie niet beschikbaar is, hebt u waarschijnlijk het verkeerde type redundantie geselecteerd. Zorg ervoor dat u alleen LRS of ZRS selecteert om deze optie beschikbaar te maken.
    :::column-end:::
:::row-end:::

Kiezen voor de grote bestands shares met een capaciteit van 100 TiB heeft verschillende voordelen:

* Uw prestaties worden aanzienlijk verhoogd in vergelijking met de kleinere bestands shares met een capaciteit van 5 TiB (bijvoorbeeld tien keer de IOPS).
* Uw migratie is aanzienlijk sneller.
* U zorgt ervoor dat een bestands share voldoende capaciteit heeft voor alle gegevens die u naar de share migreert, inclusief de opslagcapaciteit die differentiële back-ups vereisen.
* Toekomstige groei wordt gedekt.

### <a name="azure-file-shares"></a>Azure-bestands shares

Nadat uw opslagaccounts zijn  gemaakt, gaat u naar de sectie Bestands share van het opslagaccount en implementeert u het juiste aantal Azure-bestands shares volgens uw migratieplan van fase 1. Overweeg om u te houden aan de volgende basisinstellingen voor uw nieuwe bestands shares in Azure.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Een Azure Portal schermopname van de gebruikersinterface van de nieuwe bestands share.":::
    :::column-end:::
    :::column:::
        </br>**Naam**</br>Kleine letters, cijfers en afbreekstreelopen worden ondersteund.</br></br>**Quotum**</br>Het quotum is hier vergelijkbaar met een SMB-quotum voor een Windows Server-exemplaar. Het best practice is om hier geen quotum in te stellen, omdat uw migratie en andere services mislukken wanneer het quotum is bereikt.</br></br>**Lagen**</br>Selecteer **Transactie geoptimaliseerd voor** uw nieuwe bestands share. Tijdens de migratie worden er veel transacties uitgevoerd. Het is rendabeler om uw laag later te wijzigen in de laag die het meest geschikt is voor uw workload.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple-gegevensbeheer

De Azure-resource die uw migratietaken zal houden, wordt een **StorSimple Data Manager.** Selecteer **Nieuwe resource** en zoek deze. Selecteer vervolgens **Maken**.

Deze tijdelijke resource wordt gebruikt voor orchestration. U maakt deprovisioning ervan op nadat de migratie is voltooid. Deze moet worden geïmplementeerd in hetzelfde abonnement, dezelfde resourcegroep en regio als uw StorSimple-opslagaccount.

### <a name="azure-file-sync"></a>Azure File Sync

Met Azure File Sync kunt u on-premises caching van de meest gebruikte bestanden toevoegen. Net als bij de cachemogelijkheden van StorSimple biedt de functie Azure File Sync-cloudopslaglagen latentie voor lokale toegang in combinatie met verbeterde controle over de beschikbare cachecapaciteit op het Windows Server-exemplaar en synchronisatie van meerdere site. Als het uw doel is om een on-premises cache te hebben, bereidt u in uw lokale netwerk een Windows Server-VM voor (fysieke servers en failoverclusters worden ook ondersteund) met voldoende direct gekoppelde opslagcapaciteit.

> [!IMPORTANT]
> Stel de Azure File Sync nog niet in. Het is het beste om een Azure File Sync de migratie van uw share is voltooid. Het implementeren Azure File Sync mag niet beginnen vóór fase 4 van een migratie.

### <a name="phase-2-summary"></a>Samenvatting fase 2

Aan het einde van fase 2 hebt u uw opslagaccounts en alle Azure-bestands shares op deze accounts geïmplementeerd. U hebt ook een StorSimple Data Manager resource. U gebruikt het laatste in fase 3 wanneer u uw migratietaken configureert.

## <a name="phase-3-create-and-run-a-migration-job"></a>Fase 3: een migratie job maken en uitvoeren

In deze sectie wordt beschreven hoe u een migratie job in kunt stellen en hoe u de map op een StorSimple-volume zorgvuldig kunt in kaart brengen die moet worden gekopieerd naar de Azure-doelbestands share die u selecteert. Ga om te beginnen naar uw StorSimple Data Manager, zoek **taakdefinities** in het menu en selecteer **+ Taakdefinitie**. Het juiste doelopslagtype is de standaardinstelling: **Azure-bestands share**.

![Migratie jobtypen uit de StorSimple 8000-serie.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Er is een schermopname van de taakdefinities Azure Portal met een nieuw dialoogvenster Taakdefinities geopend waarin wordt gevraagd om het type taak: Kopiëren naar een bestands share of een blobcontainer.")

:::row:::
    :::column:::
        ![Migratie job uit de StorSimple 8000-serie.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Een schermopname van het formulier voor het maken van een nieuwe taak voor een migratie job.")
    :::column-end:::
    :::column:::
        **Naam van taakdefinitie**</br>Deze naam moet de set bestanden aangeven die u verplaatst. Het is een goede gewoonte om deze een vergelijkbare naam te geven als uw Azure-bestands share. </br></br>**Locatie waar de taak wordt uitgevoerd**</br>Wanneer u een regio selecteert, moet u dezelfde regio selecteren als uw StorSimple-opslagaccount, of, als dat niet beschikbaar is, dan een regio dicht bij het account. </br></br><h3>Bron</h3>**Bronabonnement**</br>Selecteer het abonnement waarin u uw StorSimple-Apparaatbeheer opgeslagen. </br></br>**StorSimple-resource**</br>Selecteer uw StorSimple-Apparaatbeheer uw apparaat is geregistreerd. </br></br>**Versleutelingssleutel voor servicegegevens**</br>Raadpleeg deze [vorige sectie in dit artikel](#storsimple-service-data-encryption-key) voor het geval u de sleutel niet in uw records kunt vinden. </br></br>**Apparaat**</br>Selecteer uw StorSimple-apparaat met het volume waarop u wilt migreren. </br></br>**Volume**</br>Selecteer het bronvolume. Later bepaalt u of u het hele volume of de subdirectorieën naar de Azure-doelbestands share wilt migreren.</br></br> **Volumeback-ups**</br>U kunt *Volumeback-ups selecteren selecteren om* specifieke back-ups te kiezen die u wilt verplaatsen als onderdeel van deze taak. In een volgende, [speciale sectie in dit artikel](#selecting-volume-backups-to-migrate) wordt het proces uitgebreid besproken.</br></br><h3>Doel</h3>Selecteer het abonnement, het opslagaccount en de Azure-bestands share als het doel van deze migratie job.</br></br><h3>Maptoewijzing</h3>[In een speciale sectie in dit artikel](#directory-mapping)worden alle relevante details besproken.
    :::column-end:::
:::row-end:::

### <a name="selecting-volume-backups-to-migrate"></a>Te migreren volumeback-ups selecteren

Er zijn belangrijke aspecten met het kiezen van back-ups die moeten worden gemigreerd:

- Uw migratietaken kunnen alleen back-ups verplaatsen, geen gegevens van een livevolume. De meest recente back-up is dus het dichtst bij de livegegevens en moet altijd in de lijst staan met back-ups die tijdens een migratie worden verplaatst.
- Zorg ervoor dat uw meest recente back-up recent is om de delta naar de live share zo klein mogelijk te houden. Het kan de moeite waard zijn om een andere volumeback-up handmatig te activeren en te voltooien voordat u een migratie job maakt. Een kleine delta aan de live-share verbetert uw migratie-ervaring. Als deze delta nul kan zijn = er zijn geen wijzigingen meer aangebracht in het StorSimple-volume nadat de nieuwste back-up in uw lijst is gemaakt, wordt de cut-over van de gebruiker aanzienlijk vereenvoudigd en is de cut-over aanzienlijk vereenvoudigd.
- Back-ups moeten worden afgespeeld naar de Azure-bestands share **van oudste naar nieuwste**. Een oudere back-up kan niet worden 'gesorteerd' in de lijst met back-ups op de Azure-bestands share nadat een migratie job is uitgevoerd. Daarom moet u ervoor zorgen dat uw lijst met back-ups is voltooid *voordat* u een taak maakt. 
- Deze lijst met back-ups in een taak kan niet worden gewijzigd zodra de taak is gemaakt, zelfs niet als de taak nooit is gemaakt. 

:::row:::
    :::column:::        
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups.png" alt-text="Een schermopname van het formulier voor het maken van een nieuwe taak met gedetailleerde informatie over het gedeelte waarin StorSimple-back-ups zijn geselecteerd voor migratie." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-expanded.png":::
    :::column-end:::
    :::column:::
        Als u back-ups van uw StorSimple-volume wilt selecteren voor uw migratie job, selecteert u volumeback-ups *selecteren* op het formulier voor het maken van de taak.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png" alt-text="Een afbeelding waarin wordt weergegeven dat in de bovenste helft van de blade voor het selecteren van back-ups alle beschikbare back-ups worden weergegeven. Een geselecteerde back-up wordt grijs weergegeven in deze lijst en toegevoegd aan een tweede lijst op de onderste helft van de blade. Daar kan het ook opnieuw worden verwijderd." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png":::
    :::column-end:::
    :::column:::
        Wanneer de blade voor back-upselectie wordt geopend, wordt deze in twee lijsten gescheiden. In de eerste lijst worden alle beschikbare back-ups weergegeven. U kunt de resultatenset uitbreiden en beperken door te filteren op een specifiek tijdsbereik. (zie de volgende sectie) </br></br>Een geselecteerde back-up wordt grijs weergegeven en wordt toegevoegd aan een tweede lijst op de onderste helft van de blade. In de tweede lijst worden alle back-ups weergegeven die zijn geselecteerd voor migratie. Een back-up die als fout is geselecteerd, kan ook opnieuw worden verwijderd.
        > [!CAUTION]
        > U moet alle **back-ups** selecteren die u wilt migreren. U kunt later geen oudere back-ups toevoegen. U kunt de taak niet wijzigen om uw selectie te wijzigen zodra de taak is gemaakt.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time.png" alt-text="Een schermopname met de selectie van een tijdsbereik van de blade voor back-upselectie." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time-expanded.png":::
    :::column-end:::
    :::column:::
        De lijst wordt standaard gefilterd om de StorSimple-volumeback-ups in de afgelopen zeven dagen weer te geven, om het eenvoudig te maken om de meest recente back-up te selecteren. Gebruik voor back-ups in het verleden het tijdsbereikfilter boven aan de blade. U kunt kiezen uit een bestaand filter of een aangepast tijdsbereik instellen om alleen te filteren op de back-ups die tijdens deze periode worden gemaakt.
    :::column-end:::
:::row-end:::

> [!CAUTION]
> Het selecteren van meer dan 50 StorSimple-volumeback-ups wordt niet ondersteund. Taken met een groot aantal back-ups kunnen mislukken.

### <a name="directory-mapping"></a>Maptoewijzing

Maptoewijzing is optioneel voor uw migratie job. Als u de sectie leeg *laat,* worden alle bestanden en mappen in de hoofdmap van uw StorSimple-volume verplaatst naar de hoofdmap van uw Azure-doelbestands share. In de meeste gevallen is het opslaan van de inhoud van een volledig volume in een Azure-bestands share niet de beste aanpak. Het is vaak beter om de inhoud van een volume te splitsen over meerdere bestands shares in Azure. Zie Uw [StorSimple-volume](#map-your-existing-storsimple-volumes-to-azure-file-shares) eerst aan Azure-bestands shares toevoegen als u nog geen plan hebt gemaakt.

Als onderdeel van uw migratieplan hebt u mogelijk besloten dat de mappen op een StorSimple-volume moeten worden verdeeld over meerdere Azure-bestands shares. Als dat het geval is, kunt u dit opsplitsen door:

1. Meerdere taken definiëren om de mappen op één volume te migreren. Elke heeft dezelfde StorSimple-volumebron, maar een andere Azure-bestands share als het doel.
1. Geef nauwkeurig op welke mappen van het StorSimple-volume moeten worden gemigreerd naar de opgegeven bestands share met behulp van de sectie Maptoewijzing van het formulier voor het maken van de taak en volg de specifieke [toewijzingssemantiek](#semantic-elements). 

> [!IMPORTANT]
> De paden en toewijzingsexpressie in dit formulier kunnen niet worden gevalideerd wanneer het formulier wordt verzonden. Als toewijzingen onjuist zijn opgegeven, kan een taak volledig mislukken of een ongewenst resultaat opleveren. In dat geval is het meestal het beste om de Azure-bestands share te verwijderen, opnieuw te maken en vervolgens de toewijzingsverklaringen in een nieuwe migratie job voor de share te herstellen. Als u een nieuwe taak met vaste toewijzingstoewijzingsverklaringen wilt uitvoeren, kunnen weggelaten mappen worden hersteld en naar de bestaande share worden gebracht. Alleen mappen die zijn weggelaten vanwege spelfouten in paden, kunnen echter op deze manier worden verholpen.

#### <a name="semantic-elements"></a>Semantische elementen

Een toewijzing wordt uitgedrukt van links naar rechts: [\bronpad] \> [\doelpad].

|Semantisch teken          | Betekenis  |
|:---------------------------|:---------|
| **\\**                     | Indicator op hoofdniveau.       |
| **\>**                     | [Bron] en [doeltoewijzing] operator.     |
|**\|** of RETURN (nieuwe regel) | Scheidingsteken van twee instructies voor maptoewijzing. </br>U kunt dit teken ook weglaten en Enter selecteren **om** de volgende toewijzingsexpressie op een eigen regel op te halen.        |

### <a name="examples"></a>Voorbeelden
Verplaatst de inhoud van de map *Gebruikersgegevens naar* de hoofdmap van de doelbestands share:
``` console
\User data > \
```
Verplaatst de volledige volume-inhoud naar een nieuw pad op de doelbestands share:
``` console
\ > \Apps\HR tracker
```
Verplaatst de inhoud van de bronmap naar een nieuw pad op de doelbestands share:
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
Sorteert meerdere bronlocaties in een nieuwe mapstructuur:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Semantische regels

* Geef altijd mappaden op ten opzichte van het hoofdniveau.
* Begin elk mappad met een indicator op hoofdniveau " \\ " .
* Neem geen stationletters op.
* Wanneer u meerdere paden opgeeft, mogen bron- of doelpaden elkaar niet overlappen:</br>
   Voorbeeld van overlapping van ongeldig bronpad:</br>
    *\\map\1 > \\ map*</br>
    *\\map \\ 1 \\ 2 > \\ map2*</br>
   Voorbeeld van overlapping van ongeldig doelpad:</br>
   *\\map > \\*</br>
   *\\map2 > \\*</br>
* Bronmappen die niet bestaan, worden genegeerd.
* Mapstructuren die niet op het doel bestaan, worden gemaakt.
* Net als Windows zijn mapnamen niet-bestandsgevoelig, maar behouden de case.

> [!NOTE]
> Inhoud van *de map \System Volume Information* en de *$Recycle.Bin* op uw StorSimple-volume worden niet gekopieerd door de migratie job.

### <a name="run-a-migration-job"></a>Een migratie job uitvoeren

Uw migratietaken worden vermeld onder *Taakdefinities* in de Data Manager resource die u hebt geïmplementeerd in een resourcegroep.
Selecteer in de lijst met taakdefinities de taak die u wilt uitvoeren.

Op de taakblade die wordt geopend, ziet u dat uw taak wordt uitgevoerd in de onderste lijst. In eerste instantie is deze lijst leeg. Boven aan de blade staat een opdracht met de naam *Taak uitvoeren.* Met deze opdracht wordt de taak niet onmiddellijk uitgevoerd, maar wordt de blade **Taak uitvoeren** geopend:

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job.png" alt-text="Een afbeelding van de blade taakuit voeren met een vervolgkeuze besturingselement geopend, met de geselecteerde back-ups die moeten worden gemigreerd. De oudste back-up is gemarkeerd. Deze moet eerst worden geselecteerd." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job-expanded.png":::
    :::column-end:::
    :::column:::
        In deze release moet elke taak meerdere keren worden uitgevoerd. </br></br>**U moet beginnen met de oudste back-up uit de lijst met back-ups die u wilt migreren.** (gemarkeerd in de afbeelding)</br></br>U kunt de taak elke keer opnieuw uitvoeren, net zo vaak als u back-ups hebt geselecteerd, op basis van een progressief nieuwere back-up.
        </br></br>
        > [!CAUTION]
        > Het is belangrijk dat u de migratie job uitvoeren met de oudste back-up geselecteerd eerst en vervolgens opnieuw, elke keer met een progressief nieuwere back-up. U moet de volgorde van uw back-ups altijd handmatig onderhouden, van oudste naar nieuwste.
    :::column-end:::
:::row-end:::

### <a name="phase-3-summary"></a>Samenvatting fase 3

Aan het einde van fase 3 hebt u ten minste één van uw migratietaken van StorSimple-volumes naar Azure-bestands share(s) uitgevoerd. U hebt dezelfde migratie job meerdere keren uitgevoerd, van oudste naar nieuwste back-ups die moeten worden gemigreerd. U kunt zich nu richten op het instellen van Azure File Sync voor de share (zodra de migratietaken voor een share zijn voltooid) of het doorverrichten van toegang tot de share voor uw informatiemedewerkers en apps naar de Azure-bestands share.

## <a name="phase-4-access-your-azure-file-shares"></a>Fase 4: Toegang tot uw Azure-bestands shares

Er zijn twee belangrijke strategieën voor toegang tot uw Azure-bestands shares:

* **Azure File Sync:** [implementeer Azure File Sync](#deploy-azure-file-sync) in een on-premises Windows Server-exemplaar. Azure File Sync heeft alle voordelen van een lokale cache, net als StorSimple.
* **Direct-share-access:** [implementeer direct-share-access.](#deploy-direct-share-access) Gebruik deze strategie als uw toegangsscenario voor een bepaalde Azure-bestands share geen voordeel heeft van lokale caching of als u niet langer de mogelijkheid hebt om een on-premises Windows Server-exemplaar te hosten. Hier blijven uw gebruikers en apps toegang houden tot SMB-shares via het SMB-protocol. Deze shares staan niet langer op een on-premises server, maar rechtstreeks in de cloud.

U moet al hebben besloten welke optie het beste bij u past in [fase 1](#phase-1-prepare-for-migration) van deze handleiding.

De rest van deze sectie is gericht op implementatie-instructies.

### <a name="deploy-azure-file-sync"></a>Azure Files SYNC implementeren

Het is tijd om een deel van de Azure File Sync.

1. Maak de Azure File Sync cloudresource.
1. Implementeer Azure File Sync agent op uw on-premises server.
1. Registreer de server bij de cloudresource.

Maak nog geen synchronisatiegroepen. Het instellen van synchronisatie met een Azure-bestands share mag alleen plaatsvinden nadat uw migratietaken naar een Azure-bestands share zijn voltooid. Als u begint met het Azure File Sync voordat de migratie is voltooid, zou dit uw migratie onnodig moeilijk maken, omdat u niet gemakkelijk kunt zien wanneer het tijd was om een cut-over te starten.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>De Azure File Sync cloudresource implementeren

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Als u de Azure-regio wilt wijzigen waarin uw gegevens zich bevinden nadat de migratie is voltooid, implementeert u de opslagsynchronisatieservice in dezelfde regio als de doelopslagaccounts voor deze migratie.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>Een on-premises Windows Server-exemplaar implementeren

* Maak Windows Server 2019 (minimaal 2012R2) als een virtuele machine of fysieke server. Een Windows Server-failovercluster wordt ook ondersteund. Gebruik de server voor de StorSimple 8100 of 8600 niet opnieuw.
* Direct gekoppelde opslag inrichten of toevoegen. Aan het netwerk gekoppelde opslag wordt niet ondersteund.

Het is best practice om uw nieuwe Windows Server-exemplaar een gelijke of grotere hoeveelheid opslagruimte te geven dan uw StorSimple 8100- of 8600-apparaat lokaal beschikbaar heeft voor caching. U gebruikt het Windows Server-exemplaar op dezelfde manier als het StorSimple-apparaat. Als het apparaat dezelfde hoeveelheid opslagruimte heeft als het apparaat, moet de caching-ervaring vergelijkbaar zijn, indien niet hetzelfde. U kunt opslag naar eigen goed wil toevoegen aan of verwijderen uit uw Windows Server-exemplaar. Met deze mogelijkheid kunt u de grootte van uw lokale volume en de hoeveelheid lokale opslag die beschikbaar is voor caching schalen.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>Het Windows Server-exemplaar voorbereiden voor bestandssynchronisatie

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>Een Azure File Sync configureren op het Windows Server-exemplaar

Uw geregistreerde on-premises Windows Server-exemplaar moet gereed zijn en verbonden zijn met internet voor dit proces.

> [!IMPORTANT]
> De StorSimple-migratie van bestanden en mappen naar de Azure-bestands share moet zijn voltooid voordat u verdergaat. Zorg ervoor dat er geen wijzigingen meer worden aangebracht in de bestands share.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Schakel opslag in cloudlagen in. Cloudopslaglagen is de Azure File Sync waarmee de lokale server minder opslagcapaciteit kan hebben dan in de cloud is opgeslagen, maar wel over de volledige naamruimte kan beschikken. Lokaal interessante gegevens worden ook lokaal in de cache opgeslagen voor snelle, lokale toegangsprestaties. Een andere reden om cloudopslaglagen in te zetten bij deze stap is dat we in deze fase geen bestandsinhoud willen synchroniseren. Op dit moment mag alleen de naamruimte worden verplaatst.

### <a name="deploy-direct-share-access"></a>Direct-share-toegang implementeren

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Deze video is een handleiding en demo voor het veilig beschikbaar maken van Azure-bestands shares rechtstreeks aan informatiemedewerkers en apps in vijf eenvoudige stappen.</br>
        De video verwijst naar toegewezen documentatie voor een aantal onderwerpen:

* [Overzicht van identiteit](storage-files-active-directory-overview.md)
* [Domein toevoegen aan een opslagaccount](storage-files-identity-auth-active-directory-enable.md)
* [Netwerkoverzicht voor Azure-bestands shares](storage-files-networking-overview.md)
* [Openbare en privé-eindpunten configureren](storage-files-networking-endpoints.md)
* [Een S2S VPN configureren](storage-files-configure-s2s-vpn.md)
* [Een Windows P2S VPN configureren](storage-files-configure-p2s-vpn-windows.md)
* [Een Linux P2S VPN configureren](storage-files-configure-p2s-vpn-linux.md)
* [DNS-doorsturen configureren](storage-files-networking-dns.md)
* [DFS-N configureren](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Samenvatting fase 4

In deze fase hebt u meerdere migratietaken gemaakt en uitgevoerd in uw StorSimple Data Manager. Deze taken hebben uw bestanden en mappen gemigreerd naar Azure-bestands shares. U hebt ook uw Azure File Sync netwerk- en opslagaccounts geïmplementeerd of voorbereid voor directe toegang tot delen.

## <a name="phase-5-user-cut-over"></a>Fase 5: cut-over van gebruiker

Deze fase gaat over het verpakken van uw migratie:

* Plan uw downtime.
* U kunt alle wijzigingen in uw gebruikers en apps die aan de StorSimple-zijde zijn geproduceerd, inhalen terwijl de migratietaken in fase 3 worden uitgevoerd.
* Fail-overs naar het nieuwe Windows Server-exemplaar met Azure File Sync of de Azure-bestands shares via direct-share-access.

### <a name="plan-your-downtime"></a>Uw downtime plannen

Deze migratiebenadering vereist enige downtime voor uw gebruikers en apps. Het doel is om downtime tot een minimum te beperken. De volgende overwegingen kunnen helpen:

* Houd uw StorSimple-volumes beschikbaar tijdens het uitvoeren van uw migratietaken.
* Wanneer u klaar bent met het uitvoeren van uw gegevensmigratietaken voor een share, is het tijd om gebruikerstoegang (ten minste schrijftoegang) te verwijderen uit de StorSimple-volumes of -shares. Een definitieve RoboCopy zal uw Azure-bestands share inhalen. Vervolgens kunt u uw gebruikers knippen. Waar u RoboCopy gaat uitvoeren, is afhankelijk van of u ervoor hebt gekozen om Azure File Sync of direct-share-access te gebruiken. In de volgende sectie over RoboCopy wordt dit onderwerp bes behandelt.
* Nadat u de RoboCopy-inhaalversie hebt voltooid, kunt u de nieuwe locatie beschikbaar maken voor uw gebruikers door de Azure-bestands share rechtstreeks of een SMB-share op een Windows Server-exemplaar met Azure File Sync. Vaak helpt een DFS-N-implementatie om snel en efficiënt een cut-over te realiseren. Het houdt uw bestaande shareadressen consistent en richt zich op een nieuwe locatie die uw gemigreerde bestanden en mappen bevat.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Bepalen wanneer uw naamruimte volledig is gesynchroniseerd met uw server

Wanneer u Azure File Sync voor een Azure-bestands share gebruikt, is het belangrijk dat u bepaalt dat uw hele naamruimte is gedownload naar de *server* voordat u een lokale RoboCopy start. De tijd die nodig is om uw naamruimte te downloaden, is afhankelijk van het aantal items in uw Azure-bestands share. Er zijn twee methoden om te bepalen of uw naamruimte volledig op de server is aangekomen.

#### <a name="azure-portal"></a>Azure Portal

U kunt de Azure Portal om te zien wanneer uw naamruimte volledig is aangekomen.

* Meld u aan bij Azure Portal en ga naar uw synchronisatiegroep. Controleer de synchronisatiestatus van uw synchronisatiegroep en server-eindpunt.
* De interessante richting is downloaden. Als het server-eindpunt nieuw is ingericht, wordt Initiële **synchronisatie** weer gegeven, wat aangeeft dat de naamruimte nog steeds uitkomt.
Nadat alles behalve Initiële synchronisatie is **gewijzigd,** wordt uw naamruimte volledig ingevuld op de server. U kunt nu doorgaan met een lokale RoboCopy.

#### <a name="windows-server-event-viewer"></a>Windows Server-Logboeken

U kunt ook de Logboeken uw Windows Server-exemplaar gebruiken om te zien wanneer de naamruimte volledig is aangekomen.

1. Open de **Logboeken** en ga naar **Toepassingen en services.**
1. Ga naar en open **Microsoft\FileSync\Agent\Telemetry.**
1. Zoek naar de meest recente **gebeurtenis 9102,** die overeenkomt met een voltooide synchronisatiesessie.
1. Selecteer **Details** en controleer of u een gebeurtenis ziet waarbij **de waarde voor SyncDirection** **Downloaden** is.
1. Voor de tijd waarin uw naamruimte is gedownload naar de server, is er één gebeurtenis met **Scenario,** de waarde **FullGhostedSync** en **HResult**  =  **0.**
1. Als u deze gebeurtenis mist, kunt u ook zoeken naar andere **9102-gebeurtenissen** met **SyncDirection**  =  **Download** en **Scenario**  =  **"RegularSync"**. Als u een van deze gebeurtenissen vindt, geeft dit ook aan dat het downloaden en synchroniseren van de naamruimte is voltooid naar reguliere synchronisatiesessies, ongeacht of er op dit moment iets moet worden gesynchroniseerd of niet.

### <a name="a-final-robocopy"></a>Een definitieve RoboCopy

Op dit moment zijn er verschillen tussen uw on-premises Windows Server-exemplaar en het StorSimple 8100- of 8600-apparaat.

1. U moet de wijzigingen die gebruikers of apps aan de StorSimple-zijde hebben geproduceerd tijdens de migratie, inhalen.
1. Voor gevallen waarin u Azure File Sync: het StorSimple-apparaat heeft een gevulde cache ten opzichte van het Windows Server-exemplaar met alleen een naamruimte zonder bestandsinhoud die op dit moment lokaal is opgeslagen. Met de uiteindelijke RoboCopy kunt u snel aan de slag met uw lokale Azure File Sync-cache door lokaal in de cache opgeslagen bestandsinhoud zoveel mogelijk op te halen en op de Azure File Sync passen.
1. Sommige bestanden zijn mogelijk achter gelaten door de migratie-taak vanwege ongeldige tekens. Als dit het geval is, kopieert u Azure File Sync windows Server-exemplaar. Later kunt u deze aanpassen zodat ze worden gesynchroniseerd. Als u geen Azure File Sync voor een bepaalde share, kunt u de naam van de bestanden beter wijzigen met ongeldige tekens op het StorSimple-volume. Voer de RoboCopy vervolgens rechtstreeks uit op de Azure-bestands share.

> [!WARNING]
> Robocopy in Windows Server 2019 heeft momenteel een probleem dat ertoe zal leiden dat bestanden die zijn gelaagd door Azure File Sync op de doelserver opnieuw worden gecopied van de bron en opnieuw worden geüpload naar Azure wanneer u de functie /PIP van Robocopy gebruikt. Het is belangrijk dat u Robocopy gebruikt op een andere Windows-server dan 2019. Een voorkeurskeuze is Windows Server 2016. Deze opmerking wordt bijgewerkt als het probleem wordt opgelost via Windows Update.

> [!WARNING]
> U *moet RoboCopy* niet starten voordat de naamruimte voor een Azure-bestands share volledig is gedownload op de server. Zie Bepalen wanneer uw naamruimte volledig is gedownload [naar uw server voor meer informatie.](#determine-when-your-namespace-has-fully-synced-to-your-server)

 U wilt alleen bestanden kopiëren die zijn gewijzigd nadat de migratietaken voor het laatst zijn gemaakt en bestanden die niet eerder door deze taken zijn verplaatst. U kunt het probleem oplossen met de reden waarom ze later niet op de server zijn verplaatst, nadat de migratie is voltooid. Zie problemen oplossen [Azure File Sync meer informatie.](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

RoboCopy heeft verschillende parameters. In het volgende voorbeeld wordt een voltooide opdracht en een lijst met redenen voor het kiezen van deze parameters weergegeven.

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /NP /B /MIR /IT /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Achtergrond:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Hiermee kan RoboCopy meerdere threads uitvoeren. De standaardwaarde is 8 en het maximum is 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Uitvoerstatus naar LOG-bestand als UNICODE (overschrijft bestaand logboek).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Uitvoer naar consolevenster. Wordt gebruikt in combinatie met uitvoer naar een logboekbestand.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /NP
   :::column-end:::
   :::column span="1":::
      De logboekregistratie van de voortgang wordt weglaten om het logboek leesbaar te houden.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      RoboCopy wordt uitgevoerd in dezelfde modus die een back-uptoepassing zou gebruiken. Hiermee kan RoboCopy bestanden verplaatsen waar de huidige gebruiker geen machtigingen voor heeft.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /WIJS
   :::column-end:::
   :::column span="1":::
      Hiermee kan RoboCopy alleen verschillen tussen de bron (StorSimple-apparaat) en het doel (Windows Server-map) overwegen.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /IT
   :::column-end:::
   :::column span="1":::
      Zorgt ervoor dat betrouwbaarheid behouden blijft in bepaalde mirror-scenario's.</br>Voorbeeld: tussen twee Robocopy-runs wordt een bestand uitgevoerd met een ACL-wijziging en een kenmerkupdate. Het wordt bijvoorbeeld ook als verborgen *gemarkeerd.* Zonder /IT kan de wijziging van de ACL worden gemist door Robocopy en dus niet worden overgedragen naar de doellocatie.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      Fidelity of the file copy (standaard is /COPY:DAT), copy flags: D=Data, A=Attributes, T=Timestamps, S=Security=NTFS ACL's, O=Owner information, U=aUditing information.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      COPY ALL-bestandsgegevens (gelijk aan /COPY:DATSOU).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      Fidelity for the copy of directories (standaard is /DCOPY:DA), copy flags: D=Data, A=Attributes, T=Timestamps.
   :::column-end:::
:::row-end:::

Wanneer u de bron- en doellocaties van de RoboCopy-opdracht configureert, controleert u de structuur van de bron en het doel om ervoor te zorgen dat ze overeenkomen. Als u de maptoewijzingsfunctie van de migratiefunctie hebt gebruikt, is uw hoofdmapstructuur mogelijk anders dan de structuur van uw StorSimple-volume. Als dat het geval is, hebt u mogelijk meerdere RoboCopy-taken nodig, één voor elke subdirectory. Als u niet zeker weet of de opdracht zal uitvoeren zoals verwacht, kunt u de */L* parameter, waarmee de opdracht wordt gesimuleerd zonder daadwerkelijk wijzigingen aan te brengen.

Deze RoboCopy-opdracht maakt gebruik van /WAS, zodat bestanden die hetzelfde zijn (bijvoorbeeld gelaagde bestanden) niet worden verplaatst. Maar als het bron- en doelpad onjuist zijn, worden met /GAAT ook mapstructuren op uw Windows Server-exemplaar of Azure-bestands share verwijderd die niet aanwezig zijn op het StorSimple-bronpad. Ze moeten exact overeenkomen voor de RoboCopy-taak om het beoogde doel te bereiken van het bijwerken van uw gemigreerde inhoud met de meest recente wijzigingen die zijn aangebracht terwijl de migratie wordt doorgevoerd.

Raadpleeg het RoboCopy-logboekbestand om te zien of er bestanden achterblijven. Als er problemen zijn, lost u deze op en voer u de RoboCopy-opdracht opnieuw uit. Maak deprovisioning van StorSimple-resources niet op voordat u openstaande problemen voor bestanden of mappen die u belangrijk vindt, hebt opgelost.

Als u geen gebruik maakt van Azure File Sync om de betreffende Azure-bestands share in de cache op te nemen, maar in plaats daarvan te kiezen voor direct-share-toegang:

1. [Uw Azure-bestands share](storage-how-to-use-files-windows.md#mount-the-azure-file-share) als een netwerkstation aan een lokale Windows-computer toevoegen.
1. Voer de RoboCopy uit tussen uw StorSimple en de aan elkaar geplaatste Azure-bestands share. Als bestanden niet worden gekopieerd, herstelt u de namen aan de StorSimple-zijde om ongeldige tekens te verwijderen. Ga vervolgens opnieuw naar RoboCopy. De eerder vermelde RoboCopy-opdracht kan meerdere keren worden uitgevoerd zonder onnodig terughalen van StorSimple te veroorzaken.

### <a name="user-cut-over"></a>Cut-over van gebruiker

Als u Azure File Sync gebruikt, moet u waarschijnlijk de SMB-shares op dat Azure File Sync Windows Server-exemplaar maken die overeenkomen met de shares die u op de StorSimple-volumes had. U kunt deze stap front-loaden en dit eerder doen om hier geen tijd te verliezen. Maar u moet ervoor zorgen dat vóór dit punt niemand toegang heeft om wijzigingen aan te brengen in het Windows Server-exemplaar.

Als u een DFS-N-implementatie hebt, kunt u de DFN-Namespaces naar de nieuwe maplocaties van de server. Als u geen DFS-N-implementatie hebt en uw 8100- of 8600-apparaat lokaal hebt gefronted met een Windows Server-exemplaar, kunt u die server uit het domein zetten. Voeg vervolgens uw nieuwe Windows Server-Azure File Sync toe aan het domein. Tijdens dit proces geeft u de server dezelfde servernaam en sharenamen als de oude server, zodat cut-over transparant blijft voor uw gebruikers, groepsbeleid en scripts.

Meer informatie over [DFS-N.](/windows-server/storage/dfs-namespaces/dfs-overview)

## <a name="deprovision"></a>Deprovision

Wanneer u de inrichting van een resource opschorten, hebt u geen toegang meer tot de configuratie van die resource en de gegevens ervan. Het ongedaan maken van deprovisioning kan niet ongedaan worden gemaakt. Ga pas verder als u dat hebt bevestigd:

* Uw migratie is voltooid.
* Er zijn geen afhankelijkheden van de StorSimple-bestanden, -mappen of -volumeback-ups die u op het punt staat deprovision te verwijderen.

Voordat u begint, is het een goed best practice nieuwe implementatie Azure File Sync in productie te nemen. Deze tijd biedt u de mogelijkheid om eventuele problemen op te lossen. Nadat u hebt gezien dat uw Azure File Sync een paar dagen hebt geïmplementeerd, kunt u in deze volgorde beginnen met het deprovision van resources:

1. Deprovision your StorSimple Data Manager resource via the Azure Portal. Al uw DTS-taken worden daarmee verwijderd. U kunt de kopieerlogboeken niet eenvoudig ophalen. Als ze belangrijk zijn voor uw records, haalt u ze op voordat u deprovision opslokt.
1. Zorg ervoor dat uw fysieke StorSimple-apparaten zijn gemigreerd en maak de registratie ervan ongedaan. Als u niet volledig zeker weet dat ze zijn gemigreerd, gaat u niet verder. Als u de inrichting van deze resources op de 10000 dagen hebt verwijderd, kunt u de gegevens of hun configuratie niet herstellen.<br>U kunt de StorSimple-volumeresource eventueel eerst deprovisioneren, waarmee de gegevens op het apparaat worden opsschoond. Dit kan enkele dagen duren en **de** gegevens op het apparaat worden niet forensisch buiten gebruik gehouden. Als dit belangrijk voor u is, moet u schijf zeroing afzonderlijk verwerken van de resource-deprovisioning en volgens uw beleid.
1. Als er geen geregistreerde apparaten meer in een StorSimple-Apparaatbeheer staan, kunt u doorgaan met het verwijderen van Apparaatbeheer resource zelf.
1. Het is nu tijd om het StorSimple-opslagaccount in Azure te verwijderen. Stop nogmaals en bevestig dat de migratie is voltooid en dat niets en niemand afhankelijk is van deze gegevens voordat u verdergaat.
1. Ontkoppel het fysieke StorSimple-apparaat van uw datacenter.
1. Als u eigenaar bent van het StorSimple-apparaat, kunt u het opnieuw gebruiken voor pc's. Als uw apparaat is geleased, informeert u de lessor en retourneerd u het apparaat naar eigen goedheid.

Uw migratie is voltooid.

> [!NOTE]
> Hebt u nog vragen of hebt u problemen ondervonden?</br>
> We zijn hier om u te helpen bij AzureFilesMigration@microsoft.com .

## <a name="next-steps"></a>Volgende stappen

* Meer vertrouwd raken met [Azure File Sync: aka.ms/AFS](./storage-sync-files-planning.md).
* Inzicht in de flexibiliteit van [beleidsregels voor cloudopslaglagen.](storage-sync-cloud-tiering-overview.md)
* [Schakel Azure Backup](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) azure-bestands shares in om momentopnamen te plannen en back-upretentieschema's te definiëren.
* Als u in de Azure Portal ziet dat sommige bestanden permanent niet [](storage-sync-files-troubleshoot.md) worden gesynchroniseerd, bekijkt u de gids voor probleemoplossing voor de stappen om deze problemen op te lossen.
