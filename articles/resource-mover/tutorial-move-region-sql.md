---
title: Azure SQL-resources verplaatsen tussen regio's met Azure Resource Mover
description: Leer hoe u Azure SQL-resources naar een andere regio kunt verplaatsen met Azure Resource Mover
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9fe43125c83436f89bf93cbe975317efec2beb46
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542810"
---
# <a name="tutorial-move-azure-sql-database-resources-to-another-region"></a>Zelfstudie: Azure SQL Database-resources naar een andere regio verplaatsen

In deze zelfstudie leert u hoe u Azure SQL-databases en elastische pools naar een andere Azure-regio kunt verplaatsen met behulp van [Azure Resource Mover](overview.md).

> [!NOTE]
> Azure Resource Mover is momenteel in preview.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De vereisten controleren.
> * De resources selecteren die u wilt exporteren.
> * Resourceafhankelijkheden oplossen.
> * De SQL Server voorbereiden en verplaatsen naar de doelregio.
> * Databases en elastische pools voorbereiden en verplaatsen.
> * Beslissen of u de verplaatsing wilt doorvoeren of niet. 
> * Desgewenst resources in de bronregio verwijderen na de verplaatsing. 

> [!NOTE]
> Zelfstudies laten de snelste manier zien om een scenario uit te proberen, en gebruiken de standaardopties. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint. Meld u vervolgens aan bij de [Azure-portal](https://portal.azure.com).

## <a name="prerequisites"></a>Vereisten

-  Controleer of u *Eigenaar*-toegang hebt voor het abonnement dat de resources bevat die u wilt verplaatsen.
    - De eerste keer dat u een resource toevoegt voor een specifiek bron- en doelpaar in een Azure-abonnement maakt Resource Mover een [door het systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (vroeger MSI genoemd (Managed Service Identity)) die door het abonnement wordt vertrouwd.
    - Om de identiteit te maken en deze de juiste rol toe te wijzen (Inzender of Administrator voor gebruikerstoegang in het bronabonnement), moet het account dat u gebruikt om resources toe te voegen *Eigenaar* smachtigingen hebben voor het abonnement. [Meer informatie](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) over rollen in Azure.
- Het abonnement moet voldoende quota hebben om de resources die u verplaatst in de doelregio te maken. Als de quota onvoldoende zijn, moet u [hogere limieten aanvragen](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Verifieer prijzen en kosten voor de doelregio waarnaar u resources verplaatst. Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om u daarbij te helpen.
    

## <a name="check-sql-requirements"></a>SQL-vereisten controleren

1. [Controleer](support-matrix-move-region-sql.md) welke database/elastische pool-functies worden ondersteund voor verplaatsen naar een andere regio.
2. Maak in de doelregio een doelserver voor elke bronserver. [Meer informatie](../azure-sql/database/active-geo-replication-security-configure.md#how-to-configure-logins-and-users).
4. Als databases zijn versleuteld met TDE (Transparent Data Encryption) en u uw eigen versleutelingssleutel gebruikt in Azure Key Vault, [kunt u hier meer lezen](../key-vault/general/move-region.md) over het verplaatsen van sleutelkluizen naar een andere regio.
5. Als SQL Data Sync is ingeschakeld, wordt het verplaatsen van liddatabases ondersteund. Na de verplaatsing moet u SQL Data Sync instellen voor de nieuwe doeldatabase.
6. Verwijder geavanceerde gegevensbeveiligingsinstellingen vóór de verplaatsing. Na de verplaatsing moet u in de doelregio de [instellingen configureren](../azure-sql/database/azure-defender-for-sql.md) op SQL Server-niveau.
7. Als controle is ingeschakeld, worden beleidsregels na de verplaatsing ingesteld op de standaardwaarden. [Stel de controle](../azure-sql/database/auditing-overview.md) opnieuw in na de verplaatsing.
7. Het bewaarbeleid voor back-ups van de brondatabase wordt meegenomen naar de doeldatabase. [Meer informatie](../azure-sql/database/long-term-backup-retention-configure.md) over het wijzigen van instellingen na het verplaatsen.
8. Verwijder firewallregels op serverniveau vóór de verplaatsing. Firewall regels op databaseniveau worden tijdens de verplaatsing gekopieerd van de bronserver naar de doelserver. Na de verplaatsing moet u [firewallregels instellen](../azure-sql/database/firewall-create-server-level-portal-quickstart.md) voor SQL Server in de doelregio.
9. Verwijder Autotuning-instellingen vóór de verplaatsing. [Stel Autotuning](../azure-sql/database/automatic-tuning-enable.md) na het verplaatsen opnieuw in.
10. Verwijder waarschuwingsinstellingen voor de database vóór de verplaatsing. [Stel ze opnieuw in](../azure-sql/database/alerts-insights-configure-portal.md) na het verplaatsen.
    
## <a name="select-resources"></a>Resources selecteren

Selecteer de resources die u wilt verplaatsen.

- U kunt elk ondersteund resourcetype selecteren in elke resourcegroep in de geselecteerde bronregio.
- U verplaatst resources naar een doelregio in hetzelfde abonnement als de bronregio. Als u het abonnement wilt wijzigen, kunt u dat doen nadat de resources zijn verplaatst.

1. Zoek in de Azure-portal naar *resource mover*. Selecteer vervolgens **Azure Resource Mover** onder **Services**.

     ![Zoekresultaten voor resource mover in de Azure-portal](./media/tutorial-move-region-sql/search.png)

2. Klik in **Overzicht** op **Aan de slag**.

    ![Knop voor het toevoegen van naar een andere regio te verplaatsen resources](./media/tutorial-move-region-sql/get-started.png)

3. Selecteer in **Resources verplaatsen** > **Bron en doel** het bronabonnement en de regio.
4. Selecteer in **Doel** de regio waarnaar u de resources wilt verplaatsen. Klik op **Volgende**.

    ![Pagina voor het selecteren van bron- en doelregio](./media/tutorial-move-region-sql/source-target.png)

6. Klik in **Resources die moeten worden verplaatst** op **Resources selecteren**.
7. Selecteer de resources in **Resources selecteren**. U kunt alleen resources selecteren waarvoor verplaatsing wordt ondersteund. Klik vervolgens op **Gereed**.

    ![Pagina voor het selecteren van SQL-resources die moeten worden verplaatst](./media/tutorial-move-region-sql/select-resources.png)

8. Klik in **Resources die moeten worden verplaatst** op **Volgende**.

9. Controleer de bron-en doel instellingen in **Controleren en toevoegen**. Geef aan dat u begrijpt dat metagegevens over de verplaatsing worden opgeslagen in een voor dit doel gemaakte resourcegroep in de metagegevensregio.


    ![Pagina voor het controleren van instellingen en doorgaan met de verplaatsing](./media/tutorial-move-region-sql/review.png)

10. Klik op **Doorgaan** om te beginnen met toevoegen van de resources.
11. Klik nadat het toevoegen geslaagd is op **Resources toevoegen** in het meldingspictogram.
12. Controleer nadat u op de melding hebt geklikt de resources op de pagina **Tussen regio's**.


> [!NOTE]
> 
> - De SQL Server heeft nu de status *Handmatige toewijzing in behandeling*.
> - Andere toegevoegde resources hebben de status *Voorbereiding in behandeling*.
> - Als u een resource uit een te verplaatsen verzameling wilt verwijderen, hangt de manier waarop u dat doet af van de plaats waar u zich in het verplaatsingsproces bevindt. [Meer informatie](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Afhankelijkheden oplossen


1. Als bij resources in **Tussen regio's** het bericht *Afhankelijkheden controleren* staat in de kolom **Problemen**, klikt u op de knop **Afhankelijkheden controleren**. Het validatieproces wordt gestart.
2. Als er afhankelijkheden worden gevonden, klikt u op **Afhankelijkheden toevoegen**.

    ![Knop voor het toevoegen van afhankelijkheden](./media/tutorial-move-region-sql/add-dependencies.png)
   
3. Selecteer in **Afhankelijkheden toevoegen** de afhankelijke Resources > **Afhankelijkheden toevoegen**. Controleer de voortgang in de meldingen.

4. Voeg indien nodig extra afhankelijkheden toe en valideer afhankelijkheden opnieuw. 

5. Controleer op de pagina **Tussen regio's** of de resources nu de status *Voorbereiding in behandeling* bevinden, zonder problemen.

    ![Pagina met resources met de status Voorbereiding in behandeling](./media/tutorial-move-region-sql/prepare-pending.png)



## <a name="move-the-sql-server"></a>SQL Server verplaatsen

Wijs een doel-SQL Server toe in de doelregio en voer de verplaatsing door.

### <a name="assign-a-target-sql-server"></a>Een doel-SQL Server toewijzen

1. Klik in **Tussen regio's** voor de SQL Server-resource in de kolom **Doelconfiguratie** op **Resource niet toegewezen**.
2. Selecteer een bestaande SQL Server-resource in de doelregio. 
    
    ![Vermelding met SQL Server-status ingesteld op Verplaatsing doorvoeren in behandeling](./media/tutorial-move-region-sql/sql-server-commit-move-pending.png) 

    
> [!NOTE]
> De status van de bron-SQL Server verandert in *Verplaatsing doorvoeren in behandeling*. 

### <a name="commit-the-sql-server-move"></a>Verplaatsing van de SQL Server doorvoeren

1. Selecteer in **Tussen regio's** de SQL Server en klik op  **Verplaatsing doorvoeren**.
2. Klik in **Resources doorvoeren** op **Doorvoeren**.

    ![Pagina voor het doorvoeren van de verplaatsing van de SQL Server](./media/tutorial-move-region-sql/commit-sql-server.png)

3. Controleer de voortgang in de meldingenbalk.

> [!NOTE]
> Na het doorvoeren heeft de SQL Server nu de status *Verwijderen bron in behandeling*.


## <a name="prepare-resources-to-move"></a>Te verplaatsen resources voorbereiden

Nadat de bron-SQL Server is verplaatst, kunt u het verplaatsen van de andere resources voorbereiden.

## <a name="prepare-an-elastic-pool"></a>Een elastische pool voorbereiden

1. Selecteer in **Tussen regio's** de elastische pool die de bron is (demo-test1-elasticpool in de walkthrough) en klik op **Voorbereiden**.

    ![Knop voor het voorbereiden van resources](./media/tutorial-move-region-sql/prepare-elastic.png)

2. Klik in **Resources voorbereiden** op **Voorbereiden**.
3. Wanneer u in de meldingen ziet dat het voorbereidingsproces is geslaagd, klikt u op **Vernieuwen**.

> [!NOTE]
> De elastische pool heeft nu de status *Initiëren verplaatsing in behandeling*.

## <a name="prepare-a-single-database"></a>Eén database voorbereiden

1. Selecteer in **Tussen regio's** de ene database (niet in een elastische pool) en klik vervolgens op **Voorbereiden**.

    ![Knop voor het voorbereiden van geselecteerde resources](./media/tutorial-move-region-sql/prepare-db.png)

2. Klik in **Resources voorbereiden** op **Voorbereiden**.
3. Wanneer u in de meldingen ziet dat het voorbereidingsproces is geslaagd, klikt u op **Vernieuwen**.

> [!NOTE]
> De database heeft nu de status *Initiëren verplaatsing in behandeling*, en is gemaakt in de doelregio.


## <a name="move-the-pool-and-prepare-pool-databases"></a>De pool verplaatsen en pooldatabases voorbereiden

Om databases in een elastische pool voor te bereiden, moet de elastische pool de status *Verplaatsing doorvoeren in behandeling* hebben. Initieer de verplaatsing voor de pool om naar deze status te gaan.

#### <a name="initiate-move---elastic-pool"></a>Verplaatsing initiëren: elastische pool

1. Selecteer in **Tussen regio's** de elastische pool die de bron is (demo-test1-elasticpool in de walkthrough) en klik op **Verplaatsing initiëren**.
2. Klik in **Resources verplaatsen** op **Verplaatsing initiëren**.

    
    ![Knop voor het initiëren van de verplaatsing van een elastische pool](./media/tutorial-move-region-sql/initiate-elastic.png)

1. Controleer de voortgang in de meldingenbalk.
1. Wanneer u in de meldingen ziet dat de verplaatsing is geslaagd, klikt u op **Vernieuwen**.

> [!NOTE]
> De elastische pool heeft nu de status *Verplaatsing doorvoeren in behandeling*.

#### <a name="prepare-database"></a>Database voorbereiden

1. Selecteer in **Tussen regio's** de database (demo-test2-sqldb in de walkthrough) en klik op **Voorbereiden**.
2. Klik in **Resources voorbereiden** op **Voorbereiden**.

    ![Knop voor voorbereiden database in elastische pool](./media/tutorial-move-region-sql/prepare-database-elastic.png) 

Tijdens het voorbereiden wordt de doeldatabase gemaakt in de doelregio en begint de gegevensreplicatie. Na de voorbereiding heeft de database de status *Initiëren verplaatsing in behandeling*. 

![Knop voor het voorbereiden van de geselecteerde database in de elastische pool](./media/tutorial-move-region-sql/initiate-move-pending.png) 

## <a name="move-databases"></a>Databases verwijderen

Begin met het verplaatsen van de databases.
1. Selecteer in **Tussen regio's** resources met de status **Initiëren verplaatsing in behandeling**. Klik op **Verplaatsing initiëren**.
2. Klik in **Resources verplaatsen** op **Verplaatsing initiëren**.

    ![Pagina voor initiëren verplaatsing](./media/tutorial-move-region-sql/initiate-move.png)

3. Controleer de voortgang in de meldingenbalk.

> [!NOTE]
> De databases hebben nu de status *Verplaatsing doorvoeren in behandeling*.


## <a name="discard-or-commit"></a>Verwijderen of doorvoeren?

Na de eerste verplaatsing kunt u beslissen of u de verplaatsing wilt doorvoeren of verwijderen. 

- **Verwijderen**: Mogelijk wilt u een verplaatsing verwijderen als u een test uitvoert en de bronresource niet echt wilt verplaatsen. Als u de verplaatsing negeert, wordt de resource teruggezet in de status **Initiëren verplaatsing in behandeling**.
- **Doorvoeren**: Met doorvoeren wordt de verplaatsing naar de doelregio voltooid. Na het doorvoeren heeft een bronresource de status **Verwijderen bron in behandeling** en kunt u besluiten of u deze wilt verwijderen.


## <a name="discard-the-move"></a>De verplaatsing verwijderen 

U kunt de verplaatsing als volgt verwijderen:

1. Selecteer in **Tussen regio's** resources met de status **Verplaatsing doorvoeren in behandeling** en klik op **Verplaatsing verwijderen**.
2. Klik in **Verplaatsing verwijderen** op **Verwijderen**.
3. Controleer de voortgang in de meldingenbalk.


> [!NOTE]
> - Nadat de resources zijn genegeerd, bevinden ze zich in de status *Initiëren verplaatsing in behandeling*.
> - Als er alleen een elastische pool is, gaat het verwijderen door en wordt de in de doelregio gemaakte elastische pool verwijderd.
> - Als er een elastische pool met gekoppelde databases in de status *Verplaatsing doorvoeren in behandeling* is, kunt u de elastische pool niet verwijderen.
> - Als u een SQL-database verwijdert, worden de resources in de doelregio niet verwijderd. 

Als u de verplaatsing opnieuw wilt starten na het verwijderen, selecteert u de SQL-database of elastische pool en initieert u de verplaatsing opnieuw.


## <a name="commit-the-move"></a>De verplaatsing doorvoeren

Voltooi als volgt het verplaatsen van databases en elastische pools:


1. Controleer of de SQL Server de status *Verwijderen bron in behandeling* heeft.
2. Werk de databaseverbindingsreeksen bij naar de doelregio voordat u doorvoert.
3. Selecteer in **Tussen regio's** de SQL-resources en klik op  **Verplaatsing doorvoeren**.
4. Klik in **Resources doorvoeren** op **Doorvoeren**.

    ![Verplaatsing doorvoeren](./media/tutorial-move-region-sql/commit-sql-resources.png)

5. Controleer de voortgang van het doorvoeren in de meldingenbalk.


> [!NOTE]
> Tijdens het doorvoerproces treedt er enige downtime van de SQL databases op.
> Doorgevoerde databases en elastische pools hebben nu de status *Verwijderen bron in behandeling*.
> Werk na het doorvoeren de database-gerelateerde instellingen bij in de doeldatabase, zoals firewallregels, beleid en waarschuwingen.


## <a name="delete-source-resources-after-commit"></a>Bronresources verwijderen na doorvoeren

Na de verplaatsing kunt u desgewenst de resources in de bronregio verwijderen. 

1. Klik in **Tussen regio's** op de naam van elke bronresource die u wilt verwijderen.
2. Selecteer **Verwijderen** op de eigenschappenpagina van elke resource.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Azure SQL databases verplaatst naar een andere Azure-regio.
> * Azure SQL elastische pools verplaatst naar een andere regio.

Probeer nu Azure-VM's naar een andere regio te verplaatsen.

> [!div class="nextstepaction"]
> [Virtuele Azure-machines verplaatsen](./tutorial-move-region-virtual-machines.md)