---
title: 'Azure-quickstart: uw eerste Batch-taak uitvoeren in Azure Portal'
description: In deze quickstart leert u hoe u de Azure-portal gebruikt voor het maken van een Batch-account, een pool met rekenknooppunten, en een taak waarmee basistaken worden uitgevoerd in de pool.
ms.date: 08/17/2020
ms.topic: quickstart
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: 3333097b4bd55173725aa33bc4bfbae318510cf1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538535"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Snelstartgids: Uw eerste Batch-taak uitvoeren in Azure Portal

Ga aan de slag met Azure Batch door Azure Portal te gebruiken voor het maken van een Batch-account, een pool met berekeningsknooppunten (virtuele machines) en een taak waarmee taken in de pool worden uitgevoerd. Nadat u deze quickstart hebt voltooid, begrijpt u de belangrijkste principes van de Batch-service en bent u er klaar voor om Batch op grotere schaal te gebruiken voor meer realistische workloads.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-batch-account"></a>Batch-account maken

Volg deze stappen om een Batch-voorbeeldaccount te maken voor testdoeleinden. U hebt een Batch-account nodig om pools en taken te maken. Zoals hier wordt getoond, kunt u een Azure-opslagaccount aan het Batch-account koppelen. Hoewel dit niet is vereist voor deze snelstartgids, is een opslagaccount handig voor het implementeren van toepassingen, en voor het opslaan van invoer- en uitvoergegevens voor de meeste workloads uit de praktijk.

