---
title: De Azure Cosmos-data base scannen (SQL-API)
description: Hier vindt u informatie over het scannen van de Azure Cosmos-data base (SQL API).
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: e1d2035b787380d9b93943b92fbe81c09fc6a527
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553170"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Azure Cosmos-data base registreren en scannen (SQL-API)

In dit artikel wordt beschreven hoe u een Azure Cosmos data base-account (SQL-API) registreert in azure controle sfeer liggen en een scan instelt.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De Azure Cosmos-data base (SQL-API) ondersteunt volledige en incrementele scans voor het vastleggen van de meta gegevens en het schema. Met scans worden de gegevens ook automatisch geclassificeerd op basis van systeem-en aangepaste classificatie regels.

## <a name="prerequisites"></a>Vereisten

- Voordat u gegevens bronnen registreert, maakt u een Azure controle sfeer liggen-account. Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)voor meer informatie over het maken van een controle sfeer liggen-account.
- U moet een Azure controle sfeer liggen-gegevens bron beheerder zijn

## <a name="setting-up-authentication-for-a-scan"></a>Verificatie instellen voor een scan

Er is slechts één manier om verificatie in te stellen voor de Azure Cosmos-data base (SQL-API):

- Accountsleutel
 
### <a name="account-key"></a>Accountsleutel

Wanneer de geselecteerde verificatie methode de **account sleutel** is, moet u uw toegangs sleutel ophalen en opslaan in de sleutel kluis:

1. Navigeer naar uw Cosmos DB-account in de Azure Portal 
1. **Instellingen**  >  **sleutels** selecteren 
1. Kopieer uw *sleutel* en sla deze ergens op om de volgende stappen uit te voeren
1. Navigeer naar uw sleutelkluis
1. **Instellingen > geheimen** selecteren
1. Selecteer **+ genereren/importeren** en voer de **naam** en **waarde** in als de *sleutel* van uw opslag account
1. Selecteer **maken** om te volt ooien
1. Als uw sleutel kluis nog niet is verbonden met controle sfeer liggen, moet u [een nieuwe sleutel kluis verbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak ten slotte [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de sleutel voor het instellen van de scan

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Een SQL-API-account (Azure Cosmos data base) registreren

Ga als volgt te werk om een nieuw Azure Cosmos data base-account (SQL-API) in uw Data Catalog te registreren:

1. Navigeer naar uw controle sfeer liggen-account
1. **Bronnen** selecteren in de linkernavigatiebalk
1. Selecteer **Registreren**
1. Selecteer **Azure Cosmos DB (SQL-API)** bij **bronnen registreren**.
1. Selecteer **Doorgaan**

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="nieuwe gegevens bron registreren" border="true":::

Ga als volgt te werk op het scherm **bronnen registreren (Azure Cosmos DB (SQL-API))** :

1. Voer een **naam** in die voor de gegevens bron wordt weer gegeven in de catalogus.
1. Kies hoe u wilt verwijzen naar het gewenste opslag account:
   1. Selecteer een **Azure-abonnement**, selecteer het juiste abonnement in de vervolg keuzelijst van het Azure- **abonnement** en het relevante cosmosDB-account in de vervolg keuzelijst **Cosmos DB account naam** .
   1. U kunt **ook hand matig invoeren** selecteren en een service-eind punt (URL) invoeren.
1. **Volt ooien** om de gegevens bron te registreren.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="bronnen opties registreren" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure controle sfeer liggen Data Catalog](how-to-browse-catalog.md)
- [Zoek in de Azure controle sfeer liggen-Data Catalog](how-to-search-catalog.md)
