---
title: Vertrouwelijke containers op Azure Kubernetes service (AKS)
description: Meer informatie over niet-gewijzigde container ondersteuning op vertrouwelijke containers.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 2/11/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: cf62e6c4e54cc7e6488b26d4251ecb813d62e7ea
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564302"
---
# <a name="confidential-containers"></a>Vertrouwelijke containers

## <a name="overview"></a>Overzicht

Ontwikkel aars in staat stellen een **bestaande docker-toepassing (nieuw of bestaand)** te gebruiken en deze veilig uit te voeren op Azure Kubernetes service (AKS) via ondersteuning voor vertrouwelijke computer knooppunten.

Vertrouwelijke containers beschermen:

- gegevens integriteit 
- vertrouwelijkheid van gegevens
- code-integriteit
- container code beveiliging via versleuteling
- op hardware gebaseerde garanties
- uitvoeren van bestaande apps toestaan
- de hoofdmap van de vertrouwens relatie van de hardware maken
- host Administrator, Kubernetes Administrator, Hyper Visor verwijderen uit de grens van de vertrouwens relatie

Een op hardware gebaseerde Trusted Execution Environment (TEE) is een belang rijk onderdeel dat wordt gebruikt om sterke garanties te bieden via hardware-en software metingen van TCB-onderdelen (Trusted Computing Base). Verificatie van deze metingen helpt bij het valideren van de verwachte berekening en het verifiëren van het onrecht matig wijzigen van de container-apps.

Vertrouwelijke containers bieden ondersteuning voor aangepaste toepassingen die zijn ontwikkeld met **python, Java, node js, enz. of ingepakte container toepassingen zoals NGINX, redis cache, MemCache**, enzovoort, die ongewijzigd kunnen worden uitgevoerd op AKS met vertrouwelijke reken knooppunten.

Vertrouwelijke containers zijn het snelste pad naar de container vertrouwelijkheid en vereisen alleen herverpakking van de bestaande docker-container toepassingen en vereisen geen wijzigingen in de toepassings code. Vertrouwelijke containers zijn docker-container toepassingen die zijn verpakt om in een TEE te worden uitgevoerd. Sommige vertrouwelijke container inschakelen bieden ook container versleuteling waarmee u de container code kunt beschermen tijdens de opslag en het Trans Port en tijdens het koppelen in de host. Met container versleuteling kunt u het code/model dat in de container is verpakt, verder beveiligen met de ontsleutelings sleutel die aan het TEE is gekoppeld.

Hieronder ziet u het proces voor het ontwikkelen van vertrouwelijke containers voor de implementatie van ![ de vertrouwelijke container.](./media/confidential-containers/how-to-confidential-container.png)

## <a name="confidential-container-enablers"></a>Vertrouwelijke container inschakelen
Als u een bestaande docker-container wilt uitvoeren, is een SGX-software vereist zodat de toepassing aanroepen speciale CPU-instructies kunnen gebruiken die beschikbaar worden gesteld om de bijsluitings surface area te verlagen en geen afhankelijkheid van het gast besturingssysteem te krijgen. Wanneer de containers zijn ingepakt met de SGX-runtime-software, worden deze automatisch gestart in de beveiligde enclaves, waardoor het gast besturingssysteem, het hostbesturingssysteem of de Hyper Visor uit de grens van de vertrouwens relatie wordt verwijderd. Deze geïsoleerde uitvoering in een knoop punt (virtuele machine) met in geheugen gegevens versleuteling die wordt ondersteund door de hardware vermindert de algehele Opper vlakte van kwets bare gebieden en vermindert de kwets baarheid met de lagen van het besturings systeem of de Hyper Visor.

Vertrouwelijke containers worden volledig ondersteund op AKS en ingeschakeld via Azure-partners en OSS-projecten (Open-Source software). Ontwikkel aars kunnen software providers kiezen op basis van de functies, integratie met Azure-Services en hulpprogramma ondersteuning.

## <a name="partner-enablers"></a>Partner-Ontwikkelers
> [!NOTE]
> De onderstaande oplossingen worden aangeboden via Azure-partners en kunnen licentie kosten in rekening worden gebracht. Controleer de software voorwaarden van de partner onafhankelijk. 

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) biedt software voor SGX-platforms waarmee u ongewijzigde containers kunt uitvoeren op AKS. Meer informatie over de functionaliteit en Bekijk [hier](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)de voorbeeld toepassingen.

Ga [hier](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp) aan de slag met een voor beeld-redis cache en een aangepaste python-toepassing

