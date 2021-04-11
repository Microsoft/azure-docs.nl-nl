---
title: 'Concepten: toegang en identiteit in azure Kubernetes Services (AKS)'
description: Meer informatie over toegang en identiteit in azure Kubernetes service (AKS), met inbegrip van Azure Active Directory integratie, Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC) en rollen en bindingen.
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 12900a64d9e023e4bddd5b5862b6a127fcba1d36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104949988"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Toegangs- en identiteitsopties voor Azure Kubernetes Service (AKS)

Er zijn verschillende manieren om te verifiëren, toegang te beheren/te controleren en Kubernetes-clusters te beveiligen. Met Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC) kunt u gebruikers, groepen en service accounts toegang verlenen tot de resources die ze nodig hebben. Met Azure Kubernetes service (AKS) kunt u de structuur van beveiliging en machtigingen verder verbeteren met behulp van Azure Active Directory en Azure RBAC. Op deze manier kunt u uw cluster toegang beveiligen en alleen de mini maal vereiste machtigingen geven aan ontwikkel aars en Opera tors.

In dit artikel worden de belangrijkste concepten geïntroduceerd die u helpen bij het verifiëren en toewijzen van machtigingen in AKS.

## <a name="aks-service-permissions"></a>AKS-service machtigingen

Wanneer u een cluster maakt, maakt AKS de resources die nodig zijn om het cluster te maken en uit te voeren, zoals Vm's en Nic's, namens de gebruiker die het cluster maakt. Deze identiteit is verschillend van de identiteits machtiging van het cluster, die wordt gemaakt tijdens het maken van het cluster.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Identiteit voor het maken en gebruiken van de cluster machtigingen

De volgende machtigingen zijn vereist voor de identiteit die het cluster maakt en bewerkt.

| Machtiging | Reden |
|---|---|
| Micro soft. Compute/diskEncryptionSets/lezen | Vereist voor het lezen van de set-ID van de schijf versleuteling. |
| Micro soft. Compute/proximityPlacementGroups/schrijven | Vereist voor het bijwerken van proximity-plaatsings groepen. |
| Micro soft. Network/applicationGateways/lezen <br/> Micro soft. Network/applicationGateways/schrijven <br/> Microsoft.Network/virtualNetworks/subnets/join/action | Vereist voor het configureren van toepassings gateways en lid worden van het subnet. |
| Microsoft.Network/virtualNetworks/subnets/join/action | Vereist voor het configureren van de netwerk beveiligings groep voor het subnet wanneer u een aangepast VNET gebruikt.|
| Microsoft.Network/publicIPAddresses/join/action <br/> Micro soft. Network/publicIPPrefixes/samen voegen/actie | Vereist voor het configureren van de uitgaande open bare Ip's op het Standard Load Balancer. |
| Micro soft. OperationalInsights/werk ruimten/sharedkeys/lezen <br/> Micro soft. OperationalInsights/werk ruimten/lezen <br/> Micro soft. OperationsManagement/oplossingen/schrijven <br/> Micro soft. OperationsManagement/Solutions/lezen <br/> Micro soft. ManagedIdentity/userAssignedIdentities/Assign/Action | Vereist om Log Analytics-werk ruimten en Azure-bewaking voor containers te maken en bij te werken. |

### <a name="aks-cluster-identity-permissions"></a>Machtigingen voor AKS-cluster identiteit

De volgende machtigingen worden gebruikt door de AKS-cluster identiteit, die wordt gemaakt en gekoppeld aan het AKS-cluster wanneer het cluster wordt gemaakt. Elke machtiging wordt gebruikt om de volgende redenen:

| Machtiging | Reden |
|---|---|
| Micro soft. container service/managedClusters/*  <br/> | Vereist voor het maken van gebruikers en het werken met het cluster
| Micro soft. Network/loadBalancers/verwijderen <br/> Micro soft. Network/loadBalancers/lezen <br/> Micro soft. Network/loadBalancers/schrijven | Vereist voor het configureren van de load balancer voor een Load Balancer-service. |
| Micro soft. Network/publicIPAddresses/verwijderen <br/> Microsoft.Network/publicIPAddresses/read <br/> Microsoft.Network/publicIPAddresses/write | Vereist om open bare Ip's voor een Load Balancer-service te zoeken en te configureren. |
| Microsoft.Network/publicIPAddresses/join/action | Vereist voor het configureren van open bare Ip's voor een Load Balancer-service. |
| Micro soft. Network/networkSecurityGroups/lezen <br/> Micro soft. Network/networkSecurityGroups/schrijven | Vereist voor het maken of verwijderen van beveiligings regels voor een Load Balancer-service. |
| Micro soft. Compute/schijven/verwijderen <br/> Microsoft.Compute/disks/read <br/> Microsoft.Compute/disks/write <br/> Micro soft. computes/locaties/onkoperen/lezen | Vereist om AzureDisks te configureren. |
| Micro soft. Storage/Storage accounts/verwijderen <br/> Micro soft. Storage/Storage accounts/Listkeys ophalen/Action <br/> Micro soft. Storage/Storage accounts/lezen <br/> Micro soft. Storage/Storage accounts/schrijven <br/> Micro soft. Storage/Operations/lezen | Vereist voor het configureren van opslag accounts voor AzureFile of AzureDisk. |
| Micro soft. Network/routeTables/lezen <br/> Micro soft. Network/routeTables/routes/verwijderen <br/> Micro soft. Network/routeTables/routes/lezen <br/> Micro soft. Network/routeTables/routes/schrijven <br/> Micro soft. Network/routeTables/schrijven | Vereist voor het configureren van route tabellen en routes voor knoop punten. |
| Micro soft. Compute/informatie/lezen | Vereist voor het vinden van informatie voor virtuele machines in een VMAS, zoals zones, fout domein, grootte en gegevens schijven. |
| Micro soft. Compute/informatie/schrijven | Vereist om AzureDisks te koppelen aan een virtuele machine in een VMAS. |
| Micro soft. Compute/virtualMachineScaleSets/lezen <br/> Micro soft. Compute/virtualMachineScaleSets/informatie/lezen <br/> Micro soft. Compute/virtualMachineScaleSets/informatie/instanceView/lezen | Dit is vereist om informatie te vinden voor virtuele machines in een schaalset voor een virtuele machine, zoals zones, fout domein, grootte en gegevens schijven. |
| Micro soft. Network/networkInterfaces/schrijven | Vereist om een virtuele machine in een VMAS toe te voegen aan een load balancer back-end-adres groep. |
| Micro soft. Compute/virtualMachineScaleSets/schrijven | Vereist voor het toevoegen van een schaalset voor een virtuele machine aan een load balancer back-end-adres groepen en het uitschalen van knoop punten in een virtuele-machine schaalset. |
| Micro soft. Compute/virtualMachineScaleSets/informatie/write | Vereist om AzureDisks te koppelen en een virtuele machine van een virtuele-machine schaalset toe te voegen aan de load balancer. |
| Micro soft. Network/networkInterfaces/lezen | Vereist voor het zoeken naar interne Ip's en load balancer back-end-adres groepen voor virtuele machines in een VMAS. |
| Micro soft. Compute/virtualMachineScaleSets/informatie/networkInterfaces/lezen | Vereist voor het zoeken naar interne Ip's en load balancer back-end-adres groepen voor een virtuele machine in een schaalset met virtuele machines. |
| Micro soft. Compute/virtualMachineScaleSets/informatie/networkInterfaces/ipconfigurations/publicipaddresses/lezen | Vereist voor het vinden van open bare Ip's voor een virtuele machine in een schaalset voor virtuele machines. |
| Micro soft. Network/virtualNetworks/lezen <br/> Microsoft.Network/virtualNetworks/subnets/read | Vereist om te controleren of er een subnet bestaat voor de interne load balancer in een andere resource groep. |
| Micro soft. Compute/moment opnamen/verwijderen <br/> Micro soft. Compute/moment opnamen/lezen <br/> Micro soft. Compute/moment opnamen/schrijven | Vereist voor het configureren van moment opnamen voor AzureDisk. |
| Micro soft. Compute/locaties/toegestane VM/lezen <br/> Micro soft. Compute/locaties/bewerkingen/lezen | Vereist voor het vinden van de grootte van virtuele machines voor het zoeken van AzureDisk-volume limieten. |

### <a name="additional-cluster-identity-permissions"></a>Aanvullende machtigingen voor de cluster identiteit

De volgende aanvullende machtigingen zijn vereist voor de cluster-id bij het maken van een cluster met specifieke kenmerken. Deze machtigingen worden niet automatisch toegewezen, dus u moet deze machtigingen toevoegen aan de cluster identiteit nadat deze is gemaakt.

| Machtiging | Reden |
|---|---|
| Micro soft. Network/networkSecurityGroups/schrijven <br/> Micro soft. Network/networkSecurityGroups/lezen | Vereist als u een netwerk beveiligings groep gebruikt in een andere resource groep. Vereist voor het configureren van beveiligings regels voor een Load Balancer-service. |
| Microsoft.Network/virtualNetworks/subnets/read <br/> Microsoft.Network/virtualNetworks/subnets/join/action | Vereist als u een subnet in een andere resource groep gebruikt, zoals een aangepast VNET. |
| Micro soft. Network/routeTables/routes/lezen <br/> Micro soft. Network/routeTables/routes/schrijven | Vereist als u een subnet gebruikt dat is gekoppeld aan een route tabel in een andere resource groep, zoals een aangepast VNET met een aangepaste route tabel. Vereist om te controleren of er al een subnet bestaat voor het subnet in de andere resource groep. |
| Microsoft.Network/virtualNetworks/subnets/read | Vereist als u een interne load balancer in een andere resource groep gebruikt. Vereist om te controleren of er al een subnet bestaat voor de interne load balancer in de resource groep. |
| Micro soft. Network/privatednszones/* | Vereist als u een privé-DNS-zone gebruikt in een andere resource groep, zoals een aangepaste privateDNSZone. |

## <a name="kubernetes-role-based-access-control-kubernetes-rbac"></a>Op rollen gebaseerd toegangs beheer op basis van Kubernetes (Kubernetes RBAC)

Kubernetes maakt gebruik van Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC) om nauw keurige filters te bieden voor de acties die gebruikers kunnen uitvoeren. Met dit besturings systeem kunt u gebruikers of groepen gebruikers toewijzen, machtigingen geven om resources te maken of te wijzigen, of logboeken van actieve werk belastingen van toepassingen bekijken. Deze machtigingen kunnen worden ingesteld op een enkele naam ruimte of worden toegestaan in het hele AKS-cluster. Met Kubernetes RBAC maakt u *rollen* om machtigingen te definiëren en wijst u deze rollen vervolgens toe aan gebruikers met *functie bindingen*.

Zie [using KUBERNETES RBAC Authorization][kubernetes-rbac](Engelstalig) voor meer informatie.

### <a name="roles-and-clusterroles"></a>Rollen en ClusterRoles

Voordat u machtigingen toewijst aan gebruikers met Kubernetes RBAC, definieert u eerst die machtigingen als een *rol*. Kubernetes-rollen *verlenen* machtigingen. Er is geen machtiging voor *weigeren* .

Rollen worden gebruikt voor het verlenen van machtigingen binnen een naam ruimte. Als u machtigingen wilt verlenen voor het hele cluster of als u cluster bronnen buiten een bepaalde naam ruimte wilt toewijzen, kunt u in plaats daarvan *ClusterRoles* gebruiken.

Een ClusterRole werkt op dezelfde manier om machtigingen te verlenen aan resources, maar kan worden toegepast op resources in het hele cluster, niet op een specifieke naam ruimte.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings en ClusterRoleBindings

Zodra rollen zijn gedefinieerd om machtigingen te verlenen voor bronnen, wijst u deze Kubernetes RBAC-machtigingen toe aan een *RoleBinding*. Als uw AKS-cluster wordt [geïntegreerd met Azure Active Directory (Azure AD)](#azure-active-directory-integration), zijn bindingen de manier waarop deze Azure AD-gebruikers machtigingen hebben voor het uitvoeren van acties in het cluster. Zie How to [Control Access to cluster resources with Kubernetes access control en Azure Active Directory Identities](azure-ad-rbac.md)(Engelstalig) voor meer informatie.

Roltoewijzingen worden gebruikt voor het toewijzen van rollen voor een bepaalde naam ruimte. Met deze benadering kunt u een enkel AKS-cluster logisch scheiden, met gebruikers die alleen toegang hebben tot de toepassings resources in hun toegewezen naam ruimte. Als u rollen moet binden in het hele cluster of als u cluster bronnen buiten een bepaalde naam ruimte wilt koppelen, kunt u in plaats daarvan *ClusterRoleBindings* gebruiken.

Een ClusterRoleBinding werkt op dezelfde manier om rollen aan gebruikers te binden, maar kan worden toegepast op resources in het hele cluster, niet op een specifieke naam ruimte. Met deze aanpak kunt u beheerders of ondersteunings technici toegang verlenen tot alle resources in het AKS-cluster.


> [!NOTE]
> Alle cluster acties die door micro soft/AKS worden uitgevoerd, worden gemaakt met toestemming van de gebruiker onder een ingebouwde Kubernetes-rol `aks-service` en ingebouwde functie binding `aks-service-rolebinding` . Deze rol stelt AKS in staat om problemen met het cluster op te lossen en te diagnosticeren, maar kan geen machtigingen wijzigen of rollen of roltoewijzingen maken, of andere acties met hoge bevoegdheden. Rollen toegang is alleen ingeschakeld onder actieve ondersteunings tickets met Just-in-time-toegang. Meer informatie over [AKS-ondersteunings beleid](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Kubernetes-service accounts

Een van de primaire gebruikers typen in Kubernetes is een *Service account*. Er bestaat een service account in en wordt beheerd door de Kubernetes-API. De referenties voor service accounts worden opgeslagen als Kubernetes geheimen, waardoor ze kunnen worden gebruikt door geautoriseerde peul om te communiceren met de API-server. De meeste API-aanvragen bieden een verificatie token voor een service account of een normaal gebruikers account.

Normale gebruikers accounts staan meer traditionele toegang toe voor beheerders of ontwikkel aars van personen, niet alleen services en processen. Kubernetes zelf biedt geen oplossing voor identiteits beheer waar reguliere gebruikers accounts en wacht woorden worden opgeslagen. In plaats daarvan kunnen externe identiteits oplossingen worden geïntegreerd in Kubernetes. Voor AKS-clusters is deze geïntegreerde identiteits oplossing Azure Active Directory.

Zie [Kubernetes-verificatie][kubernetes-authentication]voor meer informatie over de identiteits opties in Kubernetes.

## <a name="azure-active-directory-integration"></a>Integratie van Azure Active Directory

De beveiliging van AKS-clusters kan worden uitgebreid met de integratie van Azure Active Directory (AD). Azure AD is gebouwd op tien tallen bedrijfs identiteits beheer en is een multi tenant-, Cloud-en identiteits beheer service die belang rijke Directory Services, Toegangs beheer voor toepassingen en identiteits beveiliging combineert. Met Azure AD kunt u on-premises identiteiten integreren in AKS-clusters om één bron te bieden voor account beheer en beveiliging.

![Integratie met AKS-clusters Azure Active Directory](media/concepts-identity/aad-integration.png)

Met Azure AD geïntegreerde AKS-clusters kunt u gebruikers of groepen toegang verlenen tot Kubernetes-resources binnen een naam ruimte of in het cluster. `kubectl`Een gebruiker kan een configuratie context verkrijgen door de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] uit te voeren. Wanneer een gebruiker vervolgens met het AKS-cluster communiceert met `kubectl` , wordt u gevraagd zich aan te melden met hun Azure AD-referenties. Deze benadering biedt één bron voor gebruikers account beheer en wachtwoord referenties. De gebruiker heeft alleen toegang tot de resources zoals gedefinieerd door de Cluster beheerder.

Azure AD-verificatie wordt geleverd voor AKS-clusters met OpenID Connect Connect. OpenID Connect Connect is een id-laag die boven op het OAuth 2,0-protocol is gebouwd. Voor meer informatie over OpenID Connect Connect raadpleegt u de [Open-ID Connect-documentatie][openid-connect]. Vanuit het Kubernetes-cluster wordt [webhook-token verificatie][webhook-token-docs] gebruikt om verificatie tokens te verifiëren. Webhook-token verificatie wordt geconfigureerd en beheerd als onderdeel van het AKS-cluster.

### <a name="webhook-and-api-server"></a>Webhook en API-server

![Webhook-en API-Server verificatie stroom](media/concepts-identity/auth-flow.png)

Zoals in de bovenstaande afbeelding wordt weer gegeven, roept de API-server de AKS-webhookserver aan en voert de volgende stappen uit:

1. De Azure AD-client toepassing wordt door kubectl gebruikt voor het aanmelden van gebruikers met [OAuth 2,0-autorisatie subsidie stroom](../active-directory/develop/v2-oauth2-device-code.md).
2. Azure AD biedt een access_token, id_token en een refresh_token.
3. De gebruiker doet een aanvraag om kubectl te maken met een access_token van kubeconfig.
4. Kubectl verzendt het access_token naar de API-server.
5. De API-server is geconfigureerd met de auth webhook-server om validatie uit te voeren.
6. De webhookserver voor verificatie bevestigt dat de JSON Web Token hand tekening geldig is door de open bare Azure AD-ondertekeningssleutel te controleren.
7. De server toepassing maakt gebruik van door de gebruiker ingevoerde referenties voor het opvragen van groepslid maatschappen van de aangemelde gebruiker vanuit de MS Graph API.
8. Er wordt een antwoord verzonden naar de API-server met gebruikers gegevens zoals de claim van de user principal name (UPN) van het toegangs token en het groepslid maatschap van de gebruiker op basis van de object-ID.
9. De API voert een autorisatie besluit uit op basis van de Kubernetes Role/RoleBinding.
10. Na de autorisatie retourneert de API-server een reactie op kubectl.
11. Kubectl geeft feedback aan de gebruiker.
 
**Meer informatie over het integreren van AKS [met Aad.](managed-aad.md)**

## <a name="azure-role-based-access-control-azure-rbac"></a>Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)

Op rollen gebaseerd toegangsbeheer in Azure is een machtigingssysteem dat is gebouwd op [Azure Resource Manager](../azure-resource-manager/management/overview.md) dat een geavanceerd toegangsbeheer van Azure-resources biedt.

 Azure RBAC is ontworpen om te werken aan resources binnen uw Azure-abonnement terwijl Kubernetes RBAC is ontworpen voor het werken met Kubernetes-resources binnen uw AKS-cluster. 

Met Azure RBAC maakt u een *roldefinitie* waarin de machtigingen worden beschreven die moeten worden toegepast. Vervolgens wordt aan een gebruiker of groep deze roldefinitie toegewezen via een *roltoewijzing* voor een bepaald *bereik*. Dit kan een afzonderlijke resource, een resource groep of het hele abonnement zijn.

Zie [Wat is Azure Role-based Access Control (Azure RBAC)?][azure-rbac] voor meer informatie.

Er zijn twee toegangs niveaus nodig om een AKS-cluster volledig te kunnen gebruiken: 
1. [Open de AKS-resource in uw Azure-abonnement](#azure-rbac-to-authorize-access-to-the-aks-resource). Met dit proces kunt u bepalen of u uw cluster wilt schalen of upgraden met behulp van de AKS-Api's en hoe u uw kubeconfig ophaalt.
2. Toegang tot de Kubernetes-API. Deze toegang wordt bepaald door [KUBERNETES RBAC](#kubernetes-role-based-access-control-kubernetes-rbac) (traditioneel) of door [Azure RBAC te integreren met AKS voor Kubernetes-autorisatie](#azure-rbac-for-kubernetes-authorization-preview)

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC voor het machtigen van toegang tot de AKS-resource

Met Azure RBAC kunt u uw gebruikers (of identiteiten) voorzien van gedetailleerde toegang tot AKS-resources in een of meer abonnementen. U kunt bijvoorbeeld de rol van de [Azure Kubernetes-service Inzender](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) hebben waarmee u acties kunt uitvoeren, zoals het schalen en upgraden van uw cluster. Hoewel een andere gebruiker de beheer [functie van de Azure Kubernetes-service cluster](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) kan hebben die alleen toestemming geeft om de beheerder kubeconfig te halen.

U kunt uw gebruiker ook de rol algemeen [Inzender](../role-based-access-control/built-in-roles.md#contributor) geven, die de bovenstaande machtigingen bevat en elke actie die mogelijk is voor de AKS-resource, met uitzonde ring van het beheren van machtigingen zelf.

Meer informatie over het gebruik van Azure RBAC voor het beveiligen van de toegang tot het kubeconfig-bestand dat [hier](control-kubeconfig-access.md)toegang geeft tot de KUBERNETES-API.

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC voor Kubernetes-autorisatie (preview-versie)

Met de integratie van Azure RBAC maakt AKS gebruik van een Kubernetes Authorization webhook-server, zodat u machtigingen en toewijzingen van Azure AD-geïntegreerde K8s-cluster resources kunt beheren met Azure Role definition en roltoewijzingen.

![Azure RBAC voor Kubernetes-autorisatie stroom](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Zoals in het bovenstaande diagram wordt weer gegeven, volgt u bij het gebruik van de integratie van Azure RBAC alle aanvragen voor de Kubernetes-API dezelfde verificatie stroom zoals wordt uitgelegd in de [sectie integratie van Azure Active Directory](#azure-active-directory-integration). 

Maar daarna wordt het verzoek door Azure in plaats van Kubernetes RBAC voor autorisatie gebruikt, op voor waarde dat de identiteit die de aanvraag heeft ingediend in AAD voor komt. Als de identiteit niet bestaat in AAD, bijvoorbeeld een Kubernetes-service account, is de Azure RBAC niet in en de normale Kubernetes RBAC.

In dit scenario kunt u gebruikers een van de vier ingebouwde rollen geven of aangepaste rollen maken zoals u zou doen met Kubernetes-rollen, maar in dit geval de Azure RBAC-mechanismen en Api's gebruiken. 

Met deze functie kunt u bijvoorbeeld niet alleen gebruikers machtigingen geven voor de AKS-resource in abonnementen, maar ze instellen en de rol en machtigingen geven die ze zullen hebben in elk van deze clusters die de toegang tot de Kubernetes-API beheren. U kunt bijvoorbeeld de `Azure Kubernetes Service RBAC Viewer` rol voor het abonnements bereik toekennen en de ontvanger kan alle Kubernetes-objecten van alle clusters weer geven, maar niet wijzigen.

> [!IMPORTANT]
> Houd er rekening mee dat u Azure RBAC voor Kubernetes-autorisatie moet inschakelen voordat u deze functie gebruikt. [Zie hier](manage-azure-rbac.md)voor meer informatie en stapsgewijze instructies.

#### <a name="built-in-roles"></a>Ingebouwde rollen

AKS biedt de volgende vier ingebouwde rollen. Ze zijn vergelijkbaar met de [ingebouwde rollen](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) van de Kubernetes, maar met een paar verschillen als ondersteunende CRDs. Zie [hier](../role-based-access-control/built-in-roles.md)voor een volledige lijst met acties die zijn toegestaan door elke ingebouwde rol.

| Rol                                | Beschrijving  |
|-------------------------------------|--------------|
| RBAC-viewer voor Azure Kubernetes service  | Hiermee staat u alleen-lezen toegang toe om de meeste objecten in een naam ruimte weer te geven. Het weer geven van functies of functie bindingen is niet toegestaan. Deze rol staat weer gave niet toe `Secrets` , omdat het lezen van de inhoud van geheimen toegang biedt tot `ServiceAccount` referenties in de naam ruimte, waardoor er API-toegang zou kunnen worden toegestaan `ServiceAccount` in de naam ruimte (een vorm van bevoegdheden escalatie)  |
| RBAC-schrijver van Azure Kubernetes service | Hiermee wordt lees-/schrijftoegang tot de meeste objecten in een naam ruimte toegestaan. Deze rol staat het weer geven of wijzigen van rollen of rollen bindingen niet toe. Met deze rol is het echter mogelijk `Secrets` om de serviceaccount in de naam ruimte te benaderen en uit te voeren, zodat deze kan worden gebruikt om de API-toegangs niveaus van een wille keurige serviceaccount in de naam ruimte te verkrijgen. |
| RBAC-beheerder voor Azure Kubernetes service  | Hiermee kan beheerders toegang worden verleend binnen een naam ruimte. Hiermee staat u lees-/schrijftoegang toe voor de meeste bronnen in een naam ruimte (of cluster bereik), inclusief de mogelijkheid om rollen en rollen bindingen te maken binnen de naam ruimte. Deze rol staat geen schrijf toegang tot resource quota of de naam ruimte zelf toe. |
| De Azure Kubernetes service RBAC-cluster beheerder  | Hiermee kan toegang van Super gebruikers elke actie op elke resource uitvoeren. Hiermee krijgt u volledige controle over elke resource in het cluster en in alle naam ruimten. |


## <a name="summary"></a>Samenvatting

Deze tabel bevat een overzicht van de manieren waarop gebruikers zich kunnen verifiëren bij Kubernetes wanneer Azure AD-integratie is ingeschakeld.  In alle gevallen is de volg orde van de opdrachten van de gebruiker:
1. Voer uit `az login` om te verifiëren bij Azure.
1. Voer uit `az aks get-credentials` om referenties voor het cluster op te halen in `.kube/config` .
1. Uitvoeren `kubectl` van opdrachten (de eerste van waarmee op de browser gebaseerde verificatie kan worden geactiveerd voor verificatie bij het cluster, zoals beschreven in de volgende tabel).

De functie toekenning waarnaar wordt verwezen in de tweede kolom is de Azure RBAC-rol toekenning die wordt weer gegeven op het tabblad **Access Control** in het Azure Portal. De Azure AD-groep Cluster beheer wordt weer gegeven op het tabblad **configuratie** in de portal (of met `--aad-admin-group-object-ids` de parameter naam in de Azure CLI).

| Description        | Rol toekenning vereist| Azure AD-groep (en) cluster beheer | Wanneer gebruikt u dit? |
| -------------------|------------|----------------------------|-------------|
| Verouderde beheerder aanmelden met behulp van client certificaat| **Rol van Azure Kubernetes-beheerder**. Deze rol kan `az aks get-credentials` worden gebruikt met de `--admin` vlag, waarmee een [verouderd (niet-Azure AD) cluster beheer certificaat](control-kubeconfig-access.md) wordt gedownload naar de gebruiker `.kube/config` . Dit is het enige doel van de Azure Kubernetes-beheerdersrol.|n.v.t.|Als u permanent bent geblokkeerd door geen toegang tot een geldige Azure AD-groep met toegang tot uw cluster.| 
| Azure AD met hands-RoleBindings (cluster)| Gebruikersrol **Azure Kubernetes**. De rol ' gebruiker ' mag `az aks get-credentials` worden gebruikt zonder de `--admin` vlag. (Dit is het enige doel van ' Azure Kubernetes-gebruikersrol '.) Het resultaat van een Azure AD-cluster is het downloaden van [een lege vermelding](control-kubeconfig-access.md) in `.kube/config` , waardoor de browser verificatie wordt geactiveerd wanneer deze voor het eerst wordt gebruikt door `kubectl` .| De gebruiker bevindt zich niet in een van deze groepen. Omdat de gebruiker zich niet in een cluster beheer groepen bevindt, worden hun rechten volledig beheerd door alle RoleBindings of ClusterRoleBindings die zijn ingesteld door cluster beheerders. De (cluster) RoleBindings [benoemt Azure AD-gebruikers of Azure ad-groepen](azure-ad-rbac.md) als hun `subjects` . Als er geen dergelijke bindingen zijn ingesteld, kan de gebruiker geen `kubectl` opdrachten excute.|Als u een nauw keurig toegangs beheer wilt en u geen gebruik maakt van Azure RBAC voor Kubernetes-autorisatie. Houd er rekening mee dat de gebruiker die de bindingen instelt, zich moet aanmelden met een van de andere methoden die in deze tabel worden vermeld.|
| Azure AD door lid van Beheerders groep| Hetzelfde als hierboven|De gebruiker is lid van een van de groepen die hier worden vermeld. AKS genereert automatisch een ClusterRoleBinding die alle weer gegeven groepen koppelt aan de `cluster-admin` Kubernetes-rol. Zodat gebruikers in deze groepen alle opdrachten kunnen uitvoeren `kubectl` als `cluster-admin` .|Als u gebruikers eenvoudig de volledige beheerders rechten wilt verlenen en _geen_ gebruik wilt maken van Azure RBAC voor Kubernetes-autorisatie.|
| Azure AD met Azure RBAC voor Kubernetes-autorisatie|Twee rollen: First, **Azure Kubernetes gebruikersrol** (zoals hierboven). Ten tweede, een van de ' Azure Kubernetes service **RBAC**... ' de hierboven vermelde rollen of uw eigen aangepaste alternatief.|Het veld beheerders rollen op het tabblad Configuratie is niet relevant als Azure RBAC voor Kubernetes-autorisatie is ingeschakeld.|U gebruikt Azure RBAC voor Kubernetes-autorisatie. Deze benadering biedt u nauw keurige controle, zonder dat u RoleBindings of ClusterRoleBindings hoeft in te stellen.|

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Active Directory integreren met AKS][aks-aad]om aan de slag te gaan met Azure AD en Kubernetes RBAC.
- Zie [Aanbevolen procedures voor verificatie en autorisatie in AKS][operator-best-practices-identity]voor gekoppelde aanbevolen procedures.
- Om aan de slag te gaan met Azure RBAC voor Kubernetes-autorisatie, raadpleegt u [Azure RBAC gebruiken om toegang te verlenen in het Azure Kubernetes service-cluster (AKS)](manage-azure-rbac.md).
- Zie toegang tot het [cluster configuratie bestand beperken](control-kubeconfig-access.md) om aan de slag te gaan met het beveiligen van uw kubeconfig-bestand

Raadpleeg de volgende artikelen voor meer informatie over de belangrijkste Kubernetes-en AKS-concepten:

- [Kubernetes/AKS-clusters en-workloads][aks-concepts-clusters-workloads]
- [Kubernetes/AKS-beveiliging][aks-concepts-security]
- [Kubernetes/AKS virtuele netwerken][aks-concepts-network]
- [Kubernetes/AKS-opslag][aks-concepts-storage]
- [Kubernetes/AKS-schaal][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
