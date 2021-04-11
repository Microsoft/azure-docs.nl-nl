---
title: 'Snelstartgids: een beheer groep maken met Go'
description: In deze Quick Start gebruikt u go om een beheer groep te maken om uw resources in een resource hiërarchie in te delen.
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: bf2d2c556cfd6ada6d31fc6ee797888ed0899573
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091443"
---
# <a name="quickstart-create-a-management-group-with-go"></a>Snelstartgids: een beheer groep maken met Go

Managementgroepen zijn containers die u helpen bij het beheren van toegang, beleid en naleving voor meerdere abonnementen. Maak deze containers om een doeltreffende en efficiënte hiërarchie te maken die kan worden gebruikt met [Azure Policy](../policy/overview.md) en [op rollen gebaseerd toegangsbeheer van Azure](../../role-based-access-control/overview.md). Zie [Uw resources indelen met Azure-beheergroepen](overview.md) voor meer informatie over beheergroepen.

Het kan tot vijftien minuten duren voordat de eerste beheergroep die in de map is gemaakt, is voltooid. Er zijn processen die de eerste keer worden uitgevoerd om de service voor beheergroepen in Azure in te stellen voor uw map. U ontvangt een melding wanneer het proces is voltooid. Zie [Eerste configuratie van beheergroepen](./overview.md#initial-setup-of-management-groups) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

- Een Azure-service-principal, met inbegrip van de _clientId_ en _clientSecret_. Als u geen service-principal hebt voor gebruik met Azure Policy of als u een nieuwe wilt maken, raadpleegt u [Azure-beheerbibliotheken voor .NET-verificatie](/dotnet/azure/sdk/authentication#mgmt-auth).
  Sla de stap over om de .NET Core-pakketten te installeren; dit doet u in de volgende stappen.

- Elke Azure AD-gebruiker in de tenant kan een beheergroep maken zonder dat de schrijfmachtiging voor beheergroepen is toegewezen als [Hiërarchie beschermen](./how-to/protect-resource-hierarchy.md#setting---require-authorization) niet is ingeschakeld. Deze nieuwe beheergroep wordt een onderliggend element van de hoofdbeheergroep of de [standaard beheergroep](./how-to/protect-resource-hierarchy.md#setting---default-management-group) en de maker krijgt de roltoewijzing "Eigenaar". Met de beheergroep-service kan deze functie worden toegewezen, zodat roltoewijzingen niet nodig zijn op hoofdmapniveau. Gebruikers hebben geen toegang tot de hoofdbeheergroep wanneer deze wordt gemaakt. Om te voorkomen dat de drempel van het vinden van de Azure AD Global Admins om beheergroepen te kunnen gebruiken te hoog is, wordt het maken van de eerste beheergroepen op hoofdmapniveau toegestaan.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-management-group-package"></a>Het beheer groeps pakket toevoegen

Als u Ga naar beheer groepen wilt inschakelen, moet u het pakket toevoegen. Dit pakket werkt overal waar Go kan worden gebruikt, inclusief [bash in Windows 10](/windows/wsl/install-win10) of lokaal geïnstalleerd.

1. Controleer of de meest recente Go is geïnstalleerd (ten minste **1,15**). Als deze nog niet is geïnstalleerd, downloadt u deze op [Golang.org](https://golang.org/dl/).

1. Controleer of de meest recente Azure CLI is geïnstalleerd (minimaal **2.5.1**). Als deze nog niet is geïnstalleerd, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLI is vereist om Go in te schakelen voor het gebruik van de `auth.NewAuthorizerFromCLI()`-methode in het volgende voorbeeld. Zie [Azure SDK voor Go - meer verificatiedetails](https://github.com/Azure/azure-sdk-for-go#more-authentication-details) voor informatie over andere opties.

1. Verifieer via Azure CLI.

   ```azurecli
   az login
   ```

1. Installeer de vereiste pakketten voor beheer groepen in uw Go-omgeving:

   ```bash
   # Add the management group package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="application-setup"></a>Installatie van toepassing

Met de go-pakketten die zijn toegevoegd aan uw omgeving, is het tijd om de Go-toepassing in te stellen die een beheer groep kan maken.

1. Maak de Go-toepassing en sla de volgende bron op als `mgCreate.go`:

   ```Go
   package main
   
   import (
    "context"
    "fmt"
    "os"
   
    mg "github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups"
    "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
    // Get variables from command line arguments
    var mgName = os.Args[1]
   
    // Create and authorize a client
    mgClient := mg.NewClient()
    authorizer, err := auth.NewAuthorizerFromCLI()
    if err == nil {
        mgClient.Authorizer = authorizer
    } else {
        fmt.Printf(err.Error())
    }
   
    // Create the request
    Request := mg.CreateManagementGroupRequest{
        Name: &mgName,
    }
   
    // Run the query and get the results
    var results, queryErr = mgClient.CreateOrUpdate(context.Background(), mgName, Request, "no-cache")
    if queryErr == nil {
        fmt.Printf("Results: " + fmt.Sprint(results) + "\n")
    } else {
        fmt.Printf(queryErr.Error())
    }
   }
   ```

1. De Go-toepassing maken:

   ```bash
   go build mgCreate.go
   ```

1. Maak een beheer groep met behulp van de gecompileerde Go-toepassing. Vervang door `<Name>` de naam van de nieuwe beheer groep:

   ```bash
   mgCreate "<Name>"
   ```

Het resultaat is een nieuwe beheergroep in de hoofdbeheergroep.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de geïnstalleerde pakketten uit uw Go-omgeving wilt verwijderen, kunt u dit doen met de volgende opdracht:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een beheergroep gemaakt om uw resource-hiërarchie te organiseren. De beheergroep kan abonnementen of andere beheergroepen bevatten.

Ga verder met het volgende voor meer informatie over beheergroepen en het beheren van uw resource-hiërarchie:

> [!div class="nextstepaction"]
> [Uw resources beheren met beheergroepen](./manage.md)