1. Selecteer in [Azure Portal](https://portal.azure.com) de optie **Een resource maken** > **Compute** > **Batch-service**. 

   :::image type="content" source="media/quick-create-portal/marketplace-batch.png" alt-text="Schermopname van de Batch-service in Azure Marketplace.":::

1. Selecteer in het veld **Resourcegroep** de optie **Nieuwe maken** en voer een naam in voor de resourcegroep.

1. Voer een waarde in voor **Accountnaam**. Deze naam moet uniek zijn voor de geselecteerde Azure-**locatie**. De naam mag alleen uit kleine letters en cijfers bestaan en moet tussen de 3 en 24 tekens bevatten.

1. Selecteer onder **Opslagaccount** een bestaand opslagaccount of maak een nieuw opslagaccount.

1. Wijzig geen andere instellingen. Selecteer **Controleren en maken** en vervolgens **Maken** om het Batch-account te maken.

Wanneer het bericht **Implementatie is voltooid** wordt weergegeven, gaat u naar het Batch-account dat u hebt gemaakt.

## <a name="create-a-pool-of-compute-nodes"></a>Een pool met rekenknooppunten maken

Nu u beschikt over een Batch-account, maakt u een voorbeeldpool met Windows-rekenknooppunten voor testdoeleinden. De pool in dit snelle voorbeeld bestaat uit twee knooppunten waarop een Windows Server 2019-installatiekopie wordt uitgevoerd vanuit Azure Marketplace.

1. Selecteer in het Batch-account **Pools** > **Toevoegen**.

1. Voer een **Pool-id** in met de naam *mypool*.

1. Selecteer bij **Besturingssysteem** de volgende instellingen (u kunt andere opties verkennen).
  
   |Instelling  |Waarde  |
   |---------|---------|
   |**Type installatiekopie**|Marketplace|
   |**Publisher**     |microsoftwindowsserver|
   |**Aanbieding**     |windowsserver|
   |**SKU**     |2019-datacenter-core-smalldisk|

1. Schuif naar beneden om de **Knooppuntgrootte** en instellingen voor **Schalen** in te voeren. De voorgestelde knooppuntgrootte in dit snelle voorbeeld biedt een goede balans tussen prestaties en kosten.
  
   |Instelling  |Waarde  |
   |---------|---------|
   |**Prijscategorie voor het knooppunt**     |Standard A1|
   |**Aan het doel toegewezen knooppunten**     |2|

1. Laat de overige instellingen op de standaardwaarden staan en selecteer **OK** om de pool te maken.

De pool wordt onmiddellijk gemaakt in Batch, maar het duurt enkele minuten voordat de rekenknooppunten zijn toegewezen en gestart. Gedurende deze minuten is de **Toewijzingsstatus** van de pool ingesteld op **Grootte wordt gewijzigd**. U kunt doorgaan met het maken van een taak terwijl de grootte van de pool wordt gewijzigd.

Na enkele minuten verandert de toewijzingsstatus in **Onveranderlijk** en worden de knooppunten gestart. Als u de status van de knooppunten wilt controleren, selecteert u de pool en vervolgens **Knooppunten**. Wanneer de status van een knooppunt **Inactief** is, kunt u taken op een knooppunt uitvoeren.

## <a name="create-a-job"></a>Een taak maken

Nu u beschikt over een pool, kunt u een Batch-taak maken om uit te voeren op deze pool. Een Batch-job is een logische groep van een of meer taken. Een Batch-taak omvat instellingen die gemeenschappelijk zijn voor de taken, zoals prioriteit en de pool waarop taken moeten worden uitgevoerd. De Batch-taak heeft in eerste instantie geen taken.

1. Selecteer in de weergave Batch-account **Taken** > **Toevoegen**.

1. Voer een **taak-id** in met naam *myjob*. Selecteer *mypool* bij **Pool**. Behoud de standaardwaarden voor de overige instellingen en selecteer **OK**.

## <a name="create-tasks"></a>Taken maken

Selecteer nu de taak om de pagina **Taken** te openen. Hier maakt u voorbeeldtaken die in de taak worden uitgevoerd. Doorgaans maakt u meerdere taken die met Batch in de wachtrij worden geplaatst en worden gedistribueerd voor uitvoering op de berekeningsknooppunten. In dit voorbeeld maakt u twee identieke taken. In elke taak wordt een opdrachtregel uitgevoerd om de variabelen voor de Batch-omgeving op een rekenknooppunt weer te geven. Vervolgens is er een pauze van 90 seconden.

Wanneer u Batch gebruikt, geeft u uw app of script op de opdrachtregel op. Batch biedt een aantal manieren om apps en scripts te implementeren op rekenknooppunten.

De eerste taak maken:

1. Selecteer **Toevoegen**.

1. Voer een **Taak-id** in met de naam *mytask*.

1. Voer `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"` in bij **Opdrachtregel**. Behoud de standaardwaarden voor de overige instellingen en selecteer **Verzenden**.

Nadat u een taak hebt gemaakt, wordt deze in Batch in de wachtrij geplaatst voor uitvoering op de pool. Wanneer er een knooppunt beschikbaar is om de taak uit te voeren, wordt de taak uitgevoerd.

Herhaal de bovenstaande stappen om een tweede taak te maken. Voer een andere **Taak-id** in, maar geef een identieke opdrachtregel op. Als de eerste taak nog steeds wordt uitgevoerd, wordt de tweede taak in Batch gestart op een ander knooppunt in de pool.

## <a name="view-task-output"></a>Taakuitvoer weergeven

De voorbeeldtaken die u hebt gemaakt, worden in een paar minuten voltooid. Als u de uitvoer van een voltooide taak wilt weergeven, selecteert u **Bestanden op knooppunt**. Selecteer het bestand `stdout.txt` om de standaarduitvoer van de taak weer te geven. De inhoud ziet er ongeveer als volgt uit:

:::image type="content" source="media/quick-create-portal/task-output.png" alt-text="Schermopname van de uitvoer van een voltooide taak.":::

De inhoud geeft de variabelen voor de Azure Batch-omgeving weer die zijn ingesteld op het knooppunt. Wanneer u uw eigen Batch-taken en taken maakt, kunt u naar deze omgevingsvariabelen verwijzen in opdrachtregels voor taken, en in de apps en scripts die met de opdrachtregels worden uitgevoerd.

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt doorgaan met Batch-zelfstudies en -voorbeelden, gebruikt u het Batch-account en het gekoppelde opslagaccount dat in deze snelstartgids is gemaakt. Voor het Batch-account zelf worden geen kosten in rekening gebracht.

Er worden kosten berekend voor de pool zolang de knooppunten actief zijn, zelfs als er geen taken zijn gepland. Verwijder de pool wanneer u deze niet meer nodig hebt. Selecteer in de accountweergave **Pools** en de naam van de pool. Selecteer vervolgens **Verwijderen**.  Wanneer u de pool verwijdert, wordt ook alle taakuitvoer op de knooppunten verwijderd.

Verwijder de resourcegroep, het Batch-account en alle gerelateerde resources, wanneer u deze niet meer nodig hebt. Hiervoor selecteert u de resourcegroep voor het Batch-account en selecteert u **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u een Batch-account, een Batch-pool en een Batch-taak gemaakt. Met de Batch-taak zijn voorbeeldtaken uitgevoerd, en u hebt de uitvoer van een van de knooppunten bekeken. Nu u de belangrijkste principes van de Batch-service begrijpt, bent u er klaar voor om Batch op grotere schaal te gebruiken voor meer realistische workloads. Voor meer informatie over Azure Batch gaat u naar de Azure Batch-zelfstudies.

> [!div class="nextstepaction"]
> [Azure Batch-zelfstudies](./tutorial-parallel-dotnet.md)
