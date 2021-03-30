---
title: 'Azure-quickstart: een Azure Automation-runbook maken | Microsoft Docs'
description: Met dit artikel kunt u aan de slag gaan met het maken van een Azure Automation-runbook.
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 948ca820347c7cdcd560ade46e850f66b25bc88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90987295"
---
# <a name="create-an-azure-automation-runbook"></a>Een Azure Automation-runbook maken

Azure Automation-runbooks kunnen via Azure worden gemaakt. Deze methode biedt een gebruikersinterface op basis van een browser voor het maken van Automation-runbooks. Deze quickstart bevat stapsgewijze instructies voor het maken, bewerken, testen en publiceren van een Automation PowerShell-runbook.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure op https://portal.azure.com.

## <a name="create-the-runbook"></a>Het runbook maken

Maak eerst een runbook. Het voorbeeldrunbook dat in deze quickstart is gemaakt, produceert standaard `Hello World` als uitvoer.

1. Open uw Automation-account.

1. Klik op **Runbooks** onder **Procesautomatisering**. De lijst met runbooks wordt weergegeven.

1. Klik bovenaan de lijst op **Een runbook maken**.

1. Voer voor de naam van het runbook `Hello-World` in het veld **Naam** in en selecteer de waarde **PowerShell** voor het veld **Runbooktype**. 

   ![Gegevens over uw Automation-runbook invoeren op de pagina](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Klik op **Create**. Het runbook wordt gemaakt en de pagina PowerShell-runbook bewerken wordt geopend.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png" alt-text="Schermopname van de pagina 'PowerShell-runbook bewerken'.":::

1. Typ of kopieer en plak de volgende code in het bewerkingsvenster. Er wordt een optionele invoerparameter `Name` gemaakt met een standaardwaarde `World`, en als uitvoer wordt een tekenreeks geproduceerd die gebruikmaakt van deze invoerwaarde:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Klik op **Opslaan** om een conceptversie van het runbook op te slaan.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook.png" alt-text="Schermopname van de pagina 'PowerShell-runbook bewerken' met een codevoorbeeld in het rechtervenster.":::

## <a name="test-the-runbook"></a>Het runbook testen

Zodra het runbook is gemaakt, moet u het testen om te zien of het werkt.

1. Klik op **Testvenster** om het testvenster te openen.

1. Voer een waarde in bij **Naam** en klik op **Starten**. De testtaak wordt gestart en de taakstatus en uitvoer worden weergegeven.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-test-runbook.png" alt-text="Schermopname van het testvenster met een voorbeeldwaarde in het naamveld.":::

1. Sluit het testvenster door op de **X** in de rechterbovenhoek te klikken. Selecteer **OK** in de pop-up die wordt weergegeven.

1. Op de pagina PowerShell-runbook bewerken klikt u op **Publiceren** om het runbook als de officiële versie van het runbook in het account te publiceren.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png" alt-text="Schermopname van de pagina 'PowerShell-runbook bewerken' met de geselecteerde knop 'Publiceren'.":::

## <a name="run-the-runbook"></a>Het runbook uitvoeren

Zodra het runbook is gepubliceerd, wordt de overzichtspagina weergegeven.

1. Klik op de overzichtspagina van het runbook op **Starten** om de configuratiepagina Runbook starten voor dit runbook te openen.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png" alt-text="Schermopname van de configuratiepagina 'Runbook starten'.":::

1. Laat **Naam** leeg, zodat de standaardwaarde wordt gebruikt en klik op **OK**. De runbooktaak is verzonden en de pagina Taak wordt weergegeven.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-job-page.png" alt-text="Schermopname van de taakpagina waarop de knop 'Uitvoer' geselecteerd wordt weergegeven.":::

1. Wanneer de taakstatus `Running` of `Completed` is, klikt u op **Uitvoer** om het deelvenster Uitvoer te openen en de runbookuitvoer weer te geven.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png" alt-text="Schermopname van het deelvenster 'Uitvoer' waarin de runbookuitvoer wordt weergegeven.":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u het runbook niet meer nodig hebt, kunt u het verwijderen. Om dit te doen, selecteert u het runbook in de runbooklijst en klikt u op **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd om een runbook te maken, bewerken, testen en publiceren, en hebt u een runbooktaak gestart. Voor meer informatie over Automation-runbooks, gaat u verder naar het artikel over de verschillende runbooktypen die u in Automation kunt maken en gebruiken.

> [!div class="nextstepaction"]
> [Azure Automation-runbooktypen](./automation-runbook-types.md)
