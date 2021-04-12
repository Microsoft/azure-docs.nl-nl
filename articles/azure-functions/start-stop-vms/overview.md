---
title: Overzicht van Vm's van v2 (preview-versie) starten/stoppen
description: In dit artikel wordt de versie twee van de functie Vm's starten/stoppen (preview) beschreven, waarmee Azure Resource Manager en klassieke Vm's op een schema worden gestart of gestopt.
ms.topic: conceptual
ms.service: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.openlocfilehash: 44bfbaa8b18ebeab3b74bc696a16fc4cfb6c08ec
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220931"
---
# <a name="startstop-vms-v2-preview-overview"></a>Overzicht van Vm's van v2 (preview-versie) starten/stoppen

Met de functie Vm's van het start/stoppen (preview) worden virtuele Azure-machines (Vm's) voor meerdere abonnementen gestart of gestopt. Azure-Vm's op door de gebruiker gedefinieerde planningen worden gestart of gestopt, geeft inzichten door [Azure-toepassing inzichten](../../azure-monitor/app/app-insights-overview.md)en verzenden optionele meldingen via [actie groepen](../../azure-monitor/alerts/action-groups.md). De functie kan zowel Azure Resource Manager Vm's als klassieke Vm's beheren voor de meeste scenario's.

Deze nieuwe versie van Vm's voor starten/stoppen v2 (preview) biedt een gecentraliseerde automatiserings optie met lage kosten voor klanten die hun VM-kosten willen optimaliseren. Het biedt dezelfde functionaliteit als de [oorspronkelijke versie](../../automation/automation-solution-vm-management.md) die beschikbaar is bij Azure Automation, maar is ontworpen om te profiteren van de nieuwere technologie in Azure.

## <a name="overview"></a>Overzicht

Het starten/stoppen van Vm's v2 (preview) is opnieuw ontworpen en is niet afhankelijk van Azure Automation-of Azure Monitor-logboeken, zoals vereist door de [vorige versie](../../automation/automation-solution-vm-management.md). Deze versie is afhankelijk van [Azure functions](../../azure-functions/functions-overview.md) om de uitvoering van de virtuele machine te starten en te stoppen.

