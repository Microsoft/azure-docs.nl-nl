---
title: Referenties opslaan in Azure Key Vault
description: Meer informatie over het opslaan van referenties voor gegevens archieven die worden gebruikt in een Azure-sleutel kluis die Azure Data Factory automatisch kan ophalen tijdens runtime.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: 00c28a9ede01a74f4ae64109d277276050047461
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653761"
---
# <a name="store-credential-in-azure-key-vault"></a>Referenties opslaan in Azure Key Vault

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

U kunt referenties voor gegevens archieven en berekeningen opslaan in een [Azure Key Vault](../key-vault/general/overview.md). Azure Data Factory haalt de referenties op bij het uitvoeren van een activiteit die gebruikmaakt van de gegevens opslag/compute.

Momenteel ondersteunen alle activiteitstypen behalve aangepaste activiteiten deze functie. Voor de configuratie van de connector raadpleegt u de sectie ' eigenschappen van gekoppelde service ' in [elk onderwerp](copy-activity-overview.md#supported-data-stores-and-formats) van de connector voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Deze functie is afhankelijk van de data factory beheerde identiteit. Meer informatie over hoe het werkt vanuit de [beheerde identiteit voor Data Factory](data-factory-service-identity.md) en om ervoor te zorgen dat uw Data Factory een koppeling hebben.

## <a name="steps"></a>Stappen

Als u wilt verwijzen naar een referentie die is opgeslagen in Azure Key Vault, moet u het volgende doen:

1. **Haal Data Factory beheerde identiteit** op door de waarde van de beheerde identiteit object-id die samen met uw fabriek is gegenereerd te kopiëren. Als u de gebruikers interface van ADF gebruikt, wordt de object-ID van de beheerde identiteit weer gegeven in het venster Azure Key Vault gekoppelde service maken. u kunt deze ook ophalen uit Azure Portal. Raadpleeg [Data Factory beheerde identiteit ophalen](data-factory-service-identity.md#retrieve-managed-identity).
2. **Verleen de beheerde identiteit toegang tot uw Azure Key Vault.** Zoek in uw sleutel kluis-> toegangs beleid-> toegangs beleid toevoegen door deze beheerde identiteit te doorzoeken om machtigingen in de vervolg keuzelijst voor geheime machtigingen **te verlenen.** Hiermee kan deze toegewezen Factory toegang krijgen tot het geheim in de sleutel kluis.
3. **Een gekoppelde service maken die verwijst naar uw Azure Key Vault.** Raadpleeg [Azure Key Vault gekoppelde service](#azure-key-vault-linked-service).
4. **Maak een gekoppelde Data Store-service, waarin wordt verwezen naar het corresponderende geheim dat is opgeslagen in de sleutel kluis.** Raadpleeg het [referentie geheim dat is opgeslagen in de sleutel kluis](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Gekoppelde Azure Key Vault-service

De volgende eigenschappen worden ondersteund voor Azure Key Vault gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **AzureKeyVault**. | Ja |
| baseUrl | Geef de Azure Key Vault URL op. | Ja |

**Gebruikers interface voor ontwerpen gebruiken:**

Selecteer **verbindingen**  ->  **gekoppelde services**  ->  **Nieuw**. In nieuwe gekoppelde service zoekt en selecteert u ' Azure Key Vault ':

![Azure Key Vault zoeken](media/store-credentials-in-key-vault/search-akv.png)

Selecteer de ingerichte Azure Key Vault waar uw referenties worden opgeslagen. U kunt de **verbinding testen** om er zeker van te zijn dat uw Azure-verbinding geldig is. 

![Azure Key Vault configureren](media/store-credentials-in-key-vault/configure-akv.png)

**JSON-voor beeld:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>Verwijzen naar het geheim dat in Azure Key Vault is opgeslagen

De volgende eigenschappen worden ondersteund wanneer u een veld configureert in een gekoppelde service die naar een sleutel kluis geheim verwijst:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van het veld moet worden ingesteld op: **AzureKeyVaultSecret**. | Ja |
| secretName | De naam van het geheim in Azure Key Vault. | Ja |
| secretVersion | De versie van het geheim in Azure Key Vault.<br/>Als u niets opgeeft, wordt altijd de meest recente versie van het geheim gebruikt.<br/>Als deze is opgegeven, wordt de opgegeven versie weer gegeven.| Nee |
| winkel | Verwijst naar een Azure Key Vault gekoppelde service die u gebruikt om de referentie op te slaan. | Ja |

**Gebruikers interface voor ontwerpen gebruiken:**

Selecteer **Azure Key Vault** voor geheime velden tijdens het maken van de verbinding met uw gegevens archief/compute. Selecteer de ingerichte Azure Key Vault gekoppelde service en geef de **geheime naam** op. U kunt eventueel ook een geheime versie opgeven. 

>[!TIP]
>Voor connectors die gebruikmaken van connection string in gekoppelde service, zoals SQL Server, Blob Storage, enzovoort, kunt u kiezen om alleen het geheime veld op te slaan, bijvoorbeeld een wacht woord in azure, of om de volledige connection string op te slaan in Azure. U kunt beide opties vinden in de gebruikers interface.

![Azure Key Vault geheim configureren](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON-voor beeld: (Zie de sectie wacht woord)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
