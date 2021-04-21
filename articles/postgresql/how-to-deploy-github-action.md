---
title: 'Quickstart: Verbinding maken met Azure PostgreSQL met GitHub Actions'
description: Azure PostgreSQL gebruiken vanuit een GitHub Actions-werkstroom
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.author: sumuth
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 7fc59c0d9036a2e83c742f51fc17750d40e057fe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791423"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-postgresql"></a>Quickstart: GitHub Actions gebruiken om verbinding te maken met Azure PostgreSQL

<Token>**VAN TOEPASSING OP:** :::image type="icon" source="./media/applies-to/yes.png" border="false":::Azure Database for PostgreSQL - Enkele server :::image type="icon" source="./media/applies-to/yes.png" border="false":::Azure Database for PostgreSQL - Flexibele server</Token>

Aan de slag met [GitHub Actions](https://docs.github.com/en/actions) met behulp van een werkstroom voor het implementeren van database-updates voor [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/).

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig:
- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-opslagplaats met voorbeeldgegevens (`data.sql`). Als u geen GitHub-account hebt, [kunt u zich gratis registreren](https://github.com/join).
- Een Azure Database for PostgreSQL-server.
    - [Quickstart: Een Azure Database for PostgreSQL-server maken in Azure Portal](quickstart-create-server-database-portal.md)

## <a name="workflow-file-overview"></a>Overzicht van werkstroom bestand

Een GitHub Actions-werkstroom wordt gedefinieerd door een YAML-bestand (.yml) in het pad `/.github/workflows/` in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom.

Het bestand heeft twee secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. Definieer een service-principal. <br /> 2. Maak een GitHub-opslagplaats. |
|**Implementeren** | 1. Implementeer de database. |

## <a name="generate-deployment-credentials"></a>Genereer implementatiereferenties

U kunt een [service-principal](../active-directory/develop/app-objects-and-service-principals.md) maken met de opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac&preserve-view=true) in de [Azure CLI](/cli/azure/). Voer deze opdracht uit met [Azure Cloud Shell](https://shell.azure.com/) in de Azure Portal of door de knop **Uitproberen** te selecteren.

Vervang de tijdelijke aanduidingen `server-name` door de naam van uw PostgreSQL-server die wordt gehost in Azure. Vervang `subscription-id` en `resource-group` door de abonnements-id en de resourcegroep die zijn verbonden met de PostgreSQL-server.

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

De uitvoer is een JSON-object met de roltoewijzingsreferenties die toegang bieden tot uw database, vergelijkbaar met hieronder. Kopieer de uitvoer van dit JSON-object voor later.

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

## <a name="copy-the-postgresql-connection-string"></a>PostgreSQL-verbindingsreeks kopiëren

Ga in de Azure-portal naar de Azure Database for PostgreSQL-server en open **Instellingen** > **Verbindingsreeksen**. Kopieer de verbindingsreeks voor **ADO.NET**. Vervang de plaatsaanduidingswaarden door `your_database` en `your_password`. De verbindingsreeks ziet er ongeveer als volgt uit.

> [!IMPORTANT]
> - Gebruik voor Enkele server: ```user=adminusername@servername```. Let op: ```@servername``` is vereist.
> - Gebruik voor Flexibele server: ```user= adminusername``` zonder ```@servername```.

```output
psql host={servername.postgres.database.azure.com} port=5432 dbname={your_database} user={adminusername} password={your_database_password} sslmode=require
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

1. Plak de verbindingsreekswaarde in het waardeveld van het geheim. Geef het geheim de naam `AZURE_POSTGRESQL_CONNECTION_STRING`.


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

1. Wijzig de naam van uw werkstroom `PostgreSQL for GitHub Actions` en voeg de acties voor uitchecken en aanmelden toe. Met deze acties wordt uw sitecode uitgecheckt en geverifieerd met Azure met behulp van het `AZURE_CREDENTIALS` GitHub-geheim dat u eerder hebt gemaakt.

    ```yaml
    name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

2. Gebruik de actie Azure PostgreSQL implementeren om verbinding te maken met het PostgreSQL-exemplaar. Vervang `POSTGRESQL_SERVER_NAME` door de naam van uw server. U moet een PostgreSQL-gegevensbestand met de naam `data.sql` hebben op het niveau van de hoofdmap van uw opslagplaats.

    ```yaml
     - uses: azure/postgresql@v1
       with:
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        server-name: POSTGRESQL_SERVER_NAME
        sql-file: './data.sql'
    ```

3. Voltooi uw werkstroom door een actie toe te voegen aan de afmelding van Azure. Dit is de voltooide werkstroom. Het bestand wordt weergegeven in de map `.github/workflows` van uw opslagplaats.

    ```yaml
   name: PostgreSQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/postgresql@v1
      with:
        server-name: POSTGRESQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_POSTGRESQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Beoordeel uw implementatie

1. Ga naar **Acties** voor uw GitHub-opslagplaats.

1. Open het eerste resultaat om gedetailleerde logboeken van de uitvoering van de werkstroom weer te geven.

    :::image type="content" source="media/how-to-deploy-github-action/gitbub-action-postgres-success.png" alt-text="Logboek van uitgevoerde GitHub-acties":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer de Azure PostgreSQL-database en -opslagplaats niet meer nodig zijn, moet u de resources opschonen die u hebt geïmplementeerd, door de resourcegroep en de GitHub-opslagplaats te verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure en GitHub-integratie](/azure/developer/github/)
<br/>
> [!div class="nextstepaction"]
> [Meer informatie over verbinding maken met de server](how-to-connect-query-guide.md)
