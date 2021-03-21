---
title: 'Zelfstudie: Gegevens scannen met Azure Purview (preview)'
description: In deze zelfstudie voert u een starterkit uit om een gegevensdomein in te stellen en scant u vervolgens gegevens van gegevensbronnen in uw Azure Purview-catalogus.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 16692ac75f0ab6df0c8ee1bebef393848ca066b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676547"
---
# <a name="tutorial-scan-data-with-azure-purview-preview"></a>Zelfstudie: Gegevens scannen met Azure Purview (preview)

> [!IMPORTANT]
> Azure Purview is momenteel in PREVIEW. De [Aanvullende voorwaarden voor gebruik van Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) omvatten aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die in bèta of preview zijn of die anders nog niet algemeen beschikbaar zijn.

In deze *vijfdelige reeks zelfstudies* leert u de basisprincipes van het beheren van gegevens-governance van een gegevensdomein met behulp van Azure Purview (preview). De gegevens die u in dit deel van de zelfstudie maakt, worden gebruikt voor de rest van de reeks.

In deel 1 van deze zelfstudiereeks doet u het volgende:

> [!div class="checklist"]
>
> * Een gegevensdomein maken met verschillende Azure-gegevensresources.
> * Gegevens naar een catalogus scannen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* Een [Azure Purview-account](create-catalog-portal.md).
* [De starterkit](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip) waarmee uw gegevensdomein wordt geïmplementeerd.

> [!NOTE]
> De starterkit is alleen beschikbaar voor Windows.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-data-estate"></a>Een gegevensdomein maken

In deze sectie voert u de scripts van de starterkit uit om een gesimuleerd gegevensdomein te maken. Een gegevensdomein is een portfolio van alle gegevens die een bedrijf in eigendom heeft. Het script van de starterkit voert de volgende acties uit:

* Maakt een Azure Blob-opslagaccount en vult het account met gegevens.
* Maakt een Azure Data Lake Storage Gen2-account.
* Maakt een Azure Data Factory-exemplaar en koppelt het exemplaar aan Azure Purview.
* Stelt een copy-activiteitpijplijn in tussen de Azure Blob-opslag en Azure Data Lake Storage Gen2-accounts.
* Pusht de gekoppelde herkomst van Azure Data Factory naar Azure Purview.

### <a name="prepare-to-run-the-starter-kit"></a>Voorbereiden op het uitvoeren van de starterkit

Volg deze stappen om de clientsoftware van de starterkit op uw Windows-computer in te stellen:

1. [Download de starterkit](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip) en extraheer de inhoud ervan naar de gewenste locatie.


1. Voer op uw computer **PowerShell** in het zoekvak op de Windows-taakbalk in. Klik met de rechtermuisknop in de zoeklijst op **Windows PowerShell** en selecteer **Als beheerder uitvoeren**.

1. Voer in het PowerShell-venster de volgende opdracht in en vervang `<path-to-starter-kit>` door het mappad van de geëxtraheerde starterkitbestanden.

   ```powershell
   dir -Path <path-to-starter-kit> -Recurse | Unblock-File
   ```

