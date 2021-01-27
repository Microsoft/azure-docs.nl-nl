---
title: 'Quickstart: uw eerste Ruby-query'
description: In deze quickstart voert u de stappen uit om de Resource Graph-gem voor Ruby in te schakelen en uw eerste query uit te voeren.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 41769359eaa1f930d34b0a182f35da38a6f5109d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920034"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-ruby"></a>Quickstart: uw eerste Resource Graph-query uitvoeren met Ruby

De eerste stap voor het gebruik van Azure Resource Graph bestaat uit het controleren of de vereiste gems voor Ruby zijn geïnstalleerd. In deze quickstart doorloopt u het proces voor het toevoegen van de gems aan uw Ruby-installatie.

Aan het einde van dit proces hebt u de gems toegevoegd aan uw Ruby-installatie en hebt u uw eerste Resource Graph-query uitgevoerd.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
- Een Azure-service-principal, met inbegrip van de _clientId_ en _clientSecret_.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-graph-project"></a>Het Resource Graph-project maken

De gem moet worden toegevoegd aan het `Gemfile` om ervoor te zorgen dat Ruby query's kan uitvoeren op Azure Resource Graph. Deze gem werkt overal waar Ruby kan worden gebruikt, met inbegrip van [Azure Cloud Shell](https://shell.azure.com), [bash in Windows 10](/windows/wsl/install-win10) of lokaal geïnstalleerd.

1. Controleer of de meest recente versie van Ruby is geïnstalleerd (minimaal **2.7.1**). Als dit niet zo is, downloadt u deze versie op [Ruby-Lang.org](https://www.ruby-lang.org/en/downloads/).

1. Initialiseer in de Ruby-omgeving van uw keuze een bundel in een nieuwe projectmap:

   ```bash
   # Initialize a bundle to create a new Gemfile
   bundle init
   ```

1. Werk uw `Gemfile` bij met de gems die nodig zijn voor Azure Resource Graph. Het bijgewerkte bestand moet er ongeveer uitzien zoals in dit voorbeeld:

   ```file
   # frozen_string_literal: true

   source "https://rubygems.org"

   git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

   # gem "rails"
   gem 'azure_mgmt_resourcegraph', '~> 0.17.2'
   ```

1. Voer `bundle install` uit vanuit de projectmap. Controleer of de gems zijn geïnstalleerd met `bundle list`.

1. Maak in dezelfde projectmap `argQuery.rb` met behulp van de volgende code en sla het bijgewerkte bestand op:

   ```ruby
   #!/usr/bin/env ruby

   require 'azure_mgmt_resourcegraph'
   ARG = Azure::ResourceGraph::Profiles::Latest::Mgmt

   # Get arguments and set options
   options = {
       tenant_id: ARGV[0],
       client_id: ARGV[1],
       client_secret: ARGV[2],
       subscription_id: ARGV[3]
   }

   # Create Resource Graph client from options
   argClient = ARG::Client.new(options)

   # Create Resource Graph QueryRequest for subscription with query
   query_request = ARGModels::QueryRequest.new.tap do |qr|
       qr.subscriptions = [ARGV[3]]
       qr.query = ARGV[4]
   end

   # Get the resources from Resource Graph
   response = argClient.resources(query_request)

   # Convert data to JSON and output
   puts response.data.to_json
   ```

## <a name="run-your-first-resource-graph-query"></a>Uw eerste Resource Graph-query uitvoeren

Het Ruby-script is nu opgeslagen en klaar voor gebruik. Tijd dus om een eenvoudige Resource Graph-query uit te proberen. De query retourneert de eerste vijf Azure-resources met de **naam** en het **resourcetype** van elke resource.

In elke aanroep naar `argQuery` worden verschillende variabelen gebruikt die u moet vervangen door uw eigen waarden:

- Vervang `{tenantId}` door uw tenant-id
- `{clientId}` - vervang door de client-id van uw service-principal
- `{clientSecret}` - vervang door het clientgeheim van uw service-principal
- Vervang `{subscriptionId}` door uw abonnements-ID

1. Ga naar de projectmap waarin u de bestanden `Gemfile` en `argClient.rb` hebt gemaakt.

1. Voer uw eerste Azure Resource Graph-query uit met behulp van de gems en de methode `resources`:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Omdat deze voorbeeldquery geen sorteermodificator geeft, bijvoorbeeld `order by`, zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd.

1. Wijzig de laatste parameter in `argQuery.rb` en wijzig de query om `order by` de eigenschap **Naam**:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Net als bij de eerste query zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd. De volgorde van de queryopdrachten is belangrijk. In dit voorbeeld komt `order by` na `limit`. Met deze opdracht worden de queryresultaten eerst beperkt en vervolgens gerangschikt.

1. Wijzig de laatste parameter in `argQuery.rb` en wijzig de query om eerst te `order by` op de eigenschap **Naam** en daarna de resultaten van de top vijf te `limit`:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Wanneer de laatste query meerdere keren wordt uitgevoerd, ervan uitgaande dat niets in uw omgeving verandert, zijn de geretourneerde resultaten consistent en gesorteerd op de eigenschap **Naam**, maar nog steeds beperkt tot de top vijf.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de geïnstalleerde gems uit uw Ruby-omgeving wilt verwijderen, kunt u dit doen met de volgende opdracht:

```bash
# Remove the installed gems from the Ruby environment
gem uninstall azure_mgmt_resourcegraph
```

> [!NOTE]
> De gem `azure_mgmt_resourcegraph` heeft afhankelijkheden zoals `ms_rest` en `ms_rest_azure` die mogelijk ook zijn geïnstalleerd, afhankelijk van uw omgeving. U kunt deze gems ook verwijderen als u deze niet meer nodig hebt.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de Resource Graph-gems toegevoegd aan uw Ruby-omgeving en uw eerste query uitgevoerd. Ga door naar de pagina met details van de querytaal voor meer informatie over de taal van Resource Graph.

> [!div class="nextstepaction"]
> [Meer informatie over de querytaal](./concepts/query-language.md)
