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
ms.date: 09/09/2020
ms.openlocfilehash: a9ad018980784a1f809ad28a77dacf9f0328fffa
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94873893"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Enter prise Security en governance voor Azure Machine Learning

In dit artikel vindt u informatie over de beschik bare beveiligings functies voor Azure Machine Learning.

Wanneer u een Cloud service gebruikt, is een best practice het beperken van de toegang tot alleen de gebruikers die er behoefte aan hebben. Begin met het verificatie-en autorisatie model dat door de service wordt gebruikt. U kunt ook de netwerk toegang beperken of bronnen veilig samen voegen in uw on-premises netwerk met de Cloud. Gegevens versleuteling is ook cruciaal, zowel op rest als tijdens het verplaatsen van gegevens tussen services. U kunt ook beleids regels maken voor het afdwingen van bepaalde configuraties of Logboeken wanneer niet-compatibele configuraties worden gemaakt. Ten slotte moet u de service kunnen bewaken en een audit logboek maken van alle activiteiten.

> [!NOTE]
> De informatie in dit artikel werkt met de Azure Machine Learning python SDK-versie 1.0.83.1 of hoger.

## <a name="authentication--authorization"></a>Verificatie & autorisatie

De meeste authenticatie voor Azure Machine Learning bronnen gebruiken Azure Active Directory (Azure AD) voor verificatie en op rollen gebaseerd toegangs beheer (Azure RBAC) voor autorisatie. De uitzonde ringen hierop zijn:

* __SSH__: u kunt SSH-toegang inschakelen voor bepaalde reken resources, zoals Azure machine learning reken instantie. SSH-toegang gebruikt verificatie op basis van een sleutel. Zie [SSH-sleutels maken en beheren](../virtual-machines/linux/create-ssh-keys-detailed.md)voor meer informatie over het maken van SSH-sleutels. Zie [Azure machine learning Compute-instantie maken en beheren](how-to-create-manage-compute-instance.md)voor meer informatie over het inschakelen van SSH-toegang.
* __Modellen die zijn geïmplementeerd als__ webservices: implementaties op basis van __sleutels__ of op __tokens__ gebaseerd toegangs beheer kunnen worden gebruikt. Sleutels zijn statische teken reeksen. Tokens worden opgehaald met behulp van een Azure AD-account. Zie [verificatie configureren voor modellen die zijn geïmplementeerd als een webservice](how-to-authenticate-web-service.md)voor meer informatie.

Specifieke services waarvan Azure Machine Learning afhankelijk is, zoals Azure Data Storage-services, hebben hun eigen verificatie-en autorisatie methoden. Zie [verbinding maken met opslag Services](how-to-access-data.md)voor meer informatie over de verificatie van Storage-services.

### <a name="azure-ad-authentication"></a>Azure Active Directory-verificatie

Multi-factor Authentication wordt ondersteund als Azure Active Directory (Azure AD) is geconfigureerd om het te gebruiken. Dit is het verificatie proces:

1. De client meldt zich aan bij Azure AD en ontvangt een Azure Resource Manager token.  Gebruikers en service-principals worden volledig ondersteund.
1. De client geeft het token aan Azure Resource Manager en alle Azure Machine Learning.
1. De Machine Learning-service levert een Machine Learning-service token aan het gebruikers Compute-doel (bijvoorbeeld Machine Learning Compute). Dit token wordt gebruikt door het gebruikers Compute-doel om terug te bellen naar de Machine Learning-service nadat de uitvoering is voltooid. Het bereik is beperkt tot de werk ruimte.

