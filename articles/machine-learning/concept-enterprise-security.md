---
title: Enterprisebeveiliging en governance
titleSuffix: Azure Machine Learning
description: 'Gebruik veilig Azure Machine Learning: verificatie, autorisatie, netwerk beveiliging, gegevens versleuteling en bewaking.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/20/2020
ms.openlocfilehash: a079504872eaf3840416a99e784c4d33a6828b0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94992026"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Enter prise Security en governance voor Azure Machine Learning

In dit artikel vindt u informatie over de beveiligings-en beheer functies die beschikbaar zijn voor Azure Machine Learning. Deze functies zijn handig voor beheerders, DevOps en MLOps die een beveiligde configuratie willen maken die compatibel is met uw bedrijfs beleid. Met Azure Machine Learning en het Azure-platform kunt u het volgende doen:

* Toegang tot resources en bewerkingen beperken op basis van gebruikers account of groepen
* Binnenkomende en uitgaande netwerk communicatie beperken
* Gegevens versleutelen tijdens de overdracht en op rest
* Controleren op beveiligings problemen
* Configuratie beleid Toep assen en controleren

## <a name="restrict-access-to-resources-and-operations"></a>Beperk de toegang tot resources en bewerkingen

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) is de identiteits serviceprovider voor Azure machine learning. Hiermee kunt u de beveiligings objecten (gebruiker, groep, Service-Principal en beheerde identiteit) maken en beheren die worden gebruikt voor _verificatie_ bij Azure-resources. Multi-factor Authentication wordt ondersteund als Azure AD is geconfigureerd om het te gebruiken.

Dit is het verificatie proces voor Azure Machine Learning met behulp van multi-factor Authentication in azure AD:

1. De client meldt zich aan bij Azure AD en ontvangt een Azure Resource Manager token.
1. De client geeft het token aan Azure Resource Manager en alle Azure Machine Learning.
1. Azure Machine Learning levert een Machine Learning service token aan het gebruikers Compute-doel (bijvoorbeeld Azure Machine Learning Reken cluster). Dit token wordt gebruikt door het gebruikers Compute-doel om terug te bellen naar de Machine Learning-service nadat de uitvoering is voltooid. De scope is beperkt tot de werk ruimte.

