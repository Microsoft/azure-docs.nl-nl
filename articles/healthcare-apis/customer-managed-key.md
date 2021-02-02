---
title: Door de klant beheerde sleutels configureren voor Azure API for FHIR
description: De functie voor het meenemen van uw eigen sleutel wordt ondersteund in Azure API for FHIR via Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: ae78aa80594e46b02d77adcafed961e801780d4f
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430256"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Door de klant beheerde sleutels op rest configureren

Wanneer u een nieuwe Azure API for FHIR-account maakt, worden uw gegevens standaard versleuteld met door Microsoft beheerde sleutels. U kunt nu een tweede laag versleuteling voor de gegevens toevoegen met behulp van uw eigen sleutel die u zelf kiest en beheert.

In azure wordt dit doorgaans gerealiseerd met behulp van een versleutelings sleutel in de Azure Key Vault van de klant. Azure SQL, Azure Storage en Cosmos DB zijn enkele voorbeelden die deze mogelijkheid bieden. Azure API for FHIR maakt gebruik van deze ondersteuning van Cosmos DB. Wanneer u een account maakt, hebt u de optie om een Azure Key Vault sleutel-URI op te geven. Deze sleutel wordt door gegeven aan Cosmos DB wanneer het DB-account is ingericht. Wanneer er een FHIR-aanvraag wordt gedaan, haalt Cosmos DB uw sleutel op en gebruikt deze om de gegevens te versleutelen/ontsleutelen. Om aan de slag te gaan, kunt u de volgende koppelingen raadplegen:

- [De Azure Cosmos DB-resourceprovider registreren voor uw Azure-abonnement](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Uw Azure Key Vault-exemplaar configureren](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Een toegangs beleid toevoegen aan uw Azure Key Vault-exemplaar](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Een sleutel in Azure Key Vault genereren](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="specify-the-azure-key-vault-key"></a>De Azure Key Vault sleutel opgeven

Wanneer u uw Azure-API voor FHIR-account maakt op Azure Portal, ziet u de configuratie optie gegevens versleuteling onder de data base-instellingen op het tabblad aanvullende instellingen. Standaard wordt de optie voor de door de service beheerde sleutel gekozen. 

U kunt uw sleutel kiezen uit de volgende:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Sleutelkiezer":::

U kunt ook uw Azure Key Vault sleutel opgeven door de optie door de klant beheerde sleutel te selecteren. U kunt hier de sleutel-URI invoeren:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Azure API for FHIR maken":::

Voor bestaande FHIR-accounts kunt u de sleutelversleutelingskeuze (door service of door de klant beheerde sleutel) bekijken in de blade 'Data base', zoals hieronder wordt weergegeven. De configuratieoptie kan niet worden gewijzigd wanneer deze eenmaal is gekozen. U kunt uw sleutel echter wel wijzigen en bijwerken.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Database":::

Daarnaast kunt u een nieuwe versie van de opgegeven sleutel maken, waarna uw gegevens worden versleuteld met de nieuwe versie zonder onderbreking van de service. U kunt ook toegang tot de sleutel verwijderen om de toegang tot de gegevens te verwijderen. Als de sleutel is uitgeschakeld, resulteert de query in een fout. Als de sleutel opnieuw wordt ingeschakeld, worden query's opnieuw uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u door de klant beheerde sleutels op rest kunt configureren. Daarna kunt u de sectie met veelgestelde vragen over Azure Cosmos DB bekijken: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: CMK instellen](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#frequently-asked-questions)
