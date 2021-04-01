---
title: 'Azure-quickstart: een Azure Automation-account maken | Microsoft Docs'
description: Met dit artikel kunt u aan de slag gaan met het maken van een Azure Automation-account en het uitvoeren van een runbook.
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: a07c6ac524aa213519ace1ae204ac2d76db802aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83836699"
---
# <a name="create-an-azure-automation-account"></a>Een Azure Automation-account maken

U kunt via Azure een Azure Automation-account maken met behulp van Azure Portal, een op de browser gebaseerde gebruikersinterface waarmee u toegang hebt tot een aantal resources. Met één Automation-account kunt u resources in alle regio's en abonnementen voor een bepaalde tenant beheren. 

Deze quickstart laat u zien hoe u een Automation-account kunt maken en een runbook in het account kunt uitvoeren. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

[Meld u aan bij Azure](https://portal.azure.com).

## <a name="create-automation-account"></a>Automation-account maken

1. Kies een naam voor uw Azure-account. Automation-accountnamen zijn uniek per regio en resourcegroep. Namen voor Automation-accounts die zijn verwijderd, zijn mogelijk niet onmiddellijk beschikbaar.

    > [!NOTE]
    > U kunt de accountnaam niet wijzigen nadat deze is ingevoerd in de gebruikersinterface. 

2. Klik op de knop **Een resource maken** in de linkerbovenhoek van Azure Portal.

3. Selecteer **IT en beheerhulpprogramma’s** en selecteer vervolgens **Automatisering**.

4. Voer de accountgegevens in, met inbegrip van de geselecteerde accountnaam. Kies bij **Een Uitvoeren als-account voor Azure maken**, de optie **Ja** zodat de artefacten die verificatie bij Azure vereenvoudigen, automatisch worden ingeschakeld. Als de informatie compleet is, klikt u op **Maken** om met de implementatie van het Automation-account te beginnen.

    ![Gegevens over uw Automation-account invoeren op de pagina](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Zie [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all) voor een bijgewerkte lijst met locaties waarin u een Automation-account kunt implementeren.

5. Wanneer de implementatie is voltooid, klikt u op **Alle services**.

6. Selecteer **Automation-accounts** en kies vervolgens het Automation-account dat u hebt gemaakt.

    ![Overzicht van Automation-account](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Een runbook uitvoeren

Voer een van de zelfstudierunbooks uit.

1. Klik op **Runbooks** onder **Procesautomatisering**. De lijst met runbooks wordt weergegeven. Verschillende zelfstudierunbooks zijn standaard ingeschakeld in het account.

    ![Lijst met runbooks in het Automation-account](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Selecteer het runbook **AzureAutomationTutorialScript**. Met deze actie wordt de overzichtspagina van het runbook geopend.

    ![Overzicht van het runbook](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Klik op **Starten** en klik op de pagina Runbook starten op **OK** om het runbook te starten.

    ![Pagina met runbooktaak](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Nadat de taakstatus verandert in `Running`, klikt u op **Uitvoer** of **Alle logboeken** om de uitvoer van de runbooktaak weer te geven. Voor dit zelfstudierunbook bestaat de uitvoer uit een lijst van uw Azure-resources.

## <a name="next-steps"></a>Volgende stappen

In het kader van deze quickstart hebt u een Automation-account geïmplementeerd, een runbooktaak gestart en de resultaten van een taak weergegeven. Als u meer wilt weten over Azure Automation, gaat u verder met de quickstart om een eerste runbook te maken.

> [!div class="nextstepaction"]
> [Automation-quickstart: een Azure Automation-runbook maken](./automation-quickstart-create-runbook.md)