[![Verificatie in Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Aan elke werk ruimte is een door het systeem toegewezen [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) gekoppeld die dezelfde naam heeft als de werk ruimte. Deze beheerde identiteit wordt gebruikt om veilig toegang te krijgen tot resources die worden gebruikt door de werk ruimte. Het heeft de volgende Azure RBAC-machtigingen op gekoppelde resources:

| Resource | Machtigingen |
| ----- | ----- |
| Werkruimte | Inzender |
| Storage-account | Inzender voor Storage Blob-gegevens |
| Key Vault | Toegang tot alle sleutels, geheimen, certificaten |
| Azure Container Registry | Inzender |
| Resource groep die de werk ruimte bevat | Inzender |
| De resource groep die de sleutel kluis bevat (als deze anders is dan de naam die de werk ruimte bevat) | Inzender |

Het is niet raadzaam om beheerders de toegang tot de beheerde identiteit in te trekken voor de resources die in de voor gaande tabel worden vermeld. U kunt de toegang herstellen met behulp van de bewerking voor het [opnieuw synchroniseren van sleutels](how-to-change-storage-access-key.md).

Azure Machine Learning maakt ook een extra toepassing (de naam begint met `aml-` of `Microsoft-AzureML-Support-App-` ) met toegang op Inzender niveau in uw abonnement voor elke werkruimte regio. Als u bijvoorbeeld één werk ruimte hebt in VS-Oost en één in Europa-noord in hetzelfde abonnement, ziet u twee van deze toepassingen. Met deze toepassingen kunt u Azure Machine Learning helpen bij het beheren van reken resources.

U kunt ook uw eigen beheerde identiteiten configureren voor gebruik met Azure Virtual Machines en Azure Machine Learning Compute-Cluster. Met een virtuele machine kan de beheerde identiteit worden gebruikt voor toegang tot uw werk ruimte vanuit de SDK, in plaats van het Azure AD-account van de individuele gebruiker. Met een berekenings cluster wordt de beheerde identiteit gebruikt voor toegang tot bronnen zoals beveiligde gegevens opslag die de gebruiker die de trainings taak uitvoert, geen toegang heeft tot. Zie [verificatie voor Azure machine learning-werk ruimte](how-to-setup-authentication.md)voor meer informatie.

> [!TIP]
> Er zijn enkele uitzonde ringen op het gebruik van Azure AD en Azure RBAC binnen Azure Machine Learning:
> * U kunt optioneel __SSH__ -toegang inschakelen voor het berekenen van resources, zoals Azure machine learning reken instantie en reken cluster. SSH-toegang is gebaseerd op paren van open bare/persoonlijke sleutels, niet voor Azure AD. SSH-toegang wordt niet geregeld door Azure RBAC.
> * U kunt zich verifiëren bij modellen die zijn geïmplementeerd als webservices (eind punten afbakenen) met behulp van __sleutel__ -of op __tokens__ gebaseerde verificatie. Sleutels zijn statische teken reeksen, terwijl tokens worden opgehaald met behulp van een Azure AD-beveiligings object. Zie [verificatie configureren voor modellen die zijn geïmplementeerd als een webservice](how-to-authenticate-web-service.md)voor meer informatie.

Raadpleeg voor meer informatie de volgende artikelen:
* [Verificatie voor Azure Machine Learning werk ruimte](how-to-setup-authentication.md)
* [Toegang tot Azure Machine Learning beheren](how-to-assign-roles.md)
* [Verbinding maken met Storage services](how-to-access-data.md)
* [Azure Key Vault voor geheimen gebruiken bij het trainen](how-to-use-secrets-in-runs.md)
* [Met Azure AD beheerde identiteit gebruiken met Azure Machine Learning](how-to-use-managed-identities.md)
* [Met Azure AD beheerde identiteit gebruiken met uw webservice](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>Netwerk beveiliging en-isolatie

Als u de netwerk toegang tot Azure Machine Learning bronnen wilt beperken, kunt u [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md)gebruiken. Met VNets kunt u netwerk omgevingen maken die gedeeltelijk of volledig zijn geïsoleerd van het open bare Internet. Dit vermindert de kwets baarheid voor uw oplossing en de kans op gegevens exfiltration.

U kunt een VPN-gateway (virtueel particulier netwerk) gebruiken om afzonderlijke clients of uw eigen netwerk te verbinden met het VNet

De Azure Machine Learning-werk ruimte kan gebruikmaken van een [persoonlijke Azure-koppeling](../private-link/private-link-overview.md) om een persoonlijk eind punt achter het VNet te maken. Dit biedt een reeks privé-IP-adressen die kunnen worden gebruikt voor toegang tot de werk ruimte vanuit het VNet. Sommige van de services waarvan Azure Machine Learning afhankelijk is, kunnen ook gebruikmaken van een persoonlijke Azure-koppeling, maar bepaalde netwerk beveiligings groepen of door de gebruiker gedefinieerde route ring zijn afhankelijk.

Raadpleeg de volgende documenten voor meer informatie:

* [Overzicht van virtuele netwerk isolatie en privacy](how-to-network-security-overview.md)
* [Beveiligde werkruimteresources](how-to-secure-workspace-vnet.md)
* [Beveiligde trainingsomgeving](how-to-secure-training-vnet.md)
* [Veilige interferentie omgeving](how-to-secure-inferencing-vnet.md)
* [Studio gebruiken in een beveiligd virtueel netwerk](how-to-enable-studio-virtual-network.md)
* [Aangepaste DNS gebruiken](how-to-custom-dns.md)
* [Firewall configureren](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Gegevensversleuteling

Azure Machine Learning gebruikt diverse reken bronnen en gegevens archieven op het Azure-platform. Zie [gegevens versleuteling met Azure machine learning](concept-data-encryption.md)voor meer informatie over hoe elk van deze gegevens versleuteling in rust en onderweg wordt ondersteund.

Wanneer u modellen als webservices implementeert, kunt u TLS (trans port Layer Security) inschakelen voor het versleutelen van gegevens die onderweg zijn. Zie [Configure a Secure Web Service](how-to-secure-web-service.md)(Engelstalig) voor meer informatie.

## <a name="vulnerability-scanning"></a>Scannen op beveiligingsproblemen

[Azure Security Center](../security-center/security-center-introduction.md) biedt geïntegreerd beveiligingsbeheer en geavanceerde bedreigingsbeveiliging voor verschillende hybride cloudworkloads. Voor Azure machine learning moet u het scannen van uw [Azure container Registry](../container-registry/container-registry-intro.md) resource en Azure Kubernetes-service resources inschakelen. Zie [Azure container Registry Image scanning by Security Center](../security-center/defender-for-container-registries-introduction.md) en [Azure Kubernetes Services Integration with Security Center](../security-center/defender-for-kubernetes-introduction.md)(Engelstalig) voor meer informatie.

## <a name="audit-and-manage-compliance"></a>Naleving controleren en beheren

[Azure Policy](../governance/policy/index.yml) is een beheer programma waarmee u ervoor kunt zorgen dat Azure-resources voldoen aan uw beleid. U kunt beleids regels instellen om specifieke configuraties toe te staan of af te dwingen, bijvoorbeeld of uw Azure Machine Learning-werk ruimte een persoonlijk eind punt gebruikt. Raadpleeg de [Azure Policy documentatie](../governance/policy/overview.md)voor meer informatie over Azure Policy. Zie [naleving controleren en beheren met Azure Policy](how-to-integrate-azure-policy.md)voor meer informatie over het beleid dat specifiek is voor Azure machine learning.

## <a name="next-steps"></a>Volgende stappen

* [Azure Machine Learning webservices beveiligen met TLS](how-to-secure-web-service.md)
* [Een Machine Learning model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
* [Azure Machine Learning gebruiken met Azure Firewall](how-to-access-azureml-behind-firewall.md)
* [Azure Machine Learning gebruiken met Azure Virtual Network](how-to-network-security-overview.md)
* [Gegevens versleuteling in rust en onderweg](concept-data-encryption.md)
* [Een real-time aanbevelings-API bouwen op Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
