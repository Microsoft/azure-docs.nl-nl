---
title: Veelgestelde vragen over Microsoft Azure Data Box Disk | Microsoft Docs in gegevens
description: Bevat veelgestelde vragen en antwoorden voor Azure Data Box Disk, een cloudoplossing waarmee u grote hoeveelheden gegevens naar Azure kunt overdragen
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: 483ae81f22358e41f252dd8090c01d5b4cad7fb1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96344161"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure Data Box Disk: veelgestelde vragen

Met de cloudoplossing Microsoft Azure Data Box Disk kunt u terabytes aan gegevens op een snelle, goedkope en betrouwbare manier naar Azure verzenden. Hier vindt u antwoorden op vragen die u mogelijk hebt over het gebruik van Data Box-schijven in de Azure-portal. 

De vragen en antwoorden zijn in de volgende categorieën onderverdeeld:

- Over de service
- Configureren en verbinding maken 
- Status bijhouden
- Gegevens migreren 
- Gegevens verifiëren en uploaden 


## <a name="about-the-service"></a>Over de service

### <a name="q-what-is-azure-data-box-service"></a>V. Wat is de Azure Data Box-service? 
A.  De service Azure Data Box is bedoeld voor offlinegegevensopname. Met deze service wordt een reeks producten beheerd die geschikt zijn voor gegevenstransport bij verschillende opslagcapaciteiten. 

### <a name="q-what-are-azure-data-box-disks"></a>V. Wat is Azure Data Box Disks?
A. Met Azure Data Box Disks kunt u terabytes aan gegevens snel, goedkoop en veilig naar en vanuit Azure overdragen. U ontvangt maximaal vijf schijven, met een maximale opslagcapaciteit van 35 TB. U kunt deze schijven makkelijk configureren, verbinden en ontgrendelen via de Data Box-service in Azure Portal.  

Schijven worden versleuteld met Microsoft BitLocker-stationsversleuteling en uw encryptiesleutels worden beheerd in Azure Portal. Vervolgens kopieert u de gegevens van de servers van de klant. In het datacenter worden uw gegevens naar de cloud gemigreerd met behulp van een snelle uploadkoppeling via een privénetwerk en naar Azure geüpload.

### <a name="q-when-should-i-use-data-box-disks"></a>V. Wanneer moet ik Data Box Disks gebruiken?
A. Als u 40 TB (of minder) aan gegevens naar Azure wilt overdragen, hebt u baat bij het gebruik van Data Box Disks.

