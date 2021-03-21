---
title: Azure Data Box, Azure Data Box Heavy gebeurtenissen voor de export volgorde bijhouden en registreren | Microsoft Docs
description: Hierin wordt beschreven hoe u gebeurtenissen in de verschillende fasen van uw Azure Data Box en Azure Data Box Heavy export volgorde kunt volgen en registreren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 3a915ac8de83a5e183660ec4a3d05044eafff4a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94337505"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-export-orders"></a>Tracering en logboek registratie voor uw Azure Data Box en Azure Data Box Heavy het exporteren van orders

Een Data Box-of Data Box Heavy-export volgorde gaat door de volgende stappen: order, instellen, gegevens kopiëren, retour neren en gegevens verwijdering. Die overeenkomt met elke stap in de order, kunt u meerdere acties ondernemen om de toegang tot de order te beheren, de gebeurtenissen te controleren, de volg orde bij te houden en de verschillende logboeken te interpreteren die worden gegenereerd.

In dit artikel worden de verschillende mechanismen of hulpprogram ma's beschreven die beschikbaar zijn voor het bijhouden en controleren van export orders voor Data Box of Data Box Heavy. De informatie in dit artikel is van toepassing op zowel Data Box als Data Box Heavy. In de volgende secties zijn verwijzingen naar Data Box ook van toepassing op Data Box Heavy.

In de volgende tabel ziet u een overzicht van de stappen voor de Data Box-export volgorde en de hulpprogram ma's die beschikbaar zijn om de volg orde tijdens elke stap bij te houden en te controleren.

