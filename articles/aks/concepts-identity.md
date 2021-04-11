---
title: 'Concepten: toegang en identiteit in azure Kubernetes Services (AKS)'
description: Meer informatie over toegang en identiteit in azure Kubernetes service (AKS), met inbegrip van Azure Active Directory integratie, Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC) en rollen en bindingen.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: 76871565e0bb4ca1811d46531d07b89181d07e19
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105915"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Toegangs- en identiteitsopties voor Azure Kubernetes Service (AKS)

U kunt op verschillende manieren toegang tot Kubernetes-clusters verifiëren, autoriseren, beveiligen en beheren. 
* Met Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC) kunt u gebruikers, groepen en service accounts toegang verlenen tot de resources die ze nodig hebben. 
* Met Azure Kubernetes service (AKS) kunt u de structuur van beveiliging en machtigingen verder verbeteren via Azure Active Directory en Azure RBAC. 

Met Kubernetes RBAC en AKS kunt u uw cluster toegang beveiligen en alleen de mini maal vereiste machtigingen geven aan ontwikkel aars en Opera tors.

In dit artikel worden de belangrijkste concepten geïntroduceerd die u helpen bij het verifiëren en toewijzen van machtigingen in AKS.

## <a name="aks-service-permissions"></a>AKS-service machtigingen

Bij het maken van een cluster worden door AKS de benodigde resources gegenereerd of gewijzigd (zoals Vm's en Nic's) om het cluster namens de gebruiker te maken en uit te voeren. Deze identiteit is verschillend van de identiteits machtiging van het cluster, die wordt gemaakt tijdens het maken van het cluster.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Identiteit voor het maken en gebruiken van de cluster machtigingen

De volgende machtigingen zijn vereist voor de identiteit die het cluster maakt en bewerkt.

| Machtiging | Reden |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | Vereist voor het lezen van de set-ID van de schijf versleuteling. |
| `Microsoft.Compute/proximityPlacementGroups/write` | Vereist voor het bijwerken van proximity-plaatsings groepen. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Vereist voor het configureren van toepassings gateways en lid worden van het subnet. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | Vereist voor het configureren van de netwerk beveiligings groep voor het subnet wanneer u een aangepast VNET gebruikt.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | Vereist voor het configureren van de uitgaande open bare Ip's op het Standard Load Balancer. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Vereist om Log Analytics-werk ruimten en Azure-bewaking voor containers te maken en bij te werken. |

### <a name="aks-cluster-identity-permissions"></a>Machtigingen voor AKS-cluster identiteit

De volgende machtigingen worden gebruikt door de AKS-cluster identiteit, die wordt gemaakt en gekoppeld aan het AKS-cluster. Elke machtiging wordt gebruikt om de volgende redenen:

| Machtiging | Reden |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | Vereist voor het maken van gebruikers en het werken met het cluster
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | Vereist voor het configureren van de load balancer voor een Load Balancer-service. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | Vereist om open bare Ip's voor een Load Balancer-service te zoeken en te configureren. |
| `Microsoft.Network/publicIPAddresses/join/action` | Vereist voor het configureren van open bare Ip's voor een Load Balancer-service. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | Vereist voor het maken of verwijderen van beveiligings regels voor een Load Balancer-service. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | Vereist om AzureDisks te configureren. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | Vereist voor het configureren van opslag accounts voor AzureFile of AzureDisk. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | Vereist voor het configureren van route tabellen en routes voor knoop punten. |
| `Microsoft.Compute/virtualMachines/read` | Vereist voor het vinden van informatie voor virtuele machines in een VMAS, zoals zones, fout domein, grootte en gegevens schijven. |
| `Microsoft.Compute/virtualMachines/write` | Vereist om AzureDisks te koppelen aan een virtuele machine in een VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | Dit is vereist om informatie te vinden voor virtuele machines in een schaalset voor een virtuele machine, zoals zones, fout domein, grootte en gegevens schijven. |
| `Microsoft.Network/networkInterfaces/write` | Vereist om een virtuele machine in een VMAS toe te voegen aan een load balancer back-end-adres groep. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | Vereist voor het toevoegen van een schaalset voor een virtuele machine aan een load balancer back-end-adres groepen en het uitschalen van knoop punten in een virtuele-machine schaalset. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | Vereist om AzureDisks te koppelen en een virtuele machine van een virtuele-machine schaalset toe te voegen aan de load balancer. |
| `Microsoft.Network/networkInterfaces/read` | Vereist voor het zoeken naar interne Ip's en load balancer back-end-adres groepen voor virtuele machines in een VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | Vereist voor het zoeken naar interne Ip's en load balancer back-end-adres groepen voor een virtuele machine in een schaalset met virtuele machines. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | Vereist voor het vinden van open bare Ip's voor een virtuele machine in een schaalset voor virtuele machines. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | Vereist om te controleren of er een subnet bestaat voor de interne load balancer in een andere resource groep. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | Vereist voor het configureren van moment opnamen voor AzureDisk. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | Vereist voor het vinden van de grootte van virtuele machines voor het zoeken van AzureDisk-volume limieten. |

