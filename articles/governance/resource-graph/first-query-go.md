---
title: 'Quickstart: Uw eerste Go-query'
description: In deze quickstart voert u de stappen uit om het Resource Graph-pakket voor Go in te schakelen en uw eerste query uit te voeren.
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: e09bbb63b56fd30eb3aa9e7e527acdd8691d3ee6
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655795"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-go"></a>Quickstart: uw eerste Resource Graph-query uitvoeren met Go

De eerste stap voor het gebruik van Azure Resource Graph bestaat uit het controleren of de vereiste pakketten voor Go zijn geïnstalleerd. In deze quickstart doorloopt u het proces voor het toevoegen van de pakketten aan uw Go-installatie.

Aan het einde van dit proces hebt u de pakketten toegevoegd aan uw Go-installatie en hebt u uw eerste Resource Graph-query uitgevoerd.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="add-the-resource-graph-package"></a>Het Resource Graph-pakket toevoegen

Het pakket moet worden toegevoegd om ervoor te zorgen dat Go query's kan uitvoeren voor Azure Resource Graph. Dit pakket werkt overal waar Go kan worden gebruikt, inclusief [bash in Windows 10](/windows/wsl/install-win10) of lokaal geïnstalleerd.

1. Controleer of de meest recente versie van Go is geïnstalleerd (minimaal **1.14**). Als deze nog niet is geïnstalleerd, downloadt u deze op [Golang.org](https://golang.org/dl/).

1. Controleer of de meest recente Azure CLI is geïnstalleerd (minimaal **2.5.1**). Als deze nog niet is geïnstalleerd, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLI is vereist om Go in te schakelen voor het gebruik van de `auth.NewAuthorizerFromCLI()`-methode in het volgende voorbeeld. Zie [Azure SDK voor Go - meer verificatiedetails](https://github.com/Azure/azure-sdk-for-go#more-authentication-details) voor informatie over andere opties.

1. Verifieer via Azure CLI.

   ```azurecli
   az login
   ```

1. Installeer de vereiste pakketten voor Azure Resource Graph in de gewenste Go-omgeving:

   ```bash
   # Add the Resource Graph package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="run-your-first-resource-graph-query"></a>Uw eerste Resource Graph-query uitvoeren

Nu de Go-paketten zijn toegevoegd aan uw gewenste omgeving, kunt u een eenvoudige Resource Graph-query uitvoeren. De query retourneert de eerste vijf Azure-resources met de **naam** en het **resourcetype** van elke resource.

1. Maak de Go-toepassing en sla de volgende bron op als `argQuery.go`:

   ```Go
   package main
   
   import (
      "fmt"
      "os"
      "context"
      "strconv"
      arg "github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph"
      "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
       // Get variables from command line arguments
       var query = os.Args[1]
       var subList = os.Args[2:]
   
       // Create and authorize a ResourceGraph client
       argClient := arg.New()
       authorizer, err := auth.NewAuthorizerFromCLI()
       if err == nil {
           argClient.Authorizer = authorizer
       } else {
           fmt.Printf(err.Error())
       }
     
       // Set options
       RequestOptions := arg.QueryRequestOptions {
           ResultFormat: "objectArray",
       }
     
       // Create the query request
       Request := arg.QueryRequest {
           Subscriptions: &subList,
           Query: &query,
           Options: &RequestOptions,
       }
     
       // Run the query and get the results
       var results, queryErr = argClient.Resources(context.Background(), Request)
       if queryErr == nil {
           fmt.Printf("Resources found: " + strconv.FormatInt(*results.TotalRecords, 10) + "\n")
           fmt.Printf("Results: " + fmt.Sprint(results.Data) + "\n")
       } else {
           fmt.Printf(queryErr.Error())
       }
   }
   ```

1. De Go-toepassing maken:

   ```bash
   go build argQuery.go
   ```

1. Voer uw eerste Azure Resource Graph-query uit met de gecompileerde Go-toepassing. Vervang `<SubID>` door uw abonnements-id:

   ```bash
   argQuery "Resources | project name, type | limit 5" "<SubID>"
   ```

   > [!NOTE]
   > Omdat deze voorbeeldquery geen sorteermodificator geeft, bijvoorbeeld `order by`, zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd.

1. Wijzig de eerste parameter in `argQuery` en wijzig de query om te `order by` op de eigenschap **Naam**. Vervang `<SubID>` door uw abonnements-id:

   ```bash
   argQuery "Resources | project name, type | limit 5 | order by name asc" "<SubID>"
   ```

   > [!NOTE]
   > Net als bij de eerste query zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd. De volgorde van de queryopdrachten is belangrijk. In dit voorbeeld komt `order by` na `limit`. Met deze opdracht worden de queryresultaten eerst beperkt en vervolgens gerangschikt.

1. Wijzig de eerste parameter in `argQuery` en wijzig de query om eerst te `order by` op de eigenschap **Naam** en daarna de resultaten van de top vijf te `limit`. Vervang `<SubID>` door uw abonnements-id:

   ```bash
   argQuery "Resources | project name, type | order by name asc | limit 5" "<SubID>"
   ```

Wanneer de laatste query meerdere keren wordt uitgevoerd, ervan uitgaande dat niets in uw omgeving verandert, zijn de geretourneerde resultaten consistent en gesorteerd op de eigenschap **Naam**, maar nog steeds beperkt tot de top vijf.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de geïnstalleerde pakketten uit uw Go-omgeving wilt verwijderen, kunt u dit doen met de volgende opdracht:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de Resource Graph-pakketten toegevoegd aan uw Go-omgeving en uw eerste query uitgevoerd. Ga door naar de pagina met details van de querytaal voor meer informatie over de taal van Resource Graph.

> [!div class="nextstepaction"]
> [Meer informatie over de querytaal](./concepts/query-language.md)