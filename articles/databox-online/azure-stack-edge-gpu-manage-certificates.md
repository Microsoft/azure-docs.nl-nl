---
title: Certificaten gebruiken met Azure Stack Edge Pro GPU | Microsoft Docs
description: Beschrijft het gebruik van certificaten met Azure Stack Edge Pro GPU-apparaat, met inbegrip van de reden voor het gebruik van, het type en het uploaden van certificaten op het apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4193e58c28b481297df38bca8f18d2ea766ce886
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443128"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Certificaten gebruiken met Azure Stack Edge Pro GPU-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel worden de typen certificaten beschreven die op uw Azure Stack Edge Pro-apparaat kunnen worden geïnstalleerd. Het artikel bevat ook de Details voor elk certificaat type samen met de procedure voor het installeren en identificeren van de verval datum. 

## <a name="about-certificates"></a>Over certificaten

Een certificaat biedt een koppeling tussen een **open bare sleutel** en een entiteit (zoals een domein naam) die is **ondertekend** (geverifieerd) door een vertrouwde derde partij (zoals een **certificerings instantie**).  Een certificaat biedt een handige manier om vertrouwde open bare versleutelings sleutels te distribueren. Certificaten zorgen ervoor dat uw communicatie vertrouwd is en dat u versleutelde gegevens naar de juiste server verzendt. 

Wanneer uw Azure Stack Edge Pro-apparaat in eerste instantie is geconfigureerd, worden automatisch zelfondertekende certificaten gegenereerd. U kunt desgewenst uw eigen certificaten meenemen. Er zijn richt lijnen die u moet volgen als u van plan bent om uw eigen certificaten te nemen.

## <a name="types-of-certificates"></a>Typen certificaten

De verschillende typen certificaten die op uw Azure Stack Edge Pro-apparaat worden gebruikt, zijn als volgt: 
- Handtekeningcertificaten
    - Basis-CA
    - Gevorderd

- Eindpunt certificaten
    - Knooppunt certificaat
    - Lokale UI-certificaten
    - Azure Resource Manager certificaten
    - Blob Storage-certificaten
    - IoT-apparaten certificaten
    <!--- WiFi certificates
    - VPN certificates-->

- Versleutelingscertificaten
    - Sessie certificaten ondersteunen

Elk van deze certificaten wordt gedetailleerd beschreven in de volgende secties.

## <a name="signing-chain-certificates"></a>Ketting certificaten ondertekenen

Dit zijn de certificaten voor de instantie die de certificaten of de handtekening certificerings instantie ondertekent. 

### <a name="types"></a>Typen

Deze certificaten kunnen basis certificaten of tussenliggende certificaten zijn. De basis certificaten zijn altijd zelfondertekend (of ondertekend zelf). De tussenliggende certificaten zijn niet zelfondertekend en zijn ondertekend door de handtekening instantie.

### <a name="caveats"></a>Waarschuwingen

- De basis certificaten moeten keten certificaten ondertekenen.
- De basis certificaten kunnen worden geüpload op het apparaat in de volgende indeling: 
    - **Der** : deze zijn beschikbaar als `.cer` bestands extensie.
    - **Base-64-code ring** : deze zijn beschikbaar als `.cer` bestands extensie.
    - **P7b** : deze indeling wordt alleen gebruikt voor het ondertekenen van ketting certificaten die de basis-en tussenliggende certificaten bevatten.
- Handtekening keten certificaten worden altijd geüpload voordat u andere certificaten uploadt.


## <a name="node-certificates"></a>Knooppunt certificaten

<!--Your Azure Stack Edge Pro device could be a 1-node device or a 4-node device.--> Alle knoop punten in het apparaat communiceren voortdurend met elkaar en hebben daarom een vertrouwens relatie nodig. Knooppunt certificaten bieden een manier om die vertrouwens relatie tot stand te brengen. Knooppunt certificaten komen ook aan het spelen wanneer u verbinding maakt met het knoop punt van het apparaat met behulp van een externe Power shell-sessie via https.

### <a name="caveats"></a>Waarschuwingen

- Het knooppunt certificaat moet worden vermeld in `.pfx` een indeling met een persoonlijke sleutel die kan worden geëxporteerd. 
- U kunt 1 Joker teken knooppunt certificaat of vier afzonderlijke knooppunt certificaten maken en uploaden. 
- Een knooppunt certificaat moet worden gewijzigd als het DNS-domein wordt gewijzigd, maar de naam van het apparaat niet wordt gewijzigd. Als u uw eigen knooppunt certificaat wilt maken, kunt u het serie nummer van het apparaat niet wijzigen. u hoeft alleen de domein naam te wijzigen.
- Gebruik de volgende tabel om u te helpen bij het maken van een knooppunt certificaat.
   
    |Type |Onderwerpnaam (SN)  |Alternatieve naam voor onderwerp (SAN)  |Voor beeld van onderwerpnaam |
    |---------|---------|---------|---------|
    |Knooppunt|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>Eindpunt certificaten

