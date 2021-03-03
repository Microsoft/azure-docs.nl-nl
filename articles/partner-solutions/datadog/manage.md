---
title: Een Datadog-resource beheren-Azure-partner oplossingen
description: In dit artikel wordt het beheer van een Datadog-bron in de Azure Portal beschreven. Eenmalige aanmelding instellen, een confluente organisatie verwijderen en ondersteuning krijgen.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 1a76f79f31d1f4518c069afb7fccbad5bd22d4e2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745294"
---
# <a name="manage-the-datadog-resource"></a>De Datadog-resource beheren

In dit artikel wordt beschreven hoe u de instellingen voor Azure-integratie beheert met Datadog.

## <a name="resource-overview"></a>Resource overzicht

Als u de details van uw Datadog-resource wilt weer geven, selecteert u **overzicht** in het linkerdeel venster.

:::image type="content" source="media/manage/resource-overview.png" alt-text="Overzicht van Datadog-resources" border="true" lightbox="media/manage/resource-overview.png":::

De details zijn onder andere:

- Naam van de resourcegroep
- Locatie/regio
- Abonnement
- Tags
- Koppeling voor eenmalige aanmelding naar Datadog-organisatie
- Datadog aanbieding/plan
- Factureringstermijn

Het bevat ook koppelingen naar Datadog Dash boards, logboeken en host Maps.

Het overzichts scherm bevat een samen vatting van de resources die logboeken en metrische gegevens verzenden naar Datadog.

- Resource type: Azure-resource type.
- Totaal aantal resources: het aantal resources voor het bron type.
- Bronnen voor het verzenden van Logboeken: aantal resources die logboeken verzenden naar Datadog via de integratie.
- Resources die metrische gegevens verzenden: aantal resources die metrische gegevens verzenden naar Datadog via de integratie.

## <a name="reconfigure-rules-for-metrics-and-logs"></a>Regels voor metrische gegevens en logboeken opnieuw configureren

Als u de configuratie regels voor metrische gegevens en logboeken wilt wijzigen, selecteert u **metrische gegevens en logboeken** in het linkerdeel venster.

:::image type="content" source="media/manage/reconfigure-metrics-and-logs.png" alt-text="Wijzig de configuratie van Logboeken en metrische gegevens voor de Datadog-resource." border="true":::

