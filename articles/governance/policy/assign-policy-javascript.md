---
title: 'Quickstart: Nieuwe beleidstoewijzing met JavaScript'
description: In deze quickstart gebruikt u JavaScript om een Azure Policy-toewijzing te maken om niet-compatibele resources te identificeren.
ms.date: 09/24/2020
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 7548053e4bd5be214bf7de3eef3dc4c6c95442d4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91347975"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-javascript"></a>Quickstart: Een beleidstoewijzing maken om niet-compatibele resources met JavaScript te identificeren

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources. In deze snelstart maakt u een beleidstoewijzing om te identificeren welke virtuele machines geen beheerde schijven gebruiken. Zodra de toewijzing is voltooid, kunt u de virtuele machines identificeren die _niet-compatibel_ zijn.

De JavaScript-bibliotheek wordt gebruikt voor het beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze handleiding wordt uitgelegd hoe u de JavaScript-bibliotheek gebruikt om een beleidstoewijzing te maken.

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

- **Node.js**: [Node.js](https://nodejs.org/)-versie 12 of hoger is vereist.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-libraries"></a>De Policy-bibliotheken toevoegen

Om JavaScript te laten werken met Azure Policy, moeten de bibliotheken worden toegevoegd. Deze bibliotheken werken overal waar JavaScript kan worden gebruikt, inclusief met [bash in Windows 10](/windows/wsl/install-win10).

1. Stel een nieuw Node.js-project in door de volgende opdracht uit te voeren.

   ```bash
   npm init -y
   ```

1. Voeg een verwijzing toe naar de yargs-bibliotheek.

   ```bash
   npm install yargs
   ```

1. Voeg een verwijzing toe naar de Azure Policy-bibliotheken.

   ```bash
   # arm-policy is for working with Azure Policy objects such as definitions and assignments
   npm install @azure/arm-policy

   # arm-policyinsights is for working with Azure Policy compliance data such as events and states
   npm install @azure/arm-policyinsights
   ```

1. Voeg een verwijzing toe naar de Azure-verificatiebibliotheek.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Controleer in _package.json_ of `@azure/arm-policy` versie **3.1.0** of hoger is, en of `@azure/arm-policyinsights` versie **3.2.0** of hoger is, en of `@azure/ms-rest-nodeauth` versie **3.0.5** of hoger is.

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

In deze quickstart maakt u een beleidstoewijzing en wijst u de definitie **Controleer virtuele machines die niet gebruikmaken van beheerde schijven** (`06a78e20-9358-41c9-923c-fb736d382a4d`) toe. Deze beleidsdefinitie identificeert resources die niet voldoen aan de voorwaarden die zijn vastgelegd in de beleidsdefinitie.

1. Maak een nieuw bestand met de naam _policyAssignment.js_ en voer de volgende code in.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyObjects = require("@azure/arm-policy");
   
   if (argv.subID && argv.name && argv.displayName && argv.policyDefID && argv.scope && argv.description) {
   
       const createAssignment = async () => {
           const credentials = await authenticator.interactiveLogin();
           const client = new policyObjects.PolicyClient(credentials, argv.subID);
           const assignments = new policyObjects.PolicyAssignments(client);
   
           const result = await assignments.create(
               argv.scope,
               argv.name,
               {
                   displayName: argv.displayName,
                   policyDefinitionId: argv.policyDefID,
                   description: argv.description
               }
           );
           console.log(result);
       };
   
       createAssignment();
   }
   ```

1. Voer de volgende opdracht in de terminal in:

   ```bash
   node policyAssignment.js `
      --subID "{subscriptionId}" `
      --name "audit-vm-manageddisks" `
      --displayName "Audit VMs without managed disks Assignment" `
      --policyDefID "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      --description "Shows all virtual machines not using managed disks" `
      --scope "{scope}"
   ```

In de voorgaande opdrachten wordt de volgende informatie gebruikt:

- **subID**: de abonnements-id voor verificatiecontext. Vervang `{subscriptionId}` door uw abonnement.
- **name**: de unieke naam voor het object voor beleidstoewijzing. In het bovenstaande voorbeeld wordt _audit-vm-manageddisks_ gebruikt.
- **displayName**: de weergavenaam voor de beleidstoewijzing. In dit geval gebruikt u de toewijzing _Virtuele machines zonder beheerde schijven controleren_.
- **policyDefID**: het pad van de beleidsdefinitie, op basis waarvan u de toewijzing maakt. In dit geval is het de id van de beleidsdefinitie _Virtuele machines zonder beheerde schijven controleren_.
- **description**: een grondigere uitleg van wat het beleid doet of waarom het aan dit bereik is toegewezen.
- **scope**: een bereik bepaalt voor welke resources of groep resources de beleidstoewijzing wordt afgedwongen. Het kan variëren van een beheergroep tot een afzonderlijke resource. Zorg dat u `{scope}` vervangt door een van de volgende patronen:
  - Beheergroep: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Abonnement: `/subscriptions/{subscriptionId}`
  - Resourcegroep: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Resource: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

U kunt nu niet-compatibele resources identificeren om inzicht te krijgen in de nalevingsstatus van uw omgeving.

## <a name="identify-non-compliant-resources"></a>Niet-compatibele resources identificeren

Nu uw beleidstoewijzing is gemaakt, kunt u resources identificeren die niet compatibel zijn.

1. Maak een nieuw bestand met de naam _policyState.js_ en voer de volgende code in.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyInsights = require("@azure/arm-policyinsights");
   
   if (argv.subID && argv.name) {
   
       const getStates = async () => {
   
           const credentials = await authenticator.interactiveLogin();
           const client = new policyInsights.PolicyInsightsClient(credentials);
           const policyStates = new policyInsights.PolicyStates(client);
           const result = await policyStates.listQueryResultsForSubscription(
               "latest",
               argv.subID,
               {
                   queryOptions: {
                       filter: "IsCompliant eq false and PolicyAssignmentId eq '" + argv.name + "'",
                       apply: "groupby((ResourceId))"
                   }
               }
           );
           console.log(result);
       };
   
       getStates();
   }
   ```

1. Voer de volgende opdracht in de terminal in:

   ```bash
   node policyState.js --subID "{subscriptionId}" --name "audit-vm-manageddisks"
   ```

Vervang `{subscriptionId}` door het abonnement dat u wilt zien in de nalevingsresultaten voor de beleidstoewijzing met de naam audit-vm-manageddisks, die u in de vorige stap hebt gemaakt. Zie [PolicyStates*](/javascript/api/@azure/arm-policyinsights/) voor een lijst met andere bereiken en manieren om de gegevens samen te vatten.

De resultaten zien er ongeveer als volgt uit:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

De resultaten komen overeen met wat u ziet op het tabblad **Resourcenaleving** van een beleidstoewijzing in de Azure Portal-weergave.

## <a name="clean-up-resources"></a>Resources opschonen

- Verwijder de beleidstoewijzing _VM's zonder beheerde schijven controleren_ via de portal. De beleidsdefinitie is ingebouwd, dus is er geen definitie om te verwijderen.

- Als u de geïnstalleerde bibliotheken uit uw toepassing wilt verwijderen, kunt u dit doen met de volgende opdracht.

  ```bash
  npm uninstall @azure/arm-policy @azure/arm-policyinsights @azure/ms-rest-nodeauth yargs
  ```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een beleidsdefinitie toegewezen om niet-compatibele resources in uw Azure-omgeving te identificeren.

Voor meer informatie over het toewijzen van beleidsdefinities om te controleren of nieuwe resources compatibel zijn gaat u verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Beleid maken en beheren](./tutorials/create-and-manage.md)