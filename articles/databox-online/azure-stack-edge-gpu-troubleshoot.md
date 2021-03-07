---
title: De Azure Portal gebruiken om problemen met Azure Stack Edge Pro op te lossen met GPU | Microsoft Docs
description: Hierin wordt beschreven hoe u problemen met Azure Stack Edge Pro GPU oplost.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 696faaecd2227c9b9ef74f20763e36661991ff67
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438980"
---
# <a name="troubleshoot-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Problemen met uw Azure Stack Edge Pro GPU-apparaat oplossen 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u problemen oplost op uw Azure Stack Edge Pro GPU-apparaat. 


## <a name="run-diagnostics"></a>Diagnose uitvoeren

U kunt de diagnostische tests uitvoeren om apparaatfouten vast te stellen en op te lossen. Voer de volgende stappen uit in de lokale webinterface van uw apparaat om diagnostische tests uit te voeren.

1. Ga in de lokale webinterface naar **Probleemoplossing > Diagnostische tests**. Selecteer de test die u wilt uitvoeren en selecteer **test uitvoeren**. Met de test worden mogelijke problemen met uw netwerk, apparaat, webproxy, tijd of Cloud instellingen vastgesteld. U ontvangt een melding dat er tests op het apparaat worden uitgevoerd.

    ![Testen selecteren ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. Wanneer de tests zijn voltooid, worden de resultaten weergegeven. 

    ![Test resultaten weer geven](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    Als een test mislukt, wordt er een URL voor de aanbevolen actie weergegeven. Selecteer de URL om de aanbevolen actie weer te geven.
 
    ![Waarschuwingen voor mislukte testen controleren](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Ondersteuningspakket verzamelen

Er wordt een logboekpakket samengesteld van alle relevante logboeken die Microsoft Ondersteuning kunnen helpen bij het oplossen van eventuele problemen met het apparaat. U kunt een logboekpakket genereren via de lokale webinterface.

Voer de volgende stappen uit om een ondersteuningspakket te verzamelen. 

1. Ga in de lokale webinterface naar **Probleemoplossing > Ondersteuning**. Selecteer **ondersteunings pakket maken**. Het systeem begint met het verzamelen van het ondersteuningspakket. Het verzamelen van het pakket kan enkele minuten duren.

    ![Gebruiker toevoegen selecteren](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. Nadat het ondersteunings pakket is gemaakt, selecteert u **ondersteunings pakket downloaden**. Er wordt een gecomprimeerd pakket gedownload naar het pad dat u hebt gekozen. U kunt het pakket uitpakken en de systeemlogboekbestanden bekijken.

    ![Selecteer gebruiker 2 toevoegen](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>Geavanceerde beveiligings logboeken verzamelen

De geavanceerde beveiligings logboeken kunnen software-of hardware-inbreuk logboeken voor uw Azure Stack Edge Pro-apparaat zijn.

### <a name="software-intrusion-logs"></a>Software-indringings logboeken

De software-indringing of de standaard firewall logboeken worden verzameld voor binnenkomend en uitgaand verkeer. 

- Wanneer er een installatie kopie wordt van het apparaat in de fabriek, wordt de standaard logboek registratie van de firewall ingeschakeld. Deze logboeken worden standaard gebundeld in het ondersteunings pakket wanneer u een ondersteunings pakket maakt via de lokale gebruikers interface of via de Windows Power shell-interface van het apparaat.

- Als alleen de firewall logboeken nodig zijn in het ondersteunings pakket om eventuele inbraak software (NW) in het apparaat te controleren, gebruikt u `-Include FirewallLog` de optie bij het maken van het ondersteunings pakket. 

- Als er geen specifieke insluitings optie is opgegeven, wordt het firewall logboek in het ondersteunings pakket opgenomen als standaard.

- In het ondersteunings pakket is het firewall logboek het en bevindt zich `pfirewall.log` in de hoofdmap. Hier volgt een voor beeld van het logboek voor software-inbreuk voor het Azure Stack Edge Pro-apparaat. 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>Inbreuken op de hardware

Voor het detecteren van eventuele hardwareproblemen op het apparaat worden momenteel alle chassis gebeurtenissen, zoals het openen of sluiten van het chassis, vastgelegd. 

- Het systeem gebeurtenis logboek van het apparaat wordt gelezen met de `racadm` cmdlet. Deze gebeurtenissen worden vervolgens gefilterd op gebeurtenis met betrekking tot het chassis in een `HWIntrusion.txt` bestand.

- Als u alleen het hardwareprobleem in het ondersteunings pakket wilt ontvangen, gebruikt `-Include HWSelLog` u de optie bij het maken van het ondersteunings pakket. 

- Als er geen specifieke insluitings optie is opgegeven, wordt het inbraak logboek van de hardware opgenomen als standaard waarde in het ondersteunings pakket.

- In het ondersteunings pakket is het indringings logboek van de hardware de en bevindt zich `HWIntrusion.txt` in de hoofdmap. Hier volgt een voor beeld van het logboek voor hardware-inbreuken voor het Azure Stack Edge Pro-apparaat. 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="use-logs-to-troubleshoot"></a>Logboeken gebruiken om problemen op te lossen

Eventuele fouten die zich hebben voorgedaan tijdens de upload- en vernieuwingsprocessen zijn opgenomen in de respectieve foutbestanden.

1. Als u de fout bestanden wilt weer geven, gaat u naar de share en selecteert u de share om de inhoud weer te geven. 


2. Selecteer de _map micro soft data Box Edge_. Deze map heeft twee submappen:

    - uploadmap met logboekbestanden van fouten tijdens het uploaden.
    - vernieuwingsmap met logboekbestanden van fouten tijdens het vernieuwen.

    Hier volgt een voorbeeld van een logboekbestand voor vernieuwen.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. Als u in dit bestand een fout ziet (gemarkeerd in het voorbeeld), noteert u de foutcode (in dit geval 16001). Zoek de beschrijving van deze foutcode op in de volgende naslaginformatie voor fouten.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="use-error-lists-to-troubleshoot"></a>Fout lijsten gebruiken om problemen op te lossen

De fouten lijsten worden samengesteld uit geïdentificeerde scenario's en kunnen worden gebruikt voor zelf diagnose en probleem oplossing. 

## <a name="azure-resource-manager"></a>Azure Resource Manager

Hier vindt u de fouten die kunnen worden weer gegeven tijdens de configuratie van Azure Resource Manager om toegang te krijgen tot uw apparaat. 

| **Probleem/fouten** |  **Oplossing** | 
|------------|-----------------|
|Algemene problemen|<li>[Controleer of het edge-apparaat juist is geconfigureerd](#verify-the-device-is-configured-properly).<li> [Controleren of de client correct is geconfigureerd](#verify-the-client-is-configured-properly)|
|Add-AzureRmEnvironment: er is een fout opgetreden tijdens het verzenden van de aanvraag.<br>Op regel: 1 teken: 1<br>+ Add-AzureRmEnvironment naam Az3-ARMEndpoint " https://management.dbe ...|Deze fout betekent dat uw Azure Stack Edge Pro-apparaat niet bereikbaar is of niet juist is geconfigureerd. Controleer of het edge-apparaat en de-client correct zijn geconfigureerd. Zie de rij met **algemene problemen** in deze tabel voor hulp.|
|De service heeft een fout geretourneerd. Controleer InnerException voor meer details: de onderliggende verbinding is gesloten: kan geen vertrouwens relatie tot stand brengen voor het beveiligde SSL/TLS-kanaal. |   Deze fout wordt waarschijnlijk veroorzaakt door een of meer stappen voor het uitvoeren van uw eigen certificaat. U vindt [hier](./azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates)meer informatie. |
|De bewerking heeft een ongeldige status code ServiceUnavailable geretourneerd <br> De antwoord status code geeft niet aan dat geslaagd is: 503 (service niet beschikbaar). | Deze fout kan het gevolg zijn van een van deze voor waarden.<li>ArmStsPool heeft de status gestopt.</li><li>Een van de websites van Azure Resource Manager/Security Token Services is niet beschikbaar.</li><li>De Azure Resource Manager cluster bron is niet beschikbaar.</li><br><strong>Opmerking:</strong> Door het apparaat opnieuw op te starten, kan het probleem worden opgelost, maar moet u het ondersteunings pakket verzamelen zodat u het verder kunt opsporen.|
|AADSTS50126: Ongeldige gebruikers naam of wacht woord.<br>Traceer-ID: 29317da9-52fc-4ba0-9778-446ae5625e5a<br>Correlatie-ID: 1b9752c4-8cbf-4304-a714-8a16527410f4<br>Tijds tempel: 2019-11-15 09:21:57Z: de externe server heeft een fout geretourneerd: (400) ongeldige aanvraag.<br>Op regel: 1 teken: 1 |Deze fout kan het gevolg zijn van een van deze voor waarden.<li>Voor een ongeldige gebruikers naam en wacht woord controleert u of de klant het wacht woord heeft gewijzigd van Azure Portal door de volgende stappen te volgen en vervolgens het juiste wacht [woord te gebruiken](./azure-stack-edge-j-series-set-azure-resource-manager-password.md) .<li>De Tenant-id voor een ongeldige Tenant-ID is een vaste GUID en moet worden ingesteld op `c0257de7-538f-415c-993a-1b87a031879d`</li>|
|Connect-AzureRmAccount: AADSTS90056: de resource is uitgeschakeld of bestaat niet. Controleer de code van de app om te controleren of u de exacte resource-URL hebt opgegeven voor de resource die u wilt openen.<br>Traceer-ID: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>Correlatie-ID: 75c8ef5a-830e-48b5-b039-595a96488ff9-tijds tempel: 2019-11-18 07:00:51Z: de externe server heeft een fout geretourneerd: (400) ongeldig |De resource-eind punten die in de `Add-AzureRmEnvironment` opdracht worden gebruikt, zijn onjuist.|
|Kan geen eind punten ophalen uit de Cloud.<br>Controleer of u verbinding hebt met het netwerk. Fout Details: HTTPSConnectionPool (host = ' management. dbg-of4k6suvm.microsoftdatabox.com ', poort = 30005): het maximale aantal nieuwe pogingen is overschreden met URL:/metadata/endpoints? API-Version = 2015-01-01 (veroorzaakt door SSLError (SSLError ("ongeldige handshake: error ([(' SSL-routines ', ' tls_process_server_certificate ', ' certificaat controleren is mislukt ')],),) |Deze fout wordt meestal weer gegeven in een Mac/Linux-omgeving en wordt veroorzaakt door de volgende problemen:<li>Er is geen PEM-indelings certificaat toegevoegd aan het python-certificaat archief.</li> |

### <a name="verify-the-device-is-configured-properly"></a>Controleren of het apparaat juist is geconfigureerd

1. Controleer in de lokale gebruikers interface of het doelnet werk juist is geconfigureerd.

2. Controleer of de certificaten zijn bijgewerkt voor alle eind punten die [hier](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates)worden vermeld.

3. Het Azure Resource Manager beheer en het eind punt voor aanmelding ophalen van de pagina **apparaat** in de lokale gebruikers interface.

4. Controleer of het apparaat is geactiveerd en is geregistreerd in Azure.


### <a name="verify-the-client-is-configured-properly"></a>Controleren of de client correct is geconfigureerd

1. Controleer of de juiste Power shell-versie is geïnstalleerd, zoals [hier](azure-stack-edge-j-series-connect-resource-manager.md#step-3-install-powershell-on-the-client)wordt beschreven.

2. Controleer of de juiste Power shell-modules zijn geïnstalleerd, zoals [hier](azure-stack-edge-j-series-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client)wordt beschreven.

3. Controleer of Azure Resource Manager en aanmeldings eindpunten bereikbaar zijn. U kunt proberen om de eind punten te pingen. Bijvoorbeeld:

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   Als ze niet bereikbaar zijn, voegt u DNS/host-bestands vermeldingen toe zoals [hier](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)wordt vermeld.
   
4. Controleer of de client certificaten zijn geïnstalleerd, zoals [hier](azure-stack-edge-j-series-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell)wordt beschreven.

5. Als de klant Power shell gebruikt, moet u de voor keuren voor fout opsporing inschakelen om gedetailleerde berichten te bekijken door deze Power shell-opdracht uit te voeren. 

    `$debugpreference = "continue"`

## <a name="blob-storage-on-device"></a>Blob Storage op het apparaat 

Hier vindt u de fouten met betrekking tot Blob Storage op Azure Stack Edge Pro/Data Box Gateway-apparaat.

| **Probleem/fouten** |  **Oplossing** | 
|--------------------|-----------------|
|Kan geen onderliggende resources ophalen. De waarde voor een van de HTTP-headers heeft niet de juiste indeling.| Selecteer in het menu **bewerken** de optie **doel-Azure stack-api's**. Start Azure Storage Explorer vervolgens opnieuw.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Controleer of de naam van het eind punt `<accountname>.blob.<serialnumber>.microsoftdatabox.com` is toegevoegd aan het hosts-bestand op dit pad: `C:\Windows\System32\drivers\etc\hosts` in Windows of `/etc/hosts` op Linux.|
|Kan geen onderliggende resources ophalen.<br> Details: zelfondertekend certificaat |Importeer het SSL-certificaat voor uw apparaat in Azure Storage Explorer: <ol><li>Down load het certificaat van de Azure Portal. Zie [het certificaat downloaden](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)voor meer informatie.</li><li>Selecteer in het menu **bewerken** de optie SSL-certificaten en selecteer vervolgens **certificaten importeren**.</li></ol>|
|De AzCopy-opdracht lijkt niet meer te reageren gedurende een minuut voordat deze fout wordt weer gegeven:<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Controleer of de naam van het eind punt `<accountname>.blob.<serialnumber>.microsoftdatabox.com` is toegevoegd aan het hosts-bestand op: `C:\Windows\System32\drivers\etc\hosts` .|
|De AzCopy-opdracht lijkt niet meer te reageren gedurende een minuut voordat deze fout wordt weer gegeven:<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |Importeer het SSL-certificaat voor uw apparaat in het certificaat archief van het systeem. Zie [het certificaat downloaden](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)voor meer informatie.|
|De AzCopy-opdracht lijkt 20 minuten niet meer te reageren voordat deze fout wordt weer gegeven:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |Controleer of de naam van het eind punt `<accountname>.blob.<serialnumber>.microsoftdatabox.com` is toegevoegd aan het hosts-bestand op: `/etc/hosts` .|
|De AzCopy-opdracht lijkt 20 minuten niet meer te reageren voordat deze fout wordt weer gegeven:<br>`Error parsing source location… The SSL connection could not be established`. |Importeer het SSL-certificaat voor uw apparaat in het certificaat archief van het systeem. Zie [het certificaat downloaden](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)voor meer informatie.|
|De AzCopy-opdracht lijkt 20 minuten niet meer te reageren voordat deze fout wordt weer gegeven:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|Controleer of de naam van het eind punt `<accountname>.blob.<serialnumber>.microsoftdatabox.com` is toegevoegd aan het hosts-bestand op: `/etc/hosts` .|
|De AzCopy-opdracht lijkt 20 minuten niet meer te reageren voordat deze fout wordt weer gegeven: `Error parsing source location… The SSL connection could not be established` .|Importeer het SSL-certificaat voor uw apparaat in het certificaat archief van het systeem. Zie [het certificaat downloaden](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)voor meer informatie.|
|De waarde voor een van de HTTP-headers heeft niet de juiste indeling.|De geïnstalleerde versie van de Microsoft Azure Storage-bibliotheek voor python wordt niet ondersteund door Data Box. Zie Azure Data Box vereisten voor Blob Storage voor ondersteunde versies.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...| Voordat u python uitvoert, stelt u de omgevings variabele REQUESTS_CA_BUNDLE in op het pad van het met base64 gecodeerde SSL-certificaat bestand (Zie How to [down load the Certificate](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate). Bijvoorbeeld:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>U kunt het certificaat ook toevoegen aan het certificaat archief van het systeem en deze omgevings variabele vervolgens instellen op het pad van dat archief. Bijvoorbeeld op Ubuntu:<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`.|
|Er is een time-out opgestaan.|Meld u aan bij de Azure Stack Edge Pro en controleer of deze is ontgrendeld. Telkens wanneer het apparaat opnieuw wordt opgestart, blijft het vergrendeld totdat iemand zich aanmeldt.|

## <a name="troubleshoot-iot-edge-errors"></a>Problemen met IoT Edge oplossen

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [oplossen van problemen met het activeren van apparaten](azure-stack-edge-gpu-troubleshoot-activation.md).