Voor alle eind punten die het apparaat beschikbaar maakt, is een certificaat vereist voor vertrouwde communicatie. De eindpunt certificaten bevatten de vereisten die zijn vereist bij het openen van de Azure Resource Manager en de Blob-opslag via de REST Api's. 

Wanneer u een ondertekend certificaat van uw eigen certificaten inbrengt, moet u ook de bijbehorende ondertekening keten van het certificaat hebben. Voor de handtekening keten, Azure Resource Manager en de BLOB-certificaten op het apparaat hebt u de bijbehorende certificaten op de client computer ook nodig om te verifiëren en te communiceren met het apparaat.

### <a name="caveats"></a>Waarschuwingen

- De eindpunt certificaten moeten `.pfx` een indeling hebben met een persoonlijke sleutel. De handtekening keten moet DER-indeling ( `.cer` bestands extensie) hebben. 
- Wanneer u uw eigen eindpunt certificaten meebrengt, kunnen deze als afzonderlijke certificaten of certificaten met meer domeinen worden beschouwd. 
- Als u een handtekening keten wilt maken, moet het handtekening certificaat worden geüpload voordat u een eindpunt certificaat uploadt.
- Deze certificaten moeten worden gewijzigd als de naam van het apparaat of de DNS-domein naam wordt gewijzigd.
- Er kan een certificaat met een Joker teken worden gebruikt.
- De eigenschappen van de eindpunt certificaten zijn vergelijkbaar met die van een standaard SSL-certificaat. 
- Gebruik de volgende tabel bij het maken van een eindpunt certificaat:

    |Type |Onderwerpnaam (SN)  |Alternatieve naam voor onderwerp (SAN)  |Voor beeld van onderwerpnaam |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Multi-SAN één certificaat voor beide eind punten|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>Lokale UI-certificaten

U kunt via een browser toegang krijgen tot de lokale webgebruikersinterface van uw apparaat. Om ervoor te zorgen dat deze communicatie veilig is, kunt u uw eigen certificaat uploaden. 

### <a name="caveats"></a>Waarschuwingen

- Het lokale gebruikers interface certificaat wordt ook geüpload in een `.pfx` indeling met een persoonlijke sleutel die kan worden geëxporteerd.
- Nadat u het lokale gebruikers interface certificaat hebt geüpload, moet u de browser opnieuw opstarten en de cache wissen. Raadpleeg de specifieke instructies voor uw browser.

    |Type |Onderwerpnaam (SN)  |Alternatieve naam voor onderwerp (SAN)  |Voor beeld van onderwerpnaam |
    |---------|---------|---------|---------|
    |Lokale gebruikers interface| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>Certificaten van IoT Edge apparaten

Uw Azure Stack Edge Pro-apparaat is ook een IoT-apparaat met de reken kracht die is ingeschakeld door een IoT Edge apparaat. Voor beveiligde communicatie tussen deze IoT Edge apparaat en de downstream-apparaten waarmee verbinding kan worden gemaakt, kunt u ook IoT Edge certificaten uploaden. 

Het apparaat heeft zelfondertekende certificaten die kunnen worden gebruikt als u alleen het reken scenario met het apparaat wilt gebruiken. Als de Azure Stack Edge Pro-apparaat echter is aangesloten op downstream-apparaten, moet u uw eigen certificaten meenemen.

Er zijn drie IoT Edge certificaten die u moet installeren om deze vertrouwens relatie in te scha kelen:

- **Basis certificerings instantie of eigenaar van de certificerings instantie**
- **Certificerings instantie van apparaat** 
- **Certificaat van de apparaats sleutel**

### <a name="caveats"></a>Waarschuwingen

- De IoT Edge certificaten worden geüpload in de `.pem` indeling. 


