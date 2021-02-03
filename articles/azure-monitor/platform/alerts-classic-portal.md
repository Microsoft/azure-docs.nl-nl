---
title: Klassieke waarschuwingen voor metrische gegevens maken en beheren met behulp van Azure Monitor
description: Meer informatie over het gebruik van Azure Portal, CLI of Power shell voor het maken, weer geven en beheren van regels voor klassieke metrische gegevens.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/18/2018
ms.openlocfilehash: 8b4cdd834c0db6869fdf804b43914eaa74b14319
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525548"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Klassieke metrische waarschuwingen maken, weer geven en beheren met behulp van Azure Monitor

> [!WARNING]
> In dit artikel wordt beschreven hoe u oudere klassieke metrische waarschuwingen maakt. Azure Monitor ondersteunt nu [nieuwere bijna realtime waarschuwingen voor metrische gegevens en een nieuwe meldings ervaring](./alerts-overview.md). Klassieke waarschuwingen worden [buiten gebruik gesteld](./monitoring-classic-retirement.md) voor open bare Cloud gebruikers, maar zijn nog steeds beperkt in beperkte mate voor resources die de nieuwe waarschuwingen nog niet ondersteunen.
>

Klassieke metrische waarschuwingen in Azure Monitor bieden een manier om een melding te ontvangen wanneer een van uw metrische gegevens een drempel waarde overschrijdt. Klassieke waarschuwingen voor metrische gegevens is een oudere functie die alleen waarschuwingen voor niet-dimensionale metrische gegevens toestaat. Er is een bestaande nieuwere functie met de naam metrische waarschuwingen, waarmee de functionaliteit van klassieke metrische waarschuwingen wordt verbeterd. Meer informatie over de nieuwe functies voor metrische waarschuwingen vindt u in [overzicht van metrische waarschuwingen](./alerts-metric-overview.md). In dit artikel wordt beschreven hoe u klassieke metrische waarschuwings regels maakt, weergeeft en beheert via Azure Portal, Azure CLI en Power shell.

## <a name="with-azure-portal"></a>Met Azure Portal

