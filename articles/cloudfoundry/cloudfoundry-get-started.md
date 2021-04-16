---
title: Aan de slag met Cloud Foundry op Microsoft Azure
description: OSS of Pivotal Cloud Foundry uitvoeren op Microsoft Azure
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 2f80f07cad0e82ee35fe71223e1282ea24f791bb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530923"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry op Azure

Cloud Foundry is een open-source platform-as-a-service (PaaS) voor het bouwen, implementeren en gebruiken van 12-factor toepassingen die in verschillende talen en frameworks zijn ontwikkeld. In dit document worden de opties beschreven voor het uitvoeren Cloud Foundry in Azure en hoe u aan de slag kunt gaan.

## <a name="cloud-foundry-offerings"></a>Cloud Foundry-aanbiedingen

Er zijn twee soorten Cloud Foundry beschikbaar om te worden uitgevoerd in Azure: open-source Cloud Foundry (OSS CF) en Pivotal Cloud Foundry (PCF). OSS CF is een volledig [opensource-versie](https://github.com/cloudfoundry) van Cloud Foundry beheerd door de Cloud Foundry Foundation. Pivotal Cloud Foundry is een zakelijke distributie van Cloud Foundry van Pivotal Software Inc. We kijken naar enkele van de verschillen tussen de twee aanbiedingen.

### <a name="open-source-cloud-foundry"></a>Opensource-Cloud Foundry

U kunt OSS Cloud Foundry implementeren in Azure door eerst een BOSH-directeur te implementeren en vervolgens Cloud Foundry te implementeren volgens de instructies [op GitHub.](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md) Zie de documentatie van de Cloud Foundry [](https://docs.cloudfoundry.org/) Foundation voor meer informatie over het gebruik van OSS CF.

Microsoft biedt best-effort ondersteuning voor OSS CF via de volgende communitykanalen:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>het kanaal bosh-azure-cpi [op Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [cf-bosh-adressenlijst](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- GitHub-problemen voor de [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) en [servicebroker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Het ondersteuningsniveau voor uw Azure-resources, zoals de virtuele machines waarop u Cloud Foundry, is gebaseerd op uw ondersteuning voor Azure overeenkomst. Ondersteuning van de best effort community is alleen van toepassing op Cloud Foundry specifieke onderdelen.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

Pivotal Cloud Foundry hetzelfde kernplatform als de OSS-distributie, samen met een set eigen beheerhulpprogramma's en bedrijfsondersteuning. Als u PCF wilt uitvoeren in Azure, moet u een licentie verkrijgen bij Pivotal. De PCF-aanbieding van de Azure Marketplace bevat een licentie voor een proefversie van 90 dagen.

De hulpprogramma's omvatten [Pivotal Operations Manager,](https://docs.pivotal.io/ops-manager/2-10/install/)een webtoepassing die de implementatie en het beheer van een Cloud Foundry-basis vereenvoudigt, en [Pivotal Apps Manager,](https://docs.pivotal.io/pivotalcf/console/)een webtoepassing voor het beheren van gebruikers en toepassingen.

Naast de ondersteuningskanalen die hierboven worden vermeld voor OSS CF, geeft een PCF-licentie u de rol om contact op te nemen met Pivotal voor ondersteuning. Microsoft en Pivotal hebben ook ondersteuningswerkstromen ingeschakeld waarmee u contact kunt opnemen met een van beide partijen voor hulp en uw verzoek op de juiste wijze kunt laten doorstromen, afhankelijk van waar het probleem ligt.

## <a name="azure-service-broker"></a>Azure Service Broker

Cloud Foundry wordt de [twaalf-factor app-methodologie](https://12factor.net/) aangemoedigd, die een schone scheiding van staatloze toepassingsprocessen en stateful back-service bevordert. [Servicebrokers](https://docs.cloudfoundry.org/services/api.html) bieden een consistente manier om back-services in terichten en te binden aan toepassingen. De [Azure-servicebroker](https://github.com/Azure/meta-azure-service-broker) biedt enkele van de belangrijkste Azure-services via dit kanaal, waaronder Azure-opslag en Azure SQL.

Als u Pivotal Cloud Foundry, is de servicebroker ook beschikbaar [als tegel](https://docs.pivotal.io/azure-sb/installing.html) van Pivotal Network.

## <a name="related-resources"></a>Gerelateerde resources

### <a name="azure-devops-services-plugin"></a>Azure DevOps Services-invoegservice

Cloud Foundry is zeer geschikt voor flexibele softwareontwikkeling, waaronder het gebruik van continue integratie (CI) en continue levering (CD). Als u Azure DevOps Services gebruikt om uw projecten te beheren en u een CI/CD-pijplijn wilt instellen die is gericht op Cloud Foundry, kunt u de [Azure DevOps Services Cloud Foundry build-extensie](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension)gebruiken. Met de invoeging kunt u eenvoudig implementaties voor Cloud Foundry configureren en automatiseren, ongeacht of deze worden uitgevoerd in Azure of een andere omgeving.

## <a name="next-steps"></a>Volgende stappen

- [Pivotal Cloud Foundry implementeren vanuit de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)
- [Een app implementeren Cloud Foundry in Azure](./cloudfoundry-deploy-your-first-app.md)
