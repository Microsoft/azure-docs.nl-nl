---
title: Een functie in azure maken die wordt geactiveerd door wachtrij berichten
description: Gebruik Azure Functions om een functie zonder server te maken die wordt aangeroepen door een bericht dat wordt verzonden naar een wachtrij in Azure.
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: d722d420597bb459d3e7b6d2ca33fdc49bfe6f09
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90981582"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Een door Azure Queue Storage geactiveerde functie maken

Ontdek hoe u een functie maakt die wordt geactiveerd wanneer er berichten worden verzonden naar een Azure Storage-wachtrij.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-an-azure-function-app"></a>Een Azure-functie-app maken

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-create-success.png" alt-text="De functie-app is gemaakt..." border="true":::

Vervolgens maakt u een functie in de nieuwe functie-app.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Een door een wachtrij geactiveerde functie maken

1. Selecteer **functies** en selecteer vervolgens **+ toevoegen** om een nieuwe functie toe te voegen.

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-app-quickstart-choose-template.png" alt-text="Kies een functie sjabloon in de Azure Portal." border="true":::

1. Kies de **Azure Queue Storage trigger** -sjabloon.

1. Gebruik de instellingen zoals opgegeven in de tabel onder de afbeelding.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png" alt-text="Geef een naam en configureer de wachtrij opslag geactiveerde functie." border="true":::


    | Instelling | Voorgestelde waarde | Beschrijving |
    |---|---|---|
    | **Naam** | Uniek in uw functie-app | Naam van deze door een wachtrij geactiveerde functie. |
    | **Wachtrijnaam**   | myqueue-items    | De naam van de wachtrij waarmee u verbinding moet maken in uw opslagaccount. |
    | **Opslagaccountverbinding** | AzureWebJobsStorage | U kunt de opslagaccountverbinding gebruiken die al door de functie-app wordt gebruikt of u kunt een nieuwe maken.  |    

1. Selecteer **functie maken** om de functie te maken.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-3.png" alt-text="Maak de door de wachtrij opslag geactiveerde functie." border="true":::

Vervolgens gaat u verbinding maken met uw Azure Storage-account en maakt u de **myqueue-items** opslag wachtrij.

## <a name="create-the-queue"></a>De wachtrij maken

1. Selecteer in uw functie op de pagina **overzicht** de resource groep.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-resource-group.png" alt-text="Selecteer uw Azure Portal resource groep." border="true":::

1. Zoek en selecteer het opslag account van uw resource groep.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-account-access.png" alt-text="Toegang tot het opslag account." border="true":::

1. Kies **wacht rijen** en kies vervolgens **+ wachtrij**. 

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-add-queue.png" alt-text="Voeg een wachtrij toe aan uw opslag account in de Azure Portal." border="true":::

1. Typ in het veld **naam** `myqueue-items` en selecteer vervolgens **maken**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-name-queue.png" alt-text="Geef de opslag container van de wachtrij een naam." border="true":::

Nu u een opslagwachtrij hebt, kunt u de functie testen door een bericht toe te voegen aan de wachtrij.

## <a name="test-the-function"></a>De functie testen

1. Blader in Azure Portal naar de functie, vouw de **Logboeken** onderaan de pagina uit en zorg ervoor dat logboekstreaming niet wordt onderbroken.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-queue-storage-log-expander.png" alt-text="Vouw het logboek in de Azure Portal uit." border="true":::

1. Ga in een afzonderlijk browser venster naar uw resource groep in de Azure Portal en selecteer het opslag account.

1. Selecteer **wacht rijen** en selecteer vervolgens de container **myqueue-items** .

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue.png" alt-text="Ga naar de wachtrij met myqueue-items in de Azure Portal." border="true":::

1. Selecteer **bericht toevoegen** en typ "Hallo wereld!" in **bericht tekst**. Selecteer **OK**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue-test.png" alt-text="Scherm afbeelding toont de knop bericht toevoegen geselecteerd en het tekst veld bericht gemarkeerd." border="true":::

1. Wacht een paar seconden en ga vervolgens terug naar de functielogboeken en controleer of het nieuwe bericht is gelezen.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png" alt-text="Bekijk het bericht in de logboeken." border="true":::

1. Selecteer in uw opslag wachtrij **vernieuwen** en controleer of het bericht is verwerkt en niet langer in de wachtrij staat.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt een functie gemaakt die wordt uitgevoerd wanneer er een bericht wordt toegevoegd aan een opslagwachtrij. Zie [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Opslagwachtrijbindingen van Azure Functions) voor meer informatie over activeringen van Queue Storage.

Nu u uw eerste functie hebt gemaakt, gaan we een uitvoerbinding aan de functie toevoegen die een bericht terug schrijft naar een andere wachtrij.

> [!div class="nextstepaction"]
> [Berichten aan een Azure Storage-wachtrij toevoegen met behulp van Functies](functions-integrate-storage-queue-output-binding.md)
