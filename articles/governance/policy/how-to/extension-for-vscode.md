---
title: Azure Policy-extensie voor Visual Studio code
description: Meer informatie over het gebruik van de Azure Policy extensie voor Visual Studio code voor het opzoeken van Azure Resource Manager aliassen.
ms.date: 10/20/2020
ms.topic: how-to
ms.openlocfilehash: 8c7357d70323fa74ec77cf43bd11f149a2f99154
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906298"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Azure Policy-extensie voor Visual Studio code gebruiken

> Van toepassing op Azure Policy extensie versie **0.1.0** en hoger

Meer informatie over het gebruik van de Azure Policy extensie voor Visual Studio code voor het opzoeken van [aliassen](../concepts/definition-structure.md#aliases), het controleren van resources en beleid, het exporteren van objecten en het evalueren van beleids definities. Eerst wordt beschreven hoe u de extensie van de Azure Policy installeert in Visual Studio code. Vervolgens wordt uitgelegd hoe u aliassen opzoekt.

De uitbrei ding van de Azure Policy voor Visual Studio code kan worden geïnstalleerd op alle platforms die worden ondersteund door Visual Studio code. Deze ondersteuning omvat Windows, Linux en macOS.

## <a name="prerequisites"></a>Vereisten

De volgende items zijn vereist voor het voltooien van de stappen in dit artikel:

- Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- [Visual Studio Code](https://code.visualstudio.com).

## <a name="install-and-configure-the-azure-policy-extension"></a>De Azure Policy-extensie installeren en configureren

Nadat u aan de vereisten hebt voldaan, kunt u Azure Policy extensie voor Visual Studio code installeren door de volgende stappen uit te voeren:

1. Open Visual Studio Code.
1. Ga in de menu balk naar **View**  >  **uitbrei dingen** weer geven.
1. Voer in het zoekvak **Azure Policy** in.
1. Selecteer **Azure Policy** in de zoek resultaten en selecteer vervolgens **installeren**.
1. Selecteer zo nodig **Opnieuw laden**.

Voor een nationale Cloud gebruiker voert u de volgende stappen uit om eerst de Azure-omgeving in te stellen:

1. Selecteer **File\Preferences\Settings**.
1. Zoek naar de volgende teken reeks: _Azure: Cloud_
1. Selecteer de cloud in de lijst:

   :::image type="content" source="../media/extension-for-vscode/set-default-azure-cloud-sign-in.png" alt-text="Scherm opname van het selecteren van de Azure-Cloud aanmelding voor Visual Studio-code." border="false":::

## <a name="using-the-policy-extension"></a>De beleids uitbreiding gebruiken

> [!NOTE]
> Wijzigingen die lokaal zijn aangebracht in beleid dat wordt weer gegeven in de Azure Policy extensie voor Visual Studio code, worden niet gesynchroniseerd met Azure.

### <a name="connect-to-an-azure-account"></a>Verbinding maken met een Azure-account

Als u resources en lookup-aliassen wilt evalueren, moet u verbinding maken met uw Azure-account. Volg deze stappen om verbinding te maken met Azure vanuit Visual Studio code:

1. Meld u aan bij Azure vanuit de uitbrei ding van de Azure Policy of het opdracht palet.

   - Azure Policy extensie

     Selecteer in de Azure Policy-extensie **Aanmelden bij Azure**.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png" alt-text="Scherm opname van Visual Studio code en het pictogram voor de uitbrei ding van de Azure Policy." border="false":::

   - Opdracht palet

     Ga in de menu balk naar het **View**  >  **opdracht palet** weer geven en voer **Azure in: Meld** u aan.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png" alt-text="Scherm opname van de Azure Cloud-aanmeld opties voor Visual Studio code vanuit het opdracht palet." border="false":::

1. Volg de instructies voor aanmelden om u aan te melden bij Azure. Nadat u verbinding hebt gemaakt, wordt de naam van uw Azure-account weer gegeven op de status balk onder aan het venster Visual Studio code.

### <a name="select-subscriptions"></a>Abonnementen selecteren

Wanneer u zich voor het eerst aanmeldt, worden alleen de standaard-abonnements bronnen en-beleids regels geladen door de extensie Azure Policy. Volg deze stappen om abonnementen toe te voegen aan of te verwijderen uit de weer gave van resources en beleid:

1. Start de opdracht abonnement vanuit het opdracht palet of de venster voet tekst.

   - Opdracht palet:

     Ga in de menu balk naar het **View** > **opdracht palet** weer geven en voer **Azure in: Selecteer abonnementen**.

   - Venster voet tekst

     Selecteer in de voet tekst van het venster onder aan het scherm het segment dat overeenkomt met **Azure: \<your account\>**.

1. Gebruik het vak filteren om abonnementen snel op naam te vinden. Controleer of verwijder vervolgens de controle van elk abonnement om de abonnementen in te stellen die worden weer gegeven door de Azure Policy-extensie. Wanneer u klaar bent met het toevoegen of verwijderen van abonnementen om weer te geven, selecteert u **OK**.

### <a name="search-for-and-view-resources"></a>Resources zoeken en weer geven

De uitbrei ding Azure Policy vermeldt resources in de geselecteerde abonnementen op resource provider en op resource groep in het deel venster **resources** . De structuur weergave bevat de volgende groeperingen van resources binnen het geselecteerde abonnement of op het abonnements niveau:

- **Resource providers**
  - Elke geregistreerde resource provider met resources en gerelateerde onderliggende resources die beleids aliassen hebben
- **Resource groepen**
  - Alle resources op de resource groep waarin ze zich bevinden

De uitbrei ding filtert standaard het deel van de resource provider op bestaande resources en resources die beleids aliassen hebben. Wijzig dit gedrag in **instellingen**  >  **extensies**  >  **Azure Policy** om alle resource providers zonder filters weer te geven.

Klanten met honderden of duizenden resources in één abonnement kunnen een Doorzoek bare manier gebruiken om hun resources te vinden. Met de Azure Policy extensie kunt u zoeken naar een specifieke resource door de volgende stappen uit te voeren:

1. Start de Zoek interface vanuit de Azure Policy extensie of het opdracht palet.

   - Azure Policy extensie

     Beweeg de muis aanwijzer vanuit de Azure Policy-uitbrei ding over het deel venster **resources** , selecteer het beletsel teken en selecteer vervolgens **resources zoeken**.

   - Opdracht palet:

     Ga in de menu balk naar het **View** > **opdracht palet** weer geven en voer **resources in: resources zoeken**.

1. Als er meer dan één abonnement is geselecteerd voor weer gave, gebruikt u het filter om te selecteren welk abonnement u wilt zoeken.

1. Gebruik het filter om de resource groep te selecteren die u wilt doorzoeken die deel uitmaakt van het eerder gekozen abonnement.

1. Gebruik het filter om de resource te selecteren die u wilt weer geven. Het filter werkt zowel voor de resource naam als voor het resource type.

### <a name="discover-aliases-for-resource-properties"></a>Aliassen ontdekken voor resource-eigenschappen

Wanneer een resource is geselecteerd, hetzij via de Zoek interface, hetzij door deze te selecteren in de tree view, opent de extensie Azure Policy het JSON-bestand dat de resource en alle bijbehorende Azure Resource Manager eigenschaps waarden vertegenwoordigt.

Zodra een resource is geopend, wordt met de muis aanwijzer over de naam van de Resource Manager-eigenschap of de waarde de Azure Policy alias weer gegeven als er een bestaat. In dit voor beeld is de resource een `Microsoft.Compute/virtualMachines` resource type en de eigenschap **Properties. StorageProfile. imageReference. offer.** Met de muis aanwijzer worden de overeenkomende aliassen weer gegeven.

:::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Scherm afbeelding van de uitbrei ding van de Azure Policy voor Visual Studio code die een eigenschap aanwijst om de alias namen weer te geven." border="false":::

> [!NOTE]
> De VS code-extensie ondersteunt alleen de evaluatie van eigenschappen van de Resource Manager-modus. Zie de [modus definities](../concepts/definition-structure.md#mode)voor meer informatie over de modi.

### <a name="search-for-and-view-policies-and-assignments"></a>Beleids regels en toewijzingen zoeken en weer geven

In de uitbrei ding Azure Policy worden beleids typen en beleids toewijzingen weer gegeven als een tree view voor de geselecteerde abonnementen in het deel venster **beleid** . Klanten met honderden of duizenden beleids regels of toewijzingen in één abonnement kunnen een Doorzoek bare manier gebruiken om hun beleid of toewijzingen te vinden. Met de Azure Policy-extensie kunt u zoeken naar een specifiek beleid of een specifieke toewijzing door de volgende stappen uit te voeren:

1. Start de Zoek interface vanuit de Azure Policy extensie of het opdracht palet.

   - Azure Policy extensie

     Beweeg de muis aanwijzer met de uitbrei ding van de Azure Policy naar het deel venster **beleid** en selecteer het beletsel teken en selecteer vervolgens **Zoek beleid**.

   - Opdracht palet:

     Ga in de menu balk naar het **View** > **opdracht palet** weer geven en voer **beleid in: Zoek beleid**.

1. Als er meer dan één abonnement is geselecteerd voor weer gave, gebruikt u het filter om te selecteren welk abonnement u wilt zoeken.

1. Gebruik het filter om te selecteren welk beleids type of welke toewijzing u wilt doorzoeken dat deel uitmaakt van het eerder gekozen abonnement.

1. Gebruik het filter om te selecteren welk beleid u wilt weer geven. Het filter werkt voor _DisplayName_ voor de beleids definitie of beleids toewijzing.

Bij het selecteren van een beleid of toewijzing, hetzij via de Zoek interface, hetzij door het te selecteren in de structuur weergave, opent de Azure Policy extensie de JSON die het beleid of de toewijzing vertegenwoordigt en alle eigenschaps waarden van de Resource Manager. De extensie kan het geopende JSON-schema van Azure Policy valideren.

### <a name="export-objects"></a>Objecten exporteren

Objecten van uw abonnementen kunnen worden geëxporteerd naar een lokaal JSON-bestand. Beweeg in het deel venster **resources** of **beleids regels** de muis aanwijzer over of selecteer een exporteerbaar object. Selecteer aan het einde van de gemarkeerde rij het pictogram opslaan en selecteer een map om de JSON van de resources op te slaan.

De volgende objecten kunnen lokaal worden geëxporteerd:

- Deel venster resources
  - Resourcegroepen
  - Afzonderlijke resources (in een resource groep of bij een resource provider)
- Deel venster beleid
  - Beleidstoewijzingen
  - Ingebouwde beleids definities
  - Aangepaste beleids definities
  - Initiatieven

### <a name="on-demand-evaluation-scan"></a>Evaluatiescan op aanvraag

Een evaluatie scan kan worden gestart met de Azure Policy extensie voor Visual Studio code. Als u een evaluatie wilt starten, selecteert u elk van de volgende objecten en maakt u deze vast: een resource, een beleids definitie en een beleids toewijzing.

1. Als u elk object wilt vastmaken, zoekt u het in het deel venster **resources** of in het deel venster **beleid** en selecteert u de vastmaken aan een pictogram van het tabblad bewerken. Wanneer u een object vastmaakt, voegt u dit toe aan het deel venster **evaluatie** van de extensie.
1. Selecteer een van de objecten in het deel venster **evaluatie** en gebruik het pictogram selecteren voor evaluatie om dit te markeren als opgenomen in de evaluatie.
1. Selecteer boven in het deel venster **evaluatie** het pictogram evaluatie uitvoeren. Er wordt een nieuw deel venster in Visual Studio code geopend met de resulterende evaluatie Details in JSON-indeling.

> [!NOTE]
> Voor [AuditIfNotExists](../concepts/effects.md#auditifnotexists) -of [DeployIfNotExists](../concepts/effects.md#deployifnotexists) -beleids definities gebruikt u het plus pictogram in het deel venster **evaluatie** om een _gerelateerde_ resource te selecteren voor de controle op aanwezigheid.

De evaluatie resultaten bevatten informatie over de beleids definitie en beleids toewijzing, samen met de eigenschap **policyEvaluations. evaluationResult** . De uitvoer ziet er ongeveer uit als in het volgende voor beeld:

```json
{
    "policyEvaluations": [
        {
            "policyInfo": {
                ...
            },
            "evaluationResult": "Compliant",
            "effectDetails": {
                "policyEffect": "Audit",
                "existenceScope": "None"
            }
        }
    ]
}
```

> [!NOTE]
> De VS code-extensie ondersteunt alleen de evaluatie van eigenschappen van de Resource Manager-modus. Zie de [modus definities](../concepts/definition-structure.md#mode)voor meer informatie over de modi.
>
> De evaluatie functie werkt niet voor macOS-en Linux-installaties van de uitbrei ding.

### <a name="sign-out"></a>Afmelden

Ga in de menu balk naar het **View**  >  **opdracht palet** weer geven en voer **Azure in: Meld** u aan.

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](programmatically-create.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
