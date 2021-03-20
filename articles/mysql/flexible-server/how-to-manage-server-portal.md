---
title: Server-Azure Portal-Azure Database for MySQL flexibele server beheren
description: Meer informatie over het beheren van een Azure Database for MySQL flexibele server vanuit de Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7a01863b3a0c29e94550be67ca957655cff32660
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90935070"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Een Azure Database for MySQL-flexibele server (preview) beheren met Azure Portal


> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

Dit artikel laat u zien hoe u uw Azure Database for MySQL flexibele servers kunt beheren (preview). Beheer taken zijn onder andere berekening en opslag schalen, het beheerders wachtwoord voor de rest-server en het verwijderen van uw server.

## <a name="sign-in"></a>Aanmelden
Meld u aan bij de [Azure-portal](https://portal.azure.com). Ga naar de flexibele server bron in de Azure Portal.

## <a name="scale-compute-and-storage"></a>Schaal berekening en opslag

Nadat de server is gemaakt, kunt u de verschillende [prijs categorieën](https://azure.microsoft.com/pricing/details/mysql/) aanpassen als uw behoeften veranderen. U kunt berekening en geheugen ook omhoog of omlaag schalen door vCores te vergroten of te verkleinen.

1. Selecteer uw server in de Azure Portal. Selecteer **Compute + Storage**, te vinden in de sectie **instellingen** .

2. U kunt de **Compute-laag**, **vCore**, **opslag** wijzigen om de server omhoog te schalen met een hogere Compute-laag of omhoog schalen binnen dezelfde laag door opslag of vCores te verhogen naar een waarde die u wilt.

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

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="de flexibele server verwijderen":::

   > [!NOTE]
   > Het verwijderen van een server is onomkeerbaar.

3. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over het starten of stoppen van een server](how-to-stop-start-server-portal.md)
- [Meer informatie over het herstellen van een server](how-to-restore-server-portal.md)
- [Verbindingsproblemen oplossen](how-to-troubleshoot-common-connection-issues.md)