1. Ga in de [Portal](https://portal.azure.com/)naar de resource die u wilt bewaken en selecteer deze.

2. Selecteer in de sectie **controle** de optie **waarschuwingen (klassiek)**. De tekst en het pictogram kunnen enigszins verschillen voor verschillende resources. Als u hier geen **waarschuwingen (klassiek)** vindt, kunt u deze vinden in **waarschuwingen** of **waarschuwings regels**.

    ![Bewaking](media/alerts-classic-portal/AlertRulesButton.png)

3. Selecteer de opdracht **metrische waarschuwing toevoegen (klassiek)** en vul de velden in.

    ![Waarschuwing toevoegen](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **Geef** uw waarschuwings regel een naam. Kies vervolgens een **Beschrijving** die ook wordt weer gegeven in e-mail meldingen.

5. Selecteer de **metrische gegevens** die u wilt bewaken. Kies vervolgens een voor **waarde** en **drempel** waarde voor de metriek. Kies ook de tijds **periode** waaraan de metrische regel moet voldoen voordat de waarschuwing wordt geactiveerd. Als u bijvoorbeeld de periode in de afgelopen vijf minuten gebruikt en uw waarschuwing zoekt naar een CPU die hoger is dan 80%, wordt de waarschuwing geactiveerd wanneer de CPU gedurende vijf minuten 80 consistent is. Nadat de eerste trigger is uitgevoerd, wordt deze opnieuw geactiveerd wanneer de CPU gedurende 5 minuten onder 80% blijft. De meting van de CPU-metriek gebeurt elke minuut.

6. **E-mail eigenaren selecteren...** als u wilt dat beheerders en mede beheerders e-mail meldingen ontvangen wanneer de waarschuwing wordt geactiveerd.

7. Als u meldingen wilt verzenden naar extra e-mail adressen wanneer de waarschuwing wordt geactiveerd, voegt u deze toe in het veld **extra beheerder e-mail (s)** . Scheid meerdere e-mail berichten met een punt komma, in de volgende indeling: *e-mail \@ contoso. com; email2 \@ contoso.com*

8. Plaats een geldige URI in het veld **webhook** als u wilt dat deze wordt aangeroepen wanneer de waarschuwing wordt geactiveerd.

9. Als u Azure Automation gebruikt, kunt u een runbook selecteren dat moet worden uitgevoerd wanneer de waarschuwing wordt geactiveerd.

10. Selecteer **OK** om de waarschuwing te maken.

Binnen een paar minuten is de waarschuwing actief en worden triggers zoals eerder beschreven.

Nadat u een waarschuwing hebt gemaakt, kunt u deze selecteren en een van de volgende taken uitvoeren:

* Een grafiek weer geven met de metrische drempel waarde en de werkelijke waarden van de vorige dag.
* Bewerk of verwijder deze.
* **Schakel deze optie** **in** of uit als u het ontvangen van meldingen voor die waarschuwing tijdelijk wilt stoppen of hervatten.

## <a name="with-azure-cli"></a>Met Azure CLI

In de vorige secties wordt beschreven hoe u metrische waarschuwings regels kunt maken, weer geven en beheren met behulp van Azure Portal. In deze sectie wordt beschreven hoe u hetzelfde kunt doen met meerdere platformen van [Azure cli](/cli/azure/get-started-with-azure-cli). De snelste manier om te beginnen met het gebruik van Azure CLI is via [Azure Cloud shell](../../cloud-shell/overview.md).

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Alle klassieke metrische waarschuwings regels ophalen in een resource groep

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Details van een bepaalde regel voor een klassieke metrische waarschuwing weer geven

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Een regel voor een klassieke metrische waarschuwing maken

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Een regel voor een klassieke metrische waarschuwing verwijderen

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>Met PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In deze secties ziet u hoe u met Power shell-opdrachten klassieke metrische waarschuwingen kunt maken, weer geven en beheren. In de voor beelden in dit artikel wordt uitgelegd hoe u Azure Monitor-cmdlets kunt gebruiken voor klassieke metrische waarschuwingen.

1. Als u dat nog niet hebt gedaan, stelt u PowerShell in op uw computer. Zie [PowerShell installeren en configureren](/powershell/azure/) voor meer informatie. U kunt ook de volledige lijst met Azure Monitor PowerShell-cmdlets bekijken via [Azure Monitor-cmdlets (Insights)](/powershell/module/az.applicationinsights).

2. Meld u eerst aan bij uw Azure-abonnement.

    ```powershell
    Connect-AzAccount
    ```

3. Er wordt een aanmeldingsscherm weergegeven. Zodra u zich hebt aangemeld met uw account, worden de tenant-id en standaardabonnements-id weergegeven. Alle Azure-cmdlets werken in de context van uw standaardabonnement. Als u de lijst met abonnementen waartoe u toegang hebt, wilt weergeven, gebruikt u de volgende opdracht:

    ```powershell
    Get-AzSubscription
    ```

4. Als u uw werkcontext wilt wijzigen in een ander abonnement, gebruikt u de volgende opdracht:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. U kunt alle klassieke metrische waarschuwings regels ophalen voor een resource groep:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. U kunt details van een regel voor een klassieke metrische waarschuwing weer geven

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. U kunt alle waarschuwings regels ophalen die zijn ingesteld voor een doel resource. Bijvoorbeeld alle waarschuwingsregels die zijn ingesteld op een virtuele machine.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Er kunnen geen klassieke waarschuwings regels meer worden gemaakt via Power shell. Als u een waarschuwings regel wilt maken, moet u de nieuwe opdracht [' add-AzMetricAlertRule '](/powershell/module/az.monitor/add-azmetricalertrule) gebruiken.

## <a name="next-steps"></a>Volgende stappen

- [Een klassieke waarschuwing voor metrische gegevens maken met een resource manager-sjabloon](./alerts-enable-template.md).
- Een [klassieke metrische waarschuwing laten weten dat een niet-Azure-systeem gebruikmaakt van een webhook](./alerts-webhooks.md).

