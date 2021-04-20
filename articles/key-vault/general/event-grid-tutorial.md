---
title: Meldingen van de sleutelkluis ontvangen en hierop reageren met Azure Event Grid
description: Meer informatie over het integreren van Key Vault met Azure Event Grid.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: e46161812d122a1d5647e8589c58f9528578b878
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749830"
---
# <a name="receive-and-respond-to-key-vault-notifications-with-azure-event-grid"></a>Meldingen van de sleutelkluis ontvangen en hierop reageren met Azure Event Grid

Dankzij Azure Key Vault-integratie met Azure Event Grid zijn melding van gebruikers mogelijk wanneer de status van een geheim dat is opgeslagen in een sleutelkluis, is gewijzigd. Zie [Key Vault bewaken met Event Grid](event-grid-overview.md) voor een overzicht van deze functie.

In deze handleiding wordt beschreven hoe u Key Vault-meldingen kunt ontvangen via Event Grid kunt ontvangen en hoe u kunt reageren op statuswijzigingen via Azure Automation.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Een sleutelkluis in uw Azure-abonnement. U kunt snel een nieuwe sleutelkluis maken aan de hand van de stappen in [Een geheim instellen en ophalen van Azure Key Vault met Azure CLI](../secrets/quick-create-cli.md)

## <a name="concepts"></a>Concepten

Event Grid is een gebeurtenisservice voor de cloud. Als u de stappen in deze handleiding volgt, kunt u zich abonneren op gebeurtenissen voor Key Vault en de gebeurtenissen naar Automation routeren. Wanneer een van de geheimen in de sleutelkluis bijna is verlopen, wordt Event Grid van de statuswijziging op de hoogte gesteld en wordt er een HTTP POST-aanvraag naar het eindpunt gestuurd. Vervolgens activeert een webhook een Automation-uitvoering van een PowerShell-script.

![HTTP POST-stroomdiagram](../media/event-grid-tutorial-1.png)

## <a name="create-an-automation-account"></a>Een Automation-account maken

