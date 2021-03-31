---
title: Een functie in azure maken die wordt geactiveerd door Blob Storage
description: Gebruik Azure Functions om een functie zonder server te maken die wordt aangeroepen door items die zijn toegevoegd aan een BLOB storage-container.
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: bf6865d2756579f457dded90b247326d2eec137c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83122975"
---
# <a name="create-a-function-in-azure-thats-triggered-by-blob-storage"></a>Een functie in azure maken die wordt geactiveerd door Blob Storage

Meer informatie over het maken van een functie die wordt geactiveerd wanneer bestanden worden geüpload naar of bijgewerkt in een BLOB storage-container.

## <a name="prerequisites"></a>Vereisten

+ Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-an-azure-function-app"></a>Een Azure-functie-app maken

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

De nieuwe functie-app is gemaakt.

:::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-create-success.png" alt-text="De functie-app is gemaakt." border="true":::

Vervolgens maakt u een functie in de nieuwe functie-app.

<a name="create-function"></a>

## <a name="create-an-azure-blob-storage-triggered-function"></a>Een door Azure Blob Storage geactiveerde functie maken

1. Selecteer **functies** en selecteer vervolgens **+ toevoegen** om een nieuwe functie toe te voegen.

   :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-template.png" alt-text="Kies een functie sjabloon in de Azure Portal." border="true":::

1. Kies de **Azure Blob Storage trigger** -sjabloon.

1. Gebruik de instellingen zoals opgegeven in de tabel onder de afbeelding.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png" alt-text="Naam en configureer de door Blob Storage geactiveerde functie." border="true":::

    | Instelling | Voorgestelde waarde | Beschrijving |
    |---|---|---|
    | **Nieuwe functie** | Uniek in uw functie-app | Naam van deze door Blob geactiveerde functie. |
    | **Pad**   | samples-workitems/{name}    | Locatie in Blob Storage die wordt bewaakt. De bestandsnaam van de blob wordt doorgegeven in de binding als de _naam_-parameter.  |
    | **Opslagaccountverbinding** | AzureWebJobsStorage | U kunt de opslagaccountverbinding gebruiken die al door de functie-app wordt gebruikt of u kunt een nieuwe maken.  |

1. Selecteer **functie maken** om de functie te maken.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-3.png" alt-text="Maak de door Blob Storage geactiveerde functie." border="true":::

Maak vervolgens de container **samples-werk items** .

## <a name="create-the-container"></a>De container maken

1. Selecteer in uw functie op de pagina **overzicht** de resource groep.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-resource-group.png" alt-text="Selecteer uw Azure Portal resource groep." border="true":::

1. Zoek en selecteer het opslag account van uw resource groep.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-account-access.png" alt-text="Toegang tot het opslag account." border="true":::

1. Kies **containers** en kies vervolgens **+ container**. 

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-add-container.png" alt-text="Voeg een container toe aan uw opslag account in de Azure Portal." border="true":::

1. Typ in het veld **naam** `samples-workitems` en selecteer vervolgens **maken**.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-name-blob-container.png" alt-text="Geef de opslag container een naam." border="true":::

U hebt nu een blob-container en u kunt de functie testen door een bestand naar de container te uploaden.

## <a name="test-the-function"></a>De functie testen

1. Blader in Azure Portal naar de functie, vouw de **Logboeken** onderaan de pagina uit en zorg ervoor dat logboekstreaming niet wordt onderbroken.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-log-expander.png" alt-text="Vouw het logboek in de Azure Portal uit." border="true":::

1. Ga in een afzonderlijk browser venster naar uw resource groep in de Azure Portal en selecteer het opslag account.

1. Selecteer **containers** en selecteer vervolgens de container **samples-werk items** .

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-container.png" alt-text="Ga naar uw voor beelden-werk items-container in de Azure Portal." border="true":::

1. Selecteer **uploaden** en selecteer vervolgens het mappictogram om een bestand te kiezen dat u wilt uploaden.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png" alt-text="Upload een bestand naar de blob-container." border="true":::

1. Blader naar een bestand op uw lokale computer, zoals een afbeeldings bestand, kiest u het bestand. Selecteer **openen** en vervolgens **uploaden**.

1. Ga terug naar de functielogboeken en controleer of de blob is gelezen.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png" alt-text="Bekijk het bericht in de logboeken." border="true":::

    >[!NOTE]
    > Wanneer uw functie-app in het standaardverbruiksabonnement wordt uitgevoerd, kan er een vertraging van maximaal een aantal minuten zitten tussen de blob die wordt toegevoegd of bijgewerkt en de functie die wordt geactiveerd. Overweeg om uw functie-app in een App Service-plan uit te voeren, als u lage latentie in uw door blob geactiveerde functies nodig hebt.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt een functie gemaakt die wordt uitgevoerd wanneer er een blob wordt toegevoegd aan of bijgewerkt in Blob Storage. Zie [Azure Functions Blob storage bindings](functions-bindings-storage-blob.md) (Blob-opslagbindingen in Azure Functions) voor meer informatie over de Blob-opslagtriggers.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