Zie [metrische gegevens en logboeken configureren](create.md#configure-metrics-and-logs)voor meer informatie.

## <a name="view-monitored-resources"></a>Bewaakte bronnen weer geven

Selecteer **bewaakte resources** in het linkerdeel venster om de lijst met resources die logboeken naar Datadog verzenden te bekijken.

:::image type="content" source="media/manage/view-monitored-resources.png" alt-text="Weer geven welke bronnen worden bewaakt door Datadog" border="true":::

U kunt de lijst met resources filteren op resource type, naam van resource groep en locatie, en of de resource logboeken en metrische gegevens verzendt.

De kolom **Logboeken in Datadog** geeft aan of de resource logboeken naar Datadog verzendt. Als de resource geen logboeken verzendt, wordt in dit veld aangegeven waarom er geen logboeken naar Datadog worden verzonden. Dit kan de volgende oorzaken hebben:

- De resource ondersteunt het verzenden van logboeken niet. Alleen resource typen met bewakings logboek categorieën kunnen worden geconfigureerd voor het verzenden van logboeken naar Datadog.
- De limiet van vijf Diagnostische instellingen is bereikt. Elke Azure-resource kan Maxi maal vijf Diagnostische instellingen hebben. Zie [Diagnostische instellingen](../../azure-monitor/platform/diagnostic-settings.md)voor meer informatie.
- Fout. De resource is geconfigureerd voor het verzenden van logboeken naar Datadog, maar wordt geblokkeerd door een fout.
- De logboeken zijn niet geconfigureerd. Alleen Azure-resources met de juiste resource Tags zijn geconfigureerd voor het verzenden van logboeken naar Datadog.
- De regio wordt niet ondersteund. De Azure-resource bevindt zich in een regio die momenteel geen ondersteuning biedt voor het verzenden van logboeken naar Datadog.
- De Datadog-agent is niet geconfigureerd. Virtuele machines waarop geen Datadog-agent is geïnstalleerd, verzenden geen logboeken naar Datadog.

## <a name="api-keys"></a>API-sleutels

Als u de lijst met API-sleutels voor uw Datadog-resource wilt weer geven, selecteert u de **sleutels** in het linkerdeel venster. U ziet informatie over de sleutels.

:::image type="content" source="media/manage/keys.png" alt-text="API-sleutels voor de Datadog-organisatie." border="true":::

De Azure Portal biedt een alleen-lezen weer gave van de API-sleutels. Selecteer de Datadog-Portal koppeling om de sleutels te beheren. Nadat u wijzigingen in de Datadog-Portal hebt aangebracht, vernieuwt u de weer gave Azure Portal.

De integratie van Azure Datadog biedt u de mogelijkheid om Datadog-agent op een virtuele machine of app service te installeren. Als er geen standaard sleutel is geselecteerd, mislukt de installatie van de Datadog-agent.

## <a name="monitor-virtual-machines-using-the-datadog-agent"></a>Virtuele machines bewaken met behulp van de Datadog-agent

U kunt Datadog-agents op virtuele machines installeren als een uitbrei ding. Ga naar de agent van de **virtuele machine** in het linkerdeel venster van de Datadog. In dit scherm ziet u de lijst met alle virtuele machines in het abonnement.

Voor elke virtuele machine worden de volgende gegevens weer gegeven:

- Resource naam – naam van virtuele machine
- Resource status: Hiermee wordt aangegeven of de virtuele machine is gestopt of wordt uitgevoerd. De Datadog-agent kan alleen worden geïnstalleerd op virtuele machines met. Als de virtuele machine is gestopt, wordt het installeren van de Datadog-agent uitgeschakeld.
- Agent versie: het versie nummer van de Datadog-agent.
- Agent status: of de Datadog-agent wordt uitgevoerd op de virtuele machine.
- Integraties ingeschakeld: de belangrijkste metrische gegevens die worden verzameld door de Datadog-agent.
- Installatie methode: het specifieke hulp programma dat wordt gebruikt om de Datadog-agent te installeren. Bijvoorbeeld chef of script.
- Logboeken verzenden: of de Datadog-Agent logboeken naar Datadog verzendt.

Selecteer de virtuele machine waarop u de Datadog-agent wilt installeren. Selecteer **agent installeren**.

De portal vraagt om bevestiging dat u de agent wilt installeren met de standaard sleutel. Selecteer **OK** om te beginnen met de installatie. In azure wordt de status van de **installatie** weer gegeven totdat de agent is geïnstalleerd en ingericht.

Nadat de Datadog-agent is geïnstalleerd, wordt de status gewijzigd in geïnstalleerd.

Als u wilt zien dat de Datadog-agent is geïnstalleerd, selecteert u de virtuele machine en navigeert u naar het venster extensies.

U kunt Datadog-agents op een virtuele machine verwijderen door naar de agent van de **virtuele machine** te gaan. Selecteer de virtuele machine en **Verwijder de agent**.

## <a name="monitor-app-services-using-the-datadog-agent-as-an-extension"></a>App Services-agent als extensie bewaken met behulp van de Datadog

U kunt Datadog-agents op app Services installeren als een uitbrei ding. Ga naar **app service extensie** in het linkerdeel venster. In dit scherm ziet u de lijst met alle app-Services in het abonnement.

Voor elke app service worden de volgende gegevens elementen weer gegeven:

- Resource naam – naam van de virtuele machine.
- Resource status: of de app service is gestopt of wordt uitgevoerd. De Datadog-agent kan alleen worden geïnstalleerd op app-services die worden uitgevoerd. Als de app service is gestopt, is het installeren van de Datadog-agent uitgeschakeld.
- App service-plan: het specifieke abonnement dat is geconfigureerd voor de app service.
- Agent versie: het versie nummer van de Datadog-agent.

Als u de Datadog-agent wilt installeren, selecteert u de app-service en de **installatie-uitbrei ding**. De meest recente Datadog-agent wordt als een uitbrei ding geïnstalleerd op de app-service.

In de portal wordt bevestigd dat u de Datadog-agent wilt installeren. Daarnaast worden de toepassings instellingen voor de specifieke app service bijgewerkt met de standaard sleutel. De app service wordt opnieuw gestart nadat de installatie van de Datadog-agent is voltooid.

Selecteer **OK** om het installatie proces voor de Datadog-agent te starten. In de portal wordt de status van de **installatie** weer gegeven totdat de agent is geïnstalleerd. Nadat de Datadog-agent is geïnstalleerd, wordt de status gewijzigd in geïnstalleerd.

Als u Datadog-agents in de app-service wilt verwijderen, gaat u naar **app service-extensie**. Selecteer de extensie app service en **Uninstall**

## <a name="reconfigure-single-sign-on"></a>Eenmalige aanmelding opnieuw configureren

Als u eenmalige aanmelding opnieuw wilt configureren, selecteert u **eenmalige aanmelding** in het linkerdeel venster.

Als u eenmalige aanmelding wilt instellen via Azure Active Directory, selecteert u **eenmalige aanmelding inschakelen via Azure Active Directory**.

De portal haalt de juiste Datadog-toepassing op uit Azure Active Directory. De app is afkomstig uit de naam van de Enter prise-app die u hebt geselecteerd bij het instellen van de integratie. Selecteer de naam van de Datadog-app zoals hieronder wordt weer gegeven:
 
:::image type="content" source="media/manage/reconfigure-single-sign-on.png" alt-text="Configureer de toepassing voor eenmalige aanmelding opnieuw." border="true":::
 
## <a name="disable-or-enable-integration"></a>Integratie in-of uitschakelen

U kunt stoppen met het verzenden van Logboeken en metrische gegevens van Azure naar Datadog. Er worden nog steeds kosten in rekening gebracht voor andere Datadog-services die niet zijn gerelateerd aan metrische gegevens en logboeken voor bewaking.

Als u de Azure-integratie met Datadog wilt uitschakelen, gaat u naar **overzicht**. Selecteer **uitschakelen** en **OK**.
 
:::image type="content" source="media/manage/disable.png" alt-text="Datadog-resource uitschakelen." border="true":::

Als u de Azure-integratie met Datadog wilt inschakelen, gaat u naar **overzicht**. Selecteer **inschakelen** en **OK**. Als u **inschakelen** selecteert, wordt een vorige configuratie opgehaald voor metrische gegevens en Logboeken. De configuratie bepaalt welke Azure-resources metrische gegevens en logboeken naar Datadog verzenden. Na het volt ooien van de stap worden metrische gegevens en logboeken verzonden naar Datadog.
 
:::image type="content" source="media/manage/enable.png" alt-text="Schakel de Datadog-resource in." border="true":::

## <a name="delete-datadog-resource"></a>Datadog-resource verwijderen

Ga naar **overzicht** in het linkerdeel venster en selecteer **verwijderen**. Bevestig dat u de Datadog-resource wilt verwijderen. Selecteer **Verwijderen**.

:::image type="content" source="media/manage/delete.png" alt-text="Datadog-resource verwijderen" border="true":::

Als er slechts één Datadog-resource wordt toegewezen aan een Datadog-organisatie, worden logboeken en metrische gegevens niet meer naar Datadog verzonden. Alle facturering stopt voor Datadog via Azure Marketplace.

Als er meer dan één Datadog-resource aan de Datadog-organisatie is toegewezen, stopt het verwijderen van de Datadog-resource alleen het verzenden van Logboeken en metrische gegevens voor die Datadog-resource. Omdat de Datadog-organisatie is gekoppeld aan andere Azure-resources, loopt de facturering via de Azure Marketplace.

## <a name="getting-support"></a>Ondersteuning

Als u contact wilt opnemen met de ondersteuning van Azure Datadog Integration, selecteert u **nieuw ondersteuningsaanvraag** in het linkerdeel venster. Selecteer de koppeling naar de Datadog-Portal.

:::image type="content" source="media/manage/support-request.png" alt-text="Een nieuwe Datadog-ondersteunings aanvraag maken" border="true":::

## <a name="next-steps"></a>Volgende stappen

Zie [Troubleshooting Datadog Solutions](troubleshoot.md)(Engelstalig) voor hulp bij het oplossen van problemen.
