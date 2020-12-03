---
title: Referenties voor scans maken en beheren
description: In dit artikel worden de stappen beschreven voor het maken en beheren van referenties in azure controle sfeer liggen.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: c991559d550b351ce70bcc5834f96f313f856a82
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552400"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Referenties voor de verificatie van de bron in azure controle sfeer liggen

In dit artikel wordt beschreven hoe u in azure controle sfeer liggen referenties kunt maken om opgeslagen verificatie gegevens snel opnieuw te gebruiken en toe te passen op uw gegevens bron scans.

## <a name="prerequisites"></a>Vereisten

* Azure-sleutel kluis. Als u er nog geen hebt, kunt u een koppeling naar het artikel KV Creation invoegen om er een te maken.

## <a name="introduction"></a>Inleiding
Een referentie is verificatie-informatie die Azure controle sfeer liggen kan gebruiken voor verificatie bij uw geregistreerde gegevens bronnen. Een referentie object kan worden gemaakt voor diverse typen verificatie scenario's (zoals basis verificatie waarvoor gebruikers naam en wacht woord zijn vereist) en legt de specifieke informatie vast op basis van het gekozen type verificatie methode. Referenties gebruiken uw bestaande Azure-sleutel kluizen geheimen om gevoelige verificatie gegevens op te halen tijdens het proces voor het maken van de referenties.

## <a name="using-purview-managed-identity-to-set-up-scans"></a>Scans instellen met behulp van controle sfeer liggen beheerde identiteit
Als u de beheerde identiteit controle sfeer liggen gebruikt om scans in te stellen, hoeft u niet expliciet een referentie te maken en uw sleutel kluis te koppelen aan controle sfeer liggen om deze op te slaan. Raadpleeg de volgende secties voor specifieke verificatie van de gegevens bron voor gedetailleerde instructies over het toevoegen van de beheerde identiteit controle sfeer liggen om toegang te krijgen tot uw gegevens bronnen:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Beheerde instantie Azure SQL Database](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Azure Key kluizen-verbindingen maken in uw Azure controle sfeer liggen-account

Voordat u een referentie kunt maken, moet u eerst een of meer van uw bestaande Azure Key Vault-exemplaren koppelen aan uw Azure controle sfeer liggen-account.

1. Ga in het navigatie menu van Azure controle sfeer liggen naar het Management Center en navigeer vervolgens naar referenties

2. Selecteer op de opdracht balk referenties beheren Key Vault verbindingen

    :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Azure-verbindingen beheren":::

3. Selecteer + Nieuw in het deel venster Key Vault verbindingen beheren 

4. Geef de vereiste gegevens op en selecteer vervolgens maken.

5. Controleer of uw Key Vault is gekoppeld aan uw Azure controle sfeer liggen-account

    :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Azure-verbindingen weer geven":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Controle sfeer liggen beheerde identiteit toegang verlenen tot uw Azure Key Vault

Navigeer naar uw sleutel kluis-> toegangs beleid-> beleid voor toegang toevoegen. Ken Get toestemming toe in de vervolg keuzelijst geheimen machtigingen en selecteer Principal als controle sfeer liggen MSI. 

:::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Controle sfeer liggen MSI toevoegen aan Azure":::


:::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Toegangs beleid toevoegen":::


:::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Toegangs beleid opslaan":::

## <a name="create-a-new-credential"></a>Een nieuwe referentie maken

Het referentie type dat momenteel wordt ondersteund in controle sfeer liggen:
* Basis verificatie: u voegt het **wacht woord** toe als geheim in de sleutel kluis
* Service-Principal: u voegt de **sleutel** van de Service-Principal toe als geheim in de sleutel kluis 
* SQL-verificatie: u voegt het **wacht woord** toe als geheim in de sleutel kluis
* Account sleutel: u voegt de **account sleutel** toe als geheim in de sleutel kluis

Hier vindt u meer informatie over het toevoegen van geheimen aan een sleutel kluis: (sleutel kluis invoegen-artikel)

Nadat u uw geheimen in uw sleutel kluis hebt opgeslagen, maakt u een nieuwe referentie door + nieuw te selecteren in de opdracht balk voor referenties. Geef de vereiste informatie op, waaronder het selecteren van de verificatie methode en een Key Vault-exemplaar waaruit een geheim moet worden geselecteerd. Als alle gegevens zijn ingevuld, klikt u op maken.

:::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Nieuwe referentie":::

Controleer of uw nieuwe referentie wordt weer gegeven in de weer gave referentie lijst en gereed is voor gebruik

:::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Referentie weer geven":::

## <a name="manage-your-key-vault-connections"></a>Uw sleutel kluis verbindingen beheren

1. Key Vault verbindingen zoeken/zoeken op naam

    :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Sleutel kluis zoeken":::

1. Een of meer Key Vault verbindingen verwijderen
 
    :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Sleutel kluis verwijderen":::

## <a name="manage-your-credentials"></a>Uw referenties beheren

1. Referenties zoeken/zoeken op naam
  
2. Een bestaande referentie selecteren en updates maken

3. Een of meer referenties verwijderen