### <a name="additional-cluster-identity-permissions"></a>Aanvullende machtigingen voor de cluster identiteit

Wanneer u een cluster met specifieke kenmerken maakt, hebt u de volgende extra machtigingen nodig voor de cluster identiteit. Omdat deze machtigingen niet automatisch worden toegewezen, moet u deze toevoegen aan de cluster identiteit nadat deze is gemaakt.

| Machtiging | Reden |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | Vereist als u een netwerk beveiligings groep gebruikt in een andere resource groep. Vereist voor het configureren van beveiligings regels voor een Load Balancer-service. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Vereist als u een subnet in een andere resource groep gebruikt, zoals een aangepast VNET. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | Vereist als u een subnet gebruikt dat is gekoppeld aan een route tabel in een andere resource groep, zoals een aangepast VNET met een aangepaste route tabel. Vereist om te controleren of er al een subnet bestaat voor het subnet in de andere resource groep. |
| `Microsoft.Network/virtualNetworks/subnets/read` | Vereist als u een interne load balancer in een andere resource groep gebruikt. Vereist om te controleren of er al een subnet bestaat voor de interne load balancer in de resource groep. |
| `Microsoft.Network/privatednszones/*` | Vereist als u een privé-DNS-zone gebruikt in een andere resource groep, zoals een aangepaste privateDNSZone. |

## <a name="kubernetes-rbac"></a>Kubernetes RBAC

Kubernetes RBAC biedt granulair filteren van gebruikers acties. Met dit controle mechanisme:
* U wijst gebruikers of gebruikers groepen toe om resources te maken en te wijzigen of logboeken weer te geven van het uitvoeren van werk belastingen van toepassingen. 
* U kunt machtigingen voor een enkele naam ruimte of over het hele AKS-cluster bereiken. 
* U maakt *rollen* om machtigingen te definiëren en wijst deze rollen vervolgens toe aan gebruikers met *functie bindingen*.

Zie [using KUBERNETES RBAC Authorization][kubernetes-rbac](Engelstalig) voor meer informatie.

### <a name="roles-and-clusterroles"></a>Rollen en ClusterRoles

#### <a name="roles"></a>Rollen
Voordat u machtigingen toewijst aan gebruikers met Kubernetes RBAC, definieert u gebruikers machtigingen als een *rol*. Machtigingen binnen een naam ruimte verlenen met behulp van rollen. 

> [!NOTE]
> Kubernetes-rollen *verlenen* machtigingen. de machtigingen worden niet *geweigerd* .

Als u machtigingen wilt verlenen voor het hele cluster of voor cluster bronnen buiten een bepaalde naam ruimte, kunt u in plaats daarvan *ClusterRoles* gebruiken.

#### <a name="clusterroles"></a>ClusterRoles

Een ClusterRole verleent en past machtigingen toe voor bronnen in het hele cluster, niet op een specifieke naam ruimte.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings en ClusterRoleBindings

