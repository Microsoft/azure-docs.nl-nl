---
title: StorSimple 8000-serie migreren naar Azure File Sync
description: Meer informatie over het migreren van een StorSimple 8100-of 8600-apparaat naar Azure File Sync.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: a512151dce336de42da78232ca30845dae754db3
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202719"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 en 8600 migratie naar Azure File Sync

De StorSimple 8000-serie wordt vertegenwoordigd door de 8100-of de 8600 fysieke, on-premises apparaten en hun Cloud service onderdelen. Het is mogelijk om de gegevens van een van deze apparaten te migreren naar een Azure File Sync omgeving. Azure File Sync is de standaard-en strategische Azure-service voor de lange termijn waarmee StorSimple-apparaten kunnen worden gemigreerd.

De StorSimple 8000-serie bereikt het [einde van de levens duur](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) in december 2022. Het is belang rijk dat u zo snel mogelijk begint met het plannen van de migratie. Dit artikel bevat de benodigde achtergrond kennis en migratie stappen voor een geslaagde migratie naar Azure File Sync.

## <a name="phase-1-prepare-for-migration"></a>Fase 1: voorbereiden op migratie

Deze sectie bevat de stappen die u moet nemen aan het begin van de migratie van StorSimple-volumes naar Azure-bestands shares.

### <a name="inventory"></a>Inventaris

Wanneer u begint met het plannen van de migratie, moet u eerst alle StorSimple-apparaten en-volumes identificeren die u nodig hebt om te migreren. Nadat u dit hebt gedaan, kunt u besluiten op het beste migratie traject voor u.

