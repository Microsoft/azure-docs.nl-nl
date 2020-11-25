---
title: 'Quickstart: Een beheergroep maken met JavaScript'
description: In deze quickstart gebruikt u JavaScript om een beheergroep te maken om uw resources in een resourcehiërarchie in te delen.
ms.date: 11/18/2020
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: c1b800f150cebacf8d17785feb1bded5189f1574
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886597"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>Quickstart: Een beheergroep maken met JavaScript

Managementgroepen zijn containers die u helpen bij het beheren van toegang, beleid en naleving voor meerdere abonnementen. Maak deze containers om een doeltreffende en efficiënte hiërarchie te maken die kan worden gebruikt met [Azure Policy](../policy/overview.md) en [op rollen gebaseerd toegangsbeheer van Azure](../../role-based-access-control/overview.md). Zie [Uw resources indelen met Azure-beheergroepen](overview.md) voor meer informatie over beheergroepen.

Het kan tot vijftien minuten duren voordat de eerste beheergroep die in de map is gemaakt, is voltooid. Er zijn processen die de eerste keer worden uitgevoerd om de service voor beheergroepen in Azure in te stellen voor uw map. U ontvangt een melding wanneer het proces is voltooid. Zie [Eerste configuratie van beheergroepen](./overview.md#initial-setup-of-management-groups) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

- Voor u begint, moet u ervoor zorgen dat minimaal versie 12 van [Node.js](https://nodejs.org/) is geïnstalleerd.

- Elke Azure AD-gebruiker in de tenant kan een beheergroep maken zonder dat de schrijfmachtiging voor beheergroepen is toegewezen als [Hiërarchie beschermen](./how-to/protect-resource-hierarchy.md#setting---require-authorization) niet is ingeschakeld. Deze nieuwe beheergroep wordt een onderliggend element van de hoofdbeheergroep of de [standaard beheergroep](./how-to/protect-resource-hierarchy.md#setting---default-management-group) en de maker krijgt de roltoewijzing "Eigenaar". Met de beheergroep-service kan deze functie worden toegewezen, zodat roltoewijzingen niet nodig zijn op hoofdmapniveau. Gebruikers hebben geen toegang tot de hoofdbeheergroep wanneer deze wordt gemaakt. Om te voorkomen dat de drempel van het vinden van de Azure AD Global Admins om beheergroepen te kunnen gebruiken te hoog is, wordt het maken van de eerste beheergroepen op hoofdmapniveau toegestaan.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Installatie van toepassing

Als u JavaScript wilt inschakelen om een query uit te voeren in Azure Resource Graph, moet de omgeving worden ingesteld. Deze instelling werkt overal waar JavaScript kan worden gebruikt, met inbegrip van [bash in Windows 10](/windows/wsl/install-win10).

1. Stel een nieuw Node.js-project in door de volgende opdracht uit te voeren.

   ```bash
   npm init -y
   ```

1. Voeg een verwijzing toe naar de yargs-module.

   ```bash
   npm install yargs
   ```

1. Voeg een verwijzing toe naar de Azure Resource Graph-module.

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. Voeg een verwijzing toe naar de Azure-verificatiebibliotheek.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Controleer in _package.json_ of `@azure/arm-managementgroups` versie **1.1.0** of hoger is en of `@azure/ms-rest-nodeauth` versie **3.0.5** of hoger is.

## <a name="create-the-management-group"></a>De beheergroep maken

1. Maak een nieuw bestand met de naam _index. js_ en voer de volgende code in.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const managementGroups = require("@azure/arm-managementgroups");

   if (argv.groupID && argv.displayName) {
       const createMG = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new managementGroups.ManagementGroupsAPI(credentials);
          const result = await client.managementGroups.createOrUpdate(
             groupId: argv.groupID,
             {
                 displayName: argv.displayName
             }
          );
          console.log(result);
       };

       createMG();
   }
   ```

1. Voer de volgende opdracht in de terminal in:

   ```bash
   node index.js --groupID "<NEW_MG_GROUP_ID>" --displayName "<NEW_MG_FRIENDLY_NAME>"
   ```

   Zorg ervoor dat u elk token `<>` tijdelijke aanduiding vervangt door respectievelijk uw _beheergroep-id_ en _beschrijvende naam voor de beheergroep_.

   Wanneer het script probeert te verifiëren, wordt er een bericht weergegeven dat vergelijkbaar is met het volgende bericht in de terminal:

   > Als u zich wilt aanmelden, opent u de pagina https://microsoft.com/devicelogin in een webbrowser. Voer de code FGB56WJUGK in om te verifiëren.

   Zodra u zich hebt geverifieerd in de browser, wordt het script verder uitgevoerd.

Het resultaat van het maken van de beheergroep wordt uitgevoerd naar de console.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de geïnstalleerde bibliotheken uit uw toepassing wilt verwijderen, kunt u dit doen met de volgende opdracht.

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een beheergroep gemaakt om uw resource-hiërarchie te organiseren. De beheergroep kan abonnementen of andere beheergroepen bevatten.

Ga verder met het volgende voor meer informatie over beheergroepen en het beheren van uw resource-hiërarchie:

> [!div class="nextstepaction"]
> [Uw resources beheren met beheergroepen](./manage.md)