---
title: Veelgestelde vragen over Azure import/export-service | Microsoft Docs
description: Lees de antwoorden op veelgestelde vragen over de export service van Azure import.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: be6c48efc77880addf814b1609d4a371c7c5c73b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98706484"
---
# <a name="azure-importexport-service-frequently-asked-questions"></a>Azure import/export-service: veelgestelde vragen

Hieronder vindt u vragen en antwoorden die u mogelijk hebt wanneer u uw Azure import/export-service gebruikt om gegevens over te dragen naar Azure Storage. De vragen en antwoorden zijn in de volgende categorieën onderverdeeld:

- Over import/export-service
- De schijven voorbereiden voor importeren/exporteren
- Import/export-taken
- Verzend schijven
- Diversen

## <a name="about-importexport-service"></a>Over import/export-service

### <a name="can-i-copy-azure-file-storage-using-the-azure-importexport-service"></a>Kan ik Azure File Storage kopiëren met behulp van de Azure import/export-service?

Ja. De Azure import/export-service ondersteunt importeren in azure File Storage. Het exporteren van Azure Files op dit moment wordt niet ondersteund.

### <a name="is-the-azure-importexport-service-available-for-csp-subscriptions"></a>Is de Azure import/export-service beschikbaar voor CSP-abonnementen?

Ja. Azure import/export-service ondersteunt Cloud Solution Providers (CSP)-abonnementen.

### <a name="can-i-use-the-azure-importexport-service-to-copy-pst-mailboxes-and-sharepoint-data-to-microsoft-365"></a>Kan ik de Azure import/export-service gebruiken om PST-post vakken en share point-gegevens te kopiëren naar Microsoft 365?

Ja. Ga voor meer informatie naar [overzicht van het importeren van de PST-bestanden van uw organisatie](/microsoft-365/compliance/importing-pst-files-to-office-365).

### <a name="can-i-use-the-azure-importexport-service-to-copy-my-backups-offline-to-the-azure-backup-service"></a>Kan ik de Azure import/export-service gebruiken om mijn back-ups offline te kopiëren naar de Azure Backup-Service?

Ja. Ga naar de [werk stroom offline back-up in azure backup](../backup/backup-azure-backup-import-export.md)voor meer informatie.

### <a name="can-i-purchase-drives-for-importexport-jobs-from-microsoft"></a>Kan ik stations kopen voor import/export-taken van micro soft?

Nee. U moet uw eigen schijven verzenden voor het importeren en exporteren van taken.

## <a name="preparing-disks-for-importexport"></a>Schijven voorbereiden voor importeren/exporteren

### <a name="can-i-skip-the-drive-preparation-step-for-an-import-job-can-i-prepare-a-drive-without-copying"></a>Kan ik de stap voor bereiding van het station voor een import taak overs Laan? Kan ik een station voorbereiden zonder te kopiëren?

Nee. Elk station dat wordt gebruikt voor het importeren van gegevens moet worden voor bereid met behulp van het Azure WAImportExport-hulp programma. Gebruik het hulp programma om ook gegevens naar het station te kopiëren.

### <a name="do-i-need-to-perform-any-disk-preparation-when-creating-an-export-job"></a>Moet ik schijf voorbereiding uitvoeren bij het maken van een export taak?

Nee. Sommige voor spellingen worden aanbevolen. Als u het aantal vereiste schijven wilt controleren, gebruikt u de opdracht PreviewExport van het WAImportExport-hulp programma. Zie voor meer informatie [het voor beeld van het gebruik van een schijf voor een export taak bekijken](/previous-versions/azure/storage/common/storage-import-export-tool-previewing-drive-usage-export-v1). Met deze opdracht kunt u het gebruik van het station voor de geselecteerde blobs bekijken op basis van de grootte van de stations die u gaat gebruiken. Controleer ook of u kunt lezen uit en schrijven naar de vaste schijf die voor de export taak wordt verzonden.

## <a name="importexport-jobs"></a>Import/export-taken

### <a name="can-i-cancel-my-job"></a>Kan ik mijn taak annuleren?

Ja. U kunt een taak annuleren wanneer de status het **maken** of **verzenden** heeft. Na deze fasen kan de taak niet worden geannuleerd en wordt deze tot de laatste fase voortgezet.

### <a name="how-long-can-i-view-the-status-of-completed-jobs-in-the-azure-portal"></a>Hoe lang kan ik de status van voltooide taken in het Azure Portal weer geven?

U kunt de status voor voltooide taken weer geven tot 90 dagen. Voltooide taken worden na 90 dagen verwijderd.

### <a name="if-i-want-to-import-or-export-more-than-10-drives-what-should-i-do"></a>Wat moet ik doen als ik meer dan 10 stations wil importeren of exporteren?

