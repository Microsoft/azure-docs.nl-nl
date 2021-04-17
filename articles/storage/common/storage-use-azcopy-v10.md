---
title: Gegevens kopiëren of verplaatsen naar Azure Storage met behulp van AzCopy v10 | Microsoft Docs
description: AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om gegevens te kopiëren naar, van of tussen opslagaccounts. Dit artikel helpt u bij het downloaden van AzCopy, het verbinden met uw opslag account en het overdragen van bestanden.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 34d3bd45d2c0bf0260a4f8524cff6f8ac03b746c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501714"
---
# <a name="get-started-with-azcopy"></a>Aan de slag met AzCopy

AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om blobs of bestanden vanuit of naar een opslagaccount te kopiëren. Dit artikel helpt u bij het downloaden van AzCopy, het verbinden met uw opslag account en het overdragen van bestanden.

> [!NOTE]
> AzCopy **V10** is de momenteel ondersteunde versie van AzCopy.
>
> Als u een eerdere versie van AzCopy wilt gebruiken, zie dan de sectie De vorige versie van [AzCopy](#previous-version) gebruiken van dit artikel.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>AzCopy downloaden

Download eerst het uitvoerbare AzCopy V10-bestand naar een map op uw computer. AzCopy V10 is slechts een uitvoerbaar bestand, dus u kunt niets installeren.

- [Windows 64-bits](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bits](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Deze bestanden worden gecomprimeerd als een ZIP-bestand (Windows en Mac) of een TAR-bestand (Linux). Als u het TAR-bestand in Linux wilt downloaden en decomprimeren, bekijkt u de documentatie voor uw Linux-distributie.

> [!NOTE]
> Als u gegevens wilt kopiëren van en naar uw [Azure Table Storage-service,](../tables/table-storage-overview.md) installeert u [AzCopy versie 7.3.](https://aka.ms/downloadazcopynet)

## <a name="run-azcopy"></a>AzCopy uitvoeren

Voor het gemak kunt u de maplocatie van het uitvoerbare AzCopy-bestand toevoegen aan het systeempad. Op die manier kunt u `azcopy` vanuit elke map op uw systeem typen.

Als u ervoor kiest om de AzCopy-map niet toe te voegen aan uw pad, moet u de mappen wijzigen in de locatie van het uitvoerbare AzCopy-bestand en typt u of in Windows PowerShell `azcopy` opdrachtprompts. `.\azcopy`

Als eigenaar van uw Azure Storage account krijgt u niet automatisch machtigingen voor toegang tot gegevens. Voordat u iets zinvols kunt doen met AzCopy, moet u beslissen hoe u autorisatiereferenties aan de opslagservice verstrekt. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>AzCopy machtigen

U kunt autorisatiereferenties verstrekken met behulp van Azure Active Directory (AD) of met behulp van een Shared Access Signature (SAS)-token.

Gebruik deze tabel als richtlijn:

| Opslagtype | Momenteel ondersteunde autorisatiemethode |
|--|--|
|**Blob Storage** | Azure AD en SAS |
|**Blob Storage (hiërarchische naamruimte)** | Azure AD en SAS |
|**File Storage** | Alleen SAS |

#### <a name="option-1-use-azure-active-directory"></a>Optie 1: Azure Active Directory

Deze optie is alleen beschikbaar voor blob-opslag. Met behulp Azure Active Directory kunt u eenmaal referenties verstrekken in plaats van een SAS-token aan elke opdracht te moeten geven.  

> [!NOTE]
> Als u in de huidige release blobs wilt kopiëren tussen opslagaccounts, moet u een SAS-token toevoegen aan elke bron-URL. U kunt het SAS-token alleen weglaten uit de doel-URL. Zie Blobs kopiëren [tussen opslagaccounts voor voorbeelden.](#transfer-data)

Zie Toegang tot blobs machtigen met AzCopy en Azure Active Directory [(Azure AD)](storage-use-azcopy-authorize-azure-active-directory.md)om toegang te verlenen met behulp van Azure AD.

#### <a name="option-2-use-a-sas-token"></a>Optie 2: Een SAS-token gebruiken

U kunt een SAS-token toevoegen aan elke bron- of doel-URL die wordt gebruikt in uw AzCopy-opdrachten.

Met deze voorbeeldopdracht worden gegevens recursief gekopieerd van een lokale map naar een blobcontainer. Er wordt een fictief SAS-token toegevoegd aan het einde van de container-URL.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Zie Using [Shared Access Signatures (SAS) (Shared Access Signatures (SAS) gebruiken)](./storage-sas-overview.md)voor meer informatie over SAS-tokens en hoe u er een kunt verkrijgen.

> [!NOTE]
> De [instelling Veilige overdracht vereist](storage-require-secure-transfer.md) van een opslagaccount bepaalt of de verbinding met een opslagaccount wordt beveiligd met Transport Layer Security (TLS). Deze instelling is standaard ingeschakeld.   

<a id="transfer-data"></a>

## <a name="transfer-data"></a>Gegevens overdragen

Nadat u uw identiteit hebt geautoriseerd of een SAS-token hebt verkregen, kunt u beginnen met het overdragen van gegevens.

Zie een van deze artikelen voor voorbeeldopdrachten.

| Service | Artikel |
|--------|-----------|
|Azure Blob Storage|[Bestanden uploaden naar Azure Blob Storage](storage-use-azcopy-blobs-upload.md) |
|Azure Blob Storage|[Blobs downloaden van Azure Blob Storage](storage-use-azcopy-blobs-download.md)|
|Azure Blob Storage|[Blobs kopiëren tussen Azure-opslagaccounts](storage-use-azcopy-blobs-copy.md)|
|Azure Blob Storage|[Synchroniseren met Azure Blob Storage](storage-use-azcopy-blobs-synchronize.md)|
|Azure Files |[Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)|
|Amazon S3|[Gegevens kopiëren van Amazon S3 naar Azure Storage](storage-use-azcopy-s3.md)|
|Google Cloud Storage|[Gegevens kopiëren van Google Cloud Storage naar Azure Storage (preview)](storage-use-azcopy-google-cloud.md)|
|Azure Stack opslag|[Gegevens overdragen met AzCopy en Azure Stack opslag](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="get-command-help"></a>Hulp bij opdrachten krijgen

Als u een lijst met opdrachten wilt zien, `azcopy -h` typt u en drukt u op enter.

Als u meer wilt weten over een specifieke opdracht, moet u de naam van de opdracht opnemen (bijvoorbeeld: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Hulp bij Inline](media/storage-use-azcopy-v10/azcopy-inline-help.png)

### <a name="list-of-commands"></a>Lijst met opdrachten

De volgende tabel bevat alle AzCopy v10-opdrachten. Elke opdracht is een koppeling naar een naslagartikel. 

|Opdracht|Beschrijving|
|---|---|
|[azcopy bench](storage-ref-azcopy-bench.md?toc=/azure/storage/blobs/toc.json)|Voert een prestatiebenchmark uit door testgegevens te uploaden of te downloaden naar of van een opgegeven locatie.|
|[azcopy copy](storage-ref-azcopy-copy.md?toc=/azure/storage/blobs/toc.json)|Kopieert brongegevens naar een doellocatie|
|[azcopy doc](storage-ref-azcopy-doc.md?toc=/azure/storage/blobs/toc.json)|Genereert documentatie voor het hulpprogramma in Markdown-indeling.|
|[azcopy env](storage-ref-azcopy-env.md?toc=/azure/storage/blobs/toc.json)|Toont de omgevingsvariabelen die het gedrag van AzCopy kunnen configureren.|
|[azcopy jobs](storage-ref-azcopy-jobs.md?toc=/azure/storage/blobs/toc.json)|Subtaken met betrekking tot het beheren van taken.|
|[azcopy jobs clean](storage-ref-azcopy-jobs-clean.md?toc=/azure/storage/blobs/toc.json)|Verwijder alle logboek- en planbestanden voor alle taken.|
|[azcopy jobs list](storage-ref-azcopy-jobs-list.md?toc=/azure/storage/blobs/toc.json)|Geeft informatie weer over alle taken.|
|[azcopy jobs remove](storage-ref-azcopy-jobs-remove.md?toc=/azure/storage/blobs/toc.json)|Verwijder alle bestanden die zijn gekoppeld aan de opgegeven taak-id.|
|[azcopy jobs resume](storage-ref-azcopy-jobs-resume.md?toc=/azure/storage/blobs/toc.json)|Hervat de bestaande taak met de opgegeven taak-id.|
|[azcopy jobs show](storage-ref-azcopy-jobs-show.md?toc=/azure/storage/blobs/toc.json)|Geeft gedetailleerde informatie weer voor de opgegeven taak-id.|
|[azcopy load](storage-ref-azcopy-load.md)|Subcommands met betrekking tot het overdragen van gegevens in specifieke indelingen.|
|[azcopy load clfs](storage-ref-azcopy-load-avere-cloud-file-system.md?toc=/azure/storage/blobs/toc.json)|Brengt lokale gegevens over naar een container en slaat deze op in de CLFS-indeling (Avere Cloud FileSystem) van Microsoft.|
|[azcopy list](storage-ref-azcopy-list.md?toc=/azure/storage/blobs/toc.json)|Een lijst met de entiteiten in een bepaalde resource.|
|[azcopy login](storage-ref-azcopy-login.md?toc=/azure/storage/blobs/toc.json)|Meldt u aan bij Azure Active Directory toegang tot Azure Storage resources.|
|[azcopy logout](storage-ref-azcopy-logout.md?toc=/azure/storage/blobs/toc.json)|Registreert de gebruiker en beëindigt de toegang tot Azure Storage resources.|
|[azcopy make](storage-ref-azcopy-make.md?toc=/azure/storage/blobs/toc.json)|Hiermee maakt u een container of bestands share.|
|[azcopy remove](storage-ref-azcopy-remove.md?toc=/azure/storage/blobs/toc.json)|Verwijder blobs of bestanden uit een Azure-opslagaccount.|
|[azcopy sync](storage-ref-azcopy-sync.md?toc=/azure/storage/blobs/toc.json)|Repliceert de bronlocatie naar de doellocatie.|

## <a name="use-in-a-script"></a>Gebruiken in een script

#### <a name="obtain-a-static-download-link"></a>Een statische downloadkoppeling verkrijgen

Na een periode zal de [AzCopy-downloadkoppeling](#download-and-install-azcopy) verwijzen naar nieuwe versies van AzCopy. Als met uw script AzCopy wordt gedownload, werkt het script mogelijk niet meer als een nieuwere versie van AzCopy functies wijzigt die afhankelijk zijn van uw script.

U kunt deze problemen voorkomen door een statische (ongewijzigde) koppeling naar de huidige versie van AzCopy te verkrijgen. Op die manier downloadt uw script steeds dezelfde exacte versie van AzCopy wanneer het wordt uitgevoerd.

Voer deze opdracht uit om de koppeling te verkrijgen:

| Besturingssysteem  | Opdracht |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Voor Linux verwijdert u met de opdracht de map op het hoogste niveau die de versienaam bevat en extraheert in plaats daarvan het binaire bestand `--strip-components=1` rechtstreeks naar de huidige `tar` map. Hierdoor kan het script worden bijgewerkt met een nieuwe versie van door `azcopy` alleen de URL bij te `wget` werken.

De URL wordt weergegeven in de uitvoer van deze opdracht. Uw script kan vervolgens AzCopy downloaden met behulp van die URL.

| Besturingssysteem  | Opdracht |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>Speciale tekens escapen in SAS-tokens

In batchbestanden die de extensie hebben, moet u de tekens die worden weergegeven `.cmd` `%` in SAS-tokens escapen. U kunt dit doen door een extra teken toe `%` te voegen naast bestaande tekens in de tekenreeks van het `%` SAS-token.

#### <a name="run-scripts-by-using-jenkins"></a>Scripts uitvoeren met Jenkins

Als u Jenkins wilt gebruiken [om](https://jenkins.io/) scripts uit te voeren, moet u de volgende opdracht aan het begin van het script plaatsen.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Gebruiken in Azure Storage Explorer

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruikt AzCopy om alle gegevensoverdrachtsbewerkingen uit te voeren. U kunt [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruiken als u gebruik wilt maken van de prestatievoordelen van AzCopy, maar u liever een grafische gebruikersinterface gebruikt in plaats van de opdrachtregel om met uw bestanden te communiceren.

Storage Explorer gebruikt uw accountsleutel om bewerkingen uit te voeren, dus nadat u zich hebt Storage Explorer, hoeft u geen aanvullende autorisatiereferenties op te geven.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Configureren, optimaliseren en oplossen

Zie een van de volgende resources:

- [AzCopy-configuratie-instellingen](storage-ref-azcopy-configuration-settings.md)

- [De prestaties van AzCopy optimaliseren](storage-use-azcopy-optimize.md)

- [Problemen met AzCopy V10 in Azure Storage oplossen met behulp van logboekbestanden](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Een eerdere versie gebruiken

Als u de vorige versie van AzCopy wilt gebruiken, bekijkt u een van de volgende koppelingen:

- [AzCopy in Windows (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy in Linux (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Volgende stappen

Als u vragen, problemen of algemene feedback hebt, kunt u deze indienen [op de GitHub-pagina.](https://github.com/Azure/azure-storage-azcopy)