Er wordt een beheerde identiteit gemaakt in Azure Active Directory (Azure AD) voor deze Azure Functions toepassing en Hiermee kunnen Vm's van virtuele machines (preview) worden gestart/gestopt om eenvoudig toegang te krijgen tot andere met Azure AD beveiligde resources, zoals de Logic apps en Azure-Vm's. Zie [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten in azure AD.

Er wordt een HTTP trigger endpoint-functie gemaakt ter ondersteuning van de planning-en sequentie scenario's die in de functie zijn opgenomen, zoals wordt weer gegeven in de volgende tabel.

|Name |Trigger |Beschrijving |
|-----|--------|------------|
|AlertAvailabilityTest |Timer |Deze functie voert de beschikbaarheids test uit om ervoor te zorgen dat de primaire functie **AutoStopVM** altijd beschikbaar is.|
|Autostop |HTTP |Deze functie ondersteunt het scenario voor **autostop** , de ingangs punt functie die wordt aangeroepen vanuit een logische app.|
|AutoStopAvailabilityTest |Timer |Deze functie voert de beschikbaarheids test uit om ervoor te zorgen dat de primaire functie **autostop** altijd beschikbaar is.|
|AutoStopVM |HTTP |Deze functie wordt automatisch geactiveerd door de VM-waarschuwing wanneer de status van de waarschuwing waar is.|
|CreateAutoStopAlertExecutor |Wachtrij |Deze functie haalt de payload-informatie van de functie **autostop** op om de waarschuwing te maken op de VM.|
|Gepland |HTTP |Deze functie is voor zowel het geplande als het Sequence-scenario (wordt onderscheiden van het payload-schema). Het is de ingangs punt functie die vanuit de logische app wordt aangeroepen en de payload voor het verwerken van de start-of stop bewerking van de virtuele machine. |
|ScheduledAvailabilityTest |Timer |Deze functie voert de beschikbaarheids test uit om ervoor te zorgen dat de primaire functie is **gepland** altijd beschikbaar is.|
|VirtualMachineRequestExecutor |Wachtrij |Deze functie voert de werkelijke start-en stop bewerking op de virtuele machine uit.|
|VirtualMachineRequestOrchestrator |Wachtrij |Met deze functie wordt de payload-informatie opgehaald uit de **geplande** functie en worden de aanvragen voor het starten en stoppen van de VM beheerd.|

De functie **geplande** http-trigger wordt bijvoorbeeld gebruikt voor het afhandelen van plannings-en sequentie scenario's. Op dezelfde manier wordt met de functie **autostop** http-activering het scenario voor automatisch stoppen afgehandeld.

De op wachtrij gebaseerde activerings functies zijn vereist ter ondersteuning van deze functie. Alle triggers op basis van een timer worden gebruikt voor het uitvoeren van de beschikbaarheids test en voor het controleren van de status van het systeem.

 [Azure Logic apps](../../logic-apps/logic-apps-overview.md) wordt gebruikt voor het configureren en beheren van de start-en stop schema's voor de virtuele machine actie ondernemen door de functie aan te roepen met behulp van een JSON-nettolading. Tijdens de eerste implementatie maakt het standaard een totaal van vijf Logic Apps voor de volgende scenario's:

- Geplande-start-en stop acties zijn gebaseerd op een schema dat u opgeeft voor Azure Resource Manager en klassieke Vm's. de geplande start en stoppen **ststv2_vms_Scheduled_start** en **ststv2_vms_Scheduled_stop** configureren.

- Sequentieel-start-en stop acties zijn gebaseerd op een planning gericht op Vm's met vooraf gedefinieerde labels voor sequentiëren. Er worden slechts twee benoemde Tags ondersteund: **sequencestart** en **sequencestop**. **ststv2_vms_Sequenced_start** en **ststv2_vms_Sequenced_stop** de geordende start en stop configureren.

    > [!NOTE]
    > Dit scenario ondersteunt alleen Azure Resource Manager Vm's.

- Autostop: deze functionaliteit wordt alleen gebruikt voor het uitvoeren van een stop actie voor zowel Azure Resource Manager als klassieke Vm's op basis van het CPU-gebruik. Het kan ook een *actie ondernemen* op basis van een planning, waarmee waarschuwingen worden gemaakt op vm's en op basis van de voor waarde, de waarschuwing wordt geactiveerd om de actie stoppen uit te voeren. **ststv2_vms_AutoStop** configureert de functionaliteit voor automatisch stoppen.

Elke start/stop-actie ondersteunt de toewijzing van een of meer abonnementen, resource groepen of een lijst met virtuele machines.

Een Azure Storage-account, dat vereist is voor-functies, wordt ook gebruikt door Vm's voor twee doel einden v2 (preview) te starten/stoppen:

   - Maakt gebruik van Azure Table Storage voor het opslaan van de meta gegevens van de uitvoerings bewerking (dat wil zeggen, de actie VM starten/stoppen).

   - Maakt gebruik van Azure Queue Storage om de Azure Functions wachtrij Triggers te ondersteunen.

Alle telemetriegegevens, die traceer logboeken zijn van de uitvoering van de functie-app, worden verzonden naar het verbonden Application Insights exemplaar. U kunt de telemetrie-gegevens weer geven die zijn opgeslagen in Application Insights van een reeks vooraf gedefinieerde visualisaties die worden weer gegeven in een gedeeld [Azure-dash board](../../azure-portal/azure-portal-dashboards.md).

:::image type="content" source="media/overview/shared-dashboard-sml.png" alt-text="Dash board Shared status van Vm's starten/stoppen":::

E-mail meldingen worden ook verzonden als gevolg van de acties die op de Vm's worden uitgevoerd.

## <a name="new-releases"></a>Nieuwe releases

Wanneer een nieuwe versie van Vm's voor starten/stoppen v2 (preview) wordt uitgebracht, wordt uw exemplaar automatisch bijgewerkt zonder hand matig opnieuw te implementeren.

## <a name="supported-scoping-options"></a>Opties voor ondersteunde scopes

### <a name="subscription"></a>Abonnement

Het bereik van een abonnement kan worden gebruikt wanneer u de actie starten en stoppen moet uitvoeren op alle virtuele machines in een volledig abonnement, en u kunt indien nodig meerdere abonnementen selecteren.  

U kunt ook een lijst opgeven met Vm's die moeten worden uitgesloten en deze worden van de actie genegeerd. U kunt ook joker tekens gebruiken om alle namen op te geven die gelijktijdig kunnen worden genegeerd.

### <a name="resource-group"></a>Resourcegroep

Het bereik van een resource groep kan worden gebruikt wanneer u de actie starten en stoppen moet uitvoeren op alle virtuele machines door een of meer namen van resource groepen op te geven en in een of meer abonnementen.

U kunt ook een lijst opgeven met Vm's die moeten worden uitgesloten en deze worden van de actie genegeerd. U kunt ook joker tekens gebruiken om alle namen op te geven die gelijktijdig kunnen worden genegeerd.

### <a name="vmlist"></a>VMList

Het opgeven van een lijst met Vm's kan worden gebruikt wanneer u de start-en stop actie moet uitvoeren op een specifieke set virtuele machines en op meerdere abonnementen. Deze optie biedt geen ondersteuning voor het opgeven van een lijst met Vm's die moeten worden uitgesloten.

## <a name="prerequisites"></a>Vereisten

- U moet een Azure-account hebben met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/)

- Aan uw account is de machtiging [Inzender](../../role-based-access-control/built-in-roles.md#contributor) verleend in het abonnement.

- Vm's v2 starten/stoppen (preview) is beschikbaar in alle Cloud regio's van Azure Global en Amerikaanse overheid die worden vermeld op de pagina [met beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=functions) voor Azure functions.

## <a name="next-steps"></a>Volgende stappen

Zie [Deploying start/stop vm's](deploy.md) (preview) voor informatie over het implementeren van deze functie.