Een import-of export taak kan verwijzen naar slechts 10 schijven in één taak. Als u meer dan 10 schijven wilt verzenden, moet u meerdere taken maken. Schijven die zijn gekoppeld aan dezelfde taak, moeten samen in hetzelfde pakket worden verzonden.
Neem contact op met Microsoft Ondersteuning voor meer informatie en richt lijnen wanneer de gegevens capaciteit meerdere taken voor het importeren van schijven omvat.

### <a name="the-uploaded-blob-shows-status-as-lease-expired-what-should-i-do"></a>De geüploade BLOB toont de status "Lease verlopen". Wat moet ik doen?

U kunt het veld Lease verlopen negeren. Bij het importeren/exporteren wordt tijdens het uploaden lease voor de BLOB gebruikt om ervoor te zorgen dat er geen ander proces is om de BLOB parallel bij te werken. De lease is verlopen houdt in dat import/export niet meer wordt geüpload naar deze en dat de BLOB beschikbaar is voor uw gebruik.

## <a name="shipping-disks"></a>Verzend schijven

### <a name="what-is-the-maximum-number-of-hdd-for-in-one-shipment"></a>Wat is het maximum aantal harde schijven voor in één zending?

Er is geen limiet voor het aantal Hdd's in één verzen ding. Als de schijven tot meerdere taken behoren, raden we u aan:

- Voorzie de schijven van de bijbehorende taak namen.
- werk de taken bij met een tracerings nummer dat is geachtervoegseld met-1,-2, enzovoort.

### <a name="should-i-include-anything-other-than-the-hdd-in-my-package"></a>Moet ik iets anders dan de HDD in mijn pakket toevoegen?

Alleen uw harde schijven in het verzend pakket verzenden. Neem geen items op zoals voedings kabels of USB-kabels.

### <a name="do-i-have-to-ship-my-drives-using-fedex-or-dhl"></a>Moet ik mijn stations verzenden met FedEx of DHL?

U kunt stations verzenden naar het Azure-Data Center met behulp van een bekende Carrier zoals FedEx, DHL, UPS of US Postal Service. Voor retour zending van stations naar u in het Data Center moet u echter het volgende opgeven:

- Een FedEx-account nummer in de Verenigde Staten en de EU, of
- Een DHL-account nummer in de regio's Azië en Australië.

> [!NOTE]
> De data centers in India vereisen een declaratie brief in uw brief hoofd (Delivery Challan) om de stations te retour neren. Als u de vereiste vermelding wilt ordenen, moet u ook de ophaal bewerking met de geselecteerde transporteur boeken en de gegevens met het Data Center delen.

### <a name="are-there-any-restrictions-with-shipping-and-returning-my-drive-internationally"></a>Zijn er beperkingen met het verzenden en retour neren van mijn station internationaal?

Houd er rekening mee dat de fysieke media die u verzendt mogelijk internationale grenzen moeten passeren. U bent verantwoordelijk om ervoor te zorgen dat uw fysieke media en gegevens worden geïmporteerd en/of geëxporteerd overeenkomstig de toepasselijke wetgeving. Voordat u de fysieke media verzendt, raadpleegt u uw adviseurs om te controleren of uw media en gegevens juridisch kunnen worden verzonden naar het geïdentificeerde Data Center. Zo kunt u ervoor zorgen dat micro soft tijdig op de tijd komt.

Nadat het uploaden is voltooid, kan het proces om een of meer stations te retour neren naar een internationaal adres langer duren dan de typische 2-3 dagen die nodig zijn voor lokale verzen ding. Tijdens de fase die wordt vermeld in de Azure Portal als verpakking, garandeert het Data Box team dat de juiste documentatie wordt verstrekt om te garanderen dat de verzen ding voldoet aan de verschillende internationale vereisten voor importeren en exporteren.

### <a name="are-there-any-special-requirements-for-delivering-my-disks-to-a-datacenter"></a>Zijn er speciale vereisten voor het leveren van mijn schijven aan een Data Center?

De vereisten zijn afhankelijk van de specifieke beperkingen van het Azure-Data Center.

- Er zijn enkele sites, zoals Australië, Duitsland en UK-zuid, waarvoor een inkomend ID-nummer van micro soft Data Center moet worden geschreven om veiligheids redenen. Voordat u uw stations of schijven naar het Data Center verzendt, neemt u contact op met Azure DataBox Operations ( adbops@microsoft.com ) om dit aantal op te halen. Zonder dit nummer wordt het pakket geweigerd.
- De data centers in India vereisen de persoonlijke details van het stuur programma, zoals de Government ID-kaart of het bewijs nummer. (bijvoorbeeld PAN, AADHAR, DL), naam, contact en het nummer van de auto plaat om een doorgifte van de poort vermelding te verkrijgen. Informeer uw telecom bedrijf over deze vereisten om de bezorgings vertraging te voor komen.

