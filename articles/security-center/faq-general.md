---
title: Veelgestelde vragen over Azure Security Center-algemene vragen
description: Veelgestelde vragen over Azure Security Center, een product dat u helpt bedreigingen te voor komen, te detecteren en erop te reageren
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 3a8429d9dc6820b1f79c49d325872b61833f988d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095541"
---
# <a name="faq---general-questions-about-azure-security-center"></a>Veelgestelde vragen: algemene vragen over Azure Security Center

## <a name="what-is-azure-security-center"></a>Wat is Azure Security Center?
Azure Security Center helpt u bedreigingen te voor komen, te detecteren en erop te reageren met verbeterde zicht baarheid en controle over de beveiliging van uw resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt zouden blijven, en werkt met een uitgebreid ecosysteem van beveiligingsoplossingen.

Security Center gebruikt de Log Analytics agent om gegevens te verzamelen en op te slaan. Zie [gegevens verzameling in azure Security Center](security-center-enable-data-collection.md)voor gedetailleerde informatie.


## <a name="how-do-i-get-azure-security-center"></a>Hoe kan ik Azure Security Center ophalen?
Azure Security Center is ingeschakeld met uw Microsoft Azure-abonnement en is toegankelijk via de [Azure Portal](https://azure.microsoft.com/features/azure-portal/). Meld u aan bij [de portal](https://portal.azure.com), selecteer **Bladeren** en schuif naar **Security Center** om het te openen.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Welke Azure-resources worden bewaakt door Azure Security Center?
Azure Security Center bewaakt de volgende Azure-resources:

* Virtuele machines (Vm's) (inclusief [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Virtuele-machineschaalsets
* Partner oplossingen die zijn geïntegreerd met uw Azure-abonnement, zoals een Web Application Firewall op Vm's en op App Service Environment
* [De vele Azure PaaS services die worden vermeld in het product overzicht](features-paas.md)


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hoe kan ik de huidige beveiligings status van mijn Azure-resources weer geven?
Op de pagina **overzicht van Security Center** ziet u de algemene beveiligings postuur van uw omgeving, onderverdeeld op basis van compute-, netwerk-, opslag-& gegevens en toepassingen. Elk resource type heeft een indicator waarin de geïdentificeerde beveiligings problemen worden weer gegeven. Als u op elke tegel klikt, wordt een lijst met beveiligings problemen weer gegeven die zijn geïdentificeerd door Security Center, samen met een inventaris van de resources in uw abonnement.



## <a name="what-is-a-security-initiative"></a>Wat is een beveiligings initiatief?
Een Security Initiative definieert de set besturings elementen (beleids regels) die worden aanbevolen voor resources binnen het opgegeven abonnement. In Azure Security Center wijst u initiatieven toe voor uw Azure-abonnementen volgens de beveiligings vereisten van uw bedrijf en het type toepassingen of de gevoeligheid van de gegevens in elk abonnement.

Het beveiligings beleid dat is ingeschakeld in Azure Security Center aanbevelingen en controle van de beveiliging. Meer informatie in [Wat zijn beveiligings beleid, initiatieven en aanbevelingen?](security-policy-concept.md).


## <a name="who-can-modify-a-security-policy"></a>Wie kan een beveiligings beleid wijzigen?
Als u een beveiligings beleid wilt wijzigen, moet u een **beveiligings beheerder** of **eigenaar** van het abonnement zijn.

Zie [beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md)voor meer informatie over het configureren van een beveiligings beleid.


## <a name="what-is-a-security-recommendation"></a>Wat is een beveiligings aanbeveling?
Azure Security Center analyseert de beveiligingsstatus van uw Azure-bronnen. Als er mogelijke beveiligings problemen worden geïdentificeerd, worden er aanbevelingen gemaakt. De aanbevelingen begeleiden u bij het configureren van het vereiste besturings element. Een aantal voorbeelden:

* Inrichting van anti-malware voor het identificeren en verwijderen van schadelijke software
* [Netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md) en-regels voor het beheren van verkeer naar virtuele machines
* Het inrichten van een Web Application Firewall om te helpen bij het beschermen van aanvallen die gericht zijn op uw webtoepassingen
* Implementatie van ontbrekende systeemupdates
* Aanpassing van besturingssysteemconfiguraties die niet overeenkomen met de aanbevolen basislijnen

Hier worden alleen aanbevelingen weer gegeven die zijn ingeschakeld in het beveiligings beleid.


## <a name="what-triggers-a-security-alert"></a>Wat wordt een beveiligings waarschuwing geactiveerd?
Azure Security Center verzamelt, analyseert en integreert automatisch logboek gegevens van uw Azure-resources, het netwerk en partner oplossingen zoals antimalware en firewalls. Wanneer er dreigingen worden gedetecteerd, wordt een beveiligingswaarschuwing gemaakt. Voorbeelden zijn detectie van:

* Geïnfecteerde virtuele machines die communiceren met bekende schadelijke IP-adressen
* Geavanceerde malware gedetecteerd met Windows fout rapportage
* Beveiligingsaanvallen op virtuele machines
* Beveiligings waarschuwingen van geïntegreerde partner beveiligings oplossingen, zoals anti-malware of Web Application firewalls


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Wat is het verschil tussen bedreigingen die zijn gedetecteerd en gewaarschuwd op het micro soft Security Response Center versus Azure Security Center?
Het micro soft Security Response Center (MSRC) voert de selectie van beveiligings bewaking van het Azure-netwerk en de infra structuur uit en ontvangt bedreigings informatie en klachten van derden. Wanneer het MSRC van plan is dat klant gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij of het gebruik door de klant van Azure niet voldoet aan de voor waarden voor acceptabel gebruik, waarschuwt een beveiligings incident Manager de klant. Er treedt meestal een melding op door een e-mail te verzenden naar de beveiligings contactpersonen die zijn opgegeven in Azure Security Center of de eigenaar van het Azure-abonnement als er geen beveiligings contact is opgegeven.

Security Center is een Azure-service die voortdurend de Azure-omgeving van de klant bewaakt en analyses toepast om automatisch een breed scala aan mogelijk schadelijke activiteiten te detecteren. Deze detecties worden geoppereerd als beveiligings waarschuwingen in het dash board van Security Center.