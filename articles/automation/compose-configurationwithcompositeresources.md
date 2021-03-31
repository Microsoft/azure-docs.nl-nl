---
title: DSC-configuraties samenstellen
description: In dit artikel leest u hoe u configuraties kunt samen stellen met behulp van samengestelde resources in Azure Automation status configuratie.
keywords: Power shell DSC, desired state Configuration, Power shell DSC Azure, samengestelde resources
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 1b1bbb12412deec6ecac8cf1ffd47a00f778862e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98894725"
---
# <a name="compose-dsc-configurations"></a>DSC-configuraties samenstellen

Wanneer u resources moet beheren met meer dan één desired state Configuration (DSC), is het beste om [samengestelde resources](/powershell/scripting/dsc/resources/authoringresourcecomposite)te gebruiken. Een samengestelde resource is een geneste en geparametriseerde configuratie die wordt gebruikt als een DSC-resource in een andere configuratie. Met samengestelde resources kunt u complexe configuraties maken, terwijl de onderliggende samengestelde resources afzonderlijk kunnen worden beheerd en gebouwd.

Azure Automation maakt het mogelijk om [samengestelde resources te importeren en te compileren](automation-dsc-compile.md). Zodra u samengestelde resources hebt geïmporteerd in uw Automation-account, kunt u Azure Automation status configuratie gebruiken via de functie **status configuratie (DSC)** in de Azure Portal.

## <a name="compose-a-configuration"></a>Een configuratie opstellen

Voordat u een configuratie kunt toewijzen van samengestelde resources in de Azure Portal, moet u de configuratie opstellen. Samen stelling maakt gebruik van de **opstel configuratie** op de pagina State Configuration (DSC) en op het tabblad **configuraties** of de **gecompileerde configuraties** .

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik aan de linkerkant op **alle resources** en vervolgens op de naam van uw Automation-account.
1. Selecteer op de pagina Automation-account **status configuratie (DSC)** onder **configuratie beheer**.
1. Klik op de pagina status configuratie (DSC) op het tabblad **configuraties** of **gecompileerde configuraties** en klik vervolgens op **configuratie opstellen** in het menu boven aan de pagina.
1. Geef in de sectie **basis beginselen** de nieuwe configuratie naam (vereist) op en klik op een wille keurige plaats in de rij van elke samengestelde resource die u wilt toevoegen aan de nieuwe configuratie. Klik vervolgens op **volgende** of op de stap **bron code** . Voor de volgende stappen zijn geselecteerde `PSExecutionPolicy` en `RenameAndDomainJoin` samengestelde resources geselecteerd.
   ![Scherm afbeelding van de stap basis beginselen van de pagina opstellen van de configuratie](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. De **bron code** stap laat zien hoe de bestaande configuratie van de geselecteerde samengestelde resources eruit ziet. U kunt het samen voegen van alle para meters bekijken en bepalen hoe ze worden door gegeven aan de samengestelde resource. Wanneer u klaar bent met het controleren van de nieuwe bron code, klikt u op **volgende** of op de stap **para meters** .
   ![Scherm afbeelding van de stap bron code van de pagina opstellen van de configuratie](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. In de stap **para meters** wordt de para meter voor elke samengestelde resource weer gegeven, zodat waarden kunnen worden opgegeven. Als een para meter een beschrijving heeft, wordt deze weer gegeven naast het veld para meter. Als een para meter van het `PSCredential` type is, geeft de vervolg keuzelijst een lijst met **referentie** objecten in het huidige Automation-account. Er is ook een optie voor het **toevoegen van referenties** beschikbaar. Als alle vereiste para meters zijn opgegeven, klikt u op **opslaan en compileren**.
   ![Scherm afbeelding van de stap para meters van de pagina opstellen van de configuratie](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

## <a name="submit-the-configuration-for-compilation"></a>De configuratie voor compilatie verzenden

Zodra de nieuwe configuratie is opgeslagen, wordt deze voor compilatie verzonden. U kunt de status van de compilatie taak bekijken, zoals u dat met een geïmporteerde configuratie doet. Zie [een compilatie taak weer geven](automation-dsc-getting-started.md#view-a-compilation-job)voor meer informatie.

Wanneer de compilatie is voltooid, wordt de nieuwe configuratie weer gegeven op het tabblad **gecompileerde configuraties** . Vervolgens kunt u de configuratie toewijzen aan een beheerd knoop punt met behulp van de stappen in [een knoop punt opnieuw toewijzen aan een andere knooppunt configuratie](automation-dsc-getting-started.md#reassign-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Automation status configuratie inschakelen](automation-dsc-onboarding.md)voor meer informatie over het inschakelen van knoop punten.
- Zie [DSC-configuraties compileren in azure Automation status configuratie](automation-dsc-compile.md)voor meer informatie over het compileren van DSC-configuraties zodat u ze aan doel knooppunten kunt toewijzen.
- Zie [continue implementatie instellen met chocolade](automation-dsc-cd-chocolatey.md)voor een voor beeld van het gebruik van Azure Automation status configuratie in een pijp lijn voor continue implementatie.
- Zie [prijzen voor Azure Automation status configuratie](https://azure.microsoft.com/pricing/details/automation/)voor prijs informatie.
- Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
