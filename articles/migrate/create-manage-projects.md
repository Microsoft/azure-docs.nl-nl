---
title: Projecten maken en beheren
description: U kunt projecten vinden, maken, beheren en verwijderen in Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: cb0ac41d469ad9a7670ce4b1bae23b315a17dc38
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871062"
---
# <a name="create-and-manage-projects"></a>Projecten maken en beheren

In dit artikel wordt beschreven hoe u [projecten](migrate-services-overview.md)maakt, beheert en verwijdert. 

Klassieke Azure Migrate buiten gebruik worden gesteld in februari 2024. Na februari 2024 wordt de klassieke versie van Azure Migrate niet meer ondersteund en worden de meta gegevens van de inventaris in het klassieke project verwijderd. Als u klassieke projecten gebruikt, verwijdert u deze projecten en volgt u de stappen voor het maken van een nieuw project. U kunt geen klassieke projecten of onderdelen upgraden naar de Azure Migrate. Bekijk [Veelgestelde vragen](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) voordat u begint met het maken van het proces.

Project wordt gebruikt voor het opslaan van de meta gegevens voor detectie, evaluatie en migratie die zijn verzameld uit de omgeving die u wilt beoordelen of migreren. In een project kunt u gedetecteerde assets volgen, beoordelingen maken en migraties naar Azure organiseren.  

## <a name="verify-permissions"></a>Machtigingen controleren

Controleer of u de juiste machtigingen hebt om een project te maken:

1. Open in het Azure Portal het relevante abonnement en selecteer **toegangs beheer (IAM)**.
2. In **toegang controleren**, zoek het relevante account en selecteer de weer gave-machtigingen. U moet de machtigingen *Inzender* of *Eigenaar* hebben. 


## <a name="create-a-project-for-the-first-time"></a>Voor de eerste keer een project maken

Stel een nieuw project in een Azure-abonnement in.

1. Zoek in het Azure Portal naar *Azure migrate*.
2. Selecteer **Azure migrate** in **Services**.
3. Selecteer in **Overzicht** de optie **Servers evalueren en migreren**.

    :::image type="content" source="./media/create-manage-projects/assess-migrate-servers.png" alt-text="Optie in overzicht om servers te evalueren en te migreren":::

4. Selecteer in **servers** de optie **project maken**.

    :::image type="content" source="./media/create-manage-projects/create-project.png" alt-text="Knop om het project te maken":::

5. Selecteer in **project maken** het Azure-abonnement en de resource groep. Maak een resourcegroep als u er nog geen hebt.
6. Geef in **Projectdetails** de projectnaam en het geografische gebied op waarin u het project wilt maken.
    - De geografie wordt alleen gebruikt om de meta gegevens op te slaan die zijn verzameld van on-premises servers. U kunt een wille keurige doel regio voor migratie selecteren. 
    - Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

8. Selecteer **Maken**.

     :::image type="content" source="./media/create-manage-projects/project-details.png" alt-text="Pagina naar invoer van project instellingen":::


Wacht een paar minuten totdat het project is geïmplementeerd.

## <a name="create-a-project-in-a-specific-region"></a>Een project maken in een specifieke regio

In de portal kunt u de geografie selecteren waarin u het project wilt maken. Als u het project binnen een specifieke Azure-regio wilt maken, gebruikt u de volgende API-opdracht om het project te maken.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Aanvullende projecten maken

Als u al een project hebt en u een aanvullend project wilt maken, gaat u als volgt te werk:  

1. Zoek in de [open bare Azure Portal](https://portal.azure.com) of [Azure Government](https://portal.azure.us)naar **Azure migrate**.
2. Selecteer in de rechter bovenhoek op het Azure Migrate dash board > **servers** **wijzigen** .

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Project wijzigen":::

3. Als u een nieuw project wilt maken, selecteert u **hier klikken**.


## <a name="find-a-project"></a>Een project zoeken

Zoek een project op de volgende manier:

1. Zoek in het [Azure Portal](https://portal.azure.com)naar *Azure migrate*.
2. Selecteer in het Azure Migrate Dash Board > servers **wijzigen** in de rechter bovenhoek.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Overschakelen naar een bestaand project":::

3. Selecteer het juiste abonnement en project.


### <a name="find-a-classic-project"></a>Een klassiek project zoeken

Als u het project hebt gemaakt in de [vorige versie](migrate-services-overview.md#azure-migrate-versions) van Azure migrate, kunt u het als volgt vinden:

1. Zoek in het [Azure Portal](https://portal.azure.com)naar *Azure migrate*.
2. Als u in de vorige versie van het dash board van Azure Migrate een project hebt gemaakt, wordt er een banner weer gegeven dat verwijst naar oudere projecten. Selecteer de banner.

    :::image type="content" source="./media/create-manage-projects/access-existing-projects.png" alt-text="Toegang tot bestaande projecten":::

3. Bekijk de lijst met oude projecten.


## <a name="delete-a-project"></a>Een project verwijderen

Verwijder deze als volgt:

1. Open de Azure-resource groep waarin het project is gemaakt.
2. Selecteer op de pagina resource groep de optie **verborgen typen weer geven**.
3. Selecteer het project dat u wilt verwijderen en de bijbehorende resources.
    - Het resource type is **micro soft. migrate/migrateprojects**.
    - Als de resource groep uitsluitend door het project wordt gebruikt, kunt u de hele resource groep verwijderen.

Opmerking:

- Wanneer u verwijdert, worden zowel het project als de meta gegevens over gedetecteerde servers verwijderd.
- Als u de oudere versie van Azure Migrate gebruikt, opent u de Azure-resource groep waarin het project is gemaakt. Selecteer het project dat u wilt verwijderen (het resource type is een **migratie project**).
- Als u afhankelijkheids analyse gebruikt met een Azure Log Analytics-werk ruimte:
    - Als u een Log Analytics-werk ruimte hebt gekoppeld aan het hulp programma voor Server evaluatie, wordt de werk ruimte niet automatisch verwijderd. Dezelfde Log Analytics-werk ruimte kan voor meerdere scenario's worden gebruikt.
    - Als u de werk ruimte Log Analytics wilt verwijderen, doet u dat hand matig.
- Het verwijderen van het project is onomkeerbaar. Verwijderde objecten kunnen niet worden hersteld.

### <a name="delete-a-workspace-manually"></a>Een werk ruimte hand matig verwijderen

1. Blader naar de Log Analytics werkruimte die aan het project is gekoppeld.

    - Als u het project nog niet hebt verwijderd, kunt u de koppeling naar de werk ruimte in **Essentials**  >  **Server-evaluatie** vinden.
    :::image type="content" source="./media/create-manage-projects/loganalytics-workspace.png" alt-text="De werk ruimte LA":::
       
    - Als u het project al hebt verwijderd, selecteert u **resource groepen** in het linkerdeel venster van de Azure Portal en zoekt u de werk ruimte.
       
2. [Volg de instructies](../azure-monitor/logs/delete-workspace.md) om de werk ruimte te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Voeg hulpprogram ma's voor [evaluatie](how-to-assess.md) of [migratie](how-to-migrate.md) aan projecten toe.