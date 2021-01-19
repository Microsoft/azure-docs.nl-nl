---
title: Azure Migrate-projecten maken en beheren
description: U kunt projecten vinden, maken, beheren en verwijderen in Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: de0c48bb775b96052fe16d60aa58049bfd58ca4d
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567773"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Azure Migrate-projecten maken en beheren

In dit artikel wordt beschreven hoe u [Azure migrate](migrate-services-overview.md) projecten maakt, beheert en verwijdert. Als u werkt met klassieke Azure Migrate projecten, verwijdert u deze projecten en volgt u de stappen om een nieuw Azure Migrate project te maken. U kunt geen klassieke Azure Migrate projecten of onderdelen naar de Azure Migrate bijwerken.

Een Azure Migrate project wordt gebruikt voor het opslaan van de metagegevens voor detectie, evaluatie en migratie die zijn verzameld uit de omgeving die u wilt beoordelen of migreren. In een project kunt u gedetecteerde assets volgen, beoordelingen maken en migraties naar Azure organiseren.  

## <a name="verify-permissions"></a>Machtigingen controleren

Controleer of u de juiste machtigingen hebt om een Azure Migrate project te maken:

1. Open in het Azure Portal het relevante abonnement en selecteer **toegangs beheer (IAM)**.
2. In **toegang controleren**, zoek het relevante account en selecteer de weer gave-machtigingen. U moet de machtigingen *Inzender* of *Eigenaar* hebben. 


## <a name="create-a-project-for-the-first-time"></a>Voor de eerste keer een project maken

Stel een nieuw Azure Migrate project in een Azure-abonnement in.

1. Zoek in het Azure Portal naar *Azure migrate*.
2. Selecteer **Azure migrate** in **Services**.
3. Selecteer in **Overzicht** de optie **Servers evalueren en migreren**.

    ![Optie in overzicht om servers te evalueren en te migreren](./media/create-manage-projects/assess-migrate-servers.png)

4. Selecteer in **servers** de optie **project maken**.

    ![Knop om het project te maken](./media/create-manage-projects/create-project.png)

5. Selecteer in **project maken** het Azure-abonnement en de resource groep. Maak een resourcegroep als u er nog geen hebt.
6. Geef in **Projectdetails** de projectnaam en het geografische gebied op waarin u het project wilt maken.
    - De geografie wordt alleen gebruikt om de meta gegevens op te slaan die zijn verzameld van on-premises machines. U kunt een wille keurige doel regio voor migratie selecteren. 
    - Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

8. Selecteer **Maken**.

   ![Pagina naar invoer van project instellingen](./media/create-manage-projects/project-details.png)


Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd.

## <a name="create-a-project-in-a-specific-region"></a>Een project maken in een specifieke regio

In de portal kunt u de geografie selecteren waarin u het project wilt maken. Als u het project binnen een specifieke Azure-regio wilt maken, gebruikt u de volgende API-opdracht om het project te maken.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Aanvullende projecten maken

Als u al een Azure Migrate project hebt en u een aanvullend project wilt maken, gaat u als volgt te werk:  

1. Zoek in de [open bare Azure Portal](https://portal.azure.com) of [Azure Government](https://portal.azure.us)naar **Azure migrate**.
2. Selecteer in de rechter bovenhoek op het Azure Migrate dash board > **servers** **wijzigen** .

   ![Azure Migrate project wijzigen](./media/create-manage-projects/switch-project.png)

3. Als u een nieuw project wilt maken, selecteert u **hier klikken**.


## <a name="find-a-project"></a>Een project zoeken

Zoek een project op de volgende manier:

1. Zoek in het [Azure Portal](https://portal.azure.com)naar *Azure migrate*.
2. Selecteer in het Azure Migrate Dash Board > servers **wijzigen** in de rechter bovenhoek.

    ![Overschakelen naar een bestaand Azure Migrate project](./media/create-manage-projects/switch-project.png)

3. Selecteer het juiste abonnement en Azure Migrate project.


### <a name="find-a-legacy-project"></a>Een oud project zoeken

Als u het project hebt gemaakt in de [vorige versie](migrate-services-overview.md#azure-migrate-versions) van Azure migrate, kunt u het als volgt vinden:

1. Zoek in het [Azure Portal](https://portal.azure.com)naar *Azure migrate*.
2. Als u in de vorige versie van het dash board van Azure Migrate een project hebt gemaakt, wordt er een banner weer gegeven dat verwijst naar oudere projecten. Selecteer de banner.

    ![Toegang tot bestaande projecten](./media/create-manage-projects/access-existing-projects.png)

3. Bekijk de lijst met oude projecten.


## <a name="delete-a-project"></a>Een project verwijderen

Verwijder deze als volgt:

1. Open de Azure-resource groep waarin het project is gemaakt.
2. Selecteer op de pagina resource groep de optie **verborgen typen weer geven**.
3. Selecteer het migratie project dat u wilt verwijderen en de bijbehorende resources.
    - Het resource type is **micro soft. migrate/migrateprojects**.
    - Als de resource groep uitsluitend wordt gebruikt door het Azure Migrate project, kunt u de hele resource groep verwijderen.

Opmerking:

- Wanneer u verwijdert, worden zowel het project als de meta gegevens over gedetecteerde machines verwijderd.
- Als u de oudere versie van Azure Migrate gebruikt, opent u de Azure-resource groep waarin het project is gemaakt. Selecteer het migratie project dat u wilt verwijderen (het resource type is een **migratie project**).
- Als u afhankelijkheids analyse gebruikt met een Azure Log Analytics-werk ruimte:
    - Als u een Log Analytics-werk ruimte hebt gekoppeld aan het hulp programma voor Server evaluatie, wordt de werk ruimte niet automatisch verwijderd. Dezelfde Log Analytics-werk ruimte kan voor meerdere scenario's worden gebruikt.
    - Als u de werk ruimte Log Analytics wilt verwijderen, doet u dat hand matig.
- Het verwijderen van het project is onomkeerbaar. Verwijderde objecten kunnen niet worden hersteld.

### <a name="delete-a-workspace-manually"></a>Een werk ruimte hand matig verwijderen

1. Blader naar de Log Analytics werkruimte die aan het project is gekoppeld.

    - Als u het Azure migrate project nog niet hebt verwijderd, kunt u de koppeling naar de werk ruimte in **Essentials**  >  **Server-evaluatie** vinden.
       ![De werk ruimte LA ](./media/create-manage-projects/loganalytics-workspace.png) .
       
    - Als u het Azure Migrate project al hebt verwijderd, selecteert u **resource groepen** in het linkerdeel venster van de Azure Portal en zoekt u de werk ruimte.
       
2. [Volg de instructies](../azure-monitor/platform/delete-workspace.md) om de werk ruimte te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Voeg hulpprogram ma's voor [evaluatie](how-to-assess.md) of [migratie](how-to-migrate.md) toe om Azure migrate projecten.
