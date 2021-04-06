---
title: De Azure Cosmos-data base scannen (SQL-API)
description: Hier vindt u informatie over het scannen van de Azure Cosmos-data base (SQL API).
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 1aaeed1973ebd15af312b722ab61938aa4271947
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696250"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Azure Cosmos-data base registreren en scannen (SQL-API)

In dit artikel wordt beschreven hoe u een Azure Cosmos data base-account (SQL-API) registreert in azure controle sfeer liggen en een scan instelt.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De Azure Cosmos-data base (SQL-API) ondersteunt volledige en incrementele scans voor het vastleggen van de meta gegevens en het schema. Met scans worden de gegevens ook automatisch geclassificeerd op basis van systeem-en aangepaste classificatie regels.

## <a name="prerequisites"></a>Vereisten

- Voordat u gegevens bronnen registreert, maakt u een Azure controle sfeer liggen-account. Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)voor meer informatie over het maken van een controle sfeer liggen-account.
- U moet een Azure controle sfeer liggen-gegevens bron beheerder zijn

## <a name="setting-up-authentication-for-a-scan"></a>Verificatie voor een scan instellen

Er is slechts één manier om verificatie in te stellen voor de Azure Cosmos-data base (SQL-API):

- Accountsleutel
 
### <a name="account-key"></a>Accountsleutel

Wanneer de geselecteerde verificatie methode de **account sleutel** is, moet u uw toegangs sleutel ophalen en opslaan in de sleutel kluis:

1. Navigeer naar uw Cosmos DB-account in de Azure Portal 
1. **Instellingen**  >  **sleutels** selecteren 
1. Kopieer uw *sleutel* en sla deze ergens op om de volgende stappen uit te voeren
1. Navigeer naar uw sleutelkluis
1. Selecteer **Instellingen > Geheimen**
1. Selecteer **+ genereren/importeren** en voer de **naam** en **waarde** in als de *sleutel* van uw opslag account
1. Selecteer **Maken** om te voltooien
1. Als uw sleutelkluis nog niet is verbonden met Purview, moet u [een nieuwe sleutelkluisverbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak ten slotte [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de sleutel voor het instellen van de scan

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Een SQL-API-account (Azure Cosmos data base) registreren

Ga als volgt te werk om een nieuw Azure Cosmos data base-account (SQL-API) in uw Data Catalog te registreren:

1. Ga naar uw Purview-account
1. Selecteer **Bronnen** in het linkernavigatievenster
1. Selecteer **Registreren**
1. Selecteer **Azure Cosmos DB (SQL-API)** bij **bronnen registreren**.
1. Selecteer **Doorgaan**

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="nieuwe gegevensbron registreren" border="true":::

Ga als volgt te werk op het scherm **bronnen registreren (Azure Cosmos DB (SQL-API))** :

1. Voer een **Naam** in waarvan de gegevensbron wordt vermeld in de catalogus.
1. Kies hoe u wilt verwijzen naar het gewenste opslagaccount:
   1. Selecteer een **Azure-abonnement**, selecteer het juiste abonnement in de vervolg keuzelijst van het Azure- **abonnement** en het relevante cosmosDB-account in de vervolg keuzelijst **Cosmos DB account naam** .
   1. U kunt **ook hand matig invoeren** selecteren en een service-eind punt (URL) invoeren.
1. **Voltooi** om de gegevensbron te registreren.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="opties voor bronnen registreren" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)
