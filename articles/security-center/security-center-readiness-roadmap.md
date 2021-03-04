---
title: Roadmap naar Azure Security Center-gereedheid | Microsoft Docs
description: Dit document bevat een roadmap naar gereedheid om het Azure Security Center te versterken.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: e4f905b7a08564eadb20d65d651c137ef3741376
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100318"
---
# <a name="azure-security-center-readiness-roadmap"></a>Azure Security Center gereedheids schema
Dit document bevat een roadmap naar gereedheid die u helpt aan de slag te gaan met Azure Security Center.

## <a name="understanding-security-center"></a>Security Center
Azure Security Center biedt geïntegreerd beveiligingsbeheer en geavanceerde bedreigingsbeveiliging voor werkbelastingen die worden uitgevoerd in Azure, on-premises en in andere clouds. 

Gebruik de volgende bronnen om aan de slag te gaan met Security Center.

Artikelen
- [Inleiding tot Azure Security Center](security-center-introduction.md)
- [Snelstartgids voor Azure Security Center](security-center-get-started.md)

Video's
- [Korte introductievideo](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [Overzicht van de Security Center-preventie-, detectie- en reactiemogelijkheden](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planning en bewerkingen

Als u optimaal wilt profiteren van Security Center, is het belangrijk te begrijpen hoe verschillende personen of teams binnen uw organisatie de service gaan gebruiken om te voldoen aan alle vereisten voor veilig gebruik, bewaking, bestuur en reactie op incidenten.

Gebruik de volgende bronnen als hulpmiddel tijdens de plannings- en bewerkingsprocessen.

- [Plannings- en bedieningsgids voor Azure Security Center](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-security-center"></a>Computers voorbereiden voor Security Center
Security Center detecteert automatisch alle Azure-abonnementen of-werk ruimten die niet zijn beveiligd door Azure Defender. Dit omvat Azure-abonnementen met Security Center gratis en werk ruimten waarvoor de beveiligings oplossing niet is ingeschakeld.

Gebruik de volgende bronnen om u te helpen tijdens de voorbereidingsprocessen.

- [Niet-Azure-computers onboarden](quickstart-onboard-machines.md)
- [Azure Security Center Hybrid - Overzicht](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Beperkende beveiligingsproblemen met Security Center
Security Center verzamelt, analyseert en integreert automatisch logboekgegevens van uw Azure-resources, het netwerk en verbonden partneroplossingen, zoals firewall- en eindpuntbeveiligingsoplossingen om werkelijke dreigingen te detecteren en fout-positieven te reduceren.

Gebruik de volgende bronnen om u te helpen met het beheer van beveiligingswaarschuwingen en de beveiliging van uw resources.

Artikelen    
- [Protecting your network in Azure Security Center](./security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)
- [Protecting Azure SQL service and data in Azure Security Center](./security-center-remediate-recommendations.md) (Azure SQL-service en -gegevens beveiligen in Azure Security Center)


Video    
- [Beperkende beveiligingsproblemen met Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Security Center voor reactie op incidenten
Om de kosten en schade te verminderen, is het belang rijk dat u een reactie plan voor incidenten hebt voordat een aanval plaatsvindt. U kunt Azure Security Center gebruiken in verschillende fasen tijdens een reactie op een incident.

Gebruik de volgende bronnen als u wilt weten hoe Security Center kan worden opgenomen in het incidentreactieproces.

Video's    
* [Azure Security Center in Incident Response](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response) (Azure Security Center in incidentreacties)
* [Respond quickly to threats with next-generation security operation, and investigation](https://youtu.be/e8iFCz5RM4g) (Snel op bedreigingen reageren met beveiligingsprocessen van een volgende generatie, en onderzoek)

Artikelen    
* [Beveiligingsincidenten afhandelen met Azure Security Center](./tutorial-security-incident.md)
* [Automatisering gebruiken om te reageren op Security Center triggers](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Geavanceerde cloudbeveiliging

Virtuele machines in Azure kunnen gebruikmaken van geavanceerde cloudverdedigingsmogelijkheden in Security Center. Deze mogelijkheden omvatten just-in-time-toegang voor virtuele machines (VM'S) en adaptieve toepassings besturings elementen.

Gebruik de volgende bronnen voor meer informatie over het gebruik van deze mogelijkheden in Security Center.

Video's    
* [Azure Security Center: Just-in-time-VM-toegang](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center - Adaptieve besturingselementen voor toepassingen](https://youtu.be/wWWekI1Y9ck)

Artikelen    
* [Toegang tot virtuele machines beheren met Just-in-time](./security-center-just-in-time.md)
* [Adaptieve toepassingsbesturingselementen in Azure Security Center](./security-center-adaptive-application.md)

## <a name="hands-on-activities"></a>Praktijksessies

* [Security Center-praktijksessies](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Web Application Firewall (WAF) recommendation playbook in Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff) (ASC-playbook: Servers beschermen met Web Application Firewall)
* [Azure Security Center Playbook: Beveiligingswaarschuwingen](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Aanvullende bronnen
* [Documentatiepagina van Security Center](./index.yml)
* [Documentatiepagina van Security Center REST API](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Veelgestelde vragen over Azure Security Center](./faq-general.md)
* [Pagina met prijzen van Security Center](https://azure.microsoft.com/pricing/details/security-center/)
* [Aanbevolen procedures voor identiteitsbeveiliging](../security/fundamentals/identity-management-best-practices.md)
* [Best practices voor netwerkbeveiliging](../security/fundamentals/network-best-practices.md)
* [PaaS-aanbevelingen](../security/fundamentals/paas-deployments.md)
* [Naleving](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Log Analytics-klanten kunnen nu Azure Security Center gebruiken om hun hybride Cloud werkbelastingen te beschermen](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)

## <a name="community-resources"></a>Communitybronnen

* [Feedback over Security Center](https://feedback.azure.com/forums/347535-azure-security-center)
* [Q&een pagina voor Security Center](/answers/topics/azure-security-center.html)