1. Voer de volgende opdracht in om de Azure-cmdlets te installeren.

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```

1. Als de waarschuwing *NuGet-provider moet doorgaan* wordt weergegeven, voert u **Y** in en drukt u vervolgens op Enter.

1. Als de waarschuwing *Niet-vertrouwde opslagplaats* wordt weergegeven, voert u **A** in en drukt u vervolgens op Enter.

Het kan een minuut duren voordat PowerShell de vereiste modules heeft geïnstalleerd.

### <a name="collect-data-needed-to-run-the-scripts"></a>Gegevens verzamelen die nodig zijn voor het uitvoeren van de scripts

Voordat u de PowerShell-scripts uitvoert als een bootstrap voor de catalogus, haalt u de waarden op van de volgende argumenten voor gebruik in de scripts:

* TenantID
   1. Selecteer **Azure Active Directory** in de [Azure-portal](https://portal.azure.com).
   1. Selecteer **Eigenschappen** in de sectie **Beheren** van het linker navigatiemenu. Selecteer vervolgens het pictogram kopiëren voor **Tenant-id** om de waarde op het klembord op te slaan. Plak de waarde in een teksteditor voor later gebruik.

* SubscriptionID
   1. Zoek in de Azure Portal naar en selecteer de naam van het Azure Purview-exemplaar dat u hebt gemaakt als een vereiste.
   1. Selecteer de sectie **Overzicht** en sla de GUID voor de **Abonnements-id** op.

   > [!NOTE]
   > - Zorg ervoor dat u hetzelfde abonnement gebruikt als waarin u het Azure Purview-account hebt gemaakt. Dit is hetzelfde abonnement dat is opgenomen in de acceptatielijst.
   > - Afkomst kunnen soms in azure controle sfeer liggen ontbreken na het uitvoeren van het Start pakket. De reden hiervoor is dat de Data Factory gemaakt door de Starter Kit ontbrekende machtigingen heeft in controle sfeer liggen. Selecteer [**deze document koppeling**](how-to-link-azure-data-factory.md#view-existing-data-factory-connections)  om te controleren of de Data Factory juist is geconfigureerd en de juiste rol heeft toegewezen in controle sfeer liggen


* CatalogName: De naam van het Azure Purview-account dat u hebt gemaakt in [Een Azure Purview-account maken](create-catalog-portal.md).

* CatalogResourceGroupName: De naam van de resourcegroep waarin u uw Azure Purview-account hebt gemaakt.

### <a name="verify-permissions"></a>Machtigingen controleren

Volg deze stappen om de gebruiker die het script uitvoert, toe te voegen aan het Azure Purview-account dat is gemaakt als een vereiste. Gebruikers hebben zowel *Purview-gegevenscurator*- als *Purview-gegevensbronbeheerder*-rollen nodig. 

Als u zelf het Azure Purview-account hebt gemaakt, krijgt u automatisch toegang en kunt u deze sectie overslaan.

1. Ga in de Azure Portal naar de pagina van het Purview-account en selecteer het Azure Purview-account dat u wilt wijzigen.

1. Selecteer op de pagina van het account het tabblad **Access Control (IAM)** en **+ Toevoegen**.

1. Selecteer **Roltoewijzing toevoegen**.

1. Voer **Purview-gegevenscuratorrol** in voor de *Rol*.
 
1. Gebruik de standaardwaarde voor *Toegang toewijzen tot*. De standaardwaarde moet **Gebruiker, groep of service-principal** zijn.

1. Voer de naam in van de gebruiker die het script uitvoert in **Selecteren**.

1. Selecteer **Opslaan**.

1. Herhaal de vorige stappen met de *Rol* ingesteld op **Beheerdersrol voor Purview-gegevensbronbeheer**.

Zie [Catalogusmachtigingen](catalog-permissions.md)voor meer informatie.

### <a name="run-the-client-side-setup-scripts"></a>De installatiescripts aan clientzijde uitvoeren

Nadat de catalogusconfiguratie is voltooid, voert u de volgende scripts uit in het PowerShell-venster om de assets te maken, waarbij u de tijdelijke aanduidingen vervangt door de waarden die u eerder hebt verzameld.

1. Gebruik de volgende opdracht om naar de starterkitmap te navigeren. Vervang `path-to-starter-kit` door het mappad van het geëxtraheerde bestand.

   ```powershell
   cd <path-to-starter-kit>
   ```

1. Met de volgende opdracht stelt u het uitvoerbewerkingsbeleid voor de lokale computer in. Voer **A** in voor *Ja op alles* wanneer u wordt gevraagd het uitvoerbewerkingsbeleid te wijzigen.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Maak verbinding met Azure met behulp van de volgende opdracht. Vervang de tijdelijke aanduidingen `TenantID` en `SubscriptionID` .

   ```powershell
   .\RunStarterKit.ps1 -ConnectToAzure -TenantId <TenantID> `
   -SubscriptionId <SubscriptionID>
   ```

   Wanneer u de opdracht uitvoert, wordt er mogelijk een pop-upvenster weergegeven waarin u zich kunt aanmelden met uw Azure Active Directory-referenties.


