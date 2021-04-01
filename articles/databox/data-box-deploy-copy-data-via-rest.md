---
title: "Zelfstudie: Naar blobopslag kopiëren via REST API's"
titleSuffix: Azure Data Box
description: In deze zelfstudie leert u hoe u verbinding kunt maken met Azure Data Box Blob-opslag door gebruik te maken van REST API's via http of https en gegevens te kopiëren van Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: alkohli
ms.openlocfilehash: cb0a90db0595c655191006969071bc5b9cceaa75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94337590"
---
# <a name="tutorial-use-rest-apis-to-copy-data-to-azure-data-box-blob-storage"></a>Zelfstudie: Gegevens kopiëren naar Azure Data Box-Blob-opslag met REST API's  

In deze zelfstudie worden procedures beschreven voor het maken van een *http*- of *https*-verbinding met Azure Data Box-blobopslag via REST API's. Als de verbinding tot stand is gebracht, worden ook de benodigde stappen voor het kopiëren van de gegevens naar Data Box-blobopslag en het voor verzending voorbereiden van Data Box beschreven.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Vereisten
> * Verbinding maken met Data Box-blobopslag via *http* of *https*
> * Gegevens kopiëren naar Data Box

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt de zelfstudie [ Azure Data Box instellen](data-box-deploy-set-up.md) voltooid.
2. U hebt uw Data Box ontvangen en de bestelstatus in de portal is **Geleverd**.
3. U hebt de [systeemvereisten voor Data Box-blobopslag](data-box-system-requirements-rest.md) bekeken en bent bekend met de ondersteunde versies van API's, SDK's en hulpprogramma's.
4. U hebt toegang tot een hostcomputer waarop de gegevens staan die u naar Data Box wilt kopiëren. Op uw hostcomputer moet
    * Een [ondersteund besturingssysteem](data-box-system-requirements.md) worden uitgevoerd.
    * Verbonden zijn met een netwerk met hoge snelheid. Het wordt aangeraden dat u beschikt over minstens één 10-GbE-verbinding. Als er geen 10 GbE-verbinding beschikbaar is, kan een 1 GbE-gegevenskoppeling worden gebruikt, maar dit heeft invloed op de kopieersnelheden.
5. [Download AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) op de hostcomputer. U gebruikt AzCopy om gegevens te kopiëren van uw hostcomputer naar Azure Data Box-blobopslag.

## <a name="connect-via-http-or-https"></a>Verbinding maken via http of https

U kunt verbinding maken met Data Box-blobopslag via *http* of *https*.

* *Https* is de veiligste en aanbevolen verbindingsmethode voor Data Box-blobopslag.
* *Http* wordt gebruikt voor verbindingen via vertrouwde netwerken.

De stappen om verbinding te maken zijn afhankelijk van of u verbinding maakt met Data Box-blobopslag via *http* of *https*.

## <a name="connect-via-http"></a>Verbinding maken via http

Voor een verbinding met Data Box-blobopslag via REST API's via *http* zijn de volgende stappen vereist:

* De apparaat-IP en het blobservice-eindpunt aan de externe host toevoegen
* Software van derden configureren en de verbinding controleren

Deze stappen worden afzonderlijk beschreven in de volgende gedeelten.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Het IP-adres van het apparaat en het blobservice-eindpunt toevoegen

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]

### <a name="verify-connection-and-configure-partner-software"></a>Verbinding controleren en partnersoftware configureren

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Verbinding maken via https

Voor een verbinding met Azure-blobopslag via REST API's via https zijn de volgende stappen vereist:

* Het certificaat downloaden van Azure Portal
* Het certificaat importeren op de client externe host
* De apparaat-IP en het blobservice-eindpunt aan de client of externe host toevoegen
* Software van derden configureren en de verbinding controleren

Deze stappen worden afzonderlijk beschreven in de volgende gedeelten.

### <a name="download-certificate"></a>Certificaat downloaden

Gebruik Azure Portal om het certificaat te downloaden.

1. Meld u aan bij Azure Portal.
2. Ga naar uw Data Box-bestelling en ga vervolgens naar **Algemeen > Apparaatdetails**.
3. Ga onder **Apparaatreferenties** naar **API-toegang** tot het apparaat. Klik op **Downloaden**. Er wordt een certificaatbestand ( **\<your order name>.cer**) gedownload. **Sla** dit bestand op. U installeert dit certificaat op de client- of hostcomputer waarmee u verbinding maakt met het apparaat.

    ![Certificaat downloaden in Azure Portal](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)

