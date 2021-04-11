---
title: Een statische website hosten in Azure Storage
description: Meer informatie over hoe u statische inhoud (HTML-, CSS-, java script-en afbeeldings bestanden) rechtstreeks vanuit een container in een Azure Storage GPv2-account kunt verwerken.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2ba0e836f1be97681d677158f5cb81c5b033e915
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008498"
---
# <a name="host-a-static-website-in-azure-storage"></a>Een statische website hosten in Azure Storage

U kunt statische inhoud (HTML-, CSS-, java script-en afbeeldings bestanden) rechtstreeks vanuit een container in een v2-of [BlockBlobStorage](storage-blob-create-account-block-blob.md) -account voor [algemeen gebruik](../common/storage-account-create.md) verwerken. Zie voor meer informatie [statische website-hosting in azure Storage](storage-blob-static-website.md).

Dit artikel laat u zien hoe u statische website-hosting kunt inschakelen met behulp van de Azure Portal, de Azure CLI of Power shell.

## <a name="enable-static-website-hosting"></a>Statisch website hosting inschakelen

Het hosten van statische websites is een functie die u moet inschakelen op het opslag account.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) om aan de slag te gaan.

2. Zoek uw opslagaccount en geef het accountoverzicht weer.

3. Selecteer **Statische website** om de configuratiepagina voor statische websites weer te geven.

4. Selecteer **Ingeschakeld** om het hosten van statische websites in te schakelen voor het opslagaccount.

5. Geef in het veld **naam van index document** een standaard index pagina op (bijvoorbeeld: *index.html*). 

   De standaardindexpagina wordt weergegeven wanneer een gebruiker naar de hoofdmap van uw statische website navigeert.  

6. Geef in het veld **pad naar fout document** een standaardfouten pagina op (bijvoorbeeld: *404.html*). 

   De standaardfoutpagina wordt weergegeven wanneer een gebruiker probeert te navigeren naar een pagina die niet in uw statische website bestaat.

7. Klik op **Opslaan**. Het eindpunt voor uw statische website wordt nu weergegeven in de Azure-portal. 

    ![Het hosten van een statische website inschakelen voor een opslagaccount](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

<a id="cli"></a>

U kunt statisch website hosting inschakelen met behulp van de [Azure Command-Line interface (CLI)](/cli/azure/).

1. Open eerst de [Azure Cloud shell](../../cloud-shell/overview.md)of als u de Azure cli lokaal hebt [geïnstalleerd](/cli/azure/install-azure-cli) , opent u een opdracht console toepassing zoals Windows Power shell.

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account dat als host voor uw statische website gaat.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Vervang de `<subscription-id>` waarde van de tijdelijke aanduiding door de id van uw abonnement.

3. Schakel statisch website hosting in.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

   * Vervang de `<error-document-name>` tijdelijke aanduiding door de naam van het fout document dat voor gebruikers wordt weer gegeven wanneer een browser een pagina op uw site aanvraagt die niet bestaat.

   * Vervang de `<index-document-name>` tijdelijke aanduiding door de naam van het index document. Dit document is vaak "index.html".

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

U kunt statisch website hosting inschakelen met behulp van de module Azure PowerShell.

1. Open een Windows Power shell-opdracht venster.

2. Controleer of u Azure PowerShell module AZ versie 0,7 of hoger hebt.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps).

3. Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

   ```powershell
   Connect-AzAccount
   ```

4. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account dat als host voor uw statische website gaat.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Vervang de `<subscription-id>` waarde van de tijdelijke aanduiding door de id van uw abonnement.

5. Haal de context van het opslag account op waarmee het opslag account wordt gedefinieerd dat u wilt gebruiken.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Vervang de `<resource-group-name>` waarde van de tijdelijke aanduiding door de naam van uw resource groep.

   * Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

6. Schakel statisch website hosting in.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Vervang de `<error-document-name>` tijdelijke aanduiding door de naam van het fout document dat voor gebruikers wordt weer gegeven wanneer een browser een pagina op uw site aanvraagt die niet bestaat.

   * Vervang de `<index-document-name>` tijdelijke aanduiding door de naam van het index document. Dit document is vaak "index.html".

---

## <a name="upload-files"></a>Bestanden uploaden 

### <a name="portal"></a>[Portal](#tab/azure-portal)

