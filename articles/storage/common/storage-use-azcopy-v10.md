---
title: Gegevens kopiëren of verplaatsen naar Azure Storage met behulp van AzCopy V10 toevoegen | Microsoft Docs
description: AzCopy is een opdracht regel programma dat u kunt gebruiken om gegevens naar, van of tussen opslag accounts te kopiëren. Dit artikel helpt u bij het downloaden van AzCopy, het verbinden met uw opslag account en het overdragen van bestanden.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 87cc8af9206daa1f1e959b8abd8beb3e7f8ff2f2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880991"
---
# <a name="get-started-with-azcopy"></a>Aan de slag met AzCopy

AzCopy is een opdrachtregelprogramma dat u kunt gebruiken om blobs of bestanden vanuit of naar een opslagaccount te kopiëren. Dit artikel helpt u bij het downloaden van AzCopy, het verbinden met uw opslag account en het overdragen van bestanden.

> [!NOTE]
> AzCopy **V10 toevoegen** is de momenteel ondersteunde versie van AzCopy.
>
> Als u een eerdere versie van AzCopy wilt gebruiken, raadpleegt u de sectie [de vorige versie van AzCopy gebruiken](#previous-version) van dit artikel.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>AzCopy downloaden

Down load eerst het uitvoer bare bestand van AzCopy V10 toevoegen naar een map op uw computer. AzCopy V10 toevoegen is slechts een uitvoerbaar bestand, dus er is niets om te installeren.

- [Windows 64-bits](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bits](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [macOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Deze bestanden worden gecomprimeerd als een zip-bestand (Windows en Mac) of een tar-bestand (Linux). Raadpleeg de documentatie voor uw Linux-distributie om het tar-bestand in Linux te downloaden en te decomprimeren.

> [!NOTE]
> Als u gegevens wilt kopiëren van en naar uw [Azure Table Storage](../tables/table-storage-overview.md) -service, installeert u vervolgens [AzCopy versie 7,3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>AzCopy uitvoeren

Voor het gemak kunt u de maplocatie van het uitvoerbare AzCopy-bestand toevoegen aan het systeempad. Op die manier kunt u typen `azcopy` vanuit een wille keurige map op uw systeem.

Als u ervoor kiest om de AzCopy-map niet aan uw pad toe te voegen, moet u de mappen wijzigen op de locatie van het uitvoer bare AzCopy-bestand en-type `azcopy` of `.\azcopy` in Windows Power shell-opdracht prompts.

Typ `azcopy -h` en druk op de Enter-toets om een lijst met opdrachten weer te geven.

Als u meer wilt weten over een specifieke opdracht, neemt u alleen de naam van de opdracht op (bijvoorbeeld: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Inline-Help](media/storage-use-azcopy-v10/azcopy-inline-help.png)


Zie [azcopy](storage-ref-azcopy.md) voor gedetailleerde Naslag informatie voor elke opdracht en opdracht parameter.

> [!NOTE] 
> Als eigenaar van uw Azure Storage-account, worden er niet automatisch machtigingen toegewezen voor toegang tot gegevens. Voordat u iets kunt doen met AzCopy, moet u bepalen hoe u autorisatie referenties voor de opslag service opgeeft. 

<a id="choose-how-youll-provide-authorization-credentials"></a>

## <a name="authorize-azcopy"></a>AzCopy autoriseren

U kunt autorisatie referenties opgeven door gebruik te maken van Azure Active Directory (AD) of door gebruik te maken van een Shared Access Signature SAS-token.

Gebruik deze tabel als richt lijn:

| Opslagtype | Momenteel ondersteunde autorisatie methode |
|--|--|
|**Blob Storage** | Azure AD en SAS |
|**Blob-opslag (hiërarchische naam ruimte)** | Azure AD en SAS |
|**File Storage** | Alleen SAS |

#### <a name="option-1-use-azure-active-directory"></a>Optie 1: Azure Active Directory gebruiken

Deze optie is alleen beschikbaar voor Blob Storage. Door Azure Active Directory te gebruiken, kunt u eenmaal referenties opgeven in plaats van een SAS-token aan elke opdracht toe te voegen.  

> [!NOTE]
> Als u in de huidige release blobs tussen opslag accounts wilt kopiëren, moet u een SAS-token toevoegen aan elke bron-URL. U kunt de SAS-token alleen weglaten van de doel-URL. Zie [blobs kopiëren tussen opslag accounts](#transfer-data)voor voor beelden.

Als u toegang wilt verlenen met behulp van Azure AD, raadpleegt u [toegang verlenen tot blobs met AzCopy en Azure Active Directory (Azure AD)](storage-use-azcopy-authorize-azure-active-directory.md).

#### <a name="option-2-use-a-sas-token"></a>Optie 2: een SAS-token gebruiken

U kunt een SAS-token toevoegen aan elke bron-of doel-URL die wordt gebruikt in uw AzCopy-opdrachten.

Met dit voor beeld worden gegevens uit een lokale map recursief gekopieerd naar een BLOB-container. Er wordt een fictief SAS-token toegevoegd aan het einde van de container-URL.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Zie [using Shared Access signatures (SAS) (Engelstalig)](./storage-sas-overview.md)voor meer informatie over SAS-tokens en hoe u er een kunt verkrijgen.

<a id="transfer-data"></a>

## <a name="transfer-data"></a>Gegevens overdragen

Nadat u uw identiteit hebt goedgekeurd of een SAS-token hebt aangeschaft, kunt u beginnen met het overbrengen van gegevens.

Zie een van deze artikelen om voorbeeld opdrachten te vinden.

| Service | Artikel |
|--------|-----------|
|Azure Blob Storage |[Bestanden uploaden naar Azure Blob Storage](storage-use-azcopy-blobs-upload.md)<br><br>[Blobs downloaden van Azure Blob-opslag](storage-use-azcopy-blobs-download.md)<br><br>[Blobs kopiëren tussen Azure-opslag accounts](storage-use-azcopy-blobs-copy.md)<br><br>[Synchroniseren met Azure Blob Storage](storage-use-azcopy-blobs-synchronize.md)|
|Azure Files |[Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)|
|Amazon S3|[Gegevens overdragen met AzCopy en Amazon S3-opslag](storage-use-azcopy-s3.md)|
|Opslag Azure Stack|[Gegevens overdragen met AzCopy en Azure Stack Storage](/azure-stack/user/azure-stack-storage-transfer#azcopy)|

## <a name="use-in-a-script"></a>Gebruiken in een script

#### <a name="obtain-a-static-download-link"></a>Een statische Download koppeling verkrijgen

Na verloop van tijd verwijst de [Download koppeling](#download-and-install-azcopy) AzCopy naar nieuwe versies van AzCopy. Als uw script AzCopy downloadt, werkt het script mogelijk niet meer als een nieuwere versie van AzCopy functies wijzigt waarvan uw script afhankelijk is.

Als u deze problemen wilt voor komen, moet u een statische (ongewijzigde) koppeling naar de huidige versie van AzCopy verkrijgen. Op die manier downloadt uw script dezelfde exacte versie van AzCopy elke keer dat deze wordt uitgevoerd.

Voer de volgende opdracht uit om de koppeling te verkrijgen:

| Besturingssysteem  | Opdracht |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> Voor Linux `--strip-components=1` wordt op de `tar` opdracht de map op het hoogste niveau met de versie naam verwijderd en in plaats daarvan wordt het binaire bestand rechtstreeks naar de huidige map geëxtraheerd. Hierdoor kan het script worden bijgewerkt met een nieuwe versie van `azcopy` door alleen de URL bij te werken `wget` .

De URL wordt weer gegeven in de uitvoer van deze opdracht. Uw script kan vervolgens AzCopy downloaden met behulp van deze URL.

| Besturingssysteem  | Opdracht |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

#### <a name="escape-special-characters-in-sas-tokens"></a>Speciale tekens in SAS-tokens escapepen

In batch-bestanden met de `.cmd` extensie moet u de `%` tekens die worden weer gegeven in SAS-tokens weglaten. U kunt dit doen door een extra teken toe te voegen `%` naast `%` de bestaande tekens in de SAS-token teken reeks.

#### <a name="run-scripts-by-using-jenkins"></a>Scripts uitvoeren met behulp van Jenkins

Als u van plan bent [Jenkins](https://jenkins.io/) te gebruiken om scripts uit te voeren, moet u de volgende opdracht aan het begin van het script plaatsen.

```
/usr/bin/keyctl new_session
```

## <a name="use-in-azure-storage-explorer"></a>Gebruiken in Azure Storage Explorer

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruikt AzCopy om alle bewerkingen voor gegevens overdracht uit te voeren. U kunt [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruiken als u de prestatie voordelen van AzCopy wilt benutten, maar u liever een graphical user interface gebruikt in plaats van de opdracht regel om te communiceren met uw bestanden.

Storage Explorer gebruikt uw account sleutel voor het uitvoeren van bewerkingen, dus nadat u zich hebt aangemeld bij Storage Explorer, hoeft u geen aanvullende autorisatie referenties op te geven.

<a id="previous-version"></a>

## <a name="configure-optimize-and-fix"></a>Configureren, optimaliseren en oplossen

Zie [AzCopy configureren, optimaliseren en problemen oplossen](storage-use-azcopy-configure.md)

## <a name="use-a-previous-version"></a>Een vorige versie gebruiken

Als u de vorige versie van AzCopy moet gebruiken, raadpleegt u een van de volgende koppelingen:

- [AzCopy in Windows (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy op Linux (V7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>Volgende stappen

Als u vragen, problemen of algemene feedback hebt, verzendt u deze [op](https://github.com/Azure/azure-storage-azcopy) de pagina github.
