---
title: Projecten maken en beheren
description: Projecten zoeken, maken, beheren en verwijderen in Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 0c9102f8ca724e431bb478945c5f4ba0369643d6
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714830"
---
# <a name="create-and-manage-projects"></a>Projecten maken en beheren

In dit artikel wordt beschreven hoe u projecten maakt, beheert en [verwijdert.](migrate-services-overview.md) 

Klassieke Azure Migrate wordt in februari 2024 met pensioen gaat. Na februari 2024 wordt de klassieke versie van Azure Migrate niet meer ondersteund en worden de inventarismetagegevens in het klassieke project verwijderd. Als u klassieke projecten gebruikt, verwijdert u deze projecten en volgt u de stappen om een nieuw project te maken. U kunt klassieke projecten of onderdelen niet upgraden naar de Azure Migrate. Bekijk [veelgestelde](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) vragen voordat u begint met het maken.

Project wordt gebruikt voor het opslaan van detectie-, evaluatie- en migratiemetagegevens die zijn verzameld uit de omgeving die u evalueert of migreert. In een project kunt u gevonden assets bijhouden, evaluaties maken en migraties naar Azure indelen.  

## <a name="verify-permissions"></a>Machtigingen controleren

Controleer of u de juiste machtigingen hebt om een project te maken:

1. Open in Azure Portal het relevante abonnement en selecteer **Toegangsbeheer (IAM)**.
2. Zoek **in Toegang controleren** het relevante account en selecteer machtigingen weergeven. U moet de machtigingen *Inzender* of *Eigenaar* hebben. 


## <a name="create-a-project-for-the-first-time"></a>Een project voor de eerste keer maken

Stel een nieuw project in een Azure-abonnement in.

1. Zoek in Azure Portal naar *Azure Migrate*.
2. Selecteer **in Services** de **Azure Migrate**.
3. Selecteer in **Overzicht** de optie **Servers evalueren en migreren**.

    :::image type="content" source="./media/create-manage-projects/assess-migrate-servers.png" alt-text="Optie in Overzicht voor het evalueren en migreren van servers":::

4. Selecteer **in Servers** de optie Project **maken.**

    :::image type="content" source="./media/create-manage-projects/create-project.png" alt-text="Knop om te beginnen met het maken van een project":::

5. Selecteer **in Project maken** het Azure-abonnement en de resourcegroep. Maak een resourcegroep als u er nog geen hebt.
6. Geef in **Projectdetails** de projectnaam en het geografische gebied op waarin u het project wilt maken.
    - De geografie wordt alleen gebruikt voor het opslaan van de metagegevens die zijn verzameld van on-premises servers. U kunt elke doelregio voor migratie selecteren. 
    - Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government). 


    > [!Note]
    > Gebruik de **sectie Geavanceerde** configuratie om een nieuw project Azure Migrate privé-eindpuntconnectiviteit te maken. [Meer informatie](how-to-use-azure-migrate-with-private-endpoints.md#create-a-project-with-private-endpoint-connectivity) 

7. Selecteer **Maken**.

     :::image type="content" source="./media/create-manage-projects/project-details.png" alt-text="Projectinstellingen voor pagina-naar-invoer":::


Wacht enkele minuten tot het project is geïmplementeerd.

## <a name="create-a-project-in-a-specific-region"></a>Een project maken in een specifieke regio

In de portal kunt u de geografie selecteren waarin u het project wilt maken. Als u het project binnen een specifieke Azure-regio wilt maken, gebruikt u de volgende API-opdracht om het project te maken.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Aanvullende projecten maken

Als u al een project hebt en u een extra project wilt maken, gaat u als volgt te werk:  

1. Zoek in [de openbare Azure-portal](https://portal.azure.com) [of Azure Government](https://portal.azure.us)naar **Azure Migrate**.
2. Selecteer op Azure Migrate dashboard > **Servers** de optie **Wijzigen** in de rechterbovenhoek.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Project wijzigen":::

3. Selecteer klik hier om een nieuw project **te maken.**


## <a name="find-a-project"></a>Een project zoeken

Zoek als volgt een project:

1. Zoek in [Azure Portal](https://portal.azure.com)naar *Azure Migrate*.
2. Selecteer in Azure Migrate dashboard > **Servers** de **optie Wijzigen** in de rechterbovenhoek.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Overschakelen naar een bestaand project":::

3. Selecteer het juiste abonnement en project.


### <a name="find-a-classic-project"></a>Een klassiek project zoeken

Als u het project in de [vorige versie van](migrate-services-overview.md#azure-migrate-versions) Azure Migrate, gaat u als volgt te werk:

1. Zoek in [Azure Portal](https://portal.azure.com)naar *Azure Migrate*.
2. Als Azure Migrate in de vorige versie een project hebt gemaakt, wordt er een banner weergegeven die verwijst naar oudere projecten. Selecteer de banner.

    :::image type="content" source="./media/create-manage-projects/access-existing-projects.png" alt-text="Toegang tot bestaande projecten":::

3. Bekijk de lijst met oude projecten.


## <a name="delete-a-project"></a>Een project verwijderen

Verwijder deze als volgt:

1. Open de Azure-resourcegroep waarin het project is gemaakt.
2. Selecteer verborgen typen tonen op de **pagina van de resourcegroep.**
3. Selecteer het project dat u wilt verwijderen en de bijbehorende resources.
    - Het resourcetype is **Microsoft.Migrate/migrateprojects.**
    - Als de resourcegroep uitsluitend wordt gebruikt door het project, kunt u de hele resourcegroep verwijderen.

Opmerking:

- Wanneer u verwijdert, worden zowel het project als de metagegevens over gedetecteerde servers verwijderd.
- Als u de oudere versie van Azure Migrate gebruikt, opent u de Azure-resourcegroep waarin het project is gemaakt. Selecteer het project dat u wilt verwijderen (het resourcetype is **Migratieproject).**
- Als u afhankelijkheidsanalyse gebruikt met een Azure Log Analytics-werkruimte:
    - Als u een Log Analytics-werkruimte hebt gekoppeld aan het hulpprogramma Serverevaluatie, wordt de werkruimte niet automatisch verwijderd. Dezelfde Log Analytics-werkruimte kan worden gebruikt voor meerdere scenario's.
    - Als u de Log Analytics-werkruimte wilt verwijderen, doet u dat handmatig.
- Het verwijderen van een project kan niet ongedaan worden maken. Verwijderde objecten kunnen niet worden hersteld.

### <a name="delete-a-workspace-manually"></a>Een werkruimte handmatig verwijderen

1. Blader naar de Log Analytics-werkruimte die is gekoppeld aan het project.

    - Als u het project nog niet hebt verwijderd, kunt u de koppeling naar de werkruimte vinden in **Essentials**  >  **Server Assessment**.
    :::image type="content" source="./media/create-manage-projects/loganalytics-workspace.png" alt-text="LA-werkruimte":::
       
    - Als u het project al hebt verwijderd, selecteert u **Resourcegroepen** in het linkerdeelvenster van het Azure Portal zoek de werkruimte.
       
2. [Volg de instructies om](../azure-monitor/logs/delete-workspace.md) de werkruimte te verwijderen.

## <a name="next-steps"></a>Volgende stappen

[Hulpprogramma's voor](how-to-assess.md) evaluatie [of migratie](how-to-migrate.md) toevoegen aan projecten.