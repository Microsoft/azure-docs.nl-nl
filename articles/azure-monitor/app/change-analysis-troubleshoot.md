---
title: Problemen met toepassings wijzigings analyse oplossen-Azure Monitor
description: Meer informatie over het oplossen van problemen bij het analyseren van wijzigingen in de toepassing.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 7200978ce31a47f7bd0d023cecf359b10a1c96de
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521083"
---
# <a name="troubleshoot-application-change-analysis-preview"></a>Problemen met de analyse van toepassings wijzigingen oplossen (preview-versie)

## <a name="having-trouble-registering-microsoft-change-analysis-resource-provider-from-change-history-tab"></a>Problemen bij het registreren van micro soft. De analyse resource provider wijzigen op het tabblad wijzigings overzicht

Als dit de eerste keer is dat u het wijzigings overzicht bekijkt na de integratie met de analyse van de toepassings wijzigingen, ziet u dat er automatisch een resource provider **micro soft. ChangeAnalysis** wordt geregistreerd. In zeldzame gevallen kan het om de volgende redenen mislukken:

- **U hebt onvoldoende machtigingen om de resource provider micro soft. ChangeAnalysis te registreren**. Dit fout bericht betekent dat aan uw rol in het huidige abonnement geen **micro soft. support/registreer/Action-** bereik is gekoppeld. Dit kan gebeuren als u niet de eigenaar van een abonnement bent en gedeelde toegangs machtigingen hebt via een collega (dat wil zeggen, de toegang tot een resource groep weer geven). U kunt dit probleem oplossen door contact op te nemen met de eigenaar van uw abonnement om de resource provider **micro soft. ChangeAnalysis** te registreren. Dit kan worden gedaan in Azure Portal via **abonnementen | Resource providers** en zoeken ```Microsoft.ChangeAnalysis``` en registreren in de gebruikers interface of via Azure PowerShell of Azure cli.

    Registreer de resource provider via Power shell:
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Kan de micro soft. ChangeAnalysis-resource provider niet registreren**. Dit bericht geeft aan dat er direct een fout is opgetreden bij de gebruikers interface heeft een aanvraag verzonden om de resource provider te registreren en is niet gerelateerd aan het probleem met de machtiging. Waarschijnlijk is het een probleem met een tijdelijke Internet verbinding. Vernieuw de pagina en controleer de Internet verbinding. Als de fout zich blijft voordoen, neemt u contact op met changeanalysishelp@microsoft.com

- **Dit duurt langer dan verwacht**. Dit bericht geeft aan dat de registratie langer dan twee minuten duurt. Dit is ongebruikelijk, maar dit betekent niet noodzakelijkerwijs dat er iets verkeerd is gegaan. U kunt naar **Abonnementen gaan | Resource provider** die moet worden gecontroleerd op de registratie status van de **micro soft. ChangeAnalysis** -resource provider. U kunt proberen de gebruikers interface te gebruiken voor het opheffen van de registratie, opnieuw registreren of vernieuwen om te zien of het helpt. Neem contact op met de ondersteuning als het probleem zich blijft voordoen changeanalysishelp@microsoft.com .
    ![Problemen met de RP-registratie oplossen](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![Scherm afbeelding van het hulp programma problemen vaststellen en oplossen voor een virtuele machine waarop hulpprogram ma's voor probleem oplossing is geselecteerd.](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Scherm afbeelding van de tegel voor het hulp programma probleem oplossing voor het analyseren van recente wijzigingen voor een virtuele machine.](./media/change-analysis/analyze-recent-changes.png)

## <a name="azure-lighthouse-subscription-is-not-supported"></a>Azure Lighthouse-abonnement wordt niet ondersteund

- **Kan geen query uitvoeren op de micro soft. ChangeAnalysis-resource provider** met *het bericht Azure Lighthouse-abonnement wordt niet ondersteund. de wijzigingen zijn alleen beschikbaar in de thuis Tenant van het abonnement*. Er is nu een beperking voor de resource provider voor wijzigings analyse die wordt geregistreerd via een Azure Lighthouse-abonnement voor gebruikers die zich niet in de thuis Tenant bevinden. We werken aan het adresseren van deze beperking. Als dit probleem zich voordoet, is er een tijdelijke oplossing waarbij u een Service-Principal moet maken en de rol expliciet kunt toewijzen om de toegang toe te staan.  Neem contact changeanalysishelp@microsoft.com op met meer informatie.

## <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>Er is een fout opgetreden tijdens het ophalen van wijzigingen. Vernieuw deze pagina of kom later terug om de wijzigingen weer te geven

Dit is het algemene fout bericht dat door de service voor het wijzigen van de toepassings wijziging wordt weer gegeven wanneer de wijzigingen niet kunnen worden geladen. Enkele bekende oorzaken zijn:

- Fout met Internet verbinding van het client apparaat
- Wijziging van de analyse service is tijdelijk niet beschikbaar. Als u de pagina na een paar minuten vernieuwt, wordt dit probleem doorgaans opgelost. Als de fout zich blijft voordoen, neemt u contact op met changeanalysishelp@micorosoft.com

## <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>U hebt onvoldoende machtigingen om enkele wijzigingen weer te geven. Neem contact op met de beheerder van uw Azure-abonnement

Dit is het algemene niet-geautoriseerde fout bericht, waarin wordt uitgelegd dat de huidige gebruiker niet voldoende machtigingen heeft om de wijziging weer te geven. Er is ten minste toegang tot de lezer vereist voor de resource om de wijzigingen in de infra structuur weer te geven die door Azure resource Graph en Azure Resource Manager zijn geretourneerd. Voor de wijzigingen van de web-app in het gast bestand en configuratie wijzigingen is ten minste de rol Inzender vereist.

## <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Kan de micro soft. ChangeAnalysis-resource provider niet registreren

Dit bericht geeft aan dat er direct een fout is opgetreden bij de gebruikers interface heeft een aanvraag verzonden om de resource provider te registreren en is niet gerelateerd aan het probleem met de machtiging. Waarschijnlijk is het een probleem met een tijdelijke Internet verbinding. Vernieuw de pagina en controleer de Internet verbinding. Als de fout zich blijft voordoen, neemt u contact op met changeanalysishelp@microsoft.com

## <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>U hebt onvoldoende machtigingen om de resource provider micro soft. ChangeAnalysis te registreren. Neem contact op met de beheerder van uw Azure-abonnement

Dit fout bericht betekent dat aan uw rol in het huidige abonnement geen **micro soft. support/registreer/Action-** bereik is gekoppeld. Dit kan gebeuren als u niet de eigenaar van een abonnement bent en gedeelde toegangs machtigingen hebt via een collega (dat wil zeggen, de toegang tot een resource groep weer geven). U kunt dit probleem oplossen door contact op te nemen met de eigenaar van uw abonnement om de resource provider **micro soft. ChangeAnalysis** te registreren. Dit kan worden gedaan in Azure Portal via **abonnementen | Resource providers** en zoeken ```Microsoft.ChangeAnalysis``` en registreren in de gebruikers interface of via Azure PowerShell of Azure cli.

Registreer de resource provider via Power shell:

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure resource Graph](../../governance/resource-graph/overview.md), waarmee u de analyse van energie wijzigingen kunt aanbrengen.