### <a name="import-certificate"></a>Certificaat importeren

Voor toegang tot Data Box-blobopslag over HTTPS is een TLS-/SSL-certificaat voor het apparaat vereist. De manier waarop dit certificaat beschikbaar wordt gesteld aan de clienttoepassing is afhankelijk van de toepassing, het besturingssysteem en de distributie. Sommige toepassingen kunnen het certificaat openen nadat het is geïmporteerd in het certificaatarchief van het systeem, andere toepassingen maken geen gebruik van dat mechanisme.

In deze sectie wordt specifieke informatie voor bepaalde toepassingen vermeld. Raadpleeg de documentatie voor de toepassing en het gebruikte besturingssysteem voor meer informatie over andere toepassingen.

Volg deze stappen om het `.cer`-bestand te importeren in het basisarchief van een Windows- of Linux-client. Op een Windows-systeem kunt u Windows PowerShell of de gebruikersinterface van Windows Server gebruiken om het certificaat op uw systeem te importeren en te installeren.

#### <a name="use-windows-powershell"></a>Windows PowerShell gebruiken

1. Start een Windows PowerShell-sessie als beheerder.
2. Typ in de opdrachtprompt:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

#### <a name="use-windows-server-ui"></a>Gebruikersinterface voor Windows Server gebruiken