* StorSimple fysieke apparaten (8000 Series) deze migratie handleiding gebruiken.
* Virtuele apparaten, [StorSimple 1200-serie, gebruiken een andere migratie handleiding](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Samen vatting migratie kosten

Migraties naar Azure-bestands shares van StorSimple-volumes via migratie taken in een StorSimple Data Manager resource zijn gratis. Er kunnen andere kosten in rekening worden gebracht tijdens en na een migratie:

* **Netwerk** uitgaand: Uw StorSimple-bestanden bevinden zich in een opslag account binnen een specifieke Azure-regio. Als u de Azure-bestands shares die u hebt gemigreerd naar een opslag account dat zich in dezelfde Azure-regio bevindt, worden er geen kosten voor uitgaand verkeer gegenereerd. U kunt uw bestanden verplaatsen naar een opslag account in een andere regio als onderdeel van deze migratie. In dat geval zijn de kosten voor uitgaand verkeer van toepassing op u.
* **Azure-bestands share transacties:** Wanneer bestanden worden gekopieerd naar een Azure-bestands share (als onderdeel van een migratie of buiten één), worden de transactie kosten toegepast als bestanden en meta gegevens worden geschreven. Start als best practice uw Azure-bestands share tijdens de migratie op de laag geoptimaliseerd voor trans acties. Schakel over naar de gewenste laag nadat de migratie is voltooid. De volgende fasen zullen dit op het juiste punt aanroepen.
* **Een Azure-bestands share-laag wijzigen:** De laag van de kosten transacties van een Azure-bestands share wijzigen. In de meeste gevallen is het rendabeler om het advies van het vorige punt te volgen.
* **Opslag kosten:** Wanneer deze migratie begint met het kopiëren van bestanden naar een Azure-bestands share, wordt Azure Files opslag verbruikt en gefactureerd. Gemigreerde back-ups worden [moment opnamen van Azure-bestands shares](storage-snapshots-files.md). Moment opnamen van bestands shares gebruiken alleen opslag capaciteit voor de verschillen die ze bevatten.
* **StorSimple:** Totdat u de mogelijkheid hebt om de inrichting van de StorSimple-apparaten en-opslag accounts ongedaan te maken, blijven de StorSimple kosten voor opslag, back-ups en toestellen gewoon optreden.

### <a name="direct-share-access-vs-azure-file-sync"></a>Direct delen-toegang versus Azure File Sync

Azure-bestands shares openen een volledige nieuwe wereld van kansen voor het structureren van uw implementatie van bestands Services. Een Azure-bestands share is slechts een SMB-share in de cloud die u kunt instellen om gebruikers rechtstreeks toegang te geven tot het SMB-protocol met de vertrouwde Kerberos-verificatie en bestaande NTFS-machtigingen (bestanden en mappen Acl's) die systeem eigen werken. Meer informatie over [toegang tot Azure-bestands shares op basis van een identiteit](storage-files-active-directory-overview.md).

Een alternatief voor directe toegang is [Azure file sync](./storage-sync-files-planning.md). Azure File Sync is een direct analoog voor de mogelijkheid van StorSimple om veelgebruikte bestanden on-premises op te slaan in de cache.

Azure File Sync is een micro soft-Cloud service, gebaseerd op twee hoofd onderdelen:

* Bestands synchronisatie en Cloud lagen voor het maken van een prestatie toegangs cache op een Windows-Server.
* Bestands shares als systeem eigen opslag in azure waartoe toegang kan worden verkregen via meerdere protocollen zoals SMB en de REST van het bestand.

Azure-bestands shares behouden belang rijke aspecten van de bestands kwaliteit van opgeslagen bestanden als kenmerken, machtigingen en tijds tempels. Met Azure-bestands shares is het niet meer nodig om een toepassing of service te interpreteren van de bestanden en mappen die zijn opgeslagen in de Cloud. U kunt deze systeem eigen toegang tot de vertrouwde protocollen en clients, zoals Windows Verkenner. Met Azure-bestands shares kunt u algemene bestands Server gegevens en toepassings gegevens opslaan in de Cloud. Het maken van een back-up van een Azure-bestands share is een ingebouwde functionaliteit die verder kan worden verbeterd door Azure Backup.

Dit artikel is gericht op de migratie stappen. Raadpleeg de volgende artikelen als u meer wilt weten over Azure File Sync voordat u de migratie uitvoert:

* [Overzicht van Azure File Sync](./storage-sync-files-planning.md "Overzicht")
* [Implementatie handleiding Azure File Sync](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>Versleutelings sleutel voor StorSimple-service gegevens

Wanneer u uw StorSimple-apparaat voor het eerst instelt, is er een ' service gegevens versleutelings sleutel ' gegenereerd en wordt u geadviseerd om de sleutel veilig op te slaan. Deze sleutel wordt gebruikt voor het versleutelen van alle gegevens in het gekoppelde Azure Storage-account waar uw bestanden worden opgeslagen met het StorSimple-apparaat.

De ' service gegevens versleutelings sleutel ' is nodig voor een geslaagde migratie. Het is nu een goed moment om deze sleutel op te halen uit uw records, één voor elk van de apparaten in uw inventaris.

Als u de sleutels niet in uw records vindt, kunt u de sleutel van het apparaat ophalen. Elk apparaat heeft een unieke versleutelings sleutel. De sleutel ophalen:

* Een ondersteunings aanvraag met Microsoft Azure indienen via de Azure Portal. De aanvraag moet de serie nummers van uw StorSimple-apparaat bevatten en een aanvraag voor het ophalen van de versleutelings sleutel voor service gegevens.
* Een StorSimple-ondersteunings technicus neemt contact met u op voor een aanvraag voor een virtuele vergadering.
* Zorg ervoor dat voordat de vergadering begint, u verbinding maakt met uw StorSimple-apparaat [via een seriële console](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) of via een [externe Power shell-sessie](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple).

> [!CAUTION]
> Houd rekening met het volgende als u wilt bepalen hoe u verbinding maakt met uw StorSimple-apparaat:
>
> * Het maken van een verbinding via een HTTPS-sessie is de veiligste en aanbevolen optie.
> * Rechtstreeks verbinding maken met de seriële console van het apparaat is beveiligd, maar het maken van verbinding met de seriële console via netwerk switches is niet.
> * HTTP-sessie verbindingen zijn een optie, maar zijn *niet versleuteld*. Ze worden niet aanbevolen, tenzij ze worden gebruikt in een gesloten, vertrouwd netwerk.

### <a name="storsimple-volume-backups"></a>StorSimple volume back-ups

StorSimple biedt differentiële back-ups op volume niveau. Azure-bestands shares hebben ook de mogelijkheid om share-moment opnamen te noemen.
Uw migratie taken kunnen alleen back-ups verplaatsen, niet van gegevens van het Live volume. De meest recente back-up moet dus altijd op de lijst met back-ups staan die in een migratie zijn verplaatst.

Bepaal of u oudere back-ups tijdens de migratie moet verplaatsen.
De aanbevolen procedure is om deze lijst zo klein mogelijk te laten, zodat uw migratie taken sneller worden uitgevoerd.

Als u essentiële back-ups wilt identificeren die moeten worden gemigreerd, moet u een controle lijst van uw back-upbeleid maken. Bijvoorbeeld:
* De meest recente back-up. (Opmerking: de meest recente back-up moet altijd deel uitmaken van deze lijst.)
* Eén back-up per maand gedurende 12 maanden.
* Eén back-up per jaar gedurende drie jaar. 

Later, wanneer u uw migratie taken maakt, kunt u deze lijst gebruiken om de exacte StorSimple volume back-ups te identificeren die moeten worden gemigreerd om te voldoen aan de vereisten in uw lijst.

> [!CAUTION]
> Het selecteren van meer dan **50** StorSimple volume back-ups wordt niet ondersteund.
> Uw migratie taken kunnen alleen back-ups verplaatsen, nooit gegevens van het Live volume. Daarom is de meest recente back-up het dichtst bij de actuele gegevens en moet deze altijd deel uitmaken van de lijst met back-ups die moeten worden verplaatst in een migratie.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Uw bestaande StorSimple-volumes toewijzen aan Azure-bestands shares

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Aantal opslag accounts

Uw migratie zal waarschijnlijk profiteren van een implementatie van meerdere opslag accounts die elk een kleiner aantal Azure-bestands shares bevatten.

Als uw bestands shares zeer actief zijn (gebruikt door veel gebruikers of toepassingen), kunnen twee Azure-bestands shares de prestatie limiet van uw opslag account bereiken. Daarom is het best practice om te migreren naar meerdere opslag accounts, elk met hun eigen afzonderlijke bestands shares en meestal niet meer dan twee of drie shares per opslag account.

Een best practice is het implementeren van opslag accounts met één bestands share. U kunt meerdere Azure-bestands shares in hetzelfde opslag account groepen als u archiverings shares hebt.

Deze overwegingen zijn van toepassing op [directe Cloud toegang](#direct-share-access-vs-azure-file-sync) (via een Azure VM of service) dan Azure file sync. Als u van plan bent om alleen Azure File Sync te gebruiken voor deze shares, kunt u het groeperen van meerdere accounts in één Azure-opslag account prima. In de toekomst wilt u mogelijk een app omhoog en omlaag verplaatsen in de cloud die vervolgens rechtstreeks toegang zou hebben tot een bestands share. dit scenario zou kunnen profiteren van hogere IOPS en door voer. U kunt ook beginnen met het gebruik van een service in azure, wat zou kunnen profiteren van hogere IOPS en door voer.

Als u een lijst met shares hebt gemaakt, wijst u elke share toe aan het opslag account waar deze zich bevindt.

> [!IMPORTANT]
> Beslis op een Azure-regio en zorg ervoor dat elk opslag account en Azure File Sync bron overeenkomen met de regio die u hebt geselecteerd.
> Configureer de netwerk-en Firewall instellingen voor de opslag accounts nu niet. Als u deze configuraties op dit moment maakt, zou een migratie onmogelijk zijn. Deze instellingen voor Azure Storage configureren nadat de migratie is voltooid.

### <a name="phase-1-summary"></a>Samen vatting fase 1

Aan het einde van fase 1:

* U hebt een goed overzicht van uw StorSimple-apparaten en-volumes.
* De Data Manager-service is klaar om toegang te krijgen tot uw StorSimple-volumes in de Cloud omdat u uw ' service gegevens versleutelings sleutel ' hebt opgehaald voor elk StorSimple-apparaat.
* U hebt een plan voor de migratie van volumes en back-ups (indien aanwezig na de meest recente).
* U weet hoe u uw volumes kunt toewijzen aan het juiste aantal Azure-bestands shares en-opslag accounts.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>Fase 2: Azure Storage-en migratie bronnen implementeren

In deze sectie worden de overwegingen beschreven voor het implementeren van de verschillende resource typen die nodig zijn in Azure. Sommige worden uw gegevens na de migratie bewaard en sommige zijn alleen nodig voor de migratie. Begin niet te beginnen met het implementeren van resources totdat u uw implementatie plan hebt voltooid. Het is moeilijk, soms onmogelijk om bepaalde aspecten van uw Azure-resources te wijzigen nadat ze zijn geïmplementeerd.

### <a name="deploy-storage-accounts"></a>Opslag accounts implementeren

Waarschijnlijk moet u verschillende Azure-opslag accounts implementeren. Elk abonnement bevat een kleiner aantal Azure-bestands shares, conform uw implementatie plan, dat u in de vorige sectie van dit artikel hebt voltooid. Ga naar de Azure Portal voor het [implementeren van uw geplande opslag accounts](../common/storage-account-create.md#create-a-storage-account). Houd rekening met de volgende basis instellingen voor een nieuw opslag account.

> [!IMPORTANT]
> Configureer nu geen netwerk-en Firewall instellingen voor uw opslag accounts. Door deze configuraties op dit moment te maken, zou een migratie onmogelijk zijn. Deze instellingen voor Azure Storage configureren nadat de migratie is voltooid.

#### <a name="subscription"></a>Abonnement

U kunt hetzelfde abonnement gebruiken dat u hebt gebruikt voor uw StorSimple-implementatie of een andere. De enige beperking is dat uw abonnement zich in dezelfde Azure Active Directory Tenant moet bevindt als het StorSimple-abonnement. Overweeg het StorSimple-abonnement te verplaatsen naar de juiste Tenant voordat u een migratie start. U kunt alleen het hele abonnement verplaatsen; afzonderlijke StorSimple-resources kunnen niet worden verplaatst naar een andere Tenant of een ander abonnement.

#### <a name="resource-group"></a>Resourcegroep

Resource groepen helpen bij het beheren van de organisatie van resources en beheerders machtigingen. Meer informatie over [resource groepen vindt u in azure](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

#### <a name="storage-account-name"></a>Naam van het opslagaccount

De naam van uw opslag account wordt onderdeel van een URL en heeft bepaalde teken beperkingen. In uw naam Conventie kunt u overwegen dat opslag accountnamen uniek moeten zijn voor de hele wereld, alleen kleine letters en cijfers toestaan, tussen 3 en 24 tekens zijn vereist en geen speciale tekens zoals afbreek streepjes of onderstrepingen toestaan. Zie regels voor naamgeving van [Azure storage-resources](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage)voor meer informatie.

#### <a name="location"></a>Locatie

De locatie of de Azure-regio van een opslag account is zeer belang rijk. Als u Azure File Sync gebruikt, moeten al uw opslag accounts zich in dezelfde regio bevinden als de bron voor de opslag synchronisatie service. De Azure-regio die u kiest, moet sluiten of centraal zijn voor uw lokale servers en gebruikers. Nadat uw resource is geïmplementeerd, kunt u de regio niet meer wijzigen.

U kunt een andere regio kiezen waar uw StorSimple-gegevens (opslag account) momenteel zich bevinden.

> [!IMPORTANT]
> Als u een andere regio kiest uit de huidige locatie van de StorSimple-opslag account, [worden de kosten voor uitgaand verkeer toegepast](https://azure.microsoft.com/pricing/details/bandwidth) tijdens de migratie. Gegevens gaan in de regio StorSimple en voeren de nieuwe regio voor het opslag account in. Er worden geen bandbreedte kosten in rekening gebracht als u binnen dezelfde Azure-regio blijft.

#### <a name="performance"></a>Prestaties

U kunt kiezen voor Premium Storage (SSD) voor Azure-bestands shares of standaard opslag. Standaard opslag bevat [verschillende lagen voor een bestands share](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share). Standaard opslag is de juiste optie voor de meeste klanten die van StorSimple worden gemigreerd.

Nog steeds niet zeker?

* Kies Premium Storage als u de [prestaties van een Premium Azure-bestands share](understanding-billing.md#provisioned-model)nodig hebt.
* Kies standaard opslag voor werk belastingen voor bestands servers voor algemeen gebruik, waaronder gegevens over dynamische gegevens en archieven. Kies ook standaard opslag als de enige werk belasting op de share in de Cloud wordt Azure File Sync.

#### <a name="account-kind"></a>Soort account

* Kies voor standaard opslag *StorageV2 (algemeen gebruik v2)*.
* Kies voor Premium-bestands shares de optie *FileStorage*.

#### <a name="replication"></a>Replicatie

Er zijn verschillende replicatie-instellingen beschikbaar. Meer informatie over de verschillende replicatie typen.

Kies alleen een van de volgende twee opties:

* *Lokaal redundante opslag (LRS)*.
* *Zone redundant Storage (ZRS)*, die niet beschikbaar is in alle Azure-regio's.

> [!NOTE]
> Alleen LRS-en ZRS-redundantie typen zijn compatibel met de grote 100-TiB capaciteit Azure-bestands shares.

Geo redundant Storage (GRS) in alle variaties wordt momenteel niet ondersteund. U kunt later overschakelen op het redundantie type en overschakelen naar GRS wanneer ondersteuning voor deze arriveert in Azure.

#### <a name="enable-100-tib-capacity-file-shares"></a>100-TiB-capaciteits bestands shares inschakelen

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Een afbeelding met het tabblad Geavanceerd in de Azure Portal voor het maken van een opslag account.":::
    :::column-end:::
    :::column:::
        Onder de sectie **Geavanceerd** van de wizard Nieuw opslag account in de Azure Portal, kunt u ondersteuning voor **grote bestands shares** inschakelen in dit opslag account. Als deze optie niet beschikbaar is, hebt u waarschijnlijk het verkeerde type redundantie geselecteerd. Zorg ervoor dat u alleen LRS of ZRS selecteert om deze optie beschikbaar te stellen.
    :::column-end:::
:::row-end:::

Voor de grote, 100-TiB-capaciteits bestands shares geldt een aantal voor delen:

* Uw prestaties worden aanzienlijk verhoogd in vergelijking met de kleinere bestands shares met 5 TiB (bijvoorbeeld 10 keer de IOPS).
* Uw migratie wordt aanzienlijk sneller voltooid.
* U kunt ervoor zorgen dat een bestands share voldoende capaciteit heeft voor alle gegevens die u naar de server migreert, inclusief de differentiële back-ups voor opslag capaciteit.
* Toekomstige groei wordt gedekt.

### <a name="azure-file-shares"></a>Azure-bestands shares

Nadat uw opslag accounts zijn gemaakt, gaat u naar de sectie **Bestands share** van het opslag account en implementeert u het juiste aantal Azure-bestands shares volgens uw migratie plan vanuit fase 1. Denk aan de volgende basis instellingen voor uw nieuwe bestands shares in Azure.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Een Azure Portal scherm opname waarin de nieuwe gebruikers interface voor bestands shares wordt weer gegeven.":::
    :::column-end:::
    :::column:::
        </br>**Naam**</br>Kleine letters, cijfers en afbreek streepjes worden ondersteund.</br></br>**Quota**</br>Quotum hier is vergelijkbaar met een SMB-vaste quota op een Windows Server-exemplaar. Het best practice heeft geen quotum opgegeven, omdat uw migratie en andere services mislukken wanneer het quotum is bereikt.</br></br>**Lagen**</br>Selecteer **geoptimaliseerde trans actie** voor uw nieuwe bestands share. Tijdens de migratie worden er veel trans acties uitgevoerd. Meer kosten efficiënt om uw laag later te wijzigen in de laag die het meest geschikt is voor uw werk belasting.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple-gegevensbeheer

De Azure-resource die uw migratie taken zal bevatten, wordt een **StorSimple Data Manager** genoemd. Selecteer **nieuwe resource** en zoek ernaar. Selecteer vervolgens **Maken**.

Deze tijdelijke resource wordt gebruikt voor de indeling. U kunt de inrichting ongedaan maken nadat de migratie is voltooid. Het moet worden geïmplementeerd in hetzelfde abonnement, dezelfde resource groep en regio als uw StorSimple-opslag account.

### <a name="azure-file-sync"></a>Azure File Sync

Met Azure File Sync kunt u on-premises caching toevoegen van de meest gebruikte bestanden. Net als bij de cache mogelijkheden van StorSimple biedt de Azure File Sync-functie voor Cloud lagen een latentie van lokale toegang in combi natie met verbeterde controle over de beschik bare cache capaciteit op het Windows Server-exemplaar en multi-site-synchronisatie. Als u een on-premises cache hebt, moet u in uw lokale netwerk een Windows Server-VM voorbereiden (fysieke servers en failover-clusters worden ook ondersteund) met voldoende direct gekoppelde opslag capaciteit.

> [!IMPORTANT]
> Azure File Sync nog niet instellen. U kunt Azure File Sync het beste instellen nadat de migratie van de share is voltooid. Het implementeren van Azure File Sync mag niet beginnen vóór fase 4 van een migratie.

### <a name="phase-2-summary"></a>Samen vatting fase 2

Aan het einde van fase 2 hebt u uw opslag accounts en alle Azure-bestands shares in de toekomst geïmplementeerd. U hebt ook een StorSimple Data Manager resource. U gebruikt de laatste in fase 3 wanneer u uw migratie taken configureert.

## <a name="phase-3-create-and-run-a-migration-job"></a>Fase 3: een migratie taak maken en uitvoeren

In deze sectie wordt beschreven hoe u een migratie taak instelt en hoe u de mappen op een StorSimple-volume zorgvuldig toewijst die moeten worden gekopieerd naar de Azure-doel bestands share die u selecteert. Als u aan de slag wilt gaan, gaat u naar uw StorSimple Data Manager, zoekt u naar **taak definities** in het menu en selecteert u **+ taak definitie**. Het juiste type voor de doel opslag is de standaard instelling: **Azure-bestands share**.

![Typen migratie taken StorSimple 8000-serie.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Een scherm opname van de taak definities Azure Portal in het dialoog venster nieuwe taak definities waarin wordt gevraagd om het type taak: kopiëren naar een bestands share of een BLOB-container.")

:::row:::
    :::column:::
        ![Migratie taak voor de StorSimple 8000-serie.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Een scherm opname van het nieuwe formulier voor het maken van een taak voor een migratie taak.")
    :::column-end:::
    :::column:::
        **Naam van taakdefinitie**</br>Deze naam moet de set bestanden aangeven die u wilt verplaatsen. Het is een goede gewoonte om het een soort gelijke naam te geven als uw Azure-bestands share. </br></br>**Locatie waar de taak wordt uitgevoerd**</br>Wanneer u een regio selecteert, moet u dezelfde regio als uw StorSimple-opslag account selecteren. als dat niet het geval is, wordt er een regio gesloten. </br></br><h3>Bron</h3>**Bron abonnement**</br>Selecteer het abonnement waarin u de StorSimple-Apparaatbeheer resource opslaat. </br></br>**StorSimple-resource**</br>Selecteer uw StorSimple Apparaatbeheer uw apparaat is geregistreerd bij. </br></br>**Versleutelings sleutel voor service gegevens**</br>Raadpleeg deze [vorige sectie in dit artikel](#storsimple-service-data-encryption-key) voor het geval u de sleutel niet in uw records kunt vinden. </br></br>**Apparaat**</br>Selecteer uw StorSimple-apparaat dat het volume bevat waarnaar u wilt migreren. </br></br>**Volume**</br>Selecteer het bron volume. Later beslist u of u het hele volume of de submappen wilt migreren naar de Azure-doel bestands share.</br></br> **Volume back-ups**</br>U kunt Selecteer *volume back-ups selecteren* om specifieke back-ups te kiezen die u als onderdeel van deze taak wilt verplaatsen. In een aanstaande, [toegewezen sectie van dit artikel](#selecting-volume-backups-to-migrate) wordt het proces uitgebreid besproken.</br></br><h3>Doel</h3>Selecteer het abonnement, het opslag account en de Azure-bestands share als doel van deze migratie taak.</br></br><h3>Adreslijst toewijzing</h3>[Een toegewezen sectie in dit artikel](#directory-mapping)bespreekt alle relevante informatie.
    :::column-end:::
:::row-end:::

### <a name="selecting-volume-backups-to-migrate"></a>Te migreren volume back-ups

Er zijn belang rijke aspecten rond het kiezen van back-ups die moeten worden gemigreerd:

- Uw migratie taken kunnen alleen back-ups verplaatsen, geen gegevens van een actief volume. De meest recente back-up bevindt zich in de buurt van de actuele gegevens en moet altijd voor komt in de lijst met back-ups die zijn verplaatst tijdens een migratie.
- Zorg ervoor dat de meest recente back-up recent is om de Live-share zo klein mogelijk te laten verschillen. Het kan hand matig worden geactiveerd en een ander volume back-up te volt ooien voordat u een migratie taak maakt. Een kleine Delta met de Live-share zorgt voor een betere migratie. Als deze Delta gelijk kan zijn aan nul = er zijn geen wijzigingen meer aangebracht in het StorSimple-volume nadat de laatste back-up in de lijst is gemaakt. vervolgens fase 5: gebruiker knippen is drastisch vereenvoudigd en sped.
- Back-ups moeten worden afgespeeld in de Azure-bestands share **van oudste naar nieuwste**. Een oudere back-up kan niet worden gesorteerd in de lijst met back-ups op de Azure-bestands share nadat een migratie taak is uitgevoerd. Daarom moet u controleren of de lijst met back-ups is voltooid *voordat* u een taak maakt. 
- Deze lijst met back-ups in een taak kan niet worden gewijzigd nadat de taak is gemaakt, zelfs als de taak nooit is uitgevoerd. 

:::row:::
    :::column:::        
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups.png" alt-text="Een scherm opname van het nieuwe formulier voor het maken van een taak en het gedeelte waarin StorSimple-back-ups zijn geselecteerd voor migratie." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-expanded.png":::
    :::column-end:::
    :::column:::
        Als u back-ups van uw StorSimple-volume voor uw migratie taak wilt selecteren, selecteert u de *optie volume back-ups selecteren* op het formulier voor het maken van een taak.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png" alt-text="Een afbeelding die laat zien dat de bovenste helft van de Blade voor het selecteren van back-ups alle beschik bare back-ups bevat. Een geselecteerde back-up wordt grijs weer gegeven in deze lijst en toegevoegd aan een tweede lijst op de onderste helft van de Blade. Het kan ook opnieuw worden verwijderd." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png":::
    :::column-end:::
    :::column:::
        Wanneer de Blade back-upselectie wordt geopend, wordt deze onderverdeeld in twee lijsten. In de eerste lijst worden alle beschik bare back-ups weer gegeven. U kunt de resultatenset uitbreiden en beperken door te filteren op een specifiek tijds bereik. (zie volgende sectie) </br></br>Een geselecteerde back-up wordt als grijs weer gegeven en wordt toegevoegd aan een tweede lijst op de onderste helft van de Blade. In de tweede lijst worden alle back-ups weer gegeven die zijn geselecteerd voor migratie. Een back-up die is geselecteerd in fout kan ook opnieuw worden verwijderd.
        > [!CAUTION]
        > U moet **alle** back-ups selecteren die u wilt migreren. U kunt later geen oudere back-ups meer toevoegen. U kunt de taak niet wijzigen om uw selectie te wijzigen wanneer de taak is gemaakt.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time.png" alt-text="Een scherm opname van de selectie van een tijds bereik van de Blade back-upselectie." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time-expanded.png":::
    :::column-end:::
    :::column:::
        Standaard wordt de lijst gefilterd om de StorSimple-volume back-ups in de afgelopen zeven dagen weer te geven, zodat u gemakkelijk de meest recente back-up kunt selecteren. Voor back-ups in het verleden gebruikt u het filter tijd bereik boven aan de Blade. U kunt een bestaand filter selecteren of een aangepast tijds bereik instellen om alleen te filteren op back-ups die tijdens deze periode zijn gemaakt.
    :::column-end:::
:::row-end:::

> [!CAUTION]
> Het selecteren van meer dan 50 StorSimple volume back-ups wordt niet ondersteund. Taken met een groot aantal back-ups kunnen mislukken.

### <a name="directory-mapping"></a>Adreslijst toewijzing

Directory toewijzing is optioneel voor uw migratie taak. Als u de sectie leeg laat, worden *alle* bestanden en mappen op de hoofdmap van uw StorSimple-volume verplaatst naar de hoofdmap van de Azure-doel bestands share. In de meeste gevallen is het opslaan van de inhoud van een volledig volume in een Azure-bestands share niet de beste benadering. Het is vaak beter om de inhoud van een volume te splitsen over meerdere bestands shares in Azure. Als u nog geen abonnement hebt gemaakt, raadpleegt u eerst [uw StorSimple-volume toewijzen aan Azure-bestands shares](#map-your-existing-storsimple-volumes-to-azure-file-shares) .

Als onderdeel van uw migratie plan hebt u mogelijk besloten dat de mappen op een StorSimple-volume moeten worden opgesplitst op meerdere Azure-bestands shares. Als dat het geval is, kunt u die splitsing uitvoeren door:

1. Meerdere taken definiëren voor het migreren van de mappen op één volume. Elk heeft dezelfde StorSimple-volume bron, maar een andere Azure-bestands share als het doel.
1. Opgeven welke mappen van het StorSimple-volume moeten worden gemigreerd naar de opgegeven bestands share met behulp van de sectie **Directory toewijzing** van het formulier voor het maken van een taak en het volgen van de specifieke [toewijzings semantiek](#semantic-elements).

> [!IMPORTANT]
> De paden en toewijzings expressies in dit formulier kunnen niet worden gevalideerd wanneer het formulier wordt verzonden. Als toewijzingen onjuist zijn opgegeven, kan een taak volledig mislukken of een ongewenste resultaat opleveren. In dat geval is het meestal het beste om de Azure-bestands share te verwijderen, opnieuw te maken en vervolgens de toewijzings instructies in een nieuwe migratie taak voor de share op te lossen. Als u een nieuwe taak met vaste toewijzings instructies uitvoert, kunt u wegge laten mappen herstellen en deze in de bestaande share plaatsen. Er zijn echter alleen mappen die zijn wegge laten vanwege het pad naar spel fouten, op deze manier kunnen worden verholpen.

#### <a name="semantic-elements"></a>Semantische elementen

Een toewijzing wordt uitgedrukt van links naar rechts: [\Source pad] \> [\target pad].

|Semantisch teken          | Betekenis  |
|:---------------------------|:---------|
| **\\**                     | Hoofd niveau-indicator.       |
| **\>**                     | [Bron] en [doel-toewijzing] operator.     |
|**\|** of RETURN (nieuwe regel) | Schei ding van twee instructies voor toewijzing van mappen. </br>U kunt dit teken ook weglaten en **Enter** selecteren als u de volgende toewijzings expressie op een eigen regel wilt ophalen.        |

### <a name="examples"></a>Voorbeelden
Hiermee wordt de inhoud van de *gebruikers gegevens* van de map verplaatst naar de hoofdmap van de doel bestands share:
``` console
\User data > \
```
Verplaatst de inhoud van het hele volume naar een nieuw pad op de doel bestands share:
``` console
\ > \Apps\HR tracker
```
Hiermee verplaatst u de inhoud van de bronmap naar een nieuw pad op de doel bestands share:
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
Hiermee worden meerdere bron locaties in een nieuwe mapstructuur gesorteerd:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Semantische regels

* Geef altijd mappaden op die relatief zijn ten opzichte van het hoofd niveau.
* Begin elke mappad met een indicator op hoofd niveau \\ .
* Geen stationsletters bevatten.
* Wanneer u meerdere paden opgeeft, kunnen de bron-of doel paden elkaar niet overlappen:</br>
   Ongeldig overlappend voor beeld van bronpad:</br>
    *\\folder\1 > \\ map*</br>
    *\\map \\ 1 \\ 2 > \\ map2*</br>
   Ongeldig doelpad-voor beeld:</br>
   *\\map > \\*</br>
   *\\map2 > \\*</br>
* De bron mappen die niet bestaan, worden genegeerd.
* Er worden mapstructuren gemaakt die niet bestaan in het doel.
* Net als bij Windows zijn mapnamen niet hoofdletter gevoelig, maar worden ze behouden.

> [!NOTE]
> De inhoud van de map *\System Volume Information* en de *$Recycle. bin* op uw StorSimple-volume worden niet gekopieerd door de migratie taak.

### <a name="run-a-migration-job"></a>Een migratie taak uitvoeren

Uw migratie taken worden weer gegeven onder *taak definities* in de Data Manager resource die u hebt geïmplementeerd naar een resource groep.
Selecteer in de lijst met taak definities de taak die u wilt uitvoeren.

In de Blade taak die wordt geopend, ziet u dat de taken worden uitgevoerd in de onderste lijst. In eerste instantie is deze lijst leeg. Boven aan de Blade ziet u een opdracht met de naam *taak uitvoeren*. Met deze opdracht wordt de taak niet onmiddellijk uitgevoerd, wordt de Blade **taak uitvoeren** geopend:

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job.png" alt-text="Een afbeelding met de Blade taak uitvoeren met een vervolg keuzelijst geopend, waarin de geselecteerde back-ups worden weer gegeven die moeten worden gemigreerd. De oudste back-up is gemarkeerd en moet eerst worden geselecteerd." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-run-job-expanded.png":::
    :::column-end:::
    :::column:::
        In deze release moet elke taak meermaals worden uitgevoerd. </br></br>**U moet beginnen met de oudste back-up uit de lijst met back-ups die u wilt migreren.** (gemarkeerd in de afbeelding)</br></br>U voert de taak opnieuw uit, op het moment dat u back-ups hebt geselecteerd, elke keer voor een progressief nieuwere back-up.
        </br></br>
        > [!CAUTION]
        > Het is van belang dat u de migratie taak uitvoert met de oudste back-up die als eerste is geselecteerd en vervolgens weer, telkens met een progressief nieuwere back-up. U moet de volg orde van uw back-ups altijd hand matig behouden, van oudste naar nieuwste.
    :::column-end:::
:::row-end:::

### <a name="phase-3-summary"></a>Samen vatting fase 3

Aan het einde van fase 3 hebt u ten minste één van de migratie taken van StorSimple-volumes uitgevoerd naar Azure-bestands shares. U moet dezelfde migratie taak enkele keer uitvoeren, van oudste naar meest recente back-ups die moeten worden gemigreerd. U kunt zich nu richten op het instellen van Azure File Sync voor de share (zodra de migratie taken voor een share zijn voltooid) of het direct delen van toegang voor uw informatie medewerkers en apps naar de Azure-bestands share.

## <a name="phase-4-access-your-azure-file-shares"></a>Fase 4: toegang tot uw Azure-bestands shares

Er zijn twee belang rijke strategieën voor het openen van uw Azure-bestands shares:

* **Azure file sync**: [Implementeer Azure file sync](#deploy-azure-file-sync) op een on-premises Windows Server-exemplaar. Azure File Sync heeft alle voor delen van een lokale cache, net als bij StorSimple.
* **Direct share-toegang**: [direct delen-toegang implementeren](#deploy-direct-share-access). Gebruik deze strategie als uw toegangs scenario voor een bepaalde Azure-bestands share geen gebruikmaken van lokale cache of als u niet langer de mogelijkheid hebt om een on-premises Windows Server-exemplaar te hosten. Hier blijven uw gebruikers en apps toegang tot SMB-shares via het SMB-protocol. Deze shares bevinden zich niet meer op een on-premises server, maar rechtstreeks in de Cloud.

U moet al bepalen welke optie het meest geschikt is voor u in [fase 1](#phase-1-prepare-for-migration) van deze hand leiding.

De rest van deze sectie richt zich op implementatie-instructies.

### <a name="deploy-azure-file-sync"></a>Azure Files SYNC implementeren

Het is tijd om een deel van Azure File Sync te implementeren.

1. Maak de Azure File Sync Cloud resource.
1. Implementeer de Azure File Sync-agent op uw on-premises server.
1. Registreer de server bij de Cloud resource.

Maak nog geen synchronisatie groepen. Het instellen van synchronisatie met een Azure-bestands share mag alleen plaatsvinden nadat de migratie taken naar een Azure-bestands share zijn voltooid. Als u begint met het gebruik van Azure File Sync voordat de migratie is voltooid, zou u de migratie onnodig moeilijk maken, omdat u niet eenvoudig kunt zien wanneer het tijd is om een uitsnede te initiëren.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>De Azure File Sync Cloud resource implementeren

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Als u de Azure-regio wilt wijzigen waarin uw gegevens zich bevinden nadat de migratie is voltooid, implementeert u de opslag synchronisatie service in dezelfde regio als de doel opslag accounts voor deze migratie.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>Een on-premises Windows Server-exemplaar implementeren

* Maak Windows Server 2019 (mini maal 2012R2) als een virtuele machine of fysieke server. Een failover-cluster van Windows Server wordt ook ondersteund. Gebruik de server niet opnieuw op de StorSimple 8100 of 8600.
* Direct gekoppelde opslag inrichten of toevoegen. Met het netwerk gekoppelde opslag wordt niet ondersteund.

Het is best practice om uw nieuwe Windows Server-exemplaar een gelijke of grotere hoeveelheid opslag ruimte te geven dan uw StorSimple 8100-of 8600-apparaat lokaal beschikbaar is voor caching. U gebruikt het Windows Server-exemplaar op dezelfde manier als het StorSimple-apparaat. Als deze dezelfde hoeveelheid opslag ruimte heeft als het apparaat, moet de cache-ervaring gelijk zijn, als dat niet zo is. U kunt op elk gewenst tijdstip opslag toevoegen aan of verwijderen uit uw Windows Server-exemplaar. Met deze mogelijkheid kunt u de grootte van uw lokale volume en de hoeveelheid lokale opslag die beschikbaar is voor caching, schalen.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>Het Windows Server-exemplaar voorbereiden voor bestands synchronisatie

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>Azure File Sync op het Windows Server-exemplaar configureren

Uw geregistreerde on-premises Windows Server-exemplaar moet gereed zijn en zijn verbonden met internet voor dit proces.

> [!IMPORTANT]
> Uw StorSimple-migratie van bestanden en mappen naar de Azure-bestands share moet zijn voltooid voordat u verder kunt gaan. Zorg ervoor dat er geen wijzigingen meer worden aangebracht aan de bestands share.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Zorg ervoor dat u Cloud lagen inschakelt. Cloud lagen is de Azure File Sync-functie waarmee de lokale server minder opslag capaciteit kan hebben dan is opgeslagen in de Cloud, maar de volledige naam ruimte beschikbaar is. Lokaal interessante gegevens worden ook lokaal in de cache opgeslagen voor snelle, lokale toegangs prestaties. Een andere reden om Cloud-Tiering in deze stap in te scha kelen, is dat we de bestands inhoud in deze fase niet mogen synchroniseren. Alleen de naam ruimte moet op dit moment worden verplaatst.

### <a name="deploy-direct-share-access"></a>Direct share-toegang implementeren

:::row:::
    :::column:::
        [![Stapsgewijze hand leiding en demo voor het veilig beschikbaar maken van Azure-bestands shares voor informatie medewerkers en apps-Klik om af te spelen.](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/a-Twfus0HWE)
    :::column-end:::
    :::column:::
        Deze video bevat een hand leiding en demo voor het veilig beschikbaar maken van Azure-bestands shares aan informatie medewerkers en apps in vijf eenvoudige stappen.</br>
        De video verwijst naar specifieke documentatie voor sommige onderwerpen:

* [Overzicht van identiteit](storage-files-active-directory-overview.md)
* [Een domein toevoegen aan een opslag account](storage-files-identity-auth-active-directory-enable.md)
* [Overzicht van netwerken voor Azure-bestands shares](storage-files-networking-overview.md)
* [Open bare en privé-eind punten configureren](storage-files-networking-endpoints.md)
* [Een S2S-VPN configureren](storage-files-configure-s2s-vpn.md)
* [Een Windows P2S VPN configureren](storage-files-configure-p2s-vpn-windows.md)
* [Een Linux P2S-VPN configureren](storage-files-configure-p2s-vpn-linux.md)
* [DNS-door sturen configureren](storage-files-networking-dns.md)
* [DFS configureren-N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Samen vatting fase 4

In deze fase hebt u meerdere migratie taken gemaakt en uitgevoerd in uw StorSimple Data Manager. Deze taken hebben uw bestanden en mappen gemigreerd naar Azure-bestands shares. U hebt ook Azure File Sync geïmplementeerd of uw netwerk-en opslag accounts voor direct share-toegang voor bereid.

## <a name="phase-5-user-cut-over"></a>Fase 5: gebruikers knippen

In deze fase wordt de migratie afronden:

* Plan uw downtime.
* Zorg ervoor dat alle wijzigingen die uw gebruikers en apps op de StorSimple zijn gemaakt, worden uitgevoerd terwijl de migratie taken in fase 3 actief zijn.
* Laat uw gebruikers niet overdoen op het nieuwe Windows Server-exemplaar met Azure File Sync of de Azure-bestands shares via direct share-toegang.

### <a name="plan-your-downtime"></a>Uw downtime plannen

Deze migratie aanpak vereist enige downtime voor uw gebruikers en apps. Het doel is om de uitval tijd tot een minimum te beperken. De volgende overwegingen kunnen helpen:

* Zorg ervoor dat uw StorSimple-volumes beschikbaar zijn tijdens het uitvoeren van uw migratie taken.
* Wanneer u klaar bent met het uitvoeren van uw gegevens migratie taken voor een share, is het tijd om gebruikers toegang te verwijderen (ten minste schrijf toegang) van de StorSimple-volumes of-shares. Met een eind-RoboCopy wordt uw Azure-bestands share opgeteld. Vervolgens kunt u de gebruikers over de gewenste snij rechten beschikken. Waar u RoboCopy uitvoert, is afhankelijk van of u ervoor hebt gekozen om Azure File Sync of direct share-Access te gebruiken. In de komende sectie op RoboCopy vallen deze onderwerpen.
* Nadat u de RoboCopy-up hebt voltooid, kunt u de nieuwe locatie door de Azure-bestands share rechtstreeks of op een SMB-share op een Windows Server-exemplaar met Azure File Sync beschikbaar maken voor uw gebruikers. Vaak wordt een DFS-N-implementatie gebruikt om snel en efficiënt een knippen uit te voeren. De bestaande share adressen blijven consistent en verwijzen naar een nieuwe locatie die de gemigreerde bestanden en mappen bevat.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Bepalen wanneer uw naam ruimte volledig is gesynchroniseerd met uw server

Wanneer u Azure File Sync gebruikt voor een Azure-bestands share, is het belang rijk dat u bepaalt dat u de volledige naam ruimte hebt gedownload naar de server *voordat* u een lokale Robocopy start. De tijd die nodig is om uw naam ruimte te downloaden is afhankelijk van het aantal items in de Azure-bestands share. Er zijn twee methoden om te bepalen of uw naam ruimte op de server volledig is aangekomen.

#### <a name="azure-portal"></a>Azure Portal

U kunt de Azure Portal gebruiken om te zien wanneer uw naam ruimte volledig is aangekomen.

* Meld u aan bij de Azure Portal en ga naar de synchronisatie groep. Controleer de synchronisatie status van uw synchronisatie groep en server eindpunt.
* De interessante richting wordt gedownload. Als het server-eind punt nieuw is ingericht, wordt de **eerste synchronisatie** weer gegeven, wat aangeeft dat de naam ruimte nog steeds niet beschikbaar is.
Nadat dit is gewijzigd in een andere, maar een **initiële synchronisatie**, wordt uw naam ruimte volledig ingevuld op de server. U kunt nu door gaan met een lokale RoboCopy.

#### <a name="windows-server-event-viewer"></a>Windows Server-Logboeken

U kunt ook de Logboeken op uw Windows Server-exemplaar gebruiken om te zien wanneer de naam ruimte volledig is aangekomen.

1. Open de **Logboeken** en ga naar **toepassingen en services**.
1. Ga naar en open **Microsoft\FileSync\Agent\Telemetry**.
1. Zoek naar de meest recente **gebeurtenis 9102**, die overeenkomt met een voltooide synchronisatie sessie.
1. Selecteer **Details** en bevestig dat u op zoek bent naar een gebeurtenis waar de **SyncDirection** -waarde wordt **gedownload**.
1. Voor het moment waarop uw naam ruimte het downloaden van de server heeft voltooid, is er één gebeurtenis met **scenario**, de waarde **FullGhostedSync** en **HResult**  =  **0**.
1. Als u deze gebeurtenis hebt gemist, kunt u ook andere **9102-gebeurtenissen** zoeken met **SyncDirection**  =  **down load** en **scenario**  =  **"RegularSync"**. Als u een van deze gebeurtenissen zoekt, geeft u ook aan dat de naam ruimte is gedownload en dat de synchronisatie is voltooid naar reguliere synchronisatie sessies, ongeacht of er op dit moment iets is om te synchroniseren.

### <a name="a-final-robocopy"></a>Een laatste RoboCopy

Op dit moment zijn er verschillen tussen uw on-premises Windows Server-exemplaar en het StorSimple 8100-of 8600-apparaat.

1. U moet de wijzigingen die gebruikers of apps op de StorSimple hebben geproduceerd tijdens de migratie door lopen.
1. Voor gevallen waarin u Azure File Sync gebruikt: het StorSimple-apparaat heeft een gevulde cache in plaats van het Windows Server-exemplaar met alleen een naam ruimte zonder bestands inhoud die lokaal is opgeslagen. De laatste RoboCopy kan u helpen uw lokale Azure File Sync-cache te starten door over te halen van de lokaal opgeslagen bestanden in de cache, net zo lang als beschikbaar is en past op de Azure File Sync-server.
1. Sommige bestanden zijn mogelijk achtergelaten door de migratie taak vanwege ongeldige tekens. Als dit het geval is, kopieert u deze naar het Windows Server-exemplaar met Azure File Sync. Later kunt u ze aanpassen zodat ze worden gesynchroniseerd. Als u Azure File Sync niet gebruikt voor een bepaalde share, is het beter om de namen van de bestanden te wijzigen met ongeldige tekens op het StorSimple-volume. Voer vervolgens de RoboCopy rechtstreeks uit op de Azure-bestands share.

> [!WARNING]
> Robocopy in Windows Server 2019 heeft momenteel een probleem waardoor bestanden die worden gelaagd door Azure File Sync op de doel server, opnieuw moeten worden gekopieerd van de bron en opnieuw moeten worden geüpload naar Azure wanneer u de functie/MIR van Robocopy gebruikt. Het is essentieel dat u Robocopy gebruikt op een andere Windows-Server dan 2019. Een voorkeurs keuze is Windows Server 2016. Deze notitie wordt bijgewerkt als het probleem wordt opgelost via Windows Update.

> [!WARNING]
> U *moet* de Robocopy niet starten voordat de server de naam ruimte heeft voor een Azure-bestands share die volledig is gedownload. Zie [bepalen wanneer uw naam ruimte volledig is gedownload naar uw server](#determine-when-your-namespace-has-fully-synced-to-your-server)voor meer informatie.

 U wilt alleen bestanden kopiëren die zijn gewijzigd nadat de migratie taak voor het laatst werd uitgevoerd en bestanden die niet via deze taken zijn verplaatst. U kunt het probleem oplossen om te voor komen dat ze later op de server worden verplaatst nadat de migratie is voltooid. Zie [Azure file sync Troubleshooting](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)(Engelstalig) voor meer informatie.

RoboCopy heeft verschillende para meters. In het volgende voor beeld wordt een voltooide opdracht weer geven en een lijst met redenen voor het kiezen van deze para meters.

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /NP /B /MIR /IT /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Achtergrondbitmap

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Hiermee kan RoboCopy meerdere threads uitvoeren. De standaard waarde is 8 en de maximum waarde is 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Hiermee wordt de status van de uitvoer naar een logboek bestand opgeslagen als UNICODE (het bestaande logboek wordt overschreven).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      Uitvoer naar console venster. Wordt gebruikt in combi natie met uitvoer naar een logboek bestand.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /NP
   :::column-end:::
   :::column span="1":::
      Laat de logboek registratie van de voortgang ongewijzigd om het logboek leesbaar te blijven.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      Voert RoboCopy uit in dezelfde modus als een back-uptoepassing zou gebruiken. Hiermee kan RoboCopy bestanden verplaatsen waarnaar de huidige gebruiker geen machtigingen heeft.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Hiermee kan voor RoboCopy alleen verschillen worden overwogen tussen de bron (StorSimple-apparaat) en het doel (Windows Server-map).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /IT
   :::column-end:::
   :::column span="1":::
      Zorgt dat de betrouw baarheid in bepaalde spiegel scenario's blijft behouden.</br>Voor beeld: tussen twee Robocopy wordt een bestand met een ACL-wijziging en een kenmerk update uitgevoerd, zoals het is gemarkeerd als *verborgen*. Zonder/IT kan de ACL-wijziging niet worden gemist door Robocopy en dus niet worden overgedragen naar de doel locatie.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      Betrouw baarheid van het bestand kopiëren (standaard is/COPY: DAT), kopieer vlaggen: D = gegevens, A = kenmerken, T = tijds tempels, S = Security = NTFS Acl's, O = eigenaargegevens, U = controle-informatie.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      / COPYALL
   :::column-end:::
   :::column span="1":::
      Kopieer alle bestands informatie (gelijk aan/COPY: DATSOU).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      Betrouw baarheid voor het kopiëren van mappen (standaard is/DCOPY: DA), kopieer vlaggen: D = gegevens, A = kenmerken, T = tijds tempels.
   :::column-end:::
:::row-end:::

Wanneer u bron-en doel locaties van de RoboCopy-opdracht configureert, moet u de structuur van de bron en het doel controleren om ervoor te zorgen dat ze overeenkomen. Als u de functie voor adreslijst toewijzing van de migratie taak hebt gebruikt, kan de structuur van de hoofdmap afwijken van de structuur van uw StorSimple-volume. Als dat het geval is, hebt u mogelijk meerdere RoboCopy-taken nodig, één voor elke submap. Als u niet zeker weet of de opdracht wordt uitgevoerd zoals verwacht, kunt u de para meter */l* gebruiken. Hiermee wordt de opdracht gesimuleerd zonder dat er wijzigingen worden aangebracht.

Deze RoboCopy-opdracht gebruikt/MIR, zodat er geen bestanden worden verplaatst die hetzelfde zijn (gelaagde bestanden, bijvoorbeeld). Maar als u het pad naar de bron en het doelpad onjuist ophaalt, verwijdert/MIR ook mapstructuren op uw Windows Server-exemplaar of Azure-bestands share die niet aanwezig zijn in het StorSimple. Ze moeten exact overeenkomen voor de RoboCopy-taak om het beoogde doel te bereiken van het bijwerken van uw gemigreerde inhoud met de laatste wijzigingen die zijn aangebracht tijdens de migratie.

Raadpleeg het RoboCopy-logboek bestand om te zien of er bestanden achterblijven. Als er problemen zijn, corrigeert u deze en voert u de RoboCopy-opdracht opnieuw uit. Zorg ervoor dat u geen StorSimple-resources vrijmaakt voordat u uitmuntende problemen voor bestanden of mappen die u bezorgt, verhelpt.

Als u Azure File Sync niet gebruikt om de desbetreffende Azure-bestands share in de cache op te slaan, maar wel hebt gekozen voor directe share-toegang:

1. [Koppel uw Azure-bestands share](storage-how-to-use-files-windows.md#mount-the-azure-file-share) als een netwerk station aan een lokale Windows-computer.
1. Voer de RoboCopy uit tussen uw StorSimple en de gekoppelde Azure-bestands share. Als bestanden niet worden gekopieerd, herstelt u hun namen aan de StorSimple-zijde om ongeldige tekens te verwijderen. Probeer RoboCopy opnieuw. De eerder genoemde RoboCopy-opdracht kan meerdere keren worden uitgevoerd zonder onnodig terughalen naar StorSimple.

### <a name="user-cut-over"></a>Gebruiker knippen

Als u Azure File Sync gebruikt, moet u waarschijnlijk de SMB-shares op dat Azure File Sync Windows Server-exemplaar maken dat overeenkomt met de shares die u op de StorSimple-volumes had. U kunt deze stap vooraan laden en dit vroeger doen om geen tijd te verliezen. Maar u moet er wel voor zorgen dat niemand toegang heeft tot wijzigingen in de Windows Server-instantie.

Als u een DFS-N-implementatie hebt, kunt u de DFN-Namespaces naar de nieuwe locatie van de servermap wijzen. Als u geen DFS-N-implementatie hebt en u uw 8100-of 8600-apparaat lokaal hebt gefronteerd met een Windows Server-exemplaar, kunt u die server uit het domein halen. Vervolgens voegt u een domein toe aan uw nieuwe Windows Server-exemplaar met Azure File Sync. Geef tijdens dat proces de server dezelfde server naam en share namen als de oude server, zodat de uitsnede transparant blijft voor uw gebruikers, groeps beleid en scripts.

Meer informatie over [DFS-N](/windows-server/storage/dfs-namespaces/dfs-overview).

## <a name="deprovision"></a>Inrichting

Wanneer u de inrichting van een bron ongedaan maakt, verliest u de toegang tot de configuratie van die bron en de gegevens ervan. Het ongedaan maken van de inrichting van de toewijzing is niet mogelijk. Ga pas verder als u hebt bevestigd dat:

* De migratie is voltooid.
* Er zijn geen afhankelijkheden voor de StorSimple-bestanden, mappen of volume back-ups die u wilt opheffen.

Voordat u begint, is het een best practice om uw nieuwe implementatie van Azure File Sync tijdens de productie tijd te observeren. Op die manier kunt u eventuele problemen oplossen die zich kunnen voordoen. Nadat u uw Azure File Sync-implementatie ten minste enkele dagen hebt waargenomen, kunt u de inrichting van resources in deze volg orde ongedaan maken:

1. De inrichting van uw StorSimple Data Manager-bron via de Azure Portal ongedaan maken. Al uw DTS-taken worden hiermee verwijderd. U kunt de Kopieer logboeken niet eenvoudig ophalen. Als ze belang rijk zijn voor uw records, haalt u deze op voordat u de inrichting ongedaan maakt.
1. Zorg ervoor dat uw fysieke StorSimple-apparaten zijn gemigreerd en hef de registratie ervan op. Als u niet volledig zeker weet dat ze zijn gemigreerd, kunt u niet door gaan. Als u de inrichting van deze resources ongedaan maakt terwijl ze nog steeds nodig zijn, kunt u de gegevens of de configuratie ervan niet herstellen.<br>U kunt eventueel eerst de inrichting van de StorSimple-volume bron ongedaan maken, waardoor de gegevens op het apparaat worden opgeruimd. Dit kan enkele dagen duren en de gegevens op het apparaat **worden niet** forensically op nul. Als dit belang rijk voor u is, moet u schijf Zeroing afzonderlijk van de bron inrichting en volgens uw beleid afhandelen.
1. Als er nog geen geregistreerde apparaten meer zijn in een StorSimple-Apparaatbeheer, kunt u door gaan met het verwijderen van die Apparaatbeheer resource zelf.
1. Het is nu tijd om het StorSimple-opslag account te verwijderen in Azure. Het opnieuw, stoppen en bevestigen van de migratie is voltooid en die niets en niemand is afhankelijk van deze gegevens voordat u verdergaat.
1. Ontkoppel het fysieke StorSimple-apparaat uit uw Data Center.
1. Als u de eigenaar van het StorSimple-apparaat bent, kunt u het niet meer op de PC recyclen. Als uw apparaat wordt geleasd, wordt de lessor geïnformeerd en wordt het apparaat zo nodig geretourneerd.

De migratie is voltooid.

> [!NOTE]
> Hebt u nog vragen of hebt u problemen ondervonden?</br>
> We helpen u graag AzureFilesMigration@microsoft.com .

## <a name="next-steps"></a>Volgende stappen

* Krijg meer vertrouwd met [Azure File Sync: aka.MS/AFS](./storage-sync-files-planning.md).
* Meer informatie over de flexibiliteit van beleids regels voor [Cloud lagen](storage-sync-cloud-tiering-overview.md) .
* [Schakel Azure backup](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) in voor uw Azure-bestands shares om moment opnamen te plannen en schema's voor het bewaren van back-ups te definiëren.
* Als u in de Azure Portal ziet dat sommige bestanden permanent niet worden gesynchroniseerd, raadpleegt u de [hand leiding](storage-sync-files-troubleshoot.md) voor het oplossen van problemen om deze problemen op te lossen.