Maak een Automation-account in [Azure Portal](https://portal.azure.com):

1.  Ga naar portal.azure.com en meld u aan bij uw abonnement.

1.  Voer in het zoekvak **Automation-accounts** in.

1.  Onder de sectie **Services** van de vervolgkeuzelijst in de zoekbalk selecteert u **Automation-accounts**.

1.  Selecteer **Toevoegen**.

    ![Deelvenster Automation-accounts](../media/event-grid-tutorial-2.png)

1.  Voer de vereiste gegevens in het deelvenster **Automation-account toevoegen** in en selecteer **Maken**.

## <a name="create-a-runbook"></a>Een runbook maken

Als uw Automation-account klaar is, maakt u een runbook.

![Een runbook-gebruikersinterface maken](../media/event-grid-tutorial-3.png)

1.  Selecteer het Automation-account dat u zojuist hebt gemaakt.

1.  Selecteer onder **Procesautomatisering** de optie **Runbooks**.

1.  Selecteer **Een runbook maken**.

1.  Geef het runbook een naam en selecteer **PowerShell** als het type runbook.

1.  Selecteer het runbook dat u hebt gemaakt en selecteer vervolgens de knop **Bewerken**.

1.  Voer de volgende code in (voor testdoeleinden) en selecteer de knop **Publiceren**. Met deze actie wordt het resultaat van de ontvangen POST-aanvraag geretourneerd.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![Runbook-gebruikersinterface publiceren](../media/event-grid-tutorial-4.png)

## <a name="create-a-webhook"></a>Een webhook maken

Maak een webhook om uw zojuist gemaakte runbook te activeren.

1.  Selecteer **Webhooks** in de sectie **Resources** van het runbook dat u zojuist hebt gepubliceerd.

1.  Selecteer **Webhook toevoegen**.

    ![Knop Webhook toevoegen](../media/event-grid-tutorial-5.png)

1.  Selecteer **Nieuwe webhook maken**.

1. Geef de webhook een naam, stel een vervaldatum in en kopieer de URL.

    > [!IMPORTANT] 
    > U kunt de URL niet weergeven nadat u deze hebt gemaakt. Zorg ervoor dat u een kopie opslaat op een veilige locatie waar u deze kunt gebruiken voor de rest van deze handleiding.

1. Selecteer **Parameters en uitvoerinstellingen** en selecteer vervolgens **OK**. Voer geen parameters in. Hiermee wordt de knop **Maken** ingeschakeld.

1. Selecteer **OK** en selecteer vervolgens **Maken**.

    ![Nieuwe webhookgebruikersinterface maken](../media/event-grid-tutorial-6.png)

## <a name="create-an-event-grid-subscription"></a>Een Event Grid-abonnement maken

Maak een Event Grid-abonnement via [Azure Portal](https://portal.azure.com).

1.  Ga naar de sleutelkluis en selecteer het tabblad **Gebeurtenissen**.

    ![Tabblad Gebeurtenissen in Azure Portal](../media/event-grid-tutorial-7.png)

1.  Selecteer de knop **Gebeurtenisabonnement**.

1.  Geef het abonnement een beschrijvende naam.

1.  Kies **Event Grid-schema**.

1.  **Onderwerpresource** moet de sleutelkluis zijn waarvoor u statuswijzigingen wilt bewaken.

1.  Voor **Filteren op gebeurtenistypen** behoudt u alle geselecteerde opties (**negen geselecteerd**).

1.  Selecteer **Webhook** voor **Eindpunttype**.

1.  Kies **Een eindpunt selecteren**. Plak in het deelvenster voor nieuwe context de webhook-URL uit de stap [Een webhook maken](#create-a-webhook) in het veld **Abonnee-eindpunt**.

1.  Selecteer **Selectie bevestigen** in het deelvenster voor de context.

1.  Selecteer **Maken**.

    ![Gebeurtenisabonnement maken](../media/event-grid-tutorial-8.png)

## <a name="test-and-verify"></a>Testen en verifiëren

Controleer of uw Event Grid-abonnement op de juiste wijze is geconfigureerd. Voor deze test wordt aangenomen dat u zich hebt geabonneerd op de melding Geheime nieuwe versie gemaakt in [Een Event Grid-abonnement maken](#create-an-event-grid-subscription) en dat u de vereiste machtigingen hebt om een nieuwe versie van een geheim in een sleutelkluis te maken.

![Testconfiguratie van Event Grid-abonnement](../media/event-grid-tutorial-9.png)

![Deelvenster Een geheim maken](../media/event-grid-tutorial-10.png)

1.  Ga naar de sleutel kluis in Azure Portal.

1.  Maak een nieuw geheim. Voor testdoeleinden stelt u de vervaldatum in op de volgende dag.

1.  Selecteer op het tabblad **Gebeurtenissen** in de sleutelkluis het Event Grid-abonnement dat u hebt gemaakt.

1.  Controleer onder **Metrische gegevens** of een gebeurtenis is vastgelegd. Er worden twee gebeurtenissen verwacht: SecretNewVersion en SecretNearExpiry. Met deze gebeurtenissen wordt gevalideerd dat Event Grid de statuswijziging van het geheim in de sleutelkluis heeft vastgelegd.

    ![Deelvenster Metrische gegevens: controleren op vastgelegde gebeurtenissen](../media/event-grid-tutorial-11.png)

1.  Ga naar uw Automation-account.

1.  Selecteer het tabblad **Runbooks** en vervolgens het runbook dat u hebt gemaakt.

1.  Selecteer het tabblad **Webhooks** en bevestig dat het 'laatst geactiveerde' tijdstempel minder dan zestig seconden verwijderd is van het tijdstip waarop u het nieuwe geheim hebt gemaakt. Dit resultaat bevestigt dat Event Grid een POST-aanvraag naar de webhook heeft gestuurd met de gebeurtenisdetails van de statuswijziging in uw sleutelkluis en dat de webhook is geactiveerd.

    ![Tabblad Webhooks, tijdstempel Laatst geactiveerd](../media/event-grid-tutorial-12.png)

1. Ga terug naar het runbook en selecteer het tabblad **Overzicht**.

1. Bekijk de lijst **Recente taken**. U ziet dat er een taak is gemaakt en dat de status is voltooid. Hiermee wordt bevestigd dat de webhook het runbook heeft geactiveerd om het script uit te voeren.

    ![Lijst Recente taken voor webhook](../media/event-grid-tutorial-13.png)

1. Selecteer de recente taak en bekijk de POST-aanvraag die van Event Grid naar de webhook is gestuurd. Bekijk de JSON en zorg ervoor dat de parameters voor de sleutelkluis en het gebeurtenistype juist zijn. Als de parameter event type in het JSON-object overeenkomt met de gebeurtenis die is opgetreden in de sleutelkluis (in dit voorbeeld is Microsoft.KeyVault.SecretNearExpiry), is de test geslaagd.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="you-cant-create-an-event-subscription"></a>U kunt geen gebeurtenisabonnement maken

Registreer Event Grid en de sleutelkluisprovider opnieuw in de resourceproviders van uw Azure-abonnement. Zie [Azure-resourceproviders en -typen](../../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd! Als u alle stappen goed hebt uitgevoerd, kunt u nu programmatisch reageren op statuswijzigingen van geheimen die in de sleutelkluis zijn opgeslagen.

Als u een systeem op basis van navragen hebt gebruikt om te zoeken naar statuswijzigingen van geheimen in uw sleutelkluizen, kunt u deze meldingsfunctie gaan gebruiken. U kunt ook het testscript in uw runbook vervangen door code om uw geheimen op een programmatische manier te vernieuwen wanneer ze op het punt staan te verlopen.

Meer informatie:


- Overzicht: [Key Vault bewaken met Azure Event Grid](event-grid-overview.md)
- Procedure: [E-mail ontvangen wanneer een sleutelkluisgeheim verandert](event-grid-logicapps.md)
- [Azure Event Grid-gebeurtenisschema voor Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Azure Key Vault-overzicht](overview.md)
- [Overzicht van Azure Event Grid](../../event-grid/overview.md)
- [Overzicht van Azure Automation](../../automation/index.yml)
