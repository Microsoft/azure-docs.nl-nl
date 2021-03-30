---
title: Uitgeschakelde Azure Access Control Service (ACS)-naam ruimten opnieuw activeren
description: Zoek en schakel uw Azure Access Control Service (ACS)-naam ruimten in en vraag een uitbrei ding aan om deze in te scha kelen tot 4 februari 2019.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7bb572e9e22519491290e54417f5ca350d6c0b5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85383714"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Procedure: uitgeschakelde Access Control Service naam ruimten opnieuw activeren

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Op 2017 Novemberen we dat Microsoft Azure Access Control Service (ACS), een service van Azure Active Directory (Azure AD), buiten gebruik wordt gesteld op 7 november 2018.

Sinds nu hebben we e-mail berichten verzonden naar de beheerders-e-mail van de ACS-abonnementen over de uittreding van de ACS 12 maanden, negen maanden, 6 maanden, drie maanden, 1 maand, 2 weken, 1 week en 1 dag vóór de datum van uittreding van 7 november 2018.

Op 3 oktober 2018 hebben we (via e-mail en [een blog bericht](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) een verlengings aanbod aangekondigd aan klanten die hun migratie niet vóór 7 november 2018 kunnen volt ooien. De aankondiging bevat ook instructies voor het aanvragen van de uitbrei ding.

## <a name="why-your-namespace-is-disabled"></a>Waarom uw naam ruimte is uitgeschakeld

Als u zich nog niet hebt aangemeld voor de uitbrei ding, beginnen we met het uitschakelen van ACS-naam ruimten vanaf 7 november 2018. U moet de uitbrei ding van 4 februari 2019 al hebben aangevraagd. anders is het niet mogelijk om de naam ruimten via Power shell in te scha kelen.

> [!NOTE]
> U moet een service beheerder of mede beheerder van het abonnement zijn om de Power shell-opdrachten uit te voeren en een uitbrei ding aan te vragen.

## <a name="find-and-enable-your-acs-namespaces"></a>Uw ACS-naam ruimten zoeken en inschakelen

U kunt ACS Power shell gebruiken om alle ACS-naam ruimten weer te geven en opnieuw te activeren die zijn uitgeschakeld.

1. ACS Power shell downloaden en installeren:
    1. Ga naar het PowerShell Gallery en down load [ACS. namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Installeer de module:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Een lijst met alle mogelijke opdrachten ophalen:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Voer het volgende uit om hulp te krijgen voor een specifieke opdracht:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        waarbij `[Command-Name]` de naam is van de ACS-opdracht.
1. Maak verbinding met ACS met de cmdlet **Connect-AcsAccount** . 

    Mogelijk moet u het uitvoerings beleid wijzigen door **Set-ExecutionPolicy** uit te voeren voordat u de opdracht kunt uitvoeren.
1. Vermeld uw beschik bare Azure-abonnementen met de cmdlet **Get-AcsSubscription** .
1. Vermeld uw ACS-naam ruimten met behulp van de cmdlet **Get-AcsNamespace** .
1. Controleer of de naam ruimten zijn uitgeschakeld door te bevestigen dat `State` `Disabled` .

    [![Controleren of de naam ruimten zijn uitgeschakeld](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    U kunt ook gebruiken `nslookup {your-namespace}.accesscontrol.windows.net` om te controleren of het domein nog steeds actief is.

1. Schakel uw ACS-naam ruimte (n) in met de cmdlet **Enable-AcsNamespace** .

    Zodra u uw naam ruimte (n) hebt ingeschakeld, kunt u een uitbrei ding aanvragen zodat de naam ruimte (n) niet opnieuw wordt uitgeschakeld vóór 4 februari 2019. Na deze datum mislukken alle aanvragen aan ACS.

## <a name="request-an-extension"></a>Een uitbrei ding aanvragen

We nemen nieuwe verlengings aanvragen vanaf 21 januari 2019.

We gaan het uitschakelen van naam ruimten voor klanten die uitbrei dingen hebben aangevraagd voor 4 februari 2019. U kunt de naam ruimten nog steeds opnieuw inschakelen via Power shell, maar de naam ruimten worden na 48 uur weer uitgeschakeld.

Na 4 maart 2019 kunnen klanten geen naam ruimten meer opnieuw inschakelen via Power shell.

Verdere extensies worden niet meer automatisch goedgekeurd. Als u meer tijd nodig hebt om te migreren, neemt u contact op met de [ondersteuning van Azure](https://portal.azure.com/#create/Microsoft.Support) om een gedetailleerde migratie tijdlijn te bieden.

### <a name="to-request-an-extension"></a>Een uitbrei ding aanvragen

1. Meld u aan bij de Azure Portal en maak een [nieuwe ondersteunings aanvraag](https://portal.azure.com/#create/Microsoft.Support).
1. Vul het nieuwe formulier voor de ondersteunings aanvraag in, zoals wordt weer gegeven in het volgende voor beeld.

    | Ondersteuningsaanvraag veld | Waarde |
    |-----------------------|--------------------|
    | **Type probleem** | `Technical` |
    | **Abonnement** | Ingesteld op uw abonnement |
    | **Service** | `All services` |
    | **Resource** | `General question/Resource not available` |
    | **Probleem type** | `ACS to SAS Migration` |
    | **Onderwerp** | Het probleem beschrijven |

   ![Toont een voor beeld van een nieuwe technische ondersteunings aanvraag](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>Help en ondersteuning

- Als u problemen ondervindt na het volgen van deze instructies, neemt u contact op met de [ondersteuning van Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Neem contact met ons op als u vragen of feedback hebt over het buiten gebruik stellen van ACS acsfeedback@microsoft.com .

## <a name="next-steps"></a>Volgende stappen

- Lees de informatie over het buiten gebruik stellen van ACS in [procedures: migreren vanuit Azure Access Control service](active-directory-acs-migration.md).