![Anjuna-proces](./media/confidential-containers/anjuna-process-flow.png)

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) biedt ontwikkel aars een keuze uit een portal en een CLI-ervaring om hun toepassingen in containers te plaatsen en deze te converteren naar voor SGX geschikte, vertrouwelijke containers zonder dat ze de toepassing hoeven te wijzigen of opnieuw te compileren. Fortanix biedt de flexibiliteit om de breedste set toepassingen uit te voeren en te beheren, waaronder bestaande toepassingen, nieuwe enclave-systeem eigen toepassingen en vooraf verpakte toepassingen. Gebruikers kunnen beginnen met de gebruikers interface van de [vertrouwelijke computer Manager](https://em.fortanix.com/) of [rest-api's](https://www.fortanix.com/api/em/) voor het maken van vertrouwelijke containers door de [snel starten](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) gids voor de Azure Kubernetes-service te volgen.

![Fortanix-implementatie proces](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

[Scone](https://scontain.com/index.html?lang=en) biedt ondersteuning voor beveiligings beleid dat certificaten, sleutels en geheimen kan genereren, en ervoor zorgt dat ze alleen zichtbaar zijn voor de certificering van een toepassing. Op deze manier worden de services van een toepassing automatisch met behulp van TLS verklaard, zonder dat u de toepassingen noch TLS hoeft te wijzigen. Dit wordt uitgelegd aan de hand van een eenvoudige fles toepassing: https://sconedocs.github.io/flask_demo/  

SCONE kan bestaande binaire bestanden converteren naar toepassingen die worden uitgevoerd binnen enclaves zonder dat de toepassing hoeft te worden gewijzigd of dat de toepassing opnieuw moet worden gecompileerd. SCONE beschermt ook geïnterpreteerde talen als python door zowel gegevens bestanden als Python-code bestanden te **versleutelen** . Met de hulp van een SCONE-beveiligings beleid kan een van de versleutelde bestanden worden beveiligd tegen onbevoegde toegang, wijzigingen en terugdraai acties. Hoe ' sconify ' een bestaande python-toepassing wordt [hier](https://sconedocs.github.io/sconify_image/) beschreven

![Scontain-stroom](./media/confidential-containers/scone-workflow.png)

Scone-implementaties op vertrouwelijke computing knooppunten met AKS worden volledig ondersteund en geïntegreerd met andere Azure-Services. Ga hier aan de slag met een voorbeeld toepassing https://sconedocs.github.io/aks/


## <a name="oss-enablers"></a>OSS-Schakelers 
> [!NOTE]
> De onderstaande oplossingen worden aangeboden via Open-Source projecten en zijn niet rechtstreeks gekoppeld aan Azure vertrouwelijk Computing (ACC) of micro soft.  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/) is een licht gewicht gast besturingssysteem, ontworpen om één Linux-toepassing met minimale vereisten voor de host uit te voeren. Graphene kan toepassingen uitvoeren in een geïsoleerde omgeving met voor delen die vergelijkbaar zijn met het uitvoeren van een complete versie van het besturings systeem en heeft goede hulp middelen voor het converteren van bestaande docker-container toepassing naar graphene afgeschermde containers.

Ga [hier](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks) aan de slag met een voorbeeld toepassing en-implementatie op AKS

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) is een geheugen veilige, multi-process bibliotheek besturingssysteem (LibOS) voor Intel SGX. Hierdoor kunnen oudere toepassingen worden uitgevoerd op SGX met weinig wijzigingen in de bron code. Occlum beveiligt de vertrouwelijkheid van werk belastingen van gebruikers, terwijl een eenvoudige lift en verschuiving kan worden uitgevoerd naar bestaande docker-toepassingen.

Occlum biedt ondersteuning voor AKS-implementaties. Volg de instructies voor de implementatie van de verschillende voor beelden [van apps](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)


## <a name="confidential-containers-demo"></a>Demo van vertrouwelijke containers
Bekijk de demo van de vertrouwelijke gezondheids zorg met vertrouwelijke containers. Voor beeld is [hier](/azure/architecture/example-scenario/confidential/healthcare-inference)beschikbaar. 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Contact opnemen

Hebt u vragen over uw implementatie of wilt u een enabler worden? Een e-mail verzenden naar het product team **acconaks@microsoft.com**

## <a name="reference-links"></a>Referentie koppelingen

[Microsoft Azure Attestation](../attestation/overview.md)

[DCsv2 Virtual Machines](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)