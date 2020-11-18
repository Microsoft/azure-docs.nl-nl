---
title: 'Quickstart: Onboarden in Azure Sentinel'
description: In deze quickstart ontdekt u hoe u Azure Sentinel kunt onboarden door eerst Sentinel in te schakelen en vervolgens gegevensbronnen te verbinden.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 10/14/2020
ms.custom: references_regions
ms.openlocfilehash: 21e0cfd56a37304d1ec333f8713894504282541f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660658"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Quickstart: Azure Sentinel onboarden

In deze Quick Start leert u hoe u Azure Sentinel kunt onboarden. 

Om Azure Sentinel te onboarden, moet u eerst Azure Sentinel inschakelen en vervolgens uw gegevensbronnen verbinden. Azure Sentinel wordt geleverd met een aantal connectors voor Microsoft-oplossingen, die direct beschikbaar zijn en realtime integratie bieden, inclusief Microsoft 365 Defender (voorheen Microsoft Threat Protection)-oplossingen, Microsoft 365-bronnen, waaronder Office 365, Azure AD, Microsoft Defender for Identity (voorheen Azure ATP), Microsoft Cloud App Security, Azure Defender-waarschuwingen van Azure Security Center, en meer. Daarnaast zijn er ingebouwde connectors voor het bredere beveiligingsecosysteem voor niet-Microsoft-oplossingen. U kunt ook Common Event Format (CEF), Syslog of REST-API gebruiken om uw gegevensbronnen met Azure Sentinel te verbinden. 

Nadat u verbinding hebt gemaakt met uw gegevensbronnen, kiest u uit een galerie van deskundig gemaakte werkboeken die op basis van uw gegevens inzichten aan het licht brengen. Deze werkmappen kunnen gemakkelijk worden aangepast aan uw behoeften.

>[!IMPORTANT] 
> Zie [Prijzen voor Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/)voor meer informatie over de kosten voor het gebruik van Azure Sentinel.

## <a name="global-prerequisites"></a>Globale vereisten

- Een actief Azure-abonnement. Als u dit niet hebt, kunt u een [gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maken voordat u begint.

- Log Analytics-werkruimte. Leer [een Log Analytics-werkruimte maken](../azure-monitor/learn/quick-create-workspace.md). Zie [De implementatie van uw Azure Monitor-logboeken ontwerpen](../azure-monitor/platform/design-logs-deployment.md)voor meer informatie over Log Analytics-werkruimten.

- Om Azure Sentinel in te schakelen, hebt u inzendersmachtigingen nodig voor het abonnement waarin de Azure Sentinel-werkruimte zich bevindt. 
- Om Azure Sentinel te gebruiken, hebt u inzenders- of lezersmachtigingen nodig voor de resourcegroep waartoe de werkruimte behoort.
- Er zijn mogelijk extra machtigingen nodig om verbinding te maken met specifieke gegevensbronnen.
- Azure Sentinel is een betaalde service. Zie [Prijzen voor Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058) voor prijsinformatie.

### <a name="geographical-availability-and-data-residency"></a>Geografische beschikbaarheid en gegevenslocatie

- Azure Sentinel kan worden uitgevoerd op werkruimten in elke [GA-regio van Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor), behalve de regio's China en Duitsland (soevereine regio's). 

- Gegevens die worden gegenereerd door Azure Sentinel, zoals incidenten, bladwijzers en analytics-regels, bevatten mogelijk wat klantgegevens die afkomstig zijn uit de Log Analytics-werkruimten van de klant. Deze door Azure Sentinel gegenereerde gegevens worden opgeslagen in de geografie die wordt vermeld in de volgende tabel, op basis van de geografie waar de werkruimte zich bevindt:

    | Geografie van werkruimte | Gegevensgeografie gegenereerd door Azure Sentinel |
    | --- | --- |
    | Verenigde Staten<br>India<br>Brazilië<br>Afrika<br>Korea | Verenigde Staten |
    | Europa<br>Frankrijk<br>Zwitserland | Europa |
    | Australië | Australië |
    | Verenigd Koninkrijk | Verenigd Koninkrijk |
    | Canada | Canada |
    | Japan | Japan |
    |

