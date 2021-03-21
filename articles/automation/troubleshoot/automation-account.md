---
title: Problemen met Azure Automation-account oplossen
description: In dit artikel leest u hoe u problemen met een Azure-account oplost en oplost.
services: automation
ms.subservice: ''
ms.date: 03/24/2020
ms.topic: troubleshooting
ms.openlocfilehash: 06c15136e9d2fabdf50031c8b4be455cf2f7bbca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896576"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>Problemen met Azure Automation-account oplossen

In dit artikel worden oplossingen beschreven voor problemen die kunnen optreden wanneer u een Azure Automation-account gebruikt. Zie [Azure Automation-account verificatie](../automation-security-overview.md)voor algemene informatie over Automation-accounts.

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: kan Automation-resource provider niet registreren voor abonnementen

### <a name="issue"></a>Probleem

Wanneer u werkt met beheer functies, bijvoorbeeld Updatebeheer, in uw Automation-account, treedt de volgende fout op:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Oorzaak

De resource provider voor Automation is niet geregistreerd in het abonnement.

### <a name="resolution"></a>Oplossing

Voer de volgende stappen uit in de Azure Portal om de resource provider Automation te registreren:

1. Ga in uw browser naar de [Azure Portal](https://portal.azure.com).

2. Ga naar **abonnementen** en selecteer uw abonnement.   

3. Onder **instellingen** selecteert u **resource providers**.

4. Controleer in de lijst met resource providers of de resource provider **micro soft. Automation** is geregistreerd.

5. Als de provider niet wordt weer gegeven, registreert u deze zoals beschreven in [fouten voor de registratie van de resource provider oplossen](../../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet kunt oplossen met dit artikel, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Verbinding maken met [@AzureSupport](https://twitter.com/azuresupport) . Dit is het officiële Microsoft Azure account voor het verbinden van de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**.
