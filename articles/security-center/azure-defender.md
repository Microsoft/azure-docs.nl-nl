---
title: Overzicht van Azure Defender en de beschikbare abonnementen
description: Lees meer informatie over de abonnementen, beschermingen en waarschuwingen van Azure Defender. Schakel Azure Defender vervolgens in op uw abonnementen voor geavanceerde beveiliging.
author: memildin
ms.author: memildin
ms.date: 9/30/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 8bf3959651048a7a568df1d94aa07aa206663d50
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750982"
---
# <a name="introduction-to-azure-defender"></a>Inleiding tot Azure Defender

De twee grote pijlers van cloudbeveiliging zijn verwerkt in de functies van Azure Security Center:

- **Beheer van cloudbeveiligingspostuur (CSPM)** : Security Center is **gratis** beschikbaar voor alle Azure-gebruikers. De gratis ervaring omvat CSPM-functies, zoals beveiligingsscore, de detectie van onjuiste beveiligingsconfiguraties op uw Azure-machines, assetinventarisatie en meer. Gebruik deze CSPM-functies om uw hybride cloudpostuur te versterken en om de naleving van het ingebouwde beleid bij te houden.

- **Beveiliging van cloudworkloads (CWP)** : het Cloud Workload Protection Platform (CWPP), **Azure Defender**, dat geïntegreerd is in Security Center, zorgt voor geavanceerde, intelligente beveiliging van uw Azure- en hybride resources en workloads. Door Azure Defender in te schakelen, beschikt u over diverse aanvullende beveiligingsfuncties, zoals beschreven op deze pagina. Naast het ingebouwde beleid kunt u, wanneer u een Azure Defender-abonnement hebt ingeschakeld, aangepaste beleidsregels en initiatieven toevoegen. U kunt wettelijke standaarden, zoals NIST en Azure CIS, evenals de Azure Security-benchmark toevoegen voor een echte aangepaste weergave van uw nalevingsinspanningen.

Het Azure Defender-dashboard in Security Center biedt inzicht in en controle over de CWP-functies voor uw omgeving:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Een voorbeeld van het Azure Defender-dashboard" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Welke brontypen kan Azure Defender beveiligen?

Azure Defender biedt beveiligingswaarschuwingen en geavanceerde bescherming tegen dreigingen voor virtuele machines, SQL databases, containers, webtoepassingen, uw netwerk en meer.

Als u Azure Defender inschakelt in het gedeelte **Prijzen en instellingen** van Azure Security Center, worden de volgende Defender-abonnementen allemaal tegelijkertijd ingeschakeld en kunt u gebruikmaken van een totaaloplossing voor de bescherming van de berekenings-, gegevens- en servicelagen in uw omgeving:

- [Azure Defender voor servers](defender-for-servers-introduction.md)
- [Azure Defender voor App Service](defender-for-app-service-introduction.md)
- [Azure Defender voor Storage](defender-for-storage-introduction.md)
- [Azure Defender voor SQL](defender-for-sql-introduction.md)
- [Azure Defender voor Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender voor containerregisters](defender-for-container-registries-introduction.md)
- [Azure Defender voor Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender voor Resource Manager](defender-for-resource-manager-introduction.md)
- [Azure Defender voor DNS](defender-for-dns-introduction.md)

Elk van deze abonnementen wordt afzonderlijk beschreven in de documentatie voor Security Center.

> [!TIP]
> Azure Defender voor IoT (preview) is een afzonderlijk product. U vindt alle details in [Kennismaking met Azure Defender voor IoT (preview)](../defender-for-iot/overview.md). 

## <a name="hybrid-cloud-protection"></a>Bescherming voor hybride cloud

Met Azure Defender kunt u niet alleen uw Azure-omgeving, maar ook uw hybride cloudomgeving beveiligen:

- Bescherm niet-Azure-servers
- Bescherm virtuele machines in andere cloudomgevingen (zoals AWS en GCP)