In deze instructies wordt uitgelegd hoe u bestanden kunt uploaden met behulp van de versie van Storage Explorer die wordt weer gegeven in de Azure Portal. U kunt echter ook de versie van [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruiken die buiten de Azure portal wordt uitgevoerd. U kunt [AzCopy](../common/storage-use-azcopy-v10.md), Power shell, CLI of een aangepaste toepassing gebruiken die bestanden kan uploaden naar de **$Web** container van uw account. Zie [zelf studie: een statische website op Blob Storage hosten](./storage-blob-static-website-host.md)voor een stapsgewijze zelf studie die bestanden uploadt met Visual Studio code.

1. Selecteer **Storage Explorer (preview)**.

2. Vouw het knoop punt **BLOB containers** uit en selecteer vervolgens de container **$Web** .

3. Kies de knop **uploaden** om bestanden te uploaden.

   ![Bestanden uploaden](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Als u van plan bent om de inhoud van het bestand weer te geven in de browser, moet u ervoor zorgen dat het inhouds type van dat bestand is ingesteld op `text/html` . 

   ![Inhouds typen controleren](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Storage Explorer wordt deze eigenschap automatisch ingesteld op `text/html` voor gebruikelijke, herkende extensies zoals `.html` . In sommige gevallen moet u dit echter zelf instellen. Als u deze eigenschap niet instelt op `text/html` , wordt gebruikers door de browser gevraagd het bestand te downloaden in plaats van de inhoud te renderen. Als u deze eigenschap wilt instellen, klikt u met de rechter muisknop op het bestand en klikt u vervolgens op **Eigenschappen**.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Objecten uploaden naar de *$Web* container vanuit een bronmap.

In dit voor beeld wordt ervan uitgegaan dat u opdrachten uit Azure Cloud Shell sessie uitvoert.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d '$web' --account-name <storage-account-name>
```

> [!NOTE] 
> Als gebruikers van de browser wordt gevraagd om het bestand te downloaden in plaats van de inhoud weer te geven, kunt u toevoegen `--content-type 'text/html; charset=utf-8'` aan de opdracht. 

* Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

* Vervang de `<source-path>` tijdelijke aanduiding door een pad naar de locatie van de bestanden die u wilt uploaden.

> [!NOTE]
> Als u een locatie-installatie van Azure CLI gebruikt, kunt u het pad naar een wille keurige locatie op de lokale computer gebruiken (bijvoorbeeld: `C:\myFolder` .
>
> Als u Azure Cloud Shell gebruikt, moet u verwijzen naar een bestands share die zichtbaar is voor de Cloud Shell. Deze locatie kan de bestands share zijn van de Cloud share zelf of een bestaande bestands share die u koppelt van de Cloud Shell. Zie [bestanden in azure Cloud shell persistent](../../cloud-shell/persisting-shell-storage.md)maken voor meer informatie over hoe u dit doet.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Objecten uploaden naar de *$Web* container vanuit een bronmap.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

> [!NOTE] 
> Als gebruikers van de browser wordt gevraagd om het bestand te downloaden in plaats van de inhoud weer te geven, kunt u toevoegen `-Properties @{ ContentType = "text/html; charset=utf-8";}` aan de opdracht.

* Vervang de `<path-to-file>` waarde van de tijdelijke aanduiding door het volledig gekwalificeerde pad naar het bestand dat u wilt uploaden (bijvoorbeeld: `C:\temp\index.html` ).

* Vervang de `<blob-name>` waarde van de tijdelijke aanduiding door de naam die u de resulterende BLOB wilt geven (bijvoorbeeld: `index.html` ).

---

<a id="portal-find-url"></a>

## <a name="find-the-website-url"></a>De URL van de website zoeken

U kunt de pagina's van uw site vanuit een browser weer geven met behulp van de open bare URL van de website.

### <a name="portal"></a>[Portal](#tab/azure-portal)

In het deel venster dat wordt weer gegeven naast de pagina account overzicht van uw opslag account, selecteert u **statische website**. De URL van uw site wordt weer gegeven in het veld voor het **primaire eind punt** .

![Metrische gegevens voor metrische gegevens van statische websites Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Zoek de open bare URL van uw statische website met behulp van de volgende opdracht:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

* Vervang de `<resource-group-name>` waarde van de tijdelijke aanduiding door de naam van uw resource groep.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Zoek de open bare URL van uw statische website met behulp van de volgende opdracht:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Vervang de `<resource-group-name>` waarde van de tijdelijke aanduiding door de naam van uw resource groep.

* Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

---

<a id="metrics"></a>

## <a name="enable-metrics-on-static-website-pages"></a>Metrische gegevens inschakelen op de vaste website pagina's

Wanneer u metrische gegevens hebt ingeschakeld, worden verkeers statistieken voor bestanden in de **$Web** container gerapporteerd in het dash board metrische gegevens.

1. Klik op **metrische gegevens** onder het gedeelte **monitor** van het menu opslag account.

   > [!div class="mx-imgBorder"]
   > ![Koppeling metrische gegevens](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Metrische gegevens worden gegenereerd door te hooken in verschillende metrische Api's. In de portal worden alleen de API-leden weer gegeven die binnen een bepaalde periode zijn gebruikt, zodat ze alleen kunnen worden gericht op leden die gegevens retour neren. Om ervoor te zorgen dat u het benodigde API-lid kunt selecteren, is de eerste stap om het tijds bestek uit te breiden.

2. Klik op de knop tijds bestek, kies een tijds bestek en klik vervolgens op **Toep assen**.

   ![Tijds bereik voor metrische gegevens van statische websites Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Selecteer **BLOB** in de vervolg keuzelijst *naam ruimte* .

   ![Naam ruimte voor metrische gegevens van statische websites Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Selecteer **vervolgens de waarde** voor uitgaand verkeer.

   ![Scherm opname van de Azure Storage voor de uitgevende gegevens van statische websites.](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Selecteer **som** uit de *aggregatie* kiezer.

   ![Aggregatie van metrische gegevens van statische websites Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Klik op de knop **filter toevoegen** en kies de naam van de **API** in de *Eigenschappen* kiezer.

   ![API-naam van metrische gegevens voor de Azure Storage van statische websites](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Schakel het selectie vakje naast **GetWebContent** in de *waarde* selector in om het meet rapport in te vullen.

   ![Azure Storage metrische gegevens van statische websites GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > Het selectie vakje **GetWebContent** wordt alleen weer gegeven als het lid van de API in een bepaalde periode is gebruikt. In de portal worden alleen de API-leden weer gegeven die binnen een bepaalde periode zijn gebruikt, zodat ze alleen kunnen worden gericht op leden die gegevens retour neren. Als u een specifiek API-lid niet kunt vinden in deze lijst, vouwt u het tijds bestek uit.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het configureren van een aangepast domein met uw statische website. Zie [een aangepast domein toewijzen aan een Azure Blob Storage-eind punt](storage-custom-domain-name.md).