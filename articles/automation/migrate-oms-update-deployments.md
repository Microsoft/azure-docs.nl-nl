---
title: Update-implementaties van Azure Monitor-logboeken migreren naar Azure Portal
description: In dit artikel wordt beschreven hoe u Azure Monitor update-implementaties migreert naar Azure Portal.
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: a2226f55c829afa4316a92888d16f6dc68e1f931
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183597"
---
# <a name="migrate-azure-monitor-logs-update-deployments-to-azure-portal"></a>Update-implementaties van Azure Monitor-logboeken migreren naar Azure Portal

De portal van operations management suite (OMS) wordt [afgeschaft](../azure-monitor/platform/oms-portal-transition.md). Alle functionaliteit die beschikbaar was in de OMS-portal voor Updatebeheer is beschikbaar in het Azure Portal via Azure Monitor Logboeken. In dit artikel vindt u de informatie die u nodig hebt om naar het Azure Portal te migreren.

## <a name="key-information"></a>Belangrijke informatie

* Bestaande implementaties blijven werken. Wanneer u de implementatie opnieuw hebt gemaakt in azure, kunt u de oude implementatie verwijderen.
* Alle bestaande functies die u in OMS had, zijn beschikbaar in Azure. Zie [updatebeheer Overview](./update-management/overview.md)voor meer informatie over updatebeheer.

## <a name="access-the-azure-portal"></a>Naar Azure Portal gaan

1. Klik in uw werk ruimte op **openen in azure**. 

    ![Openen in azure-Log Analytics](media/migrate-oms-update-deployments/link-to-azure-portal.png)

2. Klik in het Azure Portal op **Automation-account**

    ![Azure Monitor-logboeken](media/migrate-oms-update-deployments/log-analytics.png)

3. Klik op **updatebeheer** in uw Automation-account.

    :::image type="content" source="media/migrate-oms-update-deployments/azure-automation.png" alt-text="Scherm afbeelding van de pagina voor update beheer.":::

4. Selecteer in de Azure Portal **Automation-accounts** onder **alle services**. 

5. Onder **beheer hulpprogramma's** selecteert u het juiste Automation-account en klikt u op **updatebeheer**.

## <a name="recreate-existing-deployments"></a>Bestaande implementaties opnieuw maken

Alle update-implementaties die in de OMS-Portal zijn gemaakt, hebben een [opgeslagen zoek opdracht](../azure-monitor/platform/computer-groups.md) ook wel een computer groep genoemd, met dezelfde naam als de update-implementatie die bestaat. De opgeslagen zoek actie bevat de lijst met computers die zijn gepland in de update-implementatie.

:::image type="content" source="media/migrate-oms-update-deployments/oms-deployment.png" alt-text="Scherm afbeelding van de pagina update-implementaties met de velden naam en servers gemarkeerd.":::

Voer de volgende stappen uit om deze bestaande opgeslagen zoek opdracht te gebruiken:

1. Als u een nieuwe update-implementatie wilt maken, gaat u naar de Azure Portal, selecteert u het Automation-account dat wordt gebruikt en klikt u op **updatebeheer**. Klik op **Update-implementatie plannen**.

    ![Update-implementatie plannen](media/migrate-oms-update-deployments/schedule-update-deployment.png)

2. Het deel venster nieuwe update-implementatie wordt geopend. Voer waarden in voor de eigenschappen die in de volgende tabel worden beschreven en klik vervolgens op **Maken**:

3. **Als u wilt dat machines worden bijgewerkt**, selecteert u de opgeslagen zoek opdracht die wordt gebruikt door de OMS-implementatie.

    | Eigenschap | Beschrijving |
    | --- | --- |
    |Naam |Unieke naam voor het identificeren van de update-implementatie. |
    |Besturingssysteem| Selecteer **Linux** of **Windows**.|
    |Bij te werken machines |Selecteer een opgeslagen zoek opdracht, geïmporteerde groep of machine kiezen in de vervolg keuzelijst en selecteer afzonderlijke machines. Als u **Computers** selecteert, wordt de gereedheid van de computer weergegeven in de kolom **GEREEDHEID VOOR UPDATE-AGENT**.</br> Zie [Computergroepen in Azure Monitorlogboeken](../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken |
    |Updateclassificaties|Selecteer alle update classificaties die u nodig hebt. CentOS biedt geen ondersteuning voor dit out-of-Box.|
    |Updates die moeten worden uitgesloten|Voer de updates in die moeten worden uitgesloten. Voer voor Windows het KB-artikel in zonder het voor voegsel **KB** . Voor Linux voert u de pakket naam in of gebruikt u een Joker teken.  |
    |Instellingen voor planning|Selecteer het tijdstip waarop u wilt beginnen en selecteer vervolgens **een of meer keren of** **terugkerend** voor het terugkeer patroon. | 
    | Onderhoudsvenster |Aantal minuten dat is ingesteld voor updates. De waarde mag niet minder dan 30 minuten of langer dan 6 uur zijn. |
    | Besturingselement opnieuw opstarten| Hiermee wordt bepaald hoe het opnieuw opstarten moet worden afgehandeld.</br>De volgende opties zijn beschikbaar:</br>Opnieuw opstarten indien nodig (standaard)</br>Altijd opnieuw opstarten</br>Nooit opnieuw opstarten</br>Alleen opnieuw opstarten - updates worden niet geïnstalleerd|

4. Klik op **geplande update-implementaties** om de status van de zojuist gemaakte update-implementatie weer te geven.

    ![nieuwe update-implementatie](media/migrate-oms-update-deployments/new-update-deployment.png)

5. Zoals eerder vermeld, kunt u, nadat de nieuwe implementaties zijn geconfigureerd via de Azure Portal, de bestaande implementaties uit de Azure Portal verwijderen.

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van updatebeheer](./update-management/overview.md)voor meer informatie over Updatebeheer in azure Automation.