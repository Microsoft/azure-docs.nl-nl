---
title: Over Azure DevTest Labs | Microsoft Docs
description: Meer informatie over hoe u met DevTest Labs eenvoudig Azure virtual machines kunt maken, beheren en bewaken
ms.topic: article
ms.date: 06/20/2020
ms.openlocfilehash: b7e6493e5fb1c99dfa7962ed464dca3ebc6b0c7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480198"
---
# <a name="about-azure-devtest-labs"></a>Over Azure DevTest Labs
Met Azure DevTest Labs kunnen ontwikkel aars op teams op efficiënte wijze zelf virtuele machines (Vm's) en PaaS-bronnen beheren zonder te hoeven wachten op goed keuringen.

DevTest Labs maakt Labs die bestaat uit vooraf geconfigureerde bases of Azure Resource Manager sjablonen. Deze beschikken over alle benodigde hulpprogram ma's en software die u kunt gebruiken om omgevingen te maken. U kunt binnen een paar minuten omgevingen maken, in plaats van uren of dagen.

Met behulp van DevTest Labs kunt u de nieuwste versies van uw toepassingen testen door de volgende taken uit te voeren:

- Richt snel Windows-en Linux-omgevingen in met herbruikbare sjablonen en artefacten.
- Integreer uw implementatiepijplijn eenvoudig met DevTest Labs om op aanvraag omgevingen in te richten.
- Verg root uw belasting test door meerdere test agenten in te richten en vooraf ingerichte omgevingen te maken voor training en demo's.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Functies
DevTest Labs biedt de volgende mogelijkheden voor ontwikkel aars die werken met Vm's:

- Maak snel Vm's door minder dan vijf eenvoudige stappen te volgen.
- Kies uit een lijst met VM-basissen die zijn geconfigureerd, goedgekeurd en geautoriseerd door de team lead of een centrale IT-afdeling.
- Maak Vm's van vooraf gemaakte aangepaste installatie kopieën waarbij alle software en hulpprogram ma's al zijn geïnstalleerd. 
- Maak Vm's van formules die in wezen aangepaste installatie kopieën zijn gecombineerd met de meest recente builds van de software die wordt geïnstalleerd wanneer de virtuele machines worden gemaakt. 
- Installatie artefacten die zijn geïmplementeerd op Vm's, nadat deze zijn ingericht.
- Automatisch afsluiten instellen en planningen automatisch starten op Vm's.
- Claim een vooraf gemaakte VM zonder het aanmaak proces te door lopen.

DevTest Labs biedt de volgende mogelijkheden voor ontwikkel aars die werken met PaaS-omgevingen:

- Gebruik Resource Manager om snel PaaS-omgevingen te maken door minder dan drie eenvoudige stappen te volgen.
- U kunt kiezen uit een lijst met Resource Manager-sjablonen die zijn geconfigureerd, en die worden geautoriseerd door de team lead of een centrale IT-afdeling.
- Een lege resource groep (sandbox) in te stellen met behulp van een resource manager-sjabloon voor het verkennen van Azure in de context van een lab.

Met DevTest Labs is het ook mogelijk om afval te beheren, de kosten van resources te optimaliseren en binnen budgetten te blijven door de volgende taken uit te voeren:  

- Automatische afsluiting en planningen voor automatisch starten van Vm's instellen.
- Beleid instellen op het aantal Vm's dat gebruikers kunnen maken.
- Beleids regels instellen voor de grootte en Galerie-installatie kopieën van de virtuele machines waaruit gebruikers kiezen.
- Het bijhouden van kosten en het instellen van doelen op Labs.
- Krijg een melding over de hoge geschatte kosten voor Labs, zodat u de nodige acties kunt ondernemen.

DevTest Labs biedt de volgende voor delen voor het maken, configureren en beheren van omgevingen in de Cloud.

## <a name="cost-control-and-governance"></a>Kosten beheer en governance
Met DevTest Labs kunt u gemakkelijker kosten beheren door de volgende taken uit te voeren:

- [Stel beleids regels in voor uw Labs](devtest-lab-set-lab-policy.md), zoals het aantal vm's per gebruiker of per Lab. 
- [Beleids regels maken om vm's automatisch af te sluiten](devtest-lab-set-lab-policy.md) en te starten.
- Houd de kosten bij van Vm's en PaaS bronnen in Labs om binnen [het budget](devtest-lab-configure-cost-management.md)te blijven.
- Blijf in de context van uw Labs, zodat u geen resources buiten hen kunt zetten.

## <a name="quickly-get-to-ready-to-test"></a>Snel aan de slag gaan met testen
Met DevTest Labs kunt u vooraf ingerichte omgevingen maken die zijn voorzien van alles wat uw team nodig heeft om toepassingen te ontwikkelen en testen. U hoeft alleen [de omgevingen te claimen](devtest-lab-add-claimable-vm.md) waar de laatste goede versie van uw toepassing is geïnstalleerd en aan de slag te gaan. Of gebruik containers voor het maken van een snellere, slanke omgeving.

## <a name="create-once-use-everywhere"></a>Eenmaal maken, overal gebruiken
Vastleggen en delen van PaaS- [omgevings sjablonen](devtest-lab-create-environment-from-arm.md) en- [artefacten](add-artifact-repository.md) binnen uw team of organisatie, allemaal in broncode beheer, om eenvoudig ontwikkel-en test omgevingen te maken.

## <a name="worry-free-self-service"></a>Gratis Self-service
Met DevTest Labs kunnen ontwikkel aars en testers snel en eenvoudig [IaaS vm's](devtest-lab-add-vm.md) en [PaaS bronnen](devtest-lab-create-environment-from-arm.md) maken met behulp van een set vooraf geconfigureerde resources.

## <a name="use-iaas-and-paas-resources"></a>IaaS-en PaaS-resources gebruiken 
Ontwikkel aars kunnen ook PaaS-resources, zoals Azure Service Fabric-clusters, de Web Apps functie van Azure App Service en share point-Farms, met behulp van Resource Manager-sjablonen. Om aan de slag te gaan met PaaS in Labs, gebruikt u de sjablonen uit de [opslag plaats voor open bare omgeving](devtest-lab-configure-use-public-environments.md) of [verbindt u het lab met uw eigen Git-opslag plaats](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). U kunt ook de kosten voor deze resources bijhouden om binnen uw budget te blijven.

## <a name="integrate-with-your-existing-toolchain"></a>Integreren met uw bestaande hulpprogramma keten
Gebruik vooraf gemaakte invoeg toepassingen of de API om ontwikkel-en test omgevingen rechtstreeks vanuit uw favoriete [CI-tool (continue integratie)](devtest-lab-integrate-ci-cd.md), Integrated Development Environment (IDE) of een automatische release-pijp lijn in te richten. U kunt ook het uitgebreide opdracht regel programma gebruiken.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- Zie [DevTest Labs-concepten](devtest-lab-concepts.md)voor meer informatie over DevTest Labs.
- Zie [zelf studie: een Lab instellen met behulp van Azure DevTest Labs](tutorial-create-custom-lab.md)voor een overzicht met stapsgewijze instructies.