### <a name="q-what-is-the-price-of-data-box-disks"></a>V. Wat kosten Data Box-schijven?
A. Ga naar de [pagina Prijzen](https://azure.microsoft.com/pricing/details/databox/disk/) voor informatie over de prijs van Data Box-schijven.

### <a name="q-how-do-i-get-data-box-disks"></a>V. Hoe verkrijg ik Azure Data Box Disks? 
A.  Als u Azure Data Box-schijven wilt aanschaffen, meldt u zich aan bij Azure Portal en plaatst u een Data Box-bestelling voor schijven. Geef uw contactgegevens en overige informatie op. Zodra u een bestelling hebt geplaatst, worden de schijven op basis van beschikbaarheid binnen tien dagen verzonden.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>V. Wat is de maximale hoeveelheid gegevens die ik met Data Box Disks in één keer kan overdragen?
A. Bij vijf schijven van 8 TB (7 TB aan bruikbare capaciteit), bedraagt de maximaal bruikbare capaciteit 35 TB. U kunt dus 35 TB aan gegevens in één keer overdragen. Als u meer gegevens wilt overdragen, moet u meer schijven bestellen.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>V. Hoe kan ik controleren of Data Box Disks in mijn regio beschikbaar is? 
A.  Als u wilt zien waar de Data Box schijven op dit moment beschikbaar zijn, gaat u naar de [Beschik baarheid](data-box-disk-overview.md#region-availability)van de regio.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>V. In welke regio's kan ik gegevens opslaan met Data Box Disks?
A. Data Box Disk wordt ondersteund voor alle regio's binnen de Verenigde Staten, Canada, Australië, Europa-west en Europa-noord, Korea en Japan. Alleen de Azure-regio's met een openbare cloud worden ondersteund. Azure Government of andere onafhankelijke clouds worden niet ondersteund.

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>V. In welke regio's kan ik gegevens opslaan met Data Box Disks?
A. Data Box Disk wordt ondersteund voor alle regio's binnen de Verenigde Staten, Canada, Australië, Europa-west en Europa-noord, Korea en Japan. Alleen de Azure-regio's met een openbare cloud worden ondersteund. Azure Government of andere onafhankelijke clouds worden niet ondersteund.

### <a name="q-how-can-i-import-source-data-present-at-my-location-in-one-countryregion-to-an-azure-region-in-a-different-country"></a>V. Hoe kan ik bron gegevens op mijn locatie in een land/regio importeren naar een Azure-regio in een ander land?
A. Data Box Disk ondersteunt alleen gegevens opname binnen hetzelfde land/dezelfde regio als de bestemming en snijdt geen internationale grenzen. De enige uitzonde ring hierop is voor orders in de Europese Unie (EU), waar Data Box schijven kunnen worden verzonden naar en van een EU-land/regio.

Als u bijvoorbeeld gegevens op uw locatie in Canada wilt verplaatsen naar een Azure Westus-opslag account, kunt u deze op de volgende manier belopen:

### <a name="option-1"></a>Optie 1: 

Een [ondersteunde schijf](../storage/common/storage-import-export-requirements.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#supported-disks) met gegevens verzenden met de [Azure import/export-service](../storage/common/storage-import-export-service.md) van de bron locatie in Canada naar het Azure westus Data Center.

### <a name="option-2"></a>Optie 2:

1. Volg Data Box Disk in Canada door een opslag account te kiezen in Cananda Central. De SSD-schijven worden verzonden vanuit het Azure-Data Center in Canada-centraal naar het verzend adres (in Canada) dat wordt gegeven tijdens het maken van de order.

2. Nadat de gegevens van uw on-premises server naar de schijven zijn gekopieerd, gaat u terug naar het Azure-Data Center in Canada met behulp van de micro soft-retour labels. De gegevens die aanwezig zijn op de Data Box Disk, worden vervolgens geüpload naar het doel-opslag account in de Canada Azure-regio die u hebt gekozen tijdens het maken van de order.

3. U kunt vervolgens een hulp programma als AzCopy gebruiken om de gegevens naar een opslag account in Westus te kopiëren. Met deze stap worden [standaard](https://azure.microsoft.com/pricing/details/storage/) kosten voor opslag en [band breedte](https://azure.microsoft.com/pricing/details/bandwidth/) in rekening gebracht die niet zijn opgenomen in de facturering van data Box disk.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>V. Met wie moet ik contact opnemen als ik een probleem krijg met Data Box-schijven?
A. Neem bij problemen met Data Box-schijven contact op met [Microsoft Ondersteuning](./data-box-disk-contact-microsoft-support.md).

## <a name="configure-and-connect"></a>Configureren en verbinding maken
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>V. Kan ik het aantal Data Box-schijven in de bestelling opgeven?
A.  Nee. Het aantal schijven van 8 TB (maximaal vijf) dat u ontvangt, is afhankelijk van uw gegevensgrootte en de beschikbaarheid van de schijven.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>V. Hoe ontgrendel ik de Data Box-schijven? 
A.  Ga in Azure Portal naar uw Data Box Disk-bestelling en vervolgens naar **Apparaatdetails**. Kopieer de wachtwoordsleutel. Download in de Azure-portal het ontgrendelingsprogramma van Data Box Disk voor uw besturingssysteem en pak het programma uit. Voer het hulpprogramma uit op de computer waarop de gegevens staan die u wilt kopiëren naar de schijven. Geef de wachtwoordsleutel op om de schijven te ontgrendelen. U kunt alle schijven met dezelfde wachtwoordsleutel ontgrendelen. 

Voor stapsgewijze instructies gaat u naar [Schijven ontgrendelen op een Windows-client](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) of [Schijven ontgrendelen op een Linux-client](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>V. Kan ik een Linux-hostcomputer gebruiken om verbinding te maken met de gegevens en deze naar de Data Box-schijven te kopiëren?
A.  Ja. Zowel de Linux- als de Windows-client kan worden gebruikt om verbinding te maken en gegevens te kopiëren naar de Data Box-schijven. Ga naar de lijst met [ondersteunde besturingssystemen](data-box-disk-system-requirements.md) voor uw hostcomputer voor meer informatie.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>V. Mijn schijven zijn verzonden, maar ik wil de bestelling annuleren. Waarom is er geen knop Annuleren?
A.  U kunt de bestelling alleen annuleren nadat de schijven zijn besteld en voordat ze worden verzonden. Zodra de schijven zijn verzonden, kunt u de bestelling niet meer annuleren. U kunt uw schijven tegen extra kosten echter wel retourneren. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>V. Kan de hostcomputer verbinding maken met meerdere Data Box-schijven tegelijk om gegevens over te dragen?
A. Ja. Er kunnen meerdere Data Box-schijven verbinding maken met dezelfde hostcomputer om gegevens over te dragen en er kunnen meerdere kopieertaken parallel worden uitgevoerd.

## <a name="track-status"></a>Status bijhouden

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>V. Hoe kan ik de bestelstatus van de schijven volgen vanaf het moment dat de bestelling is geplaatst als ik de schijven terug wil sturen? 
A.  U kunt de status van de Data Box Disk-bestelling volgen in Azure Portal. Als u de bestelling plaatst, wordt u ook gevraagd een e-mailadres voor meldingen op te geven. Als u dat hebt gedaan, dan krijgt u elke keer per e-mail een melding als er een statuswijziging heeft plaatsgevonden. Meer informatie over het [configureren van e-mailmeldingen](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>V. Hoe kan ik de schijven terugsturen? 
A.  In de verpakking van de Data Box-schijven zit een verzendlabel. Bevestig het label aan de doos met de schijven en geef de verzegelde verpakking af bij de verzender. Als het label beschadigd is of verloren is gegaan, gaat u naar **Overzicht > Verzendlabel downloaden** en downloadt u een nieuw verzendlabel.

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>Kan ik mijn Data Box Disk bestelling zelf ophalen? Kan ik de schijven retour neren via een door mij gekozen provider?
A. Ja. Micro soft biedt ook zelf-beheerde verzen ding in US Gov regio. Wanneer u de Data Box Disk order plaatst, kunt u de optie voor zelf-beheerde verzen ding kiezen. Voer de volgende stappen uit om uw Data Box Disk order op te halen:
    
1. Nadat u de order hebt geplaatst, wordt de order verwerkt en worden de schijven voor bereid. U wordt gewaarschuwd via een e-mail bericht dat uw bestelling gereed is voor ophalen. 
2. Zodra de bestelling gereed is voor ophalen, gaat u naar uw bestelling in het Azure Portal en gaat u naar de Blade **overzicht** . 
3. Er wordt een melding met een code in de Azure Portal weer geven. E-mail het [Azure data Box Operations-team](mailto:adbops@microsoft.com) en geef de code op. Het team geeft de locatie op en plant een datum en tijd voor het ophalen. U moet het team aanroepen binnen 5 werk dagen nadat u het e-mail bericht hebt ontvangen.

Wanneer het kopiëren en valideren van de gegevens is voltooid, voert u de volgende stappen uit om uw schijf te retour neren:

1. Koppel de schijven los, nadat de gegevensvalidatie is voltooid. Verwijder de verbindingskabels.
2. Verpak alle schijven en de verbindingskabels in bubbelplastic en plaats deze in de verzenddoos. Er kunnen kosten in rekening worden gebracht als er accessoires ontbreken.

    - Gebruik de verpakking waarin u het pakket hebt gekregen. U wordt aangeraden schijven te verpakken in stevig bubbelplastic.
    - Zorg ervoor dat alles goed past zodat het product niet in de doos kan gaan schuiven.
3. Ga naar de **Blade overzicht** voor uw bestelling in azure Portal. Er wordt een melding met een code weer geven.
4. Gebruik deze code om het [Azure data Box Operations-team](mailto:adbops@microsoft.com) te e-mailen en de code op te geven. Ze geven u informatie over waar en wanneer u de schijven moet neerzetten.


## <a name="migrate-data"></a>Gegevens migreren

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>V. Wat is de maximale hoeveelheid gegevens die voor Data Box-schijven kan worden gebruikt?  
A.  Data Box Disk kan uit maximaal vijf schijven bestaan met een maximale gebruikscapaciteit van 35 TB. De schijven zelf zijn elk 8 TB groot (waarvan 7 TB voor gebruik).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>V. Wat zijn de maximale groottes voor blok-blob en paginablob die door Data Box-schijven worden ondersteund? 
A.  De maximale groottes worden bepaald door Azure Storage-limieten. De maximale blok-blob is ongeveer 4,768 TiB en de maximale blobgrootte is 8 TiB. Zie [Schaalbaarheids- en prestatiedoelen voor blob-opslag](../storage/blobs/scalability-targets.md) voor meer informatie.

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>V. Wat is de snelheid van gegevensoverdracht voor Data Box-schijven?
A. Bij een test met schijven die verbonden waren via USB 3.0 was de prestatie van de schijven maximaal 430 MB/s. De werkelijke waarden variëren, afhankelijk van de gebruikte bestandsgrootte. Voor kleinere bestanden zijn de prestaties lager.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>V. Hoe weet ik of mijn gegevens veilig zijn tijdens de overdracht? 
A.  Data Box-schijven zijn versleuteld met BitLocker AES-128-bitsversleuteling; de wachtwoordsleutel is alleen beschikbaar via Azure Portal. Meld u aan bij Azure Portal met uw accountreferenties om de wachtwoordsleutel op te halen. Geef deze wachtwoordsleutel op als u het Data Box Disk-ontgrendelingsprogramma wilt uitvoeren.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>V. Hoe kan ik de gegevens naar de Data Box-schijven kopiëren? 
A.  Gebruik een SMB-kopieerprogramma als Robocopy of Diskboss als u gegevens naar schijven wilt kopiëren. U kunt ook slepen vanuit Windows Verkenner.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>V. Zijn er tips om het kopiëren van gegevens te versnellen?
A.  U kunt het kopieerproces als volgt versnellen:

- Gebruik meerdere gegevensstromen. In bijvoorbeeld Robocopy kunt u de optie voor meerdere threads gebruiken. Ga naar [Tutorial: Copy data to Azure Data Box Disk and verify](data-box-disk-deploy-copy-data.md#copy-data-to-disks) (Zelfstudie: Gegevens naar Azure Data Box kopiëren en verifiëren) voor meer informatie over de opdracht die u moet gebruiken.
- Gebruik meerdere sessies.
- In plaats van te kopiëren via een netwerkshare (waarbij de netwerksnelheid beperkend kan zijn), zorgt u ervoor dat de gegevens lokaal aanwezig zijn op de computer waarmee de schijven zijn verbonden.
- Zorg ervoor dat u USB 3.0 of hoger gebruikt gedurende het hele kopieerproces. Download en gebruik het hulpprogramma [USBView](/windows-hardware/drivers/debugger/usbview) om de USB-controllers en de USB-apparaten te identificeren die met de computer zijn verbonden.
- Voer een benchmark-test uit van de prestaties van de computer die wordt gebruikt om de gegevens te kopiëren. Download en gebruik het hulpprogramma [Bluestop FIO](https://ci.appveyor.com/project/axboe/fio) om een benchmark-test uit te voeren van de prestaties van de serverhardware. Selecteer de nieuwste versie van x86 of x64, selecteer het tabblad **artefacten** en down load het MSI-bestand.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>V. Hoe kan ik het kopiëren van de gegevens versnellen als de brongegevens uit kleine bestanden (kB's of enkele MB's) bestaan?
A.  U kunt het kopieerproces als volgt versnellen:

- Maak een lokale VHDx in snelle opslag of maak een lege VHD op de HDD/SSD (langzamer).
- Koppel deze aan een VM.
- Kopieer de bestanden naar de schijf van de virtuele machine.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>V. Kan ik meerdere opslagaccounts gebruiken voor Data Box Disks?
A.  Nee. Er wordt momenteel slechts één (algemeen of klassiek) opslagaccount ondersteund voor Data Box Disks. Zowel dynamische als statische blob wordt ondersteund. Op dit moment worden alleen de opslagaccounts in de V.S. en in West- en Noord-Europa in de openbare Azure-cloud ondersteund.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>V. Welke hulpprogramma's zijn er beschikbaar voor mijn gegevens op Data Box-schijven?
A. Er zijn drie hulpprogramma's beschikbaar voor Data Box Disk:
 - **Data Box Disk hulp programma voor ontgrendelen**: gebruik dit hulp programma om de versleutelde schijven te ontgrendelen die van micro soft worden geleverd. Wanneer u schijven ontgrendelt met het hulpprogramma moet u een wachtwoordsleutel opgeven. Deze vindt u in de gegevens van de Data Box Disk-bestelling in de Azure-portal. 
 - **Data Box Disk validatie hulpprogramma**: gebruik dit hulp programma om de grootte, indeling en BLOB-namen te valideren volgens de Azure-naamgevings conventies. Hiermee worden ook controlesommen voor de gekopieerde gegevens gemaakt. Deze worden vervolgens gebruikt voor het verifiëren van de gegevens die naar Azure worden geüpload.
 - **Data Box Disk Splits-hulp programma voor kopiëren**: gebruik dit hulp programma wanneer u meerdere schijven gebruikt en een grote gegevensset hebt die moet worden gesplitst en op alle schijven moeten worden gekopieerd. Dit hulpprogramma is momenteel beschikbaar voor Windows. Dit hulp programma wordt niet ondersteund met Managed disks. Dit hulp programma valideert ook bij het kopiëren van de gegevens. Daarom kunt u de validatie stap overs Laan wanneer u dit hulp programma gebruikt.

De set hulpprogramma's is beschikbaar voor zowel Windows als Linux. U kunt de set hulpprogramma's hier downloaden:
- [Data Box Disk-toolset voor Windows downloaden](https://aka.ms/databoxdisktoolswin) 
- [Data Box Disk-toolset voor Linux downloaden](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>V. Kan ik Data Box Disk gebruiken om gegevens over te brengen naar Azure Files en vervolgens de gegevens gebruiken met Azure File Sync? 
A. Azure Files worden ondersteund met Data Box Disk, maar werken niet goed met Azure File Sync. Meta gegevens blijven niet behouden als de bestands gegevens worden gebruikt met Azure File Sync.


## <a name="verify-and-upload"></a>Verifiëren en uploaden

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>V. Hoe snel krijg ik toegang tot mijn gegevens in azure nadat ik de schijven weer heb verzonden? 
A.  Zodra de bestellingsstatus voor Gegevens kopiëren als voltooid is aangemerkt, hebt u weer toegang tot uw gegevens.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>V. Waar bevinden zich mijn gegevens in Azure na het uploaden?
A.  Als u de gegevens onder de bestanden *BlockBlob* en *PageBlob* op uw schijf kopieert, wordt er voor elke submap onder de mappen *BlockBlob* en *PageBlob* een container gemaakt in het Azure Storage-account. Als u de bestanden onder de mappen *BlockBlob* en *PageBlob* rechtstreeks hebt gekopieerd, bevinden deze zich in de standaardcontainer *$root* onder het Azure Storage-account. Wanneer u de gegevens naar een map onder *AzureFile* map kopieert, wordt een file share gemaakt.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>V. Ik heb gemerkt dat ik de naamgevingsvereisten voor Azure niet voor mijn containers heb gevolgd. Worden mijn gegevens niet naar Azure geüpload?
A. Als de containernamen hoofdletters bevatten, worden deze automatisch naar kleine letters omgezet. Als de namen anderszins niet aan de vereisten voldoen (speciale tekens, andere taal, enzovoort), dan worden er geen gegevens geüpload. Ga voor meer informatie naar [Naamconventies](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>V. Hoe controleer ik de gegevens die ik naar meerdere Data Box-schijven heb gekopieerd?
A.  Als het kopiëren van de gegevens is voltooid, kunt u `DataBoxDiskValidation.cmd` uitvoeren in de map *DataBoxDiskImport* om controlesommen te genereren voor validatie. Als u meerdere schijven hebt, moet u per schijf een opdrachtvenster openen en deze opdracht uitvoeren. Houd er rekening mee dat deze bewerking lang kan duren (uren), afhankelijk van de grootte van de gegevens.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>V. Wat gebeurt er met de gegevens nadat ik de schijven heb teruggestuurd?
A.  Als de gegevens naar Azure zijn gekopieerd, worden de gegevens van de schijven veilig gewist volgens de richtlijnen van NIST SP 800-88 Revision 1.  

### <a name="q-how-is-my-data-protected-during-transit"></a>V. Hoe worden mijn gegevens tijdens het verzenden beveiligd? 
A.  De Data Box-schijven zijn versleuteld met AES-128 Microsoft BitLocker-stationsversleuteling. Hiervoor is één wachtwoordsleutel vereist om alle schijven en toegangsgegevens te ontgrendelen.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>V. Moet ik de controlesom opnieuw valideren als ik meer gegevens aan de Data Box-schijven toevoeg?
A. Ja. Als u besluit uw gegevens te valideren (aanbevolen), dan dient u de validatie opnieuw uit te voeren als u meer gegevens aan de schijven hebt toegevoegd.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>V. Ik heb al mijn schijven gebruikt om gegevens over te dragen. Ik wil meer schijven bestellen. Kan ik dat op een snelle manier doen?
A. U kunt een kloon maken van uw vorige bestelling. Hierdoor maakt u dezelfde bestelling als eerst en kunt u de details van de bestelling bewerken zonder dat u opnieuw uw adres, contactgegevens en meldingsinformatie hoeft te typen.

### <a name="q-i-copied-data-to-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-and-how-can-i-locate-it"></a>V. Ik heb gegevens gekopieerd naar de map ManagedDisk. Ik zie geen Managed disks met de resource groep die is opgegeven voor beheerde schijven. Zijn mijn gegevens geüpload naar Azure en hoe kan ik deze vinden?
A. Ja. Uw gegevens zijn geüpload naar Azure, maar als u geen beheerde schijven met de opgegeven resource groepen ziet, is het waarschijnlijk dat de gegevens ongeldig zijn. Als de pagina-blobs, blok-blobs, Azure Files en Managed disks niet geldig zijn, gaan ze naar de volgende mappen:
 - Pagina-blobs gaan naar een blok-BLOB-container die begint met *databoxdisk-ongeldig-PB*-.
 - Azure Files gaat naar een blok-BLOB-container die begint met *databoxdisk-ongeldig-af-*.
 - Beheerde schijven gaan naar een blok-BLOB-container, te beginnen met *databoxdisk-ongeldig-MD-*.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [Data Box disk systeem vereisten](data-box-disk-system-requirements.md).
- De [Limieten voor Data Box Disk](data-box-disk-limits.md) begrijpen.
- Snel [Azure Data Box Disk](data-box-disk-quickstart-portal.md) in Azure Portal implementeren.