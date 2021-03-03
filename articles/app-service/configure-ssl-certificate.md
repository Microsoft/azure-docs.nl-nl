---
title: TLS/SSL-certificaten toevoegen en beheren
description: Maak een gratis certificaat, importeer een App Service-certificaat, importeer een Key Vault-certificaat of koop een App Service-certificaat in Azure App Service.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: e563981d3a68375105256aa6015aa94ada91326b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711702"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>Een TLS/SSL-certificaat toevoegen in Azure App Service

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie. In dit artikel wordt beschreven hoe u een persoonlijk certificaat of een openbaar certificaat maakt, uploadt of importeert in App Service. 

Nadat het certificaat is toegevoegd aan uw App Service-app of [functie-app](../azure-functions/index.yml), kunt u [er een aangepaste DNS-naam mee beveiligen](configure-ssl-bindings.md) of [het certificaat gebruiken in uw toepassingscode](configure-ssl-certificate-in-code.md).

> [!NOTE]
> Een certificaat dat is geüpload in een app, wordt opgeslagen in een implementatie-eenheid die is gekoppeld aan de combinatie van resourcegroep en regio van de app (intern wordt dit een *webruimte* genoemd). Hierdoor is het certificaat toegankelijk voor andere apps in dezelfde combinatie van resourcegroep en regio. 

De volgende tabel bevat de beschikbare opties voor het toevoegen van certificaten in App Service:

|Optie|Beschrijving|
|-|-|
| Een gratis door App Service beheerd certificaat maken (preview) | Een persoonlijk certificaat dat eenvoudig te gebruiken is als u alleen uw [aangepaste `www`-domein](app-service-web-tutorial-custom-domain.md) of een domein zonder voorvoegsel in App Service hoeft te beveiligen. |
| Een App Service-certificaat kopen | Een persoonlijk certificaat dat wordt beheerd door Azure. Het certificaat biedt de eenvoud van geautomatiseerd certificaatbeheer, gecombineerd met de flexibiliteit van opties voor verlengen en exporteren. |
| Een certificaat uit Key Vault importeren | Dit is handig als u [Azure Key Vault](../key-vault/index.yml) gebruikt voor het beheren van uw [PKCS12-certificaten](https://wikipedia.org/wiki/PKCS_12). Zie [Vereisten voor persoonlijke certificaten](#private-certificate-requirements). |
| Een persoonlijk certificaat uploaden | Als u al een persoonlijk certificaat van een externe provider hebt, kunt u het certificaat uploaden. Zie [Vereisten voor persoonlijke certificaten](#private-certificate-requirements). |
| Een openbaar certificaat uploaden | Openbare certificaten worden niet gebruikt voor het beveiligen van aangepaste domeinen, maar u kunt deze in uw code laden als u ze nodig hebt om toegang te krijgen tot externe resources. |

## <a name="prerequisites"></a>Vereisten

Voor het volgen van deze instructiegids:

- [Maak een App Service-app](./index.yml).
- Alleen gratis certificaat: wijs een subdomein (bijvoorbeeld `www.contoso.com`) toe aan App Service met een [CNAME-record](app-service-web-tutorial-custom-domain.md#map-a-cname-record).

## <a name="private-certificate-requirements"></a>Vereisten voor persoonlijke certificaten

> [!NOTE]
> Azure Web Apps biedt **geen** ondersteuning voor AES256 en alle PFX-bestanden moeten worden versleuteld met TripleDES.

Het [gratis door App Service beheerde certificaat](#create-a-free-certificate-preview) of het [App Service-certificaat](#import-an-app-service-certificate) voldoet al aan de vereisten van App Service. Als u ervoor kiest om een persoonlijk certificaat te uploaden of te importeren naar App Service, moet uw certificaat voldoen aan de volgende vereisten:

* Geëxporteerd als een [PFX-bestand met wachtwoordbeveiliging](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)
* Bevat een persoonlijke sleutel van minstens 2048 bits
* Bevat alle tussenliggende certificaten in de certificaatketen

Voor het beveiligen van een aangepast domein in een TLS-binding heeft het certificaat aanvullende vereisten:

* Bevat een [Uitgebreide-sleutelgebruik](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) voor serververificatie (OID = 1.3.6.1.5.5.7.3.1)
* Ondertekend door een vertrouwde certificeringsinstantie

> [!NOTE]
> **ECC-certificaten (cryptografie met behulp van elliptische krommen)** kunnen met App Service worden gebruikt, maar worden niet in dit artikel beschreven. Werk samen met uw certificeringsinstantie aan de exacte stappen voor het maken van ECC-certificaten.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>Een gratis certificaat maken (preview)

Het gratis door App Service beheerde certificaat is een gebruiksklare oplossing voor het beveiligen van uw aangepaste DNS-naam in App Service. Het is een volledig functioneel TLS/SSL-certificaat dat wordt beheerd door App Service en dat automatisch wordt verlengd. Het gratis certificaat wordt geleverd met de volgende beperkingen:

- Het biedt geen ondersteuning voor wildcard-certificaten.
- Het biedt geen ondersteuning voor domeinen zonder voorvoegsel.
- Het kan niet worden geëxporteerd.
- Het wordt niet ondersteund door App Service Environment (ASE).
- Het biedt geen ondersteuning voor A-records. Automatische verlenging werkt bijvoorbeeld niet met A-records.

> [!NOTE]
> Het gratis certificaat wordt uitgegeven door DigiCert. Voor sommige domeinen van het hoogste niveau moet u DigiCert expliciet als certificaatverlener toestaan door een [CAA-domeinrecord](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) te maken met de waarde: `0 issue digicert.com`.
> 

Een gratis door App Service beheerd certificaat maken:

Selecteer in het linkermenu in <a href="https://portal.azure.com" target="_blank">Azure Portal</a> de optie **App Services** >  **\<app-name>** .

Selecteer in de navigatie links in uw app **TLS/SSL-instellingen** > **Certificaten met een persoonlijke sleutel (.pfx)**  > **Door App Service beheerd certificaat maken**.

![Gratis certificaat maken in App Service](./media/configure-ssl-certificate/create-free-cert.png)

In het dialoogvenster worden alle domeinen zonder voorvoegsel vermeld die op de juiste manier zijn toegewezen aan uw app met een CNAME-record. Selecteer het aangepaste domein waarvoor u een gratis certificaat wilt maken en selecteer **Maken**. U kunt slechts één certificaat maken voor elk ondersteund aangepast domein.

Wanneer de bewerking is voltooid, ziet u het certificaat in de lijst **Certificaten met een persoonlijke sleutel**.

![Het maken van een gratis certificaat is voltooid](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Als u een aangepast domein met dit certificaat wilt beveiligen, moet u nog wel een certificaatbinding maken. Volg de stappen in [Binding maken](configure-ssl-bindings.md#create-binding).
>

## <a name="import-an-app-service-certificate"></a>Een App Service-certificaat importeren

Als u een App Service-certificaat aanschaft vanuit Azure, beheert Azure de volgende taken:

- Zorgdragen voor het aankoopproces vanuit GoDaddy.
- Domeinverificatie van het certificaat uitvoeren.
- Het certificaat in [Azure Key Vault](../key-vault/general/overview.md) onderhouden.
- De verlenging van het certificaat (zie [Certificaat verlengen](#renew-certificate)) beheren.
- Synchroniseer het certificaat automatisch met de geïmporteerde kopieën in App Service-apps.

Als u een App Service-certificaat wilt kopen, gaat u naar [Certificaatorder starten](#start-certificate-order).

Als u al een werkend App Service-certificaat hebt, kunt u het volgende doen:

- [Het certificaat importeren in App Service](#import-certificate-into-app-service).
- [Het certificaat beheren](#manage-app-service-certificates), bijvoorbeeld verlengen, opnieuw versleutelen of exporteren.
> [!NOTE]
> App Service certificaten worden momenteel niet ondersteund in nationale clouds van Azure.

### <a name="start-certificate-order"></a>Certificaatorder starten

Start een App Service-certificaatorder op de <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">pagina voor het maken van een App Service-certificaat</a>.

![Aankoop van App Service-certificaat starten](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Gebruik de volgende tabel om u te helpen bij het configureren van het certificaat. Klik op **Create** als u klaar bent.

| Instelling | Beschrijving |
|-|-|
| Naam | Een beschrijvende naam voor uw App Service-certificaat. |
| Hostnaam van domein zonder voorvoegsel | Geef hier het hoofddomein op. Met het verleende certificaat wordt *zowel* het hoofddomein als het `www`-subdomein beveiligd. In het verleende certificaat bevat het veld Algemene naam het hoofddomein en het veld Alternatieve naam voor onderwerp het `www`-domein. Als u alleen een subdomein wilt beveiligen, geeft u de FQDN van het subdomein hier op (bijvoorbeeld `mysubdomain.contoso.com`).|
| Abonnement | Het abonnement dat het certificaat bevat. |
| Resourcegroep | De resourcegroep die het certificaat bevat. U kunt bijvoorbeeld een nieuwe resourcegroep gebruiken of dezelfde resourcegroep selecteren als uw App Service-app. |
| Certificaat-SKU | Hiermee wordt het type certificaat bepaald dat moet worden gemaakt. Dit kan een standaardcertificaat of een [wildcard-certificaat](https://wikipedia.org/wiki/Wildcard_certificate) zijn. |
| Juridische voorwaarden | Klik om te bevestigen dat u akkoord gaat met de juridische voorwaarden. De certificaten worden opgehaald vanuit GoDaddy. |

> [!NOTE]
> App Service-certificaten die zijn aangeschaft vanuit Azure, worden uitgegeven door GoDaddy. Voor sommige domeinen van het hoogste niveau moet u GoDaddy expliciet als certificaatverlener toestaan door een [CAA-domeinrecord](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) te maken met de waarde: `0 issue godaddy.com`
> 

### <a name="store-in-azure-key-vault"></a>Opslaan in Azure Key Vault

Nadat het certificaat is gekocht, zijn er nog enkele stappen die u moet voltooien voordat u dit certificaat kunt gebruiken. 

Selecteer het certificaat op de pagina [App Service-certificaten](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) en klik vervolgens op **Certificaatconfiguratie** > **Stap 1: opslaan**.

![Key Vault-opslag van App Service-certificaat configureren](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](../key-vault/general/overview.md) is een Azure-service waarmee u cryptografische sleutels en geheimen kunt beveiligen die door cloudtoepassingen en -services worden gebruikt. Het is de voorkeursopslagplaats voor App Service-certificaten.

Klik op de pagina **Key Vault-status** op **Key Vault-opslagplaats** om een nieuwe kluis te maken of een bestaande kluis te kiezen. Als u ervoor kiest om een nieuwe kluis te maken, gebruikt u de volgende tabel voor het configureren van de kluis en klikt u op Maken. Maak de nieuwe Key Vault binnen hetzelfde abonnement en dezelfde resourcegroep als uw App Service-app.

| Instelling | Beschrijving |
|-|-|
| Naam | Een unieke naam die bestaat uit alfanumerieke tekens en afbreekstreepjes. |
| Resourcegroep | U wordt aangeraden dezelfde resourcegroep te selecteren als uw App Service-certificaat. |
| Locatie | Selecteer dezelfde locatie als uw App Service-app. |
| Prijscategorie | Zie [Prijzen van Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie. |
| Toegangsbeleid| Hiermee worden de toepassingen en de toegestane toegang tot de kluisresources gedefinieerd. U kunt dit later configureren door de stappen te volgen in [Een Key Vault-toegangsbeleid toewijzen](../key-vault/general/assign-access-policy-portal.md). |
| Toegang tot virtueel netwerk | Beperk de toegang tot de kluis tot bepaalde virtuele Azure-netwerken. U kunt de toegang later configureren door de stappen bij [Azure Key Vault-firewalls en virtuele netwerken configureren](../key-vault/general/network-security.md) te volgen |

Nadat u de kluis hebt geselecteerd, sluit u de pagina **Key Vault-opslagplaats**. Bij de optie **Stap 1: opslaan** wordt met het groene vinkje aangegeven dat er geen problemen zijn. Houd de pagina geopend voor de volgende stap.

### <a name="verify-domain-ownership"></a>Domeineigendom controleren

Klik op dezelfde pagina **Certificaatconfiguratie** die u in de laatste stap hebt gebruikt, op **Stap 2: controleren**.

![Domein voor App Service-certificaat controleren](./media/configure-ssl-certificate/verify-domain.png)

Selecteer **App Service-verificatie**. Omdat u het domein al aan uw web-app hebt toegewezen (zie [Vereisten](#prerequisites)), is het al gecontroleerd. U hoeft alleen maar op **Verifiëren** te klikken om deze stap te voltooien. Klik op de knop **Vernieuwen** totdat het bericht **Het domein van het certificaat is bevestigd** wordt weergegeven.

> [!NOTE]
> Er worden vier typen methoden voor domeinverificatie ondersteund: 
> 
> - **App Service**: de handigste optie wanneer het domein al is toegewezen aan een App Service-app in hetzelfde abonnement. Er wordt gebruikgemaakt van het feit dat de App Service-app het eigendom van het domein al heeft geverifieerd.
> - **Domein**: verifieer een [App Service-domein dat u hebt gekocht in Azure](manage-custom-dns-buy-domain.md). De TXT-record voor de verificatie wordt automatisch voor u toegevoegd en het proces wordt voltooid.
> - **E-mail**: verifieer het domein door een e-mailbericht naar de domeinbeheerder te verzenden. Er worden instructies weergegeven wanneer u de optie selecteert.
> - **Handmatig**: verifieer het domein met behulp van een HTML-pagina (alleen **standaard** certificaat) of een DNS TXT-record. Er worden instructies weergegeven wanneer u de optie selecteert.

### <a name="import-certificate-into-app-service"></a>Certificaat in App Service importeren

Selecteer in het linkermenu in <a href="https://portal.azure.com" target="_blank">Azure Portal</a> de optie **App Services** >  **\<app-name>** .

Selecteer in de navigatie links in uw app **TLS/SSL-instellingen** > **Certificaten met een persoonlijke sleutel (.pfx)**  > **App Service-certificaat importeren**.

![App Service-certificaat in App Service importeren](./media/configure-ssl-certificate/import-app-service-cert.png)

Selecteer het certificaat dat u zojuist hebt gekocht en selecteer **OK**.

Wanneer de bewerking is voltooid, ziet u het certificaat in de lijst **Certificaten met een persoonlijke sleutel**.

![Importeren van App Service-certificaat voltooid](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Als u een aangepast domein met dit certificaat wilt beveiligen, moet u nog wel een certificaatbinding maken. Volg de stappen in [Binding maken](configure-ssl-bindings.md#create-binding).
>

## <a name="import-a-certificate-from-key-vault"></a>Een certificaat uit Key Vault importeren

Als u Azure Key Vault gebruikt om uw certificaten te beheren, kunt u een PKCS12-certificaat vanuit Key Vault in App Service importeren mits het [voldoet aan de vereisten](#private-certificate-requirements).

### <a name="authorize-app-service-to-read-from-the-vault"></a>App Service machtigen voor leestoegang tot de kluis
Standaard heeft de App Service-resourceprovider geen toegang tot Key Vault. Als u Key Vault voor een certificaatimplementatie wilt gebruiken, moet u [de resourceprovider machtigen voor leestoegang tot Key Vault](../key-vault/general/assign-access-policy-cli.md). 

`abfa0a7c-a6b6-4736-8310-5855508787cd` is de naam van de service-principal voor de App Service-resourceprovider. Deze is hetzelfde voor alle Azure-abonnementen. Voor een Azure Government-cloudomgeving moet u `6a02c803-dafd-4136-b4c3-5a6f318b4714` gebruiken als naam van de service-principal voor de resourceprovider.

### <a name="import-a-certificate-from-your-vault-to-your-app"></a>Een certificaat uit uw kluis importeren in uw app

Selecteer in het linkermenu in <a href="https://portal.azure.com" target="_blank">Azure Portal</a> de optie **App Services** >  **\<app-name>** .

Selecteer in de navigatie links in uw app **TLS/SSL-instellingen** > **Certificaten met een persoonlijke sleutel (.pfx)**  > **Key Vault-certificaat importeren**.

![Key Vault-certificaat in App Service importeren](./media/configure-ssl-certificate/import-key-vault-cert.png)

Gebruik de volgende tabel om u te helpen bij het selecteren van het certificaat.

| Instelling | Beschrijving |
|-|-|
| Abonnement | Het abonnement waarvan de sleutelkluis deel uitmaakt. |
| Key Vault | De kluis met het certificaat dat u wilt importeren. |
| Certificaat | Selecteer in de lijst met PKCS12-certificaten in de kluis. Alle PKCS12-certificaten in de kluis worden weergegeven met hun vingerafdrukken, maar niet alle certificaten worden ondersteund in App Service. |

Wanneer de bewerking is voltooid, ziet u het certificaat in de lijst **Certificaten met een persoonlijke sleutel**. Als het importeren mislukt met een fout, voldoet het certificaat niet aan de [vereisten voor App Service](#private-certificate-requirements).

![Key Vault-certificaat importeren voltooid](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!NOTE]
> Als u uw certificaat in Key Vault bijwerkt met een nieuw certificaat, wordt uw certificaat automatisch binnen 48 uur gesynchroniseerd in App Service.

> [!IMPORTANT] 
> Als u een aangepast domein met dit certificaat wilt beveiligen, moet u nog wel een certificaatbinding maken. Volg de stappen in [Binding maken](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-private-certificate"></a>Een persoonlijk certificaat uploaden

Nadat u een certificaat van uw certificaatprovider hebt ontvangen, volgt u de stappen in deze sectie om het certificaat gereed te maken voor App Service.

### <a name="merge-intermediate-certificates"></a>Tussenliggende certificaten samenvoegen

Als uw certificeringsinstantie meerdere certificaten in de certificaatketen geeft, moet u de certificaten in de juiste volgorde samenvoegen.

U doet dit door elk certificaat dat u hebt ontvangen in een teksteditor te openen.

Maak een bestand voor het samengevoegde certificaat met de naam _mergedcertificate.crt_. Kopieer de inhoud van elk certificaat in dit bestand in een teksteditor. De volgorde van uw certificaten moet de volgorde in de certificaatketen volgen, beginnend met uw certificaat en eindigend met het hoofdcertificaat. Het lijkt op het volgende voorbeeld:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Certificaat naar PFX exporteren

Exporteer uw samengevoegde TLS/SSL-certificaat met de persoonlijke sleutel die met uw certificaataanvraag is gegenereerd.

Als u de certificaataanvraag met OpenSSL hebt gegenereerd, hebt u een bestand met een persoonlijke sleutel gemaakt. Voer de volgende opdracht uit om uw certificaat naar PFX te exporteren. Vervang de tijdelijke aanduidingen _&lt;private-key-file>_ en _&lt;merged-certificate-file>_ door de paden naar uw persoonlijke sleutel en uw bestand met samengevoegde certificaten.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Wanneer u daarom wordt gevraagd, geeft u een wachtwoord voor export op. U gebruikt dit wachtwoord later wanneer u uw TLS/SSL-certificaat naar App Service uploadt.

Als u IIS of _Certreq.exe_ hebt gebruikt voor het genereren van uw certificaataanvraag, installeert u het certificaat op uw lokale computer en [exporteert u het certificaat naar PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="upload-certificate-to-app-service"></a>Certificaat uploaden naar App Service

U bent nu klaar om het certificaat te uploaden naar App Service.

Selecteer in het linkermenu in <a href="https://portal.azure.com" target="_blank">Azure Portal</a> de optie **App Services** >  **\<app-name>** .

Selecteer in de navigatie links in uw app **TLS/SSL-instellingen** > **Certificaten met een persoonlijke sleutel (.pfx)**  > **Certificaat uploaden**.

![Persoonlijk certificaat uploaden in App Service](./media/configure-ssl-certificate/upload-private-cert.png)

In **PFX-certificaatbestand** selecteert u uw PFX-bestand. Typ in **Certificaatwachtwoord** het wachtwoord dat u hebt gemaakt toen u het PFX-bestand exporteerde. Klik op **Uploaden** als u klaar bent. 

Wanneer de bewerking is voltooid, ziet u het certificaat in de lijst **Certificaten met een persoonlijke sleutel**.

![Certificaat uploaden is voltooid](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Als u een aangepast domein met dit certificaat wilt beveiligen, moet u nog wel een certificaatbinding maken. Volg de stappen in [Binding maken](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-public-certificate"></a>Een openbaar certificaat uploaden

Openbare certificaten worden ondersteund in de indeling *.cer*. 

Selecteer in het linkermenu in <a href="https://portal.azure.com" target="_blank">Azure Portal</a> de optie **App Services** >  **\<app-name>** .

Klik in de navigatie links in uw app op **TLS/SSL-instellingen** > **Openbare certificaten (.cer)**  > **Certificaat met een openbare sleutel uploaden**.

Typ in **Naam** een naam voor het certificaat. Selecteer in **CER-certificaatbestand** uw CER-bestand.

Klik op **Uploaden**.

![Openbaar certificaat uploaden in App Service](./media/configure-ssl-certificate/upload-public-cert.png)

Nadat het certificaat is geüpload, kopieert u de vingerafdruk van het certificaat en ziet u [Het certificaat toegankelijk maken](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>App Service-certificaten beheren

In deze sectie wordt beschreven hoe u een App Service certificaat beheert dat u in [Een App Service-certificaat importeren](#import-an-app-service-certificate) hebt gekocht.

- [Certificaat opnieuw versleutelen](#rekey-certificate)
- [Certificaat verlengen](#renew-certificate)
- [Certificaat exporteren](#export-certificate)
- [Certificaat verwijderen](#delete-certificate)

### <a name="rekey-certificate"></a>Certificaat opnieuw versleutelen

Als u denkt dat de persoonlijke sleutel van uw certificaat is gecompromitteerd, kunt u uw certificaat opnieuw versleutelen. Selecteer het certificaat op de pagina [App Service-certificaten](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) en selecteer vervolgens **Opnieuw versleutelen en synchroniseren** in de navigatie links.

Klik op **Opnieuw versleutelen** om het proces te starten. Dit proces kan 1-10 minuten duren.

![Een App Service-certificaat opnieuw versleutelen](./media/configure-ssl-certificate/rekey-app-service-cert.png)

Als u het certificaat opnieuw versleutelt, wordt het certificaat gedistribueerd met een nieuw certificaat dat is verleend door de certificeringsinstantie.

Nadat het opnieuw versleutelen is voltooid, klikt u op **Synchroniseren**. Met de synchronisatiebewerking worden de hostname-bindingen voor het certificaat in App Service automatisch bijgewerkt zonder dat er downtime voor uw apps wordt veroorzaakt.

> [!NOTE]
> Als u niet op **Synchroniseren** klikt, wordt het certificaat automatisch binnen 48 uur gesynchroniseerd.

### <a name="renew-certificate"></a>Certificaat verlengen

U kunt het automatisch verlengen van uw certificaat op elk gewenst moment inschakelen door het certificaat te selecteren op de pagina [App Service-certificaten](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) en vervolgens op **Instellingen voor automatisch verlengen** in de navigatie links te klikken. App Service-certificaten hebben standaard een geldigheidsduur van één jaar.

Selecteer **Aan** en klik op **Opslaan**. Voor certificaten kan 60 dagen vóór de vervaldatum automatisch het verlengen worden gestart als u automatische verlenging hebt ingeschakeld.

![App Service-certificaat automatisch verlengen](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Als u het certificaat handmatig wilt verlengen, klikt u op **Handmatig verlengen**. U kunt aanvragen om uw certificaat 60 dagen vóór de vervaldatum handmatig te verlengen.

Nadat het verlengen is voltooid, klikt u op **Synchroniseren**. Met de synchronisatiebewerking worden de hostname-bindingen voor het certificaat in App Service automatisch bijgewerkt zonder dat er downtime voor uw apps wordt veroorzaakt.

> [!NOTE]
> Als u niet op **Synchroniseren** klikt, wordt het certificaat automatisch binnen 48 uur gesynchroniseerd.

### <a name="export-certificate"></a>Certificaat exporteren

Omdat een App Service-certificaat een [Key Vault-geheim](../key-vault/general/about-keys-secrets-certificates.md) is, kunt u een PFX-kopie ervan exporteren en deze gebruiken voor andere Azure-services of buiten Azure.

Als u het App Service-certificaat als een PFX-bestand wilt exporteren, voert u de volgende opdrachten uit in [Cloud Shell](https://shell.azure.com). U kunt deze ook lokaal uitvoeren als u [Azure CLI hebt geïnstalleerd](/cli/azure/install-azure-cli). Vervang de tijdelijke aanduidingen door de namen die u bij [het maken van het App Service certificaat](#start-certificate-order) hebt gebruikt.

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

Het gedownloade *appservicecertificate.pfx*-bestand is een onbewerkt PKCS12-bestand met zowel de openbare als persoonlijke certificaten. In elke prompt gebruikt u een lege tekenreeks voor het importwachtwoord en de PEM-wachtwoordzin.

### <a name="delete-certificate"></a>Certificaat verwijderen 

Het verwijderen van een App Service-certificaat is definitief en onomkeerbaar. Als een App Service Certificate-resource wordt verwijderd, wordt het certificaat ingetrokken. Elke binding in App Service met dit certificaat wordt ongeldig. Azure plaatst een vergrendeling op het certificaat om onbedoelde verwijdering te voorkomen. Als u een App Service-certificaat wilt verwijderen, moet u eerst de vergrendeling op het certificaat verwijderen.

Selecteer het certificaat op de pagina [App Service-certificaten](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) en selecteer vervolgens **Vergrendelingen** in de navigatie links.

Zoek de vergrendeling van het certificaat met het vergrendelingstype **Verwijderen**. Selecteer rechts hiervan **Verwijderen**.

![Vergrendeling voor App Service-certificaat verwijderen](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

U kunt nu het App Service-certificaat verwijderen. Selecteer **Overzicht** > **Verwijderen** in de navigatie links. Typ in het bevestigingsvenster de naam van het certificaat en selecteer **OK**.

## <a name="automate-with-scripts"></a>Automatiseren met scripts

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Meer bronnen

* [Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service](configure-ssl-bindings.md)
* [HTTPS afdwingen](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 afdwingen](configure-ssl-bindings.md#enforce-tls-versions)
* [Een TLS/SSL-certificaat gebruiken in uw code in Azure App Service](configure-ssl-certificate-in-code.md)
* [Veelgestelde vragen: App Service-certificaten](./faq-configuration-and-management.md)