U kunt gebruikmaken van aangepaste bedreigingsinformatie en op prioriteit gerangschikte waarschuwingen afgestemd op uw specifieke omgeving. Zo kunt u zich richten op de belangrijkste zaken.

Implementeer [Azure Arc](https://azure.microsoft.com/services/azure-arc/) en schakel Azure Defender in om de bescherming uit te breiden naar virtuele machines die zich in andere clouds of on-premises bevinden. Azure Arc voor servers is een gratis service, maar services die worden gebruikt op servers met Arc-functionaliteit, zoals Azure Defender, worden in rekening gebracht volgens de prijzen voor die service. Zie [Andere computers dan Azure-computers toevoegen met Azure Arc](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc) voor meer informatie.

> [!TIP]
> De systeemeigen connector voor AWS zorgt dat de implementatie transparant voor u wordt afgehandeld. Meer informatie vindt u in [Uw AWS-accounts verbinden met Azure Security Center](quickstart-onboard-aws.md).



## <a name="azure-defender-security-alerts"></a>Beveiligingswaarschuwingen in Azure Defender 

Wanneer Azure Defender een bedreiging detecteert in een gedeelte van uw omgeving, wordt een beveiligingswaarschuwing gegenereerd. Deze waarschuwingen bevatten details over de relevante bronnen, voorgestelde stappen voor herstel en soms ook de optie om een logische app te activeren.

U kunt een waarschuwing altijd exporteren, ongeacht of deze is gegenereerd door Security Center of door Security Center is ontvangen van een geïntegreerd beveiligingsproduct. Als u waarschuwingen wilt exporteren naar Azure Sentinel, een extern SIEM of een ander extern hulpprogramma, volgt u de instructies in [Waarschuwingen streamen naar een SIEM-, SOAR- of IT Service Management-oplossing](export-to-siem.md).

> [!NOTE]
> Hoe snel een waarschuwing wordt weergegeven, hangt af van de bron van de waarschuwing. Waarschuwingen waarvoor analyse van netwerkverkeer is vereist, worden bijvoorbeeld later weergegeven dan waarschuwingen met betrekking tot verdachte processen op virtuele machines.


## <a name="azure-defender-advanced-protection-capabilities"></a>Geavanceerde beschermingsfunctionaliteit van Azure Defender

Azure Defender maakt gebruik van geavanceerde analyses voor aanbevelingen op maat voor uw bronnen. 

U kunt bijvoorbeeld het beheer van de poorten van virtuele machines beveiligen met Just-In-Time-toegang of met adaptieve toepassingsregelaars acceptatielijsten maken voor welke apps wel en niet op uw machines mogen worden uitgevoerd. 

Gebruik de tegels voor geavanceerde bescherming in het Azure Defender-dashboard om deze beschermingen te bekijken en configureren. 

## <a name="vulnerability-assessment-and-management"></a>Evaluatie en beheer van beveiligingsproblemen

Azure Defender omvat scannen op beveiligingsproblemen voor uw virtuele machines en containerregisters. Hiervoor betaalt u geen extra kosten. De scanners werken op basis van Qualys, maar u hebt geen Qualys-licentie of Qualys-account nodig. De scans worden naadloos uitgevoerd in Security Center. 

Bekijk de bevindingen van de beveiligingsscanners en voer voor elke bevinding een relevante actie uit in Security Center. Hiermee is centralisering van alle cloudbeveiligingsactiviteiten in Security Center een stap dichterbij gekomen.

Op de volgende pagina's vindt u meer informatie:

- [De geïntegreerde oplossing van Security Center voor de beoordeling van beveiligingsproblemen met virtuele Azure-machines](deploy-vulnerability-assessment-vm.md)
- [Beveiligingsproblemen met installatiekopieën in Azure-containerregisters identificeren](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Volgende stappen

In dit artikel bent u meer te weten gekomen over de voordelen van Azure Defender. 

> [!div class="nextstepaction"]
> [Azure Defender inschakelen](security-center-pricing.md)