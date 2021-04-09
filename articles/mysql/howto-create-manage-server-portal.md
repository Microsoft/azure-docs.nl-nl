---
title: Server-Azure Portal-Azure Database for MySQL beheren
description: Meer informatie over het beheren van een Azure Database for MySQL-server vanuit de Azure Portal.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/26/2021
ms.openlocfilehash: 83876f77e0d7ffc0ae20bc5a545c1f18f53f4a8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897982"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Een Azure Database for MySQL-server beheren met behulp van de Azure Portal

Dit artikel laat u zien hoe u uw Azure Database for MySQL-servers kunt beheren. Beheer taken zijn onder andere berekening en opslag schalen, beheerders wachtwoord opnieuw instellen en server details weer geven.

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term _Slave_, een term die door micro soft niet meer wordt gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.
>

## <a name="sign-in"></a>Aanmelden

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-server"></a>Een server maken

Ga naar de [Quick](quickstart-create-mysql-server-database-using-azure-portal.md) start voor informatie over het maken en aan de slag met een Azure database for mysql-server.

## <a name="scale-compute-and-storage"></a>Schaal berekening en opslag

Nadat de server is gemaakt, kunt u de schaal aanpassen tussen de lagen Algemeen en geoptimaliseerd voor geheugen als uw behoeften veranderen. U kunt reken kracht en geheugen ook schalen door de vCores te verg Roten of te verkleinen. Opslag kan omhoog worden geschaald (u kunt de opslag echter niet omlaag schalen).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Schaal tussen Algemeen en geoptimaliseerde geheugen lagen

U kunt schalen van Algemeen naar geoptimaliseerd voor geheugen en vice versa. Wijzigen van en van de Basic-laag nadat het maken van de server niet wordt ondersteund.

1. Selecteer uw server in de Azure Portal. Selecteer de **prijs categorie** die u in de sectie **instellingen** bevindt.

2. Selecteer **Algemeen** of **geoptimaliseerd geheugen**, afhankelijk van wat u wilt schalen.

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="Scherm opname van Azure Portal om de laag basis, Algemeen of geoptimaliseerd voor geheugen te kiezen in Azure Database for MySQL":::

   > [!NOTE]
   > Wanneer u de lagen wijzigt, wordt de server opnieuw opgestart.

3. Selecteer **OK** om de wijzigingen op te slaan.

### <a name="scale-vcores-up-or-down"></a>VCores omhoog of omlaag schalen

1. Selecteer uw server in de Azure Portal. Selecteer de **prijs categorie** die u in de sectie **instellingen** bevindt.

2. Wijzig de instelling **vCore** door de schuif regelaar naar de gewenste waarde te verplaatsen.

    :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="Scherm opname van Azure Portal om de optie vCore te kiezen in Azure Database for MySQL":::

    > [!NOTE]
    > Het schalen van vCores zorgt ervoor dat de server opnieuw wordt opgestart.

3. Selecteer **OK** om de wijzigingen op te slaan.

### <a name="scale-storage-up"></a>Opslag omhoog schalen

1. Selecteer uw server in de Azure Portal. Selecteer de **prijs categorie** die u in de sectie **instellingen** bevindt.

2. Wijzig de **opslag** instelling door de schuif regelaar omhoog te verplaatsen naar de gewenste waarde.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="Scherm opname van Azure Portal voor het kiezen van opslag schaal in Azure Database for MySQL":::

   > [!NOTE]
   > De opslag kan niet omlaag worden geschaald.

3. Selecteer **OK** om de wijzigingen op te slaan.

## <a name="update-admin-password"></a>Beheerders wachtwoord bijwerken

U kunt het wacht woord van de beheerdersrol wijzigen met behulp van de Azure Portal.

1. Selecteer uw server in de Azure Portal. Selecteer **wacht woord opnieuw instellen** in het venster **overzicht** .

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="Scherm opname van Azure Portal om het wacht woord in Azure Database for MySQL opnieuw in te stellen":::

2. Voer een nieuw wachtwoord in en bevestig het wachtwoord. In het tekstvak wordt u gevraagd om de vereisten voor wachtwoord complexiteit.

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Scherm opname van Azure Portal om uw wacht woord opnieuw in te stellen en op te slaan in Azure Database for MySQL":::

3. Selecteer **OK** om het nieuwe wacht woord op te slaan.
 

> [!IMPORTANT]
> Als u het beheerderswachtwoord voor de server opnieuw instelt, worden de beheerdersbevoegdheden voor de server automatisch opnieuw ingesteld op de standaardwaarden. Overweeg het opnieuw instellen van het wacht woord voor de server beheerder als u per ongeluk een of meer van de beheerders bevoegdheden voor de server hebt ingetrokken.
   
> [!NOTE]
> De server beheerder gebruiker heeft standaard de volgende bevoegdheden: selecteren, invoegen, bijwerken, verwijderen, maken, verwijderen, opnieuw laden, verwerken, verwijzingen, INDEX, wijzigen, data BASEs weer geven, tijdelijke tabellen maken, tabellen vergren delen, uitvoeren, replicatie slave, replicatie CLIENT, weer gave, gebruiker weer geven, gebeurtenis, TRIGGER

## <a name="delete-a-server"></a>Een server verwijderen

U kunt uw server verwijderen als u deze niet meer nodig hebt.

1. Selecteer uw server in de Azure Portal. Selecteer **verwijderen** in het venster **overzicht** .

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="Scherm opname van Azure Portal om de server in Azure Database for MySQL te verwijderen":::

2. Typ de naam van de server in het invoervak om te bevestigen dat dit de server is die u wilt verwijderen.

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="Scherm opname van Azure Portal om het verwijderen van de server in Azure Database for MySQL te bevestigen":::

   > [!NOTE]
   > Het verwijderen van een server is onomkeerbaar.

3. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [back-ups en server herstel](howto-restore-server-portal.md)
- Meer informatie over [Opties voor afstemmen en controleren in azure database for MySQL](concepts-monitoring.md)