Zodra u rollen hebt gedefinieerd om machtigingen te verlenen voor resources, wijst u deze Kubernetes RBAC-machtigingen toe aan een *RoleBinding*. Als uw AKS-cluster wordt [geïntegreerd met Azure Active Directory (Azure AD)](#azure-ad-integration), moet RoleBindings machtigingen verlenen aan Azure AD-gebruikers om acties in het cluster uit te voeren. Zie [toegang tot cluster bronnen beheren met Kubernetes op rollen gebaseerd toegangs beheer en Azure Active Directory-identiteiten](azure-ad-rbac.md).

#### <a name="rolebindings"></a>RoleBindings

Rollen toewijzen aan gebruikers voor een bepaalde naam ruimte met behulp van RoleBindings. Met RoleBindings kunt u één AKS-cluster logisch scheiden, zodat gebruikers alleen toegang hebben tot de toepassings resources in hun toegewezen naam ruimte. 

Als u rollen wilt binden in het hele cluster, of als u cluster bronnen buiten een bepaalde naam ruimte wilt koppelen, gebruikt u *ClusterRoleBindings*.

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

Met een ClusterRoleBinding bindt u rollen aan gebruikers en past u deze toe op resources in het hele cluster, niet op een specifieke naam ruimte. Met deze aanpak kunt u beheerders of ondersteunings technici toegang verlenen tot alle resources in het AKS-cluster.


> [!NOTE]
> Micro soft/AKS voert cluster acties uit met toestemming van de gebruiker onder een ingebouwde Kubernetes-rol `aks-service` en ingebouwde functie binding `aks-service-rolebinding` . 
> 
> Deze rol stelt AKS in staat om problemen met het cluster op te lossen en te diagnosticeren, maar kan geen machtigingen wijzigen of rollen of roltoewijzingen maken, of andere acties met hoge bevoegdheden. Rollen toegang is alleen ingeschakeld onder actieve ondersteunings tickets met Just-in-time-toegang. Meer informatie over [AKS-ondersteunings beleid](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Kubernetes-service accounts

*Service accounts* zijn een van de primaire gebruikers typen in Kubernetes. De Kubernetes-API houdt en beheert service accounts. De referenties van het service account worden opgeslagen als Kubernetes geheimen, zodat ze door een geautoriseerde peul kunnen worden gebruikt om te communiceren met de API-server. De meeste API-aanvragen bieden een verificatie token voor een service account of een normaal gebruikers account.

Normale gebruikers accounts staan meer traditionele toegang toe voor beheerders of ontwikkel aars van personen, niet alleen voor services en processen. Hoewel Kubernetes geen oplossing voor identiteits beheer biedt om reguliere gebruikers accounts en wacht woorden op te slaan, kunt u externe identiteits oplossingen integreren in Kubernetes. Voor AKS-clusters is deze geïntegreerde identiteits oplossing Azure AD.

Zie [Kubernetes-verificatie][kubernetes-authentication]voor meer informatie over de identiteits opties in Kubernetes.

## <a name="azure-ad-integration"></a>Azure AD-integratie

Verbeter de beveiliging van uw AKS-cluster met Azure AD-integratie. Azure AD is gebouwd op tien tallen bedrijfs identiteits beheer en is een multi tenant-, Cloud-en identiteits beheer service die basis Directory Services, Toegangs beheer voor toepassingen en identiteits beveiliging combineert. Met Azure AD kunt u on-premises identiteiten integreren in AKS-clusters om één bron te bieden voor account beheer en beveiliging.

![Integratie met AKS-clusters Azure Active Directory](media/concepts-identity/aad-integration.png)

Met Azure AD geïntegreerde AKS-clusters kunt u gebruikers of groepen toegang verlenen tot Kubernetes-resources binnen een naam ruimte of in het cluster. 

1. `kubectl`Een gebruiker kan een configuratie context verkrijgen door de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] uit te voeren. 
1. Wanneer een gebruiker met het AKS-cluster communiceert met `kubectl` , wordt u gevraagd zich aan te melden met hun Azure AD-referenties. 

Deze benadering biedt één bron voor gebruikers account beheer en wachtwoord referenties. De gebruiker heeft alleen toegang tot de resources zoals gedefinieerd door de Cluster beheerder.

Azure AD-verificatie wordt geleverd voor AKS-clusters met OpenID Connect Connect. OpenID Connect Connect is een id-laag die boven op het OAuth 2,0-protocol is gebouwd. Voor meer informatie over OpenID Connect Connect raadpleegt u de [Open-ID Connect-documentatie][openid-connect]. Vanuit het Kubernetes-cluster wordt [webhook-token verificatie][webhook-token-docs] gebruikt om verificatie tokens te verifiëren. Webhook-token verificatie wordt geconfigureerd en beheerd als onderdeel van het AKS-cluster.

### <a name="webhook-and-api-server"></a>Webhook en API-server

![Webhook-en API-Server verificatie stroom](media/concepts-identity/auth-flow.png)

Zoals in de bovenstaande afbeelding wordt weer gegeven, roept de API-server de AKS-webhookserver aan en voert de volgende stappen uit:

1. `kubectl` maakt gebruik van de Azure AD-client toepassing voor het aanmelden van gebruikers met [OAuth 2,0-autorisatie subsidie stroom](../active-directory/develop/v2-oauth2-device-code.md).
2. Azure AD biedt een access_token, id_token en een refresh_token.
3. De gebruiker maakt een aanvraag `kubectl` met een access_token van `kubeconfig` .
4. `kubectl` Hiermee wordt de access_token naar de API-server verzonden.
5. De API-server is geconfigureerd met de auth webhook-server om validatie uit te voeren.
6. De webhookserver voor verificatie bevestigt dat de JSON Web Token hand tekening geldig is door de open bare Azure AD-ondertekeningssleutel te controleren.
7. De server toepassing maakt gebruik van door de gebruiker ingevoerde referenties voor het opvragen van groepslid maatschappen van de aangemelde gebruiker vanuit de MS Graph API.
8. Er wordt een antwoord verzonden naar de API-server met gebruikers gegevens zoals de claim van de user principal name (UPN) van het toegangs token en het groepslid maatschap van de gebruiker op basis van de object-ID.
9. De API voert een autorisatie besluit uit op basis van de Kubernetes Role/RoleBinding.
10. Na de autorisatie retourneert de API-server een reactie op `kubectl` .
11. `kubectl` geeft feedback aan de gebruiker.
 
Meer informatie over het integreren van AKS met Azure AD met onze met onze [AKS beheerde Azure AD-hand leiding voor integratie](managed-aad.md).

## <a name="azure-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer voor Azure

Op rollen gebaseerd toegangs beheer (RBAC) van Azure is een autorisatie systeem dat is gebaseerd op [Azure Resource Manager](../azure-resource-manager/management/overview.md) dat een nauw keurig toegangs beheer van Azure-resources biedt.

| RBAC-systeem | Description |
|---|---|
| Kubernetes RBAC | Ontworpen om te werken aan Kubernetes-resources binnen uw AKS-cluster. |
| Azure RBAC | Ontworpen om te werken aan resources binnen uw Azure-abonnement. |

Met Azure RBAC maakt u een *roldefinitie* waarin de machtigingen worden beschreven die moeten worden toegepast. Vervolgens wijst u een gebruiker of groep deze roldefinitie toe via een *roltoewijzing* voor een bepaald *bereik*. Het bereik kan een afzonderlijke resource, een resource groep of binnen het abonnement zijn.

Zie [Wat is Azure Role-based Access Control (Azure RBAC)?][azure-rbac] voor meer informatie.

Er zijn twee toegangs niveaus nodig om een AKS-cluster volledig te kunnen gebruiken: 
* [Open de AKS-resource in uw Azure-abonnement](#azure-rbac-to-authorize-access-to-the-aks-resource). 
  * Besturings elementen schalen of een cluster bijwerken met behulp van de AKS-Api's.
  * Haal uw `kubeconfig` .
* Toegang tot de Kubernetes-API. Deze toegang wordt bepaald door:
  * [KUBERNETES RBAC](#kubernetes-rbac) (traditioneel).
  * [Integratie van Azure RBAC met AKS voor Kubernetes-autorisatie](#azure-rbac-for-kubernetes-authorization-preview).

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure RBAC voor het machtigen van toegang tot de AKS-resource

Met Azure RBAC kunt u uw gebruikers (of identiteiten) voorzien van gedetailleerde toegang tot AKS-resources in een of meer abonnementen. U kunt bijvoorbeeld de rol van de [Azure Kubernetes-service Inzender](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) gebruiken om uw cluster te schalen en bij te werken. Ondertussen heeft een andere gebruiker met de [rol Azure Kubernetes service cluster admin](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) alleen toestemming voor het ophalen van de beheerder `kubeconfig` .

U kunt uw gebruiker ook de rol algemene [Inzender](../role-based-access-control/built-in-roles.md#contributor) geven. Met de rol algemeen Inzender kunnen gebruikers de bovenstaande machtigingen en alle mogelijke acties voor de AKS-resource uitvoeren, met uitzonde ring van het beheer van machtigingen.

[Gebruik Azure RBAC om de toegang tot het Kubernetes-configuratie bestand in AKS te definiëren](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC voor Kubernetes-autorisatie (preview-versie)

Met de integratie van Azure RBAC maakt AKS gebruik van een Kubernetes Authorization webhook-server, zodat u Azure AD-geïntegreerde Kubernetes-cluster resource machtigingen en-toewijzingen kunt beheren met Azure Role definition en roltoewijzingen.

![Azure RBAC voor Kubernetes-autorisatie stroom](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Zoals wordt weer gegeven in het bovenstaande diagram, wordt bij het gebruik van de integratie van Azure RBAC alle aanvragen voor de Kubernetes-API dezelfde verificatie stroom gevolgd zoals wordt uitgelegd in de [sectie integratie van Azure Active Directory](#azure-ad-integration). 

Als de identiteit die de aanvraag maakt in azure AD bestaat, gaat Azure met Kubernetes RBAC om de aanvraag te autoriseren. Als de identiteit zich buiten Azure AD (een Kubernetes-service account) bevindt, wordt de normale Kubernetes RBAC door autorisatie.

In dit scenario gebruikt u de methoden en Api's van Azure RBAC voor het toewijzen van ingebouwde rollen van gebruikers of het maken van aangepaste rollen, net als bij Kubernetes-rollen. 

Met deze functie geeft u niet alleen gebruikers machtigingen voor de AKS-resource over in abonnementen, maar u kunt ook de rol en machtigingen configureren voor elk van deze clusters, waarmee de Kubernetes-API-toegang wordt beheerd. U kunt bijvoorbeeld de `Azure Kubernetes Service RBAC Viewer` rol voor het abonnements bereik verlenen. De ontvanger van de rol kan alle Kubernetes-objecten van alle clusters weer geven zonder ze te wijzigen.

> [!IMPORTANT]
> U moet Azure RBAC inschakelen voor Kubernetes-autorisatie voordat u deze functie kunt gebruiken. Voor meer informatie en stapsgewijze instructies, volgt u de hand leiding [Azure RBAC voor Kubernetes-autorisatie](manage-azure-rbac.md) .

#### <a name="built-in-roles"></a>Ingebouwde rollen

AKS biedt de volgende vier ingebouwde rollen. Ze zijn vergelijkbaar met de [ingebouwde rollen van Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) met enkele verschillen, zoals ondersteunende CRDs. Bekijk de volledige lijst met acties die zijn toegestaan door elke [ingebouwde rol van Azure](../role-based-access-control/built-in-roles.md).

| Rol                                | Beschrijving  |
|-------------------------------------|--------------|
| RBAC-viewer voor Azure Kubernetes service  | Hiermee staat u alleen-lezen toegang toe om de meeste objecten in een naam ruimte weer te geven. <br> Het weer geven van functies of functie bindingen is niet toegestaan.<br> Kan niet weer geven `Secrets` . Als de `Secrets` inhoud wordt gelezen, hebt u toegang tot `ServiceAccount` referenties in de naam ruimte, waardoor er API-toegang zou kunnen worden toegestaan `ServiceAccount` in de naam ruimte (een vorm van bevoegdheden escalation).  |
| RBAC-schrijver van Azure Kubernetes service | Hiermee wordt lees-/schrijftoegang tot de meeste objecten in een naam ruimte toegestaan. <br> Kan geen rollen of rollen bindingen weer geven of wijzigen. <br> Hiermee krijgt u toegang tot `Secrets` en uitvoeren van Peul als serviceaccount in de naam ruimte, zodat deze kan worden gebruikt om de API-toegangs niveaus van een wille keurige serviceaccount in de naam ruimte te verkrijgen. |
| RBAC-beheerder voor Azure Kubernetes service  | Hiermee kan beheerders toegang worden verleend binnen een naam ruimte. <br> Hiermee staat u lees-/schrijftoegang toe voor de meeste bronnen in een naam ruimte (of cluster bereik), inclusief de mogelijkheid om rollen en rollen bindingen te maken binnen de naam ruimte. <br> Schrijf toegang tot resource quota of de naam ruimte zelf is niet toegestaan. |
| De Azure Kubernetes service RBAC-cluster beheerder  | Hiermee kan toegang van Super gebruikers elke actie op elke resource uitvoeren. <br> Hiermee krijgt u volledige controle over elke resource in het cluster en in alle naam ruimten. |


## <a name="summary"></a>Samenvatting

Bekijk de tabel voor een snelle samen vatting van de manier waarop gebruikers zich kunnen verifiëren bij Kubernetes wanneer Azure AD-integratie is ingeschakeld. In alle gevallen is de volg orde van de opdrachten van de gebruiker:
1. Voer uit `az login` om te verifiëren bij Azure.
1. Voer uit `az aks get-credentials` om referenties voor het cluster op te halen in `.kube/config` .
1. `kubectl`Opdrachten uitvoeren. 
   * Met de eerste opdracht kan verificatie op basis van een browser worden geactiveerd voor verificatie bij het cluster, zoals wordt beschreven in de volgende tabel.

In de Azure Portal vindt u het volgende:
* De *rol toekenning* (Azure RBAC-rol verlenen) waarnaar wordt verwezen in de tweede kolom wordt weer gegeven op het tabblad **Access Control** . 
* De Azure AD-groep Cluster beheer wordt weer gegeven op het tabblad **configuratie** .
  * Ook gevonden met de parameter naam `--aad-admin-group-object-ids` in de Azure cli.


| Description        | Rol toekenning vereist| Azure AD-groep (en) cluster beheer | Wanneer gebruikt u dit? |
| -------------------|------------|----------------------------|-------------|
| Verouderde beheerder aanmelden met behulp van client certificaat| **Rol van Azure Kubernetes-beheerder**. Deze rol kan `az aks get-credentials` worden gebruikt met de `--admin` vlag, waarmee een [verouderd (niet-Azure AD) cluster beheer certificaat](control-kubeconfig-access.md) wordt gedownload naar de gebruiker `.kube/config` . Dit is het enige doel van de Azure Kubernetes-beheerdersrol.|n.v.t.|Als u permanent bent geblokkeerd door geen toegang tot een geldige Azure AD-groep met toegang tot uw cluster.| 
| Azure AD met hands-RoleBindings (cluster)| Gebruikersrol **Azure Kubernetes**. De rol ' gebruiker ' mag `az aks get-credentials` worden gebruikt zonder de `--admin` vlag. (Dit is het enige doel van ' Azure Kubernetes-gebruikersrol '.) Het resultaat van een Azure AD-cluster is het downloaden van [een lege vermelding](control-kubeconfig-access.md) in `.kube/config` , waardoor de browser verificatie wordt geactiveerd wanneer deze voor het eerst wordt gebruikt door `kubectl` .| De gebruiker bevindt zich niet in een van deze groepen. Omdat de gebruiker zich niet in een cluster beheer groepen bevindt, worden hun rechten volledig beheerd door alle RoleBindings of ClusterRoleBindings die zijn ingesteld door cluster beheerders. De (cluster) RoleBindings [benoemt Azure AD-gebruikers of Azure ad-groepen](azure-ad-rbac.md) als hun `subjects` . Als er geen dergelijke bindingen zijn ingesteld, kan de gebruiker geen `kubectl` opdrachten excute.|Als u een nauw keurig toegangs beheer wilt en u geen gebruik maakt van Azure RBAC voor Kubernetes-autorisatie. Houd er rekening mee dat de gebruiker die de bindingen instelt, zich moet aanmelden met een van de andere methoden die in deze tabel worden vermeld.|
| Azure AD door lid van Beheerders groep| Hetzelfde als hierboven|De gebruiker is lid van een van de groepen die hier worden vermeld. AKS genereert automatisch een ClusterRoleBinding die alle weer gegeven groepen koppelt aan de `cluster-admin` Kubernetes-rol. Zodat gebruikers in deze groepen alle opdrachten kunnen uitvoeren `kubectl` als `cluster-admin` .|Als u gebruikers eenvoudig de volledige beheerders rechten wilt verlenen en _geen_ gebruik wilt maken van Azure RBAC voor Kubernetes-autorisatie.|
| Azure AD met Azure RBAC voor Kubernetes-autorisatie|Twee rollen: <br> First, **Azure Kubernetes-gebruikersrol** (zoals hierboven). <br> Ten tweede, een van de ' Azure Kubernetes service **RBAC**... ' de hierboven vermelde rollen of uw eigen aangepaste alternatief.|Het veld beheerders rollen op het tabblad Configuratie is niet relevant als Azure RBAC voor Kubernetes-autorisatie is ingeschakeld.|U gebruikt Azure RBAC voor Kubernetes-autorisatie. Deze benadering biedt u nauw keurige controle, zonder dat u RoleBindings of ClusterRoleBindings hoeft in te stellen.|

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Active Directory integreren met AKS][aks-aad]om aan de slag te gaan met Azure AD en Kubernetes RBAC.
- Zie [Aanbevolen procedures voor verificatie en autorisatie in AKS][operator-best-practices-identity]voor gekoppelde aanbevolen procedures.
- Om aan de slag te gaan met Azure RBAC voor Kubernetes-autorisatie, raadpleegt u [Azure RBAC gebruiken om toegang te verlenen in het Azure Kubernetes service-cluster (AKS)](manage-azure-rbac.md).
- `kubeconfig`Zie toegang tot het [cluster configuratie bestand beperken](control-kubeconfig-access.md) om aan de slag te gaan met het beveiligen van uw bestand

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
