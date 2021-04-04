---
title: Azure Data Box bijhouden en registreren Azure Data Box Heavy gebeurtenissen voor import volgorde | Microsoft Docs
description: Hierin wordt beschreven hoe u gebeurtenissen in de verschillende fasen van uw Azure Data Box kunt bijhouden en registreren Azure Data Box Heavy kunt importeren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: a9304936f746b82b59550d62e8b60a9e0035d188
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147929"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-import-order"></a>Tracering en logboek registratie voor uw Azure Data Box en Azure Data Box Heavy import volgorde

Een Data Box-of Data Box Heavy import volgorde gaat door de volgende stappen: order, instellen, gegevens kopiëren, retour neren, uploaden naar Azure en gegevens verwijdering. Die overeenkomt met elke stap in de order, kunt u meerdere acties ondernemen om de toegang tot de order te beheren, de gebeurtenissen te controleren, de volg orde bij te houden en de verschillende logboeken te interpreteren die worden gegenereerd.

De volgende tabel bevat een samen vatting van de Data Box-of Data Box Heavy stappen voor het importeren van orders en de hulpprogram ma's die beschikbaar zijn om de volg orde tijdens elke stap bij te houden en te controleren.

| Fase van Data Box import volgorde       | Hulp programma voor het volgen en controleren                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Order maken               | [Toegangs beheer voor de order instellen via Azure RBAC](#set-up-access-control-on-the-order)                                                    |
| Order verwerkt            | [De volg orde volgen](#track-the-order) <ul><li> Azure Portal </li><li> Vervoerders website </li><li>E-mailmeldingen</ul> |
| Apparaat instellen              | De toegang tot de referenties van het apparaat is geregistreerd in [activiteiten logboeken](#query-activity-logs-during-setup)                                              |
| Gegevens kopiëren naar apparaat        | [ *error.xml* bestanden](#view-error-log-during-data-copy) voor het kopiëren van gegevens weer geven                                                             |
| Voorbereiding voor verzending            | [De stuk lijst bestanden](#inspect-bom-during-prepare-to-ship) of de manifest bestanden op het apparaat controleren                                      |
| Gegevens uploaden naar Azure       | [Kopieer logboeken](#review-copy-log-during-upload-to-azure) voor fouten tijdens het uploaden van gegevens in azure Data Center                         |
| Gegevens verwijdering van apparaat   | [Keten van Bewaar logboeken weer geven](#get-chain-of-custody-logs-after-data-erasure) , inclusief audit logboeken en order geschiedenis                |

In dit artikel worden de verschillende mechanismen of hulpprogram ma's beschreven die beschikbaar zijn voor het bijhouden en controleren van Data Box of het importeren van Data Box Heavy orders. De informatie in dit artikel is van toepassing op zowel Data Box als Data Box Heavy import orders. In de volgende secties zijn verwijzingen naar Data Box ook van toepassing op Data Box Heavy.

## <a name="set-up-access-control-on-the-order"></a>Toegangs beheer voor de order instellen

U kunt bepalen wie toegang heeft tot uw bestelling wanneer de order voor het eerst wordt gemaakt. Stel Azure-rollen in verschillende bereiken in om de toegang tot de Data Box order te beheren. Een Azure-rol bepaalt het type toegang – lezen/schrijven, alleen-lezen, lezen-schrijven naar een subset van bewerkingen.

De twee rollen die kunnen worden gedefinieerd voor de Azure Data Box-Service zijn:

- **Data Box lezer** : heeft alleen-lezen toegang tot een of meer orders zoals gedefinieerd door de scope. Ze kunnen alleen details van een order weer geven. Ze hebben geen toegang tot andere gegevens met betrekking tot opslag accounts of bewerken de details van de bestelling, zoals het adres, enzovoort.
- **Data Box Inzender** : kan alleen een order maken om gegevens over te dragen naar een opgegeven opslag account *als ze al schrijf toegang hebben tot een opslag account*. Als ze geen toegang hebben tot een opslag account, kunnen ze zelfs geen Data Box order maken om gegevens naar het account te kopiëren. Met deze rol worden geen machtigingen voor opslag accounts gedefinieerd, en wordt geen toegang verleend aan de opslag account.  

Als u de toegang tot een order wilt beperken, kunt u het volgende doen:

- Wijs een rol op een order niveau toe. De gebruiker heeft alleen die machtigingen zoals gedefinieerd door de rollen om te communiceren met die specifieke Data Box order alleen en niets anders.
- Wijs een rol toe op het niveau van de resource groep, de gebruiker heeft toegang tot alle Data Box orders binnen een resource groep.

Zie [Aanbevolen procedures voor Azure RBAC](../role-based-access-control/best-practices.md)voor meer informatie over het aanbevolen gebruik van Azure RBAC.

## <a name="track-the-order"></a>De bestelling volgen

U kunt uw bestelling volgen via de Azure Portal en via de website van de vervoerder. De volgende mechanismen zijn aanwezig om de Data Box order op elk gewenst moment te volgen:

- Als u de volg orde wilt bijhouden wanneer het apparaat zich in azure Data Center of uw locatie bevindt, gaat u naar het **overzicht van data Box order >** in azure Portal.

    ![Order status weer geven en nummer bijhouden](media/data-box-logs/overview-view-status-1.png)

- Als u de volg orde wilt bijhouden terwijl het apparaat onderweg is, gaat u naar de website van de regionale drager, bijvoorbeeld UPS-website in ons. Geef het tracerings nummer op dat is gekoppeld aan uw order.
- Data Box verzendt ook e-mail meldingen wanneer de status van de bestelling wordt gewijzigd op basis van de e-mail adressen die zijn ingevoerd toen de order werd gemaakt. Zie [order status weer geven](data-box-portal-admin.md#view-order-status)voor een lijst met alle data Box order status. Zie [meldings Details bewerken](data-box-portal-admin.md#edit-notification-details)als u de instellingen voor meldingen wilt wijzigen die zijn gekoppeld aan de order.

## <a name="query-activity-logs-during-setup"></a>Logboeken voor query activiteiten tijdens de installatie

- Uw Data Box arriveert in uw bedrijf met een vergrendelde status. U kunt de referenties van het apparaat gebruiken die beschikbaar zijn in de Azure Portal voor uw order.  

    Wanneer een Data Box is ingesteld, moet u mogelijk weten wie de referenties van het apparaat hebben geopend. Als u wilt weten wie de Blade referenties voor het **apparaat** heeft geopend, kunt u een query uitvoeren op de activiteiten Logboeken.  Alle acties die betrekking hebben op toegang tot **apparaatgegevens >** Blade referenties, worden geregistreerd in de activiteiten Logboeken als `ListCredentials` actie.

    ![Logboeken met queryactiviteit](media/data-box-logs/query-activity-log-1.png)

- Elke aanmelding bij de Data Box wordt in real-time vastgelegd. Deze informatie is echter alleen beschikbaar in de [audit logboeken](#audit-logs) nadat de volg orde is voltooid.

## <a name="view-error-log-during-data-copy"></a>Fouten logboek weer geven tijdens kopiëren van gegevens

Tijdens het kopiëren van gegevens naar Data Box of Data Box Heavy, wordt een fout bestand gegenereerd als er problemen zijn met de gekopieerde gegevens.

### <a name="errorxml-file"></a>Error.xml-bestand

Zorg ervoor dat de Kopieer taken zonder fouten zijn voltooid. Als er fouten optreden tijdens het kopieerproces, downloadt u de logboeken op de pagina **Verbinding maken en kopiëren**.

- Als u een bestand hebt gekopieerd dat niet 512 bytes is uitgelijnd op een map met beheerde schijven op uw Data Box, wordt het bestand niet geüpload als pagina-BLOB naar uw staging Storage-account. In de logboeken wordt een fout vermeld. Verwijder het bestand en kopieer een bestand dat een veelvoud van 512 bytes is.
- Als u een VHDX of een dynamische VHD of een differentiërende VHD hebt gekopieerd (deze bestanden worden niet ondersteund), wordt er een fout in de logboeken weer geven.

Hier volgt een voor beeld van de *error.xml* voor verschillende fouten bij het kopiëren naar Managed disks.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Hier volgt een voor beeld van de *error.xml* voor verschillende fouten bij het kopiëren naar pagina-blobs.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Hier volgt een voor beeld van de *error.xml* voor verschillende fouten bij het kopiëren naar blok-blobs.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Hier volgt een voor beeld van de *error.xml* voor verschillende fouten bij het kopiëren naar Azure files.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

Los de fouten in elk van de bovenstaande gevallen op voordat u verdergaat met de volgende stap. Ga voor meer informatie over de fouten die zijn ontvangen tijdens het kopiëren van gegevens naar Data Box via SMB-of NFS-protocollen, naar [problemen met data box en data Box Heavy oplossen](data-box-troubleshoot.md). Voor informatie over fouten die zijn ontvangen tijdens het kopiëren van gegevens naar Data Box via REST, gaat u naar problemen [met het data box van Blob-opslag oplossen](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>De stuk lijst controleren tijdens de voor bereiding voor verzen ding

Tijdens de voor bereiding voor verzen ding wordt er een lijst met bestanden gemaakt die de stuk lijst of het manifest bestand worden genoemd.

- Gebruik dit bestand om te controleren op basis van de werkelijke namen en het aantal bestanden dat naar de Data Box is gekopieerd.
- Gebruik dit bestand om te controleren op basis van de daad werkelijke grootte van de bestanden.
- Controleer of de *crc64* overeenkomt met een teken reeks die niet gelijk is aan nul. <!--A null value for crc64 indicates that there was a reparse point error)-->

Ga voor meer informatie over de fouten die zijn ontvangen tijdens het voorbereiden op verzen ding naar [problemen oplossen data box en data Box Heavy problemen](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>Stuk lijst-of manifest bestand

De stuk lijst of het manifest bestand bevat de lijst met alle bestanden die zijn gekopieerd naar het Data Box apparaat. Het stuk lijst bestand heeft bestands namen en de bijbehorende grootte, evenals de controlesom. Er wordt een afzonderlijk stuk lijst bestand gemaakt voor de blok-blobs, pagina-blobs, Azure Files, voor kopiëren via de REST Api's en voor het kopiëren naar beheerde schijven op de Data Box. U kunt de stuk lijst bestanden downloaden van de lokale web-UI van het apparaat tijdens de voor bereiding voor verzen ding.

Deze bestanden bevinden zich ook op het Data Box apparaat en worden geüpload naar het gekoppelde opslag account in het Azure-Data Center.

### <a name="bom-file-format"></a>Bestands indeling van stuk lijst

Stuk lijst of manifest bestand heeft de volgende algemene indeling:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Hier volgt een voor beeld van een manifest dat wordt gegenereerd wanneer de gegevens zijn gekopieerd naar de blok-BLOB share op de Data Box.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

De stuk lijst-of manifest bestanden worden ook gekopieerd naar het Azure-opslag account. U kunt de stuk lijst-of manifest bestanden gebruiken om te controleren of de bestanden die zijn geüpload naar Azure overeenkomen met de gegevens die zijn gekopieerd naar het Data Box.

## <a name="review-copy-log-during-upload-to-azure"></a>Kopie logboek controleren tijdens het uploaden naar Azure

Tijdens het uploaden van gegevens naar Azure wordt een Kopieer logboek gemaakt.

### <a name="copy-log"></a>Logboek kopiëren

Voor elke order die wordt verwerkt, maakt de Data Box-Service een kopie logboek in het gekoppelde opslag account. Het Kopieer logboek bevat het totale aantal bestanden dat is geüpload en het aantal bestanden dat tijdens het kopiëren van de gegevens is mislukt van Data Box naar uw Azure Storage-account.

Er wordt een CRC-berekening (cyclische redundantie controle) uitgevoerd tijdens het uploaden naar Azure. De CRCs van de gegevens kopie en nadat de gegevens zijn geüpload, worden vergeleken. Een CRC komt niet overeen, geeft aan dat de bijbehorende bestanden niet kunnen worden geüpload.

Standaard worden logboeken geschreven naar een container met de naam `copylog` . De logboeken worden opgeslagen met de volgende naam Conventie:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Het pad naar het kopie logboek wordt ook weer gegeven op de Blade **overzicht** voor de portal.

![Pad naar de Blade overzicht van het kopiëren van het logboek wanneer dit is voltooid](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>Het uploaden is voltooid 

In het volgende voor beeld wordt de algemene indeling van een Kopieer logboek beschreven voor een Data Box upload dat is voltooid:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>Uploaden is voltooid met fouten 

Uploaden naar Azure kan ook worden voltooid met fouten.

![Pad naar de Blade overzicht van het kopiëren van het logboek wanneer dit is voltooid met fouten](media/data-box-logs/copy-log-path-2.png)

Hier volgt een voor beeld van een Kopieer logboek waarin de upload is voltooid met fouten:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
### <a name="upload-completed-with-warnings"></a>Het uploaden is voltooid met waarschuwingen

Upload naar Azure is voltooid met waarschuwingen als uw gegevens container/BLOB/bestands namen bevatten die niet voldoen aan Azure-naamgevings conventies en de namen zijn gewijzigd om de gegevens naar Azure te uploaden.

![Pad naar de Blade overzicht van het kopiëren van het logboek wanneer deze is voltooid met waarschuwingen](media/data-box-logs/copy-log-path-3.png)

Hier volgt een voor beeld van een Kopieer logboek waarbij de naam van de containers die niet voldoen aan de Azure-naamgevings conventies werd gewijzigd tijdens het uploaden van gegevens naar Azure.

De nieuwe unieke namen voor containers hebben de indeling `DataBox-GUID` en de gegevens voor de container worden in de nieuwe hernoemde container geplaatst. In het Kopieer logboek worden de oude en de nieuwe container naam voor de container opgegeven.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Hier volgt een voor beeld van een Kopieer logboek waarbij de naam van de blobs of bestanden die niet voldoen aan de Azure-naamgevings conventies, is gewijzigd tijdens het uploaden van gegevens naar Azure. De nieuwe BLOB-of bestands namen worden geconverteerd naar de SHA256-Digest van het relatieve pad naar de container en worden geüpload naar het pad op basis van het doel type. Het doel kan blok-blobs, pagina-blobs of Azure Files zijn.

`copylog`Hiermee geeft u de oude en de nieuwe BLOB-of bestands naam en het pad in azure op.

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Keten van Bewaar logboeken ophalen na gegevens verwijdering

Nadat de gegevens zijn gewist van de Data Box schijven volgens de richt lijnen van het NIST SP 800-88 Revision 1, zijn de keten logboeken beschikbaar. Deze logboeken bevatten de audit logboeken en de order geschiedenis. De stuk lijst-of manifest bestanden worden ook gekopieerd met de audit Logboeken.

### <a name="audit-logs"></a>Auditlogboeken

Audit logboeken bevatten informatie over het inschakelen en openen van shares op de Data Box of Data Box Heavy wanneer deze zich buiten het Azure-Data Center bevindt. Deze logboeken bevinden zich in: `storage-account/azuredatabox-chainofcustodylogs`

Hier volgt een voor beeld van het audit logboek van een Data Box:

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>Ordergeschiedenis downloaden

Order geschiedenis is beschikbaar in Azure Portal. Als de order is voltooid en het opschonen van het apparaat (gegevens verwijdering van de schijven) is voltooid, gaat u naar **de volg orde** van uw apparaten en navigeert u naar Bestellingsgegevens. De optie **Ordergeschiedenis downloaden** is beschikbaar. Zie [order geschiedenis downloaden](data-box-portal-admin.md#download-order-history)voor meer informatie.

Als u door de order geschiedenis schuift, ziet u het volgende:

- Informatie over het bijhouden van vervoerders voor uw apparaat.
- Gebeurtenissen met *SecureErase* -activiteit. Deze gebeurtenissen komen overeen met het verwijderen van de gegevens op de schijf.
- Data Box logboek koppelingen. De paden voor de *audit logboeken*, *Kopieer logboeken* en *stuk lijst* bestanden worden weer gegeven.

Hier volgt een voor beeld van het logboek voor order geschiedenis van Azure Portal:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [oplossen van problemen met uw data box en data Box Heavy](data-box-troubleshoot.md).
