---
title: Cross-Origin Resource Sharing (CORS) in Azure Cosmos DB
description: In dit artikel wordt beschreven hoe u een CORS (cross-Origin Resource Sharing) configureert in Azure Cosmos DB met behulp van Azure Portal-en Azure Resource Manager-sjablonen.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: eba49ff45ba9ab1f5cfaa1d75973d656ac32ca6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339891"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Configureren van cross-Origin-resource delen (CORS)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Cross-Origin Resource Sharing (CORS) is een HTTP-functie waarmee een webtoepassing die wordt uitgevoerd onder het ene domein toegang kan krijgen tot bronnen in een ander domein. Webbrowsers implementeren een beveiligings beperking die bekend staat als hetzelfde-Origin-beleid dat voor komt dat een webpagina Api's in een ander domein aanroept. CORS biedt echter een veilige manier om het domein van de oorsprong te laten aanroepen van Api's in een ander domein. De core-API (SQL) in Azure Cosmos DB ondersteunt nu het gebruik van CORS (cross-Origin Resource Sharing) door de header ' allowedOrigins ' te gebruiken. Nadat u de CORS-ondersteuning voor uw Azure Cosmos-account hebt ingeschakeld, worden alleen geverifieerde aanvragen geëvalueerd om te bepalen of ze zijn toegestaan volgens de regels die u hebt opgegeven.

U kunt de instelling voor het delen van cross-Origin-resources (CORS) configureren vanuit het Azure Portal of van een Azure Resource Manager sjabloon. Voor Cosmos-accounts die gebruikmaken van de core-API (SQL), ondersteunt Azure Cosmos DB een Java script-bibliotheek die in zowel Node.js als op browser gebaseerde omgevingen werkt. Deze bibliotheek kan nu profiteren van CORS-ondersteuning wanneer u de gateway modus gebruikt. Er is geen configuratie aan de client zijde nodig om deze functie te gebruiken. Met CORS-ondersteuning kunnen resources van een browser rechtstreeks toegang krijgen tot Azure Cosmos DB via de [Java script-bibliotheek](https://www.npmjs.com/package/@azure/cosmos) of rechtstreeks vanuit de [rest API](/rest/api/cosmos-db/) voor eenvoudige bewerkingen.

> [!NOTE]
> CORS-ondersteuning is alleen van toepassing op en wordt ondersteund voor de Azure Cosmos DB core-API (SQL). Het is niet van toepassing op de Azure Cosmos DB-Api's voor Cassandra, Gremlin of MongoDB, aangezien deze protocollen geen gebruik maken van HTTP voor client-server communicatie.

## <a name="enable-cors-support-from-azure-portal"></a>CORS-ondersteuning van Azure Portal inschakelen

Gebruik de volgende stappen om cross-Origin resource sharing in te scha kelen met behulp van Azure Portal:

1. Navigeer naar uw Azure Cosmos DB-account. Open de Blade **CORS** .

2. Geef een door komma's gescheiden lijst van oorsprongen die cross-Origin-aanroepen naar uw Azure Cosmos DB-account kunnen maken. Bijvoorbeeld,, `https://www.mydomain.com` `https://mydomain.com` , `https://api.mydomain.com` . U kunt ook een Joker teken gebruiken \* om alle oorsprong toe te staan en **verzenden** te selecteren. 

   > [!NOTE]
   > Op dit moment kunt u geen joker tekens gebruiken als onderdeel van de domein naam. Een voor beeld van een `https://*.mydomain.net` indeling wordt nog niet ondersteund. 

   :::image type="content" source="./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png" alt-text="Cross-Origin resource delen inschakelen met behulp van Azure Portal":::

## <a name="enable-cors-support-from-resource-manager-template"></a>CORS-ondersteuning inschakelen voor de Resource Manager-sjabloon

Als u CORS wilt inschakelen met behulp van een resource manager-sjabloon, voegt u de sectie CORS met de eigenschap allowedOrigins toe aan een bestaande sjabloon. De volgende JSON is een voor beeld van een sjabloon waarmee een nieuw Azure Cosmos-account wordt gemaakt waarvoor CORS is ingeschakeld.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "cors": [
      {
        "allowedOrigins": "*"
      }
    ]
  }
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>De Azure Cosmos DB java script-bibliotheek gebruiken vanuit een browser

Momenteel heeft de Azure Cosmos DB java script-bibliotheek alleen de CommonJS-versie van de bibliotheek geleverd met het pakket. Als u deze bibliotheek vanuit de browser wilt gebruiken, moet u een hulp programma zoals Rollup of webpack gebruiken om een met browsers compatibele bibliotheek te maken. Voor bepaalde Node.js bibliotheken moeten browser modellen worden gesimuleerd. Hier volgt een voor beeld van een webpack-configuratie bestand met de benodigde model instellingen.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Hier volgt een [code voorbeeld](https://github.com/christopheranderson/cosmos-browser-sample) dat gebruikmaakt van type script en webpack met de Azure Cosmos DB java script SDK-bibliotheek om een TODO-app te bouwen die realtime-updates verzendt wanneer er nieuwe items worden gemaakt.
Als best practice gebruikt u de primaire sleutel niet om te communiceren met Azure Cosmos DB vanuit de browser. Gebruik in plaats daarvan bron tokens om te communiceren. Zie [toegang tot Azure Cosmos DB](secure-access-to-data.md#resource-tokens) -artikel beveiligen voor meer informatie over bron tokens.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over andere manieren om uw Azure Cosmos-account te beveiligen:

* [Een firewall configureren voor Azure Cosmos DB](how-to-configure-firewall.md) -artikel.

* [Virtueel netwerk en op subnet gebaseerde toegang voor uw Azure Cosmos DB-account configureren](how-to-configure-vnet-service-endpoint.md)