| Fase van de export volgorde Data Box       | Hulp programma voor het volgen en controleren                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Order maken               | [Toegangs beheer voor de order instellen via Azure RBAC](#set-up-access-control-on-the-order) <br> [Uitgebreide logboek inschakelen in de volg orde](#enable-verbose-log-in-the-order)                                                    |
| Order verwerkt            | [De volg orde volgen](#track-the-order) <ul><li> Azure Portal </li><li> Vervoerders website </li><li>E-mailmeldingen</ul> |
| Apparaat instellen              | De toegang tot de referenties van het apparaat is geregistreerd in [activiteiten logboeken](#query-activity-logs-during-setup)              |
| Gegevens kopiëren van apparaat        | [Kopieer logboeken controleren](#copy-log) <br> [Uitgebreide logboeken controleren](#verbose-log) voordat u gegevens kopieert            |
| Gegevens verwijdering van apparaat   | [Keten van Bewaar logboeken weer geven](#get-chain-of-custody-logs-after-data-erasure) , inclusief audit logboeken en order geschiedenis                |


## <a name="set-up-access-control-on-the-order"></a>Toegangs beheer voor de order instellen

U kunt bepalen wie toegang heeft tot uw bestelling wanneer de order voor het eerst wordt gemaakt. Stel Azure-rollen in verschillende bereiken in om de toegang tot de Data Box order te beheren. Een Azure-rol bepaalt het type toegang – lezen/schrijven, alleen-lezen, lezen-schrijven naar een subset van bewerkingen.

De twee rollen die kunnen worden gedefinieerd voor de Azure Data Box-Service zijn:

- **Data Box lezer** : heeft alleen-lezen toegang tot een of meer orders zoals gedefinieerd door de scope. Ze kunnen alleen details van een order weer geven. Ze hebben geen toegang tot andere gegevens met betrekking tot opslag accounts of bewerken de details van de bestelling, zoals het adres, enzovoort.
- **Data Box Inzender** : kan alleen een order maken om gegevens over te dragen naar een opgegeven opslag account *als ze al schrijf toegang hebben tot een opslag account*. Als ze geen toegang hebben tot een opslag account, kunnen ze zelfs geen Data Box order maken om gegevens naar het account te kopiëren. Met deze rol worden geen machtigingen voor opslag accounts gedefinieerd, en wordt geen toegang verleend aan de opslag account.  

Als u de toegang tot een order wilt beperken, kunt u het volgende doen:

- Wijs een rol op een order niveau toe. De gebruiker heeft alleen die machtigingen zoals gedefinieerd door de rollen om te communiceren met die specifieke Data Box order alleen en niets anders.
- Wijs een rol toe op het niveau van de resource groep, de gebruiker heeft toegang tot alle Data Box orders binnen een resource groep.

Zie [Aanbevolen procedures voor Azure RBAC](../role-based-access-control/best-practices.md)voor meer informatie over het aanbevolen gebruik van Azure RBAC.

## <a name="enable-verbose-log-in-the-order"></a>Uitgebreide logboek inschakelen in de volg orde

Wanneer u een export volgorde voor Data Box plaatst, hebt u de mogelijkheid om de verzameling van een uitgebreid logboek in te scha kelen. Hier volgt het bestel scherm waarin u het uitgebreide logboek kunt inschakelen:

![Selecteer de optie exporteren](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

Wanneer u de optie **uitgebreide logboek opnemen** selecteert, wordt er een uitgebreid logboek bestand gegenereerd bij het kopiëren van de gegevens uit uw Azure Storage-account. Dit logboek bevat een lijst met alle bestanden die zijn geëxporteerd.

Zie [een export volgorde voor data Box maken](data-box-deploy-export-ordered.md) voor meer informatie over de export volgorde

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

- Elke aanmelding bij de Data Box wordt in real-time vastgelegd. Deze informatie is echter alleen beschikbaar in de [audit logboeken van de Bewaar](#chain-of-custody-audit-logs) bewerking nadat de order is voltooid.

## <a name="view-logs-during-data-copy"></a>Logboeken weer geven tijdens kopiëren van gegevens

Voordat u gegevens van uw Data Box kopieert, kunt u het *Kopieer logboek* en *uitgebreide logboeken* downloaden en bekijken voor de gegevens die naar de data box zijn gekopieerd. Deze logboeken worden gegenereerd wanneer de gegevens uit uw opslag account in azure naar uw Data Box worden gekopieerd. 

### <a name="copy-log"></a>Logboek kopiëren

Voordat u de gegevens van uw Data Box kopieert, downloadt u het Kopieer logboek van de pagina **verbinding maken en kopiëren** .

Hier volgt een voor beeld van de uitvoer van het *Kopieer logboek* wanneer er geen fouten zijn en alle bestanden zijn gekopieerd tijdens het kopiëren van gegevens van Azure naar Data Box apparaat.

```output
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
  <TotalFiles_Blobs>5521</TotalFiles_Blobs>
  <FilesErrored>0</FilesErrored>
</CopyLog>
``` 
    
Hier volgt een voorbeeld uitvoer wanneer het *Kopieer logboek* fouten bevat en een aantal bestanden niet kan worden gekopieerd van Azure.

```output
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>    
```

U hebt de volgende opties om die bestanden te exporteren: 

- U kunt de bestanden die niet konden worden gekopieerd, via het netwerk overdragen. 
- Als de grootte van uw gegevens de apparaatcapaciteit overschrijdt, worden slechts sommige van de bestanden gekopieerd en worden alle niet-gekopieerde bestanden in dit logboek vermeld. U kunt dit logboek als een invoer-XML gebruiken om een nieuwe Data Box-bestelling te maken en deze bestanden vervolgens te kopiëren.

### <a name="verbose-log"></a>Uitgebreid logboek

Het *uitgebreide logboek* bevat een lijst met alle bestanden die uit het Azure Storage-account zijn geëxporteerd. Het logboek bevat ook bestandsgroottes en controlesomberekeningen.

Het uitgebreide logboek bevat de informatie in de volgende indeling:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Hier volgt een voor beeld van de uitvoer van het uitgebreide logboek. 

```powershell
  <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
  </File>
  ``````

De uitgebreide logboeken worden ook gekopieerd naar het Azure-opslag account. Standaard worden logboeken geschreven naar een container met de naam `copylog` . De logboeken worden opgeslagen met de volgende naam Conventie:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Het pad naar het kopie logboek wordt ook weer gegeven op de Blade **overzicht** voor de portal.

<!-- add a screenshot-->

U kunt deze logboeken gebruiken om te controleren of bestanden die zijn gekopieerd van Azure overeenkomen met de gegevens die zijn gekopieerd naar uw on-premises server. 

Het uitgebreide logboek bestand gebruiken:

- Om te controleren op basis van de werkelijke namen en het aantal bestanden dat is gekopieerd van de Data Box.
- Om te controleren op basis van de daad werkelijke grootte van de bestanden.
- Controleren of de *crc64* overeenkomt met een teken reeks die niet gelijk is aan nul. Er wordt een CRC-berekening (cyclische redundantie controle) uitgevoerd tijdens het exporteren vanuit Azure. De CRCs van de export en nadat de gegevens zijn gekopieerd van Data Box naar een on-premises server, kunnen worden vergeleken. Een CRC komt niet overeen, geeft aan dat de overeenkomende bestanden niet correct kunnen worden gekopieerd.


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Keten van Bewaar logboeken ophalen na gegevens verwijdering

Nadat de gegevens zijn gewist van de Data Box schijven volgens de richt lijnen van het NIST SP 800-88 Revision 1, zijn de keten logboeken beschikbaar. Deze logboeken bevatten de keten van controle logboeken voor bewaring en de order geschiedenis. De stuk lijst-of manifest bestanden worden ook gekopieerd met de audit Logboeken.

### <a name="chain-of-custody-audit-logs"></a>Audit logboeken van Bewaar keten

De audit logboeken van de Bewaar keten bevatten informatie over het inschakelen en openen van shares op de Data Box of Data Box Heavy wanneer deze zich buiten het Azure-Data Center bevindt. Deze logboeken bevinden zich in: `storage-account/azuredatabox-chainofcustodylogs`

Hier volgt een voor beeld van het audit logboek van een Data Box:

```output
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
