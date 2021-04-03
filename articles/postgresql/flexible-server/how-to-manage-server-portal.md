---
title: Server-Azure Portal-Azure Database for PostgreSQL-flexibele server beheren
description: Meer informatie over het beheren van een Azure Database for PostgreSQL flexibele server vanuit de Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: mvc
ms.openlocfilehash: 1ac418d855696138341115412dc7e2601d4cf3a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91961405"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Een Azure Database for PostgreSQL flexibele server beheren met behulp van de Azure Portal

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

In dit artikel wordt beschreven hoe u uw Azure Database for PostgreSQL-flexibele server beheert. Beheer taken zijn onder andere berekening en opslag schalen, beheerders wachtwoord opnieuw instellen en server details weer geven.

## <a name="sign-in"></a>Aanmelden

Meld u aan bij de [Azure-portal](https://portal.azure.com). Ga naar de flexibele server bron in de Azure Portal.

## <a name="scale-compute-and-storage"></a>Schaal berekening en opslag

Nadat de server is gemaakt, kunt u de verschillende [prijs categorieën](https://azure.microsoft.com/pricing/details/postgresql/) aanpassen als uw behoeften veranderen. U kunt berekening en geheugen ook omhoog of omlaag schalen door vCores te vergroten of te verkleinen.

> [!NOTE]
> De opslag kan niet omlaag worden geschaald naar een lagere waarde.

1. Selecteer uw server in de Azure Portal. Selecteer **Compute + Storage**, te vinden in de sectie **instellingen** .
2. U kunt de **Compute-laag** , **vCore**, **opslag** wijzigen om de server omhoog te schalen met een hogere Compute-laag of omhoog schalen binnen dezelfde laag door opslag of vCores te verhogen naar de gewenste waarde.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="flexibele server voor opslag schalen":::

> [!Important]
> - De opslag kan niet omlaag worden geschaald.
> - Het schalen van vCores zorgt ervoor dat de server opnieuw wordt opgestart.

3. Selecteer **OK** om de wijzigingen op te slaan.

## <a name="reset-admin-password"></a>Beheerders wachtwoord opnieuw instellen

U kunt het wacht woord van de beheerdersrol wijzigen met behulp van de Azure Portal.

1. Selecteer uw server in de Azure Portal. Selecteer **wacht woord opnieuw instellen** in het venster **overzicht** .
2. Voer een nieuw wachtwoord in en bevestig het wachtwoord. In het tekstvak wordt u gevraagd om de vereisten voor wachtwoord complexiteit.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="uw wacht woord opnieuw instellen voor een flexibele server":::

3. Selecteer **Opslaan** om het nieuwe wacht woord op te slaan.

## <a name="delete-a-server"></a>Een server verwijderen

U kunt uw server verwijderen als u deze niet meer nodig hebt.

1. Selecteer uw server in de Azure Portal. Selecteer **verwijderen** in het venster **overzicht** .
2. Typ de naam van de server in het invoervak om te bevestigen dat u de server wilt verwijderen.

   :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="de flexibele server verwijderen":::

   > [!IMPORTANT]
   > Het verwijderen van een server is onomkeerbaar.

  > [!div class="mx-imgBorder"]
  > ![de flexibele server verwijderen](./media/howto-manage-server-portal/delete-server.png)  

3. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over back-up-en herstel concepten](concepts-backup-restore.md)
- [De server afstemmen en bewaken](concepts-monitoring.md)
