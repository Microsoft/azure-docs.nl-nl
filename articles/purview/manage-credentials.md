---
title: Referenties voor scans maken en beheren
description: Meer informatie over de stappen voor het maken en beheren van referenties in azure controle sfeer liggen.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 3802d25ebd8f21ab5b8991a66ceb6650f2f276a9
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461705"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Referenties voor de verificatie van de bron in azure controle sfeer liggen

In dit artikel wordt beschreven hoe u referenties kunt maken in azure controle sfeer liggen. Met deze opgeslagen referenties kunt u snel opgeslagen verificatie gegevens opnieuw gebruiken en Toep assen op uw gegevens bron scans.

## <a name="prerequisites"></a>Vereisten

- Een Azure-sleutelkluis. Zie [Quick Start: een sleutel kluis maken met behulp van de Azure Portal](../key-vault/general/quick-create-portal.md)voor meer informatie over het maken van deze.

## <a name="introduction"></a>Introductie

Een referentie is verificatie-informatie die Azure controle sfeer liggen kan gebruiken voor verificatie bij uw geregistreerde gegevens bronnen. Er kan een referentie object worden gemaakt voor verschillende typen verificatie scenario's, zoals basis verificatie waarvoor gebruikers naam en wacht woord zijn vereist. Referentie Capture specifieke informatie die is vereist voor verificatie, op basis van het gekozen type verificatie methode. Referenties gebruiken uw bestaande Azure-sleutel kluizen geheimen om gevoelige verificatie gegevens op te halen tijdens het proces voor het maken van de referenties.

## <a name="use-purview-managed-identity-to-set-up-scans"></a>Controle sfeer liggen beheerde identiteit gebruiken om scans in te stellen

Als u de beheerde identiteit controle sfeer liggen gebruikt om scans in te stellen, hoeft u niet expliciet een referentie te maken en uw sleutel kluis te koppelen aan controle sfeer liggen om deze op te slaan. Raadpleeg de volgende secties van de gegevens bron-specifieke verificatie voor gedetailleerde instructies over het toevoegen van de beheerde identiteit controle sfeer liggen om toegang te krijgen tot uw gegevens bronnen:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Beheerde instantie Azure SQL Database](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Azure Key kluizen-verbindingen maken in uw Azure controle sfeer liggen-account

Voordat u een referentie kunt maken, moet u eerst een of meer van uw bestaande Azure Key Vault-exemplaren koppelen aan uw Azure controle sfeer liggen-account.

1. Selecteer uw Azure controle sfeer liggen-account in het [Azure Portal](https://portal.azure.com)en open Azure controle sfeer liggen Studio. Ga naar het **beheer centrum** in azure controle sfeer liggen Studio en navigeer vervolgens naar **referenties**.

2. Selecteer op de pagina **referenties** de optie **Key Vault verbindingen beheren**.

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Azure Key Vault verbindingen beheren":::

3. Selecteer **+ Nieuw** op de pagina Key Vault verbindingen beheren.

4. Geef de vereiste gegevens op en selecteer vervolgens **maken**.

5. Controleer of uw Key Vault is gekoppeld aan uw Azure controle sfeer liggen-account, zoals wordt weer gegeven in dit voor beeld:

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Bekijk Azure Key Vault verbindingen om te bevestigen.":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Controle sfeer liggen beheerde identiteit toegang verlenen tot uw Azure Key Vault

1. Navigeer naar uw Azure Key Vault.

2. Selecteer de pagina **toegangs beleid** .

3. Selecteer **toegangs beleid toevoegen**.

   :::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Controle sfeer liggen MSI toevoegen aan Azure":::

4. Selecteer in de vervolg keuzelijst **geheimen machtigingen** de optie **Get** and **List** permission.

5. Kies voor **Select Principal** de beheerde identiteit controle sfeer liggen. U kunt zoeken naar het controle sfeer liggen-MSI-bestand met behulp van de naam van het controle sfeer liggen-exemplaar **of** de toepassings-id van de beheerde identiteit. Er worden momenteel geen samengestelde identiteiten ondersteund (beheerde identiteits naam en toepassings-ID).

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Toegangsbeleid toevoegen":::

6. Selecteer **Toevoegen**.

7. Selecteer **Opslaan** om het toegangs beleid op te slaan.

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Toegangs beleid opslaan":::

## <a name="create-a-new-credential"></a>Een nieuwe referentie maken

Deze referentie typen worden ondersteund in controle sfeer liggen:

- Basis verificatie: u voegt het **wacht woord** toe als geheim in de sleutel kluis.
- Service-Principal: u voegt de sleutel van de **Service-Principal** toe als geheim in de sleutel kluis.
- SQL-verificatie: u voegt het **wacht woord** toe als geheim in de sleutel kluis.
- Account sleutel: u voegt de **account sleutel** toe als geheim in de sleutel kluis.
- Role ARN: Voeg uw **rol Arn** toe aan AWS voor een Amazon S3-gegevens bron. 

Zie [een geheim toevoegen aan Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) en [een nieuwe AWS-rol maken voor controle sfeer liggen](register-scan-amazon-s3.md#create-a-new-aws-role-for-purview)voor meer informatie.

Na het opslaan van uw geheimen in de sleutel kluis:

1. Ga in azure controle sfeer liggen naar de pagina referenties.

2. Maak uw nieuwe referentie door **+ Nieuw** te selecteren.

3. Geef de vereiste informatie op. Selecteer de **verificatie methode** en een **Key Vault verbinding** waaruit u een geheim wilt selecteren.

4. Als alle gegevens zijn ingevuld, selecteert u **maken**.

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Nieuwe referentie":::

5. Controleer of de nieuwe referentie wordt weer gegeven in de lijst weergave en gereed is voor gebruik.

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Referentie weer geven":::

## <a name="manage-your-key-vault-connections"></a>Uw sleutel kluis verbindingen beheren

1. Key Vault verbindingen zoeken/zoeken op naam

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Sleutel kluis zoeken":::

2. Een of meer Key Vault verbindingen verwijderen

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Sleutel kluis verwijderen":::

## <a name="manage-your-credentials"></a>Uw referenties beheren

1. Referenties zoeken/zoeken op naam.
  
2. Een bestaande referentie selecteren en updates maken.

3. Verwijder een of meer referenties.

## <a name="next-steps"></a>Volgende stappen

[Een scanregelset maken](create-a-scan-rule-set.md)