1. Klik met de rechtermuisknop op het `.cer`-bestand en selecteer **Certificaat installeren**. Deze actie start de wizard Certificaat importeren.
2. Selecteer bij **Archieflocatie** de optie **Lokale computer** en klik op **Volgende**.

    ![Wizard Certificaat importeren, Windows Server](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3. Selecteer **Alle certificaten in het onderstaande archief opslaan** en klik op **Bladeren**. Navigeer naar het basisarchief van de externe host en klik vervolgens op **Volgende**.

    ![Wizard Certificaat importeren, Certificaatarchief](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4. Klik op **Voltooien**. Er wordt een bericht weergegeven dat het importeren is geslaagd.

    ![Wizard Certificaat importeren, import voltooien](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

#### <a name="use-a-linux-system"></a>Een Linux-systeem gebruiken

De methode om een certificaat te importeren varieert per distributie.

Verschillende distributies, zoals Ubuntu en Debian, gebruiken de opdracht `update-ca-certificates`.  

* Hernoem het met Base64 gecodeerde certificaatbestaand met een `.crt`-extensie en kopieer het in de `/usr/local/share/ca-certificates directory`.
* Voer de opdracht `update-ca-certificates` uit.

Recente versies van RHEL, Fedora en CentOS gebruiken de opdracht `update-ca-trust`.

* Kopieer het bestand naar de map `/etc/pki/ca-trust/source/anchors`.
* Voer `update-ca-trust` uit.

Raadpleeg de documentatie behorend bij uw distributie voor meer informatie.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Het IP-adres van het apparaat en het blobservice-eindpunt toevoegen 

Volg dezelfde stappen om [het IP-adres van het apparaat en het bloB toe te voegen wanneer u verbinding maakt via *http*](#add-device-ip-address-and-blob-service-endpoint).

### <a name="configure-partner-software-and-verify-connection"></a>Partnersoftware configureren en verbinding controleren

Volg de stappen om [Partner-software te configureren die u hebt gebruikt bij het maken van een verbinding via *http*](#verify-connection-and-configure-partner-software). Het enige verschil is dat u de optie *HTTP gebruiken* uitgeschakeld laat.

## <a name="copy-data-to-data-box"></a>Gegevens kopiëren naar Data Box

Nadat u verbinding met de Data Box-blobopslag hebt gemaakt, moet u de gegevens kopiëren. Neem de volgende overwegingen door voordat u gegevens kopieert:

* Zorg er tijdens het kopiëren van gegevens voor dat de gegevensgrootte voldoet aan de limieten die staan beschreven in de [limieten voor Azure-opslag en Data Box](data-box-limits.md).
* Als de gegevens die door Data Box worden geüpload gelijktijdig door andere toepassingen buiten Data Box worden geüpload, kan dit leiden tot uploadfouten en beschadigde gegevens.

> [!IMPORTANT]
> Zorg dat u een kopie van de brongegevens in uw on-premises omgeving hebt totdat u kunt bevestigen dat de gegevens met Data Box naar Azure Storage zijn overgebracht.

In deze zelfstudie wordt AzCopy gebruikt om gegevens te kopiëren naar Data Box-blobopslag. U kunt de gegevens ook kopiëren met Azure Storage Explorer (als u liever een GUI-hulpprogramma gebruikt) of met partnersoftware.

De kopieerprocedure bestaat uit de volgende stappen:

* Een container maken
* De inhoud van een map uploaden naar Data Box-blobopslag
* Gewijzigde bestanden uploaden naar Data Box-blobopslag

Deze stappen worden afzonderlijk in detail beschreven in de volgende gedeelten.

### <a name="create-a-container"></a>Een container maken

De eerste stap is het maken van een container, omdat blobs altijd naar een container moeten worden geüpload. Met containers ordent u groepen blobs net zoals u bestanden in mappen op uw computer ordent. Volg deze stappen om een blobcontainer te maken.

1. Open Storage Explorer.
2. Vouw in het linkerdeelvenster het opslagaccount uit waarin u de blobcontainer wilt maken.
3. Klik met de rechtermuisknop op **Blob-containers** en selecteer **Blobcontainer maken**.

   ![Contextmenu Blobcontainers, Blobcontainer maken](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. Onder de map **Blobcontainers** wordt een tekstvak weergegeven. Voer een naam in voor de blobcontainer. Zie [De container maken en machtigingen instellen](../storage/blobs/storage-quickstart-blobs-dotnet.md) voor informatie over regels en beperkingen voor namen van blobcontainers.
5. Druk op **Enter** om de blobcontainer te maken of op **Esc** om te annuleren. Als de blobcontainer is gemaakt, wordt deze weergegeven in de map **Blobcontainers** voor het geselecteerde opslagaccount.

   ![Gemaakte blobcontainer](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>De inhoud van een map uploaden naar Data Box-blobopslag

U kunt AzCopy gebruiken om alle bestanden in een map te uploaden naar Blob-opslag in Windows of Linux. Als u alle blobs in een map wilt uploaden, voert u de volgende AzCopy-opdracht uit:

#### <a name="linux"></a>Linux

```azcopy
azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive
```

#### <a name="windows"></a>Windows

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S
```

Vervang `<key>` door uw accountsleutel. Ga naar uw opslagaccount om uw accountsleutel in Azure Portal op te halen. Ga naar **Instellingen > Toegangssleutels**, selecteer een sleutel en plak deze in de AzCopy-opdracht.

Als de opgegeven doelcontainer niet bestaat, wordt deze door AzCopy gemaakt en wordt het bestand erin geüpload. Werk het bronpad bij in uw gegevensmap en vervang `data-box-storage-account-name` in de doel-URL door de naam van het opslagaccount dat is gekoppeld aan uw Data Box.

Als u de inhoud van de opgegeven map recursief wilt uploaden naar Blob-opslag, geeft u de optie `--recursive` (Linux) of `/S` optie (Windows) op. Wanneer u AzCopy uitvoert met een van deze opties, worden alle submappen en de bijbehorende bestanden ook geüpload.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Gewijzigde bestanden uploaden naar Data Box-blobopslag

Gebruik AzCopy om bestanden te uploaden op basis van de datum/tijd waarop deze het laatst zijn gewijzigd. Als u dit wilt uitproberen, wijzigt u bestanden of maakt u nieuwe bestanden in uw bronmap voor testdoeleinden. Als u alleen bijgewerkte of nieuwe bestanden wilt uploaden, voegt u de parameter `--exclude-older` (Linux) of `/XO` (Windows) toe aan de AzCopy-opdracht.

Als u alleen resources wilt kopiëren die niet in het doel bestaan, geeft u zowel `--exclude-older` als `--exclude-newer` (Linux) of `/XO` als `/XN` (Windows) als parameters op in de AzCopy-opdracht. Door AzCopy worden alleen de bijgewerkte gegevens geüpload, op basis van het tijdstempel.

#### <a name="linux"></a>Linux

```azcopy
azcopy \
--source /mnt/myfolder \
--destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
--dest-key <key> \
--recursive \
--exclude-older
```

#### <a name="windows"></a>Windows

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO
```

Zie [Problemen oplossen met Data Box-blobopslag](data-box-troubleshoot-rest.md) als er fouten optreden tijdens het maken van de verbinding of het kopiëren.

In de laatste stap gaat u het apparaat voorbereiden voor verzending.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Data Box, zoals:

> [!div class="checklist"]
>
> * Vereisten
> * Verbinding maken met Data Box-blobopslag via *http* of *https*
> * Gegevens kopiëren naar Data Box

Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box naar Microsoft verzendt.

> [!div class="nextstepaction"]
> [Uw Azure Data Box verzenden naar Microsoft](./data-box-deploy-picked-up.md)