## <a name="enable-azure-sentinel"></a>Azure Sentinel inschakelen <a name="enable"></a>

1. Meld u aan bij Azure Portal. Zorg ervoor dat het abonnement waarin Azure Sentinel wordt gemaakt, is geselecteerd.

1. Zoek en selecteer **Azure Sentinel**.

   ![Services zoeken](./media/quickstart-onboard/search-product.png)

1. Selecteer **Toevoegen**.

1. Selecteer de werkruimte die u wilt gebruiken of maak een nieuwe. U kunt Azure Sentinel uitvoeren op meer dan één werkruimte, maar de gegevens worden geïsoleerd in één werkruimte.

   ![Een werkruimte kiezen](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Door Azure Security Center gemaakte standaardwerkruimten worden niet weergegeven in de lijst; u kunt Azure Sentinel er niet op installeren.
   >

   >[!IMPORTANT]
   >
   > - Azure Sentinel **ondersteunt momenteel niet** de verplaatsing van een werkruimte naar andere resourcegroepen of abonnement na de implementatie in deze werkruimte. 
   >
   >   Als u de werkruimte al hebt verplaatst, moet u alle actieve regels onder **Analyses** uitschakelen en na vijf minuten opnieuw inschakelen. In de meeste gevallen is dit effectief, hoewel de methode niet wordt ondersteund en voor eigen risico wordt uitgevoerd.

1. Selecteer **Azure Sentinel toevoegen**.

## <a name="connect-data-sources"></a>Verbinding maken met gegevensbronnen

Azure Sentinel neemt gegevens van services en apps op door verbinding te maken met de service en de gebeurtenissen en logboeken door te sturen naar Azure Sentinel. Voor fysieke en virtuele machines kunt u de Log Analytics-agent installeren waarmee de logboeken worden verzameld en doorgestuurd naar Azure Sentinel. Voor firewalls en proxy's installeert Azure Sentinel de Log Analytics-agent op een Linux Syslog-server, waarvandaan de agent de logboekbestanden verzamelt en naar Azure Sentinel doorstuurt. 
 
1. Selecteer **Gegevensconnectors** in het hoofdmenu. Hiermee opent u de galerie met gegevensconnectors.

1. De galerie is een lijst met alle gegevensbronnen waarmee u verbinding kunt maken. Selecteer een gegevensbron en klik vervolgens op de knop **Connectorpagina openen**.

1. De connectorpagina bevat instructies voor het configureren van de connector en eventuele aanvullende instructies die nodig kunnen zijn.<br>
Als u bijvoorbeeld de **Azure Active Directory**-gegevensbron selecteert, waarmee u logboeken van Azure AD kunt streamen naar Azure Sentinel, kunt u selecteren welk type logboeken u wilt ontvangen, aanmeldingslogboeken en/of auditlogboeken. <br> Volg de installatie-instructies of [raadpleeg de relevante verbindingshandleiding](connect-data-sources.md) voor meer informatie. Zie [Connect Microsoft services](connect-data-sources.md) (Microsoft-services verbinden) voor meer informatie over gegevensconnectors.

1. In het tabblad **Volgende stappen** op de connectorpagina ziet u relevante ingebouwde werkmappen, voorbeeldquery's en sjablonen van analytics-regels die bij de gegevensconnector horen. U kunt deze ongewijzigd gebruiken of wijzigen. In ieder geval kunt u onmiddellijk interessante inzichten krijgen over uw gegevens. <br>

Nadat uw gegevensbronnen zijn verbonden, worden uw gegevens gestreamd naar Azure Sentinel en kunt u ermee aan de slag gaan. U kunt de logboeken in de [ingebouwde werkmappen](quickstart-get-visibility.md) weergeven en beginnen met het bouwen van query's in Log Analytics om [de gegevens te onderzoeken](tutorial-investigate-cases.md).

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd over onboarden en hoe u gegevensbronnen verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- Gegevens streamen van [Common Event Format-apparaten](connect-common-event-format.md) naar Azure Sentinel.