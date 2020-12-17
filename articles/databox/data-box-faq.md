---
title: Veelgestelde vragen over Microsoft Azure Data Box | Microsoft Docs in gegevens
description: Bevat veelgestelde vragen en antwoorden voor Azure Data Box, een cloudoplossing waarmee u grote hoeveelheden gegevens naar Azure kunt overdragen.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/17/2020
ms.author: alkohli
ms.openlocfilehash: 87ec1f03a1f2294a4423e26129644eafcf7c915c
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655472"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: veelgestelde vragen

Met de hybride oplossing Microsoft Azure Data Box kunt u terabytes aan gegevens op een snelle, goedkope en betrouwbare manier naar Azure verzenden met behulp van een overdrachtsapparaat. Hier vindt u antwoorden op vragen die u mogelijk hebt over het gebruik van Data Box in de Azure Portal.

De vragen en antwoorden zijn in de volgende categorieën onderverdeeld:

- Over de service
- Apparaat bestellen
- Configureren en verbinding maken 
- Status bijhouden
- Gegevens kopiëren 
- Apparaat verzenden
- Gegevens verifiëren en uploaden 
- Ondersteuning voor de bewakingsketen

## <a name="about-the-service"></a>Over de service

### <a name="q-what-is-azure-data-box-service"></a>V. Wat is de Azure Data Box-service? 
A.  De service Azure Data Box is bedoeld voor offlinegegevensopname. Deze service beheert een matrix met producten met verschillende opslagcapaciteiten die allemaal zijn afgestemd voor gegevenstransport. 

### <a name="q-what-is-azure-data-box"></a>V. Wat is Azure Data Box?
A. De Azure Data Box biedt een snelle, goedkope en veilige overdracht van terabytes aan gegevens in Azure. U kunt het Data Box-apparaat bestellen via de Azure-portal. Microsoft verzendt een opslagapparaat met een bruikbare capaciteit van 80 TB via een regionale koerier. 

Nadat u het apparaat hebt ontvangen, kunt u het snel instellen via de lokale webinterface. Kopieer de gegevens van uw servers naar het apparaat of van het apparaat naar de servers en verzend het apparaat terug naar Azure. Voor een import volgorde, in het Azure-Data Center, worden uw gegevens automatisch van het apparaat naar Azure verzonden. Het volledige proces wordt gevolgd door de Data Box-service in de Azure-portal.

### <a name="q-when-should-i-use-data-box"></a>V. Wanneer moet ik Data Box gebruiken?
A. Als u 40-500 TB aan gegevens hebt die u wilt overdragen naar of van Azure, kunt u profiteren van Data Box. Voor gegevens grootte < 40 TB gebruikt u Data Box Disk en voor de grootte van gegevens > 500 TB, meldt u zich aan voor [Data Box Heavy](data-box-heavy-overview.md).

### <a name="q-what-is-the-price-of-data-box"></a>V. Wat kost Data Box?
A. Data Box is verkrijgbaar tegen een nominaal bedrag voor 10 dagen. Als u het productmodel selecteert terwijl u een bestelling in de Azure-portal maakt, zullen de kosten voor het apparaat worden weergegeven. Standaard verzend kosten en kosten voor Azure Storage zijn ook van toepassing. De export orders volgen een vergelijk bare prijs categorie als voor import orders, maar mogelijk zijn er extra uitgangs kosten van toepassing. 

