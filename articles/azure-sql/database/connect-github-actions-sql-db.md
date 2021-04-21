---
title: 'Quickstart: Verbinding maken met Azure SQL Database met GitHub Actions'
description: Azure SQL gebruiken vanuit een GitHub Actions-werkstroom
author: juliakm
services: sql-database
ms.service: sql-database
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: ceb9f0f9ef2a88532d5af16a03fcfd0282da84f8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787283"
---
# <a name="use-github-actions-to-connect-to-azure-sql-database"></a>GitHub Actions gebruiken om verbinding te maken met Azure SQL Database

Aan de slag met [GitHub Actions](https://docs.github.com/en/actions) met behulp van een werkstroom voor het implementeren van database-updates voor [Azure SQL Database](../azure-sql-iaas-vs-paas-what-is-overview.md). 

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig: 
- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-opslagplaats met een dacpac-pakket (`Database.dacpac`). Als u geen GitHub-account hebt, [kunt u zich gratis registreren](https://github.com/join).  
- Een Azure SQL Database.
    - [Quickstart: Een individuele database maken in Azure SQL Database](single-database-create-quickstart.md)
    - [Een dacpac-pakket maken op basis van uw bestaande SQL Server-database](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)

## <a name="workflow-file-overview"></a>Overzicht van werkstroom bestand

Een GitHub Actions-werkstroom wordt gedefinieerd door een YAML-bestand (.yml) in het pad `/.github/workflows/` in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom.

Het bestand heeft twee secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. Definieer een service-principal. <br /> 2. Maak een GitHub-opslagplaats. |
|**Implementeren** | 1. Implementeer de database. |

## <a name="generate-deployment-credentials"></a>Genereer implementatiereferenties

U kunt een [service-principal](../../active-directory/develop/app-objects-and-service-principals.md) maken met de opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) in de [Azure CLI](/cli/azure/). Voer deze opdracht uit met [Azure Cloud Shell](https://shell.azure.com/) in de Azure Portal of door de knop **Uitproberen** te selecteren.

Vervang de tijdelijke aanduidingen `server-name` door de naam van uw SQL-server die wordt gehost op Azure. Vervang de `subscription-id` en `resource-group` door de abonnements-ID en de resourcegroep die zijn verbonden met uw SQL-server.  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

De uitvoer is een JSON-object met de roltoewijzingsreferenties die toegang bieden tot uw database, vergelijkbaar met dit voorbeeld. Kopieer uw uitvoer van dit JSON-object voor later.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Het is altijd een goed idee om minimale toegang te verlenen. Het bereik in het vorige voorbeeld is beperkt tot de specifieke server en niet de hele resourcegroep.

## <a name="copy-the-sql-connection-string"></a>SQL-verbindingsreeks kopiëren 

Ga in het Azure Portal naar uw Azure SQL Database en open **Instellingen** > **Verbindingsreeksen**. Kopieer de verbindingsreeks voor **ADO.NET**. Vervang de plaatsaanduidingswaarden door `your_database` en `your_password`. De verbindingsreeks ziet er ongeveer uit als deze uitvoer. 

```output
    Server=tcp:my-sql-server.database.windows.net,1433;Initial Catalog={your-database};Persist Security Info=False;User ID={admin-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

U gebruikt de verbindingsreeks als een GitHub-geheim. 

## <a name="configure-the-github-secrets"></a>De GitHub-geheimen configureren

1. In [GitHub](https://github.com/), bladert u in uw opslagplaats.

1. Selecteer **Instellingen > Geheimen > Nieuwe geheimen**.

1. Plak de volledige JSON-uitvoer van de Azure CLI-opdracht in het waardeveld van het geheim. Geef het geheim de naam `AZURE_CREDENTIALS`.

    Wanneer u het werkstroombestand later configureert, gebruikt u het geheim voor de invoer `creds` van de Azure-aanmeldingsactie. Bijvoorbeeld:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Selecteer **Nieuwe geheim** opnieuw. 

1. Plak de verbindingsreekswaarde in het waardeveld van het geheim. Geef het geheim de naam `AZURE_SQL_CONNECTION_STRING`.


## <a name="add-your-workflow"></a>Voeg uw werkstroom toe

1. Ga naar **Acties** voor uw GitHub-opslagplaats. 

2. Selecteer **Uw werkstroom zelf instellen**. 

2. Verwijder alles na de sectie `on:` van uw werkstroombestand. Uw resterende werkstroom kan er bijvoorbeeld als volgt uitzien. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Wijzig de naam van uw werkstroom `SQL for GitHub Actions` en voeg de acties voor uitchecken en aanmelden toe. Met deze acties wordt uw sitecode uitgecheckt en geverifieerd met Azure met behulp van het `AZURE_CREDENTIALS` GitHub-geheim dat u eerder hebt gemaakt. 

    ```yaml
    name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Gebruik de Azure SQL-implementatie-actie om verbinding te maken met uw SQL-exemplaar. Vervang `SQL_SERVER_NAME` door de naam van uw server. U moet een dacpac-pakket (`Database.dacpac`) hebben op het niveau van de hoofdmap van uw opslagplaats. 

    ```yaml
    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'
    ``` 

1. Voltooi uw werkstroom door een actie toe te voegen aan de afmelding van Azure. Dit is de voltooide werkstroom. Het bestand wordt weergegeven in de map `.github/workflows` van uw opslagplaats.

    ```yaml
   name: SQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/sql-action@v1
      with:
        server-name: SQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_SQL_CONNECTION_STRING }}
        sql-file: './Database.dacpac'

        # Azure logout 
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Beoordeel uw implementatie

1. Ga naar **Acties** voor uw GitHub-opslagplaats. 

1. Open het eerste resultaat om gedetailleerde logboeken van de uitvoering van de werkstroom weer te geven. 
 
   :::image type="content" source="media/quickstart-sql-github-actions/github-actions-run-sql.png" alt-text="Logboek van uitgevoerde GitHub-acties":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer uw Azure SQL-database en -opslagplaats niet meer nodig zijn, moet u de resources opschonen die u hebt geïmplementeerd door de resourcegroep en de GitHub-opslagplaats te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure en GitHub-integratie](/azure/developer/github/)