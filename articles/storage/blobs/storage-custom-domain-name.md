---
title: Een aangepast domein toewijzen aan een Azure Blob Storage-eindpunt
titleSuffix: Azure Storage
description: Een aangepast domein toewijzen aan een Blob Storage-of Web-eind punt in een Azure-opslag account.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2021
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 52fc7b9c1229421fd46b8110857a0a7a8a4f916a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520422"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Een aangepast domein toewijzen aan een Azure Blob Storage-eindpunt

U kunt een aangepast domein toewijzen aan een BLOB service-eind punt of een [statisch website](storage-blob-static-website.md) -eind punt. 

> [!NOTE] 
> Deze toewijzing werkt alleen voor subdomeinen (bijvoorbeeld: `www.contoso.com` ). Als u wilt dat uw web-eind punt beschikbaar is in het hoofd domein (bijvoorbeeld: `contoso.com` ), moet u Azure CDN gebruiken. Zie de sectie [een aangepast domein met https-functionaliteit toewijzen](#enable-https) in dit artikel voor meer informatie. Omdat u naar deze sectie van dit artikel gaat om het hoofd domein van uw aangepaste domein in te scha kelen, is de stap in die sectie voor het inschakelen van HTTPS optioneel. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Een aangepast domein toewijzen met alleen HTTP ingeschakeld

Deze methode is eenvoudiger, maar biedt alleen HTTP-toegang. Als het opslag account is geconfigureerd voor het [vereisen van beveiligde overdracht](../common/storage-require-secure-transfer.md) via https, moet u https-toegang inschakelen voor uw aangepaste domein. 

Als u HTTPS-toegang wilt inschakelen, raadpleegt u de sectie [een aangepast domein met https-functionaliteit toewijzen](#enable-https) in dit artikel. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>Een aangepast domein toewijzen

> [!IMPORTANT]
> Uw aangepaste domein is tijdens het volt ooien van de configuratie kort niet beschikbaar voor gebruikers. Als uw domein momenteel een toepassing ondersteunt met een Service Level Agreement (SLA) die geen downtime vereist, volgt u de stappen in de sectie [een aangepast domein met nul downtime toewijzen](#zero-down-time) in dit artikel om ervoor te zorgen dat gebruikers toegang hebben tot uw domein terwijl de DNS-toewijzing plaatsvindt.

Als u zich geen zorgen maakt dat het domein kort niet beschikbaar is voor uw gebruikers, voert u de volgende stappen uit.

: heavy_check_mark: stap 1: de hostnaam van uw opslag eindpunt ophalen.

: heavy_check_mark: stap 2: Maak een CNAME-record (canonieke naam) met uw domein provider.

: heavy_check_mark: stap 3: het aangepaste domein bij Azure registreren. 

: heavy_check_mark: stap 4: uw aangepaste domein testen.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Stap 1: de hostnaam van uw opslag eindpunt ophalen 

De hostnaam is de URL van het opslag eindpunt zonder de protocol-id en de afsluitende slash. 

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw opslag account.

2. Selecteer in het menu venster onder **instellingen** de optie **Eigenschappen**.  

3. Kopieer de waarde van het **eind punt van de primaire BLOB-service** of het **eind punt van de primaire statische website** naar een tekst bestand. 
  
   > [!NOTE]
   > Het Data Lake Storage-eind punt wordt niet ondersteund (bijvoorbeeld: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Verwijder de protocol-id (bijvoorbeeld: `HTTPS` ) en de afsluitende slash uit die teken reeks. De volgende tabel bevat voor beelden.

   | Type eind punt |  endpoint | hostnaam |
   |------------|-----------------|-------------------|
   |BLOB-service  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statische website  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Stel deze waarde in voor later.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Stap 2: Maak een canonieke-naam record (CNAME) met uw domein provider

Maak een CNAME-record die verwijst naar de hostnaam. Een CNAME-record is een type Domain Name System (DNS)-record dat een naam van een bron domein toewijst aan een doel domein naam.

1. Meld u aan bij de website van uw domein registratie service en ga vervolgens naar de pagina voor het beheren van de DNS-instelling.

   Mogelijk vindt u de pagina in een sectie met de naam **domein naam**, **DNS** of **naam server beheer**.

2. Zoek de sectie voor het beheren van CNAME-records. 

   Mogelijk moet u naar een pagina met geavanceerde instellingen gaan en zoeken naar **CNAME**, **alias** of **subdomeinen**.

3. Maak een CNAME-record. Geef als onderdeel van deze record de volgende items op: 

   - De alias van het subdomein, zoals `www` of `photos` . Het subdomein is vereist. hoofd domeinen worden niet ondersteund. 
      
   - De hostnaam die u hebt verkregen in de sectie [hostnaam van uw opslag eindpunt ophalen](#endpoint) eerder in dit artikel. 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Stap 3: uw aangepaste domein bij Azure registreren

##### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw opslag account.

2. Selecteer in het menu venster onder **BLOB-service** de optie **aangepast domein**.

   > [!NOTE]
   > Deze optie wordt niet weer gegeven in accounts waarvoor de functie voor hiërarchische naam ruimte is ingeschakeld. Voor deze accounts gebruikt u Power shell of de Azure CLI om deze stap te volt ooien.

   ![optie voor aangepast domein](./media/storage-custom-domain-name/custom-domain-button.png "aangepast domein")

   Het deel venster **aangepast domein** wordt geopend.

3. Voer in het tekstvak **domein naam** de naam in van uw aangepaste domein, met inbegrip van het subdomein  
   
   Als uw domein bijvoorbeeld *contoso.com* is en uw subdomein alias is *www*, voert u in `www.contoso.com` . Als uw subdomein *Foto's* is, voert u in `photos.contoso.com` .

4. Als u het aangepaste domein wilt registreren, klikt u op de knop **Opslaan** .

   Nadat de CNAME-record door de DNS (Domain name servers) is door gegeven, en als uw gebruikers over de juiste machtigingen beschikken, kunnen ze BLOB-gegevens weer geven met behulp van het aangepaste domein.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Voer de volgende PowerShell-opdracht

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $false
```

- Vervang de `<resource-group-name>` tijdelijke aanduiding door de naam van de resource groep.

- Vervang de `<storage-account-name>` tijdelijke aanduiding door de naam van het opslag account.

- Vervang de `<custom-domain-name>` tijdelijke aanduiding door de naam van uw aangepaste domein, inclusief het subdomein.

  Als uw domein bijvoorbeeld *contoso.com* is en uw subdomein alias is *www*, voert u in `www.contoso.com` . Als uw subdomein *Foto's* is, voert u in `photos.contoso.com` .

Nadat de CNAME-record door de DNS (Domain name servers) is door gegeven, en als uw gebruikers over de juiste machtigingen beschikken, kunnen ze BLOB-gegevens weer geven met behulp van het aangepaste domein.

##### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Voer de volgende PowerShell-opdracht

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain false
  ```

- Vervang de `<resource-group-name>` tijdelijke aanduiding door de naam van de resource groep.

- Vervang de `<storage-account-name>` tijdelijke aanduiding door de naam van het opslag account.

- Vervang de `<custom-domain-name>` tijdelijke aanduiding door de naam van uw aangepaste domein, inclusief het subdomein.

  Als uw domein bijvoorbeeld *contoso.com* is en uw subdomein alias is *www*, voert u in `www.contoso.com` . Als uw subdomein *Foto's* is, voert u in `photos.contoso.com` .

Nadat de CNAME-record door de DNS (Domain name servers) is door gegeven, en als uw gebruikers over de juiste machtigingen beschikken, kunnen ze BLOB-gegevens weer geven met behulp van het aangepaste domein.

---

#### <a name="step-4-test-your-custom-domain"></a>Stap 4: uw aangepaste domein testen

Om te bevestigen dat uw aangepaste domein is toegewezen aan het eind punt van de BLOB-service, maakt u een BLOB in een open bare container in uw opslag account. Ga in een webbrowser naar de blob met behulp van een URI in de volgende indeling: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Als u bijvoorbeeld toegang wilt krijgen tot een webformulier in de `myforms` container in het aangepaste subdomein *Photos.contoso.com* , kunt u de volgende URI gebruiken: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>Een aangepast domein toewijzen met een downtime van nul

> [!NOTE]
> Als u zich geen zorgen maakt dat het domein kort niet beschikbaar is voor uw gebruikers, kunt u de stappen in de sectie [een aangepast domein toewijzen](#map-a-domain) van dit artikel gebruiken. Het is een eenvoudigere benadering met minder stappen.  

Als uw domein momenteel een toepassing ondersteunt met een Service Level Agreement (SLA) die geen downtime vereist, voert u de volgende stappen uit om ervoor te zorgen dat gebruikers toegang hebben tot uw domein terwijl de DNS-toewijzing plaatsvindt. 

: heavy_check_mark: stap 1: de hostnaam van uw opslag eindpunt ophalen.

: heavy_check_mark: stap 2: Maak een tussenliggende CNAME-record (canonieke naam) met uw domein provider.

: heavy_check_mark: stap 3: het aangepaste domein vooraf registreren bij Azure.

: heavy_check_mark: stap 4: Maak een CNAME-record met uw domein provider.

: heavy_check_mark: stap 5: uw aangepaste domein testen.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Stap 1: de hostnaam van uw opslag eindpunt ophalen 

De hostnaam is de URL van het opslag eindpunt zonder de protocol-id en de afsluitende slash. 

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw opslag account.

2. Selecteer in het menu venster onder **instellingen** de optie **Eigenschappen**.  

3. Kopieer de waarde van het **eind punt van de primaire BLOB-service** of het **eind punt van de primaire statische website** naar een tekst bestand. 

   > [!NOTE]
   > Het Data Lake Storage-eind punt wordt niet ondersteund (bijvoorbeeld: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Verwijder de protocol-id (bijvoorbeeld: `HTTPS` ) en de afsluitende slash uit die teken reeks. De volgende tabel bevat voor beelden.

   | Type eind punt |  endpoint | hostnaam |
   |------------|-----------------|-------------------|
   |BLOB-service  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statische website  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Stel deze waarde in voor later.

#### <a name="step-2-create-an-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Stap 2: een tussenliggende CNAME-record (canonieke naam) maken met uw domein provider

Maak een tijdelijke CNAME-record die verwijst naar de naam van uw host. Een CNAME-record is een soort DNS-record dat een brondomeinnaam toewijst aan een doeldomeinnaam.

1. Meld u aan bij de website van uw domein registratie service en ga vervolgens naar de pagina voor het beheren van de DNS-instelling.

   Mogelijk vindt u de pagina in een sectie met de naam **domein naam**, **DNS** of **naam server beheer**.

2. Zoek de sectie voor het beheren van CNAME-records. 

   Mogelijk moet u naar een pagina met geavanceerde instellingen gaan en zoeken naar **CNAME**, **alias** of **subdomeinen**.

3. Maak een CNAME-record. Geef als onderdeel van deze record de volgende items op: 

   - De alias van het subdomein, zoals `www` of `photos` . Het subdomein is vereist. hoofd domeinen worden niet ondersteund.

     Voeg het `asverify` subdomein toe aan de alias. Bijvoorbeeld: `asverify.www` of `asverify.photos` .
       
   - De hostnaam die u hebt verkregen in de sectie [hostnaam van uw opslag eindpunt ophalen](#endpoint) eerder in dit artikel. 

     Voeg het subdomein toe `asverify` aan de naam van de host. Bijvoorbeeld: `asverify.mystorageaccount.blob.core.windows.net`.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Stap 3: uw aangepaste domein vooraf registreren bij Azure

Wanneer u uw aangepaste domein vooraf registreert bij Azure, staat u toe dat Azure uw aangepaste domein herkent zonder dat u de DNS-record voor het domein hoeft te wijzigen. Op die manier, wanneer u de DNS-record voor het domein wijzigt, wordt deze toegewezen aan het BLOB-eind punt zonder downtime.

##### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw opslag account.

2. Selecteer in het menu venster onder **BLOB-service** de optie **aangepast domein**.

   > [!NOTE]
   > Deze optie wordt niet weer gegeven in accounts waarvoor de functie voor hiërarchische naam ruimte is ingeschakeld. Voor deze accounts gebruikt u Power shell of de Azure CLI om deze stap te volt ooien.

   ![optie voor aangepast domein](./media/storage-custom-domain-name/custom-domain-button.png "aangepast domein")

   Het deel venster **aangepast domein** wordt geopend.

3. Voer in het tekstvak **domein naam** de naam in van uw aangepaste domein, met inbegrip van het subdomein  
   
   Als uw domein bijvoorbeeld *contoso.com* is en uw subdomein alias is *www*, voert u in `www.contoso.com` . Als uw subdomein *Foto's* is, voert u in `photos.contoso.com` .

4. Schakel het selectie vakje **indirecte CNAME-validatie gebruiken** in.

5. Als u het aangepaste domein wilt registreren, klikt u op de knop **Opslaan** .
  
   Als de registratie is geslaagd, ontvangt de portal een melding dat uw opslag account is bijgewerkt. Uw aangepaste domein is geverifieerd door Azure, maar het verkeer naar uw domein wordt nog niet doorgestuurd naar uw opslag account totdat u een CNAME-record met uw domein provider hebt gemaakt. Dit doet u in de volgende sectie.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Voer de volgende PowerShell-opdracht

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $true
```

- Vervang de `<resource-group-name>` tijdelijke aanduiding door de naam van de resource groep.

- Vervang de `<storage-account-name>` tijdelijke aanduiding door de naam van het opslag account.

- Vervang de `<custom-domain-name>` tijdelijke aanduiding door de naam van uw aangepaste domein, inclusief het subdomein.

  Als uw domein bijvoorbeeld *contoso.com* is en uw subdomein alias is *www*, voert u in `www.contoso.com` . Als uw subdomein *Foto's* is, voert u in `photos.contoso.com` .

Verkeer naar uw domein wordt nog niet doorgestuurd naar uw opslag account totdat u een CNAME-record met uw domein provider maakt. Dit doet u in de volgende sectie.

##### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Voer de volgende PowerShell-opdracht

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain true
  ```

- Vervang de `<resource-group-name>` tijdelijke aanduiding door de naam van de resource groep.

- Vervang de `<storage-account-name>` tijdelijke aanduiding door de naam van het opslag account.

- Vervang de `<custom-domain-name>` tijdelijke aanduiding door de naam van uw aangepaste domein, inclusief het subdomein.

  Als uw domein bijvoorbeeld *contoso.com* is en uw subdomein alias is *www*, voert u in `www.contoso.com` . Als uw subdomein *Foto's* is, voert u in `photos.contoso.com` .

Verkeer naar uw domein wordt nog niet doorgestuurd naar uw opslag account totdat u een CNAME-record met uw domein provider maakt. Dit doet u in de volgende sectie.

---

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Stap 4: Maak een CNAME-record met uw domein provider

Maak een tijdelijke CNAME-record die verwijst naar de naam van uw host.

1. Meld u aan bij de website van uw domein registratie service en ga vervolgens naar de pagina voor het beheren van de DNS-instelling.

   Mogelijk vindt u de pagina in een sectie met de naam **domein naam**, **DNS** of **naam server beheer**.

2. Zoek de sectie voor het beheren van CNAME-records. 

   Mogelijk moet u naar een pagina met geavanceerde instellingen gaan en zoeken naar **CNAME**, **alias** of **subdomeinen**.

3. Maak een CNAME-record. Geef als onderdeel van deze record de volgende items op: 

   - De alias van het subdomein, zoals `www` of `photos` . Het subdomein is vereist. hoofd domeinen worden niet ondersteund.
      
   - De hostnaam die u hebt verkregen in de sectie [hostnaam van uw opslag eindpunt ophalen](#endpoint-2) eerder in dit artikel. 

#### <a name="step-5-test-your-custom-domain"></a>Stap 5: uw aangepaste domein testen

Om te bevestigen dat uw aangepaste domein is toegewezen aan het eind punt van de BLOB-service, maakt u een BLOB in een open bare container in uw opslag account. Ga in een webbrowser naar de blob met behulp van een URI in de volgende indeling: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Als u bijvoorbeeld toegang wilt krijgen tot een webformulier in de `myforms` container in het aangepaste subdomein *Photos.contoso.com* , kunt u de volgende URI gebruiken: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Een aangepaste domein toewijzing verwijderen

Als u een aangepaste domein toewijzing wilt verwijderen, moet u de registratie van het aangepaste domein ongedaan maken. Gebruik een van de volgende procedures.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw opslag account.

2. Selecteer in het menu venster onder **BLOB-service** de optie **aangepast domein**.  
   Het deel venster **aangepast domein** wordt geopend.

3. Wis de inhoud van het tekstvak met de aangepaste domein naam.

4. Selecteer de knop **Opslaan**.

Nadat het aangepaste domein is verwijderd, ziet u een portal melding dat uw opslag account is bijgewerkt.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u een aangepaste domein registratie wilt verwijderen, gebruikt u de Power shell [-cmdlet Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) en geeft u een lege teken reeks ( `""` ) op voor de `-CustomDomainName` argument waarde.

* Opdracht indeling:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Opdracht voorbeeld:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een aangepaste domein registratie wilt verwijderen, gebruikt u de opdracht [AZ Storage account update](/cli/azure/storage/account) CLI en geeft u een lege teken reeks ( `""` ) op voor de `--custom-domain` argument waarde.

* Opdracht indeling:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Opdracht voorbeeld:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>Een aangepast domein toewijzen waarvoor HTTPS is ingeschakeld

Deze benadering omvat meer stappen, maar biedt HTTPS-toegang. 

Als u geen gebruikers nodig hebt om toegang te krijgen tot uw BLOB of webinhoud met behulp van HTTPS, raadpleegt u de sectie [een aangepast domein toewijzen met alleen HTTP-ingeschakeld](#enable-http) in dit artikel. 

1. Schakel [Azure CDN](../../cdn/cdn-overview.md) in op uw BLOB of Web-eind punt. 

   Zie voor een Blob Storage-eind punt [een Azure-opslag account integreren met Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md). 

   Zie [een statische website integreren met Azure CDN](static-website-content-delivery-network.md)voor een statisch website-eind punt.

2. [Azure CDN inhoud toewijzen aan een aangepast domein](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Schakel HTTPS in voor een Azure CDN aangepast domein](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > Wanneer u uw statische website bijwerkt, moet u ervoor zorgen dat de inhoud in de cache op de CDN-rand servers wordt gewist door het CDN-eind punt te verwijderen. Zie voor meer informatie [Een Azure CDN-eindpunt leegmaken](../../cdn/cdn-purge-endpoint.md).

4. Beschrijving Lees de volgende richt lijnen:

   * [SAS-tokens (Shared Access Signature) met Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#shared-access-signatures).

   * [Http-naar-https-omleiding met Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection).

   * [Prijzen en facturering bij het gebruik van Blob Storage met Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection).

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het hosten van statische websites in Azure Blob-opslag](storage-blob-static-website.md)