### <a name="when-creating-a-job-the-shipping-address-is-a-location-that-is-different-from-my-storage-account-location-what-should-i-do"></a>Bij het maken van een taak is het verzend adres een andere locatie dan de locatie van mijn opslag account. Wat moet ik doen?

Sommige locaties van opslag accounts worden toegewezen aan alternatieve verzend locaties. Eerder beschik bare verzend locaties kunnen ook tijdelijk worden toegewezen aan alternatieve locaties. Controleer altijd het verzend adres dat wordt vermeld tijdens het maken van de taak voordat u uw stations verzendt.

### <a name="when-shipping-my-drive-the-carrier-asks-for-the-data-center-contact-address-and-phone-number-what-should-i-provide"></a>Bij het verzenden van mijn station vraagt de provider om het adres en telefoon nummer van de contact persoon van het Data Center. Wat moet ik bieden?

Het telefoon nummer en het DC-adres zijn beschikbaar als onderdeel van het maken van een taak.

## <a name="miscellaneous"></a>Diversen

### <a name="what-happens-if-i-accidentally-send-an-hdd-that-does-not-conform-to-the-supported-requirements"></a>Wat gebeurt er als ik per ongeluk een HDD verzend die niet voldoet aan de ondersteunde vereisten?

Het Azure-Data Center retourneert de schijf die niet voldoet aan de ondersteunde vereisten voor u. Als slechts een deel van de stations in het pakket voldoet aan de ondersteunings vereisten, worden die stations verwerkt en worden de stations die niet aan de vereisten voldoen, naar u teruggestuurd.

### <a name="does-the-service-format-the-drives-before-returning-them"></a>Formatteert de service de stations voordat ze worden geretourneerd?

Nee. Alle stations zijn versleuteld met BitLocker.

### <a name="how-can-i-access-data-that-is-imported-by-this-service"></a>Hoe kan ik toegang krijgen tot gegevens die door deze service worden geïmporteerd?

Gebruik de Azure Portal of [Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) om toegang te krijgen tot de gegevens in uw Azure Storage-account.  

### <a name="after-the-import-is-complete-what-does-my-data-look-like-in-the-storage-account-is-my-directory-hierarchy-preserved"></a>Waar worden mijn gegevens weer gegeven in het opslag account nadat het importeren is voltooid? Is mijn Directory-hiërarchie behouden?

Wanneer u een harde schijf voor een import taak voorbereidt, wordt het doel opgegeven door het veld DstBlobPathOrPrefix in het CSV-bestand van de gegevensset. Dit is de doel container in het opslag account waarnaar de gegevens van de harde schijf worden gekopieerd. Binnen deze doel container worden virtuele mappen gemaakt voor mappen van de harde schijf en worden er blobs gemaakt voor bestanden.

### <a name="if-a-drive-has-files-that-already-exist-in-my-storage-account-does-the-service-overwrite-existing-blobs-or-files"></a>Als een station bestanden bevat die al bestaan in mijn opslag account, worden bestaande blobs of bestanden overschreven door de service?

Is afhankelijk van. Wanneer u het station voorbereidt, kunt u opgeven of de doel bestanden moeten worden overschreven of moeten worden genegeerd met behulp van het veld in het CSV-bestand van de gegevensset met de naam deposition: <NaamWijzigen | zonder overschrijven | overschrijven>. De naam van de nieuwe bestanden wordt standaard door de service gewijzigd in plaats van bestaande blobs of bestanden te overschrijven.

### <a name="is-the-waimportexport-tool-compatible-with-32-bit-operating-systems"></a>Is het WAImportExport-hulp programma compatibel met 32-bits besturings systemen?

Nee. Het hulp programma WAImportExport is alleen compatibel met 64-bits Windows-besturings systemen. Ga naar [ondersteunde besturings systemen](./storage-import-export-requirements.md)voor een volledige lijst met ondersteunde versies van het besturings systeem.

### <a name="what-is-the-maximum-block-blob-and-page-blob-size-supported-by-azure-importexport"></a>Wat is de maximum grootte van een blok-Blob en een pagina-blob die wordt ondersteund door Azure import/export?

- De maximale grootte van een blok-blob is ongeveer 4.768 TB of 5.000.000 MB.
- De maximale grootte van de pagina-blob is 8TB.

### <a name="does-azure-importexport-support-aes-256-encryption"></a>Ondersteunt Azure import/export AES-256-versleuteling?

Ja. Azure import/export-service maakt gebruik van AES-256 BitLocker-versleuteling.

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure Import/Export?](storage-import-export-service.md)