Ga voor meer informatie naar [Azure data Box prijzen](https://azure.microsoft.com/pricing/details/storage/databox/) en [kosten](https://azure.microsoft.com/pricing/details/bandwidth/)voor uitgaand verkeer. 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>V. Wat is de maximale hoeveelheid gegevens die ik met Data Box in één keer kan overdragen?
A. Data Box heeft een onbewerkte capaciteit van 100 TB en een bruikbare capaciteit van 80 TB. U kunt met Data Box maximaal 80 TB aan gegevens overdragen. Als u meer gegevens wilt overdragen, moet u meer apparaten bestellen.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>V. Hoe kan ik controleren of Data Box in mijn regio beschikbaar is? 
A.  Voor informatie over welke landen/regio's de Data Box beschikbaar is, gaat u naar [beschik bare regio's](data-box-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>V. In welke regio's kan ik gegevens opslaan met Data Box?
A. Data Box wordt ondersteund voor alle regio's in VS, Europa-west, Europa-noord, Frank rijk, UK, Japan, Australië en Canada. Ga naar [beschik bare regio](data-box-overview.md#region-availability)voor meer informatie.

### <a name="q-how-can-i-import-source-data-at-my-location-in-a-particular-country-to-an-azure-region-in-a-different-countryregion-or-export-data-from-an-azure-region-in-one-country-to-a-different-countryregion"></a>V. Hoe kan ik bron gegevens op mijn locatie in een bepaald land importeren in een Azure-regio in een ander land/regio of gegevens uit een Azure-regio in één land exporteren naar een ander land/dezelfde regio?

Data Box ondersteunt opname van gegevens of uitgaand verkeer binnen hetzelfde land/dezelfde regio als de bestemming en snijdt geen internationale grenzen. De enige uitzonde ring hierop is voor orders in de Europese Unie (EU), waar gegevens vakken kunnen worden verzonden naar en van een EU-land/regio.

Als u in het scenario voor importeren bijvoorbeeld de bron gegevens in Canada had die u wilt verplaatsen naar een Azure West US-opslag account, kunt u dit op de volgende manier bereiken:

1. Volg Data Box in Canada door een opslag account in Canada te kiezen. Het apparaat wordt verzonden vanuit een Azure-Data Center in Canada naar het verzend adres (in Canada) dat wordt gegeven tijdens het maken van de order.

2. Als de on-premises gegevens kopie naar de Data Box is voltooid, gaat u terug naar het Azure-Data Center in Canada. De gegevens die aanwezig zijn op de Data Box, worden vervolgens geüpload naar het doel opslag account in de Canada Azure-regio die u hebt gekozen tijdens het maken van de order.

3. U kunt vervolgens een hulp programma als AzCopy gebruiken om de gegevens naar een opslag account in VS-West te kopiëren. Met deze stap worden [standaard](https://azure.microsoft.com/pricing/details/storage/) kosten voor opslag en [band breedte](https://azure.microsoft.com/pricing/details/bandwidth/) in rekening gebracht die niet zijn opgenomen in de facturering van data box.

### <a name="q-who-should-i-contact-if-i-come-across-any-issues-with-data-box"></a>V. Met wie moet ik contact opnemen als ik problemen ondervindt met Data Box?
A. Als u problemen ondervindt met Data Box, [neemt u contact op met Microsoft ondersteuning](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-lost-my-data-box-is-there-a-lost-device-charge"></a>V. Ik ben mijn Data Box kwijt geraakt. Zijn er kosten voor een verloren apparaat?
A. Ja. Er worden kosten in rekening gebracht voor een verloren of beschadigd apparaat. Deze kosten zijn opgenomen op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/storage/databox/) en in de [product voorwaarden van de service](https://www.microsoft.com/licensing/product-licensing/products).


## <a name="order-device"></a>Apparaat bestellen

### <a name="q-how-do-i-get-data-box"></a>V. Hoe kan ik Data Box krijgen? 
A.  Meld u aan bij de Azure Portal en maak een bestelling voor Data Box om Azure Data Box te ontvangen. Geef uw contactgegevens en overige informatie op. Zodra u een bestelling hebt geplaatst, wordt Data Box op basis van beschikbaarheid binnen 10 dagen verzonden. Ga voor meer informatie naar [Een Data Box bestellen](data-box-deploy-ordered.md).

### <a name="q-i-couldnt-create-a-data-box-order-in-the-azure-portal-why"></a>V. Ik kan geen Data Box volgorde maken in de Azure Portal. Waarom?
A. Als u geen Data Box order kunt maken, is er een probleem met het type abonnement of de toegang.

Raadpleeg uw abonnement. Data Box is alleen beschikbaar voor EA- en CSP-abonnementen (Enterprise Agreement en Cloud Solution Provider). Als u niet een van deze typen abonnementen hebt, neemt u contact op met Microsoft Ondersteuning om uw abonnement bij te werken.

Controleer het toegangsniveau van uw abonnement als u een ondersteund type aanbieding voor het abonnement hebt. U moet inzender of eigenaar van een abonnement zijn om een bestelling te kunnen maken.

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>V. Hoe lang duurt mijn bestelling uit het maken van een order naar gegevens die zijn geüpload naar Azure?

A. Met de volgende geschatte lever tijden voor elke fase van de order verwerking krijgt u een goed beeld van wat u kunt verwachten.  

Deze lever tijden zijn *schattingen*. De tijd voor elke fase van de order verwerking wordt beïnvloed door de belasting van het Data Center, gelijktijdige orders en andere omgevings omstandigheden.

**Geschatte lever tijden voor een Data Box order:**

1. Volg orde Data Box: een paar minuten, vanuit de portal
2. Toewijzing en voor bereiding van apparaten: 1-2 werk dagen, afhankelijk van de beschik baarheid van de voor Raad en andere bestellingen die in behandeling zijn
3. Verzending: 2-3 werkdagen
4. Gegevens kopie op de site van de klant: is afhankelijk van de aard van de gegevens, de grootte en het aantal bestanden
5. Retourzending: 2-3 werkdagen
6. Apparaat verwerken bij Data Center: 1-2 werk dagen, afhankelijk van andere orders die in behandeling zijn
7. Gegevens uploaden naar Azure: begint zodra de verwerking is voltooid en het apparaat is verbonden. De upload tijd is afhankelijk van de aard van de gegevens, de grootte en het aantal bestanden.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-cant-create-any-additional-orders-why"></a>V. Ik heb een aantal Data Box-apparaten besteld. Ik kan geen verdere orders maken. Waarom?
A. We hebben een maximum van vijf actieve orders per abonnement per commerce-grens toegestaan (een combi natie van land en regio geselecteerd). Neem contact op met Microsoft Ondersteuning om de limiet voor uw abonnement te verhogen als u een extra apparaat moet bestellen.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>V. Als ik een bestelling probeer te maken, ontvang ik een melding dat de Data Box-service niet beschikbaar is. Wat betekent dit?
A. De Data Box-Service is niet beschikbaar voor de combi natie van land en regio die u hebt geselecteerd. Waarschijnlijk is de Data Box-service wel beschikbaar als u deze combinatie wijzigt. Ga naar [Regionale beschikbaarheid voor Data Box](data-box-overview.md#region-availability) voor een overzicht van de regio’s waarin de service beschikbaar is.

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>V. Ik heb mijn Data Box-bestelling een paar dagen geleden geplaatst. Wanneer ontvang ik mijn Data Box?
A. Als u een bestelling plaatst, controleren we of er een apparaat beschikbaar is voor uw bestelling. Als het apparaat beschikbaar is, verzenden we het binnen 10 dagen. Mogelijk is er in bepaalde perioden een hogere vraag. Als dat het geval is, wordt uw bestelling in de wachtrij geplaatst en kunt u de statuswijziging volgen in de Azure-portal. De bestelling wordt automatisch geannuleerd als uw bestelling niet binnen 90 dagen wordt geleverd.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>V. Ik heb mijn Data Box met gegevens opgevuld en moet er een andere best Ellen. Kan ik dat op een snelle manier doen?
A. U kunt een kloon maken van uw vorige bestelling. Hierdoor maakt u dezelfde bestelling als eerst en kunt u de details van de bestelling bewerken zonder dat u opnieuw uw adres, contactgegevens en meldingsinformatie hoeft te typen. Klonen is alleen toegestaan voor import orders.

## <a name="configure-and-connect"></a>Configureren en verbinding maken

### <a name="q-how-do-i-unlock-the-data-box"></a>V. Hoe kan ik de Data Box ontgrendelen? 
A.  Ga in Azure Portal naar uw Data Box-bestelling en ga vervolgens naar **Apparaatdetails**. Kopieer het ontgrendelingswachtwoord. Gebruik dit wachtwoord om u aan te melden bij de lokale webgebruikersinterface van uw Data Box. Ga voor meer informatie naar [Zelfstudie: uw Azure Data Box uitpakken, aansluiten en verbinden](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>V. Kan ik een Linux-hostcomputer gebruiken om verbinding te maken met de gegevens en deze naar de Data Box te kopiëren?
A.  Ja. U kunt de Data Box gebruiken om verbinding te maken met SMB- en NFS-clients. Ga naar de lijst met [ondersteunde besturingssystemen](data-box-system-requirements.md) voor uw hostcomputer voor meer informatie.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>V. Mijn Data Box is verzonden, maar ik wil de bestelling annuleren. Waarom is er geen knop Annuleren?
A.  U kunt de bestelling alleen annuleren nadat de Data Box is besteld en voordat de bestelling wordt verwerkt. U kunt de bestelling van de Data Box niet meer annuleren nadat deze is verwerkt. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>V. Kan ik een Data Box met meerdere hostcomputers tegelijk verbinden om gegevens over te dragen?
A. Ja. Er kunnen meerdere hostcomputers verbinding met een Data Box maken om gegevens over te dragen en er kunnen meerdere kopieertaken parallel worden uitgevoerd. Ga voor meer informatie naar [Zelfstudie: Gegevens naar Azure Data Box kopiëren](data-box-deploy-copy-data.md).

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>V. Kan ik verbinding maken met beide 10 GbE-interfaces op de Data Box om gegevens over te dragen ?
A. Ja. Beide 10 GbE-interfaces kunnen worden aangesloten op de Data Box om gegevens tegelijkertijd te kopiëren. Ga voor meer informatie over het kopiëren van gegevens naar de [zelf studie: gegevens kopiëren naar Azure data Box](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why?
A.

### Q. I could not set up Data Box using a Static IP address. Why?
A.

### Q. I could not set up Data Box on a private network. Why?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>V. Het ledcontrolelampje voor systeemstoringen op het voorpaneel brandt. Wat moet ik doen?
A. Er zijn twee LED-lichten onder de aan/uit-knop aan de voor kant van een Data Box. Het onderste lampje is de systeem fout indicator die aangeeft of uw systeem in orde is.

Een systeem fout indicator heeft de status rood kan duiden op een van de volgende problemen:
- Fout met ventilator
- CPU-Tempe ratuur hoog
- De Tempe ratuur van het moeder bord is hoog
- Fout bij ECC (dual inline geheugen module) fout bij het maken van verbinding met de code

Voer de volgende stappen uit:
1. Controleer of de ventilator werkt.
2. Verplaats het apparaat naar een locatie met een grotere lucht stroom.

Als het lampje van de systeem fout zich nog steeds bevindt, [neemt u contact op met Microsoft ondersteuning](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why"></a>V. Ik heb geen toegang tot het ontgrendelingswachtwoord voor de Data Box in de Azure Portal. Waarom?
A. Als u geen toegang hebt tot het wacht woord voor ontgrendelen in de Azure Portal, controleert u de machtigingen voor uw abonnement en opslag account. Zorg dat u inzender- of eigenaarsmachtigingen op resourcegroepniveau hebt. U moet ten minste Data Box operator machtiging hebben om de toegangs referenties te bekijken.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>V. Wordt poortkanaalconfiguratie ondersteund in Data Box? En MPIO?
A. Er wordt geen ondersteuning geboden voor poort kanaal configuratie, MPIO-configuratie (Multipath IO) of vLAN-configuratie op Data Box.

## <a name="track-status"></a>Status bijhouden

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>V. Hoe kan ik de bestelstatus van de Data Box volgen vanaf het moment dat de bestelling is geplaatst als ik het apparaat terug wil sturen? 
A.  U kunt de status van de Data Box-bestelling volgen in Azure Portal. Wanneer u de order maakt, wordt u gevraagd een e-mail melding op te geven. Als u er een hebt gegeven, ontvangt u een melding via e-mail van alle status wijzigingen voor de order. Meer informatie over het [configureren van e-mailmeldingen](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-device"></a>V. Hoe kan ik het apparaat terugsturen? 
A.  Microsoft geeft een verzendlabel weer op het E-ink-display. Als het verzend label niet op de E-inkt wordt weer gegeven, gaat u naar **overzicht > down load shipping label**. U kunt het label downloaden en afdrukken, het label invoegen in de markering plastic op het apparaat en het apparaat op de locatie van de vervoerder neerzetten. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>V. Ik heb een e-mailbericht ontvangen dat mijn apparaat het Azure-datacenter heeft bereikt. Hoe kan ik weten of de gegevens worden geüpload?
A. U kunt in de Azure Portal naar uw Data Box-bestelling gaan en dan **Overzicht** kiezen. Als het uploaden van gegevens naar Azure is gestart, zult u de voortgang van het kopiëren in het rechterdeelvenster zien. 

## <a name="migrate-data"></a>Gegevens migreren

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>V. Wat is de maximale hoeveelheid gegevens die voor Data Box kan worden gebruikt?  
A.  Data Box heeft een bruikbare opslagcapaciteit van 80 TB. U kunt een Data Box-apparaat gebruiken voor gegevens met een grootte van 40 tot 80 TB. Voor grotere hoeveelheden gegevens tot 500 TB kunt u meerdere Data Box-apparaten bestellen. Meld u aan voor Data Box Heavy voor gegevens die groter zijn dan 500 TB.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>V. Wat zijn de maximale groottes voor blok-blob en pagina-blob die voor Data Box worden ondersteund? 
A.  De maximale groottes worden bepaald door Azure Storage-limieten. De maximale blok-blob is ongeveer 4,768 TiB en de maximale blobgrootte is 8 TiB. Zie [Schaalbaarheids- en prestatiedoelen voor blob-opslag](../storage/blobs/scalability-targets.md) voor meer informatie.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>V. Hoe weet ik of mijn gegevens veilig zijn tijdens de overdracht? 
A. Er zijn meerdere beveiligingsfuncties geïmplementeerd om te zorgen dat uw Data Box veilig is tijdens het verzenden. Sommige daarvan zijn verzegeling, detectie van hardware- en softwaremanipulatie en een wachtwoord om het apparaat te ontgrendelen. Ga naar [Azure Data Box-beveiliging en -gegevensbescherming](data-box-security.md) voor meer informatie.

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>V. Hoe kan ik de gegevens naar de Data Box kopiëren? 
A.  Als u een SMB-client gebruikt, kunt u een SMB Copy-hulp programma zoals `Robocopy` , `Diskboss` of zelfs Windows Verkenner-en-drop gebruiken om gegevens te kopiëren naar het apparaat. 

Als u een NFS-client gebruikt, kunt u [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) of [Ultracopier](https://ultracopier.first-world.info/) gebruiken. 

Ga voor meer informatie naar [Zelfstudie: Gegevens naar Azure Data Box kopiëren](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>V. Zijn er tips om het kopiëren van gegevens te versnellen?
A.  U kunt het kopieerproces als volgt versnellen:

- Gebruik meerdere gegevensstromen. `Robocopy`Gebruik bijvoorbeeld de multi-threaded optie. Ga naar [Tutorial: Copy data to Azure Data Box and verify](data-box-deploy-copy-data.md) (Zelfstudie: Gegevens naar Azure Data Box kopiëren en verifiëren) voor meer informatie over de opdracht die u moet gebruiken.
- Gebruik meerdere sessies.
- In plaats van te kopiëren over een netwerk share (waarbij netwerk snelheid de Kopieer snelheid kan beperken), slaat u de gegevens lokaal op de computer op waarop de Data Box is aangesloten.
- Voer een benchmark-test uit van de prestaties van de computer die wordt gebruikt om de gegevens te kopiëren. Down load en gebruik het [ `Bluestop` FIO-hulp programma](https://ci.appveyor.com/project/axboe/fio) om de prestaties van de serverhardware te benchmarkren. Selecteer de nieuwste versie van x86 of x64, selecteer het tabblad **artefacten** en down load het MSI-bestand.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM's disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>V. Kan ik meerdere opslagaccounts gebruiken voor Data Box?
A.  Ja. Data Box ondersteunt maximaal 10 opslagaccounts, algemene opslag, klassieke opslag en blob-opslag. Zowel dynamische als statische blob wordt ondersteund.


## <a name="ship-device"></a>Apparaat verzenden

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>V. Mijn apparaat is bezorgd, maar het apparaat lijkt beschadigd te zijn. Wat moet ik doen?
A. Gebruik het apparaat niet als het apparaat is beschadigd of geknoeid is. [Neem contact op met Microsoft ondersteuning](data-box-disk-contact-microsoft-support.md) en retour neer het apparaat zo snel mogelijk. U kunt ook een nieuwe Data Box-bestelling maken voor een vervangend apparaat. In dit geval worden er geen kosten in rekening gebracht voor het vervangende apparaat.

### <a name="q-can-i-pick-up-my-data-box-order-myself-can-i-return-the-data-box-via-a-carrier-that-i-choose"></a>V. Kan ik mijn Data Box bestelling zelf ophalen? Kan ik de Data Box retour neren via een door mij gekozen provider?
A. Ja. Micro soft biedt ook zelf-beheerde verzen ding. Wanneer u de Data Box order plaatst, kunt u de optie voor zelf-beheerde verzen ding kiezen. Zie [self managed Shipping for data Box](data-box-portal-customer-managed-shipping.md)voor meer informatie.

### <a name="q-will-my-data-box-devices-cross-countryregion-borders-during-shipping"></a>V. Worden er tijdens de verzen ding meerdere land-en regio grenzen door mijn Data Box apparaten?
A. Alle Data Box apparaten worden vanuit hetzelfde land/dezelfde regio als de bestemming verzonden en passeren geen internationale grenzen. De enige uitzonde ring hierop is voor orders in de Europese Unie (EU), waar apparaten kunnen worden verzonden naar en naar elk land/regio in de EU. Dit geldt voor zowel de Data Box als de Data Box Heavy-apparaten.

### <a name="q-i-ordered-a-data-box-in-us-east-but-i-received-a-device-that-was-shipped-from-a-location-in-us-west-where-should-i-return-the-device-to"></a>V. Ik heb een Data Box in VS Oost besteld, maar ik heb een apparaat ontvangen dat is verzonden vanaf een locatie in VS West. Waar moet ik het apparaat retour neren?
A. We proberen zo snel mogelijk een Data Box apparaat aan u te krijgen. We geven een prioriteit aan de verzen ding van een Data Center dat het dichtst bij de locatie van uw opslag account ligt, maar verzendt een apparaat vanuit elk Azure-Data Center dat beschik bare inventaris. Uw Data Box moet worden teruggestuurd naar dezelfde locatie waar deze is verzonden, zoals wordt weer gegeven in het verzend label.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>V. Er wordt geen label voor retourzending weergegeven op het E-ink-display. Wat moet ik doen?
A. Als in het E-inkt-weer gave het label retour zending niet wordt weer gegeven, voert u de volgende stappen uit:
- Verwijder het oude verzendlabel en alle stickers van de vorige verzending.
- Ga naar uw bestelling in de Azure-portal. Ga naar **overzicht** en **down load het verzend label**. Ga voor meer informatie naar [Verzendlabel downloaden](data-box-portal-admin.md#download-shipping-label).
- Print het verzendlabel en steek het in het doorzichtige plastic hoesje op het apparaat. 
- Zorg dat het verzendlabel duidelijk zichtbaar is. 

### <a name="q-how-is-my-data-protected-during-transit"></a>V. Hoe worden mijn gegevens tijdens het verzenden beveiligd? 
A.  Tijdens het transport helpen de volgende functies van de Data Box de gegevens beschermen.
 - De Data Box-schijven zijn versleuteld met AES-256-versleuteling. 
 - Het apparaat is vergrendeld en er is een ontgrendelingswachtwoord nodig om toegang te krijgen tot gegevens.
Ga voor meer informatie naar [Data Box-beveiligingsfuncties](data-box-security.md).  

### <a name="q-i-have-finished-prepare-to-ship-for-my-import-order-and-shut-down-the-device-can-i-still-add-more-data-to-the-data-box"></a>V. Ik ben klaar met het voorbereiden op verzen ding voor mijn import order en het apparaat af te sluiten. Kan ik nog steeds meer gegevens toevoegen aan de Data Box?
A. Ja. U kunt het apparaat inschakelen en meer gegevens toevoegen. Als u het kopiëren van gegevens hebt voltooid, moet u **Voorbereiding voor verzending** opnieuw uitvoeren.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>V. Ik heb mijn apparaat ontvangen en ik kan het niet opstarten. Hoe stuur ik het apparaat terug?
A. Als uw apparaat niet wordt opgestart, gaat u naar uw bestelling in het Azure Portal. Down load een verzend label en koppel dit aan het apparaat. Ga voor meer informatie naar [Verzendlabel downloaden](data-box-portal-admin.md#download-shipping-label).

## <a name="verify-and-upload"></a>Verifiëren en uploaden

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>V. Hoe snel krijg ik toegang tot mijn gegevens in azure nadat ik de Data Box weer heb verzonden? 
A.  Zodra de status van de bestelling voor het **kopiëren van gegevens** wordt weer gegeven als **voltooid**, kunt u direct toegang krijgen tot uw gegevens.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>V. Waar bevinden zich mijn gegevens in Azure na het uploaden?
A.  Wanneer u de gegevens naar Data Box kopieert, afhankelijk van het feit of de gegevens blok-BLOB of pagina-BLOB of Azure files zijn, worden de gegevens geüpload naar een van de volgende paden in uw Azure Storage-account:
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  U kunt ook naar uw Azure Storage-account gaan in de Azure Portal en daar naartoe navigeren.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>V. Ik heb gemerkt dat ik de naamgevingsvereisten voor Azure niet voor mijn containers heb gevolgd. Worden mijn gegevens niet naar Azure geüpload?
A.  Als de namen van de containers hoofd letters bevatten, worden deze namen automatisch omgezet in kleine letters. Als de namen anderszins niet aan de vereisten voldoen (speciale tekens, andere taal, enzovoort), dan worden er geen gegevens geüpload. Ga voor meer informatie over het benoemen van shares, containers en bestanden naar:
- [Naamgeving van en verwijzing naar shares](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Conventies voor blok-blobs en pagina-blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>V. Hoe kan ik de gegevens controleren die naar Data Box zijn gekopieerd?
A.  Uw gegevens worden gevalideerd als u **Voorbereiding voor verzending** uitvoert nadat het kopiëren van gegevens is voltooid. Data Box genereert tijdens het validatieproces een lijst met bestanden en controlesommen voor de gegevens. U kunt de lijst met bestanden downloaden en de lijst verifiëren met de bestanden in de brongegevens. Ga voor meer informatie naar [Voorbereiding voor verzending](data-box-deploy-picked-up.md#prepare-to-ship).

### <a name="q-what-happens-to-my-data-after-i-return-the-data-box"></a>V. Wat gebeurt er met mijn gegevens nadat ik de Data Box retour?
A.  Als de gegevens naar Azure zijn gekopieerd, worden de gegevens van op de Data Box veilig gewist volgens de richtlijnen van NIST SP 800-88 Revision 1. Ga voor meer informatie naar [Gegevens verwijderen uit de Data Box](data-box-deploy-picked-up.md#erasure-of-data-from-data-box).

## <a name="audit-report"></a>Auditrapport

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Hoe helpt de Azure Data Box-service in de procedure voor de bewakingsketen van klanten?
A.  De Azure Data Box-service biedt systeemeigen rapporten die u kunt gebruiken voor de documentatie van uw bewakingsketen. De controle- en kopielogboeken zijn beschikbaar in uw opslagaccount in Azure. U kunt [de bestelgeschiedenis downloaden](data-box-portal-admin.md#download-order-history) via de Azure-portal nadat de bestelling is voltooid.


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Wat voor soort rapporten is er beschikbaar ter ondersteuning van de bewakingsketen?
A.  De volgende soorten rapporten zijn beschikbaar ter ondersteuning van de bewakingsketen:

- Transport door UPS.
- Registratie van inschakeling en toegang tot de share van gebruikers.
- Stuk lijst of manifest bestand met een 64-bits cyclische redundantie controle (CRC-64) of controlesom voor elk bestand dat is opgenomen in de Data Box.
- Rapportage van bestanden die niet naar het Azure-opslagaccount konden worden geüpload.
- Opschoning van het Data Box-apparaat (volgens de norm NIST 800 88R1) nadat gegevens naar uw Azure-opslagaccount zijn gekopieerd.

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Zijn de logboeken voor tracering van de koerier (van UPS) beschikbaar? 
A.  De logboeken voor tracering van de koerier worden vastgelegd in de bestelgeschiedenis van Data Box. Dit rapport is beschikbaar nadat het apparaat naar het Azure-datacenter is geretourneerd en de gegevens op de schijven van het apparaat zijn opgeschoond. Voor onmiddellijke behoefte kunt u ook rechtstreeks naar de website van de vervoerder gaan met het order tracking nummer en de tracerings gegevens ophalen.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Kan ik de Data Box naar het Azure-datacenter vervoeren? 
A.  Nee. Als u micro soft Managed Shipping hebt gekozen, kunt u de gegevens niet transporteren. Op dit moment accepteert Azure Data Center geen levering van de Data Box van klanten of andere vervoerders dan UPS.

Als u zelf beheerde verzen ding hebt gekozen, kunt u uw Data Box uit het Azure-Data Center ophalen of verwijderen.


## <a name="next-steps"></a>Volgende stappen

- De [Systeemvereisten voor Data Box](data-box-system-requirements.md) lezen.
- Informatie over de [Limieten voor Data Box](data-box-limits.md).
- [Azure Data Box](data-box-quickstart-portal.md) snel implementeren in de Azure-portal.