Zie [Azure IOT Edge certificaat Details](../iot-edge/iot-edge-certs.md#iot-edge-certificates)voor meer informatie over IOT Edge certificaten.

## <a name="support-session-certificates"></a>Sessie certificaten ondersteunen

Als uw Azure Stack Edge Pro-apparaat problemen ondervindt, kunt u deze problemen oplossen door een externe Power shell-ondersteunings sessie op het apparaat te openen. Als u een beveiligde, versleutelde communicatie over deze ondersteunings sessie wilt inschakelen, kunt u een certificaat uploaden.

### <a name="caveats"></a>Waarschuwingen

- Zorg ervoor dat het bijbehorende `.pfx` certificaat met de persoonlijke sleutel is geïnstalleerd op de client computer met behulp van het hulp programma voor ontsleuteling.
- Controleer of het veld **sleutel gebruik** voor het certificaat geen **certificaat ondertekening** is. Als u dit wilt controleren, klikt u met de rechter muisknop op het certificaat, kiest u **openen** en gaat u naar het tabblad **Details** en zoekt u het **sleutel gebruik**. 


### <a name="caveats"></a>Waarschuwingen

- Het certificaat voor de ondersteunings sessie moet worden gegeven als een indeling met een `.cer` extensie.


<!--## VPN certificates

If VPN is configured on your Azure Stack Edge Pro device, then you will also need a certificate for any communication that occurs over the VPN channel. You can bring your own VPN certificate to ensure the communication is trusted.

### Caveats

- The VPN certificate must be uploaded as a pfx format with a  private key.
- The VPN certificate is not dependant on the device name, device serial number, or device configuration. It only requires the external FQDN.
- Make sure that the client OID is set.-->

<!--## WiFi certificates

If your device is configured to operate on a wireless network, then you will also need a WiFi certificate for any communication that occurs over the wireless network. 

### Caveats

- The WiFi certificate must be uploaded as a pfx format with a private key.
- Make sure that the client OID is set.-->


## <a name="create-certificates-optional"></a>Certificaten maken (optioneel)

In de volgende sectie wordt de procedure beschreven voor het maken van een handtekening keten en eindpunt certificaten.

### <a name="certificate-workflow"></a>Certificaat werk stroom

U hebt een gedefinieerde manier om de certificaten te maken voor de apparaten die in uw omgeving worden uitgevoerd. U kunt de door de IT-beheerder meegeleverde certificaten gebruiken. 

**Alleen voor ontwikkelings-en test doeleinden kunt u ook Windows Power shell gebruiken om certificaten op uw lokale systeem te maken.** Volg de volgende richt lijnen bij het maken van de certificaten voor de client:

1. U kunt een van de volgende typen certificaten maken:

    - Maak een enkel certificaat dat geldig is voor gebruik met één Fully Qualified Domain Name (FQDN). Bijvoorbeeld *mydomain.com*.
    - Maak een Joker teken certificaat om de hoofd domein naam en meerdere subdomeinen te beveiligen. Bijvoorbeeld **. mydomain.com*.
    - Maak een certificaat voor een alternatieve naam voor het onderwerp (SAN) dat meerdere domein namen in één certificaat heeft. 

2. Als u uw eigen certificaat maakt, hebt u een basis certificaat voor de handtekening keten nodig. Bekijk de stappen voor het [maken van handtekening keten certificaten](#create-signing-chain-certificate).

3. U kunt vervolgens de eindpunt certificaten maken voor de lokale gebruikers interface van het apparaat, de BLOB en de Azure Resource Manager. U kunt drie afzonderlijke certificaten maken voor het apparaat, de BLOB en de Azure Resource Manager, maar u kunt ook één certificaat maken voor alle drie de eind punten. Zie [ondertekening-en eindpunt certificaten maken](#create-signed-endpoint-certificates)voor gedetailleerde stappen.

4. Of u nu 3 afzonderlijke certificaten of een certificaat maakt, de onderwerpnaam (SN) en alternatieve namen van onderwerpen (SAN) opgeven volgens de richt lijnen voor elk certificaat type. 

### <a name="create-signing-chain-certificate"></a>Handtekening keten certificaat maken

Deze certificaten maken met behulp van Windows Power shell die wordt uitgevoerd in de beheerders modus. **De certificaten die op deze manier zijn gemaakt, moeten alleen worden gebruikt voor ontwikkelings-en test doeleinden.**

Het handtekening keten certificaat hoeft slechts één keer te worden gemaakt. De andere eindpunt certificaten verwijzen naar dit certificaat voor ondertekening.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>Ondertekende eindpunt certificaten maken

Deze certificaten maken met behulp van Windows Power shell die wordt uitgevoerd in de beheerders modus.

In deze voor beelden worden er endpoint-certificaten voor een apparaat gemaakt met:
    - Apparaatnaam: `DBE-HWDC1T2`
    - DNS-domein: `microsoftdatabox.com`

Vervang door de naam en het DNS-domein van uw apparaat om certificaten voor uw apparaat te maken.
 
**BLOB-eindpunt certificaat**

Maak een certificaat voor het BLOB-eind punt in uw persoonlijke archief.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Azure Resource Manager eindpunt certificaat**

Maak een certificaat voor de Azure Resource Manager-eind punten in uw persoonlijke archief.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Certificaat voor lokale web-UI van apparaat**

Maak een certificaat voor de lokale web-UI van het apparaat in uw persoonlijke archief.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Eén multi-SAN-certificaat voor alle eind punten**

Maak één certificaat voor alle eind punten in uw persoonlijke Store.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

Zodra de certificaten zijn gemaakt, is de volgende stap het uploaden van de certificaten op uw Azure Stack Edge Pro-apparaat


## <a name="upload-certificates"></a>Certificaten uploaden 

De certificaten die u voor het apparaat hebt gemaakt, bevinden zich standaard in het **persoonlijke archief** van de client. Deze certificaten moeten op uw client worden geëxporteerd naar de juiste indelings bestanden die vervolgens naar uw apparaat kunnen worden geüpload.

1. Het basis certificaat moet worden geëxporteerd als een DER-indeling met de `.cer` extensie. Zie [certificaten exporteren als de indeling](#export-certificates-as-der-format)voor gedetailleerde stappen.
2. De eindpunt certificaten moeten worden geëxporteerd als *PFX* -bestanden met persoonlijke sleutels. Zie [certificaten exporteren als *. pfx* -bestand met persoonlijke sleutels](#export-certificates-as-pfx-format-with-private-key)voor gedetailleerde stappen. 
3. De basis-en eindpunt certificaten worden vervolgens op het apparaat geüpload met behulp van de optie **+ certificaat toevoegen** op de pagina certificaten in de lokale webgebruikersinterface. 

    1. Upload eerst de basis certificaten. Ga in de lokale web-UI naar **certificaten > + certificaat toevoegen**.

        ![Handtekening keten certificaat 1 toevoegen](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

    2. Upload vervolgens de eindpunt certificaten. 

        ![Handtekening keten certificaat 2 toevoegen](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

        Kies de certificaat bestanden in *. pfx* -indeling en voer het wacht woord in dat u hebt opgegeven tijdens het exporteren van het certificaat. Het kan enkele minuten duren voordat het Azure Resource Manager certificaat is toegepast.

        Als de handtekening keten niet eerst wordt bijgewerkt en u probeert de eindpunt certificaten te uploaden, wordt er een fout melding weer geven.

        ![Certificaat fout Toep assen](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

        Ga terug en upload het handtekening keten certificaat en upload en pas vervolgens de eindpunt certificaten toe.

> [!IMPORTANT]
> Als de apparaatnaam of het DNS-domein wordt gewijzigd, moeten nieuwe certificaten worden gemaakt. De client certificaten en de certificaten van het apparaat moeten vervolgens worden bijgewerkt met de nieuwe apparaatnaam en het DNS-domein. 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>Certificaten importeren op de client die toegang hebben tot het apparaat

De certificaten die u hebt gemaakt en geüpload naar het apparaat, moeten worden geïmporteerd op uw Windows-client (toegang tot het apparaat) in het juiste certificaat archief.

1. Het basis certificaat dat u hebt geëxporteerd als de DER moet nu worden geïmporteerd in de **vertrouwde basis certificerings instanties** op het client systeem. Zie [certificaten importeren in het archief met vertrouwde basis certificerings instanties](#import-certificates-as-der-format)voor gedetailleerde stappen.

2. De eindpunt certificaten die u als de moet exporteren, `.pfx` moeten worden geëxporteerd als een der- `.cer` extensie. Deze `.cer` wordt vervolgens geïmporteerd in het **persoonlijke certificaat archief** op uw systeem. Zie [certificaten importeren in het persoonlijke certificaat archief](#import-certificates-as-der-format)voor gedetailleerde stappen.

### <a name="import-certificates-as-der-format"></a>Certificaten importeren als een DER-indeling

Als u certificaten wilt importeren op een Windows-client, voert u de volgende stappen uit:

1. Klik met de rechter muisknop op het bestand en selecteer **certificaat installeren**. Deze actie start de wizard Certificaat importeren.

    ![Certificaat 1 importeren](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. Selecteer voor de **Archief locatie** de optie **lokale computer** en selecteer vervolgens **volgende**.

    ![Certificaat 2 importeren](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. Selecteer **alle certificaten in het onderstaande archief opslaan** en selecteer vervolgens **Bladeren**. 

    - Als u wilt importeren in een persoonlijk archief, gaat u naar het persoonlijke archief van uw externe host en selecteert u vervolgens **volgende**.

        ![Certificaat importeren 4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - Als u wilt importeren in een vertrouwde Store, gaat u naar de vertrouwde basis certificerings instantie en selecteert u **volgende**.

        ![Certificaat importeren 3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. Selecteer **Finish**. Er wordt een bericht weer gegeven met het effect dat het importeren is geslaagd.

### <a name="export-certificates-as-pfx-format-with-private-key"></a>Certificaten exporteren als. pfx-indeling met persoonlijke sleutel

Voer de volgende stappen uit om een SSL-certificaat met een persoonlijke sleutel te exporteren op een Windows-computer. 

> [!IMPORTANT]
> Voer de volgende stappen uit op de computer die u hebt gebruikt om het certificaat te maken. 

1. Voer *certlm. msc* uit om het certificaat archief van de lokale computer te starten.

1. Dubbel klik op de **persoonlijke** map en klik vervolgens op **certificaten**.

    ![Certificaat exporteren 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. Klik met de rechter muisknop op het certificaat waarvan u een back-up wilt maken en kies **alle taken > exporteren...**

    ![Certificaat exporteren 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. Volg de wizard Certificaat exporteren om een back-up van uw certificaat naar een pfx-bestand te maken.

    ![Certificaat exporteren 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. Kies **Ja, de persoonlijke sleutel exporteren**.

    ![Certificaat exporteren 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. Kies **indien mogelijk alle certificaten insluiten in** certificaatpad, **Exporteer alle uitgebreide eigenschappen** en **Schakel certificaat privacy** in. 

    > [!IMPORTANT]
    > Selecteer de optie voor het verwijderen van de **persoonlijke sleutel niet als het exporteren is gelukt**.

    ![Certificaat exporteren 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. Geef een wacht woord op dat u wilt onthouden. Bevestig het wachtwoord. Met het wacht woord wordt de persoonlijke sleutel beveiligd.

    ![Certificaat exporteren 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. Kies of u het bestand op een ingestelde locatie wilt opslaan.

    ![Certificaat exporteren 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. Selecteer **Finish**.

    ![Certificaat exporteren 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. U ontvangt een bericht dat de export is voltooid. Selecteer **OK**.

    ![Certificaat exporteren 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

De back-up van het pfx-bestand wordt nu opgeslagen op de locatie die u hebt geselecteerd en kan worden verplaatst of opgeslagen voor veilig bewaren.


### <a name="export-certificates-as-der-format"></a>Certificaten exporteren als DER notatie

1. Voer *certlm. msc* uit om het certificaat archief van de lokale computer te starten.

1. Selecteer het basis certificaat in het persoonlijke certificaat archief. Klik met de rechter muisknop en selecteer **alle taken > exporteren...**

    ![Certificaat exporteren DER 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. De wizard certificaat wordt geopend. Selecteer de indeling als **der Encoded Binary X. 509 (. CER)**. Selecteer **Next**.

    ![Certificaat exporteren DER 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. Blader en selecteer de locatie waar u het. CER-indelings bestand wilt exporteren.

    ![Certificaat exporteren DER 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)

4. Selecteer **Finish**.

    ![Certificaat exporteren DER 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="supported-certificate-algorithms"></a>Ondersteunde certificaat algoritmen

 Alleen de Rivest – Shamir – Adleman (RSA)-certificaten worden ondersteund met uw Azure Stack Edge Pro-apparaat. Als elliptische curve ECDSA-certificaten (Digital Signature Algorithm) worden gebruikt, is het gedrag van het apparaat onbepaald.

 Certificaten die een open bare RSA-sleutel bevatten, worden RSA-certificaten genoemd. Certificaten die een met elliptische curve cryptografische (ECC) open bare sleutel bevatten, worden ECDSA (elliptische digitale hand tekening algoritme) genoemd. 


## <a name="view-certificate-expiry"></a>Certificaat verloop weer geven

Als u uw eigen certificaten meebrengt, verlopen de certificaten doorgaans in één jaar of 6 maanden. Als u de verval datum van uw certificaat wilt weer geven, gaat u naar de pagina **certificaten** in de lokale webgebruikersinterface van uw apparaat. Als u een specifiek certificaat selecteert, kunt u de verval datum van uw certificaat weer geven.

<!--## Rotate certificates

Rotation of certificates is not implemented in this release. You are also not notified of the pending expiration date on your certificate. 

View the certificate expiration date on the **Certificates** page in the local web UI of your device. Once the certificate expiration is approaching, create and upload new certificates as per the detailed instructions in [Create and upload certificates]().-->

## <a name="next-steps"></a>Volgende stappen

[Uw Azure Stack Edge Pro-apparaat implementeren](azure-stack-edge-gpu-deploy-prep.md)