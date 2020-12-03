---
title: Azure SQL DB scannen achter een firewall
description: Meer informatie over het scannen van bronnen achter een firewall met behulp van de Azure controle sfeer liggen-Portal.
author: sudhandkumar
ms.author: kumarsud
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/16/2020
ms.openlocfilehash: 4e1e76aaae2951e6d65fd1ebaaa5798ff417daf9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552593"
---
# <a name="scan-azure-sql-db-behind-a-firewall-in-azure-purview"></a>Azure SQL-data base scannen achter een firewall in azure controle sfeer liggen

In dit artikel leert u hoe u Azure controle sfeer liggen gebruikt voor het scannen van een bron achter een firewall.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Uw eigen [Azure Active Directory-Tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com).

## <a name="set-up-sql-storage-behind-a-firewall"></a>SQL-opslag instellen achter een firewall

De eerste stap bestaat uit het toevoegen van het MSI-bestand van de catalogus aan een Azure SQL-data base via de [registratie en het scannen van een Azure SQL database](register-scan-azure-sql-database.md).

## <a name="scan-sql-db-from-purview"></a>SQL-data base van controle sfeer liggen scannen

1. Ga naar de start pagina van controle sfeer liggen.

1. Selecteer **beheer centrum** in het navigatie venster aan de linkerkant.

1. Selecteer **gegevens bronnen** onder **bronnen en scannen**.

1. Selecteer **+ Nieuw** om de gegevens bron toe te voegen.

1. Selecteer **Azure SQL database**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-source.png" alt-text="Scherm opname van SQL Server selecteren.":::

1. Geef een naam op voor de nieuwe gegevens bron, selecteer **uit Azure-abonnement** en selecteer uw abonnement en server naam in de vervolg keuzelijst.

   Selecteer **volt ooien** om de registratie te volt ooien. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-register.png" alt-text="Scherm afbeelding om de selectie te volt ooien":::

1. Selecteer **Scan instellen** voor de opslag die zich achter de firewall bevindt en test de verbinding. De verbinding geeft aan dat het geslaagd is. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-setscan.png" alt-text="Scherm opname van de selectie T0 scan instellen.":::

1. Stel de scan frequentie in en selecteer **door gaan**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-once.png" alt-text="Scherm afbeelding van de selectie om de SQL-Scan te starten.":::

1.  De scan is voltooid en de status wordt bijgewerkt in de lijst met gegevens bronnen.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-success.png" alt-text="Scherm opname van de bericht scan is voltooid":::
   
## <a name="next-steps"></a>Volgende stappen

Vervolgens kunt u een regelset instellen [een scan regel maken](create-a-scan-rule-set.md) die is ingesteld op het samen stellen van de groep scans.
