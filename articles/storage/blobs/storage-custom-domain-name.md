---
title: Een aangepast domein toewijzen aan een Azure Blob Storage-eindpunt
titleSuffix: Azure Storage
description: Wijs een aangepast domein toe aan een Blob Storage of web-eindpunt in een Azure-opslagaccount.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2021
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 45ae3d80202bfb29074461f899798d278eb0895b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538360"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Een aangepast domein toewijzen aan een Azure Blob Storage-eindpunt

U kunt een aangepast domein aan een blobservice-eindpunt of een statisch [website-eindpunt](storage-blob-static-website.md) toevoegen. 

> [!NOTE] 
> Deze toewijzing werkt alleen voor subdomeinen (bijvoorbeeld: `www.contoso.com` ). Als u wilt dat uw web-eindpunt beschikbaar is in het hoofddomein (bijvoorbeeld: ), moet u `contoso.com` Azure CDN. Zie de sectie [Een aangepast domein met HTTPS-ingeschakeld in](#enable-https) dit artikel voor hulp. Omdat u naar dat gedeelte van dit artikel gaat om het hoofddomein van uw aangepaste domein in te stellen, is de stap in die sectie voor het inschakelen van HTTPS optioneel. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Een aangepast domein met alleen HTTP is ingeschakeld

Deze aanpak is eenvoudiger, maar maakt alleen HTTP-toegang mogelijk. Als het opslagaccount is geconfigureerd om veilige overdracht via HTTPS te [vereisen,](../common/storage-require-secure-transfer.md) moet u HTTPS-toegang inschakelen voor uw aangepaste domein. 

Zie de sectie Een aangepast domein met HTTPS-ingeschakeld inschakelen van dit artikel als u [HTTPS-toegang](#enable-https) wilt inschakelen. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>Een aangepast domein in kaart brengen

> [!IMPORTANT]
> Uw aangepaste domein is tijdelijk niet beschikbaar voor gebruikers terwijl u de configuratie voltooit. Als uw domein momenteel ondersteuning biedt voor een toepassing met een Service Level Agreement (SLA) die geen downtime vereist, volgt u de stappen in de sectie Een aangepast domein toewijzen met nul [downtime](#zero-down-time) van dit artikel om ervoor te zorgen dat gebruikers toegang hebben tot uw domein terwijl de DNS-toewijzing plaatsvindt.

Als u niet zeker weet dat het domein tijdelijk niet beschikbaar is voor uw gebruikers, volgt u deze stappen.

:heavy_check_mark: Stap 1: haal de hostnaam van uw opslag-eindpunt op.

:heavy_check_mark: Stap 2: maak een record met een canonieke naam (CNAME) met uw domeinprovider.

:heavy_check_mark: Stap 3: het aangepaste domein registreren bij Azure. 

:heavy_check_mark: Stap 4: uw aangepaste domein testen.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Stap 1: de hostnaam van uw opslag-eindpunt op halen 

De hostnaam is de URL van het opslag-eindpunt zonder de protocol-id en de schuine streep erts. 

1. Ga in [Azure Portal](https://portal.azure.com)naar uw opslagaccount.

2. Selecteer in het menuvenster onder **Instellingen** de optie **Eigenschappen.**  

3. Kopieer de waarde van het **primaire blobservice-eindpunt** of het eindpunt van de primaire **statische website** naar een tekstbestand. 
  
   > [!NOTE]
   > Het Data Lake Storage-eindpunt wordt niet ondersteund (bijvoorbeeld: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Verwijder de protocol-id (bijvoorbeeld: `HTTPS` ) en de schuine streep uit die tekenreeks. De volgende tabel bevat voorbeelden.

   | Type eindpunt |  endpoint | hostnaam |
   |------------|-----------------|-------------------|
   |blob-service  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statische website  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Stel deze waarde in voor later.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Stap 2: maak een record met een canonieke naam (CNAME) met uw domeinprovider

Maak een CNAME-record om naar uw hostnaam te wijzen. Een CNAME-record is een type DNS Domain Name System record dat een brondomeinnaam toe te voegen aan een doeldomeinnaam.

1. Meld u aan bij de website van uw domeinregistrar en ga vervolgens naar de pagina voor het beheren van de DNS-instelling.

   Mogelijk vindt u de pagina in een sectie met de naam **Domeinnaam,** **DNS** of **Naamserverbeheer.**

2. Zoek de sectie voor het beheren van CNAME-records. 

   Mogelijk moet u naar een pagina met geavanceerde instellingen gaan en zoeken naar **CNAME,** **Alias** of **Subdomeinen.**

3. Maak een CNAME-record. Geef als onderdeel van die record de volgende items op: 

   - De subdomeinalias, `www` zoals of `photos` . Het subdomein is vereist. Hoofddomeinen worden niet ondersteund. 
      
   - De hostnaam die u eerder in dit artikel hebt verkregen in de sectie De [hostnaam](#endpoint) van uw opslag-eindpunt op halen. 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Stap 3: uw aangepaste domein registreren bij Azure

##### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga in [Azure Portal](https://portal.azure.com)naar uw opslagaccount.

2. Selecteer in het menuvenster onder **Blob Service** de optie **Aangepast domein.**

   > [!NOTE]
   > Deze optie wordt niet weergegeven in accounts met de functie hiërarchische naamruimte ingeschakeld. Voor deze accounts gebruikt u PowerShell of de Azure CLI om deze stap te voltooien.

   ![optie aangepast domein](./media/storage-custom-domain-name/custom-domain-button.png "aangepast domein")

   Het **deelvenster Aangepast domein** wordt geopend.

3. Voer in **het tekstvak** Domeinnaam de naam van uw aangepaste domein in, inclusief het subdomein  
   
   Als uw domein bijvoorbeeld  is contoso.com uw subdomeinalias *www* is, voert u `www.contoso.com` in. Als uw subdomein photos is, *voert* u `photos.contoso.com` in.

4. Als u het aangepaste domein wilt registreren, kiest u **de knop** Opslaan.

   Nadat de CNAME-record is doorgegeven via de DNS (Domain Name Servers), en als uw gebruikers de juiste machtigingen hebben, kunnen ze blobgegevens weergeven met behulp van het aangepaste domein.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Voer de volgende PowerShell-opdracht

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $false
```

- Vervang de `<resource-group-name>` tijdelijke aanduiding door de naam van de resourcegroep.

- Vervang de `<storage-account-name>` tijdelijke aanduiding door de naam van het opslagaccount.

- Vervang de `<custom-domain-name>` tijdelijke aanduiding door de naam van uw aangepaste domein, inclusief het subdomein.

  Als uw domein bijvoorbeeld  is contoso.com uw subdomeinalias *www* is, voert u `www.contoso.com` in. Als uw subdomein photos is, *voert* u `photos.contoso.com` in.

Nadat de CNAME-record is doorgegeven via de DNS (Domain Name Servers), en als uw gebruikers de juiste machtigingen hebben, kunnen ze blobgegevens weergeven met behulp van het aangepaste domein.

##### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Voer de volgende PowerShell-opdracht

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain false
  ```

- Vervang de `<resource-group-name>` tijdelijke aanduiding door de naam van de resourcegroep.

- Vervang de `<storage-account-name>` tijdelijke aanduiding door de naam van het opslagaccount.

- Vervang de `<custom-domain-name>` tijdelijke aanduiding door de naam van uw aangepaste domein, met inbegrip van het subdomein.

  Als uw domein bijvoorbeeld  is contoso.com uw subdomeinalias *www* is, voert u `www.contoso.com` in. Als uw subdomein photos is, *voert* u `photos.contoso.com` in.

Nadat de CNAME-record is doorgegeven via de DNS (Domain Name Servers), en als uw gebruikers de juiste machtigingen hebben, kunnen ze blobgegevens weergeven met behulp van het aangepaste domein.

---

#### <a name="step-4-test-your-custom-domain"></a>Stap 4: uw aangepaste domein testen

Als u wilt controleren of uw aangepaste domein is toegeschreven aan uw blobservice-eindpunt, maakt u een blob in een openbare container binnen uw opslagaccount. Ga vervolgens in een webbrowser naar de blob met behulp van een URI in de volgende indeling: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Als u bijvoorbeeld toegang wilt krijgen tot een webformulier in de container in photos.contoso.com subdomein, kunt u `myforms` de volgende URI gebruiken: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>Een aangepast domein zonder downtime in kaart brengen

> [!NOTE]
> Als u niet zeker weet dat het domein tijdelijk niet beschikbaar is voor [](#map-a-domain) uw gebruikers, kunt u overwegen de stappen in de sectie Een aangepast domein in dit artikel toe te passen. Het is een eenvoudigere benadering met minder stappen.  

Als uw domein momenteel ondersteuning biedt voor een toepassing met een Service Level Agreement (SLA) die geen downtime vereist, volgt u deze stappen om ervoor te zorgen dat gebruikers toegang hebben tot uw domein terwijl de DNS-toewijzing plaatsvindt. 

:heavy_check_mark: Stap 1: haal de hostnaam van uw opslag-eindpunt op.

:heavy_check_mark: Stap 2: maak een intermediaire canonieke naamrecord (CNAME) met uw domeinprovider.

:heavy_check_mark: Stap 3: registreer het aangepaste domein vooraf bij Azure.

:heavy_check_mark: Stap 4: maak een CNAME-record met uw domeinprovider.

:heavy_check_mark: Stap 5: test uw aangepaste domein.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Stap 1: de hostnaam van uw opslag-eindpunt op halen 

De hostnaam is de URL van het opslag-eindpunt zonder de protocol-id en de schuine streep. 

1. Ga in [Azure Portal](https://portal.azure.com)naar uw opslagaccount.

2. Selecteer in het menuvenster onder **Instellingen** de optie **Eigenschappen.**  

3. Kopieer de waarde van het **primaire blobservice-eindpunt** of het eindpunt van de primaire **statische website** naar een tekstbestand. 

   > [!NOTE]
   > Het Data Lake Storage-eindpunt wordt niet ondersteund (bijvoorbeeld: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Verwijder de protocol-id (bijvoorbeeld: `HTTPS` ) en de schuine streep uit die tekenreeks. De volgende tabel bevat voorbeelden.

   | Type eindpunt |  endpoint | hostnaam |
   |------------|-----------------|-------------------|
   |blob-service  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statische website  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Stel deze waarde in voor later.

#### <a name="step-2-create-an-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Stap 2: maak een intermediaire record met een canonieke naam (CNAME) met uw domeinprovider

Maak een tijdelijke CNAME-record om naar de hostnaam te wijzen. Een CNAME-record is een soort DNS-record dat een brondomeinnaam toewijst aan een doeldomeinnaam.

1. Meld u aan bij de website van uw domeinregistrar en ga vervolgens naar de pagina voor het beheren van dns-instellingen.

   Mogelijk vindt u de pagina in een sectie met de naam **Domeinnaam,** **DNS** of **Naamserverbeheer.**

2. Zoek de sectie voor het beheren van CNAME-records. 

   Mogelijk moet u naar een pagina met geavanceerde instellingen gaan en zoeken naar **CNAME,** **Alias** of **Subdomeinen.**

3. Maak een CNAME-record. Geef als onderdeel van die record de volgende items op: 

   - De subdomeinalias zoals `www` of `photos` . Het subdomein is vereist. Hoofddomeinen worden niet ondersteund.

     Voeg het `asverify` subdomein toe aan de alias . Bijvoorbeeld: `asverify.www` of `asverify.photos` .
       
   - De hostnaam die u eerder in dit artikel hebt verkregen in de sectie De [hostnaam](#endpoint) van uw opslag-eindpunt verkrijgen. 

     Voeg het subdomein toe `asverify` aan de hostnaam. Bijvoorbeeld: `asverify.mystorageaccount.blob.core.windows.net`.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Stap 3: uw aangepaste domein vooraf registreren bij Azure

Wanneer u uw aangepaste domein vooraf registreert bij Azure, staat u Azure toe uw aangepaste domein te herkennen zonder dat u de DNS-record voor het domein moet wijzigen. Op die manier wordt de DNS-record voor het domein zonder uitvaltijd aan het blob-eindpunt toe te voegen wanneer u de DNS-record wijzigt.

##### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga in [Azure Portal](https://portal.azure.com)naar uw opslagaccount.

2. Selecteer in het menuvenster onder **Blob Service** de optie **Aangepast domein.**

   > [!NOTE]
   > Deze optie wordt niet weergegeven in accounts met de functie hiërarchische naamruimte ingeschakeld. Voor deze accounts gebruikt u PowerShell of de Azure CLI om deze stap te voltooien.

   ![aangepaste domeinoptie](./media/storage-custom-domain-name/custom-domain-button.png "aangepast domein")

   Het **deelvenster Aangepast domein** wordt geopend.

3. Voer in **het tekstvak** Domeinnaam de naam van uw aangepaste domein in, inclusief het subdomein  
   
   Als uw domein bijvoorbeeld  is contoso.com uw subdomeinalias *www* is, voert u `www.contoso.com` in. Als uw subdomein photos is, *voert* u `photos.contoso.com` in.

4. Schakel het **selectievakje Indirecte CNAME-validatie** gebruiken in.

5. Als u het aangepaste domein wilt registreren, kiest u **de knop** Opslaan.
  
   Als de registratie is geslaagd, wordt u via de portal op de hoogte gehouden dat uw opslagaccount is bijgewerkt. Uw aangepaste domein is geverifieerd door Azure, maar verkeer naar uw domein wordt nog niet doorgeleid naar uw opslagaccount totdat u een CNAME-record met uw domeinprovider maakt. U doet dat in de volgende sectie.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Voer de volgende PowerShell-opdracht

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $true
```

- Vervang de `<resource-group-name>` tijdelijke aanduiding door de naam van de resourcegroep.

- Vervang de `<storage-account-name>` tijdelijke aanduiding door de naam van het opslagaccount.

- Vervang de `<custom-domain-name>` tijdelijke aanduiding door de naam van uw aangepaste domein, inclusief het subdomein.

  Als uw domein bijvoorbeeld  is contoso.com uw subdomeinalias *www* is, voert u `www.contoso.com` in. Als uw subdomein photos is, *voert* u `photos.contoso.com` in.

Verkeer naar uw domein wordt nog niet doorgeleid naar uw opslagaccount totdat u een CNAME-record met uw domeinprovider maakt. U doet dat in de volgende sectie.

##### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Voer de volgende PowerShell-opdracht

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain true
  ```

- Vervang de `<resource-group-name>` tijdelijke aanduiding door de naam van de resourcegroep.

- Vervang de `<storage-account-name>` tijdelijke aanduiding door de naam van het opslagaccount.

- Vervang de `<custom-domain-name>` tijdelijke aanduiding door de naam van uw aangepaste domein, inclusief het subdomein.

  Als uw domein bijvoorbeeld  is contoso.com uw subdomeinalias *www* is, voert u `www.contoso.com` in. Als uw subdomein photos is, *voert* u `photos.contoso.com` in.

Verkeer naar uw domein wordt nog niet doorgeleid naar uw opslagaccount totdat u een CNAME-record met uw domeinprovider maakt. U doet dat in de volgende sectie.

---

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Stap 4: een CNAME-record maken met uw domeinprovider

Maak een tijdelijke CNAME-record om naar de hostnaam te wijzen.

1. Meld u aan bij de website van uw domeinregistrar en ga vervolgens naar de pagina voor het beheren van dns-instellingen.

   Mogelijk vindt u de pagina in een sectie met de naam **Domeinnaam,** **DNS** of **Naamserverbeheer.**

2. Zoek de sectie voor het beheren van CNAME-records. 

   Mogelijk moet u naar een pagina met geavanceerde instellingen gaan en zoeken naar **CNAME,** **Alias** of **Subdomeinen.**

3. Maak een CNAME-record. Geef als onderdeel van die record de volgende items op: 

   - De subdomeinalias, `www` zoals of `photos` . Het subdomein is vereist. Hoofddomeinen worden niet ondersteund.
      
   - De hostnaam die u eerder in dit artikel hebt verkregen in de sectie De [hostnaam](#endpoint-2) van uw opslag-eindpunt verkrijgen. 

#### <a name="step-5-test-your-custom-domain"></a>Stap 5: uw aangepaste domein testen

Als u wilt controleren of uw aangepaste domein is toegeschreven aan uw blobservice-eindpunt, maakt u een blob in een openbare container binnen uw opslagaccount. Ga vervolgens in een webbrowser naar de blob met behulp van een URI in de volgende indeling: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Als u bijvoorbeeld toegang wilt krijgen tot een webformulier in de container in photos.contoso.com subdomein, kunt u `myforms` de volgende URI gebruiken: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Een aangepaste domeintoewijzing verwijderen

Als u een aangepaste domeintoewijzing wilt verwijderen, moet u de registratie van het aangepaste domein ongedaan maken. Gebruik een van de volgende procedures.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga in [Azure Portal](https://portal.azure.com)naar uw opslagaccount.

2. Selecteer in het menuvenster onder **Blob Service** de optie **Aangepast domein.**  
   Het **deelvenster Aangepast domein** wordt geopend.

3. Leeg de inhoud van het tekstvak dat uw aangepaste domeinnaam bevat.

4. Selecteer de knop **Opslaan**.

Nadat het aangepaste domein is verwijderd, ziet u een portalmelding dat uw opslagaccount is bijgewerkt.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u een aangepaste domeinregistratie wilt verwijderen, gebruikt u de PowerShell-cmdlet [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) en geeft u een lege tekenreeks ( ) op `""` voor de `-CustomDomainName` argumentwaarde.

* Opdrachtindeling:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Opdrachtvoorbeeld:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een aangepaste domeinregistratie wilt verwijderen, gebruikt u de CLI-opdracht [az storage account update](/cli/azure/storage/account) en geeft u vervolgens een lege tekenreeks ( ) op voor de `""` `--custom-domain` argumentwaarde.

* Opdrachtindeling:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Opdrachtvoorbeeld:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>Een aangepast domein met HTTPS toe te staan

Deze aanpak omvat meer stappen, maar maakt HTTPS-toegang mogelijk. 

Als u gebruikers geen toegang nodig hebt tot uw blob- [](#enable-http) of webinhoud via HTTPS, gaat u naar de sectie Een aangepast domein met alleen HTTP-ingeschakelde domeinen in dit artikel. 

1. Schakel [Azure CDN](../../cdn/cdn-overview.md) in op uw blob- of web-eindpunt. 

   Zie Een Azure-Blob Storage integreren met een Azure CDN voor een [Azure CDN.](../../cdn/cdn-create-a-storage-account-with-cdn.md) 

   Zie Een statische website integreren met een statisch [website-eindpunt Azure CDN](static-website-content-delivery-network.md).

2. [Wijs Azure CDN toe aan een aangepast domein.](../../cdn/cdn-map-content-to-custom-domain.md)

3. [Schakel HTTPS in op een Azure CDN aangepast domein](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > Wanneer u uw statische website bij te werken, moet u ervoor zorgen dat inhoud in de cache op de CDN-randservers wordt gewisd door het CDN-eindpunt op te leegmaken. Zie voor meer informatie [Een Azure CDN-eindpunt leegmaken](../../cdn/cdn-purge-endpoint.md).

4. (Optioneel) Bekijk de volgende richtlijnen:

   * [SAS-tokens (Shared Access Signature) met Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#shared-access-signatures).

   * [HTTP-naar-HTTPS-omleiding met Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection).

   * [Prijzen en facturering bij gebruik van Blob Storage met Azure CDN](../../cdn/cdn-storage-custom-domain-https.md#pricing-and-billing).

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het hosten van statische websites in Azure Blob Storage](storage-blob-static-website.md)