1. Gebruik de volgende opdracht om de starterkit uit te voeren. Vervang de tijdelijke aanduidingen `CatalogName`, `TenantID`, `SubscriptionID`, `NewResourceGroupName`en `CatalogResourceGroupName`. Gebruik voor `NewResourceGroupName` een unieke naam (met alleen kleine alfanumerieke tekens) voor de resourcegroep die het gegevensdomein bevat.

   > [!IMPORTANT]
   > De **newresourcegroupname** gebruikt alleen cijfers en kleine en moet minder dan 17 tekens bevatten. **Er zijn geen hoofdletters en speciale tekens toegestaan.** Deze beperking is afkomstig van regels voor naamgeving van opslagaccounts.

   ```powershell
   .\RunStarterKit.ps1 -CatalogName <CatalogName> -TenantId <TenantID>`
   -ResourceGroup <newresourcegroupname> `
   -SubscriptionId <SubscriptionID> `
   -CatalogResourceGroup <CatalogResourceGroupName>
   ```

Het kan tot tien minuten duren voordat de omgeving is ingesteld. Gedurende deze periode ziet u mogelijk verschillende pop-upvensters, die u kunt negeren. Sluit het venster **BlobDataCreator.exe** niet. Het wordt automatisch gesloten wanneer het is voltooid.

Wanneer u het bericht `Executing Copy pipeline xxxxxxxxxx-487e-4fc4-9628-92dd8c2c732b` ziet, wacht u op een ander exemplaar van **BlobDataCreator.exe** om de uitvoering te starten en te voltooien.

> [!IMPORTANT]
> Als het ‘Aantal actieve taken’ niet meer afneemt, kunt u het venster Blob Creator sluiten en op enter drukken in het PowerShell-venster

Nadat het proces is voltooid, wordt er een resourcegroep gemaakt met de naam die u hebt opgegeven. De Azure Data Factory-, Azure Blob-opslag- en Azure Data Lake Storage Gen2-accounts zijn allemaal opgenomen in deze resourcegroep. De resourcegroep bevindt zich in het abonnement dat u hebt opgegeven.

## <a name="scan-data-into-the-catalog"></a>Gegevens naar de catalogus scannen

Scannen is een proces waarmee de catalogus rechtstreeks verbinding maakt met een gegevensbron op basis van een door de gebruiker opgegeven planning. De catalogus weerspiegelt het gegevensdomein van een bedrijf door middel van scannen, herkomst, de portal en de API. Doelstellingen zijn onder andere het onderzoeken van de inhoud, het extraheren van schema's en het begrijpen van semantiek. In deze sectie stelt u een scan in van de gegevensbronnen die u hebt gegenereerd met de starterkit.

Het script van de starterkit dat u hebt uitgevoerd heeft twee gegevensbronnen gemaakt, Azure Blob-opslag en Azure Data Lake Storage Gen2. U kunt deze gegevensbronnen een voor een naar de catalogus scannen.

### <a name="authenticate-to-your-storage-with-managed-identity"></a>Verifiëren bij uw opslag met Beheerde identiteit

Er wordt automatisch een beheerde identiteit met dezelfde naam als uw Azure Purview-account gemaakt wanneer uw account wordt gemaakt. Voordat u uw gegevens kunt scannen, moet u machtigingen voor Azure Purview-rollen geven voor uw opslagaccounts.

1. Navigeer naar de resourcegroep die is gemaakt door de startkit en selecteer uw blobopslagaccount.

1. Selecteer **Access Control (IAM)** in het linker navigatiemenu. Selecteer vervolgens **+ Toevoegen**.

1. Stel de rol in op **Lezer voor opslagblobgegevens** en voer de naam van uw Azure Purview-accountnaam in voor **Selecteren**. Selecteer vervolgens **Opslaan** om deze rol toe te wijzen aan uw Purview-account.

   :::image type="content" source="media/tutorial-scan-data/add-role-assignment.png" alt-text="Roltoewijzing toevoegen":::

1. Herhaal de vorige stappen voor Azure Data Lake Storage Gen2.

### <a name="scan-your-data-sources"></a>Uw gegevensbronnen scannen

1. Navigeer naar uw Azure controle sfeer liggen-resource in de [Azure Portal](https://portal.azure.com) en selecteer *Open controle sfeer liggen Studio*. U wordt automatisch naar de startpagina van Purview Studio geleid.

1. Selecteer **Bronnen** op de webpagina van uw catalogus en selecteer **Registreren**. Selecteer vervolgens **Azure Blob Storage** en **Doorgaan**.

   :::image type="content" source="media/tutorial-scan-data/add-blob-storage.png" alt-text="Resource voor blobopslag registreren":::

1. Voer op de pagina **Bronnen registreren** een **Naam** in. Kies de **Opslagaccountnaam** van het Azure Blob-opslagaccount dat u eerder hebt gemaakt met de starterkit. Het account heeft de naam `<YourResourceGroupName>adcblob`. Selecteer **Finish**.

   :::image type="content" source="./media/tutorial-scan-data/register-azure-blob-storage.png" alt-text="Schermopname met de instellingen voor het registreren van een gegevensbron van uw Azure Blob opslag." border="true":::

1. Selecteer in de kaartweergave **Gegevensbronnen** **Nieuwe scan** op de tegel Azure Blob Storage.

   :::image type="content" source="./media/tutorial-scan-data/select-setup-scan.png" alt-text="Schermopname die laat zien hoe u een scaninstelling kunt selecteren op basis van een gegevensbron." border="true":::

1. Voer op de pagina **Scan** een scannaam in, selecteer uw Beheerde identiteit in de vervolgkeuzelijst **Referentie** en selecteer **Doorgaan**.

   :::image type="content" source="media/tutorial-scan-data/scan-blob.png" alt-text="Blobopslag in Azure Purview scannen":::

1. U kunt het bereik van uw scan instellen op afzonderlijke blobs. In deze zelfstudie moet u alle assets aangevinkt laten om alles te scannen en **Ga door**.

   :::image type="content" source="media/tutorial-scan-data/scope-your-scan.png" alt-text="Stel het bereik van uw opslagscan in":::

1. Selecteer de standaard scanregelreeks en **Ga door**.

1. U kunt een scantrigger instellen voor terugkerende scans. Selecteer **Eenmaal** voor deze zelfstudie en **Ga door**.

1. Selecteer **Opslaan en uitvoeren** om de scan te voltooien.

1. Herhaal de vorige stappen om uw Azure Data Lake Storage Gen2-account te scannen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:
> [!div class="checklist"]
>
> * Voer het script van de starterkit uit om een gegevensdomeinomgeving in te stellen.
> * Gegevens scannen naar een Azure Purview-catalogus.

Ga naar de volgende zelfstudie om te leren hoe u naar de startpagina navigeert en naar een asset zoekt.

> [!div class="nextstepaction"]
> [Naar de startpagina navigeren en een asset zoeken](tutorial-asset-search.md)