[![Verificatie in Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Zie [verificatie voor Azure machine learning-werk ruimte](how-to-setup-authentication.md)voor meer informatie.

### <a name="azure-rbac"></a>Azure RBAC

U kunt meerdere werk ruimten maken en elke werk ruimte kan worden gedeeld door meerdere personen. U kunt bepalen welke functies of bewerkingen van de werkruimte gebruikers toegang hebben door hun Azure AD-account toe te wijzen aan Azure RBAC-rollen. Hier volgen de ingebouwde rollen:

* Eigenaar
* Inzender
* Lezer

Eigen aren en inzenders kunnen alle reken doelen en gegevens archieven gebruiken die aan de werk ruimte zijn gekoppeld.  

De volgende tabel bevat enkele van de belangrijkste Azure Machine Learning bewerkingen en de rollen die ze kunnen uitvoeren:

| Azure Machine Learning bewerking | Eigenaar | Inzender | Lezer |
| ---- |:----:|:----:|:----:|
| Werkruimte maken | ✓ | ✓ | |
| Werk ruimte delen | ✓ | |  |
| Reken doel maken | ✓ | ✓ | |
| Reken doel koppelen | ✓ | ✓ | |
| Gegevens archieven koppelen | ✓ | ✓ | |
| Experiment uitvoeren | ✓ | ✓ | |
| Uitvoeringen/metrische gegevens weer geven | ✓ | ✓ | ✓ |
| Model registreren | ✓ | ✓ | |
| Installatie kopie maken | ✓ | ✓ | |
| Webservice implementeren | ✓ | ✓ | |
| Modellen/afbeeldingen weer geven | ✓ | ✓ | ✓ |
| Webservice aanroepen | ✓ | ✓ | ✓ |

Als de ingebouwde rollen niet aan uw behoeften voldoen, kunt u aangepaste rollen maken. Aangepaste rollen bepalen alle bewerkingen binnen een werk ruimte, zoals het maken van een berekening, het indienen van een uitvoering, het registreren van een gegevens opslag of het implementeren van een model. Aangepaste rollen kunnen Lees-, schrijf-of verwijder machtigingen hebben voor de verschillende resources van een werk ruimte, zoals clusters, gegevens opslag, modellen en eind punten. U kunt de rol beschikbaar maken op een specifiek werk ruimte niveau, op een specifiek niveau van de resource groep of op een specifiek abonnements niveau. Zie [gebruikers en rollen beheren in een Azure machine learning-werk ruimte](how-to-assign-roles.md)voor meer informatie.

Zie [Azure Role-Based Access Control voor Kubernetes-autorisatie](../aks/manage-azure-rbac.md)voor meer informatie over het gebruik van RBAC met Kubernetes.

> [!IMPORTANT]
> Azure Machine Learning is afhankelijk van andere Azure-Services, zoals Azure Blob Storage en Azure Kubernetes Services. Elke Azure-service heeft zijn eigen Azure RBAC-configuraties. Om het gewenste niveau van toegangs beheer te verkrijgen, moet u mogelijk zowel Azure RBAC-configuraties voor Azure Machine Learning als voor de services die worden gebruikt met Azure Machine Learning Toep assen.

> [!WARNING]
> Azure Machine Learning wordt ondersteund met Azure Active Directory Business-to-Business-samen werking, maar wordt momenteel niet ondersteund met Azure Active Directory Business-to-Consuming-samen werking.

### <a name="managed-identities"></a>Beheerde identiteiten

Aan elke werk ruimte is ook een aan het systeem toegewezen [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) gekoppeld die dezelfde naam heeft als de werk ruimte. De beheerde identiteit wordt gebruikt om veilig toegang te krijgen tot resources die worden gebruikt door de werk ruimte. Het heeft de volgende machtigingen op gekoppelde resources:

| Resource | Machtigingen |
| ----- | ----- |
| Werkruimte | Inzender |
| Opslagaccount | Inzender voor Storage Blob-gegevens |
| Key Vault | Toegang tot alle sleutels, geheimen, certificaten |
| Azure Container Registry | Inzender |
| Resource groep die de werk ruimte bevat | Inzender |
| De resource groep die de sleutel kluis bevat (als deze anders is dan de naam die de werk ruimte bevat) | Inzender |

Het is niet raadzaam om beheerders de toegang tot de beheerde identiteit in te trekken voor de resources die in de voor gaande tabel worden vermeld. U kunt de toegang herstellen met behulp van de bewerking voor het opnieuw synchroniseren van sleutels.

Azure Machine Learning maakt een extra toepassing (de naam begint met `aml-` of `Microsoft-AzureML-Support-App-` ) met toegang op Inzender niveau in uw abonnement voor elke werkruimte regio. Als u bijvoorbeeld één werk ruimte hebt in VS-Oost en één in Europa-noord in hetzelfde abonnement, ziet u twee van deze toepassingen. Met deze toepassingen kunt u Azure Machine Learning helpen bij het beheren van reken resources.

U kunt desgewenst uw eigen beheerde identiteiten configureren voor gebruik met Azure Virtual Machines en Azure Machine Learning Compute-Cluster. Met een virtuele machine kan de beheerde identiteit worden gebruikt voor toegang tot uw werk ruimte vanuit de SDK, in plaats van het Azure AD-account van de individuele gebruiker. Met een berekenings cluster wordt de beheerde identiteit gebruikt voor toegang tot bronnen zoals beveiligde gegevens opslag die de gebruiker die de trainings taak uitvoert, geen toegang heeft tot. Zie [verificatie voor Azure machine learning-werk ruimte](how-to-setup-authentication.md)voor meer informatie.

## <a name="network-security-and-isolation"></a>Netwerk beveiliging en-isolatie

U kunt Azure Virtual Network (VNet) gebruiken om fysieke toegang tot Azure Machine Learning resources te beperken. Met VNets kunt u netwerk omgevingen maken die gedeeltelijk of volledig zijn geïsoleerd van het open bare Internet. Dit vermindert de kwets baarheid voor uw oplossing en de kans op gegevens exfiltration.

Raadpleeg de volgende documenten voor meer informatie:

* [Overzicht van virtuele netwerk isolatie en privacy](how-to-network-security-overview.md)
* [Beveiligde werkruimteresources](how-to-secure-workspace-vnet.md)
* [Beveiligde trainingsomgeving](how-to-secure-training-vnet.md)
* [Veilige interferentie omgeving](how-to-secure-inferencing-vnet.md)
* [Studio gebruiken in een beveiligd virtueel netwerk](how-to-enable-studio-virtual-network.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Gegevensversleuteling

Azure Machine Learning gebruikt diverse reken bronnen en gegevens archieven. Zie [gegevens versleuteling met Azure machine learning](concept-data-encryption.md)voor meer informatie over hoe elk van deze gegevens versleuteling in rust en onderweg wordt ondersteund.

### <a name="microsoft-generated-data"></a>Door micro soft gegenereerde gegevens

Wanneer u services zoals automatische Machine Learning gebruikt, kan micro soft een tijdelijke, vooraf verwerkte gegevens genereren voor het trainen van meerdere modellen. Deze gegevens worden opgeslagen in een gegevens opslag in uw werk ruimte, zodat u op de juiste wijze toegangs controle en versleuteling kunt afdwingen.

U kunt ook [Diagnostische gegevens die vanuit uw geïmplementeerde eind punt zijn geregistreerd](how-to-enable-app-insights.md) , versleutelen in uw Azure-toepassing Insights-exemplaar.

## <a name="monitoring"></a>Bewaking

Er zijn verschillende bewakings scenario's met Azure Machine Learning, afhankelijk van de rol en wat wordt bewaakt.

| Rol | Bewaking die moet worden gebruikt | Beschrijving |
| ---- | ----- | ----- |
| Beheerder, DevOps, MLOps | [Azure monitor metrische gegevens](#azure-monitor), [activiteiten logboek](#activity-log), het [scannen van beveiligings problemen](#vulnerability-scanning) | Informatie over service niveau |
| Data wetenschapper, MLOps | [Monitors worden uitgevoerd](#monitor-runs) | Informatie die is vastgelegd tijdens de trainings uitvoeringen |
| MLOps | [Model gegevens verzamelen](how-to-enable-data-collection.md), [controleren met Application Insights](how-to-enable-app-insights.md) | Informatie vastgelegd door modellen die zijn geïmplementeerd als webservices of IoT Edge modules|

### <a name="monitor-runs"></a>Monitors worden uitgevoerd

U kunt experimentele uitvoeringen bewaken in Azure Machine Learning, inclusief informatie over logboek registratie vanuit uw trainings scripts. Deze informatie kan worden weer gegeven via de SDK, Azure CLI en Studio. Raadpleeg voor meer informatie de volgende artikelen:

* [Trainings uitvoeringen starten, controleren en annuleren](how-to-manage-runs.md)
* [Logboeken inschakelen](how-to-track-experiments.md)
* [Logboeken weergeven](how-to-monitor-view-training-logs.md)
* [Uitvoeringen visualiseren met TensorBoard](how-to-monitor-tensorboard.md)

### <a name="azure-monitor"></a>Azure Monitor

U kunt Azure Monitor metrische gegevens gebruiken om metrische gegevens voor uw Azure Machine Learning-werk ruimte weer te geven en te controleren. Selecteer in de [Azure Portal](https://portal.azure.com)uw werk ruimte en selecteer vervolgens **metrische gegevens**:

[![Scherm opname van voor beelden van metrische gegevens voor een werk ruimte](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

De metrische gegevens bevatten informatie over uitvoeringen, implementaties en registraties.

Zie [metrische gegevens in azure monitor](../azure-monitor/platform/data-platform-metrics.md)voor meer informatie.

### <a name="activity-log"></a>Activiteitenlogboek

U kunt het activiteiten logboek van een werk ruimte bekijken om verschillende bewerkingen te bekijken die worden uitgevoerd op de werk ruimte. Het logboek bevat basis informatie, zoals de naam van de bewerking, de gebeurtenis initiator en de tijds tempel.

Deze scherm afbeelding toont het activiteiten logboek van een werk ruimte:

[![Scherm opname van het activiteiten logboek van een werk ruimte](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Details van Score aanvragen worden opgeslagen in Application Insights. Application Insights wordt in uw abonnement gemaakt wanneer u een werk ruimte maakt. Geregistreerde gegevens bevatten onder andere de volgende velden:

* HTTPMethod
* User agent
* ComputeType
* RequestUrl
* Status code
* RequestId
* Duur

> [!IMPORTANT]
> Sommige acties in de Azure Machine Learning-werk ruimte registreren geen gegevens in het activiteiten logboek. Bijvoorbeeld, het begin van een trainings uitvoering en de registratie van een model worden niet geregistreerd.
>
> Sommige van deze acties worden weer gegeven in het gebied **activiteiten** van uw werk ruimte, maar deze meldingen geven niet aan wie de activiteit heeft gestart.

### <a name="vulnerability-scanning"></a>Scannen op beveiligingsproblemen

Azure Security Center biedt geïntegreerd beveiligingsbeheer en geavanceerde bedreigingsbeveiliging voor verschillende hybride cloudworkloads. Voor Azure machine learning moet u het scannen van uw Azure Container Registry resource en Azure Kubernetes-service resources inschakelen. Zie [Azure container Registry Image scanning door Security Center](../security-center/defender-for-container-registries-introduction.md) en [Azure Kubernetes Services-integratie met Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Naleving controleren en beheren

[Azure Policy](../governance/policy/index.yml) is een beheer programma waarmee u ervoor kunt zorgen dat Azure-resources voldoen aan uw beleid. Met Azure Machine Learning kunt u de volgende beleids regels toewijzen:

* Door de **klant beheerde sleutel**: controleren of afdwingen of werk ruimten een door de klant beheerde sleutel moeten gebruiken.
* **Privé-koppeling**: controleren of werk ruimten een persoonlijk eind punt gebruiken om te communiceren met een virtueel netwerk.

Raadpleeg de [Azure Policy documentatie](../governance/policy/overview.md)voor meer informatie over Azure Policy.

Zie [naleving controleren en beheren met Azure Policy](how-to-integrate-azure-policy.md)voor meer informatie over het beleid dat specifiek is voor Azure machine learning.

## <a name="resource-locks"></a>Resourcevergrendelingen

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Volgende stappen

* [Azure Machine Learning webservices beveiligen met TLS](how-to-secure-web-service.md)
* [Een Machine Learning model gebruiken dat is geïmplementeerd als een webservice](how-to-consume-web-service.md)
* [Azure Machine Learning gebruiken met Azure Firewall](how-to-access-azureml-behind-firewall.md)
* [Azure Machine Learning gebruiken met Azure Virtual Network](how-to-network-security-overview.md)
* [Gegevens versleuteling in rust en onderweg](concept-data-encryption.md)
* [Een real-time aanbevelings-API bouwen op Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
